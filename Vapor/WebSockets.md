# WebSockets

*Example implementation*
```swift
enum WebSocketSendOption {
  case sender(WebSocket), all((WebSocket, UUID))
}

class WebSocketController {
  var clients: [UUID: [WebSocket]] // seperate websocket room according to id
  
  init() {
    self.clients = [:]
  }
  
  func webSocket(req: Request, socket: WebSocket) {
    let id = req.query[UUID.self, at: "id"]!
    self.connect(socket, id: id)
  }
  
  func connect(_ ws: WebSocket, id: UUID) { // register a client to room (specified by id)
    if clients[id] == nil {
      clients[id] = []
    } else {
      clients[id] = clients[id]!.filter({
        !$0.isClosed
      })
      
      if clients[id] == nil {
        clients[id] = []
      }
    }
    
    clients[id]!.append(ws)
    
    ws.onBinary { [weak self] ws, buffer in
      guard let self = self, let data = buffer.getData(at: buffer.readerIndex, length: buffer.readableBytes) else { return }
      self.onData(ws, data)
    }
    
    ws.onText { [weak self] ws, text in
      guard let self = self, let data = text.data(using: .utf8) else { return }
      self.onData(ws, data)
    }
		
    self.send(message: Handshake(), to: .sender(ws))
  }
  
  func send<T: Codable>(message: T, to sendOption: WebSocketSendOption) {
    do {
      let targets: [WebSocket] = {
        switch sendOption {
          case .sender(let ws):         // send to single client
            return [ws]
          case .all((let ws, let id)):  // send to all clients in the room of id
            guard let clients = clients[id] else {
              return []
            }
            
            // skip send to self
            return clients.filter { $0 !== ws }
        }
      }()
      
      let encoder = JSONEncoder()
      let data = try encoder.encode(message)
			
      targets.forEach {
        $0.send(raw: data, opcode: .binary)
      }
    } catch {
      logger.report(error: error)
    }
  }
	
  func onData(_ ws: WebSocket, _ data: Data) {
    let decoder = JSONDecoder()
    do {
      let sinData = try decoder.decode(SinData.self, from: data)
      switch sinData.type {
        case .ale:
          guard let id = UUID(uuidString: try decoder.decode(Ale.self, from: data).id) else { break }
          send(message: AleResponse(), to: .all((ws, id)))
        default:
          break
      }
    } catch {
      fatalError("\(error)")
    }
  }
}

extension WebSocketController: RouteCollection {
  func boot(routes: RoutesBuilder) throws {
    routes.webSocket("socket", onUpgrade: self.webSocket)
  }
}

```

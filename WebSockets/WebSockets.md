# WebSockets

A WebSocket is a network protocol that allows two-way communication between a server and client.  
Unlike HTTP, which uses a request and response pattern, WebSocket peers can send messages in either direction at any point in time.

[Server side implementation](https://github.com/YIshihara11201/iOSTips/blob/main/Vapor/WebSockets.md)

## Start Websocket communication
```swift
let session = URLSession(configuration: .default)
let url = URL(string: "ws://\(wsURLString)/socket?id=\(id)")!
let socket = session.webSocketTask(with: url)
```

## Receive Websocket message from server
```swift
socket.receive { result in
  switch result {
    case .failure(let error):
      // handle failure
    case .success(let message):
      // handle success
      switch message { // message can be Data or String
        case .data(let data):
          // handle Data
        case .string(let str):
          // handle String
        @unknown default:
          break
      }
  }
}
```

## Cancel Websocket communication
```swift
socket.cancel(with: .goingAway, reason: nil)
```

## Resume Websocket communication
```swift
socket.resume()
```

## Example implementation
```swift
final class WebSocketController {
  private let wsURLString = "someurl.ca"
  private let session: URLSession
  private let decoder = JSONDecoder()
  private let encoder = JSONEncoder()
  
  var socket: URLSessionWebSocketTask!
  var id: UUID
	
  init(id: UUID) {
    self.id = id
    self.session = URLSession(configuration: .default)
  }
  
  func connect() {
    self.socket = session.webSocketTask(with: URL(string: "ws://\(wsURLString)/socket?id=\(id)")!)
    self.listen()
    self.socket.resume()
  }
  
  func listen() {
    self.socket.receive { [weak self] (result) in
      guard let self = self else { return }
      switch result {
        case .failure(let error):
          print(error)
          self.socket.cancel(with: .goingAway, reason: nil)
          self.connect()
          return
        case .success(let message):
          switch message {
            case .data(let data):
              self.handle(data)
            case .string(let str):
              guard let data = str.data(using: .utf8) else { return }
              self.handle(data)
            @unknown default:
              break
          }
        }
        
      self.listen() // once communication successed, prepare for next one
    }
  }
  
  func handle(_ data: Data) {
    do {
      let sinData = try decoder.decode(SinData.self, from: data)
      switch sinData.type {
        case .handshake:
          print("Shook the hand")
        case .aleResponse:
          self.handleAleResponse()
        default:
          break
      }
    } catch {
      print(error)
    }
  }
  
  func handleAleResponse() {
    print("handling ale")
    DispatchQueue.main.async {
      NotificationCenter.default.post(name: Notification.Name.thumbsUpReceivedNotification, object: nil) // notify the application that it received an ale message from websocket communication
    }
  }
  
  func sendAle() {
    let message = Ale(id: id.uuidString)
    do {
      let data = try encoder.encode(message)
        self.socket.send(.data(data)) { (err) in
          if err != nil {
            print(err.debugDescription)
          }
        }
    } catch {
      print(error)
    }
  }
}

// Websocket Communication types
enum MessageType: String, Codable {
  case handshake, ale, aleResponse
}

struct SinData: Codable {
  let type: MessageType
}

struct Handshake: Codable {
  var type = MessageType.handshake
}

struct Ale: Codable {
  var type = MessageType.ale
  var id: String
}

struct AleResponse: Codable {
  var type = MessageType.aleResponse
}

```

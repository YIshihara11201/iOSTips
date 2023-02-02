# Flood Fill implementation

This is an example of Flood Fill implementation for the following problem.  

Problem:  
&nbsp;&nbsp; Starting from cell (sr, sc), fill in 4-directionally adjacent cells with the color specified as an argument.  

```swift

func floodFill(_ image: [[Int]], _ sr: Int, _ sc: Int, _ color: Int) -> [[Int]] {
  struct Square {
    let row: Int
    let column: Int
  }
  
  let dr = [0, 0, 1, -1]
  let dc = [1, -1, 0, 0]
  
  let m = input.count
  let n = input[0].count
  var visited = [[Bool]](repeating: [Bool](repeating: false, count: n), count: m)
  var res = [[Int]]()
  
  for row in image {
    res.append(row)
  }
  
  let queue = Queue<Square>()
  queue.enqueue(item: Square(row: sr, column: sc))
  visited[sr][sc] = true
  let initialColor = res[sr][sc]
  res[sr][sc] = color
  
  while !queue.isEmpty() {
    let curr = queue.dequeue()!
    for i in 0..<4 {
      let nr = curr.row + dr[i]
      let nc = curr.column + dc[i]
      
      if nr >= 0 && nr < m && nc >= 0 && nc < n {
        if !visited[nr][nc] && res[nr][nc] == initialColor {
	  queue.enqueue(item: Square(row: nr, column: nc))
	  res[nr][nc] = color
	  visited[nr][nc] = true
	}
      }
    }
  }
  
  return res
}

```

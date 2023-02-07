# Key Paths
Key Paths are a way of storing a reference to a property without actually accessing the property. 

```swift
struct Person {
  var firstName: String
  var lastName: String
}

var prop = \Person.firstName

// ... time passes ...

prop = \.lastName // inferred as \Person.lastName

let whatname = p[keyPath: prop]
```

<br>

The true power of key paths comes from the fact that they let us reference a property without having to associate it with any specific instance.  
https://www.swiftbysundell.com/articles/the-power-of-key-paths-in-swift/

```swift
struct CellConfigurator<Model> {
  let titleKeyPath: KeyPath<Model, String>
  let subtitleKeyPath: KeyPath<Model, String>
  let imageKeyPath: KeyPath<Model, UIImage?>

  func configure(_ cell: UITableViewCell, for model: Model) {
    cell.textLabel?.text = model[keyPath: titleKeyPath]
    cell.detailTextLabel?.text = model[keyPath: subtitleKeyPath]
    cell.imageView?.image = model[keyPath: imageKeyPath]
  }
}
```

```swift
let songCellConfigurator = CellConfigurator<Song>(
  titleKeyPath: \.name,
  subtitleKeyPath: \.artistName,
  imageKeyPath: \.albumArtwork
)

let playlistCellConfigurator = CellConfigurator<Playlist>(
  titleKeyPath: \.title,
  subtitleKeyPath: \.authorName,
  imageKeyPath: \.artwork
)
```

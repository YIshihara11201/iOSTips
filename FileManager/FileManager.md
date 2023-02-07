# FileManager

reference: https://tech.amefure.com/swift-file-manager

## Sandbox structure
The iOS file system uses a sandbox structure.
The sandbox structure is a virtual area called a sandbox that is isolated from the outside world, and operations and operations are allowed only within the sandbox. This structure prevents malicious access and operation of important external programs.
Installed iOS apps are stored and operated in the sandbox. Saved data is also stored in the sandbox and will be deleted when uninstalled.
When saving files in the iOS app, they are also saved in the directory determined in the sandbox.

## Referable directories
<img width="486" alt="FileSystemDirectory" src="https://user-images.githubusercontent.com/98417271/217349603-cb6dcf22-e51c-4db8-96d3-2e9fd09c002c.png">
https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html  

<br>

**Documents**  
Document directory stores files that users view, including user-generated files and data such as images and videos.  
Within this directory will be backed up to iTunes/iCloud.  

## APIs

### Get directory path inside sandbox
```swift
FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
```

### Create/Update file
```swift
func writingFile(_ text:String) {
  guard let docURL = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first else{
    fatalError("Invalid URL")
  }
  
  let fullURL = docURL.appendingPathComponent("sample.txt")
  do {
    try text.write(to: fullURL,atomically: true,encoding: .utf8)
  } catch{
    print("failed to write the file")
  }         
}
```

### Read file
```swift
func readingFile()->String?{
  guard let docURL = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first else{
    fatalError("Invalid URL")
  }
  
  let fullURL = docURL.appendingPathComponent("sample.txt")
  do {
    let textData = try String(contentsOf: fullURL, encoding: .utf8)
    return textData
  } catch {
    return nil
  }
}
```

### Delete file
```swift
func removeFile(){
    let fileManager = FileManager.default
    guard let docURL = fileManager.urls(for: .documentDirectory, in: .userDomainMask).first else{
        fatalError("Invalid URL")
    }
    let fullURL = docURL.appendingPathComponent("sample.txt")
    do {
        try fileManager.removeItem(at: fullURL)
    } catch {
        print(error.localizedDescription)
    }
}
```

### File existence
```swift
if !fileManager.fileExists(atPath: filePath) {
  fileManager.createFile(atPath:filePath, contents: nil, attributes: [:])
}else{
  print("file exists")
}
```

### Create Directory
```swift
func createDirectory(){
  let fileManager = FileManager.default
  let docPath =  FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
  let directory = docPath.appendingPathComponent("Test", conformingTo: .directory)
  do {
    try fileManager.createDirectory(at: directory, withIntermediateDirectories: true, attributes: nil)
  } catch {
    print("failed to create directory")
  }
}
```

# Keychain

Keychain Services help you to securely store items, or small chunks of data, into an encrypted database on behalf of the user.  

When you want to store a secret such as a password, you package it as a keychain item. This is an opaque type which consists of two parts: data and a set of attributes. Just before it inserts a new item, Keychain Services encrypts the data then wraps it together with its attributes.  

<img width="500" alt="Keychain_Image" src="https://user-images.githubusercontent.com/98417271/217373757-1ac14685-b351-4578-8f30-a8d4e4619037.png">

<br>

## Item Attribute Keys
Use attributes to identify and store metadata or to control access to your stored items. 
https://developer.apple.com/documentation/security/keychain_services/keychain_items/item_attribute_keys_and_values

## Item Class Keys and Values
Keychain items come in a variety of classes according to the kind of data they hold, such as passwords, cryptographic keys, and certificates. The item's class dictates which attributes apply and enables the system to decide whether or not the data should be encrypted on disk. For example, passwords require encryption, but certificates don't because they are not secret.  
You will specify a class inside a query dictionary like `[kSecClass as String: kSecClassGenericPassword]`


## APIs

### SecItemAdd(_:_:): 
Use this function to add one or more items to a keychain.
### SecItemCopyMatching(_: _:)
This function returns one or more keychain items that match a search query. Additionally, it can copy attributes of specific keychain items.
### SecItemUpdate(_: _:)
This function allows you to modify items that match a search query.
### SecItemDelete(_:)
This function removes items that match a search query.

<br>

While the functions above operate with different parameters, they all return a result code expressed as an `OSStatus`. This is a 32-bit signed integer which can assume one of the values listed in [Item Return Result Keys](https://developer.apple.com/documentation/security/1542001-security_framework_result_codes).

*example implementation*
```swift
class KeychainAccess {

  func addKeychainData(itemKey: String, itemValue: String) throws {
    guard let valueData = itemValue.data(using: .utf8) else {
      print("Keychain: Unable to store data, invalid input - key: \(itemKey), value: \(itemValue)")
      return
    }
    
    // clean up existing data
    do {
      try deleteKeychainData(itemKey: itemKey)
    } catch {
      print("Keychain: nothing to delete...")
		}
    
    // bnild queary
    let queryAdd: [String: AnyObject] = [
      kSecClass as String: kSecClassGenericPassword,
      kSecAttrAccount as String: itemKey as AnyObject,
      kSecValueData as String: valueData as AnyObject,
      kSecAttrAccessible as String: kSecAttrAccessibleWhenUnlocked
    ]
    let resultCode: OSStatus = SecItemAdd(queryAdd as CFDictionary, nil)
		
    if resultCode != 0 {
      print("Keychain: value not added - Error: \(resultCode)")
    } else {
      print("Keychain: value added successfully")
    }
  }
  
  func deleteKeychainData(itemKey: String) throws {
    let queryDelete: [String: AnyObject] = [
      kSecClass as String: kSecClassGenericPassword,
      kSecAttrAccount as String: itemKey as AnyObject
    ]
    
    let resultCodeDelete = SecItemDelete(queryDelete as CFDictionary)
		
    if resultCodeDelete != 0 {
      print("Keychain: unable to delete from keychain: \(resultCodeDelete)")
    } else {
      print("Keychain: successfully deleted item")
    }
  }
	
  func queryKeychainData (itemKey: String) throws -> String? {
    let queryLoad: [String: AnyObject] = [
      kSecClass as String: kSecClassGenericPassword,
      kSecAttrAccount as String: itemKey as AnyObject,
      kSecReturnData as String: kCFBooleanTrue,
      kSecMatchLimit as String: kSecMatchLimitOne
    ]
    
    var result: AnyObject?
    let resultCodeLoad = withUnsafeMutablePointer(to: &result) {
      SecItemCopyMatching(queryLoad as CFDictionary, UnsafeMutablePointer($0))
    }
		
    if resultCodeLoad != 0 {
      print("Keychain: unable to load data - \(resultCodeLoad)")
      return nil
    }
    
    guard let resultVal = result as? NSData, let keyValue = NSString(data: resultVal as Data, encoding: String.Encoding.utf8.rawValue) as String? else {
      print("Keychain: error parsing keychain result - \(resultCodeLoad)")
      return nil
    }
    
    return keyValue
  }
}

```

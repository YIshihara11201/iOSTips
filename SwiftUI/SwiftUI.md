# SwiftUI

### [Data Flow Through SwiftUI](https://developer.apple.com/videos/play/wwdc2019/226)

<img src="https://user-images.githubusercontent.com/8536870/115537484-cf9f7600-a2d5-11eb-8b60-0847e186f288.png">

- Single source of truth
- declaratively defined View function receives a State as input and constructs a layout
- the data reflected in the View is always consistent

<br/>

### Lifecycle and State management

- UI update is realized by passing State input to View, not directory modify View directly
- Example of Lifecycle referred from https://www.vadimbulavin.com/swiftui-view-lifecycle/
<img src="https://user-images.githubusercontent.com/8536870/115531403-b4316c80-a2cf-11eb-962f-8d81b9aedda5.png">

- Appearing
  - State monitoring starts when the screen is displayed
  - Render `View.body` output
  - [onAppear](https://developer.apple.com/documentation/swiftui/text/onappear(perform:)) is callded
- Updating
  - When State is updated, the current ViewGraph difference is checked
  - In case ViewGraph has updates, re-rendering runs accoding to `View.body` output
- Disappearing
  - When display disappears, [onDisappear](https://developer.apple.com/documentation/swiftui/text/ondisappear(perform:)) is called

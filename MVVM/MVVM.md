## MVVM Architecture

- diagram
<img src="https://user-images.githubusercontent.com/8536870/115537612-f2ca2580-a2d5-11eb-937a-98ea74da920f.png">

- Responsibilities

  - Model
    - process data
  - View
    - UI layout
    - pass user interaction to ViewModel
    - bind ViewModel output to UI
  - ViewModel
    - manage View State using Model, according to an input from View
    - process and pass State to View


- [MVVM - Android Recommended app architecture](https://developer.android.com/jetpack/guide#recommended-app-arch)

<img src="https://user-images.githubusercontent.com/8536870/115537744-18572f00-a2d6-11eb-8d24-1e4f22d2701b.png">

- Repository is responsible for returning data (-> ex) from Web, local DB, ...) to ViewModel
- ViewModel does not need to pay attention to the data resource, and can focus on View State management

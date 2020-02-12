## 前言
我在[react-native-cpp-demo](https://github.com/drafish/react-native-cpp-demo)中介绍了`react-native`中集成c++的三种方案。在这个项目里，我将介绍怎么在Android和IOS端创建c++动态库，并且集成到两个端。创建动态库的部分代码都在这个项目里，集成动态库的代码在`react-native-cpp-demo`中。这个项目是`react-native-cpp-demo`的衍生项目，大部分操作细节我在`react-native-cpp-demo`中已经讲过了，这里不再赘述。

## Android
`react-native-cpp-demo`中三种在Android端集成c++的方案，虽然在js调用c++的流程和原理上都有些差异，但都是先将c++编译成动态库，然后在java中加载动态库。我们要做的就是将创建动态库和使用动态库这两步分开。

### 创建动态库

1. 用`Android Studio`创建一个空项目，这个项目不需要页面，只需要将c++代码编译成动态库，所以创建的时候选择`Add No Activity`。

2. 在`android/app/build.gradle`文件中添加`externalNativeBuild`配置。

3. 编译项目，编译完后生成的动态库文件在`android/app/build/intermediates/cmake/debug/obj`目录下。

### 使用动态库
为了方便展示，我先将`react-native-cpp-demo`中集成c++的方案改成JNI方案。

4. 将动态库文件复制到`react-native-cpp-demo/android/app/libs`目录下。

5. 编辑`react-native-cpp-demo/android/app/build.gradle`文件，将`externalNativeBuild`注释，添加`sourceSets`配置，这样就不会编译c++，而是直接从`react-native-cpp-demo/android/app/libs`目录下引用已经编译好的动态库文件。


## IOS
`react-native-cpp-demo`中三种在IOS端集成c++的方案，都没有用到动态库，而是直接将object-c和c++混合编译。在IOS端使用动态库要稍微麻烦一点。这里分享一篇文章[iOS动态库的使用](https://juejin.im/post/5b1f1d3a6fb9a01e6e2baded)，我基本上就是照着这篇文章和文中给出的demo做的。

### 创建动态库

1. 用`XCode`创建一个`Framework`项目。项目创建完有一个自带的头文件示例，可以删掉，没什么用。

2. 在项目中引用c++源文件和头文件

3. `Build Phases -> Headers`配置中，将c++头文件放到`Public`下，只有在`Public`下的头文件才能导出。默认是在`Project`下。

4. 编译动态库，编译完后生成的动态库文件在`/Users/用户名/Library/Developer/Xcode/DerivedData/项目名-一串字符串/Build/Products`目录下。

### 使用动态库

5. `Debug-iphonesimulator`对应模拟器环境，`Debug-iphoneos`对应真实环境。我是在模拟器环境下跑demo，所以我将`Debug-iphonesimulator`目录下的动态库复制到`react-native-cpp-demo/ios`目录下

6. 将动态库`DynamicLibraryDemo.framework`引入项目

7. 将项目配置中`General -> Framework, Libraries, and Embedded Content`中`DynamicLibraryDemo.framework`的`Embed`值改成`Embed & Sign`或者`Embed Without Signing`，默认是`Do Not Embed`。

8. `react-native-cpp-demo/ios/ReactNativeCppDemo/example/jni/TestModule.m`中将`#import "Test.h"`改成`#import <DynamicLibraryDemo/Test.h>`


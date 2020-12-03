### react native笔记
1. [踩过坑](https://juejin.im/post/5b4d47e96fb9a04f844abde0)
1. [loading在线制作](https://loading.io/)
1. [原型制作工具](https://www.xiaopiu.com/)
1. [渐变配色](http://www.sj520.cn/tools/jianbian/)
1. [渐变配色](https://uigradients.com/#OrangeFun)
1. [APP启动图标图片生成工具](https://icon.wuruihong.com/)
### ios 笔记
1. [配置启动图标](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/app-icon/)
1. [配置启动屏](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/launch-screen/)

### 动画设计
1. [动画](https://lottiefiles.com/)

### android 笔记

### 开发工具
1. [移动端调试工具](https://www.mobiledebug.com/)


### ReactNative错误集合

本文章专门用于记录在React Native 开发中所遇到的问题，以便于以后查阅。
1. 初始化RN ts项目 `npx react-native init MyApp --template typescript`

1. `java.lang.RuntimeException: java.lang.RuntimeException: com.android.builder.dexing.DexArchiveMergerException: Unable to merge dex`

    **解决方案**：`react-native项目名/android/app/build.gradle`中找到`defaultConfig`闭包， 在里面添加`multiDexEnabled true` 代码。然后在`dependencies`中新增依赖`implementation 'com.android.support:multidex:1.0.1'`。然后命令行切换到`react-native项目名/android`目录下面，运行命令`./gradlew clean`，清理项目。再运行`react-native run-android`即可

1. `Error:java.util.concurrent.ExecutionException: com.android.tools.aapt2.Aapt2Exception: AAPT error: check logs for details` 错误
    
    **解决方案**：`react-native项目名/android/gradle.properties` 中，新增 `android.enableAapt2=false` 。然后命令行切换到`react-native项目名/android`目录下面，运行命令`./gradlew clean`，清理项目。再运行`react-native run-android`即可
1. 在React Native 0.56 版本初始化项目运行`react-native run-android`
    ```
    problem occurred configuring project ':app'.
    > Could not resolve all dependencies for configuration ':app:_debugApkCopy'.
       > Could not resolve com.android.support:appcompat-v7:26.1.0.
         Required by:
             project :app
             project :app > com.facebook.react:react-native:0.56.0
          > Could not resolve com.android.support:appcompat-v7:26.1.0.
             > Could not get resource 'https://maven.google.com/com/android/support/appcompat-v7/26.1.0/appcompat-v7-26.1.0.pom'.
                > Could not HEAD 'https://maven.google.com/com/android/support/appcompat-v7/26.1.0/appcompat-v7-26.1.0.pom'.
                   > Remote host closed connection during handshake
    ```
    **解决方案**：在 `react-native 项目名/android/build.gradle`中修改maven 的远程地址，默认用的是google 的，在中国被墙了，一般是下载不出来的。这里建议换成阿里的镜像服务器地址。然后再次运行就可以了。修改如下:
    ```
    buildscript {
        repositories {
            jcenter()
            maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' } //在此处添加阿里maven镜像服务器
        }
        ...
    }
    allprojects {
        repositories {
            mavenLocal()
            jcenter()
            maven {
                url "$rootDir/../node_modules/react-native/android"
            }
            maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' } //在此处添加阿里maven镜像服务器
        }
    }
    ...

    ```
1. 在React Native 中,使用`react-navigation`的tab 导航+FlatList 组件很卡，导致其他tab点击出现卡死的情况。
    
    **解决方案**： 优化`FlatList`的`onEndReachedThreshold`属性，设置为`onEndReachedThreshold=0.1`，如果是异步加载数据的话可以把分页的数据长度设置小点，我的项目里面设置的是5。即每次`FlatList`滑动到底部的时候，再去请求后台数据，请求的数据长度为5条记录

1. 在android中，项目里面使用了`code-push`，需要打个一个测试包出来进行测试，也就是`code-push` 里面的staging,按照`code-push`的文档在`android/app/build.gradle`里面进行配置
    ```
    buildTypes {
            debug {
                buildConfigField "String", "CODEPUSH_KEY", '""'
            }
            release {
                minifyEnabled enableProguardInReleaseBuilds
                proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
                signingConfig signingConfigs.release
                 buildConfigField "String", "CODEPUSH_KEY", '"CODE-PUSH-KEY"'
            }
            releaseStaging {
                minifyEnabled enableProguardInReleaseBuilds
                proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
                signingConfig signingConfigs.release
                 buildConfigField "String", "CODEPUSH_KEY", '"WaU6FbgL8FnipCODE-PUSH-KEY"'
            }
        }
    ```
    然后运行`gradlew assembleReleaseStaging`报错。错误如下
    ```
    FAILURE: Build failed with an exception.
    
    * What went wrong:
    Could not determine the dependencies of task ':app:lintVitalReleaseStaging'.
    > Could not resolve all task dependencies for configuration ':app:releaseStagingRuntimeClasspath'.
       > Could not resolve project :react-native-picker.
         Required by:
             project :app
          > Unable to find a matching configuration of project :react-native-picker:
              - Configuration 'debugApiElements':
                  - Required com.android.build.api.attributes.BuildTypeAttr 'releaseStaging' and found incompatible value 'debug'.
                  - Required com.android.build.gradle.internal.dependency.AndroidTypeAttr 'Aar' and found compatible value 'Aar'.
                  - Found com.android.build.gradle.internal.dependency.VariantAttr 'debug' but wasn't required.
                  - Required org.gradle.api.attributes.Usage 'java-runtime' and found incompatible value 'java-api'.
              - Configuration 'debugRuntimeElements':
                  - Required com.android.build.api.attributes.BuildTypeAttr 'releaseStaging' and found incompatible value 'debug'.
                  - Required com.android.build.gradle.internal.dependency.AndroidTypeAttr 'Aar' and found compatible value 'Aar'.
                  - Found com.android.build.gradle.internal.dependency.VariantAttr 'debug' but wasn't required.
                  - Required org.gradle.api.attributes.Usage 'java-runtime' and found compatible value 'java-runtime'.
              - Configuration 'releaseApiElements':
                  - Required com.android.build.api.attributes.BuildTypeAttr 'releaseStaging' and found incompatible value 'release'.
                  - Required com.android.build.gradle.internal.dependency.AndroidTypeAttr 'Aar' and found compatible value 'Aar'.
                  - Found com.android.build.gradle.internal.dependency.VariantAttr 'release' but wasn't required.
                  - Required org.gradle.api.attributes.Usage 'java-runtime' and found incompatible value 'java-api'.
              - Configuration 'releaseRuntimeElements':
                  - Required com.android.build.api.attributes.BuildTypeAttr 'releaseStaging' and found incompatible value 'release'.
                  - Required com.android.build.gradle.internal.dependency.AndroidTypeAttr 'Aar' and found compatible value 'Aar'.
                  - Found com.android.build.gradle.internal.dependency.VariantAttr 'release' but wasn't required.
                  - Required org.gradle.api.attributes.Usage 'java-runtime' and found compatible value 'java-runtime'.
    ``` 

    **解决方案**：修改`android/app/build.gradle`里配置为如下：
    
    ```
        buildTypes {
                debug {
                    buildConfigField "String", "CODEPUSH_KEY", '""'
                }
                release {
                    minifyEnabled enableProguardInReleaseBuilds
                    proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
                    signingConfig signingConfigs.release
                     buildConfigField "String", "CODEPUSH_KEY", '"CODE-PUSH-KEY"'
                }
                releaseStaging {
                    minifyEnabled enableProguardInReleaseBuilds
                    proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
                    signingConfig signingConfigs.release
                     buildConfigField "String", "CODEPUSH_KEY", '"WaU6FbgL8FnipCODE-PUSH-KEY"'
                     //指定匹配失败匹配其他变体
                    matchingFallbacks = ['debug', 'qa', 'release'] // 添加这段代码
                }
            }
    ```
    然后运行`gradlew assembleReleaseStaging` 即可打包成功
    
1. 在android中使用gif 图片，按照中文网里面说明，在`android/app/build.gradle`中加入了一下代码，
    
    ```
        dependencies {
          // 如果你需要支持Android4.0(API level 14)之前的版本
          compile 'com.facebook.fresco:animated-base-support:1.9.0'
        
          // 如果你需要支持GIF动图
          compile 'com.facebook.fresco:animated-gif:1.9.0'
        
          // 如果你需要支持WebP格式，包括WebP动图
          compile 'com.facebook.fresco:animated-webp:1.9.0'
          compile 'com.facebook.fresco:webpsupport:1.9.0'
        
          // 如果只需要支持WebP格式而不需要动图
          compile 'com.facebook.fresco:webpsupport:1.9.0'
        }
    ```
    重新生成debug.apk闪退问题。
    
    **解决方案**：这个问题是当前RN 的内置依赖有问题，`cmd` 进入`android`目录下,运行一下命令`gradlew -q app:dependencies
        `查看当前RN 的`com.facebook.fresco:fresco:1.3.0`依赖版本是多少，我这儿是`1.3.0`。
    ```
            +--- com.android.support:appcompat-v7:23.0.1
            |    \--- com.android.support:support-v4:23.0.1
            |         \--- com.android.support:support-annotations:23.0.1
            \--- com.facebook.react:react-native:+ -> 0.53.3
                 +--- javax.inject:javax.inject:1
                 +--- com.android.support:appcompat-v7:23.0.1 (*)
                 +--- com.facebook.fbui.textlayoutbuilder:textlayoutbuilder:1.0.0
                 |    \--- com.facebook.fbui.textlayoutbuilder:staticlayout-proxy:1.0
                 +--- com.facebook.fresco:fresco:1.3.0 // 这儿是你的 fresco依赖版本
                 |    +--- com.facebook.fresco:drawee:1.3.0
                 |    |    \--- com.facebook.fresco:fbcore:1.3.0
                 |    +--- com.facebook.fresco:fbcore:1.3.0
                 |    \--- com.facebook.fresco:imagepipeline:1.3.0
                 |         +--- com.parse.bolts:bolts-tasks:1.4.0
                 |         +--- com.facebook.fresco:fbcore:1.3.0
                 |         \--- com.facebook.fresco:imagepipeline-base:1.3.0
                 |              +--- com.parse.bolts:bolts-tasks:1.4.0
                 |              \--- com.facebook.fresco:fbcore:1.3.0
                 +--- com.facebook.fresco:imagepipeline-okhttp3:1.3.0
                 |    +--- com.squareup.okhttp3:okhttp:3.6.0
                 |    |    \--- com.squareup.okio:okio:1.11.0 -> 1.13.0
                 |    +--- com.facebook.fresco:fbcore:1.3.0
                 |    \--- com.facebook.fresco:imagepipeline:1.3.0 (*)
                 +--- com.facebook.soloader:soloader:0.1.0
                 +--- com.google.code.findbugs:jsr305:3.0.0
                 +--- com.squareup.okhttp3:okhttp:3.6.0 (*)
                 +--- com.squareup.okhttp3:okhttp-urlconnection:3.6.0
                 |    \--- com.squareup.okhttp3:okhttp:3.6.0 (*)
                 +--- com.squareup.okio:okio:1.13.0
                 \--- org.webkit:android-jsc:r174650
            
    ```
    然后将上面的依赖对应修改,修改完成之后如下:
    ```
        dependencies {
          // 如果你需要支持Android4.0(API level 14)之前的版本
          compile 'com.facebook.fresco:animated-base-support:1.3.0' // 修改版本1.9.0 为1.3.0
        
          // 如果你需要支持GIF动图
          compile 'com.facebook.fresco:animated-gif:1.3.0'// 修改版本1.9.0 为1.3.0
        
          // 如果你需要支持WebP格式，包括WebP动图
          compile 'com.facebook.fresco:animated-webp:1.3.0'// 修改版本1.9.0 为1.3.0
          compile 'com.facebook.fresco:webpsupport:1.3.0'// 修改版本1.9.0 为1.3.0
        
          // 如果只需要支持WebP格式而不需要动图
          compile 'com.facebook.fresco:webpsupport:1.3.0'// 修改版本1.9.0 为1.3.0
        }
    ```
    重新运行即可，就不会出现闪退了
    
1. 在React Native中使用mobx 报错,错误如下
   ```
   Can't find variable: Symbol 
   ```
   **解决方案**：出现这个问题一般来说是`mobx`的版本太高了，降低一些mobx的版本即可。
   在`package.json`中使用如下的版本即可
   ```
   {
       dependencies:{
           ...
            "mobx": "^4.3.0",
            "mobx-react": "^5.2.5",
           ...
       }
   }
   ```
1. 在React Native中 'config.h' file not found
   ```
   'config.h' file not found
   ```
   **解决方案**：
   ```
   cd node_modules/react-native/third-party/glog-0.3.4
    ../../scripts/ios-configure-glog.sh
   ```
1. 在React Native中 运行 `react-native run-android` 报如下错误
    ```
    Could not create service of type GeneralCompileCaches using GradleScopeCompileServices.createGeneralCompileCaches().
    ```
   **解决方案**：
   ```
    cd android 
    ./gradlew compileDebugJavaWithJavac --stacktrace
   ```
1. 在React Native中真机调试模式下报跨域错误的问题！

   **解决方案**：
   ```
   open -n /Applications/Google\ Chrome.app/ --args --disable-web-security --user-data-dir=/Users/zhangxueyan/data
   ```
1. 在React Native中`react-native-code-push`和`react-native-amap3d` 函数名称冲突问题.
    ```
    duplicate symbol _aes_encrypt_key128 in:
        /Users/berry/Documents/webstrom/BerryApp/ios/Pods/AMap3DMap/MAMapKit.framework/MAMapKit(MAMapKit-x86_64-master.o)
        /Users/berry/Library/Developer/Xcode/DerivedData/BerryAPP-arhfppjvlmwpaofkjfpfcpotishq/Build/Products/Debug-iphonesimulator/SSZipArchive/libSSZipArchive.a(aeskey.o)
    duplicate symbol _aes_decrypt_key128 in:
        /Users/berry/Documents/webstrom/BerryApp/ios/Pods/AMap3DMap/MAMapKit.framework/MAMapKit(MAMapKit-x86_64-master.o)
        /Users/berry/Library/Developer/Xcode/DerivedData/BerryAPP-arhfppjvlmwpaofkjfpfcpotishq/Build/Products/Debug-iphonesimulator/SSZipArchive/libSSZipArchive.a(aeskey.o)
    ld: 2 duplicate symbols for architecture x86_64
    clang: error: linker command failed with exit code 1 (use -v to see invocation)
    ```
   **解决方案**：
   找到`../node_modules/react-native-code-push/ios`，在里面进行搜索`aes_encrypt_key128`和`aes_decrypt_key128`这两个关键字。将其替换成为自己定义的函数名称，这儿我定义的为`cp_aes_encrypt_key128`和`cp_aes_decrypt_key128`这个。如果用了这种方式来解决上面的重复定义，就不能通过`pod`来安装`react-native-code-push`了。应该使用[手动的方式](https://github.com/Microsoft/react-native-code-push/blob/master/docs/setup-ios.md#plugin-installation-ios---manual)来安装`react-native-code-push`。我把我改的`react-native-code-push`放到了GitHub上面了。可以直接通过`yarn add git+https://github.com/25juan/react-native-code-push.git`来安装修改之后的版本
   [详情参见](https://github.com/25juan/react-native-code-push)
   
 1. `react native`打正式包报错如下：
    ```
    Execution failed for task ':jmrtc-react-native:verifyReleaseResources'.
        > java.util.concurrent.ExecutionException: com.android.builder.internal.aapt.v2.Aapt2Exception: Android resource linking failed
          error: resource android:style/TextAppearance.Material.Widget.Button.Borderless.Colored not found.
          error: resource android:style/TextAppearance.Material.Widget.Button.Colored not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values-v26\values-v26.xml:7: error: resource android:attr/colorError not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values-v26\values-v26.xml:11: error: resource android:attr/colorError not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values-v26\values-v26.xml:15: error: style attribute 'android:attr/keyboardNavigationCluster' not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values-v28\values-v28.xml:7: error: resource android:attr/dialogCornerRadius not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values-v28\values-v28.xml:11: error: resource android:attr/dialogCornerRadius not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3036: error: resource android:attr/fontStyle not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3037: error: resource android:attr/font not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3038: error: resource android:attr/fontWeight not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3039: error: resource android:attr/fontVariationSettings not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3040: error: resource android:attr/ttcIndex not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3121: error: resource android:attr/startX not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3124: error: resource android:attr/startY not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3127: error: resource android:attr/endX not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3130: error: resource android:attr/endY not found.
          F:\react-native\JRCT\node_modules\jmrtc-react-native\android\build\intermediates\res\merged\release\values\values.xml:3138: error: resource android:attr/offset not found.
    ```
    **解决方案**：修改目标依赖的 build.gradle `compileSdkVersion`、`buildToolsVersion`、`minSdkVersion`、`targetSdkVersion` 版本同项目一致即可解决
1. 在React Native中`react-native-code-push`和`react-native-amap3d` 函数名称冲突问题.
    ```
    duplicate symbol _aes_encrypt_key128 in:
        /Users/berry/Documents/webstrom/BerryApp/ios/Pods/AMap3DMap/MAMapKit.framework/MAMapKit(MAMapKit-x86_64-master.o)
        /Users/berry/Library/Developer/Xcode/DerivedData/BerryAPP-arhfppjvlmwpaofkjfpfcpotishq/Build/Products/Debug-iphonesimulator/SSZipArchive/libSSZipArchive.a(aeskey.o)
    duplicate symbol _aes_decrypt_key128 in:
        /Users/berry/Documents/webstrom/BerryApp/ios/Pods/AMap3DMap/MAMapKit.framework/MAMapKit(MAMapKit-x86_64-master.o)
        /Users/berry/Library/Developer/Xcode/DerivedData/BerryAPP-arhfppjvlmwpaofkjfpfcpotishq/Build/Products/Debug-iphonesimulator/SSZipArchive/libSSZipArchive.a(aeskey.o)
    ld: 2 duplicate symbols for architecture x86_64
    clang: error: linker command failed with exit code 1 (use -v to see invocation)
    ```
   **解决方案**：
   找到`../node_modules/react-native-code-push/ios`，在里面进行搜索`aes_encrypt_key128`和`aes_decrypt_key128`这两个关键字。将其替换成为自己定义的函数名称，这儿我定义的为`cp_aes_encrypt_key128`和`cp_aes_decrypt_key128`这个。如果用了这种方式来解决上面的重复定义，就不能通过`pod`来安装`react-native-code-push`了。应该使用[手动的方式](https://github.com/Microsoft/react-native-code-push/blob/master/docs/setup-ios.md#plugin-installation-ios---manual)来安装`react-native-code-push`。我把我改的`react-native-code-push`放到了GitHub上面了。可以直接通过`yarn add git+https://github.com/25juan/react-native-code-push.git`来安装修改之后的版本
   [详情参见](https://github.com/25juan/react-native-code-push)
   
1. `react native ios`运行报错打正式包报错如下：
    ```
    error: Cycle inside Pods-react_native_templateTests; building could produce unreliable results.
    Cycle details:
    Target build order preserved because “Parallelize Build” is off
    
    → Target 'Pods-react_native_templateTests': Libtool /Users/mac/Library/Developer/Xcode/DerivedData/react_native_template-bjrgercjrgrfuyepqfrqbiitqcpl/Build/Products/Debug-iphonesimulator/libPods-react_native_templateTests.a normal x86_64
    ○ Target 'Pods-react_native_templateTests' has target dependency on Target 'react_native_template' due to target order in a “Target Dependencies” build phase or the scheme
    ○ That command depends on command in Target 'react_native_template': script phase “[CP] Copy Pods Resources”
    ```
    **解决方案**：
    修改build system 在Xcode菜单栏 -> File -> Workspace Setting，将build system修改为legacy build system，然后clean后编译。即可解决。
    
    ![](https://user-gold-cdn.xitu.io/2019/8/17/16c9d2518d9d7cdb?w=534&h=562&f=png&s=474476)
    
    ![](https://user-gold-cdn.xitu.io/2019/8/17/16c9d259ed95e744?w=558&h=482&f=png&s=184264)
1. `react native`中使用mobx-react报错,这种情况一般是用了mobx-react  @inject这个注入store方法造成的：
    ```
   Expected a constructor
    ```
    **解决方案**：
    ```
    @inject("store")
    @observer
    class Book extends Component{
        render(){
           return <Text>{this.props.store.fullName}</Text>
        }
    }
    ```
    只需要改成如下使用方式即可
    ```
    @observer
    class Book extends React.Component{
      render(){
        return <Text>{this.props.store.fullName}</Text>
      }
    }
    let HOCBook = inject("store")(Book);
    ```
1. MacOS中，每次运行`react native`项目都需要加上`sudo`的问题。
    ```
     Error: EPERM: operation not permitted, chmod '/Users/apple/Documents/code/webstorm/micaiwang/node_modules/@react-native-community/cli/build/commands/server/external/xsel'
        at Object.chmodSync (fs.js:1023:3)
        at Object.<anonymous> (/Users/apple/Documents/code/webstorm/micaiwang/node_modules/@react-native-community/cli/build/commands/server/copyToClipBoard.js:50:15)
        at Module._compile (internal/modules/cjs/loader.js:689:30)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:700:10)
        at Module.load (internal/modules/cjs/loader.js:599:32)
        at tryModuleLoad (internal/modules/cjs/loader.js:538:12)
        at Function.Module._load (internal/modules/cjs/loader.js:530:3)
        at Module.require (internal/modules/cjs/loader.js:637:17)
        at require (internal/modules/cjs/helpers.js:22:18)
        at Object.<anonymous> (/Users/apple/Documents/code/webstorm/micaiwang/node_modules/@react-native-community/cli/build/commands/server/middleware/copyToClipBoardMiddleware.js:8:47)
    ```
    **解决方法**
    
    在项目根目录下执行如下命令``` sudo chown -R `whoami` ./ ```,让后再执行`react-native run-android`。这个时候即可不加`sudo`命令
    
1. 记一次真实的flatlist组件+react-navigation tabNavigation导致tab切换卡顿的问题。
    ```jsx
     class extends Component{
        renderHeader = () => {
            return (
                    <View>
                        <Swiper onPress={this.onImagePress} data={this.state.tops}/>
                        ... // 此处省略代码
                    </View>
                )
        };
         
        render(){
             reuturn <FlatList
                        ListHeaderComponent={this.renderHeader()} // 注意此处
                        onRefresh={this.refresh}
                        onLoadMore={this.loadMore}
                        onEndReachedThreshold={0.1}
                        ref={flatList=>this.flatList = flatList }
                        renderItem={this.renderItem}/>
         }
     }
    ```
    **解决方案**
    
    ListHeaderComponent这个属性的赋值千万不要写成了ListHeaderComponent={this.renderHeader}，传一个React元素过去，这边在flatlist更新的时候能够提高渲染性能。footer同理。

1. jetify 修复AndroidX的问题`找不到android.support.v4.view.ViewPager`
    **解决方案**
    ```
    cd android
    ./gradlew clean
    npx jetify
    ```
    
1. 修复IOS webview 长按显示英文问题，在info.plist里面添加
    **解决方案**
    ```
    <key>CFBundleLocalizations</key>
	<array>
		<string>zh_CN</string>
		<string>en</string>
	</array>
    ```





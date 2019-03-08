[groovy语法中的+解释为：如果在行尾认为是append，如果是在行首认为是positive，如+6视为正6，见stackOverFlow](https://stackoverflow.com/questions/31044673/groovy-postgres-no-signature-of-method-java-lang-string-positive)
---
<hr class="hr1"/>

```groovy
def fileName = "${buildTypeName}_${flavorTypeName}_${versionName}_${releaseTime()}.apk"
//and this statement has an error
//> A problem occurred configuring project ':app'.
//> No signature of method: java.lang.String.call() is applicable for argument types: () values: []
//>   Possible solutions: wait(), any(), wait(long), each(groovy.lang.Closure), take(int), any(groovy.lang.Closure)
```

#### __BuildVariant__

```groovy
buildTypes{
    debug{  
    //通常不显示   
    ext.enableCrashlytics = false   //停用crashlytics报告以提高构建速度
    }   
    release{}   
}

```
flavorDimensions 'part','com'
这个列表按优先级从高到低的优先级排序，gradle编译合并构建变体的资源和配置时，必须为每一个flavor指定dimension；
可以仅指定一部分资源参与配置， resConfigs
productFlavors {
pro{
    resConfigs "en","xxhdpi"
}
dev{

}

}


[developer.android 构建](https://developer.android.google.cn/studio/build/dependencies)  
---
关于不同的构建其源集的优先级(由低到高)：
libraries >> mainSourceSet >> productFlavor >> buildType >> buildVariant
低优先级的资源会被高优先级的替换

AndroidManifest.xml
如果有多个构建变体或者存在库文件的AndroidManifest(一般库文件都存在)，高优先级会使用合并算法将低优先级的文件一步一步合并到高优先级，同时
我们可以使用指定tools命名空间以及在不同的元素中应用tools：node = "replace","remove","merge"等标签，或者replace = ","等，
> Subsequent supplement

#### __deference between PackageName and ApplicationID__
ApplicationID 是应用的唯一标识，可以通过在不同的构建中指定不同的applicationId    
packageName 是应用的包名，用来指定所有引用资源的R.java文件的命名空间以及AndroidManifest文件中各个元素的类名称；
实际上packageName在构建结束后会使用gradle的applicationId替换，googlePlay或者Android平台以此属性作为id；
即在构建时使用androidManifest的packageName用作R.java的命名空间以及指定类的名称后就会舍弃此值。

> 划重点: 如果是WebView，应该使用packageName作为applicationID的前缀  WebView 53 and onward will attempt to strip package name components from the app package name until it finds an R class, which will deal with the simplest cases here (applicationIdSuffix appending a suffix to the base name), but not other cases where the package name is unrelated to the R class name.

#### __Dependency Type__

##### **Local library module dependency**
implementation project(":myProject")  
其中myProject是一个module，并且必须和setting.gradle里的配置名称一致

##### **Local binary dependency**
implementation fileTree(dir:'libs', include:['*.jar'])
这里默认的路径为 module/,可以自定义路径，同时修改dir的值


##### **Remote binary dependency**
implementation 'com.example.android:app-magic:12.3'
com.example.android 命名空间（包名） app-magic  名称   12.3 版本号

#### __Dependency Configuration__
> 一下的各个依赖配置为Android Gradle Plugin 3.0.0 以后的，如果之前存在不同的声明配置，使用()标记；如implementation(compile)

- implementation(compile)
编译时期该依赖不暴露给当前module的上游module，仅在运行时暴露；一旦该依赖的api发生改变，仅仅当前module受影响，除了当前module以外的其他的不需要重新编译(recompile)
- api(compile)
该依赖在编译期或者运行时对所有模块而言都是可见的，即如果该依赖发生变化，所有的模块都需要重新编译；
以上两者的区别为implementation会缩短构建时间，目前大多数都使用这个。
- compileOnly(provided)
仅在编译时引用，可以减小apk体积
- runtimeOnly(apk)
仅在运行时引用
- annotationProcessor(compile)


如果是单独为productFlavors配置单独的依赖配置可以在默认的项目依赖里使用  ${productFlavors}Implementation;
如果是为BuildVariant配置，则必须添加如下配置：
configurations {
//在这里为该构建变体配置一个配置占位（placeHolder），然后可以在依赖里使用该配置
${productFlavor}${buildType}Implementation
}

> test / androidText 依赖不能被其他模块所依赖，所以只能使用implementation而不是api





#### AAPT2 error: check logs for details
just update gradle wrapper url to 4.8 and update build.gradle to 3.2.1





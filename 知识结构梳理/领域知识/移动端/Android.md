- Android Unity Crash原因定位（64位）：aarch64-linux-android-addr2line -f -C -e E:\Unity\Editor2017\Editor\Data\PlaybackEngines\AndroidPlayer\Variations\il2cpp\Release\Symbols\armeabi-v7a\[libunity.sym.so](http://libunity.sym.so) 0049b648
- 修改ADB端口:环境变量中添加 ANDROID_ADB_SERVER_PORT:5037
- ADB列出已连接设备：`adb devices`
- ADB指定设备安装APK：`adb -s 设备 install apk文件.apk`
- 连接模拟器：`adb connect 127.0.0.1:7555`
- logcat进程过滤：
    - adb shell pm list packages -3//查看所有第三方应用包名
    - adb shell ps |findstr packageName//根据包名获取进程号(PID)
    - adb logcat | findstr PID//根据PID过滤日志
- Android11无线调试：
    - adb pair ipaddr:port
    - adb connect ipaddr:port
- apktool反编译包: `apktool.jar d 需要反编译的APK文件.apk`
- apktool包文件夹编译为APK(此APK未经签名)：`apktool b APK文件夹`
- 为APK签名：`jarsigner -verbose -sigalg SHA1withRSA -keystore 签名文件 -signedjar 签名后的APK名.apk 签名前的APK名.apk 签名文件别名`
- 解压jar包：`jar -xvf jar文件.jar`
- 将解压的jar包打包为jar：`jar -cvf 打包后名称.jar 文件夹地址(./)`
- 查看APK签名：`keytool -list -printcert -jarfile apk名.apk`
- 查看keystore详细信息：`keytool -list -v -keystore keystore地址`

Android 权限API等级分为（获取存储路径为例）：

1. 无需在AndroidManifest.xml中声明，即可使用<getFilesDir()>
2. 需要在AndroidManifest.xml中声明，才能使用的<getExternalFilesDir("")>
3. 需要在AndroidManifest.xml中声明，且需要动态申请的(弹窗请求)<getExternalStorageDirectory("")>

gradle 配置NDK编译需要 build.gradle中targets名字和Android.mk中LOCAL_MODULE_FILENAME相同(不需要lib前缀)

## 刘海适配

若想自身内容不被遮挡，可配置窗口属性LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER进行强制下压，或不做任何配置直接使用默认值；然后获取刘海的参数应用内部适配

```java
WindowManager.LayoutParams lp = getWindow().getAttributes();
lp.layoutInDisplayCutoutMode = WindowManager.LayoutParams.LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER;
```

若想自身内容显示在刘海区域下，可配置窗口属性LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES，系统将不做任何处理；

```java
WindowManager.LayoutParams lp = getWindow().getAttributes();
lp.layoutInDisplayCutoutMode = WindowManager.LayoutParams.LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES;
```

## 获取工程签名信息

```bash
./gradlew signingReport
```

## Android多语言实践

1. 多语言工具类MultiLanguageUtils
    
    ```java
    package com.proj.multi_language;
    
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.res.Configuration;
    import android.content.res.Resources;
    import android.os.Build;
    import android.os.LocaleList;
    import android.text.TextUtils;
    import android.util.DisplayMetrics;
    
    import androidx.core.os.ConfigurationCompat;
    import androidx.core.os.LocaleListCompat;
    
    import java.util.Locale;
    
    public class MultiLanguageUtils {
        /**
         * 获取应用语言
         * @param context context
         */
        public static Locale getAppLocale(Context context){
            if (Build.VERSION.SDK_INT>=Build.VERSION_CODES.N){
                return context.getResources().getConfiguration().getLocales().get(0);
            }
            return context.getResources().getConfiguration().locale;
        }
    
        /**
         * 获取系统语言列表
         * @return LocaleListCompat
         */
        public static LocaleListCompat getSystemLanguage(){
            Configuration configuration = Resources.getSystem().getConfiguration();
            return ConfigurationCompat.getLocales(configuration);
        }
    
        /**
         * 修改应用内语言
         * @param context context
         * @param language language
         * @param area area
         */
        public static void changeLanguage(Context context,String language,String area){
            saveLanguageData(context,language,area);
            if(TextUtils.isEmpty(language) && TextUtils.isEmpty(area)){
                return;
            }
            Locale locale = new Locale(language,area);
            setAppLanguage(context,locale);
        }
    
        public static boolean isSameWithSetting(Context context){
            Locale locale = getAppLocale(context);
            String language = locale.getLanguage();
            String country = locale.getCountry();
            SharedPreferences sp = context.getSharedPreferences("MultiLanguage", Context.MODE_PRIVATE);
            String sp_language = sp.getString("SP_LANGUAGE", "");
            String sp_country = sp.getString("SP_COUNTRY","");
            if (language.equals(sp_language) && country.equals(sp_country)) {
                return true;
            } else {
                return false;
            }
        }
    
        public static void setAppLanguage(Context context,Locale locale){
            Resources resources = context.getResources();
            DisplayMetrics metrics = resources.getDisplayMetrics();
            Configuration configuration = resources.getConfiguration();
            if (Build.VERSION.SDK_INT>=24){
                configuration.setLocale(locale);
                configuration.setLocales(new LocaleList(locale));
                context.createConfigurationContext(configuration);
                resources.updateConfiguration(configuration,metrics);
            }else if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.JELLY_BEAN_MR1 ){
                // Android 4.1 以上方法
                configuration.setLocale(locale);
                resources.updateConfiguration(configuration,metrics);
            }else{
                configuration.locale=locale;
                resources.updateConfiguration(configuration,metrics);
            }
        }
    
        /**
         * 本地保存语言记录
         * @param context context
         * @param language language
         * @param area area
         */
        public static void saveLanguageData(Context context,String language, String area){
            SharedPreferences sp = context.getSharedPreferences("MultiLanguage", Context.MODE_PRIVATE);
            SharedPreferences.Editor edit = sp.edit();
            edit.putString("SP_LANGUAGE",language);
            edit.putString("SP_COUNTRY", area);
            edit.apply();
        }
    }
    ```
    
2. 建立对应语言的strings.xml文件
    
3. 在Application中注册事件
    
    ```java
    			   //多语言设置
            registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
                @Override
                public void onActivityCreated(@NonNull Activity activity, @Nullable Bundle bundle) {
                    SharedPreferences sp = getApplicationContext().getSharedPreferences("MultiLanguage", Context.MODE_PRIVATE);
                    String sp_language = sp.getString("SP_LANGUAGE", "");
                    String sp_country = sp.getString("SP_COUNTRY","");
    									//语言设置发生改变
                    if(!MultiLanguageUtils.isSameWithSetting(getApplicationContext())){
                        Locale locale = new Locale(sp_language, sp_country);
                        MultiLanguageUtils.setAppLanguage(activity,locale);
                    }
                }
    ```
    
4. Activity中用户选择语言
    
    ```java
              	 if(radbtn.getText().equals("vi")){//选择了越南文
                    MultiLanguageUtils.changeLanguage(this, "vi","vn");
                }
    ```
    
5. AppName会随手机系统语言更改而更改
    

## Android多渠道实践

1. setting同目录创建flavors.gradle文件
    
    ```java
    android {
        flavorDimensions "versionCode"
        productFlavors{
            huawei{
                applicationId = "com.zjy.huawei"
                //不同渠道配置不同参数
                manifestPlaceholders=[tapDB_id:"bf56el4diz16tt2g",channel:"lsj", pay_channel_id:"101",apk_url:""]
                // 设置BuildConfig
                buildConfigField "int", "TEST_VALUE", "2"
            }
            xiaomi{
                applicationId = "com.zjy.xiaomi"
                manifestPlaceholders=[tapDB_id:"bf56el4diz16tt2g",channel:"lsj", pay_channel_id:"101",apk_url:""]
            }
            //遍历productFlavors多渠道，设置渠道号（xiaomi 、huawei）
            productFlavors.all {
                flavor -> flavor.manifestPlaceholders.put("CHANNEL", name)
            }
        }
        applicationVariants.all { variant ->
            // 打包完成后输出路径
            def name = ((project.name != "app") ? project.name : rootProject.name.replace(" ", "")) +
                    "_" + variant.flavorName +
                    "_" + variant.buildType.name +
                    "_" + variant.versionName +
                    "_" + new Date().format('yyyyMMddhhmm') + ".apk"
            //相对路径app/build/outputs/apk/huawei/release/
            def path = "../../../../../apk/" //相当于路径 app/apk/
            variant.outputs.each { output ->
                def outputFile = output.outputFile
                if (outputFile != null && outputFile.name.endsWith('.apk')) {
                    //指定路径输出
                    output.outputFileName = new File(path, name)
                }
            }
            // 在打包完成后还可以做一些别的操作，可以复制到指定目录，或者移动文件到指定目录
            variant.assemble.doLast {
                File out = new File("${project.rootDir}/apk")
                variant.outputs.forEach { file ->
                    //复制apk到指定文件夹
                    //copy {
                    //	from file.outputFile
                    //	into out
                    //}
                    //把文件移动到指定文件夹
                    ant.move file: file.outputFile,
                            todir: "${project.rootDir}/apk"
                }
            }
        }
    
        //多渠道签名的配置
        signingConfigs {
            xiaomi {
                storeFile file("../xiaomi.keystore")
                storePassword 'xiaomi'
                keyAlias 'xiaomi'
                keyPassword 'xiaomi'
                v1SigningEnabled true
                v2SigningEnabled true
            }
            huawei {
                storeFile file("../huawei.keystore")
                storePassword 'huawei'
                keyAlias 'huawei'
                keyPassword 'huawei'
                v1SigningEnabled true
                v2SigningEnabled true
            }
        }
        buildTypes {
            release {
                productFlavors.xiaomi.signingConfig signingConfigs.xiaomi
                productFlavors.huawei.signingConfig signingConfigs.huawei
            }
        }
        //不同渠道不同资源文件配置
        sourceSets{
            xiaomi.res.srcDirs 'src/main/res-xiaomi'
            huawei.res.srcDirs 'src/main/res-huawei'
        }
        //不同渠道不同的依赖文件
        dependencies {
            xiaomiApi('androidx.appcompat:appcompat:1.4.1')
            huaweiImplementation('androidx.appcompat:appcompat:1.4.1')
        }
    }
    ```
    
2. 在app级build.gradle中导入flavors.gradle
    
    ```java
    apply from: rootProject.file("flavors.gradle")
    ```
    
3. 在Android Studio的Build Variants中切换渠道
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cce47430-8724-46ed-9569-fbb4e13c0528/1494bced-d1e7-448b-a109-8cf3b144eabe/Untitled.png)
    
4. 渠道出包命令行
    
    ```bash
    gradlew assemblehuaweiRelease
    ```
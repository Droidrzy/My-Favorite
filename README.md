# My-Favorite
Droidrzy_Favorite
## 依赖注入的概念
 ## 依赖
- 例如下面的代码中HRSystem就依赖与Person。

示例代码中Person这种直接初始化的方式也叫Hard Init方式（硬初始化）,弊端在于两个类之间不够独立,如果我们更改了Person的构造函数,所有使用到Person初始化方法的代码都要进行修改。

public class HRSystem {
    public static void main(String[] args) {
        Person person = new Person();
    }
}
依赖注入
依赖注入指的是程序运行过程中,调用者(HRSystem)需要被调用者(Person)的辅助,但是创建被调用者(Person)的工作不再由调用者(HRSystem)来完成,因此称为控制反转(IOC:Inversion of Control),而是由相关的容器控制程序将被调用者(Person)的对象在外部创建出来并注入到调用者(HRSystem)的引用中。

控制反转：
	控制反转指的是依赖对象的获得被反转了。基于这个结论，控制反转也可以被称作是依赖注入。
控制反转就是对象在被创建的时候，由一个调控系统内所有对象的控制程序将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。
所以，控制反转是：关于一个对象如何获取他所依赖的对象的引用，这个责任的反转。

依赖注入的优点：
	依赖注入是实现控制反转的方式之一(另一方式是依赖查找),目的就是为了让调用者和被调用者之间解耦。
	可以注入依赖的模拟实现,使得测试变得更加简单。

依赖查找
简而言之：
	给定上下文环境，在上下文环境中，根据预先定义好的名称，进行查找。

public class MyBusniessObject {
    private DataSource ds;
    private MyCollaborator myCollaborator;

    public MyBusnissObject() {
        Context ctx = null;
        try {
            ctx = new InitialContext();
            ds = (DataSource) ctx.lookup(“java:comp / env / dataSourceName”);
            myCollaborator =
		(MyCollaborator) ctx.lookup(“java:comp / env / myCollaboratorName”);
        }
    }
}
依赖注入框架
Butterknife
主页
 https://github.com/JakeWharton/butterknife
用途
主要用来简化各种初始化控件的操作，除此之外，还可以绑定资源、点击事件等。
添加依赖（老版本添加依赖方式，此处使用7.0.1版本）
在app/build.gradle文件中dependencies节点添加如下代码：
compile 'com.jakewharton:butterknife:7.0.1'
安装相关AndroidStudio插件
插件安装方式：
在Android Studio中点击 File-->Settings-->Plugins-->Browse repositories,搜索Android ButterKnife Zelezny插件,安装成功以后重启Android Studio。

插件使用前提：
在布局文件中添加控件的时候,所有需要在Activity/Fragment代码中进行控制的控件都要添加id属性。

插件使用方式：
在Activity/Fragment 代码中,将鼠标放在布局文件的引用上(即R.layout.activity_main中的activity_main上面),此时按快捷键Alt + Insert或鼠标右键选择Generate，在弹出的菜单中选择Generate ButterKnife Injections。
此时会再次弹出一个对话框，新的对话框中点击confirm后，直接生成控件的引用,代替findViewById。
如果需要处理控件的点击事件,可以选择对应控件的OnClick复选框。
如果是ListView的Item视图,还可以选择左下角的Create ViewHolder复选框,生成ViewHolder静态类。
在AndroidStudio模板中直接添加ButterKnife的依赖
在
AndroidStudio\plugins\android\lib\templates\gradle-projects\NewAndroidModule\recipe.xml.ftl中添加如下代码,以后新创建的工程默认会添加Butterknife依赖：

<dependency mavenUrl="com.jakewharton:butterknife:7.0.1" />

ButterKnife 8.0.1的使用（与老版本变化较大）
ButterKnife 8.0.1在使用方式上较上个版本有了较大的变化：
增加了资源绑定，并且将 compiler 分离了出来，引用方式上也做了改变。

引用方式：
	首先，在Project级别的build.gradle中添加如下配置(标红)：
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0-alpha1'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8' //配置Android annotations
    }
}

	在Module级别的build.gradle中添加如下配置(标红)：
apply plugin: 'com.android.application'
apply plugin: 'android-apt'

android {
……
}

dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    compile 'com.android.support:appcompat-v7:23.4.0'
    compile 'com.jakewharton:butterknife:8.1.0'
    apt 'com.jakewharton:butterknife-compiler:8.1.0'
}

详细使用介绍：
http://jakewharton.github.io/butterknife/

示例Demo：
/**
 *  ButterKnife 8.1.0版本 使用演示
 */
public class MainActivity extends AppCompatActivity {

    /**  使用 ButterKnife 替代 findViewById*/
    @BindView(R.id.textView)
    TextView mTextView;

    @BindView(R.id.button)
    Button mButton;

    /**  使用 ButterKnife 绑定资源*/
    @BindString(R.string.tv_content)
    String mContent;

    /**  使用 ButterKnife 构建控件实例集合*/
    @BindViews({ R.id.edit1, R.id.edit2, R.id.edit3 })
    List<EditText> nameViews;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // ButterKnife注入
        ButterKnife.bind(this);
    }

    /**
     *  为Button添加点击事件
     */
    @OnClick({R.id.button, R.id.button2})
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.button:
                mTextView.setText(mContent);
                break;
            case R.id.button2:
                insertValues();
                break;
        }
    }

    public void insertValues() {
        // 为nameViews的每个EditText赋值
        ButterKnife.apply(nameViews, new ButterKnife.Action<EditText>() {
            @Override
            public void apply(@NonNull EditText view, int index) {
                view.setText("我是第" + index + "被赋值的");
            }
        });

        // 修改nameViews的每个EditText的属性，使其旋转20度
        ButterKnife.apply(nameViews, View.ROTATION, 20.0f);
    }
}
AndroidAnnotations
主页
 http://androidannotations.org/
https://github.com/excilys/androidannotations/wiki
用途
	使用依赖注入Views,extras,System Service,resources
	简化线程模型
	事件绑定
	REST Client（网络请求）
配置方式
https://github.com/excilys/androidannotations/wiki/Building-Project-Gradle

在Project的build.gradle中添加如下配置（标红）：
buildscript {
    repositories {
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0-alpha1'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8' //配置Android annotations
    }
}

allprojects {
    repositories {
        jcenter()
        mavenCentral()
        mavenLocal()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

在项目Module的build.gradle中添加如下配置信息（标红）:
apply plugin: 'com.android.application'
apply plugin: 'android-apt'

android {
……
}

apt {
    arguments {
        androidManifestFile variant.outputs[0]?.processResources?.manifestFile
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:23.4.0'
    compile 'com.android.support.constraint:constraint-layout:1.0.0-alpha1'
    compile "org.androidannotations:androidannotations-api:4.0.0"
    apt "org.androidannotations:androidannotations:4.0.0"
}
使用注意事项
	Manifest中注册的activity要在原类名后追加下划线“_”
	使用注解的控件和方法不能使用private修饰符  
	大型项目并不适用
AndroidAnnotations vs Butterknife
AndroidAnnotations是在编译时执行生成代码，在运行应用时，对性能没有影响。 
Butterknife会在应用运行时反射执行，可能会有一些性能方面的影响。
但是Butterknife用起来方便一些，而且AndroidStudio有相关的插件，可以配合ButterKnife使用。
使用范例
前提：
	1. 配置AndroidAnnotation
	2. 修改清单文件，将Activity注册的原类名后加一个下划线“_”
		配置前：
<activity android:name=".MainActivity">
		配置后：
<activity android:name=".MainActivity_">

示例代码：
@Fullscreen //配置Activity全屏展示
@WindowFeature(Window.FEATURE_NO_TITLE) // 配置Activity不显示ActionBar
@EActivity(R.layout.activity_main) // 替代setContentView
public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";

    /** 替代findViewById */
    @ViewById(R.id.textView)
    TextView mTv;

    @Click(R.id.button) // 注册点击事件
    public void changeTv() {
        Log.e(TAG, "button click ======= " + Thread.currentThread().getName());
        doInBackGround();
    }

    @Background // 标识此方法运行在子线程
    public void doInBackGround() {
        Log.e(TAG, "doInBackGround ======= " + Thread.currentThread().getName());
        doInMain();
    }

    @UiThread // 标识此方法运行在主线程
    public void doInMain() {
        Log.e(TAG, "doInMain ======= " + Thread.currentThread().getName());
        mTv.setText("我的内容被更改啦...");
    }
}

运行结果：
 
所有可用注解列表
https://github.com/excilys/androidannotations/wiki/AvailableAnnotations
Dagger2
主页
 https://github.com/google/dagger
历史
Dagger1是由Square公司受到Google公司的Guice(https://github.com/google/guice)启发创建的依赖注入框架。
Dagger2是Dagger1(https://github.com/square/dagger)的分支，由谷歌开发。该项目受到了Google的Auto项目(https://github.com/google/auto)的启发。
Dagger2的优点
	没有使用反射,图的验证、配置和预先设置都在编译的时候执行
	容易调试,完全具体地调用提供和创建的堆栈
	更高的性能,谷歌声称他们提高了13%的处理性能
	代码混淆,使用派遣方法，就如同自己写的代码一样
与其他依赖注入框架的区别
其他的框架，类似ButterKnife和AndroidAnnotations，主要专注于替代findViewById，注解某些事件监听等等。

Dagger2则是用于注入类的实例，解决在创建对象时造成的耦合严重的问题。
配置方式
在Project级别的build.gradle中添加如下配置(标红)：
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0-alpha1'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8' //配置Android annotations
    }
}

在项目Module的build.gradle中添加如下配置信息（标红）:
apply plugin: 'com.android.application'
apply plugin: 'com.neenbedankt.android-apt'

android {
……
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:23.4.0'
    compile 'com.google.dagger:dagger:2.2'
    apt 'com.google.dagger:dagger-compiler:2.2'
    provided 'org.glassfish:javax.annotation:10.0-b28'
}
主要的组件
@Provides
用来修饰方法，方法的返回类型就是所提供的依赖类型，可以简单的把方法的返回值理解为我们通常要new出来的对象。

@Provides方法都会用provide作为前缀。
@Module
所有的@Provides方法都必须放到一个Moudle中，一个Moudle就是使用@Moudle修饰的类,可以简单的理解为一个持有各种对象的仓库，用来给@Component组件提供实例化的对象。

Android中有三种Module：
ApplicationModule，ActivityModule，UserModule
这三种Module分别提供整个应用级别的（只生成一个该组件的实例），Activity级别的，用户级别的Module。

@Module类都用Module作为后缀。
@Inject
通常在需要依赖的地方使用这个注解，简单的理解为声明一个需要被注入的对象。
@Component
@Component是@Inject和@Module的桥梁，它的主要作用就是连接这两个部分。

我们必须用@Component注解一个接口，为该注解传入Module类，或者添加其依赖的组件。

@Component注解的接口或抽象类都是以Component为后缀。
@Component的实现类
@Component注解的接口或抽象类，Dagger将会为该接口或者抽象类生成一个实现，这个实现的命名是在接口或抽象类前面加上Dagger，如MyComponent生成的实现是DaggerMyComponent。
使用范例
创建Module,并在其中创建Provides：
/**
 * 所有的@Provides方法都必须放到一个Moudle中，
 * 一个Moudle就是使用@Moudle修饰的类,
 * 可以简单的理解为一个持有各种对象的仓库，用来给@Component组件提供实例化的对象。
 */
@Module
public class MainActivityModule {

    /**
     *  Provides方法的返回类型就是所提供的依赖类型
     */
    @Provides
    public User providesUser() {
        return new User("张三", 18);
    }
}

创建Component：
/**
 * Component : @Inject和@Module的桥梁，它的主要作用就是连接这两个部分
 * 我们必须用@Component注解一个接口，为该注解传入Module类，或者添加其依赖的组件。
 */
@Component(modules = MainActivityModule.class)
public interface MainActivityComponent {

    /** 用于设置要被注入的类 */
    void inject(MainActivity activity);
}

Rebuild工程,生成对应的以Dagger开始的工具类，类似上面的代码，生成的工具类就应该是DaggerMainActivityComponent。

在要被注入的类中声明@inject，并借助DaggerMainActivityComponent连接@inject和@Module，完成注入：
public class MainActivity extends AppCompatActivity {

    /**  通过@inject来声明要被注入的成员变量*/
    @Inject
    User user;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        /**  通过Module构建Component对象*/
        MainActivityComponent component = DaggerMainActivityComponent.builder()
                .mainActivityModule(new MainActivityModule()).build();

        /**  为Component对象设置要被注入的类，从而实现连接@inject和@Module*/
        component.inject(this);

        // 打印数据，看是否注入成功
        Log.e("MainActivity", user.toString());
    }
}

运行结果：
 
网络框架
Volley
Volley使用文档：
 
OkHttp
Github下载地址：
https://github.com/square/okhttp

android系统提供了两种HTTP通信类：HttpURLConnection和HttpClient。

Android6.0以后，Google删除了HttpClient，但是相比HttpClient来讲，HttpURLConnection要难用的多。

假如想再Android 6.0还使用HttpClient，则需要：
1. 在相应的module下的build.gradle中加入：
android {
    useLibrary 'org.apache.http.legacy'
}
注意放置的位置：是在android { }中

2. 或者直接使用Jar包：org.apache.http.legacy.jar

OkHttp是一个相对成熟的解决方案，据说Android4.4的源码中可以看到HttpURLConnection已经替换成OkHttp实现了。所以我们更有理由相信OkHttp的强大。

OkHttp优势：
	支持SPDY，共享同一个Socket来处理同一个服务器的所有请求
	如果SPDY不可用，则通过连接池来减少请求延迟
	无缝的支持GZIP来减少数据流量
	缓存响应数据来减少重复的网络请求

SPDY：Google开发的基于TCP的应用层协议，用以最小化网络延迟，提升网络速度，优化用户的网络使用体验。
添加OKHttp支持
在build.gradle中添加okhttp的依赖：
compile 'com.squareup.okhttp3:okhttp:3.3.1'
OkHttp发起请求的步骤
okHttp请求步骤： 
   1. 获取okHttpClient实例
2. 构建Request(Request是OkHttp中访问的请求, Builder是辅助类，可选：是否传参)
   3. 获取网络请求（Call）
   4. 执行网络请求（同步excute or 异步enqueue）
   5. 获取Response(Response即OkHttp中的响应)
OkHttp发起get请求
同步方式
同步方式发起请求，是不会开启线程的，因此就需要我们去开启一个子线程去执行网络请求，否则会报异常。
/**
 *  同步get请求
 *  注意：同步Get请求不会开启线程，因此需将代码运行到子线程
 */
@OnClick(R.id.btn_get_sync)
public void getSync() {
    new Thread(){
        @Override
        public void run() {
            // 1. 获取okHttpClient实例
            mOkHttpClient = new OkHttpClient();
            // 2. 构建Request
            mGetRequest = new Request.Builder().url(Constants.GET_URL).build();
            // 3. 获取网络请求Call对象
            Call call = mOkHttpClient.newCall(mGetRequest);
            try {
                // 4. 执行网络请求（同步方式），获取Response
                Response response = call.execute();
                // 5.处理响应的结果
                if (response.isSuccessful()) {
                    String reponseStr = response.body().string();
                    Log.e(TAG, "同步GET请求成功 ===========" + reponseStr);
                } else {
                    Log.e(TAG, "同步GET请求失败 ===========, code = " + response.code());
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }.start();
}

异步方式
异步方式去执行GET请求，OkHttp本身会开启一个子线程去执行网络操作，因此不需要我们再去开启线程。
/**
 *  异步GET方式
 *  注意：无需自己开启子线程，异步方式会自己开一个线程去执行
 */
@OnClick(R.id.btn_get_asyc)
public void getAsyc() {
    // 1. 获取okHttpClient实例
    mOkHttpClient = new OkHttpClient();
    // 2. 构建Request
    mGetRequest = new Request.Builder().url(Constants.GET_URL).build();
    // 3. 获取网络请求Call对象
    Call call = mOkHttpClient.newCall(mGetRequest);
    // 4. 执行网络请求（异步方式）
   call.enqueue(new Callback() {
       @Override
       public void onFailure(Request request, IOException e) {
           Log.e(TAG, "异步GET请求失败 ===========, code = " + e.getMessage());
       }

       @Override
       public void onResponse(Response response) throws IOException {
           // 5.处理响应的结果
           if (response.isSuccessful()) {
               String reponseStr = response.body().string();
               Log.e(TAG, "异步GET请求成功 ===========" + reponseStr);
           } else {
               Log.e(TAG, "异步GET请求失败 ===========, code = " + response.code());
           }
       }
   });
}
OkHttp发起post请求
同步方式
Post请求与Get请求的区别就在于是否在构建Request的时候传递了参数。

这里我们用直接上传数据到服务器的方式来演示Post请求：
/**
 *  同步post请求(注意要开子线程)
 *  采用直接传递数据的方式请求服务器
 */
@OnClick(R.id.btn_post_sync)
public void postSync() {
    new Thread(){
        @Override
        public void run() {
            // 要传递给服务器的数据
            String content = "{'msg':'Hello, OkHttp'}";

            // 2. 构建要传输给服务器的参数
            RequestBody body = RequestBody.create(MediaType.parse("application/json; charset=utf-8"), content);
            // 3. 构建Request
            Request request = new Request.Builder().url(Constants.POST_URL_JSON).post(body).build();
            // 4. 获取Call对象
            Call call = mOkHttpClient.newCall(request);
            try {
                // 5. 同步post请求服务器
                Response response = call.execute();
                // 6. 处理响应的结果
                if (response.isSuccessful()) {
                    String reponseStr = response.body().string();
                    Log.e(TAG, "同步Post请求成功 ===========" + reponseStr);
                } else {
                    Log.e(TAG, "同步Post请求失败 ===========, code = " + response.code());
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }.start();
}

异步方式
这里我们用传统的上传key – value形式的数据的方式来演示异步post请求：
/**
 *  异步post请求
 *  采用key - value的方式传递参数给服务器
 */
@OnClick(R.id.btn_post_asyc)
public void postAsyc() {
    // 2. 构建要传输给服务器的参数
    RequestBody body = new FormEncodingBuilder()
            .add("platform", "android")
            .add("version", "23")
            .add("SDK", "24")
            .build();
    // 3. 构建Request
    Request request = new Request.Builder().url(Constants.POST_URL_PARAMS).post(body).build();
    // 4. 获取Call对象
    Call call = mOkHttpClient.newCall(request);
    // 5. 同步post请求服务器
    call.enqueue(new Callback() {
        @Override
        public void onFailure(Request request, IOException e) {
            Log.e(TAG, "异步Post请求失败 =========== " + e.getMessage());
        }

        @Override
        public void onResponse(Response response) throws IOException {
            // 6. 处理响应的结果
            if (response.isSuccessful()) {
                String reponseStr = response.body().string();
                Log.e(TAG, "异步Post请求成功 ===========" + reponseStr);
            } else {
                Log.e(TAG, "异步Post请求失败 ===========, code = " + response.code());
            }
        }
    });
}

OkHttpUtil开源库
GitHub地址：
	https://github.com/hongyangAndroid/okhttp-utils

添加依赖方式：
compile 'com.zhy:okhttputils:2.3.8'
Retrofit
主页
https://github.com/square/retrofit
http://square.github.io/retrofit/
注意
使用Retrofit的前提是服务器端代码遵循REST规范。

REST规范介绍：
	http://www.coderli.com/translate-restful-standard-resolved/
REST规范其实就是定义接口的访问地址的规范，接口的前缀要求是符合业务的,做到见名知意。
优点
	效率非常高
	可以直接将结果转换称Java类
	主要是配合RxJava一起使用
 
配置Retrofit
添加Retrofit依赖:
compile 'com.squareup.retrofit2:retrofit:2.0.2'

添加数据解析依赖(根据实际情况进行选择):
	Gson : com.squareup.retrofit2:converter-gson:2.0.2
	Jackson : com.squareup.retrofit2:converter-jackson:2.0.2
	Moshi : com.squareup.retrofit2:converter-moshi:2.0.2
	Protobuf : com.squareup.retrofit2:converter-protobuf:2.0.2
	Wire : com.squareup.retrofit2:converter-wire:2.0.2
	Simple XML : com.squareup.retrofit2:converter-simplexml:2.0.2
使用步骤
以访问下方的URL为例：
	http://apis.juhe.cn/mobile/get?phone=13812345678&key=daf8fa858c330b22e342c882bcbac622

	利用http://www.jsonschema2pojo.org/创建数据模型
 
	创建REST API 接口
	请求方法:@GET / @POST / @PUT / @DELETE / @HEAD
	URL处理
	@Path - 替换参数，作用相当于字符串占位符替换
@GET("/group/{id}/users")
public Call<List<User>> groupList(@Path("id") int groupId);
	@Query - 添加查询参数，作用相当于GET请求URL地址后追加一个参数
@GET("/group/{id}/users")
public Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);
	@QueryMap - 如果有多个查询参数，把它们放在Map中，作用相当于GET请求URL地址后追加多个参数
@GET("/group/{id}/users")
public Call<List<User>> groupList(@Path("id") int groupId, @QueryMap Map<String, String> options);
代码范例：
/**
 *  创建REST API 接口
 */
public interface LocationAPI {
    /**
     *  注意：指定url的时候一定是以"/"开始
     */
    @GET("/mobile/get")
    public Call<LocationModel> getLocation(@Query("phone") String num, @Query("key") String key);
}

	创建Retrofit对象, 并发起请求
// 构建 Retrofit实例
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(BASE_URL)
        .addConverterFactory(GsonConverterFactory.create())
        .build();
// 构建接口的实例
LocationAPI locationAPI = retrofit.create(LocationAPI.class);
// 调用接口方法，传递参数
Call<LocationModel> call = locationAPI.getLocation("18607880755", "daf8fa858c330b22e342c882bcbac622");
// call.execute();// 同步执行网络请求
// 异步执行网络请求,而且可以直接操作UI线程
call.enqueue(new Callback<LocationModel>() {
    @Override
    public void onResponse(Call<LocationModel> call, Response<LocationModel> response) {
        LocationModel body = response.body();
        tvOk.setText(body.getResult().getCity());
    }

    @Override
    public void onFailure(Call<LocationModel> call, Throwable t) {
        tvError.setText(t.toString());
    }
});
图片加载框架
Universal-Image-Loader
主页
 	https://github.com/nostra13/Android-Universal-Image-Loader
添加相关支持
添加依赖: 
compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.5'

添加权限:
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITEEXTERNALSTORAGE" />
使用步骤
1. 在Application或Activity中进行初始化配置（一般都在Application中来做）
// ImageLoaderConfiguration 详细配置
File cacheDir = StorageUtils.getOwnCacheDirectory(getApplicationContext(), "imageloader/Cache"); // 自定义缓存文件夹
ImageLoaderConfiguration config = new ImageLoaderConfiguration.Builder(this)
        .memoryCacheExtraOptions(480, 800) // 指定缓存到内存时图片的大小,默认是屏幕尺寸的长宽
        .diskCacheExtraOptions(480, 800, null) // 指定缓存到硬盘时图片的大小,并不建议使用
        .taskExecutor(new ThreadPoolExecutor()) // 自定义一个线程来加载和显示图片
        .taskExecutorForCachedImages(new ThreadPoolExecutor())// 自定义一个线程来缓存图片
        .threadPoolSize(3) // default, 指定线程池大小
        .threadPriority(Thread.NORM_PRIORITY - 2) // default ,指定线程优先级
        .tasksProcessingOrder(QueueProcessingType.FIFO) // default , 指定加载显示图片的任务队列的类型
        .denyCacheImageMultipleSizesInMemory() // 禁止在内存中缓存同一张图片的多个尺寸类型
        .memoryCache(new LruMemoryCache(2 * 1024 * 1024)) // 指定内存缓存的大小,默认值为1/8 应用的最大可用内存
        .memoryCacheSize(2 * 1024 * 1024)
        .memoryCacheSizePercentage(13) // default
        .diskCache(new UnlimitedDiskCache(cacheDir)) // default , 指定硬盘缓存的地址
        .diskCacheSize(50 * 1024 * 1024) // 指定硬盘缓存的大小
        .diskCacheFileCount(100) // 指定硬盘缓存的文件个数
        .diskCacheFileNameGenerator(new HashCodeFileNameGenerator()) // default , 指定硬盘缓存时文件名的生成器
        .imageDownloader(new BaseImageDownloader(this)) // default , 指定图片下载器
        .imageDecoder(new BaseImageDecoder()) // default , 指定图片解码器
        .defaultDisplayImageOptions(DisplayImageOptions.createSimple()) // default , 指定图片显示的配置
        .writeDebugLogs() // 是否显示Log
        .build();

// ImageLoaderConfiguration 简单初始化(直接使用默认的配置)
ImageLoaderConfiguration configuration = ImageLoaderConfiguration.createDefault(this);
// 初始化配置
ImageLoader.getInstance().init(configuration);
2. 初始化配置DisplayImageOptions（可以不配置）
DisplayImageOptions options = new DisplayImageOptions.Builder()
        .showImageOnLoading(R.mipmap.ic_launcher) // 图片正在加载时显示的图片资源ID
        .showImageForEmptyUri(R.mipmap.ic_launcher) // URI为空时显示的图片资源ID
        .showImageOnFail(R.mipmap.ic_launcher) // 图片加载失败时显示的图片资源ID
        .resetViewBeforeLoading(false)  // default 图片在下载前是否重置,复位
        .delayBeforeLoading(1000) // 图片开始加载前的延时.默认是0
        .cacheInMemory(false) // default , 是否缓存在内存中, 默认不缓存
        .cacheOnDisk(false) // default , 是否缓存在硬盘 , 默认不缓存
        .preProcessor(new BitmapProcessor) // 设置图片缓存在内存前的图片处理器
        .postProcessor(new BitmapProcessor) // 设置图片在缓存到内存以后 , 显示在界面之前的图片处理器
        .extraForDownloader(null) // 为图片下载设置辅助参数
        .considerExifParams(false) // default , 设置是否考虑JPEG图片的EXIF参数信息,默认不考虑
        .imageScaleType(ImageScaleType.IN_SAMPLE_POWER_OF_2) // default , 指定图片缩放的方式,ListView/GridView/Gallery推荐使用此默认值
        .bitmapConfig(Bitmap.Config.ARGB_8888) // default , 指定图片的质量,默认是 ARGB_8888
        .decodingOptions(new BitmapFactory.Options()) // 指定图片的解码方式
        .displayer(new SimpleBitmapDisplayer()) // default , 设置图片显示的方式,用于自定义
        .handler(new Handler()) // default ,设置图片显示的方式和ImageLoadingListener的监听, 用于自定义
        .build();
3. 调用API显示图片
   常见API：
	ImageLoader.getInstance().loadImage(String uri, ImageLoadingListener listener)
	ImageLoader.getInstance().displayImage(String uri, ImageView imageView)
	ImageLoader.getInstance().displayImage(String uri, ImageView imageView, DisplayImageOptions options)
	ImageLoader.getInstance().displayImage(String uri, ImageView imageView, DisplayImageOptions options,ImageLoadingListener listener, ImageLoadingProgressListener progressListener)

示例：
ImageLoader.getInstance().displayImage(URL_IMG, mIv);
特殊用法
	显示圆形图片。注意：使用该效果,必须显式指定图片的宽高：
// 显示圆形的图片
DisplayImageOptions options = new DisplayImageOptions.Builder()
        .displayer(new CircleBitmapDisplayer())
        .build();
ImageLoader.getInstance().displayImage(URL_IMG, mIv, options);
	显示圆角图片。注意：使用该效果,必须显式指定图片的宽高：
// 显示圆角图片.使用该效果,必须显式指定图片的宽高
 DisplayImageOptions options = new DisplayImageOptions.Builder()
         .displayer(new RoundedBitmapDisplayer(90))
         .build();
 ImageLoader.getInstance().displayImage(URL_IMG, mIv, options);
	显示圆角缩放图片。注意：使用该效果,必须显式指定图片的宽高：
DisplayImageOptions options = new DisplayImageOptions.Builder()
        .displayer(new RoundedVignetteBitmapDisplayer(30,90))
        .build();
ImageLoader.getInstance().displayImage(URL_IMG, mIv, options);
	显示渐显图片
DisplayImageOptions options = new DisplayImageOptions.Builder()
        .displayer(new FadeInBitmapDisplayer(3000))
        .build();
ImageLoader.getInstance().displayImage(URL_IMG, mIv, options);
Fresco
主页
主页: https://github.com/facebook/fresco
中文文档: http://fresco-cn.org/docs/index.html
添加相关支持
添加依赖：
compile 'com.facebook.fresco:fresco:0.9.0+'

添加权限：
<uses-permission android:name="android.permission.INTERNET"/>
使用步骤
1. 在Application初始化或在Activity 的setContentView()方法之前，进行初始化
Fresco.initialize(this);

2. 在布局文件中添加Fresco的图片控件SimpleDraweeView。注意：宽高必须显示指定,否则图片无法显示。
<com.facebook.drawee.view.SimpleDraweeView
    android:id="@+id/sdv"
    android:layout_width="200dp"
    android:layout_height="200dp"
    fresco:placeholderImage="@mipmap/ic_launcher" />

由于需要制定宽高，如果希望图片以特定的宽高比例显示，例如 4:3，可以在XML中指定:
<com.facebook.drawee.view.SimpleDraweeView
    android:id="@+id/sdv"
    android:layout_width="200dp"
    android:layout_height="wrap_content"
    android:layout_centerInParent="true"
    fresco:placeholderImage="@mipmap/ic_launcher"
    fresco:viewAspectRatio="1.33" />

也可以在代码中指定显示比例：
mSimpleDraweeView.setAspectRatio(1.33f);

3. 在Java代码中指定图片的路径，显示图片。注意：SimpleDraweeView接收的路径参数为URI,所以需要一次转换。
Uri uri = Uri.parse(URL_IMG);
SimpleDraweeView view = (SimpleDraweeView) findViewById(R.id.sdv);
view.setImageURI(uri);
在布局文件中设置更多参数
<com.facebook.drawee.view.SimpleDraweeView
    android:id="@+id/my_image_view"
    android:layout_width="20dp"
    android:layout_height="20dp"
    fresco:actualImageScaleType="focusCrop"// 图片的缩放方式.
    fresco:backgroundImage="@color/blue" //背景图.不支持缩放.XML仅能指定一张背景图.如果使用Java代码指定的话,可以指定多个背景,显示方式类似FrameLayout,多个背景图按照顺序一级一级层叠上去.
    fresco:fadeDuration="300" // 渐显图片的时间
    fresco:failureImage="@drawable/error" // 图片加载失败显示的图片
    fresco:failureImageScaleType="centerInside" //// 图片加载失败显示的图片的缩放类型
    fresco:overlayImage="@drawable/watermark" // 层叠图,最后叠加在图片之上.不支持缩放.XML仅能指定一张.如果使用Java代码指定的话,可以指定多个,显示方式类似FrameLayout,多个图按照顺序一级一级层叠上去.
    fresco:placeholderImage="@color/wait_color"  // 图片加载成功之前显示的占位图
    fresco:placeholderImageScaleType="fitCenter" // 图片加载成功之前显示的占位图的缩放类型
    fresco:pressedStateOverlayImage="@color/red" // 设置按压状态下的层叠图.不支持缩放.
    fresco:progressBarAutoRotateInterval="1000" // 进度条图片旋转显示时长
    fresco:progressBarImage="@drawable/progress_bar" // 进度条图片
    fresco:progressBarImageScaleType="centerInside" //进度条图片的缩放类型
    fresco:retryImage="@drawable/retrying" // 当图片加载失败的时候,显示该图片提示用户点击重新加载图片
    fresco:retryImageScaleType="centerCrop" // 提示图片的缩放类型
    fresco:roundAsCircle="false" // 显示圆形图片
    fresco:roundBottomLeft="false" // roundedCornerRadius属性设置后,四个角都会有圆角,如果左下角不需要设置为false.
    fresco:roundBottomRight="true" // roundedCornerRadius属性设置后,四个角都会有圆角,如果右下角不需要设置为false.
    fresco:roundTopLeft="true" // roundedCornerRadius属性设置后,四个角都会有圆角,如果左上角不需要设置为false.
    fresco:roundTopRight="false" // roundedCornerRadius属性设置后,四个角都会有圆角,如果右上角不需要设置为false.
    fresco:roundWithOverlayColor="@color/corner_color" // 设置图片圆角后空出区域的颜色.如示例图中的红色部分
    fresco:roundedCornerRadius="1dp" // 设置图片圆角角度,设置该属性后四个角都会生效
    fresco:roundingBorderColor="@color/border_color" // 设置圆角后,边框的颜色.
    fresco:roundingBorderWidth="2dp" /> // 设置圆角后,外边框的宽高

在Java代码中配置参数
在Java代码中配置参数需要使用GenericDraweeHierarchy，但是注意：
	对于同一个View，不要多次调用setHierarchy，即使这个View是可回收的。创建 DraweeHierarchy 的较为耗时的一个过程。

范例：可以借助DraweeHierarchy设置占位图、加载进度条等（加载进度条默认样式是下方显示一个蓝条来显示图片的加载速度）
GenericDraweeHierarchy hierarchy = GenericDraweeHierarchyBuilder
        .newInstance(getResources())
        .setPlaceholderImage(getResources().getDrawable(R.mipmap.ic_launcher)) // 设置占位图
        .setProgressBarImage(new ProgressBarDrawable())  // 设置加载进度条
        .build();

mSimpleDraweeView.setHierarchy(hierarchy);
mSimpleDraweeView.setImageURI(uri);

特殊用法
显示渐进式JPEG图片
Fresco 支持渐进式的网络JPEG图。在开始加载之后，图会从模糊到清晰渐渐呈现。
可以设置一个清晰度标准，在未达到这个清晰度之前，会一直显示占位图。

配置参数信息：
// 渐进式JPEG图配置
ProgressiveJpegConfig pjpegConfig = new ProgressiveJpegConfig() {
    @Override
    // 返回下一个需要解码的扫描次数
    public int getNextScanNumberToDecode(int scanNumber) {
        return scanNumber + 2;
    }

    // 确定多少个扫描次数之后的图片才能开始显示
    public QualityInfo getQualityInfo(int scanNumber) {
        boolean isGoodEnough = (scanNumber >= 5);
        return ImmutableQualityInfo.of(scanNumber, isGoodEnough, false);
    }
};
// ImagePipelineConfig配置如何加载渐进JPEG图像
ImagePipelineConfig config = ImagePipelineConfig.newBuilder(this)
        .setProgressiveJpegConfig(pjpegConfig)
        .build();

// 初始化Fresco（假如不需配置特殊参数，直接调用Fresco.initialize(this);即可）
Fresco.initialize(this, config);

显式地指定允许渐进式JPEG图片加载，加载图片：
//  显式地指定允许渐进式JPEG图片加载
ImageRequest request = ImageRequestBuilder
        .newBuilderWithSource(uri)
        .setProgressiveRenderingEnabled(true)
        .build();
// 构建显示图片所用到的DraweeController
DraweeController controller = Fresco.newDraweeControllerBuilder()
        .setImageRequest(request)
        .setOldController(mSimpleDraweeView.getController())
        .build();

mSimpleDraweeView.setController(controller);
显示GIF图片
Fresco 支持 GIF 和 WebP 格式的动画图片。如果你希望图片下载完之后自动播放，同时，当View从屏幕移除时，停止播放，只需要在 image request 中简单设置：
DraweeController controller = Fresco.newDraweeControllerBuilder()
        .setUri(URL_GIF)
        .setAutoPlayAnimations(true)
        .build();
mSimpleDraweeView.setController(controller);
Picasso
主页
Picasso是Square公司出品的一个强大的图片下载和缓存图片库。
官方下载网址：
http://square.github.io/picasso/
优点
Picasso不仅实现了图片异步加载功能，还解决了android中加载图片时需要解决的一些常见问题：
	使用ListView，GridView的时候，自动检测Adapter的重用，取消下载，使用缓存
	使用复杂的图片压缩转换来尽可能的减少内存消耗
	自带内存和硬盘二级缓存功能
添加相关的支持
添加依赖：
compile 'com.squareup.picasso:picasso:2.5.2'

添加权限：
<uses-permission android:name="android.permission.INTERNET"/>
使用步骤
Picasso的使用方式非常简单，直接在代码中指定要下载的图片的URL和要显示图片的ImageView即可。

使用范例：（不需要其他的加载配置的话，只需标红的几行代码即可展示图片）
Picasso
        .with(this)// 指定Context
        .load(URL_IMG) //指定图片URL
        .placeholder(R.mipmap.ic_launcher) //指定图片未加载成功前显示的图片
        .error(R.mipmap.ic_launcher)// 指定图片加载失败显示的图片
        .resize(300, 300)// 指定图片的尺寸
        .fit()// 指定图片缩放类型为fit
        .centerCrop()// 指定图片缩放类型为centerCrop
        .centerInside()// 指定图片缩放类型为centerInside
        .memoryPolicy(MemoryPolicy.NO_CACHE, MemoryPolicy.NO_STORE)// 指定内存缓存策略
        .priority(Picasso.Priority.HIGH)// 指定优先级
        .into(mIv); // 指定显示图片的ImageView
特殊用法
支持加载资源文件的图片
除了加载网络图片picasso还支持加载Resources, assets, files, content providers中的资源文件：
// 加载资源文件的图片
Picasso.with(this)
        .load(R.mipmap.ic_launcher)
        .into(mIv);
支持加载sdcard中的图片文件
// 加载SD卡中的图片资源
Picasso.with(this)
        .load(new File(...))
        .into(mIv);
图形变换
Picasso支持图形变换，可以通过实现Transformation接口来写自己的变换类。

自定义Picasso圆形图片变换类：
/**
 *  Picasso圆形图片变换类
 */
class CircleTransformation implements com.squareup.picasso.Transformation {
    @Override
    public Bitmap transform(Bitmap source) {
        int size = Math.min(source.getWidth(), source.getHeight());
        int x = (source.getWidth() - size) / 2;
        int y = (source.getHeight() - size) / 2;
        Bitmap squaredBitmap = Bitmap.createBitmap(source, x, y, size, size);
        if (squaredBitmap != source) {
            source.recycle();
        }
        Bitmap bitmap = Bitmap.createBitmap(size, size, source.getConfig());
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        BitmapShader shader = new BitmapShader(squaredBitmap,
                BitmapShader.TileMode.CLAMP, BitmapShader.TileMode.CLAMP);
        paint.setShader(shader);
        paint.setAntiAlias(true);
        float r = size / 2f;
        canvas.drawCircle(r, r, r, paint);
        squaredBitmap.recycle();
        return bitmap;
    }

    @Override
    public String key() {
        return "circle";
    }
}

自定义圆角图片变换类：
/**
 * Picasso圆角图片变换类
 */
class RoundedTransformation implements com.squareup.picasso.Transformation {

    /**
     * 圆角角度
     */
    private final int radius;

    /**
     * 边距
     */
    private final int margin;

    // radius is corner radii in dp
    // margin is the board in dp
    public RoundedTransformation(final int radius, final int margin) {
        this.radius = radius;
        this.margin = margin;
    }

    @Override
    public Bitmap transform(final Bitmap source) {
        final Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setShader(new BitmapShader(source, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP));

        Bitmap output = Bitmap.createBitmap(source.getWidth(), source.getHeight(), Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(output);
        canvas.drawRoundRect(new RectF(margin, margin, source.getWidth() - margin, source.getHeight() - margin), radius, radius, paint);

        if (source != output) {
            source.recycle();
        }
        return output;
    }

    @Override
    public String key() {
        return "rounded(radius=" + radius + ", margin=" + margin + ")";
    }
}

如何使用：
Picasso
        .with(this)// 指定Context
        .load(URL_IMG) //指定图片URL
        .transform(new RoundedTransformation(90,2)) // 指定图片转换器
        .into(mIv); // 指定显示图片的ImageView
Glide
主页
Glide是Google公司仿照Picasso做出来的一个图片加载框架，因此API用起来与Picasso非常相像，但是Glide可以直接加载GIF图片。

主页
 https://github.com/bumptech/glide
中文文档
	http://mrfu.me/2016/02/27/Glide_Getting_Started/
添加相关支持
添加依赖：
compile 'com.github.bumptech.glide:glide:3.7.0'

添加权限：
<uses-permission android:name="android.permission.INTERNET"/>
使用步骤
Glide的使用方法和Picasso差不多，使用方式都非常简单，直接在代码中指定要下载的图片的URL和要显示图片的ImageView即可。

使用范例：（不需要其他的加载配置的话，只需标红的几行代码即可展示图片）
Glide
        .with(this) // 指定Context
        .load(URL_GIF)// 指定图片的URL
        .placeholder(R.mipmap.ic_launcher)// 指定图片未成功加载前显示的图片
        .error(R.mipmap.ic_launcher)// 指定图片加载失败显示的图片
        .override(300, 300)//指定图片的尺寸
        .fitCenter()//指定图片缩放类型为fitCenter
        .centerCrop()// 指定图片缩放类型为centerCrop
        .skipMemoryCache(true)// 跳过内存缓存
        .diskCacheStrategy(DiskCacheStrategy.NONE)//跳过磁盘缓存
        .diskCacheStrategy(DiskCacheStrategy.SOURCE)//仅仅只缓存原来的全分辨率的图像
        .diskCacheStrategy(DiskCacheStrategy.RESULT)//仅仅缓存最终的图像
        .diskCacheStrategy(DiskCacheStrategy.ALL)//缓存所有版本的图像
        .priority(Priority.HIGH)//指定优先级.Glide 将会用他们作为一个准则，并尽可能的处理这些请求，但是它不能保证所有的图片都会按照所要求的顺序加载。优先级排序:IMMEDIATE > HIGH > NORMAL >　LOW
        .into(mIv);//指定显示图片的ImageView
特殊用法
图形变换
Glide支持图形变换，可以通过实现BitmapTransformation 接口来写自己的变换类。

自定义Glide圆形图片变换类：
/**
 * Glide圆形图片变换类
 */
class GlideCircleTransform extends BitmapTransformation {
    public GlideCircleTransform(Context context) {
        super(context);
    }

    @Override
    protected Bitmap transform(BitmapPool pool, Bitmap toTransform, int outWidth, int outHeight) {
        return circleCrop(pool, toTransform);
    }

    private Bitmap circleCrop(BitmapPool pool, Bitmap source) {
        if (source == null) return null;

        int size = Math.min(source.getWidth(), source.getHeight());
        int x = (source.getWidth() - size) / 2;
        int y = (source.getHeight() - size) / 2;

        Bitmap squared = Bitmap.createBitmap(source, x, y, size, size);

        Bitmap result = pool.get(size, size, Bitmap.Config.ARGB_8888);
        if (result == null) {
            result = Bitmap.createBitmap(size, size, Bitmap.Config.ARGB_8888);
        }

        Canvas canvas = new Canvas(result);
        Paint paint = new Paint();
        paint.setShader(new BitmapShader(squared, BitmapShader.TileMode.CLAMP, BitmapShader.TileMode.CLAMP));
        paint.setAntiAlias(true);
        float r = size / 2f;
        canvas.drawCircle(r, r, r, paint);
        return result;
    }

    @Override
    public String getId() {
        return getClass().getName();
    }
}

自定义圆角图片变换类：
/**
 *  Glide圆角图片变换类
 */
class GlideRoundTransform extends BitmapTransformation {

    private  float radius = 0f;

    public GlideRoundTransform(Context context) {
        this(context, 4);
    }

    public GlideRoundTransform(Context context, int dp) {
        super(context);
        this.radius = Resources.getSystem().getDisplayMetrics().density * dp;
    }

    @Override protected Bitmap transform(BitmapPool pool, Bitmap toTransform, int outWidth, int outHeight) {
        return roundCrop(pool, toTransform);
    }

    private  Bitmap roundCrop(BitmapPool pool, Bitmap source) {
        if (source == null) return null;

        Bitmap result = pool.get(source.getWidth(), source.getHeight(), Bitmap.Config.ARGB_8888);
        if (result == null) {
            result = Bitmap.createBitmap(source.getWidth(), source.getHeight(), Bitmap.Config.ARGB_8888);
        }

        Canvas canvas = new Canvas(result);
        Paint paint = new Paint();
        paint.setShader(new BitmapShader(source, BitmapShader.TileMode.CLAMP, BitmapShader.TileMode.CLAMP));
        paint.setAntiAlias(true);
        RectF rectF = new RectF(0f, 0f, source.getWidth(), source.getHeight());
        canvas.drawRoundRect(rectF, radius, radius, paint);
        return result;
    }

    @Override public String getId() {
        return getClass().getName() + Math.round(radius);
    }
}

如何使用：
// 显示圆形图片
Glide.with(this)
          .load(URL_IMG)
          .transform(new GlideCircleTransform(this))
          .into(mIv);
更改Glide默认配置
创建一个GlideModule的实现类,并在其中更改自己需要的设置：
/**
 * Glide默认配置修改类
 */
public class SimpleGlideModule implements GlideModule {
    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        // 更改Bitmap图片压缩质量为8888,默认为565
        builder.setDecodeFormat(DecodeFormat.PREFER_ARGB_8888);
    }

    @Override
    public void registerComponents(Context context, Glide glide) {

    }
}

在清单文件中添加一个meta-data节点，name值为刚刚创建的GlideModule实现类的完整包名+类名，value值为GlideModule：
<meta-data
    android:name="com.example.glidedemo.SimpleGlideModule"
    android:value="GlideModule" />

配置完成后，Glide加载图片的时候将会按照新的设置进行加载。
图像库对比
	快速加载图片推荐Glide（默认图形加载质量为RGB_565）
	对图片质量要求较高推荐Picasso（默认图形加载质量为ARGB_8888）
	如果应用加载的图片很多,推荐Fresco > Glide > Picasso（Fresco采用了内存双缓存+Native缓存构成的三级缓存）

 
数据库
数据库_ormlite
主页
 http://ormlite.com/
添加相关支持
compile 'com.j256.ormlite:ormlite-android:4.48'
compile 'com.j256.ormlite:ormlite-core:4.48'
使用步骤
1. 声明数据库表：定义一个JavaBean，通过ORMLite提供的注解来声明数据库表名和列名。
我们假设现在有个User表，表中有四个字段：主键（自增长）、姓名、年龄、电话号码
/**
 * JavaBean —— 对应数据库表 ，使用DatabaseTable声明表名为user
 */
@DatabaseTable(tableName = "user")
public class User {

    /**  主键ID，设置为自增*/
    @DatabaseField(generatedId = true)
    long id;

    /**  姓名，数据库表列名：name*/
    @ DatabaseField(columnName = "name")
    String name;

    /**  年龄，数据库表列名：age*/
    @ DatabaseField(columnName = "age")
    String age;

    /**  电话号码，数据库表列名：tel*/
    @ DatabaseField(columnName = "tel")
    String tel;

    public User() {
    }

    public User(String name, String age, String tel) {
        this.name = name;
        this.age = age;
        this.tel = tel;
    }
}

2. 定义一个OpenHelper,继承自OrmLiteSqliteOpenHelper，用于操作数据库：
/**
 * 操作数据库表的辅助类，因为需要使用ORMLite框架，因此需要继承自 OrmLiteSqliteOpenHelper
 */
public class UserOpenHelper extends OrmLiteSqliteOpenHelper {

    public UserOpenHelper(Context context) {
        super(context, "student.db", null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase database, ConnectionSource connectionSource) {
        try {
            // 创建数据库表（直接使用ORMLite的工具类创建）
            // 参数2是对应数据库表的JavaBean的class
            TableUtils.createTable(connectionSource, User.class);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onUpgrade(SQLiteDatabase database, ConnectionSource connectionSource, int oldVersion, int newVersion) {
        try {
            // 更新数据库表（可以先删除表，再创建新表，但是实际开发中数据都要备份出来）
            // 删除User表
            TableUtils.dropTable(connectionSource, User.class, true);
            // 重写创建表
            onCreate(database, connectionSource);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
3. 获取数据库表对应的DAO：
// 获取操作数据库表的DAO
Dao<User, Integer> mDao = new UserOpenHelper (this).getDao(User.class);

4. 操作DAO开始进行增删改查操作
  添加数据：
/**  添加数据到User表*/
public void add(View v) {
    User user = new User("zhangsan", 18, "13112345678");
    User user1 = new User("lisi", 28, "13222345678");
    User user2 = new User("wangwu", 38, "13332345678");
    User user3 = new User("zhaoqi", 48, "13442345678");

    try {
        mDao.create(user);
        mDao.create(user1);
        mDao.create(user2);
        mDao.create(user3);
    } catch (SQLException e) {
        e.printStackTrace();
    }
}

  删除数据：
/**  从User表删除I数据*/
public void del(View v) {
    try {
        // 删除ID为2的用户的数据
        mDao.deleteById(2);
    } catch (SQLException e) {
        e.printStackTrace();
    }
}

  更新数据：
/**  更新User表*/
public void update(View v) {
    try {
        // 将name为zhangsan的用户的电话号码改为120
        mDao.updateRaw("update user set tel='120' where name=?", new String[]{"zhangsan"});
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
  查询数据：
/**  从User表查询数据*/
public void query(View v) {
    try {
        // 从User表查询name为zhangsan 的用户的信息
        List<User> users = mDao.queryForEq("name", "zhangsan");
        for (int i = 0; i < users.size(); i++) {
            User user = users.get(i);
            Log.e("MainActivity", user.tel);
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
数据库_GreenDAO
主页
https://github.com/greenrobot/greenDAO
添加相关支持
compile 'de.greenrobot:greendao:2.1.0'
compile 'de.greenrobot:greendao-generator:2.1.0'
使用步骤
生成相关操作类
创建自定义的DAOGenerater,指定数据库相关配置并生成相关类：
/**
 * GreenDAO 操作数据库相关类的生成程序
 */
public class CustomDAOGenerater {
    public static void main(String[] args) throws Exception {
        /**
         *  参数1：数据库版本
         *  参数2：数据库相关类文件所在的包名
         */
        Schema schema = new Schema(1, "com.example.greendaodemo.db");
        // 创建数据库表,参数为表名
        Entity entity = schema.addEntity("User");
        // 为表添加字段
        entity.addIdProperty();// 该字段为id
        entity.addStringProperty("name");// String类型字段
        entity.addIntProperty("age");//Int类型字段
        entity.addStringProperty("tel");// String类型字段

        // 生成数据库相关类
        //第二个参数指定生成文件的本次存储路径,AndroidStudio工程指定到当前工程的java路径
        new DaoGenerator().generateAll(schema, "D:\\developtools\\android\\AndroidStudioProjects\\21_OSFrameworks\\greendaodemo\\src\\main\\java");
    }
}

右键运行main()方法，GreenDAO将在指定的本地路径下的指定包名路径下生成相关类文件：
 
初始化数据库
初始化数据库操作最好在Application中去执行，只执行一次就够了，因为Session在被创建的时候，默认会将所有的DAO都创建出来，非常消耗资源。
/**
 *  初始化数据库
 *  该初始化过程最好放在Application中进行,避免创建多个Session
 */
private void setupDatabase() {
    /**
     *  通过 DaoMaster 的内部类 DevOpenHelper创建数据库
     *  注意：默认的 DaoMaster.DevOpenHelper 会在数据库升级时，删除所有的表。
     *  所以，在正式的项目中，你还应该做一层封装，来实现数据库的安全升级（先备份数据再升级）。
     *
     * 参数1：Context
     * 参数2: 要创建的数据库名字
     * 参数3 : CursorFactroy
     */
    DaoMaster.DevOpenHelper helper = new DaoMaster.DevOpenHelper(this, "student.db", null);
    // 获取数据库
    SQLiteDatabase database = helper.getWritableDatabase();
    // 获取DaoMaster
    DaoMaster daoMaster = new DaoMaster(database);
    // 获取Session
    DaoSession daoSession = daoMaster.newSession();
    // 获取对应的表的DAO对象
    UserDao mUserDao = daoSession.getUserDao();
}
使用DAO进行增删改查操作
增加数据：
/**  插入数据到数据库*/
public void add(View v) {
    User user = new User(null, "zhangsan", 12, "13112345678");
    User user2 = new User(null, "lisi", 22, "13222345678");
    User user3 = new User(null, "wangwu", 32, "13332345678");
    User user4 = new User(null, "zhaoqi", 42, "13442345678");

    dao.insert(user);
    dao.insert(user2);
    dao.insert(user3);
    dao.insert(user4);
}

删除数据：
/**  从数据库删除数据*/
public void del(View v) {
// 参数为要删除的数据的ID
    dao.deleteByKey(2L);
}

更新数据：
/**  更新数据库的数据*/
public void update(View v) {
    User user = new User(4L, "赵琦", 42, "13442345678");
    dao.update(user);
}

查找数据：
/**  查询数据库的数据*/
public void query(View v) {
    // 构建查询条件：age = 32
    QueryBuilder<User> userQueryBuilder = dao.queryBuilder();
    QueryBuilder<User> builder = userQueryBuilder.where(UserDao.Properties.Age.eq("32"));
    Query<User> build = builder.build();
    // 查询数据
    List<User> list = build.list();
    // 遍历数据并输出
    for (int i = 0; i < list.size(); i++) {
        User user = list.get(i);
        Log.e("MainActivity", user.getName());
    }
}
GreenDAO优点
	性能最大化,内存开销最小化
	易于使用的API
	为Android进行高度优化

GreenDAO是目前效率最高的操作数据库的框架：
 
数据库_Litepal
主页: https://github.com/LitePalFramework/LitePal
中文文档地址: http://blog.csdn.net/sinyu890807/article/category/2522725
响应式函数编程-RxAndroid
什么是RxAndroid
Rx是响应式编程的意思, 本质是观察者模式, 是以观察者(Observer)和订阅者(Subscriber)为基础的异步响应方式。
RxAndroid来源于RxJava, 在RxJava的基础上扩展了一些Android的功能。

RxAndroid最核心的两个东西是Observables（被观察者，事件源）和Subscribers（观察者）。Observables发出一系列事件，Subscribers处理这些事件。这里的事件可以是任何你感兴趣的东西（触摸事件，网络通信…）。

一个Observable可以发出零个或者多个事件，直到结束或者出错。每发出一个事件，就会调用它的Subscriber的onNext方法，最后调用Subscriber.onNext()或者Subscriber.onError()结束。

Rxjava的看起来很像设计模式中的观察者模式，但是有一点明显不同，那就是如果一个Observerble没有任何的的Subscriber，那么这个Observable是不会发出任何事件的。
文档地址
主页:
 https://github.com/ReactiveX/RxJava
中文资料:
https://github.com/lzyzsd/Awesome-RxJava
https://www.zhihu.com/question/35511144
添加相关支持
compile 'io.reactivex:rxjava:1.1.3'
compile 'io.reactivex:rxandroid:1.1.0'
compile 'com.squareup.retrofit2:retrofit:2.0.1'
compile 'com.squareup.retrofit2:converter-gson:2.0.1'
compile 'com.squareup.retrofit2:adapter-rxjava:2.0.1'
基本概念
被观察者: Observable
	作用: 决定什么时候触发事件以及触发怎样的事件
	创建方法:
	Observable.just(T...) 参数为单个的
	Observable.from(T[]) / Observable.from(Iterable<? extends T>) 参数为数组或Iterable

观察者: Observer
	作用: 当事件触发的时候将有怎样的行为，实现类有Observer / Subscriber

订阅: subscribe
	作用: 把Observable和Observer关联起来
	方法:
	observable.subscribe(observer);
	observable.subscribe(subscriber);

事件:
	onNext()：普通事件
	onCompleted():事件队列完结
	onError(): 事件队列异常

注意：
onCompleted()和onError()是互斥的，调用了其中一个就不应该触发另一个。
如何使用
发布事件 - just
发布者发布单个事件，交由订阅者处理，订阅者接收到事件后，将字符串打印出来。
Observable // 被观察的事件
        .just("Hello") // 发布事件
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                // 订阅者处理事件：打印字符串
                Log.e(TAG, "=========from subscribe=====" + s);
            }
        });

Map操作符
现在有一个新的需求：我希望修改发出去的事件，比如发出的事件是一个String,现在我希望将发出去的事件修改为一个hash值。

RxAndroid提供了一个操作符map来处理这种事件转换的需求，map专门用于将一个事件转换为另一种事件。

Observable // 被观察的事件
        .just("Hello") // 发布事件
        // 转换为另一个事件：String转hash值
        .map(new Func1<String, Integer>() {
            @Override
            public Integer call(String s) {
                return s.hashCode();
            }
        })
        // 将事件交由订阅者处理
        .subscribe(new Action1<Integer>() {
            @Override
            public void call(Integer s) {
                // 订阅者处理事件：打印哈希值
                Log.e(TAG, "=========from subscribe=====" + s);
            }
        });

Map操作符并不是只能用一次的，你可以连续多次调用map操作符去做事件的转换，比如当前有这样一个需求：将原始事件字符串转换为哈希值再转回字符串。
Observable // 被观察的事件
        .just("Hello") // 发布事件
        // 转换为另一个事件：String转hash值
        .map(new Func1<String, Integer>() {
            @Override
            public Integer call(String s) {
                return s.hashCode();
            }
        })
        // 在将hash值转换回String
        .map(new Func1<Integer, String>() {
            @Override
            public String call(Integer integer) {
                return "我被转换成字符串了" + Integer.toString(integer);
            }
        })
        // 将事件交由订阅者处理
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                // 订阅者处理事件：打印哈希值
                Log.e(TAG, "=========from subscribe=====" + s);
            }
        });
发布事件 – from
Observable提供了一个from()方法，它接收一个集合作为输入，然后每次输出一个元素给subscriber。

场景：遍历一个字符串集合，将每个元素交由订阅者处理
Observable
        .from(arrs) // 将集合作为参数发布出去
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                // 订阅者处理事件
                Log.e(TAG, "=========from subscribe=====" + s);
            }
        });

filter操作符
filter操作符可以输出和输入相同的元素，并且会过滤掉那些不满足检查条件的元素。

场景：遍历一个字符串集合，过滤以“z”开头的字符串，将过滤后的每个元素交由订阅者处理。

Observable
        .from(arrs) // 将集合作为参数发布出去
        // 使用filter过滤以 ‘z’ 开头的字符串
        .filter(new Func1<String, Boolean>() {
            @Override
            public Boolean call(String s) {
                // 返回true的字符将被交给订阅者处理
                return !s.startsWith("z");
            }
        })
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                // 订阅者处理事件
                Log.e(TAG, "=========from subscribe=====" + s);
            }
        });
flatMap操作符
RxAndroid提供了map操作符来进行一对一的转换，同时还提供了一个flatMap的操作符来进行一对多的转换。

场景：一个学生对应多门课程，打印某学生的全部课程信息
// 构建数据
Course yuwen = new Course("语文", 1);
Course shuxue = new Course("数学", 2);
Course yingyu = new Course("英文", 3);
Course lishi = new Course("历史", 4);
Course zhengzhi = new Course("政治", 5);
Course xila = new Course("希腊语", 6);
ArrayList<Course> course1 = new ArrayList<>();
course1.add(yuwen);
course1.add(shuxue);
course1.add(yingyu);
course1.add(lishi);
course1.add(zhengzhi);
course1.add(xila);
Student zhangsan = new Student("zhangsan", course1);

Observable
        .just(zhangsan)
        // 进行一对多过滤
        .flatMap(new Func1<Student, Observable<Course>>() {
            @Override
            public Observable<Course> call(Student student) {
// 相当于在一开始发布事件的时候调用了一次Observable.from()，效果是一样的
                return Observable.from(student.getCourses());
            }
        })
        // 订阅者处理单个事件
        .subscribe(new Action1<Course>() {
            @Override
            public void call(Course course) {
                Log.e(TAG, "=========from subscribe=====" + course.getName());
            }
        });
线程控制
RxAndroid提供了一个Scheduler用来控制线程，指定某一段代码在那个线程里运行。

内置的Scheduler:
	Schedulers.immediate(): 直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。
	Schedulers.newThread(): 总是启用新线程，并在新线程执行操作。
	Schedulers.io(): I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler。行为模式和 newThread() 差不多，区别在于 io() 的内部实现是是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下 io() 比 newThread() 更有效率。不要把计算工作放在 io() 中，可以避免创建不必要的线程。
	Schedulers.computation(): 计算所使用的 Scheduler。这个计算指的是 CPU 密集型计算，即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 Scheduler 使用的固定的线程池，大小为 CPU 核数。不要把 I/O 操作放在 computation() 中，否则 I/O 操作的等待时间会浪费 CPU。
	AndroidSchedulers.mainThread(): Android专用,它指定的操作将在 Android 主线程运行。

指定线程：
	Observable.subscribeOn():指定事件产生的线程
	Observable.observeOn():指定订阅者所运行在的线程。或者叫做事件消费的线程。

范例：
	在子线程中加载一张图片，在主线程中将图片更新到界面上。
Observable
        // 传入要展示的图片的ID
        .just(R.mipmap.ic_launcher)
        // 指定下一个方法将在IO线程执行
        .subscribeOn(Schedulers.io())
        // 加载图片，由于是耗时的，所以应该运行在子线程
        .map(new Func1<Integer, Drawable>() {
            @Override
            public Drawable call(Integer integer) {
                Log.e(TAG, "=====map thread======= " + Thread.currentThread().getName());
                return getResources().getDrawable(integer);
            }
        })
        // 指定订阅者将在主线程接收事件
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Action1<Drawable>() {
            @Override
            public void call(Drawable drawable) {
                Log.e(TAG, "=====subscribe thread======= " + Thread.currentThread().getName());
                mIv.setImageDrawable(drawable);
            }
        });
和Retrofit一起使用
添加依赖:
compile 'com.squareup.retrofit2:retrofit:2.0.1'
compile 'com.squareup.retrofit2:converter-gson:2.0.1'
compile 'com.squareup.retrofit2:adapter-rxjava:2.0.1'

1. 创建数据模型
	使用http://www.jsonschema2pojo.org/创建数据模型
2. 创建REST API 接口。注意此时返回的不能是Call而是Observable：
/**
 * Retrofit REST API
 */
public interface IPInterface {

    // 要访问的IP地址 http://ip.taobao.com/service/getIpInfo.php?ip=202.178.10.23

    @GET("/service/getIpInfo.php")
    public Observable<IPBean> getLocation(@Query("ip") String ip);
}
3. 创建Retrofit对象,发起请求。注意此时Retrofit需要添加addCallAdapterFactory：
// 构建Retrofit
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(baseUrl)
        .addConverterFactory(GsonConverterFactory.create())
        .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
        .build();

// 获取API实例
IPInterface ipInterface = retrofit.create(IPInterface.class);
// 获取网络返回的结果
Observable<IPBean> location = ipInterface.getLocation("8.8.8.8");
// 通过RxAndroid解析网络返回的结果，拿到IP的国家信息
location
        .subscribeOn(Schedulers.io())
        .map(new Func1<IPBean, String>() {
            @Override
            public String call(IPBean ipBean) {
                return ipBean.getData().getCountry();
            }
        })
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                Log.e(TAG, "=====retrofit RxAndroid======= " + s);
            }
        });


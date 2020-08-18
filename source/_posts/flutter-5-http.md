---
title: Flutter学习之路 - 网络编程与数据存储技术
date: 2020-06-08 09:42:14
tags: ['Flutter']
categories: Flutter
---

### 网络编程与数据存储技术

<!-- more -->

### 网络请求

```
dio: ^2.2.2	

import 'package:cookie_jar/cookie_jar.dart';
import 'package:dio/dio.dart';
import 'package:fluttertoast/fluttertoast.dart';

const BASE_URL = 'xxx';

class HttpUtil {
  static HttpUtil instance;
  Dio dio;
  BaseOptions options;

  CancelToken cancelToken = CancelToken();

  static HttpUtil getInstance() {
    if (null == instance) instance = HttpUtil();
    return instance;
  }

  /*
   * config it and create
   */
  HttpUtil() {
    //BaseOptions、Options、RequestOptions 都可以配置参数，优先级别依次递增，且可以根据优先级别覆盖参数
    options = BaseOptions(
      //请求基地址,可以包含子路径
      baseUrl: BASE_URL,
      //连接服务器超时时间，单位是毫秒.
      connectTimeout: 10000,
      //响应流上前后两次接受到数据的间隔，单位为毫秒。
      receiveTimeout: 5000,
      //Http请求头.
      // headers: {
      //   'Accept': 'application/json,*/*',
      //   'Content-Type': 'application/json',
      // },
      //请求的Content-Type，默认值是"application/json; charset=utf-8",Headers.formUrlEncodedContentType会自动编码请求体.
      // contentType: Headers.formUrlEncodedContentType,
      //表示期望以那种格式(方式)接受响应数据。接受四种类型 `json`, `stream`, `plain`, `bytes`. 默认值是 `json`,
      // contentType: Headers.formUrlEncodedContentType,
      responseType: ResponseType.plain,
    );

    dio = Dio(options);

    //Cookie管理
    dio.interceptors.add(CookieManager(CookieJar()));

    //添加拦截器
    dio.interceptors
        .add(InterceptorsWrapper(onRequest: (RequestOptions options) {
      print("请求之前");
   
      // Do something before request is sent
      return options; //continue
    }, onResponse: (Response response) {
      print("响应之前");
      
      // Do something with response data
      return response; // continue
    }, onError: (DioError e) {
      print("错误之前");
      // Do something with response error
      return e; //continue
    }));
  }

  /*
   * get请求
   */
  get(url, {data, options, cancelToken}) async {
    Response response;
    try {
      response = await dio.get(url,
          queryParameters: data, options: options, cancelToken: cancelToken);
      print('get success---------${response.statusCode}');
      print('get success---------${response.data}');

//      response.data; 响应体
//      response.headers; 响应头
//      response.request; 请求体
//      response.statusCode; 状态码

    } on DioError catch (e) {
      print('get error---------$e');
      formatError(e);
    }
    return response;
  }

  /*
   * post请求
   */
  post(url, {data, options, cancelToken}) async {
    
    Response response;
    try {
      response = await dio.post(url,
          queryParameters: data, options: options, cancelToken: cancelToken);
      print('post success---------${response.data}');
    } on DioError catch (e) {
      print('post error---------$e');
      formatError(e);
    }
    return response;
  }

  /*
   * 下载文件
   */
  downloadFile(urlPath, savePath) async {
    Response response;
    try {
      response = await dio.download(urlPath, savePath,
          onReceiveProgress: (int count, int total) {
        //进度
        print("$count $total");
      });
      print('downloadFile success---------${response.data}');
    } on DioError catch (e) {
      print('downloadFile error---------$e');
      formatError(e);
    }
    return response.data;
  }

  /*
   * error统一处理
   */
  void formatError(DioError e) {
    if (e.type == DioErrorType.CONNECT_TIMEOUT) {
      // It occurs when url is opened timeout.
      print("连接超时");
    } else if (e.type == DioErrorType.SEND_TIMEOUT) {
      // It occurs when url is sent timeout.
      print("请求超时");
    } else if (e.type == DioErrorType.RECEIVE_TIMEOUT) {
      //It occurs when receiving timeout
      print("响应超时");
    } else if (e.type == DioErrorType.RESPONSE) {
      // When the server response, but with a incorrect status, such as 404, 503...
      Fluttertoast.showToast(
        msg: '网络异常',
        toastLength: Toast.LENGTH_SHORT,
        gravity: ToastGravity.CENTER,
        timeInSecForIos: 1,         
        fontSize: 16
      );
      print("网络异常");
    } else if (e.type == DioErrorType.CANCEL) {
      // When the request is cancelled, dio will throw a error with this type.
      print("请求取消");
    } else {
      //DEFAULT Default error type, Some other Error. In this case, you can read the DioError.error if it is not null.
      print("未知错误");
    }
  }

  /*
   * 取消请求
   *
   * 同一个cancel token 可以用于多个请求，当一个cancel token取消时，所有使用该cancel token的请求都会被取消。
   * 所以参数可选
   */
  void cancelRequests(CancelToken token) {
    token.cancel("cancelled");
  }
}
```

### Future

#### 什么是Future
Future表示在接下来的某个时间的值或错误，借助Future我们可以在Flutter实现异步操作。

> 它类似于ES6中的Promise，提供then和catchError的链式调用；

```
import 'dart:async';

Future是dart:async包中的一个类，使用需要导入包，Future有两种状态

pending - 执行中
completed - 执行结束，分两种情况要么成功要么失败
```

#### Future的then

```
Future<String> testFuture() {
  return Future.value('hello');
}

调用
testFuture().then((s) {
  print(s);
}, onError: (e) {
  print(e);
});

```

#### async await

```
test() async {
  int result = await Future.delayed(Duration(milliseconds: 2000),() {
    return Future.value(1);
  });

  print('---$result');
}
```

### JSON解析和序列化
- 从服务端返回的json数据格式，Flutter需要对json格式的字符串转为Dart对象。

#### 手动序列化数据，在模型中序列化JSON数据

```
{
  "id":"487349",
  "name":"Pooja Bhaumik",
  "score" : 1000
}

//实现一个构造函数时使用 factory 关键字时
class Student{
  String studentId;
  String studentName;
  int studentScores;

  Student({
    this.studentId,
    this.studentName,
    this.studentScores
  });

  //创建了一个叫做 Student.fromJson 的工厂方法，用来简单地反序列化你的 json
  //Map<String, dynamic> 这意味着它将 String 键映射为 dynamic 值
  //Map<String, dynamic> 因为键总是一个 string 并且值可以是任何类型，所以我们将它保持为 dynamic 以保证安全
  factory Student.fromJson(Map<String, dynamic> parsedJson){
    return Student(
      studentId: parsedJson['id'],
      studentName : parsedJson['name'],
      studentScores : parsedJson ['score']
    );
  }
}
```

```
//使用
Future loadStudent() async {
  String jsonString = await _loadAStudentAsset();
  final jsonResponse = json.decode(jsonString); //解码我们得到的 json 字符串
  //通过调用 Student.fromJson 方法反序列化解码的 json 响应，这样我们现在可以使用 Student 对象来访问我们的实体
  Student student = new Student.fromJson(jsonResponse);
  print(student.studentScores);
}
```
#### 使用代码生成库序列化 JSON 数据

> 使用Dio请求返回数据不是Json字符串，而是Json对象

- 将responseType 设置为ResponseType.plain，默认使用json接收的。

```
responseType: ResponseType.plain,
```

```
json_annotation: ^2.0.0
build_runner: ^1.0.0
json_serializable: ^2.0.0
```

```
import 'package:json_annotation/json_annotation.dart';

//生成 user.g.dart 文件
part 'user.g.dart';

//生成的JSON序列化逻辑
@JsonSerializable(explicitToJson: true)

class User {
  User(this.name, this.email);

  String name;
  String email;

  //创建新的User实例的必要工厂构造函数，转Dart的对象
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);

  //转换为JSON
  Map<String, dynamic> toJson() => _$UserToJson(this);
}

```
- 每次修改模型文件，都要重新运行 - flutter packages pub run build_runner build

```
使用

getHttp() async { 
  try {
      
    var jsonString = await HttpUtil().get("/io/flutter_app/json/home_page.json");

    //jsonDecode() 方法来解码 JSON
    Map userMap = jsonDecode(jsonString.data);

    //在模型类中序列化 JSON 数据
    var resultJson = HomeModel.fromJson(userMap); 
    print('-----------');
    print(resultJson.config);
  } catch (e) {
    print(e);
  }
}
```

### shared_preferences本地存储

- 简单的，异步的，持久化的key-value存储系统；
- 在Android上它是基于SharedPreferences的；
- 在iOS上它是基于NSUserDefaults的；

```
shared_preferences: ^0.5.3

import 'package:shared_preferences/shared_preferences.dart';

设置 key值为counter，value为10
SharedPreferences prefs = await SharedPreferences.getInstance();
await prefs.setInt('counter', 10);

获取,假如获取不到counter为0
int counter = prefs.getInt('counter')??0;

删除
prefs.remove('counter')
```
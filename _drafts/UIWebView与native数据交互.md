#UIWebView与native数据交互
## 背景
我们在APP的开发中，一定会有某些页面是用js来写的。因为js跨平台，开发成本相对较低等等很多方面的优势。我们在iOS中一般是通过UIWebView来展示一个HTML页面，那么我们在UIWebView中与native的数据交互就显得尤其的重要。比如在js中如何获取到手机的一些参数，位置信息，设备信息。同样，我们也可能需要从UIWebView中传递一些数据到native中，那么我们就需要有一套UIWebView与native的交互方案可以使得在UIWebView与native进行数据交互。
## 方案
### UIWebViewDelegate
我们可以通过实现UIWebViewDelegate的方法来监听UIWebView的所有的请求。

```
@interface ViewController ()<UIWebViewDelegate>
```
在VC中实现UIWebViewDelegate，这样UIWebView中所有的请求我们都可以在下面的函数中获取到。

```
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType{
    NSLog(@"url：%@",request.URL);
    if (<#condition#>) {
        return false;
    } else {
        return true;
    }
    
}
```
如果我们想要从UIWebView中传递数据到native中，我们可以伪造以"jsBridge"开头的请求，这样我们在native拦截到以"jsBridge"的请求就不发送真实的请求，而是读取请求中对应的数据，这样我们就可以从js中传递数据到native中。

缺点：js传递数据到native中，但是native无法返回值到js中。
### 使用JavaScriptCore
iOS提供了JavaScriptCore库可以进行native与js进行数据交互。

```
#import <JavaScriptCore/JavaScriptCore.h>
```
在VC中导入JavaScriptCore库

```
    UIWebView *w = [[UIWebView alloc]initWithFrame:[[UIScreen mainScreen]applicationFrame]];
    //NSURLRequest *request =[NSURLRequest requestWithURL:[NSURL URLWithString:@"file:///Users/winbo/HBuilderProjects/FirstHtml/index.html"]];
    NSURLRequest *request =[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://192.168.10.174/steam/video.html"]];
    [w loadRequest:request];
    w.scrollView.bounces = NO;//设置不可上下拖动
    w.delegate = self;
    w.allowsInlineMediaPlayback = YES;
    w.mediaPlaybackAllowsAirPlay = NO;//全屏播放返回网页播放时，设置可继续播放
    w.mediaPlaybackRequiresUserAction = NO;
    
    [self.view addSubview:w];
    
    [self initJSBridgeAPI:w];
```
我们创建一个UIWebView，在最后一行是调用了一个初始化js与native交互的函数，函数的具体实现在下边。

```
//提供给js调用的API
- (void)initJSBridgeAPI:(UIWebView *)w{
    JSContext *context = [w valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
    //此方法用于web向native上报信息，并存储在native应用本地供以后获取
    context[@"edu_pushBI"] = ^(NSString *key, NSString *value){
        NSUserDefaults *defaults=[NSUserDefaults standardUserDefaults];
        NSLog(@"edu_pushBI key:%@,value:%@",key,value);
        [defaults setObject:value forKey:key];
        [defaults synchronize];
        return true;
    };
}
```
在上面代码中我们可以看到，我们通过JSContext向js中提供了名字为edu_pushBI的函数，函数的入参为两个NSString，返回的是一个bool类型的值。

```
Boolean result = edu_pushBI("key","value");
```
这样我们在js端就可以直接调用edu_pushBI函数，得到一个bool类型的返回值。

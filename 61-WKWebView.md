# WKWebView

在iOS8之前，我们如果需要在APP中嵌入Web页面是通过UIWebView来实现，苹果在iOS8以后推出了webkit新框架，提供了WKWebView组件用来替换存在各种问题的UIWebview，用WKWebview加载网页，相较于UIWebview速度更快了，内存占用更少了。WKWebview还提供了更加丰富的接口，功能更加强大.

特点：

* 添加了更多的支持HTML5的特性

* 高达60fps的滚动刷新率和丰富的内置手势(Built-in gestures)

* 将UIWebViewDelegate与UIWebView拆分成了14类与3个协议,以前很多不方便实现的功能得以实现。

* Safari相同的JavaScript引擎Nitro(JJT四个进程解释执行优化js代码)(Fast JavaScript)

* 占用更少的内存

* app和网页的交互更简便（Easy app-webpage communication）

* 响应滚动（Responsive scrolling）

* 更省电量 （battery）

## WKWebView的使用

两个代理：WKUIDelegate 和 WKNavigationDelegate

### WKUIDelegate

WKUIDelegate主要用来处理JavaScript的一些脚本，比如：确认框，警告框等。


```swift
/// WKWebView创建初始化加载的一些配置
optional public func webView(_ webView: WKWebView, createWebViewWith configuration: WKWebViewConfiguration, for navigationAction: WKNavigationAction, windowFeatures: WKWindowFeatures) -> WKWebView?

/// WkWebView已经关闭
optional public func webViewDidClose(_ webView: WKWebView)

/// 处理网页js中的提示框,若不使用该方法,则提示框无效
optional public func webView(_ webView: WKWebView, runJavaScriptAlertPanelWithMessage message: String, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping () -> Void)

/// 处理网页js中的确认框,若不使用该方法,则确认框无效
optional public func webView(_ webView: WKWebView, runJavaScriptConfirmPanelWithMessage message: String, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (Bool) -> Void)

/// 处理网页js中的文本输入
optional public func webView(_ webView: WKWebView, runJavaScriptTextInputPanelWithPrompt prompt: String, defaultText: String?, initiatedByFrame frame: WKFrameInfo, completionHandler: @escaping (String?) -> Void)
```

### WKNavigationDelegate

WKNavigationDelegate主要用来处理一些跳转、加载处理等导航操作。

```swift

追踪加载过程的代理方法

/// 页面开始加载时调用
optional public func webView(_ webView: WKWebView, didStartProvisionalNavigation navigation: WKNavigation!)

/// 内容加载失败时调用
optional public func webView(_ webView: WKWebView, didFailProvisionalNavigation navigation: WKNavigation!, withError error: Error)

/// 处理页面内容并开始返回时调用
optional public func webView(_ webView: WKWebView, didCommit navigation: WKNavigation!)

/// 页面加载完成时调用
optional public func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!)

/// 处理网页返回内容时发生的失败则调用
optional public func webView(_ webView: WKWebView, didFail navigation: WKNavigation!, withError error: Error)
```


```swift

监听页面跳转的代理方法，分为：收到跳转与决定是否跳转两种

/// 接收到服务器跳转请求之后调用
optional public func webView(_ webView: WKWebView, didReceiveServerRedirectForProvisionalNavigation navigation: WKNavigation!)

/// 在发送请求之前，决定网页能否被允许跳转
optional public func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void)

// 在收到响应后，决定是否跳转
optional public func webView(_ webView: WKWebView, decidePolicyFor navigationResponse: WKNavigationResponse, decisionHandler: @escaping (WKNavigationResponsePolicy) -> Void) ;

/// 网页进程终止
optional public func webViewWebContentProcessDidTerminate(_ webView: WKWebView)

```



### WKWebView常规操作


```swift

属性

// 网页标题
var title: String? { get }

//  网页加载中当前的进度
var estimatedProgress: Double { get }

// 网页是否能够后退
var canGoBack: Bool { get }

// 网页是否能够前进
var canGoForward: Bool { get }

方法

// 加载请求
func load(_ request: URLRequest) -> WKNavigation?

// 网页后退
func goBack() -> WKNavigation?

// 网页前进
func goForward() -> WKNavigation?

// 网页重新加载
func reload() -> WKNavigation?

// 网页停止加载  
func stopLoading()

```

### Swift与JavaScript的交互

#### webView 执行JS代码

```swift
open func evaluateJavaScript(_ javaScriptString: String, completionHandler: ((Any?, Error?) -> Void)? = nil)
```

#### JavaScript 调用 Native APP

###### WKScriptMessageHandler协议

```swift

协议中包含的方法如下：

/// 当从web界面中接收到一个脚本时调用
public func userContentController(_ userContentController: WKUserContentController, didReceive message: WKScriptMessage)
```

此方法可直接将接收到的JS脚本转为Swift对象。





# Build a WebView

[Once you have a Portal instance](./) and [you have generated a wallet](create-a-wallet.md), you can now build a web view to interact with dApps using the Portal wallet.

In the example below, we created `WebViewController` that initializes Portal's `PortalWebView` and adds it as a child view controller.\
\
`PortalWebView` init parameters:&#x20;

* **portal**: Your Portal instance.
* **url**: The URL the web view should start at.
* **persistSessionData**: Will persist browser session data (local-storage, cookies, etc...) when enabled.
* **onError**: An error handler in case the web view throws errors.
* **onPageStart**: A handler that fires when the web view is starting to load a page.
* **onPageComplete**: A handler that fires when the web view has finished loading a page.
* **eip6963Icon**: A string representing the Base64-encoded icon for EIP-6963 compliance.
* **eip6963Name**: A string representing the name for EIP-6963 compliance.
* **eip6963Rdns**: A reverse DNS string for identifying the application in EIP-6963-compliant contexts.
* **eip6963Uuid**: A unique identifier string for EIP-6963 compliance.

```swift
import PortalSwift
import WebKit

class WebViewController: UIViewController, PortalWebViewDelegate {
  var portal: Portal?
  var url: String?
  var webViewController: PortalWebView?
  let persistSessionData = false
  let eip6963Icon = "Base64-encoded image string"
  let eip6963Name = "Portal MPC Wallet"
  let eip6963Rdns = "io.portalhq"
  let eip6963Uuid = "d73d7104-7e24-442b-913b-1147cd8e0325"

  override func viewDidLoad() {
    super.viewDidLoad()

    guard let portal else {
      print("❌ WebViewController error: The portal object is nil.")
      return
    }

    guard let url else {
      print("❌ WebViewController error: The url object is nil.")
      return
    }

    guard let url = URL(string: url) else {
      print("❌ WebViewController error: URL could not be derived.")
      return
    }

    webViewController = PortalWebView(
      portal: portal,                                // Your Portal instance.
      url: url,                                      // The URL the web view should start at.
      persistSessionData: persistSessionData,        // Will persist browser session data (local-storage, cookies, etc...) when enabled.
      onError: self.onErrorHandler,                  // An error handler in case the web view throws errors.
      onPageStart: self.onPageStartHandler,          // A handler that fires when the web view is starting to load a page.
      onPageComplete: self.onPageCompleteHandler,    // A handler that fires when the web view has finished loading a page.
      eip6963Icon: eip6963Icon,                      // A string representing the Base64-encoded icon for EIP-6963 compliance.
      eip6963Name: eip6963Name,                      // A string representing the name for EIP-6963 compliance.
      eip6963Rdns: eip6963Rdns,                      // A reverse DNS string for identifying the application in EIP-6963-compliant contexts.
      eip6963Uuid: eip6963Uuid                       // A unique identifier string for EIP-6963 compliance.
    )

    guard let webViewController = webViewController else {
      print("❌ WebViewController error: the PortalWebView object is nil.")
      return
    }

    webViewController.delegate = self

    // Install the WebViewController as a child view controller.
    addChild(webViewController)

    guard let webViewControllerView = webViewController.view else {
      print("❌ WebViewController error: webViewController.view could not be derived.")
      return
    }

    view.addSubview(webViewControllerView)
    webViewController.didMove(toParent: self)
  }

  override func viewDidDisappear(_ animated: Bool) {
    super.viewDidDisappear(animated)
    self.webViewController = nil
  }

  func onPageStartHandler() {
    print("🔄 PortalWebView: Page loading started")
    // Add Loader here if needed
  }

  func onPageCompleteHandler() {
    print("✅ PortalWebView: Page loading completed")
    // Remove the loader here if needed
  }

  func onErrorHandler(result: Result<Any>) {
    if let error = result.error {
      print("❌ PortalWebviewError:", error, "Description:", error.localizedDescription)
      return
    }

    guard let dataAsAnyObject = result.data as? AnyObject,
          let nestedResult = dataAsAnyObject.result as? Result<Any>
    else {
      print("❌ Unable to cast result data")
      return
    }

    if let nestedError = nestedResult.error {
      print("❌ Error in nested PortalWebviewError:", nestedError)
      return
    }
  }

  func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) {
    print("✅ Delegate method fired!", webView, navigationAction, decisionHandler)
    decisionHandler(.allow)
  }
}
```

And thats it! Remember that you will need a portal instance to start the web view and you will also need to initialize Portal with `autoApprove: true` if you want the web view to auto-approve transactions.



Next, let's explore how to use Portal's Swaps integration to perform swaps directly from your iOS app.

# Build a WebView

[Once you have a Portal instance](./) and [you have generated a wallet](create-a-wallet.md), you can now create a WebView to interact with dApps using the Portal wallet.

In the example below, we have an Activity that creates an instance of the `PortalWebView` Fragment and adds it to the view.

```kotlin
class PortalWebViewActivity() : AppCompatActivity() {
  private lateinit var portal: Portal
  private lateinit var webView: PortalWebView


  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    setContentView(R.layout.activity_portal_webview)
    portal = (application as PortalApplication).getPortal()
    
    val url = "https://app.uniswap.org"
    
    webView = portal.createWebView(url) // This returns an instance of PortalWebView

    // Add the PortalWebView Fragment to the view
    val transaction = supportFragmentManager.beginTransaction()
    transaction.replace(R.id.webViewContainer, webView)
    transaction.commit()
  }
  
  fun goBack() {
    if (webView.canGoBack()) {
      webView.goBack() // Navigates the WebView to the previous page
    }
  }
  
  fun goForward() {
    if (webView.canGoForward()) {
      webView.goForward() // Navigates the WebView to the next page
    }
  }
  
  fun reload() {
    webView.reload()
  }
}
```

And thats it! Remember that you will need a portal instance to start the web view and you will also need to initialize Portal with `autoApprove: true` if you want the web view to auto-approve transactions.

### Optional Parameters

Following are the optional parameters that you can pass to customize the injected provider in the WebView further

* **chainId:** Chain id you want to run the dapp on, by default it is `eip155:11155111`
* **eip6963Icon:** A string representing the Base64-encoded icon for EIP-6963 compliance
* **eip6963Name:** A string representing the name for EIP-6963 compliance.
* **eip6963Rdns:** A reverse DNS string for identifying the application in EIP-6963-compliant contexts.
* **eip6963Uuid:** A unique identifier string for EIP-6963 compliance.

Next, let's explore how to use Portal's Swaps integration to perform swaps directly within your Android app.

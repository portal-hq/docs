# Create a wallet

In the MPC **wallet generation** process, two sets of key shares are generated. The **signing key shares** are used for signing transactions, and the **backup key shares** are used for recovery if the device storing a signing key shares is lost.

The `generate` command initiates the MPC process to create a set of shares. The users share and the wallet address is automatically stored in the user's secure phone storage.

[Once you have a Portal instance](create-a-wallet.md), you can now generate a wallet. In the example below, we create a `Generate Wallet` button:

```kotlin
// Imports...

class MainActivity : AppCompatActivity() {
    lateinit var portal: Portal
    lateinit var generateButton: Button
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        generateButton = findViewById(R.id.generateButton)
        generateButton.setOnClickListener { handleGenerate() }
    }

    private fun handleGenerate() {
        lifecycleScope.launch {
            portal.createWallet { status ->
                // Do something with the status, such as update a progress bar
                // or log the progress
                Log.d(
                  "[PortalEx]",
                  "Generate status: ${status.status} is done: ${status.done}",
                )
              }.onSuccess { walletAddresses ->
                Log.d(
                  "[PortalEx]",
                  "✅ Generated address successfully! $walletAddresses",
                )
              }.onFailure { e ->
                Log.d("[PortalEx]", "Error generating share: ${e.stackTraceToString()}")
              }
        }
    }
}
```

{% hint style="warning" %}
Be sure to use a **Production** API key when creating clients for production. Read more details about going to production [here](../../resources/going-to-production.md).
{% endhint %}

{% hint style="danger" %}
**WARNING**: To create a wallet with the Portal SDK, your device must be configured to use passcode authentication. If you change your passcode, your Portal wallet will continue to operate as expected. However, if you disable passcode authentication after running the `generate` function, you will be required to execute the `recover` function before you can continue using your Portal wallet.
{% endhint %}

### Status Flow

<pre class="language-kotlin"><code class="lang-kotlin"><strong>1. GeneratingShare("Generating share")
</strong>2. ParsingShare("Parsing share")
3. StoringShare("Storing share")
4. Done("Done")
</code></pre>

Now that you've generated a wallet, let's back it up in the next section.

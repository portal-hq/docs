# Create a client

In order to create a wallet, we first need to create a Portal **client**. Although in your production environment you will be doing this programmatically via the [new Client](../../reference/custodian-api/v1-endpoints.md#create-a-new-client) REST API endpoint, for testing purposes you can head to the Portal Admin Dashboard to create a test client.

A Portal **client** represents a single user. You use the Portal client as a 1:1 with users on your backend.

### Steps

1. Log in to the [Portal Admin Dashboard](https://app.portalhq.io). If don’t have access reach out to us to [request trial access](https://www.portalhq.io/book-demo)!
2.  After you login, under “Settings” on the left hand side of the page, click on “API Keys”.

    <figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>Navigate to Settings -> API Keys</p></figcaption></figure>
3.  Once you have located the API keys section, click on “New Test Client API Key”. This will generate a test **client** with a **Client API Key -** test credentials for development purposes.

    <figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption><p>Click "New Test Client API Key"</p></figcaption></figure>
4.  After clicking the “New Test Client API Key” button, this will pop open a window displaying the Client API Key and the Client ID. Go ahead and copy the Client API Key

    <figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption><p>Copy the "Client API Key" to your clipboard</p></figcaption></figure>

Congrats! 🎉 You have now created your first Portal client and are ready to generate a wallet!

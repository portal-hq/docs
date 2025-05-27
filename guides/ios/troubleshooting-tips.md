# Troubleshooting Tips

## Debugging Development Performance

When developing and debugging on physical iOS devices with Portal SDK, you may experience slower performance compared to running without a debugger or on simulators. To optimize the debugging experience on physical iOS devices, add the following environment variable to your Xcode scheme:

1. Open your project in Xcode
2. Go to Product > Scheme > Edit Scheme (or press ⌘<)
3. Select "Run" on the left sidebar
4. Go to the "Arguments" tab
5. In the "Environment Variables" section, click "+"
6. Add:
   * Name: `GODEBUG`
   * Value: `asyncpreemptoff=1`

This optimization will significantly improve the debugging experience when using a debugger on physical devices.

{% hint style="info" %}
```
This environment variable only affects debugging sessions and has no impact on release builds or production performance. Your app will perform optimally in production whether or not you add this debugging configuration - it only helps improve the development experience when debugging on physical devices.
```
{% endhint %}

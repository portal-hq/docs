---
description: >-
  Feature flags allow you to enable or disable specific features in the Portal
  SDK. This provides flexibility in customizing the behavior of the SDK for your
  application.
---

# Feature Flags

### Overview

The `FeatureFlags` struct is used to configure feature flags when initializing the Portal SDK. Each flag corresponds to a specific feature or behavior that can be toggled on or off.

#### Example Usage:

```swift
import PortalSwift

// Initialize Portal with custom feature flags
let portal = try Portal(
  "CLIENT_API_KEY_OR_CLIENT_SESSION_TOKEN", 
  featureFlags: FeatureFlags(
    isMultiBackupEnabled: true,
    useEnclaveMPCApi: true
  )
)
```

***

### Available Feature Flags

Below is a list of available feature flags and their functionality.

#### 1. `isMultiBackupEnabled`

* **Type**: `Bool?`
* **Default**: `nil` (disabled)
* **Description**: Enables or disables multi-backup functionality. When enabled, users can create multiple backups.

**Example:**

```swift
let portal = try Portal(
  "CLIENT_API_KEY_OR_CLIENT_SESSION_TOKEN", 
  featureFlags: FeatureFlags(
    isMultiBackupEnabled: true
  )
)
```

***

#### 2. `useEnclaveMPCApi`

* **Type**: `Bool?`
* **Default**: `nil` (disabled)
* **Description**: Enables the use of the **Enclave MPC API** for signing transactions. When enabled, MPC operations are executed server-side in a secure AWS Nitro Enclave, ensuring consistent and faster signing times.

**How It Works**

Executing MPC operations on client devices can lead to inconsistent signing times due to variations in device CPU performance. By enabling the `useEnclaveMPCApi` flag, the client key share is transmitted to a **Trusted Execution Environment (TEE)** hosted in an AWS Nitro Enclave. This ensures:

1. **Encrypted Memory**: All data processed in the enclave is encrypted and inaccessible to anyone, including Portal employees.
2. **Verified Execution**: Users can cryptographically verify that their request was handled in a secure enclave using signed measurements.

**Example:**

```swift
import PortalSwift

// Initialize Portal with the Enclave MPC API enabled
let portal = try Portal(
  "CLIENT_API_KEY_OR_CLIENT_SESSION_TOKEN", 
  featureFlags: FeatureFlags(
    useEnclaveMPCApi: true
  )
)
```

By setting `useEnclaveMPCApi` to `true`, the Portal instance will use the Enclave MPC API for signing transactions, ensuring faster computation and consistent performance across client devices.

\

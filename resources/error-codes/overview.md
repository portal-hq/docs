---
description: Learn about Portal Errors
---

# Overview

## Portal MPC Error Codes

This guide documents the error identifiers returned by Portal's MPC (Multi-Party Computation) operations. These errors follow a string-based identifier system that provides clear, meaningful error categorization.

### Error Structure

MPC operations return errors in a standardized JSON format:

```json
{
  "error": {
    "id": "RPC_OP_FAILED",
    "message": "Failed to send transaction: insufficient funds for gas * price + value: balance 1109136374275907, tx cost 2726006969664281, overshot 1616870595388374",
    "code": 123  // Deprecated - do not use!
  }
}
```

#### Error Fields

* **`id`** (string): A stable, uppercase identifier. Use this field to identify and handle specific errors programmatically.
* **`message`** (string): Human-readable details about the error. May contain dynamic information about the specific failure. This field is usually present but may occasionally be missing.
* **`code`** (number): **DEPRECATED** - Legacy numeric error code maintained for backward compatibility. Will be removed in future versions.

> **Note**: When using Portal SDK, the raw response structure may not be directly exposed to your application, but you can still access the `id` and `message` fields through the SDK's error handling mechanisms.

### Error Identifier Format

Error identifiers follow a consistent naming pattern:

```
MODULE_[CONTEXT_]ERROR
```

* **MODULE**: The system component where the error originated (e.g., `AUTH`, `RPC`, `WS`)
* **CONTEXT**: Optional qualifier providing additional context
* **ERROR**: The specific error condition

Examples:

* `AUTH_FAILED` - Authentication failure
* `MSG_BUILD_FAILED` - Failed to build the message
* `DKG_FAILED` - Distributed Key Generation failed

### Example of errors in different categories

#### General Errors

These errors can occur across various MPC operations:

| Error ID                | Description                                                            |
| ----------------------- | ---------------------------------------------------------------------- |
| `ERROR`                 | Generic error when a more specific error type isn't available          |
| `INTERNAL_ERROR`        | Internal server error - typically indicates an unexpected system issue |
| `BAD_REQUEST`           | Invalid request format or parameters                                   |
| `NOT_FOUND`             | Requested resource not found                                           |
| `STATE_INVALID`         | Operation attempted in an invalid state                                |
| `OPERATION_IN_PROGRESS` | Another operation is already in progress                               |
| `WEBHOOK_FAILED`        | Webhook verification or execution failed                               |

#### Authentication & Access Control

Errors related to authentication, authorization and security:

| Error ID          | Description                                           |
| ----------------- | ----------------------------------------------------- |
| `AUTH_FAILED`     | Authentication failed - invalid credentials or token  |
| `ADDRESS_BLOCKED` | The address is blocked from performing this operation |

#### Transaction & Message Building

Errors that occur when constructing, signing, and sending transactions and messages:

| Error ID                   | Description                                                           |
| -------------------------- | --------------------------------------------------------------------- |
| `TX_BUILD_FAILED`          | Failed to build the transaction                                       |
| `MSG_BUILD_FAILED`         | Failed to build the message                                           |
| `METHOD_UNSUPPORTED`       | The requested method is not supported                                 |
| `RPC_OP_FAILED`            | RPC operation failed (often includes details like insufficient funds) |
| `MPC_PROTOCOL_UNSUPPORTED` | The requested MPC protocol is not supported                           |

#### WebSocket Communication

Network and WebSocket-specific errors:

| Error ID           | Description                                  |
| ------------------ | -------------------------------------------- |
| `WS_MSG_MALFORMED` | WebSocket message format is invalid          |
| `WS_NETWORK_ERROR` | Network error during WebSocket communication |
| `WS_CLOSED`        | WebSocket connection was unexpectedly closed |
| `WS_READ_FAILED`   | Failed to read from WebSocket                |
| `WS_WRITE_FAILED`  | Failed to write to WebSocket                 |

#### MPC Protocol Operations

Errors specific to Multi-Party Computation operations:

| Error ID               | Description                          |
| ---------------------- | ------------------------------------ |
| `DKG_FAILED`           | Distributed Key Generation failed    |
| `DKG_REFRESH_FAILED`   | DKG refresh operation failed         |
| `DKG_REFRESH_MISMATCH` | DKG refresh data mismatch detected   |
| `SIGN_FAILED`          | Signature generation failed          |
| `SIGN_SHARE_MISMATCH`  | Share mismatch detected when signing |
| `ENCRYPT_FAILED`       | Encryption operation failed          |
| `DECRYPT_FAILED`       | Decryption operation failed          |

#### Key & Share Management

Errors related to cryptographic keys and share operations:

| Error ID                         | Description                           |
| -------------------------------- | ------------------------------------- |
| `SAVE_SIGNING_SHARE_FAILED`      | Failed to save signing share          |
| `SAVE_BACKUP_SHARE_FAILED`       | Failed to save backup share           |
| `PUBLIC_KEY_BUILD_FAILED`        | Failed to build public key            |
| `PUBLIC_KEY_VERIFICATION_FAILED` | Public key verification failed        |
| `ADDRESS_CALC_FAILED`            | Wallet address calculation failed     |
| `PRIVATE_KEY_RECOVERY_FAILED`    | Private key recovery operation failed |
| `SHARE_PARSING_FAILED`           | Failed to parse provided share        |
| `METADATA_INVALID`               | Invalid metadata provided             |

### Error Handling Guidelines

#### 1. Use Error IDs for Logic

Always use the `id` field when implementing error handling logic. Error IDs are stable and designed for programmatic use.

#### 2. Optional Human-Readable Messages

Use the `message` field to get details about the error. Depending on your use case, the message can be displayed to users or included as additional context in application error logs. Avoid parsing the message content programmatically, as the wording may change over time.

#### 3. Avoid Using Numeric Codes

Do not use the deprecated `code` field. It exists only for backward compatibility and will be removed in future versions.

#### 4. Handle Missing Messages

While rare, the `message` field may occasionally be missing. Always provide a fallback when displaying errors to users.

### Common Error Resolution

When encountering persistent errors that cannot be resolved through standard error handling:

1. **Check Portal Status**: Visit the [Portal status](https://portal.instatus.com/) page to check for any ongoing service disruptions or maintenance.
2. **Verify Input Data**: Ensure all input parameters are correctly formatted and valid:
   * Cipher text hasn't been modified or corrupted
   * Addresses are properly formatted
   * Transaction parameters are within acceptable ranges
3. **Contact Support**: If the issue persists, contact Portal support with:
   * The error ID
   * The complete error message
   * Steps to reproduce the issue
   * Any relevant request/response data (excluding sensitive information)

### Migration Guide

If you're migrating from the old numeric error system to the new string-based identifiers:

#### Old System (Numeric Codes)

Previously, errors were identified by numeric codes in ranges:

* 1xx: MPC Errors
* 2xx: Network Errors
* 3xx: General Errors
* 4xx: Encryption Errors
* 5xx: Portal Connect Errors

#### New System (String IDs)

Now, use specific string identifiers:

```javascript
// Old system - checking specific numeric codes
if (error.code === 101) {
  // Handle DKG failure
}
if (error.code === 201) {
  // Handle network error
}

// New system - checking specific string IDs
if (error.id === 'DKG_FAILED') {
  // Handle DKG failure
}
if (error.id === 'WS_NETWORK_ERROR' || ) {
  // Handle network error
}
```

The string-based system provides more meaningful error identification without needing to remember numeric ranges or codes.

### Additional Resources

* [Portal Status Page](https://portal.instatus.com/)

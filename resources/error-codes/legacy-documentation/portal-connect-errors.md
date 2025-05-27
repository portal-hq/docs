---
description: These errors are related to failures with Portal Connect.
---

# Portal Connect errors

| Code | Name                                     | Description                                                                                                                          |
| ---- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 500  | Unknown Error                            | This could be caused due to a web socket issue or an issue with wallet connect. There might be more information in the error message |
| 501  | Error approving session                  | There was an issue approving the session with wallet connect                                                                         |
| 502  | Error changing chainId                   | There was an error changing the chain                                                                                                |
| 503  | Dapp did not acknowledge approval        | The dapp had an issue acknowledeging the approval of the connection                                                                  |
| 504  | Received data is not of type ConnectData | You have approved/rejected a request to connect from a dapp but passed in the incorrect data for approval.                           |


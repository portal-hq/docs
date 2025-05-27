# Rate Limits

This page outlines the rate limits for API requests to `portalhq.io`. These limits are applied in order of priority, meaning more specific routes take precedence over general ones.

### Rate Limits Overview

<table><thead><tr><th width="156.1875">Name</th><th width="390.171875">Routes</th><th>Limit (Requests per minute)</th></tr></thead><tbody><tr><td><strong>Clients API</strong></td><td><code>api.portalhq.io/api/:version/clients/*</code></td><td>200</td></tr><tr><td><strong>Custodian API</strong></td><td><code>api.portalhq.io/api/:version/custodian/*</code></td><td>200</td></tr><tr><td><strong>Enclave MPC API</strong></td><td><code>mpc.portalhq.io/*</code></td><td>50</td></tr><tr><td><strong>All Services</strong></td><td><code>*</code></td><td>200</td></tr></tbody></table>

If you have any questions or need further clarification, please reach out to our support team.

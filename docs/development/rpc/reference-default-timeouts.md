# Reference: Default Timeouts for RPCs

This document provides recommended default timeout values for various RPC scenarios. These are general guidelines and may need to be adjusted based on the specific requirements of your service.

| Scenario                                  | Recommended Timeout | Justification                                                                                                                              |
| ----------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Inter-service communication (same datacenter) | 2 seconds           | Low-latency, high-bandwidth network. A 2-second timeout is generally sufficient to handle most requests without causing cascading failures.      |
| External API calls                        | 5-10 seconds        | Higher latency and less predictable performance. A longer timeout is necessary to accommodate for network variability and potential slowness. |
| User-facing requests                      | 1 second            | Users expect fast responses. A short timeout ensures that the user is not left waiting for an unresponsive service.                          |
| Asynchronous background jobs              | 30-60 seconds       | These are typically long-running operations that can tolerate longer timeouts without impacting the user experience.                       |

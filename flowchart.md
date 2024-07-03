```mermaid
flowchart TD;
    A[AWS Cloud9] -->|Docker push| B[Amazon Elastic Container Registry];
    B -->|Deploy| C[AWS App Runner];
    D[Cloud Shell] -->|curl| C;
```
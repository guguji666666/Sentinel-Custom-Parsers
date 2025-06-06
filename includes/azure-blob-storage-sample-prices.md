---
 author: normesta
 ms.service: storage
 ms.topic: include
 ms.date: 05/15/2025
 ms.author: normesta
ms.custom:
  - build-2025
---

The following tables include sample (fictitious) prices for each request to the Blob Service endpoint (`blob.core.windows.net`). 

> [!IMPORTANT]
> These prices are meant only as examples, and shouldn't be used to calculate your costs. For official prices, see the [Azure Blob Storage pricing](https://azure.microsoft.com/pricing/details/storage/blobs/) or [Azure Data Lake Storage pricing](https://azure.microsoft.com/pricing/details/storage/data-lake/) pages. For more information about how to choose the correct pricing page, see [Understand the full billing model for Azure Blob Storage](../articles/storage/common/storage-plan-manage-costs.md).

# [Locally-redundant storage (LRS)](#tab/LRS)

The following tables assume that an account is configured for LRS redundancy.

| Price factor                                                    | Hot            | Cool           | Cold           | Archive |
|-----------------------------------------------------------------|----------------|----------------|----------------|---------|
| Price of write operations (per 10,000)                          | $0.055         | $0.10          | $0.18          | $0.11   |
| Price of read operations (per 10,000)                           | $0.0044        | $0.01          | $0.10          | $5.50   |
| List and container operations (per 10,000)                      | $0.055         | $0.055         | $0.18         | $.055   |
| All other operations (per 10,000)                               | $0.0044        | $0.0044        | $0.0052        | Not applicable |
| Price of data retrieval (per GB)                                | Free           | $0.01          | $0.03          | $.022   |
| Price of Data storage first 50 TB (pay-as-you-go)               | $0.0208        | $0.0115        | $0.0045        | $0.002  |
| Price of Data storage next 450 TB (pay-as-you-go)               | $0.020         | $0.0115        | $0.0045        | $0.002  |
| Price of 100 TB (One-year reserved capacity)                    | $1,747         | $966           | Not available  | $183    |
| Price of 100 TB (Three-year reserved capacity)                  | $1,406         | $872           | Not available  | $168    |
| Network bandwidth between regions within North America (per GB) | $0.02          | $0.02          | $0.02          | $0.02   |
| Price of high priority read operations (per 10,000)             | Not applicable | Not applicable | Not applicable | $65.00  |
| Price of high priority data retrieval (per GB)                  | Not applicable | Not applicable | Not applicable | $0.13   |

The following table includes sample prices (fictitious) prices for each request to the Data Lake Storage endpoint (`dfs.core.windows.net`). For official prices, see [Azure Data Lake Storage pricing](https://azure.microsoft.com/pricing/details/storage/data-lake/). 

| Price factor                                                    | Hot            | Cool           | Cold           | Archive        |
|-----------------------------------------------------------------|----------------|----------------|----------------|----------------|
| Price of write operations (every 4 MiB, per 10,000)             | $0.07150      | $0.13          | $0.234         | $0.143         |
| Price of read operations (every 4 MiB, per 10,000)              | $0.0057        | $0.013         | $0.13          | $7.15          |
| Iterative write operations (per 100)                            | $0.0715        | $0.0715        | $0.0715        | $0.0715        |
| Iterative read operations (per 10,000)                          | $0.0715        | $0.0715        | $0.0845        | $0.0715        |
| Price of data retrieval (per GB)                                | Free           | $0.01          | $0.03          | $0.022         |
| Network bandwidth between regions within North America (per GB) | $0.02          | $0.02          | $0.02          | $0.02          |
| Data storage prices first 50 TB (pay-as-you-go)                 | $0.021         | $0.012         | $0.0045        | $0.002         |
| Data storage prices next 450 TB (pay-as-you-go)                 | $0.020         | $0.012         | $0.0045        | $0.002         |
| Price of 100 TB (One-year reserved capacity)                    | $1,747         | $966           | Not available  | $183           |
| Price of 100 TB (Three-year reserved capacity)                  | $1,406         | $872           | Not available  | $168           |
| Price of high priority read operations (per 10,000)             | Not applicable | Not applicable | Not applicable | $84.50         |
| Price of high priority data retrieval (per GB)                  | Not applicable | Not applicable | Not applicable | $0.13          |
| Index (GB / month)                                              | $0.0297        | Not applicable | Not applicable | Not applicable |

# [Geo-redundant storage (GRS)](#tab/GRS)

The following tables assume that an account is configured for GRS redundancy.

| Price factor                                                    | Hot            | Cool           | Cold           | Archive |
|-----------------------------------------------------------------|----------------|----------------|----------------|---------|
| Price of write operations (per 10,000)                          | $0.1100        | $0.20          | $0.36          | $0.21   |
| Price of read operations (per 10,000)                           | $0.0044        | $0.01          | $0.10          | $5.50   |
| List and container operations (per 10,000)                      | $0.1100        | $0.110         | $0.110         | $.110   |
| All other operations (per 10,000)                               | $0.0044        | $0.0044        | $0.0052        | $.0044  |
| Price of data retrieval (per GB)                                | Free           | $0.01          | $0.03          | $.022   |
| Price of Data storage first 50 TB (pay-as-you-go)               | $0.0458        | $0.0230        | $0.0081        | $0.0030 |
| Price of Data storage next 450 TB (pay-as-you-go)               | $0.0440        | $0.0230        | $0.0081        | $0.0030 |
| Price of 100 TB (One-year reserved capacity)                    | $3,846         | $1,932         | Not available  | $273    |
| Price of 100 TB (Three-year reserved capacity)                  | $3,096         | $1,743         | Not available  | $252    |
| Data transfer fees (per GB)                                     | $0.02          | $0.02          | $0.02          | $0.02   |
| Network bandwidth between regions within North America (per GB) | $0.02          | $0.02          | $0.02          | $0.02   |
| Price of high priority read operations (per 10,000)             | Not applicable | Not applicable | Not applicable | $65.00  |
| Price of high priority data retrieval (per GB)                  | Not applicable | Not applicable | Not applicable | $0.13   |

The following table includes sample prices (fictitious) prices for each request to the Data Lake Storage endpoint (`dfs.core.windows.net`). For official prices, see [Azure Data Lake Storage pricing](https://azure.microsoft.com/pricing/details/storage/data-lake/). 

| Price factor                                                    | Hot            | Cool           | Cold           | Archive        |
|-----------------------------------------------------------------|----------------|----------------|----------------|----------------|
| Price of write operations (every 4 MiB, per 10,000)             | $0.1430        | $0.2600        | $0.4680        | $0.2730        |
| Price of read operations (every 4 MiB, per 10,000)              | $0.0057        | $0.013         | $0.13          | $7.15          |
| Iterative write operations (per 100)                            | $0.1430        | $0.1430        | $0.1430        | $0.1430        |
| Iterative read operations (per 10,000)                          | $0.1430        | $0.1430        | $0.1430        | $0.1430        |
| Price of data retrieval (per GB)                                | Free           | $0.01          | $0.03          | $0.022         |
| Network bandwidth between regions within North America (per GB) | $0.02          | $0.02          | $0.02          | $0.02          |
| Data storage prices first 50 TB (pay-as-you-go)                 | $0.046         | $0.046         | $0.0081        | $0.0030        |
| Data storage prices next 450 TB (pay-as-you-go)                 | $0.0440        | $0.0460        | $0.0081        | $0.0030        |
| Price of 100 TB (One-year reserved capacity)                    | $3,846         | $3,846         | Not available  | $273           |
| Price of 100 TB (Three-year reserved capacity)                  | $3,096         | $1,743         | Not available  | $252           |
| Price of high priority read operations (per 10,000)             | Not applicable | Not applicable | Not applicable | $84.50         |
| Price of high priority data retrieval (per GB)                  | Not applicable | Not applicable | Not applicable | $0.13          |
| Index (GB / month)                                              | $0.0655        | Not applicable | $0.0655        | Not applicable |

---



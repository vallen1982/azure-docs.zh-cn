---
title: 利用生存时间使 Azure Cosmos DB 中的数据过期 | Microsoft Docs
description: 通过 TTL 功能，Microsoft Azure Cosmos DB 能够在一段时间后将文档自动从系统清除。
services: cosmos-db
documentationcenter: ''
keywords: 生存时间
author: SnehaGunda
manager: kfile
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 61db8f85e73d2c071bdec0ace60911813fa4f0e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>利用生存时间使 Azure Cosmos DB 集合中的数据自动过期
应用程序可以生成和存储大量数据。 其中的某些数据（如计算机生成的事件数据、日志和用户会话信息）仅在有限的一段时间内才有用。 当数据变得多余，应用程序不再需要时，可以安全地清除这些数据并减少应用程序的存储需求。

通过“生存时间”或 TTL 功能，Microsoft Azure Cosmos DB 能够在一段时间后将文档自动从数据库清除。 可以在集合级别设置默认生存时间，并且可以在每个文档上覆盖该时间。 TTL 设置后，无论作为集合默认或在文档级别，Cosmos DB 都将自文档上次修改起的某段时间（以秒为单位）后，自动删除该文档。

Cosmos DB 中的生存时间针对上次修改该文档的时间使用偏移量。 为此，它会使用每个文档中存在的 `_ts` 字段。 _ts 字段为 unix 型的时期时间戳，表示日期和时间。 每次修改文档时都会更新 `_ts` 字段。 

## <a name="ttl-behavior"></a>TTL 行为
TTL 功能在两个级别受 TTL 属性控制 - 集合级别和文档级别。 设置这些值时以秒为单位，这些值被视为自上次修改文档所在的 `_ts` 起的增量。

1. 集合的 DefaultTTL
   
   * 如果缺失（或设置为 NULL），则文档不会自动删除。
   * 如果存在且值为“-1”= 无限期 - 则默认情况下，文档不过期
   * 如果存在且值为某个数字（“n”）- 则文档在上次修改“n”秒后过期
2. 文档的 TTL： 
   
   * 属性仅在对父集合设置 DefaultTTL 时适用。
   * 替代父集合的 DefaultTTL 值。

只要文档已过期（`ttl` + `_ts` <= 当前服务器时间），则文档就会标记为“已过期”。 此时间过后，将不允许对这些文档执行任何操作，这些文档也将从执行的任何查询结果中排除。 这些文档在系统中被物理删除，并且稍后找机会在后台删除。 这不占用集合预算的任何[请求单元 (RU)](request-units.md)。

上面的逻辑可显示在以下矩阵中：

|  | 集合上的 DefaultTTL 缺失/未设置 | 集合上的 DefaultTTL =-1 | 集合上的 DefaultTTL =“n” |
| --- |:--- |:--- |:--- |
| 文档上的 TTL 缺失 |在文档级别没有要替代的内容，因为文档和集合都没有 TTL 的概念。 |在此集合中的文档不会过期。 |在此集合中的文档会在 n 秒间隔后过期。 |
| 文档上的 TTL =-1 |在文档级别没有要替代的内容，因为集合并未定义文档可以替代的 DefaultTTL 属性。 系统未解释文档上的 TTL。 |在此集合中的文档不会过期。 |此集合中，TTL =-1 的文档将永不过期。 所有其他文档将在“n”秒间隔后过期。 |
| 文档上的 TTL = n |在文档级别没有要替代的内容。 系统未解释文档上的 TTL。 |TTL = n 的文档会在时间间隔 n 秒后过期。 其他文档将继承 -1 时间间隔，并且永不过期。 |TTL = n 的文档会在时间间隔 n 秒后过期。 其他文档将从集合中继承“n”时间间隔。 |

## <a name="configuring-ttl"></a>配置 TTL
默认情况下，在所有 Cosmos DB 集合和所有文档上禁用生存时间。 可以编程方式设置 TTL，或在 Azure 门户下，集合的“设置”部分中进行设置。 

## <a name="enabling-ttl"></a>启用 TTL
要在集合或集合内的文档上启用 TTL，需要将集合的 DefaultTTL 属性设置为 -1 或非零正数。 将 DefaultTTL 设置为 -1 表示默认情况下，集合中的所有文档将永久生存，但 Cosmos DB 服务应该监视此集合中已替代此默认值的文档。

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>在集合上配置默认 TTL
可以在集合级别配置默认生存时间。 若要在集合上设置 TTL，则需要提供一个非零正数，该数字表示自文档上次的修改时间戳 (`_ts`) 之后，集合中的所有文档过期所经过的时间段（以秒为单位）。 或者，可以将默认值设置为 -1，这意味着插入到集合中的所有文档在默认情况下将无限期地生存。

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>在文档上设置 TTL
除了在集合上设置默认的 TTL 外，还可以在文档级别设置特定的 TTL。 执行此操作将替代该集合的默认设置。

* 若要在文档上设置 TTL，则需要提供一个非零正数，该数字表示自文档上次的修改时间戳 (`_ts`) 之后，文档过期所经过的时间段（以秒为单位）。
* 如果文档没有 TTL 字段，将应用集合的默认值。
* 如果在集合级别禁用了 TTL，在文档上的 TTL 字段会被忽略，直到在集合上再次启用 TTL。

以下是演示如何在文档上设置 TTL 过期的代码片段：

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>在现有文档上扩展 TTL
通过对文档执行任何写入操作，可以在文档上重置 TTL。 执行此操作会将 `_ts` 设置为当前时间，并将再次开始 `ttl` 所设置的对文档到期的倒计时。 如果想要更改文档的 `ttl`，则可以像对任何其他可设置字段操作的那样更新字段。

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>从文档中移除 TTL
如果已在文档上设置 TTL，并且不再想要该文档过期，则可以检索文档，移除 TTL 字段并替换服务器上的文档。 当从文档中移除 TTL 字段时，将应用集合的默认值。 要阻止文档过期并且不从集合继承，则需要将 TTL 值设置为 -1。

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>禁用 TTL
若要在集合上完全禁用 TTL 并阻止后台进程查找过期文档，应删除集合上的 DefaultTTL 属性。 删除此属性不同于将其设置为 -1。 设置为 -1 表示添加到集合中的新文档将永久生存，但可以替代此集合中的特定文档。 完全从集合中移除该属性意味着文档不会过期，即使有的文档已显示替代以前的默认值。

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>TTL 和索引交互
通过添加或更改集合上的 TTL 设置更改基础索引。 将 TTL 值从 Off 更改为 On 时，集合将重新编制索引。 如果在索引模式为“一致”时对索引策略进行更改，用户将看不到对索引的更改。 当索引模式设置为“延迟”时，索引将始终同步，如果更改了 TTL 值，将从头重新创建索引。 如果更改了 TTL 值并且索引模式设置为“延迟”，则在索引重新生成期间完成的查询不会返回完整或正确的结果。

如果需要返回确切数据，请不要在索引模式设置为“延迟”时更改 TTL 值。 最好应选择一致的索引，以确保查询结果一致。 

## <a name="faq"></a>常见问题
**TTL 将收取我多少费用？**

在文档上设置 TTL 不会产生额外费用。

**TTL 运行后删除我的文档需要多长时间？**

TTL 启动后，文档将立即过期，并且无法通过 CRUD 或查询 API 进行访问。 

**文档上的 TTL 对 RU 费用是否会产生影响？**

否，不会对通过 Cosmos DB 中的 TTL 删除过期文档的 RU 费用产生影响。

**TTL 功能是否仅应用于整个文档，或者是否可以使单个文档的属性值过期？**

TTL 应用于整个文档。 如果只是想要使文档的一部分过期，则建议将该部分从主文档中提取到一个单独的“链接”文档，并对被提取的文档使用 TTL。

**TTL 功能是否具有特定的索引编制要求？**

是的。 该集合必须将[索引策略设置](indexing-policies.md)为“一致”或“迟缓”。 尝试在索引设置为“无”的集合上设置 DefaultTTL 将导致错误，尝试关闭已设置 DefaultTTL 的集合上的索引也是如此。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure Cosmos DB 的详细信息，请参阅该服务的[文档](https://azure.microsoft.com/documentation/services/cosmos-db/)页。


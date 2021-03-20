---
title: Telepítse át az alkalmazást az Amazon DynamoDB-ból Azure Cosmos DB
description: Ismerje meg, hogyan telepítheti át .NET-alkalmazását az Amazon DynamoDB, hogy Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334225"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Telepítse át az alkalmazást az Amazon DynamoDB-ból Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB egy méretezhető, globálisan elosztott, teljes körűen felügyelt adatbázis. Garantáltan alacsony késésű hozzáférést biztosít az adataihoz. Ha többet szeretne megtudni a Azure Cosmos DBről, tekintse meg az [áttekintő](introduction.md) cikket. Ez a cikk azt ismerteti, hogyan telepítheti át .NET-alkalmazását a DynamoDB-ről Azure Cosmos DBre minimális kód-módosításokkal.

## <a name="conceptual-differences"></a>Fogalmi különbségek

A Azure Cosmos DB és a DynamoDB között a következők a legfontosabb fogalmi eltérések:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Nem alkalmazható|  Adatbázis |
|Tábla      |  Gyűjtemény |
|  Elem |  Dokumentum |
|Attribútum|Mező|
|Másodlagos index|Másodlagos index|
|Elsődleges kulcs – partíciós kulcs|Partíciókulcs|
|Elsődleges kulcs – rendezési kulcs| Nem kötelező |
|Stream|ChangeFeed|
|Számítási egység írása|Kérési egység (rugalmas, olvasásra és írásra is használható)|
|Számítási egység olvasása    |Kérési egység (rugalmas, olvasásra és írásra is használható)|
|Globális táblák| Nem kötelező. Közvetlenül is kiválaszthatja a régiót az Azure Cosmos-fiók kiépítés közben (később módosíthatja a régiót)|

## <a name="structural-differences"></a>Strukturális különbségek

A Azure Cosmos DB egyszerűbb JSON-szerkezettel rendelkezik a DynamoDB képest. A következő példa a különbségeket mutatja be

**DynamoDB**:

A következő JSON-objektum a DynamoDB adatformátumát jelöli.

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos db**:

A következő JSON-objektum a Azure Cosmos DB adatformátumát jelöli

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Adatok áttelepítése

Számos lehetőség közül választhat, amelyek segítségével áttelepítheti az adatait Azure Cosmos DBba. További információ: a helyszíni [vagy a Felhőbeli információk áttelepítésének lehetőségei Azure Cosmos db](cosmosdb-migrationchoices.md) cikkre.

## <a name="migrate-your-code"></a>Kódok áttelepítése

Ez a cikk az alkalmazás kódjának Azure Cosmos DBre való áttelepítésének hatókörét képezi, amely az adatbázis-áttelepítés kritikus aspektusa. A tanulási görbe csökkentése érdekében a következő részekben az Amazon DynamoDB és a Azure Cosmos DB egyenértékű kódrészlete közötti párhuzamos kódok szerepelnek.

A forráskód letöltéséhez a következő adattár klónozása szükséges:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Előfeltételek

- .NET-keretrendszer 4.7.2
- Visual Studio 2019
- Hozzáférés Azure Cosmos DB SQL API-fiókhoz
- Az Amazon DynamoDB helyi telepítése
- Java 8
- Futtassa az Amazon DynamoDB letölthető verzióját a 8000-as porton (a kód módosítható és konfigurálható)

### <a name="set-up-your-code"></a>A kód beállítása

Adja hozzá a következő "NuGet-csomagot" a projekthez:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Kapcsolat létesítése

**DynamoDB**:

Az Amazon DynamoDB a következő kódot használja a kapcsolódáshoz:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos db**:

Azure Cosmos DB összekapcsolásához frissítse a kódját a következőre:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**A Azure Cosmos DB-beli kapcsolatok optimalizálása**

A Azure Cosmos DB a következő lehetőségek közül választhat a kapcsolódás optimalizálásához:

* **ConnectionMode** – közvetlen kapcsolati mód használata a Azure Cosmos db szolgáltatás adatcsomópontjaihoz való kapcsolódáshoz. Az átjáró mód csak a logikai címek inicializálására és gyorsítótárazására, valamint a frissítések frissítésére használható. További részletekért tekintse meg a [kapcsolati módokat](sql-sdk-connection-modes.md) ismertető cikket.

* **ApplicationRegion** – ezzel a beállítással adható meg az az előnyben részesített földrajzi replikált régió, amely a Azure Cosmos db való interakcióra szolgál. További információt a [globális terjesztéssel](distribute-data-globally.md) foglalkozó cikkben talál.

* **ConsistencyLevel** – ezzel a beállítással lehet felülbírálni az alapértelmezett konzisztencia-szintet. További információt a [konzisztencia-szintek](consistency-levels.md) című cikkben talál.

* **BulkExecutionMode** – ezzel a beállítással tömeges műveleteket hajthat végre, ha a *AllowBulkExecution* tulajdonságot True értékre állítja. További információt a [tömeges importálásról](tutorial-sql-api-dotnet-bulk-import.md) szóló cikkben talál.

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>A tároló kiépítése

**DynamoDB**:

Az adatgyűjtés Amazon DynamoDB való tárolásához először létre kell hoznia a táblát. Ebben a folyamatban a séma, a kulcs típusa és az attribútumok meghatározása a következő kódban látható módon:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos db**:

Az Amazon DynamoDB-ban az olvasási számítási egységeket kell kiépíteni & írási számítási egységeket. Míg Azure Cosmos DB az átviteli sebességet a [kérelmek egysége (ru/s)](request-units.md)alapján határozza meg, amely bármilyen művelethez dinamikusan használható. Az adatgyűjtés adatbázis-> tároló--> tételként történik. Megadhatja az átviteli sebességet az adatbázis szintjén vagy a gyűjtemény szintjén, vagy mindkettőt.

Adatbázis létrehozása:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

A tároló létrehozása:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Az adatok betöltése

**DynamoDB**:

Az alábbi kód bemutatja, hogyan tölthetők be az Amazon DynamoDB. A moviesArray JSON-dokumentumból áll, majd meg kell ismételni a JSON-dokumentum betöltését az Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos db**:

Azure Cosmos DB a stream és az írás a következővel végezhető el: `moviesContainer.CreateItemStreamAsync()` . Ebben a példában azonban a JSON a *MovieModel* típusra lesz deszerializálva a Type-Casting funkció bemutatásához. A kód többszálas, amely a Azure Cosmos DB elosztott architektúráját fogja használni, és felgyorsítja a betöltést:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Dokumentum létrehozása

**DynamoDB**:

Az Amazon DynamoDB új dokumentumának írása nem biztonságos, a következő példa newItem használ:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos db**:

A Azure Cosmos DB adatmodell használatával adja meg a biztonságot. A "MovieModel" nevű adatmodellt használjuk:

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

Azure Cosmos DB newItem a MovieModel lesz:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Dokumentum olvasása

**DynamoDB**:

Az Amazon DynamoDB való olvasáshoz primitíveket kell meghatároznia:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos db**:

Azonban a Azure Cosmos DB a lekérdezés természetes (LINQ):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

A fenti példában szereplő dokumentumok gyűjteménye a következőket eredményezi:

- típus biztonságos
- Adjon meg egy természetes lekérdezési lehetőséget.

### <a name="update-an-item"></a>Elem módosítása

**DynamoDB**: a cikk frissítése az Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos db**:

A Azure Cosmos DBban a frissítés Upsert műveletként fog megjelenni, amely azt jelenti, hogy a dokumentum nem létezik:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Dokumentum törlése

**DynamoDB**:

Ha törölni szeretne egy tételt az Amazon DynamoDB-ban, újra kell esnie a primitívekre:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos db**:

Azure Cosmos DB a dokumentumot lekérheti és aszinkron módon törölheti:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Dokumentumok lekérdezése

**DynamoDB**:

Az Amazon DynamoDB az adatok lekérdezéséhez API-függvények szükségesek:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos db**:

Azure Cosmos DB a vetítést és a szűrést egy egyszerű SQL-lekérdezésen belül végezheti el:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

A tartományon belüli műveleteknél, például a "között", az Amazon DynamoDB-ban vizsgálatot kell végeznie:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

Azure Cosmos DB az SQL-lekérdezést és egy egysoros utasítást is használhat:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Tároló törlése

**DynamoDB**:

Az Amazon DynamoDB lévő tábla törléséhez a következőket adhatja meg:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos db**:

A gyűjtemény Azure Cosmos DBban való törléséhez a következőket adhatja meg:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Ezután törölje az adatbázist is, ha szüksége van rá:

```csharp
await cosmosDatabase.DeleteAsync();
```

Amint láthatja, a Azure Cosmos DB támogatja a természetes lekérdezéseket (SQL), a műveletek aszinkron és sokkal egyszerűbbek. Az összetett kódot egyszerűen áttelepítheti Azure Cosmos DBre, ami egyszerűbbé válik az áttelepítés után.

### <a name="next-steps"></a>Következő lépések

- Tudnivalók a [teljesítmény optimalizálásáról](performance-tips.md).
- Tudnivalók az [olvasási és írási optimalizálásról](key-value-store-cost.md)
- Tudnivalók a [Cosmos db figyeléséről](monitor-cosmos-db.md)


---
title: A HBase .NET SDK használata – Azure HDInsight
description: A HBase .NET SDK használatával táblákat hozhat létre és törölhet, valamint az adatolvasást és-írást.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 70c1dcc133f01cf72392f444192d977440c55cf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866624"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Az Apache HBase készült .NET SDK használata

Az [Apache HBase](apache-hbase-overview.md) két elsődleges lehetőséget biztosít az adatokkal való munkához: [Apache Hive lekérdezéseket, és meghívja a HBase REST API-](apache-hbase-tutorial-get-started-linux.md)ját. A `curl` parancs vagy egy hasonló segédprogram használatával közvetlenül is dolgozhat a REST API.

A C# és a .NET-alkalmazások esetében a [Microsoft HBASE Rest ügyféloldali kódtára a .net-hez](https://www.nuget.org/packages/Microsoft.HBase.Client/) biztosít egy HBase REST API.

## <a name="install-the-sdk"></a>Az SDK telepítése

A HBase .NET SDK NuGet-csomagként érhető el, amely a Visual Studio **NuGet Package Manager konzolról** telepíthető a következő paranccsal:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Új HBaseClient objektum példányának létrehozása

Ha az SDK-t szeretné használni, hozza létre az új `HBaseClient` objektumot, amely `ClusterCredentials` a `Uri` fürtből és a Hadoop felhasználónévből és jelszóból áll.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Cserélje le a CLUSTERNAME-t a HDInsight HBase, valamint a Felhasználónév és a jelszó helyére a fürt létrehozásához megadott Apache Hadoop hitelesítő adatokat. Az alapértelmezett Hadoop-Felhasználónév a **rendszergazda**.

## <a name="create-a-new-table"></a>Új tábla létrehozása

A HBase táblákban tárolja az adattárakat. A tábla egy *Rowkey*, egy elsődleges kulcsból és egy vagy több *oszlopból* álló csoportból áll. Az egyes táblákban lévő összes adathalmazt vízszintesen osztják el egy Rowkey-tartományból *régiókba*. Minden régió rendelkezik kezdési és befejezési kulccsal. Egy tábla egy vagy több régióval rendelkezhet. Ahogy a tábla adatai növekednek, a HBase a nagyméretű régiókat kisebb régiókra osztja fel. A régiók tárolása a *régió-kiszolgálókon* történik, ahol az egyik régió-kiszolgáló több régiót is tárolhat.

Az adattárolást fizikailag a *HFiles* tárolja. Egyetlen HFile egy tábla, egy régió és egy oszlop családja számára tartalmaz adattípust. A HFile lévő sorok tárolása a Rowkey szerint történik. Minden HFile rendelkezik egy *B + Tree* indextel a sorok gyors lekéréséhez.

Új tábla létrehozásához meg kell adnia a `TableSchema` és az oszlopot. A következő kód ellenőrzi, hogy a (z) "RestSDKTable" tábla már létezik-e – ha nem, a tábla jön létre.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Ez az új tábla kétoszlopos családokkal (T1 és T2) rendelkezik. Mivel az oszlopos családokat a különböző HFiles külön tárolják, érdemes külön oszlop-családot választani a gyakran lekérdezett adattípusokhoz. A következő [adatbeszúrási](#insert-data) példában az oszlopokat a rendszer hozzáadja a T1 oszlop családhoz.

## <a name="delete-a-table"></a>Tábla törlése

Tábla törlése:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Adat beszúrása

Az adat beszúrásához egyedi sort kell megadnia a sor azonosítójaként. Az összes adattal egy tömb tárolja `byte[]` . A következő kód meghatározza és hozzáadja a `title` , `director` és `release_date` oszlopokat a T1 oszlop családhoz, mivel ezek az oszlopok a leggyakrabban elérhetők. A `description` és az `tagline` oszlopok hozzáadódnak a T2 oszlop családhoz. Szükség szerint particionálhatja az adatait oszlopos családokba.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

A HBase a [Felhőbeli BigTable](https://cloud.google.com/bigtable/)valósítja meg, így az adatformátum a következő képhez hasonlóan néz ki:

:::image type="content" source="./media/apache-hbase-rest-sdk/hdinsight-table-roles.png" alt-text="Apache HBase-mintaadatok kimenete" border="true":::

## <a name="select-data"></a>Adatok kiválasztása

Ha egy HBase-táblából szeretne beolvasni egy adatforrást, adja át a tábla nevét és a sor kulcsát a `GetCellsAsync` metódusnak `CellSet` .

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

Ebben az esetben a kód csak az első egyező sort adja vissza, mivel egy egyedi kulcsnak csak egy sora lehet. A visszaadott értéket `string` a tömb formátumára változtatja `byte[]` . Az értéket más típusokra is konvertálhatja, például a film kiadási dátumának egészére:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Sorok ellenőrzése

A HBase `scan` egy vagy több sor lekérésére használja. Ez a példa több sort kér le 10 kötegben, és lekéri azokat az értékeket, amelyek kulcsai 25 és 35 között vannak. Az összes sor beolvasása után törölje a lapolvasót az erőforrások törléséhez.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a HDInsight egy Apache HBase-példájába](apache-hbase-tutorial-get-started-linux.md)
* Hozzon létre egy teljes körű alkalmazást az [Apache HBase valós idejű Twitter-hangulatának elemzésével](./apache-hbase-tutorial-get-started-linux.md)
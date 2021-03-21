---
title: SQL-alapú kapcsolatok modellezése importáláshoz és indexeléshez
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan modellezheti a viszonyítási adathalmazokat egy egyszerű eredményhalmaz alapján, az indexeléshez és a teljes szöveges kereséshez az Azure Cognitive Searchban.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924520"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>A kapcsolódó SQL-adattípusok modellezése importáláshoz és indexeléshez az Azure-ban Cognitive Search

Az Azure Cognitive Search egy egyszerű sorhalmazt fogad el bemenetként az [indexelési folyamathoz](search-what-is-an-index.md). Ha a forrásadatok egy SQL Server relációs adatbázisban lévő összekapcsolt táblákból származnak, ez a cikk az eredményhalmaz összeállítását és a szülő-gyermek kapcsolat Azure Cognitive Search indexben való modellezését mutatja be.

Illusztrációként egy feltételezett Hotels-adatbázisra fogunk hivatkozni, a [bemutató](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)alapján. Tegyük fel, hogy az adatbázis egy 50-es Hotels $ Table-vel és egy, a különböző típusú, díjszabási és kényelmi lehetőséggel rendelkező szobával rendelkezik, és összesen 750 szobát tartalmaz. A táblák között egy-a-többhöz kapcsolat van. A megközelítésben a nézet megadja a lekérdezést, amely visszaadja a 50 sort, egy sort a szálloda egy sorában, és az egyes sorokba ágyazza be a kapcsolódó helyiségek részleteit.

   ![Táblák és nézetek a Hotels adatbázisban](media/index-sql-relational-data/hotels-database-tables-view.png "Táblák és nézetek a Hotels adatbázisban")


## <a name="the-problem-of-denormalized-data"></a>A denormalizált adatmennyiség problémája

Az egy-a-többhöz kapcsolattal kapcsolatos egyik kihívás az, hogy az összekapcsolt táblákra épülő szabványos lekérdezések visszaadják a denormalizált adatok, amelyek nem működnek megfelelően az Azure Cognitive Search-forgatókönyvekben. Vegye figyelembe a következő példát, amely összekapcsolja a szállodákat és a szobákat.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
A lekérdezés eredményei visszaadják az összes szállodai mezőt, majd az összes hely mező értékét, az előzetes szállodai információk pedig minden egyes szobatípus esetében megismétlődnek.

   ![Denormalizált adat, redundáns szállodai adat a helyiség mezőinek hozzáadásakor](media/index-sql-relational-data/denormalize-data-query.png "Denormalizált adat, redundáns szállodai adat a helyiség mezőinek hozzáadásakor")


Habár ez a lekérdezés a felületen sikeres lesz (amely az összes adatát egy sík sorban adja meg), nem sikerül a megfelelő dokumentum-struktúrát kézbesíteni a várt keresési élményhez. Az indexelés során az Azure Cognitive Search egy keresési dokumentumot fog létrehozni az egyes betöltött sorokhoz. Ha a keresési dokumentumok a fenti eredményekhez hasonlóan látszanak, akkor a Twin Dome-beli hotelhez csak hét különálló dokumentumot kellene megtekinteni. A "Hotels in Florida" nevű lekérdezés hét eredményt ad vissza csupán a Twin Dome-szálloda számára, és a többi releváns szállodát kikényszeríti a keresési eredményekbe.

Ahhoz, hogy egy adott dokumentumhoz tartozó várható élményt igénybe vegye, meg kell adnia egy sorhalmazt a megfelelő részletességgel, de a teljes információval. Szerencsére ezt könnyedén elvégezheti a jelen cikkben ismertetett módszerek bevezetésével.

## <a name="define-a-query-that-returns-embedded-json"></a>Beágyazott JSON-t visszaadó lekérdezés definiálása

A várt keresési élmény biztosításához az adatkészletnek egy sorból kell állnia minden egyes keresési dokumentumhoz az Azure Cognitive Searchban. A példánkban egy sort szeretnénk használni az egyes hotelekhez, de azt is szeretnénk, hogy a felhasználók más, a helyiségekkel kapcsolatos mezőkre is képesek legyenek keresni, például az éjszakai díjszabás, a méret és az ágyak száma, illetve a strand nézete, amelyek mindegyike egy szoba részletének részét képezi.

A megoldás a helyiség részleteit beágyazott JSON-ként rögzíti, majd beszúrja a JSON-struktúrát egy nézet egyik mezőjébe, ahogy azt a második lépésben is mutatja. 

1. Tegyük fel, hogy két, a 50-es és a 750-es, valamint a HotelID mezőhöz csatlakoztatott tábla, a Hotels $ és a Rooms $ érték szerepel. Ezek a táblázatok az 50-es és a 750-es kapcsolódó szobákat tartalmazzák.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Hozzon létre egy nézetet, amely a fölérendelt tábla összes mezőjéből áll ( `SELECT * from dbo.Hotels$` ), egy új *szobák* mező hozzáadásával, amely egy beágyazott lekérdezés kimenetét tartalmazza. A **for JSON Auto** záradék a `SELECT * from dbo.Rooms$` kimenet JSON-ként való felépítéséhez. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   A következő képernyőképen az eredményül kapott nézet jelenik meg, a *szobák* nvarchar mezője pedig alul található. A *szobák* mező csak a HotelSzobak nézetben létezik.

   ![HotelSzobak nézet](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms nézet")

1. `SELECT * FROM dbo.HotelRooms`A sor beolvasásához futtassa a parancsot. Ez a lekérdezés 50 sort ad vissza, amely egy-egy helyen, a hozzá tartozó információval pedig JSON-gyűjteményként van társítva. 

   ![Sorhalmaz a HotelSzobak nézetből](media/index-sql-relational-data/hotelrooms-rowset.png "Sorhalmaz a HotelSzobak nézetből")

Ez a sorhalmaz most már készen áll az Azure Cognitive Searchba való importálásra.

> [!NOTE]
> Ez a megközelítés azt feltételezi, hogy a beágyazott JSON a [SQL Server oszlopainak maximális mérete](/sql/sql-server/maximum-capacity-specifications-for-sql-server)alá esik. 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Egy-a-többhöz kapcsolat "több" oldalához használjon összetett gyűjteményt

Az Azure Cognitive Search oldalon hozzon létre egy index sémát, amely az egy-a-többhöz kapcsolatot modellezi a beágyazott JSON használatával. Az előző szakaszban létrehozott eredményhalmaz általában az alább megadott index-sémának felel meg (a kis-és nagyméretű mezőket kivágták).

A következő példa hasonló az [összetett adattípusok modellezésének](search-howto-complex-data-types.md#creating-complex-fields)példához. A *termek* struktúrája, amely a cikk középpontjában szerepel, a *Hotels* nevű index mezőinek gyűjteménye. Ebben a példában egy összetett típust is mutatunk a *címekhez*, amely eltér a *szobák* rögzített készletének, a gyűjteményben engedélyezett többszörös, tetszőleges számú elemtől.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Az előző eredményhalmaz és a fenti index séma miatt a sikeres indexelési művelethez minden szükséges összetevővel rendelkezik. Az összeolvasztott adatkészlet megfelel az indexelési követelményeknek, ugyanakkor megőrzi a részletes információkat. Az Azure Cognitive Search indexében a keresési eredmények könnyen bekerülnek a szállodai alapú entitásokra, miközben megőrizheti az egyes szobák és attribútumaik kontextusát.

## <a name="next-steps"></a>Következő lépések

Az index létrehozásához és betöltéséhez használja az [adatimportálás varázsló](search-import-data-portal.md) segítségével a saját adatkészletét. A varázsló észleli a beágyazott JSON-gyűjteményt, például a *szobákban* találhatót, és egy összetett típusú gyűjteményt tartalmazó index sémát következtet ki. 

  ![Index következtetni az adatimportálás varázsló segítségével](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Index következtetni az adatimportálás varázsló segítségével")

Az adatok importálása varázsló alapvető lépéseinek megismeréséhez próbálkozzon a következő rövid útmutatóval.

> [!div class="nextstepaction"]
> [Gyors útmutató: keresési index létrehozása Azure Portal használatával](search-get-started-portal.md)
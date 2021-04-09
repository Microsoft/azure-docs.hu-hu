---
title: JSON-adatok használata
description: A Azure SQL Database és az Azure SQL felügyelt példánya lehetővé teszi, hogy JavaScript Object Notation (JSON) formátumban elemezze, lekérdezze és formázza az adatelemzést.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782800"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Ismerkedés a JSON-funkciókkal Azure SQL Database és az Azure SQL felügyelt példányában
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database és az Azure SQL felügyelt példánya lehetővé teszi a JavaScript Object Notation [(JSON)](https://www.json.org/) formátumban ábrázolt adatelemzést és lekérdezéseket, és a rokoni adatait JSON-szövegként exportálja. A következő JSON-forgatókönyvek érhetők el:

- A (z) záradék használatával [formázza a rokoni adattípusokat a JSON formátumban](#formatting-relational-data-in-json-format) `FOR JSON` .
- [JSON-adatok használata](#working-with-json-data)
- [JSON-adatok lekérdezése](#querying-json-data) JSON skaláris függvények használatával.
- [A JSON átalakítása táblázatos formátumban](#transforming-json-into-tabular-format) a `OPENJSON` függvény használatával.

## <a name="formatting-relational-data-in-json-format"></a>A kapcsolatok adatai JSON formátumban formázhatók

Ha olyan webszolgáltatással rendelkezik, amely adatokat fogad az adatbázis-rétegből, és JSON formátumú választ ad, vagy ügyféloldali JavaScript-keretrendszerek vagy kódtárak, amelyek JSON-ként formázott adatokat fogadnak, akkor az adatbázis tartalmát JSON-ként közvetlenül egy SQL-lekérdezésben formázhatja. Többé nem kell írnia az eredményeket a Azure SQL Database vagy az Azure SQL felügyelt példányának JSON-ként való formázásához, vagy tartalmaznia kell néhány JSON-szerializálási függvénytárat a táblázatos lekérdezés eredményeinek átalakításához, majd az objektumok JSON formátumba történő szerializálásához. Ehelyett használhatja a FOR JSON záradékot az SQL-lekérdezés eredményeinek JSON-ként való formázásához, és közvetlenül az alkalmazásban való felhasználásához.

A következő példában a Sales. Customer táblából származó sorok JSON-ként vannak formázva a FOR JSON záradék használatával:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A FOR JSON PATH záradék JSON-szövegként formázza a lekérdezés eredményét. Az oszlopnevek kulcsként használatosak, míg a cella értékei JSON-értékként jönnek létre:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Az eredményhalmaz JSON-tömbként van formázva, ahol az egyes sorok külön JSON-objektumként vannak formázva.

Az ELÉRÉSi út azt jelzi, hogy a JSON-eredmény kimeneti formátumát az oszlopbeli aliasok pont szerinti jelöléssel szabhatja testre. A következő lekérdezés módosítja a "Customername (" kulcs nevét a kimenet JSON-formátumában, és a telefonos és a faxszámot a "Contact" alobjektumon helyezi el:

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

A lekérdezés kimenete a következőképpen néz ki:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Ebben a példában egyetlen JSON-objektumot adott vissza egy tömb helyett a [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) beállítás megadásával. Ezt a lehetőséget akkor használhatja, ha tudja, hogy a lekérdezés eredményeképpen egyetlen objektumot ad vissza.

A FOR JSON záradék fő értéke az, hogy lehetővé teszi összetett hierarchikus adatok visszaadását az adatbázisból beágyazott JSON-objektumokként vagy tömbökként formázva. Az alábbi példa azt mutatja be, hogyan lehet a tábla sorait a következő `Orders` `Customer` beágyazott tömbhöz tartozó sorokba foglalni `Orders` :

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Ahelyett, hogy külön lekérdezéseket küldene az ügyféladatok beszerzésére, majd lekéri a kapcsolódó megrendelések listáját, egyetlen lekérdezéssel lekérheti az összes szükséges adatát, ahogy az a következő példában látható:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>JSON-adatok használata

Ha nem rendelkezik szigorúan strukturált adattal, ha összetett alobjektumokkal, tömbökkel vagy hierarchikus adatokkal rendelkezik, vagy ha az adatszerkezetek idővel fejlődnek, a JSON formátuma segítségével bármilyen összetett adatstruktúrát képviselheti.

A JSON olyan szöveges formátum, amely a Azure SQL Database és az Azure SQL felügyelt példányaiban bármilyen más karakterlánc-típushoz használható. A JSON-adatszolgáltatásokat szabványos NVARCHAR is elküldheti vagy tárolhatja:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Az ebben a példában használt JSON-adatai a NVARCHAR (MAX) típussal jelennek meg. A JSON beilleszthető ebbe a táblába, vagy a tárolt eljárás argumentuma a standard Transact-SQL szintaxissal lehet megadni, ahogy az alábbi példában is látható:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

A Azure SQL Database és az Azure SQL felügyelt példányain található karakterlánc-adattal rendelkező ügyféloldali nyelvek vagy könyvtárak JSON-adattal is működni fognak. A JSON bármely olyan táblában tárolható, amely támogatja a NVARCHAR típusát, például egy memóriára optimalizált táblát vagy egy rendszerverzióval ellátott táblát. A JSON nem vezet be korlátozásokat az ügyféloldali kódban vagy az adatbázis rétegében.

## <a name="querying-json-data"></a>JSON-adatbázis lekérdezése

Ha az Azure SQL-táblákban JSON-ként formázott adatok vannak, akkor a JSON-függvények bármely SQL-lekérdezésben használhatják ezeket az adatok használatát.

A Azure SQL Database és az Azure SQL felügyelt példányaiban elérhető JSON-függvények lehetővé teszik a JSON-ként formázott adatok kezelését bármilyen más SQL-adattípus esetén. A JSON-szövegből egyszerűen kinyerheti az értékeket, és a JSON-adatok bármilyen lekérdezésben használhatók:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A JSON_VALUE függvény Kinyer egy értéket az adat oszlopban tárolt JSON-szövegből. Ez a függvény egy JavaScript-hez hasonló útvonalat használ a kinyerni kívánt JSON-szövegben lévő értékre való hivatkozáshoz. A kinyert érték az SQL-lekérdezés bármely részén használható.

A JSON_QUERY függvény hasonló a JSON_VALUEhoz. A JSON_VALUEtól eltérően ez a függvény összetett alobjektumokat, például a JSON-szövegbe helyezett tömböket vagy objektumokat is kibontja.

A JSON_MODIFY függvény segítségével megadhatja a JSON-szövegben szereplő érték elérési útját, valamint egy új értéket, amely felül fogja írni a régit. Így egyszerűen frissítheti a JSON-szöveget a teljes struktúra újraelemzése nélkül.

Mivel a JSON szabványos szövegben van tárolva, nincs garancia arra, hogy a szöveges oszlopokban tárolt értékek megfelelően vannak formázva. Ellenőrizze, hogy a JSON oszlopban tárolt szöveg megfelelően formázott-e a szabványos Azure SQL Database ellenőrzési megkötések és a ISJSON függvény használatával:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Ha a bemeneti szöveg megfelelően formázott JSON, a ISJSON függvény az 1 értéket adja vissza. Ez a korlátozás ellenőrzi, hogy az új szöveges érték nem formázott JSON-e a JSON-oszlop összes INSERT vagy Update utasításakor.

## <a name="transforming-json-into-tabular-format"></a>A JSON átalakítása táblázatos formátumba

A Azure SQL Database és az Azure SQL felügyelt példánya lehetővé teszi a JSON-gyűjtemények táblázatos formátumban való átalakítását, illetve a JSON-adatokat betöltését

A OPENJSON UTASÍTÁSSAL egy Table-Value függvény, amely a JSON-szöveg elemzésére szolgál, megkeresi a JSON-objektumok tömbjét, megismétli a tömb elemeit, és egy sort ad vissza a tömb egyes elemeinek kimeneti eredményében.

![JSON táblázatos](./media/json-features/image_2.png)

A fenti példában megadhatjuk, hol keresse meg a megnyitni kívánt JSON-tömböt ($. A megrendelések elérési útja), hogy milyen oszlopokat kell visszaadni eredményként, és hol találhatók a cellákként visszaadott JSON-értékek.

A változóban található JSON-tömb átalakítható @orders sorokba, elemezheti ezt az eredményhalmazt, vagy sorokat szúrhat be egy standard táblába:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

A JSON-tömbként formázott és a tárolt eljárás paraméterként megadott gyűjteménye elemezhető, és beszúrható az Orders (megrendelések) táblába.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan integrálhatja a JSON-t az alkalmazásba, tekintse meg ezeket az erőforrásokat:

Ha többet szeretne megtudni a JSON alkalmazásba való integrálásához szükséges különböző forgatókönyvekről, tekintse meg a [Channel 9 videójában](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) található bemutatókat, vagy keressen olyan forgatókönyvet, amely megfelel a [JSON-blogbejegyzésekben](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases)használt használati esetnek.
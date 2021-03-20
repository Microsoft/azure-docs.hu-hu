---
title: Az Azure Cosmos DB Cassandra API-ja által támogatott Apache Cassandra-funkciók
description: További tudnivalók az Apache Cassandra-funkciók támogatásáról az Azure Cosmos DB Cassandra API-ban
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f48935edd5746b0f3f3440e249024cbe2901317b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374371"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API-ja által támogatott Apache Cassandra-funkciók 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A Azure Cosmos DB Cassandra API a CQL bináris protokoll v4 [Wire Protocol](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) -kompatibilis nyílt forráskódú Cassandra [-ügyfélszoftverek használatával](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)kommunikálhat. 

Az Azure Cosmos DB Cassandra API használatával kihasználhatja az Apache Cassandra API-k előnyeit, valamint az Azure Cosmos DB által biztosított vállalati képességeket. A vállalati képességek magukban foglalják a [globális terjesztést](distribute-data-globally.md), az [automatikus horizontális felskálázás particionálását](cassandra-partitioning.md), rendelkezésre állási és késési garanciát, az inaktív adatok titkosítását, a biztonsági mentéseket és még sok más.

## <a name="cassandra-protocol"></a>Cassandra protokoll 

A Azure Cosmos DB Cassandra API kompatibilis a Cassandra Query Language (CQL) v 3.11 API-val (a 2. x verzióval visszamenőlegesen kompatibilis). A támogatott CQL-parancsok, -eszközök, -korlátozások és -kivételeket alább láthatók. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia az Azure Cosmos DB Cassandra API-hoz.

## <a name="cassandra-driver"></a>Cassandra-illesztőprogram

Az Azure Cosmos DB Cassandra API a következő Cassandra-illesztőprogramverziókat támogatja:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 

## <a name="cql-data-types"></a>CQL-adattípusok 

Az Azure Cosmos DB Cassandra API a következő CQL-adattípusokat támogatja:

|Típus  |Támogatott |
|---------|---------|
| ascii  | Yes |
| bigint  | Yes |
| blob  | Yes |
| boolean  | Yes |
| számláló  | Yes |
| dátum  | Yes |
| tizedes tört  | Yes |
| double  | Yes |
| float  | Yes |
| kimerevítve  | Yes |
| inet  | Yes |
| int  | Yes |
| list  | Yes |
| halmaz  | Yes |
| smallint  | Yes |
| szöveg  | Yes |
| time  | Yes |
| időbélyeg  | Yes |
| timeuuid  | Yes |
| tinyint  | Yes |
| rekord  | Yes |
| uuid  | Yes |
| varchar  | Yes |
| varint  | Yes |
| rekordok | Yes | 
| udts  | Yes |
| map | Yes |

Az adattípusok deklarációjában a statikus érték is támogatott.

## <a name="cql-functions"></a>CQL-függvények

Az Azure Cosmos DB Cassandra API a következő CQL-függvényeket támogatja:

|Parancs  |Támogatott |
|---------|---------|
| Jogkivonat | Yes |
| TTL * * * | Yes |
| writetime *** | Yes |
| Cast * * | Yes |

> [!NOTE] 
> \* Cassandra API a tokent kivetítési/választóként támogatja, és csak a tokent (PK) engedélyezi egy WHERE záradék bal oldalán. Például `WHERE token(pk) > 1024` támogatott, de `WHERE token(pk) > token(100)` **nem** támogatott.  
> \*\* A `cast()` függvény nem ágyazható be Cassandra APIba. Például `SELECT cast(count as double) FROM myTable` támogatott, de `SELECT avg(cast(count as double)) FROM myTable` **nem** támogatott.    
> \*\*\* A beállítással megadott egyéni időbélyegek és TTL-érték `USING` egy sorban (és nem cella) van alkalmazva.



Összesítő függvények:

|Parancs  |Támogatott |
|---------|---------|
| AVG | Yes |
| count | Yes |
| p | Yes |
| max. | Yes |
| Sum | Yes |

> [!NOTE]
> Az összesítő függvények normál oszlopokon működnek, de a fürtözési oszlopok összesítései **nem** támogatottak.


BLOB-átalakítási függvények:
 
|Parancs  |Támogatott |
|---------|---------|
| typeAsBlob(value)   | Yes |
| blobAsType(value) | Yes |


UUID és timeuuid függvények:
 
|Parancs  |Támogatott |
|---------|---------|
| dateOf()  | Yes |
| now()  | Yes |
| minTimeuuid()  | Yes |
| unixTimestampOf()  | Yes |
| toDate(timeuuid)  | Yes |
| toTimestamp(timeuuid)  | Yes |
| toUnixTimestamp(timeuuid)  | Yes |
| toDate(timestamp)  | Yes |
| toUnixTimestamp(timestamp)  | Yes |
| toTimestamp(date)  | Yes |
| toUnixTimestamp(date) | Yes |


  
## <a name="cql-commands"></a>CQL-parancsok

Az Azure Cosmos DB a következő adatbázisparancsokat támogatja a Cassandra API-fiókok esetében.

|Parancs  |Támogatott |
|---------|---------|
| SZŰRÉS ENGEDÉLYEZÉSE | Yes |
| LEMEZTERÜLET MÓDOSÍTÁSA | N/A (Pásti szolgáltatás, belső replikáció felügyelt)|
| MÓDOSÍTHATÓ ANYAGÚ NÉZET | No |
| SZEREPKÖR MÓDOSÍTÁSA | No |
| ALTER TABLE | Yes |
| MÓDOSÍTÁS TÍPUSA | No |
| FELHASZNÁLÓ MÓDOSÍTÁSA | No |
| BATCH | Igen (csak nem naplózott köteg)|
| KOMPAKT TÁROLÓ | N/A (Pásti szolgáltatás) |
| ÖSSZESÍTÉS LÉTREHOZÁSA | No | 
| EGYÉNI INDEX LÉTREHOZÁSA (SASI) | No |
| CREATE INDEX | Igen (az [index nevének megadása](cassandra-secondary-index.md)és a fürtözési kulcsok indexelése nélkül, vagy a teljes fagyasztott gyűjtemény nem támogatott) |
| CREATE FUNCTION | No |
| SZÓKÖZ létrehozása (replikációs beállítások figyelmen kívül hagyva) | Yes |
| ANYAGELSZÁMOLÁSÚ NÉZET LÉTREHOZÁSA | No |
| CREATE TABLE | Yes |
| TRIGGER LÉTREHOZÁSA | No |
| LÉTREHOZÁS TÍPUSA | Yes |
| SZEREPKÖR LÉTREHOZÁSA | No |
| FELHASZNÁLÓ létrehozása (natív Apache Cassandra-ban elavult) | No |
| DELETE | Yes |
| DISTINCT | No |
| ÖSSZESÍTÉS ELDOBÁSA | No |
| DROP FUNCTION | No |
| DROP INDEX | Yes |
| SZÓKÖZ ELDOBÁSA | Yes |
| ANYAGOS NÉZET ELDOBÁSA | No |
| SZEREPKÖR ELDOBÁSA | No |
| DROP TABLE | Yes |
| TRIGGER ELDOBÁSA | No | 
| DROP TYPE | Yes |
| FELHASZNÁLÓ eldobása (elavult, natív Apache Cassandra) | No |
| GRANT | No |
| INSERT | Yes |
| ENGEDÉLYEK LISTÁZÁSA | No |
| SZEREPKÖRÖK LISTÁZÁSA | No |
| FELHASZNÁLÓK LISTÁZÁSa (a natív Apache Cassandra-ban elavult) | No |
| REVOKE | No |
| SELECT | Yes |
| UPDATE | Yes |
| TRUNCATE | No |
| USE | Yes |

## <a name="lightweight-transactions-lwt"></a>Könnyűsúlyú tranzakciók (VVAST)

| Összetevő  |Támogatott |
|---------|---------|
| TÖRLÉS, HA LÉTEZIK | Yes |
| TÖRLÉSi feltételek | No |
| HA NEM LÉTEZIK A BESZÚRÁS | Yes |
| FRISSÍTÉS, HA LÉTEZIK | Yes |
| A FRISSÍTÉS NEM LÉTEZIK | Yes |
| FRISSÍTÉSI feltételek | No |

## <a name="cql-shell-commands"></a>CQL-rendszerhéj parancsai

Az Azure Cosmos DB a következő adatbázisparancsokat támogatja a Cassandra API-fiókok esetében.

|Parancs  |Támogatott |
|---------|---------|
| RÖGZÍTÉSE | Yes |
| EGYÉRTELMŰ | Yes |
| KONZISZTENCIA | N/A |
| MÁSOLJA | No |
| ISMERTETIK | Yes |
| cqlshExpand | No |
| KILÉPÉSI | Yes |
| BEJELENTKEZÉSI | N/A (a CQL függvény `USER` nem támogatott, ezért `LOGIN` redundáns) |
| LAPOZÓFÁJL | Yes |
| SOROS KONZISZTENCIA * | N/A |
| MEGJELENÍTÉSE | Yes |
| FORRÁS | Yes |
| NYOMKÖVETÉS | N/A (Cassandra API Azure Cosmos DB biztonsági mentést végez – [diagnosztikai naplózás](cosmosdb-monitor-resource-logs.md) használata a hibaelhárításhoz) |

> [!NOTE] 
> \* A konzisztencia a Azure Cosmos DBban eltérően működik, további információért lásd [itt](cassandra-consistency.md) .  


## <a name="json-support"></a>JSON-támogatás
|Parancs  |Támogatott |
|---------|---------|
| JSON KIVÁLASZTÁSA | Yes |
| JSON BESZÚRÁSA | Yes |
| fromJson() | No |
| toJson() | No |


## <a name="cassandra-api-limits"></a>A Cassandra API korlátai

Az Azure Cosmos DB Cassandra API nem rendelkezik semmilyen korláttal a táblában tárolt adatok méretére vonatkozóan. Több száz terabájtnyi vagy petabájtnyi adat tárolható, ha biztosítva van a partíciókulcs korlátainak betartása. Hasonlóképpen, minden entitás vagy sor megfelelője nem korlátozza az oszlopok számát. Az entitás teljes mérete azonban nem haladhatja meg a 2 MB-ot. Az adat/partíciós kulcs nem haladhatja meg a 20 GB-ot az összes többi API-ban.

## <a name="tools"></a>Eszközök 

Az Azure Cosmos DB Cassandra API egy felügyelt szolgáltatási platform. A fürt kezeléséhez nincs szükség semmilyen felügyeleti többletterhelésre vagy segédprogramra, mint a Garbage Collector, Java Virtual Machine (JVM), sem csomóponti eszközre. Támogatja az olyan eszközöket, mint a cqlsh, amely bináris CQLv4-kompatibilitást használ. 

* A fiók kezeléséhez a Azure Portal adatkezelője, mérőszámai, naplózási diagnosztikája, PowerShell és CLI más támogatott mechanizmusok.

## <a name="hosted-cql-shell-preview"></a>Üzemeltetett CQL-rendszerhéj (előzetes verzió)

Egy üzemeltetett natív Cassandra shellt (CQLSH v 5.0.1) közvetlenül a [Azure Portal](data-explorer.md) vagy a [Azure Cosmos db Explorer](https://cosmos.azure.com/)adatkezelő is megnyithat. A CQL-rendszerhéj engedélyezése előtt engedélyeznie kell [a jegyzetfüzetek](enable-notebooks.md) szolgáltatást a fiókjában (ha még nincs engedélyezve, akkor a rendszer rákérdez a gombra `Open Cassandra Shell` ). Tekintse meg a [Azure Cosmos db-fiókok notebookok használatának engedélyezése](enable-notebooks.md#supported-regions) a támogatott Azure-régiókhoz című cikket.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="CQLSH megnyitása":::

A helyi gépre telepített CQLSH használatával a Azure Cosmos DB Cassandra API is csatlakozhat. Az Apache Cassandra 3.1.1-es verziójában a környezeti változók beállításával működik a doboz. A következő részekben a CQLSH-t használó Windowson vagy Linuxon Cassandra API telepítéséhez, konfigurálásához és a Azure Cosmos DBhoz való kapcsolódáshoz szükséges utasítások találhatók.

> [!NOTE]
> A Azure Cosmos DB Cassandra API létesített kapcsolatai nem fognak működni a CQLSH DataStax Enterprise (DSE) verziójával. A Cassandra APIhoz való csatlakozáskor ügyeljen arra, hogy csak a nyílt forráskódú Apache Cassandra-verziókat használja a CQLSH. 

**Windows**

Ha Windows rendszert használ, javasoljuk, hogy engedélyezze a [Linux rendszerhez készült Windows-fájlrendszert](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Ezután követheti az alábbi Linux-parancsokat.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

A CQL v4-kompatibilis SDK-n keresztül végrehajtott összes szifilisz-művelet további információkat ad vissza a hibákról és a kért egységekről. A TÖRLÉSi és frissítési parancsokat a kiépített átviteli sebesség leghatékonyabb kihasználásának biztosítása érdekében figyelembe kell venni az erőforrás-szabályozással.

* Ügyeljen arra, hogy a gc_grace_seconds értékének nullának kell lennie, ha meg van adva.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Konzisztencialeképezés 

Az Azure Cosmos DB Cassandra API konzisztenciaválasztási lehetőséget kínál az olvasási műveletekhez.  [Itt](./cassandra-consistency.md#mapping-consistency-levels)részletesen ismertetjük a konzisztencia-hozzárendelést.

## <a name="permission-and-role-management"></a>Engedély- és szerepkörkezelés

Azure Cosmos DB támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) a kiépítés, a kulcsok elforgatása, a metrikák, valamint az írási és olvasási és csak olvasható jelszavak/kulcsok megtekintésére, amelyek a [Azure Portal](https://portal.azure.com)keresztül szerezhetők be. A Azure Cosmos DB nem támogatja a szifilisz-tevékenységek szerepkörét.

## <a name="keyspace-and-table-options"></a>A térköz és a tábla beállításai

Jelenleg a rendszer figyelmen kívül hagyja a régió neve, osztály, replication_factor és Datacenter beállításait a "Create Space" parancsban. A rendszer az alapul szolgáló Azure Cosmos DB [globális terjesztési](global-dist-under-the-hood.md) replikációs módszerét használja a régiók hozzáadásához. Ha szüksége van a régiók közötti adatmennyiségre, a PowerShell, a CLI vagy a portál használatával engedélyezheti a fiók szintjén a további tudnivalókat lásd: [régiók hozzáadása](how-to-manage-database-account.md#addremove-regions-from-your-database-account) cikk. Durable_writes nem lehet letiltani, mert Azure Cosmos DB gondoskodik az összes írás tartós állapotáról. Azure Cosmos DB minden régióban replikálja az adathalmazt, amely négy replikából áll, és ez a replikakészlet- [konfiguráció](global-dist-under-the-hood.md) nem módosítható.
 
A rendszer a tábla létrehozásakor figyelmen kívül hagyja az összes beállítást, kivéve a gc_grace_secondst, amelyet nullára kell beállítani.
A szóköz és a tábla "cosmosdb_provisioned_throughput" nevű extra lehetőséggel rendelkezik, amelynek minimális értéke 400 RU/s. A térköz átviteli sebessége lehetővé teszi a több tábla közötti adatforgalom megosztását, és olyan helyzetekben hasznos, amikor az összes tábla nem használja a kiépített átviteli sebességet. Az ALTER TABLE parancs lehetővé teszi a kiépített átviteli sebesség módosítását a régiók között. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Másodlagos index
A Cassandra API támogatja a másodlagos indexeket minden adattípuson, kivéve a fagyasztott gyűjtemények típusait, a decimális és a Variant típust. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>A Cassandra csatlakozási újrapróbálkozásra vonatkozó szabályzatának használata

A Azure Cosmos DB erőforrás-szabályozású rendszer. Ez azt jelenti, hogy egy adott másodpercben bizonyos számú műveletet végrehajthat a műveletek által felhasznált kérelmek egységei alapján. Ha egy alkalmazás túllépi ezt a korlátot egy adott másodpercben, a kérések száma korlátozott, és a rendszer a kivételeket is eldönti. A Azure Cosmos DB Cassandra API lefordítja ezeket a kivételeket a Cassandra Native protokollon túlterhelt hibák esetén. Annak biztosítása érdekében, hogy az alkalmazás képes legyen feltartóztatni és újrapróbálkozni a kérelmeket a díjszabás korlátozása esetén, a [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) és a [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) -bővítmények is elérhetők. Lásd még: Java-kódrészletek a [3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) . és [4-es verziójú](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) Datastax-illesztőprogramok esetén, amikor a Cassandra APIhoz csatlakozik Azure Cosmos DBban. Ha más SDK-kat használ a Azure Cosmos DB Cassandra APIhoz való hozzáféréshez, hozzon létre egy kapcsolati szabályzatot, amely újrapróbálkozik ezekkel a kivételekkel.

## <a name="next-steps"></a>Következő lépések

- Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával

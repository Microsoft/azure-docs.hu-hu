---
title: Az Azure Cosmos DB Cassandra API bemutatása
description: Megtudhatja, hogyan használhatja a Azure Cosmos DBt a meglévő alkalmazások átemeléséhez, valamint új alkalmazások létrehozásához a Cassandra-illesztőprogramok és a CQL használatával
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 11/25/2020
ms.openlocfilehash: 2a778c2e9a44bda148fd40112776858943cffe4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96188615"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API bemutatása
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Az Azure Cosmos DB Cassandra API az [Apache Cassandrára](https://cassandra.apache.org) írt alkalmazások adattáraként használható. Ez azt jelenti, hogy a meglévő CQLv4-kompatibilis [Apache-illesztőprogramok](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) segítségével a Cassandrára írt alkalmazások mostantól kommunikálhatnak az Azure Cosmos DB Cassandra API-val. Sok esetben átválthat az Apache Cassandra használatával Azure Cosmos DB Cassandra API használatára, csak a kapcsolódási karakterláncok módosításával. 

A Cassandra API lehetővé teszi a Azure Cosmos DB tárolt adataival való interakciót a Cassandra Query Language (CQL), a Cassandra-alapú eszközök (például a cqlsh) és a már ismert Cassandra Client-illesztőprogramok használatával.

> [!NOTE]
> A [kiszolgáló nélküli kapacitás mód](serverless.md) mostantól elérhető a Azure Cosmos db Cassandra API.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Milyen előnyökkel jár az Azure Cosmos DB az Apache Cassandra API-n keresztüli használata?

**Nincs szükség üzemeltetésre**: Teljesen felügyelt felhőszolgáltatásként az Azure Cosmos DB Cassandra API szükségtelenné teszi az operációs rendszerben, a JVM-ben és a yaml-fájlokban lévő számtalan beállítás és azok interakciójának felügyeletét és monitorozását. Az Azure Cosmos DB lehetővé teszi a teljesítmény, a késés, a tárolás és a rendelkezésre állás monitorozását, és konfigurálható riasztásokat is biztosít.

**Nyílt forráskódú szabvány**: annak ellenére, hogy egy teljes körűen felügyelt szolgáltatás, Cassandra API továbbra is támogatja a natív [Apache Cassandra Wire protokoll](cassandra-support.md)nagy felületét, amely lehetővé teszi, hogy a széles körben használt és a Felhőbeli agnosztikus nyílt forráskódú szabványt használó alkalmazásokat építsen ki.

**Teljesítménykezelés**: Az Azure Cosmos DB a szolgáltatói szerződés által garantált alacsony késésű olvasási és írási teljesítményt biztosít az esetek 99%-ában. A felhasználóknak nem kell üzemeltetési többletterheléstől tartaniuk a nagy teljesítmény és a kis késleltetésű olvasás és írás biztosítása érdekében. Vagyis nem kell kézileg tömörítést ütemezniük, törlésjelzőket kezelniük, bloom szűrőket és replikákat beállítaniuk. Az Azure Cosmos DB használatával ezekkel a problémákkal nem kell foglalkoznia, ezek helyett nyugodtan koncentrálhat az alkalmazáslogikára.

**A meglévő kódok és eszközök használata**: Az Azure Cosmos DB protokollszintű kompatibilitást biztosít a meglévő Cassandra SDK-kkal és eszközökkel. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal az Azure Cosmos DB Cassandra API-val is használhatja.

**Adatátviteli és tárolási rugalmasság**: Azure Cosmos db az összes régióban biztosítja az átviteli sebességet, és Azure Portal, POWERSHELL vagy CLI műveletekkel méretezheti a kiosztott átviteli sebességet. [Rugalmasan méretezheti](manage-scale-cassandra.md) a táblák tárolási és átviteli sebességét a kiszámítható teljesítménnyel.

**Globális terjesztés és rendelkezésre állás**: Az Azure Cosmos DB lehetővé teszi az adatok összes Azure-régióba történő globális terjesztését, és helyi kiszolgálását, miközben közel valós idejű adatelérést és magas rendelkezésre állást is biztosít. Az Azure Cosmos DB a régión belül 99,99%-os magas rendelkezésre állást, a régiók között pedig 99,999%-os olvasási és írási rendelkezésre állást biztosít felügyeleti kötelezettségek nélkül. További információ a [Globális adatterjesztés](distribute-data-globally.md) című cikkben található. 

**Megválasztható konzisztenciaszint**: Az Azure Cosmos DB-vel öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A konzisztenciaszintek: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik a fejlesztő számára, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késés között. További információ a [konzisztenciaszintek](consistency-levels.md) című cikkben található. 

**Nagyvállalati szint**: Az Azure Cosmos DB [megfelelőségi tanúsítványainak](https://www.microsoft.com/trustcenter) köszönhetően a felhasználók biztosak lehetnek benne, hogy a platform használata biztonságos. Az Azure Cosmos DB emellett biztosítja az adatok titkosítását a továbbítás és a tárolás során, valamint egy IP-tűzfalat is tartalmaz, illetve auditnaplókat a vezérlősík tevékenységeinek felügyelete érdekében.

**Esemény beszerzése**: Cassandra API hozzáférést biztosít egy állandó változási naplóhoz, a [változási csatornához](cassandra-change-feed.md), amely közvetlenül az adatbázisból tudja megkönnyíteni az események beszerzését. Az Apache Cassandra esetében az egyetlen egyenértékű az adatváltozások rögzítése (CDC), amely csupán egy olyan mechanizmus, amely az archiváláshoz adott táblákat jelöl, valamint elutasítja az írásokat az adott táblákba, amint a CDC-naplóhoz konfigurálható méretű lemez érhető el (ezek a képességek redundánsak Cosmos DB, mivel a vonatkozó szempontok automatikusan szabályozva vannak).

## <a name="next-steps"></a>Következő lépések

* A következő nyelvspecifikus alkalmazások összeállításával gyorsan hozzákezdhet a Cassandra API-adatok létrehozásához és kezeléséhez:
  - [Node.js-alkalmazás](create-cassandra-nodejs.md)
  - [.NET-alkalmazás](create-cassandra-dotnet.md)
  - [Python-alkalmazás](create-cassandra-python.md)

* Ismerkedjen meg [Cassandra API fiók, adatbázis és egy Java-alkalmazás használatával létrehozott táblázat létrehozásával](create-cassandra-api-account-java.md) .

* [Mintaadatokat betöltése a Cassandra API táblába](cassandra-api-load-data.md) Java-alkalmazás használatával.

* [Adatok lekérdezése a Cassandra API-fiókból](cassandra-api-query-data.md) Java-alkalmazás használatával.

* További információ az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra-funkciókról a [Cassandra-támogatás](cassandra-support.md) cikkben található.

* Tekintse meg a [gyakori kérdéseket](cassandra-faq.md).

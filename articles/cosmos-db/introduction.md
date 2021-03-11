---
title: Az Azure Cosmos DB bemutatása
description: Az Azure Cosmos DB ismertetése. Ez a globálisan elosztott, többmodelles adatbázis az alacsony késés, a rugalmas skálázhatóság és a magas rendelkezésre állás jegyében készült, valamint natív támogatást biztosít a NoSQL-adatokhoz.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 9df5c63ef175d0cf736ce16036466158ab6565f9
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618304"
---
# <a name="welcome-to-azure-cosmos-db"></a>Üdvözli az Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A mai alkalmazásoknak alacsony válaszidővel kell rendelkezniük, és mindig elérhetőnek kell lenniük a hálózaton keresztül. A kis késés és a magas rendelkezésre állás elérése érdekében ezeknek az alkalmazásoknak a példányait a felhasználókhoz közel lévő adatközpontokban kell üzembe helyezni. Az alkalmazásoknak valós időben kell válaszolniuk a használat csúcsidőszakokban tapasztalható nagy változásaira, valamint egyre nagyobb mennyiségű adatot kell tárolniuk, és ezeket az adatokat ezredmásodpercek alatt kell elérhetővé tenniük a felhasználók számára.

A Azure Cosmos DB egy teljes körűen felügyelt NoSQL-adatbázis a modern alkalmazások fejlesztéséhez. Egyszámjegyű ezredmásodperces válaszidő, az automatikus és az azonnali méretezhetőség, a garantált sebesség bármilyen méretben. Az üzletmenet folytonossága garantált [SLA-alapú](https://azure.microsoft.com/support/legal/sla/cosmos-db) rendelkezésre állással és nagyvállalati szintű biztonsággal. Az alkalmazásfejlesztés gyorsabb és termelékenyebb, köszönhetően a világ bármely pontján elérhető, nyílt forráskódú API-k és SDK-k népszerű nyelvekhez Teljes körűen felügyelt szolgáltatásként Azure Cosmos DB az adatbázis-felügyeletet az automatikus felügyelettel, frissítésekkel és javításokkal végzi. Emellett a kapacitások felügyeletét is a költséghatékony kiszolgáló nélküli és automatikus skálázási lehetőségekkel kezeli, amelyek az alkalmazásra reagálva kielégítik a kapacitást az igényeknek megfelelően.

> [!NOTE]
> Szeretne segíteni a felhasználói tanulmányokban való részvétellel Azure Cosmos DB dokumentumok fejlesztésében? Kérjük, szánjon néhány percet az 5 perces [szűrési kérdőív](https://aka.ms/cosmosdb-documentation-screener-survey)kitöltésére. Ha Ön jogosult, a rendszer átirányítja egy olyan ütemező munkahelyre, ahol egy interaktív kutatási munkamenethez is lefoglalhat egy tárolóhelyet. A folyamat során a rendszer [adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=521839)szerint nem gyűjt személyes adatokat.

Ha ingyenes Azure-előfizetéssel, díjmentesen, vagy a [Azure Cosmos db ingyenes](optimize-dev-test.md#azure-cosmos-db-free-tier) szinten szeretné [kipróbálni a Azure Cosmos DBT](https://azure.microsoft.com/try/cosmosdb/) , akkor az első 400 ru/s és 5 GB tárhellyel rendelkező fiókot is igénybe veheti.

> [!div class="nextstepaction"]
> [Próbálja ki Azure Cosmos DB ingyen](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="A Azure Cosmos DB egy teljes körűen felügyelt NoSQL-adatbázis a modern alkalmazások fejlesztéséhez." border="false":::

## <a name="key-benefits"></a>Főbb előnyök

### <a name="guaranteed-speed-at-any-scale"></a>Garantált sebesség bármilyen méretben

Páratlan [SLA-alapú](https://azure.microsoft.com/support/legal/sla/cosmos-db) sebességet és teljesítményt, gyors globális hozzáférést és azonnali rugalmasságot nyerhet.

- Valós idejű hozzáférés gyors olvasási és írási késéssel globálisan, és az átviteli sebesség és a konzisztencia a [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) -kat is alátámasztva
- Többrégiós írások és Adatterjesztés bármely Azure-régióba egy kattintással.
- Egymástól függetlenül, rugalmasan méretezhető a tárolás és az átviteli sebesség bármely Azure-régióban – akár előre nem látható forgalom esetén is –, korlátlan léptékben világszerte.

### <a name="simplified-application-development"></a>Egyszerűbb alkalmazásfejlesztés

Gyorsan készíthet nyílt forráskódú API-kat, több SDK-t, séma nélküli és nem ETL-elemzést az operatív adathoz.

- Szorosan integrálva van a modern (Felhőbeli natív) alkalmazások fejlesztéséhez használt főbb Azure-szolgáltatásokkal, többek között a Azure Functions, a IoT Hub, az AK (Azure Kubernetes Service), a App Service és sok más szolgáltatással.
- Több adatbázis-API közül választhat, beleértve a natív Core (SQL) API-t, az API-t a MongoDB, a Cassandra API, a Gremlin API-t és a Table APIt.
- A .NET, a Java, a Node.js és a Python SDK-k segítségével a Core (SQL) API-t használhatja az alkalmazások létrehozásához. Vagy az Ön által választott illesztőprogramokat bármelyik másik adatbázis API-hoz.
- Az Azure szinapszis Analytics használatával a Azure Cosmos DBban tárolt üzemeltetési adathoz közel valós időben futtasson nem ETL-elemzést.
- A módosítási hírcsatorna segítségével egyszerűen nyomon követheti és kezelheti az adatbázis-tárolók módosításait, és az aktivált eseményeket Azure Functions segítségével hozhatja létre.
- Azure Cosmos DB séma nélküli szolgáltatása automatikusan indexeli az összes adatait, függetlenül az adatmodelltől, a villámgyors lekérdezések biztosításához.

### <a name="mission-critical-ready"></a>Feladat – kritikusan kész

Garantálhatja az üzletmenet folytonosságát, a 99,999%-os rendelkezésre állást és a nagyvállalati szintű biztonságot minden alkalmazás esetében.

- A Azure Cosmos DB számos [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) -t kínál, többek között piacvezető rendelkezésre állást világszerte.
- Az automatikus adatreplikálással könnyedén terjesztheti az összes Azure-régiót. Erős konzisztencia használata esetén a nulla állásidőt a többrégiós írásokkal vagy a RPO 0 értékkel veheti igénybe.
- A nagyvállalati szintű titkosítást saját maga által felügyelt kulcsokkal használhatja.
- Az Azure szerepköralapú hozzáférés-vezérlés gondoskodik az adatai biztonságáról, és kiválóan hangolt vezérlést biztosít.

### <a name="fully-managed-and-cost-effective"></a>Teljes körűen felügyelt és költséghatékony

Végpontok közötti adatbázis-kezelés, kiszolgáló nélküli és automatikus skálázással, amely megfelel az alkalmazásnak és a TCO-nak

- Teljes körűen felügyelt adatbázis-szolgáltatás. Automatikus, érintés nélküli, karbantartási, javítási és frissítési, a fejlesztők időt és pénzt takaríthat meg.
- Költséghatékony lehetőségek bármilyen méret vagy skála előre nem látható vagy szórványos számítási feladataihoz, ami lehetővé teszi a fejlesztők számára, hogy a kapacitás megtervezése és kezelése nélkül is könnyedén megkezdjék a munkát.
- A kiszolgáló nélküli modell villámgyors munkaterheléseket biztosít az automatikus és a rugalmas szolgáltatás számára, amellyel igény szerint kezelheti a forgalom feltöréseit.
- Automatikusan kiépített átviteli sebesség automatikus méretezése, és a nem kiszámítható számítási feladatokhoz tartozó kapacitások azonnali skálázása az [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)-kat tart fenn.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Az Azure Cosmos DB előnyeit kihasználó megoldások

Az olyan [webes, mobil-, játék-és IoT-alkalmazások](use-cases.md) , amelyek nagy mennyiségű adatot, olvasási és írási műveleteket igényelnek [globális szinten](distribute-data-globally.md) , a különböző adatokhoz közel valós válaszidő esetén, Cosmos db [garantált magas rendelkezésre állást](https://azure.microsoft.com/support/legal/sla/cosmos-db/), magas átviteli sebességet, kis késést és hangolt konzisztenciát biztosítanak. Ismerje meg, hogyan használhatók a Azure Cosmos DB a [IoT és a telematika](use-cases.md#iot-and-telematics), a [kiskereskedelmi és a marketing](use-cases.md#retail-and-marketing), a [játékok](use-cases.md#gaming) és a [webes és mobil alkalmazások](use-cases.md#web-and-mobile-applications)létrehozásához.

## <a name="next-steps"></a>Következő lépések

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

- [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
- [Ismerkedés a Azure Cosmos DB API-MongoDB](create-mongodb-nodejs.md)
- [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
- [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
- [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)

---
title: Adatok migrálása a Cassandra-ből Azure Cosmos DB Cassandra API a Blitzz használatával
description: Ismerje meg, hogyan telepítheti át az Apache Cassandra Database-ből származó adatok Azure Cosmos DB Cassandra API a Blitzz használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: c26d21e74e9808fe65890b7f4eba31ee742552a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339988"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Adatok migrálása a Cassandra-ből Azure Cosmos DB Cassandra API-fiókba a Blitzz használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DB Cassandra API az Apache Cassandra-on futó nagyvállalati számítási feladatok széles választékát váltják ki, többek között: 

* **A felügyelet és a figyelés nem jelent terhelést:** Kiküszöböli a több operációs rendszer-, JVM-és YAML-fájlon, valamint azok interakcióján keresztüli különböző beállítások felügyeletének és figyelésének a terhelését.

* **Jelentős költségmegtakarítás:** A költségeket megtakaríthatja Azure Cosmos DBával, amely magában foglalja a virtuális gépek, a sávszélesség és a kapcsolódó licencek költségeit is. Emellett nem szükséges az adatközpontok, a kiszolgálók, az SSD-tárolók, a Hálózatkezelés és a villamosenergia-költségek kezelése. 

* **Meglévő kód és eszközök használatának lehetősége:** A Azure Cosmos DB a meglévő Cassandra SDK-k és eszközök segítségével biztosítja a vezetékes protokollok szintjének kompatibilitását. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal az Azure Cosmos DB Cassandra API-val is használhatja.

Az adatbázis-számítási feladatok egyik platformról a másikra való áttelepíthetők többféleképpen. A [Blitzz](https://www.blitzz.io) egy olyan eszköz, amely biztonságos és megbízható módot biztosít a különböző adatbázisokból Azure Cosmos DBba való áttelepítésének leállására. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek az adatok Apache Cassandra-adatbázisból való átCassandra API Azure Cosmos DBához a Blitzz használatával.

## <a name="benefits-using-blitzz-for-migration"></a>Az Blitzz for Migration használatának előnyei

A Blitzz áttelepítési megoldása a komplex működési feladatok áttelepítésének lépésenkénti megközelítését követi. A Blitzz Zero-állásidő áttelepítési tervének főbb szempontjai a következők:

* Az Apache Cassandra Database-ből a Azure Cosmos DBba automatikus áttelepítést biztosít az üzleti logika (táblák, indexek, nézetek) számára. A sémákat nem kell manuálisan létrehoznia.

* A Blitzz nagy mennyiségű és párhuzamos adatbázis-replikációt biztosít. Lehetővé teszi, hogy a forrás-és a célként megadott platformok szinkronban legyenek az áttelepítés során a Change-adatrögzítés (CDC) nevű technikával. A CDC használatával a Blitzz folyamatosan lekéri a forrás-adatbázis (Apache Cassandra) változásait, és alkalmazza azt a céladatbázis (Azure Cosmos DB) számára.

* Hibatűrő, és pontosan egyszer biztosítja az adatátvitelt, még a rendszer hardver-vagy szoftver-meghibásodása során is.

* Az adatátvitel során különböző biztonsági módszerekkel, például a TLS-vel, a titkosítással biztosítja az adatátvitelt.

## <a name="steps-to-migrate-data"></a>Az adatáttelepítés lépései

Ez a szakasz ismerteti a Blitzz beállításához szükséges lépéseket, valamint az Apache Cassandra-adatbázisból az adatok áttelepítése Azure Cosmos DBra.

1. Adjon hozzá egy biztonsági tanúsítványt a számítógépről, amelyre telepíteni szeretné a Blitzz-replikált. Ezt a tanúsítványt a Blitzz-replikált a megadott Azure Cosmos DB fiókkal létesített TLS-kapcsolat létesítéséhez szükséges. A tanúsítványt a következő lépésekkel adhatja hozzá:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. A Blitzz-telepítést és a bináris fájlokat a [Blitzz webhelyén](https://www.blitzz.io)található bemutató igénylésével szerezheti be. Azt is megteheti, hogy [e-mailt](mailto:success@blitzz.io) küld a csapatnak.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz replikált eszköz letöltése":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz replikált-fájlok":::

1. A CLI-terminálon állítsa be a forrás adatbázis konfigurációját. Nyissa meg a konfigurációs fájlt a **`vi conf/conn/cassandra.yml`** paranccsal, és adja hozzá a Cassandra csomópontok, a portszám, a Felhasználónév, a jelszó és a többi szükséges adat IP-címeinek vesszővel tagolt listáját. A következő példa a konfigurációs fájlban található tartalmakat szemlélteti:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png" alt-text="A Cassandra-kapcsolatok szerkesztőjének megnyitása":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png" alt-text="Cassandra-kapcsolatok konfigurálása":::

   A konfigurációs adatok kitöltése után mentse és zárjuk be a fájlt.

1. Igény szerint beállíthatja a forrás adatbázis-szűrő fájlját is. A szűrő fájl határozza meg, hogy mely sémákat vagy táblákat kell áttelepíteni. Nyissa meg a konfigurációs fájlt a **`vi filter/cassandra_filter.yml`** parancs használatával, és adja meg a következő konfigurációs adatokat:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Az adatbázis-szűrő részleteinek kitöltése után mentse és zárjuk be a fájlt.

1. Ezután be kell állítania a céladatbázis konfigurációját. A konfiguráció megadása előtt [hozzon létre egy Azure Cosmos DB Cassandra API-fiókot](create-cassandra-dotnet.md#create-a-database-account) , majd hozzon létre egy alapterületet, és egy táblát az áttelepített adattároláshoz. Mivel az Apache Cassandra-ból áttelepítést végez a Azure Cosmos DB Cassandra APIre, használhatja ugyanazt a partíciót, amelyet az Apache Cassandra használatával használt.

1. Az adatok áttelepítése előtt növelje a tároló átviteli sebességét az alkalmazás gyors áttelepítéséhez szükséges mennyiségre. Megnövelheti például az átviteli sebességet 100000 RUs-re. Az adatátviteli sebesség az áttelepítés megkezdése előtt méretezése segít az adatok áttelepítésében kevesebb idő alatt.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Az Azure Cosmos-tároló méretezése az egész":::

   Csökkentse az átviteli sebességet az áttelepítés befejeződése után. A tárolt adatok mennyisége és az egyes műveletekhez szükséges RUs alapján megbecsülhető az adatáttelepítés után szükséges átviteli sebesség. Ha többet szeretne megtudni arról, hogyan kell megbecsülni az RUs-t, tekintse meg a [tárolók és adatbázisok átviteli sebességének kiépítése](set-throughput.md) és [a Azure Cosmos db Capacity Planner cikkeinek becslése](estimate-ru-with-capacity-planner.md) című cikket.

1. Szerezze be az Azure Cosmos-fiók **kapcsolattartási pontját, portját, felhasználónevét** és **elsődleges jelszavát** a **kapcsolati karakterlánc** ablaktáblán. Ezeket az értékeket a konfigurációs fájlban fogja használni.

1. A CLI-terminálon állítsa be a céladatbázis konfigurációját. Nyissa meg a konfigurációs fájlt a **`vi conf/conn/cosmosdb.yml`** paranccsal, és adja hozzá a gazdagép URI-ja, portszáma, felhasználóneve, jelszava és egyéb szükséges paraméterek vesszővel tagolt listáját. A következő példa a konfigurációs fájl tartalmát jeleníti meg:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Ezután az Blitzz használatával telepítse át az adatátvitelt. A Blizz replikált **teljes** vagy **Pillanatkép** módban is futtathatja:

   * **Teljes mód** – ebben a módban a replikált továbbra is futni fog az áttelepítés után, és figyeli a forrás Apache Cassandra rendszer változásainak változásait. Ha bármilyen változást észlel, valós időben replikálja a cél Azure Cosmos-fiókban.

   * **Pillanatkép mód** – ebben a módban elvégezheti a séma áttelepítését és az egyszeri adatreplikálást. Ez a beállítás nem támogatja a valós idejű replikálást.

   A fenti két mód használatával a Migrálás nulla állásidővel végezhető el. 

1. Az adatok áttelepíthetők a Blitzz replikált CLI-terminálból, a következő parancs futtatásával:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   A replikált felhasználói felületén látható a replikálási folyamat. Ha elkészült a séma-áttelepítési és a pillanatkép-művelet, a folyamat 100%-ot mutat. Az áttelepítés befejezése után érvényesítheti a cél Azure Cosmos-adatbázisban található adatforrásokat.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png" alt-text="Cassandra adatáttelepítés kimenete":::


1. Mivel a teljes áttelepítési módot használta, olyan műveleteket hajthat végre, mint például az adatok beszúrása, frissítése vagy törlése a forrás Apache Cassandra-adatbázison. Később ellenőrizze, hogy a cél Azure Cosmos-adatbázisban valós időben replikálódnak-e. Az áttelepítés után csökkentse az Azure Cosmos-tárolóhoz konfigurált átviteli sebességet.

1. A replikált bármikor leállíthatja, és újraindíthatja a **--resume** kapcsolóval. A replikálás az adatok konzisztenciájának veszélyeztetése nélkül folytatódik a pontról. Az alábbi parancs bemutatja, hogyan használhatja a folytatás kapcsolót.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Ha többet szeretne megtudni az adatáttelepítés célhelyre való áttelepítéséről, tekintse meg a [Blitzz replikált bemutatóját](https://www.youtube.com/watch?v=fsUhF9LUZmM).

## <a name="next-steps"></a>Következő lépések

* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md) 
* [A partíciós kulcs ajánlott eljárásai](partitioning-overview.md#choose-partitionkey)
* [Ru/s becslése a Azure Cosmos db Capacity Planner cikkei alapján](estimate-ru-with-capacity-planner.md)

---
title: Adatok migrálása az Oracle-ből Azure Cosmos DB Cassandra API a Blitzz használatával
description: Ismerje meg, hogyan telepítheti át az Oracle Database-ből származó adatok Azure Cosmos DB Cassandra API a Blitzz használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336440"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Adatok migrálása az Oracle-ből Azure Cosmos DB Cassandra API-fiókba a Blitzz használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DB Cassandra API az Oracle-ben futó nagyvállalati számítási feladatok széles választékát váltják ki, többek között:

* **Jobb méretezhetőség és rendelkezésre állás:** Kiküszöböli az egyes meghibásodási pontokat, az alkalmazások jobb méretezhetőségét és rendelkezésre állását.

* **Jelentős költségmegtakarítás:** A költségeket megtakaríthatja Azure Cosmos DBával, amely magában foglalja a virtuális gépek, a sávszélesség és az esetlegesen érvényes Oracle-licencek költségeit is. Emellett nem szükséges az adatközpontok, a kiszolgálók, az SSD-tárolók, a Hálózatkezelés és a villamosenergia-költségek kezelése.

* **A felügyelet és a figyelés nem jelent terhelést:** Teljes körűen felügyelt felhőalapú szolgáltatásként Azure Cosmos DB eltávolítja a rengeteg beállítás felügyeletének és figyelésének terhét.

Az adatbázis-számítási feladatok egyik platformról a másikra való áttelepíthetők többféleképpen. A [Blitzz](https://www.blitzz.io) egy olyan eszköz, amely biztonságos és megbízható módot biztosít a különböző adatbázisokból Azure Cosmos DBba való áttelepítésének leállására. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek az Oracle Database-ből származó adatok Azure Cosmos DB Cassandra API a Blitzz használatával történő átimportálásához.

## <a name="benefits-using-blitzz-for-migration"></a>Az Blitzz for Migration használatának előnyei

A Blitzz áttelepítési megoldása a komplex működési feladatok áttelepítésének lépésenkénti megközelítését követi. A Blitzz Zero-állásidő áttelepítési tervének főbb szempontjai a következők:

* A szolgáltatás az üzleti logika (táblák, indexek, nézetek) automatikus áttelepítését kínálja az Oracle Database-ből Azure Cosmos DBra. A sémákat nem kell manuálisan létrehoznia.

* A Blitzz nagy mennyiségű és párhuzamos adatbázis-replikációt biztosít. Lehetővé teszi, hogy a forrás-és a célként megadott platformok szinkronban legyenek az áttelepítés során a Change-adatrögzítés (CDC) nevű technikával. A CDC használatával a Blitzz folyamatosan lekéri a forrás-adatbázis (Oracle) változásait, és alkalmazza azt a céladatbázis (Azure Cosmos DB) számára.

* Hibatűrő, és a rendszer hardver-vagy szoftver-meghibásodása esetén is pontosan egyszer garantálja az adattovábbítást.

* Az adatátvitel során a különböző biztonsági módszertanok, például a TLS/SSL és a titkosítás használatával biztosít védelmet.

* Olyan szolgáltatásokat kínál, amelyekkel az összetett üzleti logikát (PL./SQL) a Azure Cosmos DBban egyenértékű üzleti logikára konvertálhatja.

## <a name="steps-to-migrate-data"></a>Az adatáttelepítés lépései

Ez a szakasz ismerteti azokat a lépéseket, amelyek a Blitzz beállításához és az Oracle Database-ből a Azure Cosmos DBba való áttelepítéséhez szükségesek.

1. Adjon hozzá egy biztonsági tanúsítványt a számítógépről, amelyre telepíteni szeretné a Blitzz-replikált. Ezt a tanúsítványt a Blitzz-replikált a megadott Azure Cosmos DB fiókkal létesített TLS-kapcsolat létesítéséhez szükséges. A tanúsítványt a következő lépésekkel adhatja hozzá:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. a szervezeti egység a Blitzz-telepítést és a bináris fájlokat a [Blitzz webhelyén](https://www.blitzz.io)bemutató kéréssel szerezheti be. Azt is megteheti, hogy [e-mailt](mailto:success@blitzz.io) küld a csapatnak.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz replikált eszköz letöltése":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz replikált-fájlok":::

1. A CLI-terminálon állítsa be a forrás adatbázis konfigurációját. Nyissa meg a konfigurációs fájlt a **`vi conf/conn/oracle.yml`** paranccsal, és adja hozzá az Oracle-csomópontok, a portszám, a Felhasználónév, a jelszó és a többi szükséges adat IP-címeinek vesszővel tagolt listáját. A következő kód egy példaként szolgáló konfigurációs fájlt mutat be:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="Az Oracle-kapcsolatok szerkesztőjének megnyitása":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Oracle-kapcsolatok konfigurálása":::

   A konfigurációs adatok kitöltése után mentse és zárjuk be a fájlt.

1. Igény szerint beállíthatja a forrás adatbázis-szűrő fájlját is. A szűrő fájl határozza meg, hogy mely sémákat vagy táblákat kell áttelepíteni. Nyissa meg a konfigurációs fájlt a **`vi filter/oracle_filter.yml`** parancs használatával, és adja meg a következő konfigurációs adatokat:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Az adatbázis-szűrő részleteinek kitöltése után mentse és zárjuk be a fájlt.

1. Ezután be kell állítania a céladatbázis konfigurációját. A konfiguráció megadása előtt [hozzon létre egy Azure Cosmos DB Cassandra API fiókot](create-cassandra-dotnet.md#create-a-database-account). [Válassza ki az adatokból a megfelelő partíciós kulcsot](partitioning-overview.md#choose-partitionkey) , majd hozzon létre egy kulcstartót, valamint egy táblát az áttelepített adatok tárolásához.

1. Az adatok áttelepítése előtt növelje a tároló átviteli sebességét az alkalmazás gyors áttelepítéséhez szükséges mennyiségre. Megnövelheti például az átviteli sebességet 100000 RUs-re. Az adatátviteli sebesség az áttelepítés megkezdése előtt méretezése segít az adatok áttelepítésében kevesebb idő alatt. 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Az Azure Cosmos-tároló méretezése az egész":::

   Az áttelepítés befejeződése után csökkentenie kell az átviteli sebességet. A tárolt adatok mennyisége és az egyes műveletekhez szükséges RUs alapján megbecsülhető az adatáttelepítés után szükséges átviteli sebesség. Ha többet szeretne megtudni arról, hogyan kell megbecsülni az RUs-t, tekintse meg a [tárolók és adatbázisok átviteli sebességének kiépítése](set-throughput.md) és [a Azure Cosmos db Capacity Planner cikkeinek becslése](estimate-ru-with-capacity-planner.md) című cikket.

1. Szerezze be az Azure Cosmos-fiók **kapcsolattartási pontját, portját, felhasználónevét** és **elsődleges jelszavát** a **kapcsolati karakterlánc** ablaktáblán. Ezeket az értékeket a konfigurációs fájlban fogja használni.

1. A CLI-terminálon állítsa be a céladatbázis konfigurációját. Nyissa meg a konfigurációs fájlt a **`vi conf/conn/cosmosdb.yml`** paranccsal, és adja hozzá a gazdagép URI-ja, portszáma, felhasználóneve, jelszava és egyéb szükséges paraméterek vesszővel tagolt listáját. A következő példa a konfigurációs fájlban található tartalmakat szemlélteti:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Ezután az Blitzz használatával telepítse át az adatátvitelt. A Blizz replikált **teljes** vagy **Pillanatkép** módban is futtathatja:

   * **Teljes mód** – ebben a módban a replikált továbbra is futni fog az áttelepítés után, és figyeli a forrás Oracle-rendszer változásait. Ha bármilyen változást észlel, valós időben replikálja a cél Azure Cosmos-fiókban.

   * **Pillanatkép mód** – ebben a módban elvégezheti a séma áttelepítését és az egyszeri adatreplikálást. Ez a beállítás nem támogatja a valós idejű replikálást.


   A fenti két mód használatával a Migrálás nulla állásidővel végezhető el.

1. Az adatok áttelepíthetők a Blitzz replikált CLI-terminálból, a következő parancs futtatásával:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   A replikált felhasználói felületén látható a replikálási folyamat. Ha elkészült a séma-áttelepítési és a pillanatkép-művelet, a folyamat 100%-ot mutat. Az áttelepítés befejezése után érvényesítheti a cél Azure Cosmos-adatbázisban található adatforrásokat.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Oracle-adatáttelepítési kimenet":::

1. Mivel a teljes áttelepítési módot használta, olyan műveleteket hajthat végre, mint például az adatok beszúrása, frissítése vagy törlése a forrás Oracle-adatbázisban. Később ellenőrizheti, hogy valós időben replikálódnak-e a cél Azure Cosmos-adatbázison. Az áttelepítés után csökkentse az Azure Cosmos-tárolóhoz konfigurált átviteli sebességet.

1. A replikált bármikor leállíthatja, és újraindíthatja a **--resume** kapcsolóval. A replikálás az adatok konzisztenciájának veszélyeztetése nélkül folytatódik a pontról. Az alábbi parancs bemutatja, hogyan használhatja a folytatás kapcsolót.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Ha többet szeretne megtudni az adatáttelepítés célhelyre való áttelepítéséről, tekintse meg a [Blitzz replikált bemutatóját](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Következő lépések

* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md) 
* [A partíciós kulcs ajánlott eljárásai](partitioning-overview.md#choose-partitionkey)
* [Ru/s becslése a Azure Cosmos db Capacity Planner cikkei alapján](estimate-ru-with-capacity-planner.md)

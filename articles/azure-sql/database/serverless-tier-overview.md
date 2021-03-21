---
title: Kiszolgáló nélküli számítási szint
description: Ez a cikk az új kiszolgáló nélküli számítási szintet ismerteti, és összehasonlítja azt a Azure SQL Database meglévő kiépített számítási szintjével.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 2/22/2021
ms.openlocfilehash: 4dd7bbe613b30df2611bfe6631950e121235204a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658588"
---
# <a name="azure-sql-database-serverless"></a>Kiszolgáló nélküli Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A kiszolgáló nélküli szint az Azure SQL Database-ben az önálló adatbázisok számítási szintje, amely automatikusan skálázza a számításokat a számítási feladatok igényei alapján, és a másodpercenként felhasznált számítási mennyiségek szerint számláz. A kiszolgáló nélküli számítási szint emellett automatikusan szünetelteti az adatbázisokat az inaktív időszakok során, amikor csak a tárterület használati díját számlázzuk ki, és automatikusan folytatja az adatbázisok működését, amikor ismét előfordulnak tevékenységek.

## <a name="serverless-compute-tier"></a>Kiszolgáló nélküli számítási szint

A Azure SQL Databaseban található önálló adatbázisok kiszolgáló nélküli számítási rétegét egy számítási automatikus skálázási tartomány és egy automatikus szüneteltetési késleltetés jellemzi. Ezeknek a paramétereknek a konfigurációja az adatbázis teljesítményének és számítási költségeit formálja.

![kiszolgáló nélküli számlázás](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Teljesítmény konfigurálása

- A **minimális virtuális mag** és a **maximális virtuális mag** olyan konfigurálható paraméterek, amelyek meghatározzák az adatbázis számára elérhető számítási kapacitás tartományát. A memória és az i/o-korlátok arányosak a megadott virtuális mag-tartománnyal.  
- Az **automatikus szüneteltetési késleltetés** egy konfigurálható paraméter, amely meghatározza azt az időtartamot, ameddig az adatbázisnak inaktívnak kell lennie, mielőtt automatikusan szünetelteti. A rendszer automatikusan folytatja az adatbázist, ha a következő bejelentkezés vagy más tevékenység történik.  Alternatív megoldásként az automatikus szüneteltetés le is tiltható.

### <a name="cost"></a>Költségek

- A kiszolgáló nélküli adatbázisok díja a számítási és a tárolási díjak összegzése.
- Ha a számítási használat a minimális és a maximális korlát között van, a számítási díj a felhasznált virtuális mag és memória alapján történik.
- Ha a számítási használat a minimálisan konfigurált határértékek alatt van, a számítási díj a minimális virtuális mag és a minimálisan konfigurált memória alapján történik.
- Ha az adatbázis szüneteltetve van, a számítási költség nulla, és csak a tárolási költségek merülnek fel.
- A tárolási költségeket ugyanúgy határozzák meg, mint a kiépített számítási szinten.

További részletekért tekintse meg a [számlázást](serverless-tier-overview.md#billing)ismertető témakört.

## <a name="scenarios"></a>Forgatókönyvek

A kiszolgáló nélküli adatbázisok ár–teljesítmény aránya az időszakos, kiszámíthatatlan használati mintázatú önálló adatbázisokhoz van optimalizálva, ahol elfogadható némi késés a növekvő mennyiségű számítási feladatok elvégzésében a tétlen időszakok után. Ezzel szemben a kiépített számítási szint ár–teljesítmény aránya olyan önálló adatbázisokra vagy rugalmas készletekbe rendezett több adatbázisra van optimalizálva, amelyeknél a magasabb átlagos kihasználtság miatt nem elfogadható a késés, amikor felpörög a számítási feladatok száma.

### <a name="scenarios-well-suited-for-serverless-compute"></a>A kiszolgáló nélküli számítási feladatokhoz jól illeszkedő forgatókönyvek

- Az egyidejű, előre nem látható használati mintákkal rendelkező önálló adatbázisok időbeli inaktivitási és alacsonyabb átlagos számítási kihasználtsággal rendelkeznek.
- A kiépített számítási rétegben lévő önálló adatbázisok, amelyek gyakran átméretezhetők, és az ügyfelek, akik szívesebben delegálják a szolgáltatásnak a számítási átméretezést.
- Az új, használati előzmények nélküli önálló adatbázisok, amelyekben a számítási méretezés nehéz, vagy a SQL Database-ben való üzembe helyezés előtt nem lehet megbecsülni.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>A kiépített számítási feladatokhoz jól illeszkedő forgatókönyvek

- Az önálló adatbázisok esetében több rendszeres, kiszámítható használati minta és nagyobb átlagos számítási kihasználtság áll az idő múlásával.
- Azokat az adatbázisokat, amelyek nem tudják tolerálni a teljesítménybeli kompromisszumokat, így gyakrabban fordulnak elő a memóriából, vagy késleltetik a szüneteltetett állapotból való folytatást.
- Több adatbázis időszakos, előre nem látható használati mintákkal, amelyek rugalmas készletekbe egyesíthetők a jobb ár-teljesítmény optimalizálása érdekében.

## <a name="comparison-with-provisioned-compute-tier"></a>Összehasonlítás a kiépített számítási szintjével

A következő táblázat összefoglalja a kiszolgáló nélküli számítási rétegek és a kiépített számítási szintek közötti különbségeket:

| | **Kiszolgáló nélküli számítástechnika** | **Kiépített számítás** |
|:---|:---|:---|
|**Adatbázis-használati minta**| Időszakos, előre jelezhető használat kisebb átlagos számítási használattal az idő múlásával. | A rendszeres használati minták nagyobb átlagos számítási kihasználtságot és rugalmas készleteket használó több adatbázist használnak.|
| **Teljesítmény-felügyeleti tevékenység** |Alacsonyabb|Magasabb|
|**Számítási skálázás**|Automatikus|Kézi|
|**Számítási rugalmasság**|Alacsonyabb az inaktív időszakok után|Azonnali|
|**Számlázási részletesség**|Másodpercenként|/Óra|

## <a name="purchasing-model-and-service-tier"></a>Modell-és szolgáltatási szintek vásárlása

A kiszolgáló nélküli SQL Database jelenleg csak a virtuális mag beszerzési modell 5. generációs hardverének általános célú szintje támogatja.

## <a name="autoscaling"></a>Automatikus skálázás

### <a name="scaling-responsiveness"></a>Rugalmas méretezés

Általánosságban elmondható, hogy a kiszolgáló nélküli adatbázisok egy olyan gépen futnak, amely elegendő kapacitással rendelkezik ahhoz, hogy a maximális virtuális mag értékkel beállított korlátokon belül bármilyen mennyiségű számítási igény megszakítható legyen. Esetenként a terheléselosztás automatikusan történik, ha a gép néhány percen belül nem képes kielégíteni az erőforrás-igényt. Ha például az erőforrás igénye 4 virtuális mag, de csak 2 virtuális mag érhető el, akkor a 4 virtuális mag megadása előtt akár néhány percet is igénybe vehet. Az adatbázis online állapotban marad a terheléselosztás során, kivéve, ha a kapcsolat eldobásakor a művelet végén rövid ideig tart.

### <a name="memory-management"></a>Memória kezelése

A kiszolgáló nélküli adatbázisok memóriáját gyakrabban, mint a kiépített számítási adatbázisokhoz. Ez a viselkedés fontos a kiszolgáló nélküli költségek szabályozására, és hatással lehet a teljesítményre.

#### <a name="cache-reclamation"></a>Gyorsítótár-visszanyerés

A kiépített számítási adatbázisokkal ellentétben az SQL-gyorsítótárból származó memóriát egy kiszolgáló nélküli adatbázisból kell visszaigényelni, amikor a CPU vagy az aktív gyorsítótár kihasználtsága alacsony.

- Az aktív gyorsítótár kihasználtsága akkor minősül alacsonynak, ha a legutóbb használt gyorsítótár-bejegyzések teljes mérete egy adott időtartam alatt egy küszöbérték alá esik.
- A gyorsítótár-újraindításkor a cél gyorsítótárának mérete fokozatosan csökken az előző méret töredékéért, és a visszaigénylés csak akkor folytatódik, ha a használat alacsony marad.
- A gyorsítótár-visszanyeréskor a kizárni kívánt gyorsítótár-bejegyzések kiválasztására szolgáló házirend ugyanaz a kiválasztási házirend, mint a kiépített számítási adatbázisok esetében, ha a memória nyomása magas.
- A gyorsítótár mérete soha nem csökken a min. virtuális mag által meghatározott minimális memória-korlát alatt, amely konfigurálható.

A kiszolgáló nélküli és a kiépített számítási adatbázisokban a gyorsítótár bejegyzései kizárható, ha az összes rendelkezésre álló memória használatban van.

Vegye figyelembe, hogy ha a CPU-kihasználtság alacsony, akkor az aktív gyorsítótár kihasználtsága a használati mintatól és a memória-visszanyeréstől függően magas marad.  Azt is megteheti, hogy a felhasználói tevékenység leállítása után további késések történnek, mielőtt a rendszer visszaállítja a memóriát a korábbi felhasználói tevékenységekre válaszoló időszakos háttérben futó folyamatok miatt.  A törlési műveletek és a QDS kiszolgálónév-karbantartási feladatok például a törlésre kijelölt szellemkép-rekordokat hoznak, de nem törlődnek fizikailag addig, amíg a szellemkép-tisztítási folyamat fut, ami magában foglalhatja az adatlapok gyorsítótárba való beolvasását is.

#### <a name="cache-hydration"></a>Gyorsítótár-hidratáció

Az SQL-gyorsítótár növekszik, ahogy az adatok beolvasása a lemezről ugyanúgy történik, mint a kiépített adatbázisokkal azonos sebességgel. Ha az adatbázis foglalt, akkor a gyorsítótár mérete a maximális memória korlátja miatt nem lehet nagyobb.

## <a name="auto-pausing-and-auto-resuming"></a>Automatikus szüneteltetés és automatikus folytatás

### <a name="auto-pausing"></a>Automatikus szüneteltetés

Az automatikus szüneteltetés akkor aktiválódik, ha az alábbi feltételek mindegyike teljesül az automatikus szüneteltetés késleltetésének időtartama alatt:

- Munkamenetek száma = 0
- CPU = 0 a felhasználói munkaterheléshez, amely a felhasználói készletben fut

Lehetőség van az automatikus szüneteltetés letiltására, ha szükséges.

A következő szolgáltatások nem támogatják az automatikus szüneteltetést, de támogatják az automatikus skálázást.  Ha a következő funkciók bármelyikét használja, az automatikus szüneteltetést le kell tiltani, és az adatbázis az adatbázis inaktivitásának időtartamától függetlenül továbbra is elérhető marad:

- Geo-replikáció (aktív geo-replikáció és automatikus feladatátvételi csoportok).
- A biztonsági másolatok hosszú távú megőrzése (LTR).
- Az SQL-adatszinkronizálás során használt szinkronizálási adatbázis.  A szinkronizálási adatbázisoktól eltérően a központ és a tagok adatbázisai támogatják az automatikus szüneteltetést.
- DNS-alias
- A rugalmas feladatokban (előzetes verzió) használt feladat-adatbázis.

Az automatikus felfüggesztés átmenetileg megakadályozható néhány olyan szolgáltatás telepítése során, amelyeknek az adatbázis online állapotba kell esnie.  Ilyen esetekben a szolgáltatás frissítésének befejeződése után ismét engedélyezve lesz az automatikus felfüggesztés.

### <a name="auto-resuming"></a>Automatikus folytatás

Az Automatikus folytatás akkor aktiválódik, ha a következő feltételek bármelyike bármikor teljesül:

|Szolgáltatás|Trigger automatikus folytatása|
|---|---|
|Hitelesítés és engedélyezés|Bejelentkezés|
|Fenyegetések észlelése|A veszélyforrások észlelési beállításainak engedélyezése/letiltása az adatbázis vagy a kiszolgáló szintjén.<br>A veszélyforrások észlelési beállításainak módosítása az adatbázis vagy a kiszolgáló szintjén.|
|Adatfelderítés és besorolás|Érzékenységi Címkék hozzáadása, módosítása, törlése vagy megtekintése|
|Naplózás|Naplózási rekordok megtekintése.<br>Naplózási házirend frissítése vagy megtekintése.|
|Adatmaszkolás|Az adatmaszkolási szabályok hozzáadása, módosítása, törlése vagy megtekintése|
|Transzparens adattitkosítás|Transzparens adattitkosítás állapotának vagy állapotának megtekintése|
|Sebezhetőségi felmérés|Ad hoc vizsgálatok és rendszeres vizsgálatok, ha engedélyezve vannak|
|Lekérdezés (teljesítmény) adattár|A lekérdezési tároló beállításainak módosítása vagy megtekintése|
|Teljesítménnyel kapcsolatos javaslatok|Teljesítményre vonatkozó javaslatok megtekintése és alkalmazása|
|Automatikus hangolás|Automatikus hangolási javaslatok alkalmazása és ellenőrzése, például automatikus indexelés|
|Adatbázis másolása|Adatbázis létrehozása másolásként.<br>Exportálás BACPAC-fájlba.|
|SQL-adatszinkronizálás|Az olyan központi és tag adatbázisok közötti szinkronizálás, amelyek konfigurálható ütemterv szerint futnak, vagy manuálisan végeznek műveleteket|
|Bizonyos adatbázis-metaadatok módosítása|Új adatbázis-Címkék hozzáadása.<br>A maximális virtuális mag, a minimális virtuális mag vagy az automatikus szüneteltetés késleltetésének módosítása.|
|SQL Server Management Studio (SSMS)|A 18,1-nál korábbi SSMS-verziók használata és a kiszolgáló bármely adatbázisához tartozó új lekérdezési ablak megnyitása folytatja az automatikusan szüneteltetett adatbázist ugyanabban a kiszolgálón. Ez a viselkedés nem fordul elő, ha a SSMS 18,1-es vagy újabb verzióját használja.|

A fent felsorolt műveletek bármelyikét végző figyelési, felügyeleti és egyéb megoldások automatikusan folytatják az automatikus folytatást.

A rendszer az automatikus folytatást is elindítja néhány olyan szolgáltatás telepítése során, amelyeknek az adatbázis online állapotba kell esnie.

### <a name="connectivity"></a>Kapcsolat

Ha egy kiszolgáló nélküli adatbázis szüneteltetve van, akkor az első bejelentkezés folytatja az adatbázist, és egy hibaüzenetet ad vissza, amely azt jelzi, hogy az adatbázis nem érhető el a 40613-as hibakódú kóddal. Az adatbázis újraindítása után a bejelentkezést újra meg kell próbálni a kapcsolat létrehozásához. A kapcsolódási újrapróbálkozási logikával rendelkező adatbázis-ügyfeleket nem szükséges módosítani.

### <a name="latency"></a>Késés

A kiszolgáló nélküli adatbázisok automatikus folytatásának és automatikus szüneteltetésének késése általában 1 percet vesz igénybe az Automatikus folytatás és a 1-10 perc közötti automatikus szüneteltetés érdekében.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Ügyfél által felügyelt transzparens adattitkosítás (BYOK)

Ha az [ügyfél által felügyelt transzparens adattitkosítást](transparent-data-encryption-byok-overview.md) (BYOK) használja, és a kiszolgáló nélküli adatbázis automatikusan szünetel a kulcs törlése vagy visszavonása során, akkor az adatbázis az automatikusan szüneteltetett állapotban marad.  Ebben az esetben az adatbázis következő folytatása után az adatbázis körülbelül 10 percen belül elérhetetlenné válik.  Ha az adatbázis elérhetetlenné válik, a helyreállítási folyamat ugyanaz, mint a kiépített számítási adatbázisokhoz.  Ha a kiszolgáló nélküli adatbázis a kulcs törlése vagy visszavonása esetén online állapotú, akkor az adatbázis a kiépített számítási adatbázisok esetében is körülbelül 10 percen belül elérhetetlenné válik.

## <a name="onboarding-into-serverless-compute-tier"></a>A kiszolgáló nélküli számítási szintek bevezetése

Ha egy új adatbázist hoz létre, vagy egy meglévő adatbázist kiszolgáló nélküli számítási rétegbe helyez át, a következő két lépéssel megegyező mintát kell létrehoznia egy új adatbázis létrehozásához a kiépített számítási szinten.

1. A szolgáltatási cél meghatározása. A szolgáltatási cél a szolgáltatási szintet, a hardverek létrehozását és a maximális virtuális mag írja elő. A szolgáltatás céljának beállításaival kapcsolatban lásd: [kiszolgáló nélküli erőforrás-korlátok](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Szükség esetén megadhatja a minimális virtuális mag és az automatikus szüneteltetés késleltetését is, ha módosítani szeretné az alapértelmezett értékeket. A következő táblázat a paraméterek elérhető értékeit tartalmazza.

   |Paraméter|Értékek megválasztása|Alapértelmezett érték|
   |---|---|---|---|
   |Minimális virtuális mag|A maximális virtuális mag függ – lásd az [erőforrás-korlátokat](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 virtuális mag|
   |Automatikus szüneteltetés késleltetése|Minimum: 60 perc (1 óra)<br>Maximum: 10080 perc (7 nap)<br>Növekmények: 10 perc<br>Automatikus szüneteltetés letiltása:-1|60 perc|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Új adatbázis létrehozása a kiszolgáló nélküli számítási szinten

Az alábbi példák egy új adatbázist hoznak létre a kiszolgáló nélküli számítási szinten.

#### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Lásd [: gyors útmutató: önálló adatbázis létrehozása Azure SQL Database a Azure Portal használatával](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>A PowerShell használata

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) használata

A T-SQL használatakor a rendszer az alapértelmezett értékeket alkalmazza a minimális virtuális mag és az automatikus szüneteltetés késleltetésére.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Részletekért lásd: [adatbázis létrehozása](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Adatbázis áthelyezése a kiépített számítási szintjéről a kiszolgáló nélküli számítási szintjére

Az alábbi példák egy adatbázist helyeznek át a kiépített számítási szinten a kiszolgáló nélküli számítási szintjére.

#### <a name="use-powershell"></a>A PowerShell használata


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) használata

A T-SQL használatakor a rendszer az alapértelmezett értékeket alkalmazza a minimális virtuális mag, és automatikusan szünetelteti a késleltetést.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Részletekért lásd: [adatbázis módosítása](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Adatbázis áthelyezése a kiszolgáló nélküli számítási szintjéből a kiépített számítási szinten

A kiszolgáló nélküli adatbázisok a kiépített számítási rétegekbe helyezhetők, ugyanúgy, mint a kiépített számítási adatbázisok kiszolgáló nélküli számítási szintjére való áthelyezése.

## <a name="modifying-serverless-configuration"></a>Kiszolgáló nélküli konfiguráció módosítása

### <a name="use-powershell"></a>A PowerShell használata

A maximális vagy a minimális virtuális mag, valamint az automatikus szüneteltetés késleltetésének módosítása a PowerShell [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsával történik `MaxVcore` , a, `MinVcore` és `AutoPauseDelayInMinutes` argumentumok használatával.

### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

A maximális vagy a minimális virtuális mag módosítása, valamint az automatikus szüneteltetés késleltetése az az [SQL db Update](/cli/azure/sql/db#az-sql-db-update) paranccsal hajtható végre az Azure CLI-ben a `capacity` , `min-capacity` , és `auto-pause-delay` argumentumokkal.


## <a name="monitoring"></a>Figyelés

### <a name="resources-used-and-billed"></a>Felhasznált erőforrások és számlázás

A kiszolgáló nélküli adatbázisok erőforrásai az alkalmazáscsomag, az SQL-példány és a felhasználói erőforráskészlet-entitások beágyazásával vannak beágyazva.

#### <a name="app-package"></a>Alkalmazáscsomag

Az alkalmazáscsomag egy adatbázis külső erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten van-e. Az alkalmazáscsomag tartalmazza azokat az SQL-példányokat és külső szolgáltatásokat, amelyek közösen tartalmazzák az adatbázis által SQL Database-ben használt összes felhasználói és rendszererőforrást. Külső szolgáltatások például az R és a teljes szöveges keresés. Az SQL-példány általában a teljes erőforrás-kihasználtságot uralja az alkalmazáscsomag között.

#### <a name="user-resource-pool"></a>Felhasználói erőforráskészlet

A felhasználói erőforráskészlet egy adatbázis belső erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten van-e. A felhasználói erőforráskészlet hatóköre CPU és IO a DDL-lekérdezések által generált felhasználói számítási feladatokhoz, például LÉTREHOZÁSi és MÓDOSÍTÁSi, valamint DML-lekérdezések, például SELECT, INSERT, UPDATE és DELETE. Ezek a lekérdezések általában a kihasználtság legjelentősebb hányadát jelentik az alkalmazáscsomag keretében.

### <a name="metrics"></a>Mérőszámok

A kiszolgáló nélküli adatbázisok alkalmazáscsomag és felhasználói készlete erőforrás-használatának figyelésére szolgáló mérőszámok az alábbi táblázatban láthatók:

|Entitás|Metric|Leírás|Egység|
|---|---|---|---|
|Alkalmazáscsomag|app_cpu_percent|Az alkalmazás által az alkalmazáshoz engedélyezett maximális virtuális mag képest használt virtuális mag százalékos aránya.|Százalék|
|Alkalmazáscsomag|app_cpu_billed|A jelentési időszak során az alkalmazás számára számlázott számítási mennyiség. Az ebben az időszakban fizetett összeg a metrika terméke és a virtuális mag egység ára. <br><br>A metrika értékeit a rendszer a felhasznált CPU és a másodpercenként felhasznált memória maximális számának időbeli összesítésével határozza meg. Ha a felhasznált mennyiség kevesebb, mint a minimum virtuális mag és a minimális memória által beállított minimális mennyiség, akkor a kiosztott minimális összegért kell fizetnie.Ha a CPU-t számlázási célokra szeretné összehasonlítani a memóriával, a memória a virtuális mag-egységekbe van normalizálva azáltal, hogy a memória mennyiségét GB-ban, virtuális mag 3 GB-onként átméretezni.|Virtuális mag másodpercben|
|Alkalmazáscsomag|app_memory_percent|Az alkalmazás által az alkalmazás számára engedélyezett maximális memóriához képest használt memória százalékos aránya.|Százalék|
|Felhasználói készlet|cpu_percent|A felhasználói munkaterhelés által a felhasználói munkaterhelés számára engedélyezett maximális virtuális mag képest felhasznált virtuális mag százalékos aránya.|Százalék|
|Felhasználói készlet|data_IO_percent|A felhasználói munkaterhelés által a felhasználói munkaterhelés számára engedélyezett maximális IOPS képest IOPS adatok százalékos aránya.|Százalék|
|Felhasználói készlet|log_IO_percent|A felhasználói munkaterhelés által használt log MB/s százalékos aránya a felhasználói munkaterhelés számára engedélyezett maximális log MB/s értékhez képest.|Százalék|
|Felhasználói készlet|workers_percent|A felhasználói munkaterhelés által a felhasználói munkaterhelés számára engedélyezett maximális munkaterheléshez képest használt munkavégzők százalékos aránya.|Százalék|
|Felhasználói készlet|sessions_percent|A felhasználói munkaterhelés által használt munkamenetek százalékos aránya a felhasználói munkaterhelés számára engedélyezett maximális munkamenetekhez képest.|Százalék|

### <a name="pause-and-resume-status"></a>Szüneteltetés és folytatás állapota

A Azure Portal az adatbázis állapota megjelenik a kiszolgáló áttekintés ablaktábláján, amely felsorolja a benne lévő adatbázisokat. Az adatbázis állapota az adatbázis áttekintés paneljén is megjelenik.

A következő parancsok használatával kérdezheti le egy adatbázis szüneteltetési és folytatási állapotát:

#### <a name="use-powershell"></a>A PowerShell használata

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Erőforráskorlátok

Az erőforrás-korlátokat lásd: [kiszolgáló nélküli számítási szintek](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Számlázás

A számlázott számítások mennyisége a felhasznált CPU és a másodpercenként felhasznált memória maximális mennyisége. Ha a felhasznált CPU mennyisége és a felhasznált memória kevesebb, mint az egyesek számára kiépített minimális mennyiség, akkor a kiosztott mennyiség számlázásra kerül. Ha a CPU-t számlázási célokra szeretné összehasonlítani a memóriával, a memória a virtuális mag-egységekbe van normalizálva azáltal, hogy a memória mennyiségét GB-ban, virtuális mag 3 GB-onként átméretezni.

- **Számlázott erőforrás**: CPU és memória
- **Számlázott összeg**: virtuális mag egység ára * Max (min virtuális mag, virtuális mag felhasznált, minimális memória gb * 1/3, memória GB használatban * 1/3) 
- **Számlázási gyakoriság**: másodpercenként

A virtuális mag egység díja virtuális mag/másodperc. Egy adott régióban az adott egység áraira vonatkozó [Azure SQL Database díjszabási oldalán](https://azure.microsoft.com/pricing/details/sql-database/single/) talál további információt.

A számlázott számítások mennyiségét a következő metrika teszi elérhetővé:

- **Metrika**: App_cpu_billed (virtuális mag másodperc)
- **Definíció**: Max (min. virtuális mag, felhasznált virtuális mag, minimális memória gb * 1/3, memória GB használatban * 1/3)
- **Jelentéskészítés gyakorisága**: percenként

Ezt a mennyiséget másodpercenként számítjuk ki, és 1 percenként összesítjük.

### <a name="minimum-compute-bill"></a>Minimális számítási számla

Ha egy kiszolgáló nélküli adatbázis szüneteltetve van, akkor a számítási számla nulla.  Ha egy kiszolgáló nélküli adatbázis nincs szüneteltetve, akkor a minimális számítási számla nem kevesebb, mint a Max (min. virtuális mag, minimális memória GB * 1/3) alapján megadott virtuális mag mennyisége.

Angol nyelvű Példák:

- Tegyük fel, hogy egy kiszolgáló nélküli adatbázis nincs szüneteltetve, és 8 maximális virtuális mag és 1 perc 3,0 GB-os memória-virtuális mag van konfigurálva.  Ezt követően a minimális számítási számla a Max (1 virtuális mag, 3,0 GB * 1 virtuális mag/3 GB) = 1 virtuális mag alapul.
- Tegyük fel, hogy egy kiszolgáló nélküli adatbázis nincs szüneteltetve, és 4 maximális virtuális mag és 0,5 perces virtuális mag van konfigurálva, amely 2,1 GB-os memóriának felel meg.  Ezután a minimális számítási számla a Max (0,5 virtuális mag, 2,1 GB * 1 virtuális mag/3 GB) = 0,7 virtuális mag alapul.

A kiszolgáló nélküli [Azure SQL Database árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=sql-database) használatával meghatározható a minimális memória, amely a maximális és a minimális virtuális mag-érték alapján állítható be.  Szabályként, ha a minimálisan konfigurált virtuális mag nagyobb, mint 0,5 virtuális mag, akkor a minimális számítási számla független a minimálisan konfigurált memóriától, és csak a beállított minimális virtuális mag számától függ.

### <a name="example-scenario"></a>Példaforgatókönyv

Vegyünk egy 1 perces virtuális mag és 4 maximális virtuális mag konfigurált kiszolgáló nélküli adatbázist.  Ez körülbelül 3 GB-os memória-és 12 GB-os maximális memória-értéknek felel meg.  Tegyük fel, hogy az automatikus szüneteltetés késleltetése 6 óra, az adatbázis-munkaterhelés pedig aktív a 24 órás időszak első 2 órájában, ellenkező esetben inaktív.    

Ebben az esetben az adatbázist a számítás és a tárolás után számítjuk fel az első 8 órában.  Annak ellenére, hogy az adatbázis inaktív a második óra után, a rendszer továbbra is kiszámlázza a számítást a következő 6 órában az adatbázis online állapotában kiosztott minimális számítási kapacitás alapján.  A rendszer csak a 24 órás időszak hátralévő részében számlázza a tárterületet, amíg az adatbázis szüneteltetve van.

Pontosabban, a példában szereplő számítási számla kiszámítása a következőképpen történik:

|Időtartam|másodpercenként használt virtuális mag|GB használt másodpercenként|Számítási dimenzió számlázása|Virtuális mag másodpercben elszámolt időtartam|
|---|---|---|---|---|
|0:00-1:00|4|9|használt virtuális mag|4 virtuális mag * 3600 másodperc = 14400 virtuális mag másodperc|
|1:00-2:00|1|12|Felhasznált memória|12 GB * 1/3 * 3600 másodperc = 14400 virtuális mag másodperc|
|2:00-8:00|0|0|Minimális memória kiépítve|3 GB * 1/3 * 21600 másodperc = 21600 virtuális mag másodperc|
|8:00-24:00|0|0|A felfüggesztett számítási díj nem számítható fel|0 virtuális mag másodperc|
|24 órán át számlázott virtuális mag-másodpercek összesen||||50400 virtuális mag másodperc|

Tegyük fel, hogy a számítási egység ára $0.000145/virtuális mag/Second.  Ezt követően a 24 órás időszakra kiszámított számítás a számítási egység árának és a virtuális mag másodpercben mért kiszámlázásának szorzata: $0.000145/virtuális mag/Second * 50400 virtuális mag másodperc ~ $7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid Benefit és fenntartott kapacitás

A Azure Hybrid Benefit (AHB) és a fenntartott kapacitási kedvezmények nem vonatkoznak a kiszolgáló nélküli számítási szintjére.

## <a name="available-regions"></a>Elérhető régiók

A kiszolgáló nélküli számítási csomag világszerte elérhető, kivéve a következő régiókat: Kelet-Kína, Észak-Kína, Közép-Németország, Kelet-Németország és US Gov Central (Iowa).

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg a rövid útmutató [: önálló adatbázis létrehozása Azure SQL Database a Azure Portal használatával](single-database-create-quickstart.md)című témakört.
- Az erőforrások korlátaival kapcsolatban lásd: [kiszolgáló nélküli számítási keret erőforrás-korlátai](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

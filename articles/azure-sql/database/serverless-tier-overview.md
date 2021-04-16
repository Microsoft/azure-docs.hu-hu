---
title: Kiszolgáló nélküli számítási szint
description: Ez a cikk ismerteti az új kiszolgáló nélküli számítási szintet, és összehasonlítja azt a meglévő kiépített számítási Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 4/15/2021
ms.openlocfilehash: ea9d5a5c39bf73ede2391c586f09dd95ff79b63c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531980"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database nélküli
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A kiszolgáló nélküli szint az Azure SQL Database-ben az önálló adatbázisok számítási szintje, amely automatikusan skálázza a számításokat a számítási feladatok igényei alapján, és a másodpercenként felhasznált számítási mennyiségek szerint számláz. A kiszolgáló nélküli számítási szint emellett automatikusan szünetelteti az adatbázisokat az inaktív időszakok során, amikor csak a tárterület használati díját számlázzuk ki, és automatikusan folytatja az adatbázisok működését, amikor ismét előfordulnak tevékenységek.

## <a name="serverless-compute-tier"></a>Kiszolgáló nélküli számítási szint

A szolgáltatásban található egyetlen adatbázis kiszolgáló nélküli számítási Azure SQL Database a számítási automatikus skálázás tartománya és az automatikus szüneteltetési késleltetés paramétere. Ezeknek a paramétereknek a konfigurációja alakítja az adatbázis-teljesítményt és a számítási költségeket.

![kiszolgáló nélküli számlázás](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Teljesítménykonfiguráció

- A **virtuális magok minimális és** maximális **magszáma** konfigurálható paraméterek, amelyek meghatározzák az adatbázishoz elérhető számítási kapacitás tartományát. A memória- és I/O-korlátok arányosak a megadott virtuálismag-tartománysal.  
- Az **automatikus szüneteltetési késleltetés** egy konfigurálható paraméter, amely meghatározza, hogy az adatbázisnak mikor kell inaktívnak lennie az automatikus szüneteltetés előtt. Az adatbázis automatikusan folytatódik a következő bejelentkezés vagy más tevékenység bekövetkeztekor.  Az automatikus szüneteltetés is letiltható.

### <a name="cost"></a>Költségek

- A kiszolgáló nélküli adatbázisok költsége a számítási és tárolási költségek összegzése.
- Ha a számítási használat a konfigurált minimális és maximális korlát között van, a számítási költség a virtuális magon és a felhasznált memórián alapul.
- Ha a számítási kihasználtság a konfigurált minimális korlátok alatt van, a számítási költség a minimális virtuális magok és a minimálisan konfigurált memória alapján lesz kiszámítva.
- Az adatbázis szüneteltetése esetén a számítási költség nulla, és csak tárolási költségek merülnek fel.
- A tárolási költségek meghatározása a kiépített számítási szinthez hasonló módon megegyezik.

További költségadatokért lásd: [Számlázás.](serverless-tier-overview.md#billing)

## <a name="scenarios"></a>Forgatókönyvek

A kiszolgáló nélküli adatbázisok ár–teljesítmény aránya az időszakos, kiszámíthatatlan használati mintázatú önálló adatbázisokhoz van optimalizálva, ahol elfogadható némi késés a növekvő mennyiségű számítási feladatok elvégzésében a tétlen időszakok után. Ezzel szemben a kiépített számítási szint ár–teljesítmény aránya olyan önálló adatbázisokra vagy rugalmas készletekbe rendezett több adatbázisra van optimalizálva, amelyeknél a magasabb átlagos kihasználtság miatt nem elfogadható a késés, amikor felpörög a számítási feladatok száma.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Kiszolgáló nélküli számításhoz jól használható forgatókönyvek

- Időszakos, kiszámíthatatlan használati mintákkal és inaktivitási időszakokkal időszakos, kiszámíthatatlan használati mintákkal és alacsonyabb átlagos számítási kihasználtságtal álló adatbázisok.
- A kiépített számítási szint egyetlen adatbázisa, amelyek gyakran újra vannak skálázva, és azok az ügyfelek, akik inkább a szolgáltatásnak szeretnék delegálni a számítási átméretezést.
- Új, használati előzmények nélküli adatbázisokat, ahol a számítási méretezés nehezen vagy egyáltalán nem becsülható meg, mielőtt üzembe SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>A kiépített számításhoz jól használható forgatókönyvek

- Egységes adatbázisok hagyományosabb, kiszámíthatóbb használati mintákkal és magasabb átlagos számítási kihasználtságtal.
- Olyan adatbázisok esetén, amelyek nem tolerálják a teljesítménybeli cseréket, mert gyakoribb memóriavágást vagy a szüneteltetett állapotból való visszaesés késését eredményezik.
- Több adatbázis időszakos, kiszámíthatatlan használati mintákkal, amelyek rugalmas készletekbe konszolidálhatóak a jobb ár-teljesítmény optimalizálás érdekében.

## <a name="comparison-with-provisioned-compute-tier"></a>Összehasonlítás a kiépített számítási szintekkel

Az alábbi táblázat összefoglalja a kiszolgáló nélküli számítási szint és a kiépített számítási szint közötti különbségeket:

| | **Kiszolgáló nélküli számítástechnika** | **Kiépített számítás** |
|:---|:---|:---|
|**Adatbázis-használati minta**| Időszakos, kiszámíthatatlan használat, alacsonyabb átlagos számítási kihasználtságtal. | Rendszeresebb használati minták magasabb átlagos számítási kihasználtság mellett az idő alatt, vagy több, rugalmas készleteket használó adatbázis.|
| **Teljesítménykezelési erőfeszítések** |Alacsonyabb|Magasabb|
|**Számítás skálázása**|Automatikus|Kézi|
|**Számítási válaszképesség**|Inaktív időszakok utáni alsóbb szintű|Azonnali|
|**Számlázási részletesség**|Másodpercenként|Óránként|

## <a name="purchasing-model-and-service-tier"></a>Vásárlási modell és szolgáltatási szint

SQL Database a kiszolgáló nélküli virtuálismag-alapú vásárlási modellben általános célú a virtuálismag-alapú vásárlási modell 5. generációs hardverének általános célú támogatott.

## <a name="autoscaling"></a>Automatikus skálázás

### <a name="scaling-responsiveness"></a>A válaszképesség skálázása

Általánosságban elmondható, hogy a kiszolgáló nélküli adatbázisok olyan gépen futnak, amely elegendő kapacitással rendelkezik ahhoz, hogy megszakítás nélkül kielégítse az erőforrásigényt a maximális virtuális magérték által meghatározott korlátokon belül kért számítási mennyiség esetén. Időnként a terheléselosztás automatikusan megtörténik, ha a gép néhány percen belül nem tudja kielégíteni az erőforrásigényt. Ha például az erőforrásigény 4 virtuális mag, de csak 2 virtuális mag áll rendelkezésre, akkor a terheléselosztás akár néhány percet is igénybe vehet, mielőtt 4 virtuális magot ad meg. Az adatbázis online állapotban marad a terheléselosztás során, kivéve azt a rövid időszakot, amely a művelet végén történik a kapcsolatok megszakadása esetén.

### <a name="memory-management"></a>Memóriakezelés

A kiszolgáló nélküli adatbázisok memóriája gyakrabban van felszabadítva, mint a kiépített számítási adatbázisokhoz. Ez a viselkedés fontos a költségek kiszolgáló nélküli szabályozásához, és hatással lehet a teljesítményre.

#### <a name="cache-reclamation"></a>Gyorsítótár-visszahelyezettség

A kiépített számítási adatbázisokkal ellentétben az SQL-gyorsítótárból származó memória vissza lesz igénylésre egy kiszolgáló nélküli adatbázisból, ha a processzor vagy az aktív gyorsítótár kihasználtsága alacsony.

- Az aktív gyorsítótár kihasználtsága alacsonynak minősül, ha a legutóbb használt gyorsítótár-bejegyzések teljes mérete egy adott időszakban egy küszöbérték alá esik.
- A gyorsítótár-visszahelyezés aktiválása esetén a célgyorsítótár mérete fokozatosan az előző méret töredékére csökken, és a visszahelyezés csak akkor folytatódik, ha a használat alacsony marad.
- Gyorsítótár-visszahelyezettség esetén a gyorsítótár-bejegyzések kiürítéskor való kiválasztásának szabályzata megegyezik a kiépített számítási adatbázisok kiválasztási szabályzatával, ha nagy a memóriabeli nyomás.
- A gyorsítótár mérete soha nem csökken a minimálisan konfigurálható virtuális magok által meghatározott minimális memóriakorlát alá.

A kiszolgáló nélküli és a kiépített számítási adatbázisokban is kiüríthetőek a gyorsítótár-bejegyzések, ha az összes rendelkezésre álló memória ki van használva.

Vegye figyelembe, hogy ha a processzor kihasználtsága alacsony, az aktív gyorsítótár kihasználtsága a használati mintától függően magas maradhat, és megakadályozhatja a memória-visszahelyezettségeket.  Emellett a korábbi felhasználói tevékenységekre reagáló rendszeres háttérfolyamatok miatt további késések is bekövetkezhetnek, miután a felhasználói tevékenység leáll a memória-visszagenerálás előtt.  A törlési műveletek és a QDS-tisztítási feladatok például olyan szellemrekordokat hoznak létre, amelyek törlésre vannak megjelölve, de addig nem törlődnek fizikailag, amíg le nem fut a szellemtörlési folyamat, amely magában foglalhatja az adatlapok gyorsítótárba való beolvasását.

#### <a name="cache-hydration"></a>Gyorsítótárhidratálás

Az SQL-gyorsítótár növekszik, ahogy az adatok a kiépített adatbázisokhoz hasonló módon és sebességgel vannak lekérve a lemezről. Ha az adatbázis foglalt, a gyorsítótár korlátlanul nőhet a maximális memóriakorlátig.

## <a name="auto-pausing-and-auto-resuming"></a>Automatikus szüneteltetés és automatikus szüneteltetés

### <a name="auto-pausing"></a>Automatikus szüneteltetés

Az automatikus szüneteltetés akkor aktiválódik, ha az automatikus szüneteltetés késleltetésének időtartamára az alábbi feltételek mind teljesülnek:

- Szám-munkamenetek = 0
- CPU = 0 a felhasználói készletben futó felhasználói számítási feladatokhoz

Lehetőség van az automatikus szüneteltetés letiltására, ha szeretné.

Az alábbi funkciók nem támogatják az automatikus szüneteltetés, de az automatikus skálázást.  Ha az alábbi szolgáltatások bármelyikét használja, az automatikus szüneteltetés le lesz tiltva, és az adatbázis az adatbázis inaktivitási időtartamától függetlenül online állapotban marad:

- Georeplikáció (aktív georeplikáció és automatikus feladatátvételi csoportok).
- Biztonsági másolatok hosszú távú megőrzése (LTR).
- Az SQL-adatszinkronizálásban használt szinkronizálási adatbázis.  A szinkronizálási adatbázisoktól eltérően a központ- és tagadatbázisok támogatják az automatikus szüneteltetéseket.
- DNS-aliasok
- A rugalmas feladatokban (előzetes verzió) használt feladat-adatbázis.

Az automatikus szüneteltetés ideiglenesen le van tiltva bizonyos olyan szolgáltatásfrissítések telepítése során, amelyek megkövetelik, hogy az adatbázis online állapotban legyen.  Ilyen esetekben az automatikus szüneteltetés a szolgáltatásfrissítés befejezése után ismét engedélyezett lesz.

### <a name="auto-resuming"></a>Automatikus visszaesés

Az automatikus indítás akkor aktiválódik, ha az alábbi feltételek bármelyike bármikor teljesül:

|Szolgáltatás|Automatikus folytatás eseményindító|
|---|---|
|Hitelesítés és engedélyezés|Bejelentkezés|
|Fenyegetések észlelése|A fenyegetésészlelési beállítások engedélyezése/letiltása az adatbázis vagy a kiszolgáló szintjén.<br>Fenyegetésészlelési beállítások módosítása az adatbázis vagy a kiszolgáló szintjén.|
|Adatfelderítés és besorolás|Bizalmasság címkék hozzáadása, módosítása, törlése vagy megtekintése|
|Naplózás|Naplórekordok megtekintése.<br>A naplózási szabályzat frissítése vagy megtekintése.|
|Adatmaszkolás|Adatmaszkolási szabályok hozzáadása, módosítása, törlése vagy megtekintése|
|Transzparens adattitkosítás|A transzparens adattitkosítás állapotának megtekintése|
|Sebezhetőségi felmérés|Alkalmi vizsgálatok és rendszeres vizsgálatok, ha engedélyezve van|
|Lekérdezési (teljesítménybeli) adattár|Lekérdezéstár beállításainak módosítása vagy megtekintése|
|Teljesítménnyel kapcsolatos javaslatok|Teljesítménnyel kapcsolatos javaslatok megtekintése vagy alkalmazása|
|Automatikus hangolás|Automatikus hangolási javaslatok alkalmazása és ellenőrzése, például automatikus indexelés|
|Adatbázis-másolás|Adatbázis létrehozása másolatként.<br>Exportálás BACPAC-fájlba.|
|SQL-adatszinkronizálás|Konfigurálható ütemezés szerint vagy manuálisan futtatott vagy manuálisan futtatott hub- és tagadatbázisok közötti szinkronizálás|
|Bizonyos adatbázis-metaadatok módosítása|Új adatbáziscímkék hozzáadása.<br>A virtuális magok maximális, minimális magszámának vagy automatikus késleltetésének módosítása.|
|SQL Server Management Studio (SSMS)|Ha a 18.1-esnél korábbi SSMS-verziókat használ, és új lekérdezési ablakot nyit meg a kiszolgálón található adatbázisokhoz, a rendszer folytatja az ugyanazon a kiszolgálón automatikusan szüneteltetett adatbázisokat. Ez a viselkedés nem fordul elő az SSMS 18.1-es vagy újabb verziójának használata esetén.|

A fent felsorolt műveletek bármelyikét végző figyelési, felügyeleti vagy egyéb megoldások aktiválják az automatikus továbbvezetést.

Az automatikus indítás bizonyos szolgáltatásfrissítések telepítésekor is aktiválódik, amelyek megkövetelik, hogy az adatbázis online állapotban legyen.

### <a name="connectivity"></a>Kapcsolatok

Ha egy kiszolgáló nélküli adatbázis fel van függesztve, akkor az első bejelentkezés folytatja az adatbázist, és hibaüzenetet ad vissza, amely szerint az adatbázis nem érhető el a 40613-as hibakóddal. Az adatbázis folytatása után újra meg kell hozni a bejelentkezést a kapcsolat létesítéséhez. A kapcsolat-újrapróbálkozási logikával nem szükséges módosítani az adatbázis-ügyfeleket.

### <a name="latency"></a>Késés

A kiszolgáló nélküli adatbázisok automatikus folytatása és automatikus szüneteltetése általában 1 percet, az automatikus szüneteltetés pedig 1–10 percet tart.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Ügyfél által felügyelt transzparens adattitkosítás (BYOK)

Ha az ügyfél [által felügyelt](transparent-data-encryption-byok-overview.md) transzparens adattitkosítást (BYOK) használja, és a kiszolgáló nélküli adatbázis automatikusan szünetel a kulcs törlésekor vagy visszavonásakor, akkor az adatbázis automatikusan szüneteltetett állapotban marad.  Ebben az esetben az adatbázis következő folytatása után az adatbázis körülbelül 10 percen belül elérhetetlenné válik.  Ha az adatbázis elérhetetlenné válik, a helyreállítási folyamat megegyezik a kiépített számítási adatbázisok helyreállítási folyamatával.  Ha a kiszolgáló nélküli adatbázis a kulcs törlésekor vagy visszavonásakor online állapotban van, akkor az adatbázis is körülbelül 10 percen belül elérhetetlenné válik a kiépített számítási adatbázisokhoz hasonló módon.

## <a name="onboarding-into-serverless-compute-tier"></a>A kiszolgáló nélküli számítási szintbe való be- és be-

Egy új adatbázis létrehozása vagy egy meglévő adatbázis kiszolgáló nélküli számítási szintre való áthelyezésének mintája ugyanazt a mintát követi, mint egy új adatbázis létrehozása a kiépített számítási szinten, és a következő két lépésből áll.

1. Adja meg a szolgáltatási célt. A szolgáltatási cél a szolgáltatási szintet, a hardvergenerációt és a maximális virtuális magszámot írja elő. Szolgáltatási célokkal kapcsolatos beállításokért lásd: [Kiszolgáló nélküli erőforráskorlátok](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Megadhatja a minimális virtuális magszámot és az automatikus késleltetést az alapértelmezett értékek módosítása érdekében. Az alábbi táblázat ezeknek a paramétereknek az elérhető értékeit mutatja be.

   |Paraméter|Értékválasztások|Alapértelmezett érték|
   |---|---|---|---|
   |Virtuális magok minimális|A konfigurált virtuális magok maximális értékétől függ – lásd: [erőforráskorlátok.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)|0,5 virtuális mag|
   |Automatikus kitöltés késleltetése|Minimum: 60 perc (1 óra)<br>Maximum: 10080 perc (7 nap)<br>Növekmények: 10 perc<br>Automatikus kitöltés letiltása: -1|60 perc|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Új adatbázis létrehozása a kiszolgáló nélküli számítási szinten

Az alábbi példák egy új adatbázist hoznak létre a kiszolgáló nélküli számítási szinten.

#### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Lásd: [Rövid útmutató: Egyetlen](single-database-create-quickstart.md)adatbázis létrehozása a Azure SQL Database a Azure Portal.


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

T-SQL használata esetén a rendszer az alapértelmezett értékeket alkalmazza a minimális virtuális magok és az automatikus időparasztás esetén.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Részletekért lásd: [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Adatbázis áthelyezése a kiépített számítási szintről a kiszolgáló nélküli számítási szintre

Az alábbi példák egy adatbázist áthelyeznek a kiépített számítási szintről a kiszolgáló nélküli számítási szintre.

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

T-SQL használata esetén a rendszer az alapértelmezett értékeket alkalmazza a minimális virtuális magok és az automatikus szüneteltetés késleltetésére.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Részletekért lásd: [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Adatbázis áthelyezése a kiszolgáló nélküli számítási szintről a kiépített számítási szintre

A kiszolgáló nélküli adatbázisokat úgy lehet áthelyezni egy kiépített számítási szintre, mint a kiépített számítási adatbázisokat egy kiszolgáló nélküli számítási szintre.

## <a name="modifying-serverless-configuration"></a>Kiszolgáló nélküli konfiguráció módosítása

### <a name="use-powershell"></a>A PowerShell használata

A virtuális magok maximális vagy minimális magszámának módosítása és az automatikus késleltetés a [PowerShell Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsával, a `MaxVcore` , és `MinVcore` `AutoPauseDelayInMinutes` argumentumokkal végezhető el.

### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

A virtuális magok maximális vagy minimális magszámának módosítása és az automatikus késleltetés az [Azure CLI-ben](/cli/azure/sql/db#az-sql-db-update) az az sql db update paranccsal, a `capacity` , és `min-capacity` `auto-pause-delay` argumentumokkal végezhető el.


## <a name="monitoring"></a>Figyelés

### <a name="resources-used-and-billed"></a>Felhasznált és számlázt erőforrások

A kiszolgáló nélküli adatbázisok erőforrásait az alkalmazáscsomag, az SQL-példány és a felhasználói erőforráskészlet entitásai lik be.

#### <a name="app-package"></a>Alkalmazáscsomag

Az alkalmazáscsomag az adatbázis külső legtöbb erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten van-e. Az alkalmazáscsomag tartalmazza az SQL-példányt és a külső szolgáltatásokat, amelyek együttesen az adatbázis által használt összes felhasználói és rendszererőforrást együtt SQL Database. Külső szolgáltatások például az R és a teljes szöveges keresés. Az ALKALMAZÁScsomag általános erőforrás-kihasználtsága általában az SQL-példányban van meghatározó.

#### <a name="user-resource-pool"></a>Felhasználói erőforráskészlet

A felhasználói erőforráskészlet az adatbázis legbelső erőforrás-kezelési határa, függetlenül attól, hogy az adatbázis kiszolgáló nélküli vagy kiépített számítási szinten van-e. A felhasználói erőforráskészlet hatóköre a DDL-lekérdezések, például a CREATE és alter és DML lekérdezések, például a SELECT, INSERT, UPDATE és DELETE felhasználói számítási feladatok processzor- és I/O-hatókörei. Ezek a lekérdezések általában az alkalmazáscsomagon belüli kihasználtság legnagyobb részét képviselik.

### <a name="metrics"></a>Mérőszámok

Az alkalmazáscsomag és a kiszolgáló nélküli adatbázis felhasználói készletének erőforrás-használatának monitorozásához szükséges metrikák a következő táblázatban vannak felsorolva:

|Entitás|Metric|Leírás|Egység|
|---|---|---|---|
|Alkalmazáscsomag|app_cpu_percent|Az alkalmazás által használt virtuális magok százalékos aránya az alkalmazás számára engedélyezett virtuális magok maximális értékéhez viszonyítva.|Százalék|
|Alkalmazáscsomag|app_cpu_billed|Az alkalmazásnak a jelentéskészítési időszakban számlázható számítási mennyisége. Az ebben az időszakban kifizetett összeg ennek a metrikának és a virtuális mag egységárának a terméke. <br><br>A metrika értékeinek meghatározása az idő múlása alapján a másodpercenként felhasznált cpu és memória maximális száma alapján van meghatározva. Ha a felhasznált mennyiség kisebb a minimális virtuális magok és a minimális memória által kiépített minimális mennyiségnél, akkor a rendszer a minimálisan kiépített mennyiséget számláz.Annak érdekében, hogy a processzor és a memória összehasonlítható legyen számlázási célokból, a memória virtuális magonként 3 GB-os átméretezéssel virtuális magonkénti egységre van normalizálva.|Virtuális mag másodperc|
|Alkalmazáscsomag|app_memory_percent|Az alkalmazás által használt memória százalékos aránya az alkalmazás számára engedélyezett maximális memóriához viszonyítva.|Százalék|
|Felhasználói készlet|cpu_percent|A felhasználói számítási feladatok által használt virtuális magok százalékos aránya a felhasználói számítási feladatokhoz engedélyezett maximális virtuális maghoz viszonyítva.|Százalék|
|Felhasználói készlet|data_IO_percent|A felhasználói számítási feladat által használt adat IOPS százalékos aránya a felhasználói számítási feladatokhoz engedélyezett maximális adat IOPS-értékhez viszonyítva.|Százalék|
|Felhasználói készlet|log_IO_percent|A felhasználói munkaterhelés által használt napló MB/s százalékos aránya a felhasználói számítási feladatokhoz engedélyezett maximális naplózási MB/s értékhez viszonyítva.|Százalék|
|Felhasználói készlet|workers_percent|A felhasználói számítási feladatok által használt dolgozók százalékos aránya a felhasználói számítási feladatokhoz engedélyezett maximális munkaterheléshez viszonyítva.|Százalék|
|Felhasználói készlet|sessions_percent|A felhasználói számítási feladat által használt munkamenetek százalékos aránya a felhasználói számítási feladatokhoz engedélyezett maximális munkamenetekhez viszonyítva.|Százalék|

### <a name="pause-and-resume-status"></a>Szüneteltetés és folytatás állapota

A Azure Portal az adatbázis állapota megjelenik a benne található adatbázisokat felsoroló kiszolgáló áttekintő paneljén. Az adatbázis állapota az adatbázis áttekintő paneljén is megjelenik.

Az alábbi parancsokkal lekérdezheti egy adatbázis szüneteltetési és folytatási állapotát:

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

Az erőforráskorlátokat lásd: [kiszolgáló nélküli számítási szint.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)

## <a name="billing"></a>Számlázás

A kiszámlázható számítási mennyiség a másodpercenként felhasznált cpu és memória maximális mennyisége. Ha a felhasznált CPU-mennyiség és a felhasznált memória mennyisége kisebb, mint az egyes erőforrásokhoz kiépített minimális mennyiség, akkor a rendszer a kiépített mennyiséget számláz. Annak érdekében, hogy a processzor és a memória összehasonlítható legyen számlázási célokból, a memória virtuális magok egységére van normalizálva a memória GB-ban való átméretezése virtuális magonként 3 GB-ral.

- **Számlázt erőforrás:** CPU és memória
- **Számlázható** mennyiség: virtuálismag-egységár * maximum (minimális virtuális mag, felhasznált virtuális magok, minimális memória GB * 1/3, felhasznált memória GB * 1/3) 
- **Számlázási gyakoriság:** Másodpercenként

A virtuális mag egységára a másodpercenkénti virtuális magonkénti költség. Az adott [Azure SQL Database adott](https://azure.microsoft.com/pricing/details/sql-database/single/) egységáraiért tekintse meg a díjszabást.

A kiszámlázható számítási mennyiséget a következő metrika teszi elérhetővé:

- **Metrika:** app_cpu_billed (virtuális mag másodperc)
- **Definíció:** maximum (minimális virtuális mag, felhasznált virtuális magok, minimális memória GB * 1/3, felhasznált memória GB * 1/3)
- **Jelentéskészítés gyakorisága:** Percenként

A rendszer másodpercenként kiszámítja ezt a mennyiséget, és 1 percen keresztül összesíti.

### <a name="minimum-compute-bill"></a>Minimális számítási számla

Ha egy kiszolgáló nélküli adatbázis fel van függesztve, akkor a számítási számla nulla.  Ha a kiszolgáló nélküli adatbázis nincs szüneteltetve, akkor a minimális számítási számla nem kisebb a virtuális magok maximális mennyisége alapján (minimális virtuális mag, minimális memória GB * 1/3).

Angol nyelvű Példák:

- Tegyük fel, hogy a kiszolgáló nélküli adatbázis nincs szüneteltetve, és legfeljebb 8 virtuális maggal és 1 perc 3,0 GB memóriával van konfigurálva.  Ezután a minimális számítási számla alapja a maximális (1 virtuális mag, 3,0 GB * 1 virtuális mag / 3 GB) = 1 virtuális mag.
- Tegyük fel, hogy a kiszolgáló nélküli adatbázis nincs szüneteltetve, és 4 maximális virtuális maggal és 0,5 perc virtuális maggal van konfigurálva, amely 2,1 GB perc memóriának megfelelő.  Ezután a minimális számítási számla alapja a maximális (0,5 virtuális mag, 2,1 GB * 1 virtuális mag / 3 GB) = 0,7 virtuális mag.

A [](https://azure.microsoft.com/pricing/calculator/?service=sql-database) Azure SQL Database-díjkalkulátor használható a minimálisan konfigurálható memória megállapításához a konfigurált virtuális magok maximális és minimális száma alapján.  Ha a minimálisan konfigurált virtuális magok száma meghaladja a 0,5 virtuális magot, akkor a minimális számítási számla független a konfigurált minimális memóriától, és csak a minimálisan konfigurált virtuális magok számán alapul.

### <a name="example-scenario"></a>Példaforgatókönyv

Vezétsünk egy 1 perc virtuális maggal és 4 virtuális maggal konfigurált kiszolgáló nélküli adatbázist.  Ez körülbelül 3 GB perc memóriának és 12 GB maximális memóriának felel meg.  Tegyük fel, hogy az automatikus szüneteltetés késleltetése 6 órára van beállítva, és az adatbázis számítási feladatai egy 24 órás időszak első 2 órájában aktívak, más esetben inaktívak.    

Ebben az esetben az adatbázisra az első 8 óra során kell fizetni a számítási és tárolási erőforrásokért.  Annak ellenére, hogy az adatbázis a második óra után inaktív, a számításért a következő 6 órában továbbra is ki lesz számlázva az adatbázis online állapotban kiépített minimális számítási kapacitása alapján.  Az adatbázis szüneteltetése esetén a 24 órás időszak hátralévő részében csak a tárterület számlázása lesz kiszámlázva.

Pontosabban ebben a példában a számítási számla számítása a következőképpen történik:

|Időintervallum|Másodpercenként használt virtuális magok|Másodpercenként felhasznált GB|Kiszámlázva számítási dimenzió|A virtuális mag másodperces számlázása az időintervallumban|
|---|---|---|---|---|
|0:00-1:00|4|9|Használt virtuális magok|4 virtuális mag * 3600 másodperc = 14400 virtuális mag másodperc|
|1:00-2:00|1|12|Felhasznált memória|12 GB * 1/3 * 3600 másodperc = 14400 virtuális mag másodperc|
|2:00-8:00|0|0|Minimálisan kiépítve memória|3 GB * 1/3 * 21600 másodperc = 21600 virtuális mag másodperc|
|8:00-24:00|0|0|Nincs felszámlázva számítási idő szüneteltetve|0 virtuális mag másodperc|
|A 24 órán át számlázott virtuális magok összes másodperce||||50400 virtuális mag másodperc|

Tegyük fel, hogy a számítási egység ára 0,000145 USD/virtuális mag/másodperc.  Ezután a 24 órás időszakra számlázható számítás a számítási egység árának és a virtuális mag másodpercének a terméke, amely a következő: 0,000145 USD/virtuális mag/másodperc * 50400 virtuális mag másodperc ~ 7,31 USD

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid Benefit és fenntartott kapacitás

Azure Hybrid Benefit (AHB) és fenntartott kapacitásra vonatkozó kedvezmények nem vonatkoznak a kiszolgáló nélküli számítási szintre.

## <a name="available-regions"></a>Elérhető régiók

A kiszolgáló nélküli számítási szint világszerte elérhető, kivéve a következő régiókat: Kelet-Kína, Észak-Kína, Közép-Németország, Északkelet-Németország és US Gov (Iowa).

## <a name="next-steps"></a>Következő lépések

- Első lépésekért [lásd: Rövid útmutató: Egyetlen](single-database-create-quickstart.md)adatbázis létrehozása a Azure SQL Database a Azure Portal.
- Az erőforráskorlátokat [lásd: Kiszolgáló nélküli számítási szint erőforráskorlátok.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)

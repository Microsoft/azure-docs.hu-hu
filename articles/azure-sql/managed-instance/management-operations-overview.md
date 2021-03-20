---
title: Felügyeleti műveletek áttekintése
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg az Azure SQL felügyelt példányok kezelési műveletének időtartamát és ajánlott eljárásait.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 2da7311e61aa39be69a6a0a29eff686baaad7ebf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323192"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Az Azure SQL Managed Instance felügyeleti műveleteinek áttekintése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példánya olyan felügyeleti műveleteket biztosít, amelyek segítségével automatikusan telepítheti az új felügyelt példányokat, frissítheti a példány tulajdonságait, és törölheti a példányokat, ha már nincs rá szükség.

## <a name="what-are-management-operations"></a>Mik azok a felügyeleti műveletek?

A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány üzembe helyezése (új példány létrehozása)
- Példány frissítése (példány tulajdonságainak módosítása, például virtuális mag vagy fenntartott tároló)
- Példány törlése

Az Azure-beli [virtuális hálózatokon belüli központi telepítések](../../virtual-network/virtual-network-for-azure-services.md) támogatásához és az ügyfelek elkülönítésének és biztonságának biztosításához az SQL felügyelt példánya [virtuális fürtökre](connectivity-architecture-overview.md#high-level-connectivity-architecture)támaszkodik. A virtuális fürt az ügyfél virtuális hálózati alhálózatán belül üzembe helyezett elkülönített virtuális gépek dedikált készletét jelöli. Lényegében minden felügyelt példány, amely üres alhálózatra van telepítve, egy új virtuális fürt buildout eredményez.

A felügyelt példányokon a későbbi felügyeleti műveletek befolyásolhatják a mögöttes virtuális fürtöt. Az alapul szolgáló virtuális fürtöt érintő változások befolyásolhatják a felügyeleti műveletek időtartamát, mivel a további virtuális gépek üzembe helyezése olyan terheléssel jár, amelyet figyelembe kell vennie, amikor új központi telepítéseket vagy frissítéseket tervez a meglévő felügyelt példányok esetében.


## <a name="duration"></a>Időtartam

A virtuális fürt műveleteinek időtartama eltérő lehet, de általában a leghosszabb időtartamú. 

A következő értékek általában várhatóan a meglévő szolgáltatás-telemetria adatok alapján:

- **Virtuális fürt létrehozása**: a létrehozás egy szinkron lépés a példányok kezelési műveleteihez. <br/> **a műveletek 90%-a 4 órán belül befejeződik**.
- **Virtuális fürtök átméretezése (bővítés vagy zsugorodás)**: a bővítés egy szinkron lépés, míg a zsugorodás aszinkron módon történik (a példányok kezelési műveleteinek időtartamára való hatás nélkül). <br/>a **fürt bővítésének 90%-a kevesebb, mint 2,5 óra**.
- **Virtuális fürt törlése**: a törlés egy aszinkron lépés, de [manuálisan is elindítható](virtual-cluster-delete.md) egy üres virtuális fürtön, ebben az esetben a rendszer szinkron módon hajtja végre. <br/>a **virtuális fürtök törlésének 90%-a 1,5 órában fejeződik be**.

Emellett a példányok kezelése az üzemeltetett adatbázisokra vonatkozó egyik műveletet is magában foglalhat, ami hosszabb időtartamot eredményez:

- **Adatbázisfájlok csatolása az Azure Storage-ból**: egy szinkron lépés, például méretezési számítás (virtuális mag), illetve a általános célú szolgáltatási szinten felfelé vagy lefelé történő tárterület. <br/>a **műveletek 90%-a 5 percen belül befejeződik**.
- **Always On rendelkezésre állási csoport** beültetése: egy szinkron lépés, például a számítási (virtuális mag) vagy a tárolási skálázás a üzletileg kritikus szolgáltatási rétegben, valamint a szolgáltatási réteg általános célúról üzletileg kritikusra (vagy fordítva) való megváltoztatására. A művelet időtartama a teljes adatbázis méretével, valamint az aktuális adatbázis-tevékenységgel (aktív tranzakciók száma) arányos. Az adatbázis-tevékenység egy példány frissítésekor jelentős variancia bevezetését jelentheti a teljes időtartamra. <br/>a **műveletek 90%-a 220 GB/óra vagy újabb**.

A következő táblázatok összefoglalják a műveleteket és a jellemző általános időtartamokat a művelet kategóriája alapján:

**Kategória: üzembe helyezés**

|Művelet  |Hosszan futó szegmens  |Becsült időtartam  |
|---------|---------|---------|
|Első példány egy üres alhálózaton|Virtuális fürt létrehozása|a műveletek 90%-a 4 órán belül befejeződik.|
|Egy másik hardver generációjának első példánya egy nem üres alhálózatban (például az első gen 5 példány egy 4. generációs példánnyal rendelkező alhálózatban)|Virtuális fürt létrehozása<sup>1</sup>|a műveletek 90%-a 4 órán belül befejeződik.|
|A következő példány létrehozása a nem üres alhálózaton belül (2., 3. stb. példány)|Virtuális fürtök átméretezése|a műveletek 90%-a 2,5 órában fejeződik be.|
| | | 

<sup>1</sup> virtuális fürt hardveres generálásra épül.

**Kategória: frissítés**

|Művelet  |Hosszan futó szegmens  |Becsült időtartam  |
|---------|---------|---------|
|Példány tulajdonságainak módosítása (rendszergazdai jelszó, Azure AD-bejelentkezés, Azure Hybrid Benefit jelző)|N/A|Legfeljebb 1 perc.|
|A példány tárolási felskálázása felfelé/lefelé (általános célú szolgáltatási szintet)|Adatbázisfájlok csatolása|a műveletek 90%-a 5 percen belül befejeződik.|
|A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus szolgáltatási szintet)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú)|– A virtuális fürtök átméretezése<br>– Adatbázisfájlok csatolása|a műveletek 90%-a 2,5 órában fejeződik be.|
|A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
| | | 

**Kategória: törlés**

|Művelet  |Hosszan futó szegmens  |Becsült időtartam  |
|---------|---------|---------|
|Példány törlése|Az összes adatbázis biztonsági másolatának naplózása|90%-os művelet akár 1 percenként is befejeződik.<br>Megjegyzés: Ha az alhálózat utolsó példánya törölve lett, a művelet 12 óra elteltével a virtuális fürt törlését fogja ütemezni. <sup>1</sup>|
|Virtuális fürt törlése (felhasználó által kezdeményezett művelet)|Virtuális fürt törlése|a műveletek 90%-a akár 1,5 óráig is befejeződik.|
| | | 

<sup>1</sup>12 óra az aktuális konfiguráció, de ez a későbbiekben változhat. Ha korábban törölni kell egy virtuális fürtöt (például az alhálózat felszabadításához), tekintse meg az [alhálózat törlése a felügyelt példány törlése után](virtual-cluster-delete.md)című témakört.

## <a name="instance-availability"></a>Példány rendelkezésre állása

Az SQL felügyelt példánya a frissítési **műveletek során elérhető**, kivéve a frissítés végén előforduló feladatátvétel által okozott rövid állásidőt. Általában akár 10 másodpercig is tart, akár a hosszan futó tranzakciók megszakítása esetén is, a [gyorsított adatbázis-helyreállításnak](../accelerated-database-recovery.md)köszönhetően.

Az SQL felügyelt példánya nem érhető el az ügyfélalkalmazások számára az üzembe helyezési és törlési műveletek során.

> [!IMPORTANT]
> Nem ajánlott az Azure SQL felügyelt példányának számítási vagy tárolási méretezése, illetve a szolgáltatási réteg módosítása a hosszan futó tranzakciók (adatimportálás, adatfeldolgozási feladatok, indexek újraépítése stb.) esetében. A művelet végén található adatbázis feladatátvétele megszakítja az összes folyamatban lévő tranzakciót. 

## <a name="management-operations-steps"></a>Felügyeleti műveletek lépései

A felügyeleti műveletek több lépésből állnak. Az [Operations API](management-operations-monitor.md) -val bevezetett lépések a műveletek egy részhalmazára vannak kitéve (telepítés és frissítés). Az üzembe helyezési művelet három lépésből áll, a frissítési művelet végrehajtása pedig hat lépésben történik. A műveletek időtartamával kapcsolatos részletekért tekintse meg a [kezelési műveletek időtartamát](#duration) ismertető szakaszt. A lépések a végrehajtás sorrendjében vannak felsorolva.

### <a name="managed-instance-deployment-steps"></a>Felügyelt példányok telepítési lépései

|Lépés neve  |Lépés leírása  |
|----|---------|
|Kérelem érvényesítése |Az elküldött paraméterek ellenőrzése megtörtént. Hibás konfigurációs művelet esetén hiba történik. |
|Virtuális fürt átméretezése/létrehozása |Az alhálózat állapotától függően a virtuális fürt létrehozása vagy átméretezése folyamatban van. |
|Új SQL-példány indítása |Az SQL-folyamat elindult a telepített virtuális fürtön. |

### <a name="managed-instance-update-steps"></a>Felügyelt példányok frissítési lépései

|Lépés neve  |Lépés leírása  |
|----|---------|
|Kérelem érvényesítése | Az elküldött paraméterek ellenőrzése megtörtént. Hibás konfigurációs művelet esetén hiba történik. |
|Virtuális fürt átméretezése/létrehozása |Az alhálózat állapotától függően a virtuális fürt létrehozása vagy átméretezése folyamatban van. |
|Új SQL-példány indítása | Az SQL-folyamat elindult a telepített virtuális fürtön. |
|Adatbázisfájlok kivetése/adatbázisfájlok csatolása |A frissítési művelet típusától függően az adatbázis-előkészítés vagy az adatbázisfájlok csatolása történik. |
|Feladatátvétel és feladatátvétel előkészítése |Miután a rendszer elvégezte az adatfeldolgozást vagy az adatbázisfájlok újracsatolását, a rendszer előkészíti a feladatátvételt. Ha minden be van állítva, a feladatátvétel **rövid állásidővel** történik. |
|Régi SQL-példányok karbantartása |A régi SQL-folyamat eltávolítása a virtuális fürtből |

> [!NOTE]
> A példányok méretezésének eredményeképpen a mögöttes virtuális fürt a fel nem használt kapacitás és a lehetséges kapacitás-töredezettségmentesítés folyamatán halad át, ami hatással lehet a létrehozási/skálázási műveletekben nem részt vevő példányokra. 


## <a name="management-operations-cross-impact"></a>A kezelési műveletek hatásainak következményei

A felügyelt példányokon a felügyeleti műveletek befolyásolhatják az azonos virtuális fürtön belül elhelyezett példányok más felügyeleti műveleteit:

- A virtuális fürtben a **hosszan futó visszaállítási műveletek** más példány-létrehozási vagy skálázási műveleteket végeznek ugyanabban az alhálózatban.<br/>**Példa:** Ha van egy hosszú ideig futó visszaállítási művelet, és létezik egy létrehozási vagy méretezési kérelem ugyanabban az alhálózatban, akkor ez a kérés hosszabb ideig tart, amíg a visszaállítási művelet befejeződik, mielőtt továbblépne.

- **Egy későbbi példány-létrehozási vagy skálázási** műveletet egy korábban kezdeményezett példány-létrehozási vagy-példány-méretezési művelet tart, amely a virtuális fürt átméretezését kezdeményezte.<br/>**Példa:** Ha ugyanazon az alhálózaton több létrehozási és/vagy méretezési kérelem is található ugyanazon a virtuális fürtön belül, és az egyikük egy virtuális fürt átméretezését kezdeményezi, az összes olyan kérés, amely 5 + perccel a kezdeti művelet kérésének megkezdése után a vártnál hosszabb időt vesz igénybe, a kérelmeknek a folytatás előtt várniuk kell az átméretezés befejeződésére.

- Az **5 perces ablakban elküldött műveletek létrehozása/méretezése** párhuzamosan történik.<br/>**Példa:** A rendszer csak egy virtuális fürt átméretezését hajtja végre az 5 perces ablakban elküldött összes művelethez (az első művelet kérésének végrehajtásának pillanatától számítva). Ha egy másik kérelmet több mint 5 perccel az első elküldése után küld el, a rendszer megvárja, amíg a virtuális fürt átméretezése megkezdődik a végrehajtás megkezdése előtt.

> [!IMPORTANT]
> Azok a felügyeleti műveletek, amelyek egy folyamatban lévő másik művelet miatt tartanak, automatikusan folytatódnak, ha a feltételek teljesülnek. Az átmenetileg szüneteltetett felügyeleti műveletek folytatásához nincs szükség felhasználói beavatkozásra.

## <a name="monitoring-management-operations"></a>Figyelési felügyeleti műveletek

A felügyeleti művelet előrehaladásának és állapotának figyeléséről a felügyeleti [műveletek figyelése](management-operations-monitor.md)című cikkből tájékozódhat.

## <a name="canceling-management-operations"></a>Felügyeleti műveletek megszakítása

A felügyeleti művelet megszakításával kapcsolatos további információkért lásd: [felügyeleti műveletek megszakítása](management-operations-cancel.md).


## <a name="next-steps"></a>Következő lépések

- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](instance-create-quickstart.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-funkciók](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A Azure Database Migration Service áttelepítésre való használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).

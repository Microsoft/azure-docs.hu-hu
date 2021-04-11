---
title: Azure Backup támogatási mátrix az Azure-beli virtuális gépeken futó SQL Server biztonsági mentéshez
description: Összefoglalja a támogatási beállításokat és korlátozásokat, amikor Azure-beli virtuális gépeken SQL Server biztonsági mentést készít a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: d7038b47bd4aba8f7747eef455f1e8dd3c77a695
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257343"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Az Azure-beli virtuális gépek SQL Server biztonsági mentésének támogatási mátrixa

A Azure Backup használatával biztonsági mentést készíthet a Microsoft Azure Cloud platformon üzemeltetett Azure-beli virtuális gépeken futó SQL Server-adatbázisokról. Ez a cikk az Azure-beli virtuális gépek SQL Server biztonsági mentésének forgatókönyveit és üzembe helyezéseit érintő általános támogatási beállításokat és korlátozásokat foglalja össze.

## <a name="scenario-support"></a>Forgatókönyvek támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott központi telepítések** | Az SQL Marketplace Azure-beli virtuális gépek és a nem piactér (SQL Server manuálisan telepített) virtuális gépek támogatottak.
**Támogatott régiók** | Dél-Ausztrália, Kelet-Ausztrália (AE), Ausztrália középső régiója (AC), Ausztrália középső régiója (AC) <br> Dél-Brazília (BRS)<br> Közép-Kanada (CNC), Kelet-Kanada (CE)<br> Dél-Kelet-Ázsia (tenger), Kelet-Ázsia (EA) <br> USA keleti régiója (EUS), USA 2. keleti régiója (EUS2), USA nyugati középső régiója (WCUS), USA nyugati régiója (WUS); USA 2. nyugati régiója (WUS 2), USA északi középső régiója (NCUS), USA középső régiója (ke), USA déli középső régiója (SCUS) <br> India Central (INC), Dél-India (INS), Nyugat-India <br> Kelet-Japán (JPE), Nyugat-Japán (JPW) <br> Korea középső régiója (KRC), Dél-Korea (KRS) <br> Észak-Európa (NE), Nyugat-Európa <br> Egyesült Királyság déli régiója (UKS), Egyesült Királyság nyugati régiója (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD – középső régió, US DoD – keleti régió <br> Észak-Németország, Középnyugat-Németország <br> Észak-Svájc, Nyugat-Svájc <br> Közép-Franciaország <br> Kelet-Kína, Kelet-Kína 2, Észak-Kína, Észak-Kína 2
**Támogatott operációs rendszerek** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> A Linux jelenleg nem támogatott.
**Támogatott SQL Server verziók** | SQL Server 2019, SQL Server 2017 a [termék életciklusának keresése lapon](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 és az SPS a [termék életciklusa lapon](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, standard, web, Developer, Express.<br><br>Az expressz helyi adatbázis-verziók nem támogatottak.
**Támogatott .NET-verziók** | .NET-keretrendszer 4.5.2-es vagy újabb verziója telepítve a virtuális gépen

## <a name="feature-considerations-and-limitations"></a>Szolgáltatásokkal kapcsolatos megfontolások és korlátozások

|Beállítás  |Felső korlát |
|---------|---------|
|A kiszolgálókon (és a tárolóban) védetté tehető adatbázisok száma    |   2000      |
|Az adatbázis mérete támogatott (ezen túlmenően a teljesítménnyel kapcsolatos problémák merülhetnek fel)   |   6 TB *      |
|Adatbázisban támogatott fájlok száma    |   1000      |

_* Az adatbázis méretének korlátja az általunk támogatott adatátviteli sebességtől és a biztonsági mentés időkorlátjának konfigurációjától függ. Ez nem a rögzített korlát. [További információ](#backup-throughput-performance) a biztonsági mentés teljesítményéről._

* SQL Server biztonsági mentés konfigurálható a Azure Portal vagy a **PowerShellben**. A CLI nem támogatott.
* A megoldás mindkét típusú [üzembe helyezést](../azure-resource-manager/management/deployment-models.md) támogatja – Azure Resource Manager virtuális gépeket és klasszikus virtuális gépeket.
* Az összes biztonsági mentési típus (teljes/különbözeti/napló) és helyreállítási modellek (egyszerű/teljes/tömegesen naplózott) támogatottak.
* Csak **olvasható** adatbázisok esetén: a teljes és a másolás csak a teljes biztonsági mentés az egyetlen támogatott biztonsági mentési típus.
* Az SQL natív tömörítés támogatott, ha a felhasználó explicit módon engedélyezi a biztonsági mentési szabályzatot. Azure Backup felülbírálja a példányok szintjének alapértelmezett értékeit a TÖMÖRÍTÉSi/NO_COMPRESSION záradékkal a felhasználó által beállított vezérlőelem értékétől függően.
* A TDE-kompatibilis adatbázis biztonsági mentése támogatott. Ha egy TDE-titkosított adatbázist szeretne visszaállítani egy másik SQL Serverre, először [vissza kell állítania a tanúsítványt a célkiszolgálóra](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). A TDE-kompatibilis adatbázisok SQL Server 2016-es és újabb verzióinak biztonsági mentési tömörítése elérhető, de az [itt](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)leírtak szerint alacsonyabb adatátviteli mérettel.
* A tükrözési adatbázisokhoz és az adatbázis-pillanatképekhez tartozó biztonsági mentési és visszaállítási műveletek nem támogatottak.
* SQL Server **feladatátvevő fürt példánya (** nem támogatott).
* Ha több biztonsági mentési megoldást használ a különálló SQL Server példány vagy az SQL always on rendelkezésre állási csoport biztonsági mentéséhez, a biztonsági mentés sikertelen lehet. Ne tegye meg. A rendelkezésre állási csoport két csomópontjának különálló, azonos vagy eltérő megoldásokkal történő biztonsági mentése a biztonsági mentés sikertelenségét is eredményezheti.
* A rendelkezésre állási csoportok konfigurálásakor a biztonsági mentések a különböző csomópontokból származnak, néhány tényező alapján. A rendelkezésre állási csoport biztonsági mentési viselkedését az alábbiakban összegzi.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Biztonsági mentés működése az Always On rendelkezésreállási csoportok esetén

Azt javasoljuk, hogy a biztonsági mentés a rendelkezésre állási csoport (AG) csak egy csomópontjára legyen konfigurálva. Mindig konfigurálja a biztonsági mentést ugyanabban a régióban, mint az elsődleges csomópontot. Más szóval mindig szüksége lesz az elsődleges csomópontra abban a régióban, ahol a biztonsági mentést konfigurálja. Ha az AG összes csomópontja ugyanabban a régióban van, ahol a biztonsági mentés be van állítva, nincs semmi gond.

#### <a name="for-cross-region-ag"></a>Régiók közötti AG

* A biztonsági mentéstől függetlenül a biztonsági mentések csak azokon a csomópontokon futnak, amelyek ugyanabban a régióban találhatók, ahol a biztonsági mentés konfigurálva van. Ennek az az oka, hogy a régiók közötti biztonsági másolatok nem támogatottak. Ha csak két csomóponttal rendelkezik, és a másodlagos csomópont a másik régióban található, akkor a biztonsági mentések továbbra is az elsődleges csomópontról fognak futni (kivéve, ha a biztonsági mentési beállítások "másodlagosak").
* Ha egy csomópont feladatátvételt hajt végre egy olyan régióba, amely eltér a biztonsági mentés konfigurálásának helyétől, a biztonsági mentések sikertelenek lesznek a feladatátvételi régió csomópontjain.

A biztonsági mentési beállítások és a biztonsági másolatok típusaitól függően (teljes/különbözeti/napló/csak másolás) a biztonsági másolatok egy adott csomópontból (elsődleges/másodlagos) származnak.

#### <a name="backup-preference-primary"></a>Biztonsági mentési beállítások: elsődleges

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Elsődleges
Copy-Only teljes |  Elsődleges

#### <a name="backup-preference-secondary-only"></a>Biztonsági mentési beállítás: csak másodlagos

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Copy-Only teljes |  Másodlagos

#### <a name="backup-preference-secondary"></a>Biztonsági mentési beállítások: másodlagos

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Copy-Only teljes |  Másodlagos

#### <a name="no-backup-preference"></a>Nincs biztonsági mentési beállítás

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Copy-Only teljes |  Másodlagos

## <a name="backup-throughput-performance"></a>A biztonsági mentés teljesítményének teljesítménye

A Azure Backup 200 Mbps egységes adatátviteli sebességet támogat a nagyméretű SQL-adatbázisok (500 GB) teljes és különbözeti biztonsági mentéséhez. Az optimális teljesítmény kihasználása érdekében a következőket kell tennie:

- A mögöttes virtuális gép (amely tartalmazza az adatbázist futtató SQL Server példányt) a szükséges hálózati átviteli sebességgel van konfigurálva. Ha a virtuális gép maximális átviteli sebessége kevesebb, mint 200 Mbps, Azure Backup nem tudja az adatok átvitelét az optimális sebességgel.<br></br>Emellett az adatbázisfájlok tartalmazó lemeznek elegendő átviteli sebességgel kell rendelkeznie. [További](../virtual-machines/disks-performance.md) információ a lemezek átviteli sebességéről és teljesítményéről az Azure-beli virtuális gépeken. 
- A virtuális gépen futó folyamatok nem veszik igénybe a virtuális gép sávszélességét. 
- A biztonsági mentési ütemtervek az adatbázisok egy részhalmazán keresztül oszlanak meg. Egy virtuális gépen egyszerre több biztonsági másolat is fut, és a hálózati használat aránya a biztonsági mentések között van. [További](faq-backup-sql-server.md#can-i-control-how-many-concurrent-backups-run-on-the-sql-server) információ az egyidejű biztonsági mentések számának szabályozásáról.

>[!NOTE]
> [Töltse le a részletes Resource Plannert](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) , hogy kiszámítsa a kiszolgálón javasolt védett adatbázisok hozzávetőleges számát a virtuálisgép-erőforrások, a sávszélesség és a biztonsági mentési szabályzat alapján.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [készíthet biztonsági mentést egy](backup-azure-sql-database.md) Azure-beli virtuális gépen futó SQL Server-adatbázisról.
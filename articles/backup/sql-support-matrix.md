---
title: Azure Backup Azure-beli virtuális gépeken SQL Server Backup támogatási mátrixa
description: Összefoglalja az Azure-beli virtuális gépeken SQL Server azure-beli virtuális gépeken a Azure Backup korlátozásokat.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: bcbac4f6a91ad77d21eb6274aa03d251b8fbfe7c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515056"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Támogatási mátrix a biztonsági SQL Server Azure-beli virtuális gépeken

A felhőplatformon Azure Backup Azure-beli virtuális gépeken SQL Server adatbázisok biztonsági Microsoft Azure biztonsági mentése. Ez a cikk az Azure-beli virtuális gépek biztonsági mentésének forgatókönyvei és SQL Server általános támogatási beállításait és korlátozását foglalja össze.

## <a name="scenario-support"></a>Forgatókönyv támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott központi telepítések** | Az SQL Marketplace-en elérhető Azure-beli virtuális gépek és a nem Marketplace-SQL Server (manuálisan telepített) virtuális gépek támogatottak.
**Támogatott régiók** | Délkelet-Ausztrália (ASE), Kelet-Ausztrália (AE), Ausztrália középső régiója (AC), Ausztrália 2. középső régiója (AC) <br> Dél-Brazília (BRS)<br> Közép-Kanada (CNC), Kelet-Kanada (CE)<br> Dél Kelet-Ázsia (SEA), Kelet-Ázsia (EA) <br> USA keleti régiója (EUS), USA 2. keleti régiója (EUS2), USA nyugati középső régiója (WCUS), USA nyugati régiója (WUS); USA 2. nyugati régiója (WUS 2) AZ USA északi középső régiója (NCUS) USA középső régiója (CUS) USA déli középső régiója (SCUS) <br> Közép-India (INC), Dél-India (INS), Nyugat-India <br> Kelet-Japán (JPE), Nyugat-Japán (JPW) <br> Korea középső (KRC), Dél-Korea déli (KRS) <br> Észak-Európa (NE), Nyugat-Európa <br> Egyesült Királyság déli régiója (UKS), Egyesült Királyság nyugati régiója (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD – középső régió, US DoD – keleti régió <br> Észak-Németország, Nyugat-Németország <br> Észak-Svájc, Nyugat-Svájc <br> Közép-Franciaország <br> Kelet-Kína, Kína 2. keleti régiója, Észak-Kína, Kína 2. északi régiója
**Támogatott operációs rendszerek** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> A Linux jelenleg nem támogatott.
**Támogatott SQL Server verziók** | SQL Server 2019., SQL Server 2017. SQL Server részletesen a Search [product lifecycle](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)(Termék életciklusának keresése) oldalon ( SQL Server 2016 és SPS) a Search [product lifecycle](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)(Termék életciklusának keresése) oldalon (2014. SQL Server, SQL Server 2012. SQL Server. SQL Server, 2008 R2, SQL Server 2008. <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Az Express Local DB-verziók nem támogatottak.
**Támogatott .NET-verziók** | .NET-keretrendszer 4.5.2-es vagy újabb telepítése a virtuális gépre

## <a name="feature-considerations-and-limitations"></a>A funkciókra vonatkozó szempontok és korlátozások

|Beállítás  |Felső korlát |
|---------|---------|
|A kiszolgálón (és a tárolóban) védható adatbázisok száma    |   2000      |
|Támogatott adatbázisméret (ezen kívül teljesítménybeli problémák is előfordulhatnak)   |   6 TB*      |
|Adatbázis által támogatott fájlok száma    |   1000      |

_*Az adatbázis méretkorlátja a támogatott adatátviteli sebességtől és a biztonsági mentés időkorlátjának konfigurációjától függ. Nem ez a korlát. [További információ a](#backup-throughput-performance) biztonsági mentés átviteli sebességéről._

* SQL Server biztonsági mentés a következőben konfigurálható: Azure Portal **PowerShell.** A CLI nem támogatott.
* A megoldás mindkét típusú üzemelő példányon támogatott [–](../azure-resource-manager/management/deployment-models.md) a virtuális Azure Resource Manager a klasszikus virtuális gépeken.
* Minden biztonsági mentési típus (teljes/különbségi/napló) és helyreállítási modell (egyszerű/teljes/tömeges naplózás) támogatott.
* Csak **olvasható adatbázisok esetén:** a teljes és a csak másolásos teljes biztonsági mentés az egyetlen támogatott biztonsági mentési típus.
* A natív SQL-tömörítés támogatott, ha a felhasználó explicit módon engedélyezi a biztonsági mentési házirendben. Azure Backup felülbírálja a példányszintű alapértelmezéseket a COMPRESSION /NO_COMPRESSION záradékkal, a vezérlő felhasználó által beállított értékétől függően.
* TDE – az engedélyezett adatbázis biztonsági mentése támogatott. Ha egy TDE-titkosítású adatbázist egy másik SQL Server vissza, először vissza kell állítania a tanúsítványt a [célkiszolgálón.](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server) A 2016-os és SQL Server verziójú TDE-kompatibilis adatbázisokhoz elérhető a biztonsági mentési tömörítés, de alacsonyabb átviteli mérettel, az itt leírtak [szerint.](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)
* A tükrözött adatbázisok és adatbázis-pillanatképek biztonsági mentési és visszaállítási műveletei nem támogatottak.
* SQL Server **fürtpéldány (FCI)** nem támogatott.
* Ha több biztonsági mentési megoldással készít biztonsági másolatot az önálló SQL Server vagy az SQL Always on rendelkezésre állási csoportról, az biztonsági mentési hibákhoz vezethet. Ennek mellőzését. A rendelkezésre állási csoport két csomópontja egyenként, azonos vagy eltérő megoldásokkal való biztonsági mentése biztonsági mentési hibákhoz vezethet.
* A rendelkezésre állási csoportok konfigurálásakor a rendszer néhány tényező alapján készít biztonsági mentéseket a különböző csomópontokról. A rendelkezésre állási csoportok biztonsági mentési viselkedését az alábbiakban foglaljuk össze.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Biztonsági mentés működése az Always On rendelkezésreállási csoportok esetén

Javasoljuk, hogy a biztonsági mentést egy rendelkezésre állási csoport (AG) csak egy csomópontján konfigurálja. A biztonsági mentést mindig ugyanabban a régióban konfigurálja, mint az elsődleges csomópontot. Más szóval az elsődleges csomópontnak mindig jelen kell lennie abban a régióban, ahol a biztonsági mentést konfigurálja. Ha a csoport összes csomópontja ugyanabban a régióban van, ahol a biztonsági mentés konfigurálva van, akkor nem kell aggódnia.

#### <a name="for-cross-region-ag"></a>Régiók közötti AG-hez

* A biztonsági mentés beállításaitól függetlenül a biztonsági mentések csak olyan csomópontokon fognak futni, amelyek ugyanabban a régióban vannak, ahol a biztonsági mentés konfigurálva van. Ennek az az oka, hogy a régiók közötti biztonsági mentések nem támogatottak. Ha csak két csomópontja van, és a másodlagos csomópont a másik régióban van, a biztonsági mentések továbbra is az elsődleges csomópontról fognak futni (kivéve, ha a biztonsági mentési beállítás csak másodlagos).
* Ha egy csomópont a biztonsági mentés konfigurált régiójától eltérő régióba veszi át a feladatátvételt, a biztonsági mentések sikertelenek lesznek a feladatátvételi régióban lévő csomópontokon.

A biztonsági mentés beállításától és a biztonsági mentések típusaitól (teljes/különbségi/napló/csak másolási/ csak teljes) függően a biztonsági mentések egy adott csomópontról (elsődleges/másodlagos) vannak készítve.

#### <a name="backup-preference-primary"></a>Biztonsági mentés beállítása: Elsődleges

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Elsődleges
Copy-Only Megtelt |  Elsődleges

#### <a name="backup-preference-secondary-only"></a>Biztonsági mentés beállítása: Csak másodlagos

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Copy-Only Megtelt |  Másodlagos

#### <a name="backup-preference-secondary"></a>Biztonsági mentés beállítása: Másodlagos

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Copy-Only Megtelt |  Másodlagos

#### <a name="no-backup-preference"></a>Nincs biztonsági mentési beállítás

**Biztonsági mentés típusa** | **Csomópont**
--- | ---
Összes | Elsődleges
Differenciál | Elsődleges
Napló |  Másodlagos
Copy-Only Megtelt |  Másodlagos

## <a name="backup-throughput-performance"></a>A biztonsági mentés átviteli sebességének teljesítménye

Azure Backup nagy (500 GB-os) SQL-adatbázisok teljes és különbségi biztonsági mentéséhez 200 Mb/s konzisztens adatátviteli sebességet támogat. Az optimális teljesítmény kihasználása érdekében győződjön meg a következő feltételekről:

- A mögöttes virtuális gép (amely az adatbázist SQL Server tároló virtuális gépet tartalmazza) a szükséges hálózati átviteli sebességre van konfigurálva. Ha a virtuális gép maximális átviteli sebessége kisebb, mint 200 Mbps Azure Backup az optimális sebességgel nem lehet adatokat áthozni.<br></br>Emellett az adatbázisfájlokat tartalmazó lemezen elegendő átviteli sebességet kell kiépítve lennie. [További információ az](../virtual-machines/disks-performance.md) Azure-beli virtuális gépek lemezteljesítményéről és teljesítményéről. 
- A virtuális gépen futó folyamatok nem fogyasztják a virtuális gép sávszélességét. 
- A biztonsági mentési ütemezések az adatbázisok egy részkészletében vannak elterjesztve. Egy virtuális gépen egyidejűleg futó több biztonsági mentés osztozik a biztonsági mentések közötti hálózatfelhasználási arányon. [További információ](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) az egyidejű biztonsági mentések számának szabályozásról.

>[!NOTE]
> [Töltse le a részletes Resource Plannert,](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) hogy kiszámítsa a kiszolgálónként ajánlott védett adatbázisok hozzávetőleges számát a virtuális gép erőforrásai, a sávszélesség és a biztonsági mentési házirend alapján.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [lehet biztonsági SQL Server](backup-azure-sql-database.md) Azure-beli virtuális gépen futó virtuális gépeken futó virtuális gépekről.
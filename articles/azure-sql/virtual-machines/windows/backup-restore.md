---
title: Azure-beli virtuális gépek SQL Serverának biztonsági mentése és visszaállítása | Microsoft Docs
description: Az Azure Virtual Machines-on futó SQL Server adatbázisok biztonsági mentési és visszaállítási szempontjait ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 2fcba81bcd20db321d791fcda589f40fb0699702
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97733073"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>SQL Server biztonsági mentése és visszaállítása Azure-beli virtuális gépeken
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk útmutatást nyújt az Azure-beli Windows rendszerű virtuális gépeken (VM) futó SQL Server számára elérhető biztonsági mentési és visszaállítási lehetőségekről. Az Azure Storage minden Azure-beli virtuális gép lemezét három példányban tárolja az adatvesztés vagy a fizikai adatsérülés elleni védelem biztosításához. Így a helyszíni SQL Servertól eltérően nem kell a hardver meghibásodására összpontosítania. Azonban érdemes lehet biztonsági másolatot készíteni a SQL Server adatbázisairól az alkalmazás-vagy felhasználói hibák, például a véletlen adatbeszúrások vagy törlések elleni védelem érdekében. Ebben az esetben fontos, hogy egy adott időpontra lehessen visszaállítani.

A cikk első része áttekintést nyújt a rendelkezésre álló biztonsági mentési és visszaállítási lehetőségekről. Ezeket a szakaszokat követve további információkat adhat meg az egyes stratégiákról.

## <a name="backup-and-restore-options"></a>Biztonsági mentési és visszaállítási beállítások

Az alábbi táblázat az Azure-beli virtuális gépek SQL Serverának különböző biztonsági mentési és visszaállítási lehetőségeiről nyújt információkat:

| Stratégia | SQL-verziók | Description |
|---|---|---|
| [Automatikus biztonsági mentés](#automated) | 2014<br/> 2016<br/> 2017 | Az automatikus biztonsági mentés lehetővé teszi a SQL Server VM összes adatbázisának rendszeres biztonsági mentését. A biztonsági mentések tárolása az Azure Storage-ban akár 30 napig is eltartható. A SQL Server 2016-es verziójától kezdve az automatizált Backup v2 további lehetőségeket kínál, például a manuális ütemezés konfigurálását, valamint a teljes és naplózott biztonsági másolatok gyakoriságát. |
| [Azure Backup SQL-alapú virtuális gépekhez](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017<br/> 2019 | A Azure Backup nagyvállalati szintű biztonsági mentési képességet biztosít a SQL Server Azure-beli virtuális gépeken. Ezzel a szolgáltatással központilag kezelheti a biztonsági mentéseket több kiszolgáló és több ezer adatbázis között. Az adatbázisok visszaállíthatók egy adott időpontban a portálon. Testreszabható adatmegőrzési szabályzatot kínál, amely évekig képes biztonsági mentéseket fenntartani. |
| [Manuális biztonsági mentés](#manual) | Mind | A SQL Server verziójától függően számos módszer áll rendelkezésre az Azure-beli virtuális gépen futó SQL Server manuális biztonsági mentésére és visszaállítására. Ebben az esetben Ön felelős az adatbázisok biztonsági mentésének, valamint a biztonsági másolatok tárolási helyének és kezelésének módjáról. |

Az alábbi szakaszok részletesebben ismertetik az egyes lehetőségeket. A cikk utolsó szakasza egy szolgáltatás-mátrix formájában összefoglalást tartalmaz.

## <a name="automated-backup"></a><a id="automated"></a> Automatikus biztonsági mentés

Az automatikus biztonsági mentés automatikus biztonsági mentési szolgáltatást biztosít az Azure-beli Windows rendszerű virtuális gépeken futó SQL Server Standard és Enterprise kiadásokhoz. Ezt a szolgáltatást a [SQL Server IaaS-ügynök bővítménye](sql-server-iaas-agent-extension-automate-management.md)nyújtja, amely a Azure Portal SQL Server Windows rendszerű virtuálisgép-lemezképeken automatikusan települ.

Az összes adatbázis biztonsági mentése egy Ön által konfigurált Azure Storage-fiókba történik. A biztonsági mentések titkosítva és akár 30 napig is megmaradnak.

A SQL Server 2016-es és újabb virtuális gépek további testreszabási lehetőségeket kínálnak az automatizált Backup v2-mel. A következő fejlemények a következők:

- Rendszeradatbázis biztonsági mentései
- Manuális biztonsági mentés időpontja és időablaka
- Teljes és naplófájlok biztonsági mentésének gyakorisága

Egy adatbázis visszaállításához meg kell keresnie a szükséges biztonságimásolat-fájlokat a Storage-fiókban, és el kell végeznie az SQL-alapú virtuális gép visszaállítását SQL Server Management Studio (SSMS) vagy Transact-SQL parancs használatával.

Az SQL virtuális gépek automatikus biztonsági mentésének konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkek egyikét:

- **SQL Server 2016/2017**: [automatizált Backup v2 az Azure](automated-backup.md) -hoz Virtual machines
- **SQL Server 2014**: [SQL Server 2014 automatikus biztonsági mentése Virtual Machines](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a> Azure Backup SQL virtuális gépekhez

A [Azure Backup](../../../backup/index.yml) nagyvállalati szintű biztonsági mentési képességet biztosít a SQL Server Azure-beli virtuális gépeken. Az összes biztonsági mentést egy Recovery Services tárolóban tárolják és kezelik. A megoldás számos előnnyel jár, különösen a vállalatok számára:

- **Zéró infrastruktúra biztonsági mentése**: a biztonsági mentési kiszolgálókat és a tárolóhelyeket nem kell kezelnie.
- **Scale**: számos SQL-virtuális gép és több ezer adatbázis védelmének biztosítása.
- **Utólagos elszámolás: ez** a funkció Azure Backup által biztosított külön szolgáltatás, de az összes Azure-szolgáltatáshoz hasonlóan csak a ténylegesen használt funkciókért kell fizetnie.
- **Központi felügyelet és monitorozás**: központilag kezelheti az összes biztonsági mentést, beleértve a Azure Backup által támogatott egyéb munkaterheléseket az Azure egyetlen irányítópultján.
- **Házirend által vezérelt biztonsági mentés és megőrzés**: szabványos biztonsági mentési szabályzatok létrehozása a rendszeres biztonsági mentésekhez. Adatmegőrzési szabályzatokat hozhat létre a biztonsági mentések évekig történő fenntartásához.
- **Az SQL always on szolgáltatás támogatása**: a SQL Server always on Configuration és a biztonsági mentési rendelkezésre állási csoport biztonsági mentési preferenciájának észlelése és biztosítása.
- **15 perces helyreállítási időkorlát (RPO)**: az SQL-tranzakciós naplók biztonsági másolatainak beállítása 15 percenként.
- **Időponthoz tartozó visszaállítás**: a portál használatával állíthatja helyre az adatbázisokat egy adott időpontra anélkül, hogy manuálisan kellene visszaállítani a több teljes, differenciált és naplózott biztonsági mentést.
- **Összevont e-mail-riasztások**: az összevont e-mail-értesítések konfigurálása bármilyen hiba esetén.
- **Azure szerepköralapú hozzáférés-vezérlés**: határozza meg, hogy kik kezelhetik a biztonsági mentési és visszaállítási műveleteket a portálon keresztül.

Az alábbi videóban gyorsan áttekintheti, hogyan működik együtt a bemutatóval:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Az SQL virtuális gépekre vonatkozó Azure Backup megoldás általánosan elérhető. További információ: [SQL Server adatbázis biztonsági mentése az Azure-](../../../backup/backup-azure-sql-database.md)ba.

## <a name="manual-backup"></a><a id="manual"></a> Manuális biztonsági mentés

Ha manuálisan szeretné kezelni a biztonsági mentési és visszaállítási műveleteket az SQL-alapú virtuális gépeken, a használt SQL Server verziójától függően több lehetőség is rendelkezésre áll. A biztonsági mentés és a visszaállítás áttekintéséhez tekintse meg az alábbi cikkek egyikét a SQL Server verziója alapján:

- [Biztonsági mentés és visszaállítás a SQL Server 2016-es és újabb verzióiban](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [SQL Server 2014 biztonsági mentése és visszaállítása](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases?viewFallbackFrom=sql-server-2014)
- [SQL Server 2012 biztonsági mentése és visszaállítása](/previous-versions/sql/sql-server-2012/ms187048(v=sql.110))
- [Biztonsági mentés és visszaállítás a SQL Server SQL Server 2008 R2 rendszerhez](/previous-versions/sql/sql-server-2008-r2/ms187048(v=sql.105))
- [SQL Server 2008 biztonsági mentése és visszaállítása](/previous-versions/sql/sql-server-2008/ms187048(v=sql.100))

A következő szakaszok részletesen ismertetik a manuális biztonsági mentési és visszaállítási lehetőségeket.

### <a name="backup-to-attached-disks"></a>Biztonsági mentés csatolt lemezekre

Az Azure-beli virtuális gépeken lévő SQL Server esetén natív biztonsági mentési és visszaállítási technikákat használhat a virtuális gépen lévő csatlakoztatott lemezek használatával a biztonságimásolat-fájlok céljához. A [virtuális gép mérete](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)alapján azonban korlátozva van az Azure-beli virtuális gépekhez kapcsolható lemezek száma. A Lemezkezelés szolgáltatással kapcsolatos szempontokat is figyelembe kell venni.

A teljes adatbázis biztonsági mentésének a SQL Server Management Studio (SSMS) vagy a Transact-SQL használatával történő manuális létrehozásával kapcsolatos példát a [teljes adatbázis biztonsági másolatának létrehozása](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)című témakörben talál.

### <a name="backup-to-url"></a>Biztonsági mentés az URL-címre

A SQL Server 2012 SP1 CU2 kezdve a biztonsági mentést és visszaállítást közvetlenül Microsoft Azure Blob Storage-ba, amely az URL-re való biztonsági mentés néven is ismert. SQL Server 2016 a következő fejlesztéseket is bemutatta ehhez a szolgáltatáshoz:

| 2016-fejlesztés | Részletek |
| --- | --- |
| **Csíkozás** |Ha Microsoft Azure Blob Storage-ba készít biztonsági másolatot, SQL Server 2016 támogatja a több blobra történő biztonsági mentést, így a nagyméretű adatbázisok biztonsági mentését legfeljebb 12,8 TB-ig engedélyezheti. |
| **Pillanatkép biztonsági mentése** |Az Azure-Pillanatképek használatával a SQL Server File-Snapshot Backup szinte azonnali biztonsági mentéseket és gyors visszaállításokat biztosít az Azure Blob Storage szolgáltatással tárolt adatbázisfájlok számára. Ez a funkció lehetővé teszi a biztonsági mentési és visszaállítási házirendek egyszerűsítését. A fájl – pillanatkép biztonsági mentése az időponthoz tartozó visszaállítást is támogatja. További információ: az [Azure-beli adatbázisfájlok biztonsági mentései](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

További információkért tekintse meg az alábbi cikkek egyikét a SQL Server verziója alapján:

- **SQL Server 2016/2017**: [SQL Server biztonsági mentés az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 biztonsági mentés az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service?viewFallbackFrom=sql-server-2014)
- **SQL Server 2012**: [SQL Server 2012 biztonsági mentés az URL-címre](/previous-versions/sql/sql-server-2012/jj919148(v=sql.110))

### <a name="managed-backup"></a>Felügyelt biztonsági mentés

A SQL Server 2014-es verziójától kezdve a felügyelt biztonsági mentés automatizálja az Azure Storage-ba történő biztonsági másolatok létrehozását. A háttérben a felügyelt biztonsági mentés a jelen cikk előző szakaszában ismertetett, a biztonsági mentés URL-címére szolgáló funkciót használja. A felügyelt biztonsági mentés a mögöttes szolgáltatás, amely támogatja a SQL Server VM automatikus biztonsági mentési szolgáltatást.

A SQL Server 2016-es verziójától kezdve a felügyelt biztonsági mentés további lehetőségeket kínál az ütemezéshez, a rendszeradatbázis biztonsági mentéséhez, valamint a teljes és naplózott biztonsági mentés gyakoriságához

További információkért tekintse meg az alábbi cikkek egyikét a SQL Server verziója alapján:

- [Felügyelt biztonsági mentés Microsoft Azure SQL Server 2016-es és újabb verziókban](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Felügyelt biztonsági mentés a Microsoft Azure SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?viewFallbackFrom=sql-server-2014)

## <a name="decision-matrix"></a>Döntési mátrix

Az alábbi táblázat összefoglalja az Azure-ban SQL Server virtuális gépek biztonsági mentésének és visszaállításának funkcióit.

| Beállítás | Automatikus biztonsági mentés | SQL-Azure Backup | Manuális biztonsági mentés |
|---|---|---|---|
| További Azure-szolgáltatásra van szükség |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Biztonsági mentési szabályzat konfigurálása Azure Portal | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Adatbázisok visszaállítása Azure Portal |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Több kiszolgáló kezelése egyetlen irányítópulton |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Adott időpontnak megfelelő helyreállítás | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) |
| 15 perces helyreállítási pont célkitűzése (RPO) | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) |
| Rövid távú biztonsági mentési adatmegőrzési szabályzat (nap) | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Biztonsági másolatok hosszú távú megőrzési szabályzata (hónapok, évek) |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Beépített támogatás a SQL Server always on |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Biztonsági mentés az Azure Storage-fiók (ok) ba | ![Zöld pipa](./media/backup-restore/yes.png)automatikus | ![Zöld pipa](./media/backup-restore/yes.png)automatikus | ![Zöld pipa](./media/backup-restore/yes.png)(ügyfél által felügyelt) |
| A tárolási és a biztonságimásolat-fájlok kezelése | | ![Zöld pipa](./media/backup-restore/yes.png) |  |
| Biztonsági másolat készítése a virtuális gépen található csatlakoztatott lemezekre |   |   | ![Zöld pipa](./media/backup-restore/yes.png) |
| Központi testreszabható biztonsági mentési jelentések |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Összevont e-mail-riasztások a hibákhoz |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| A figyelés testreszabása Azure Monitor naplók alapján |   | ![Zöld pipa](./media/backup-restore/yes.png) |   |
| Biztonsági mentési feladatok figyelése SSMS vagy Transact-SQL-parancsfájlok segítségével | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) | ![Zöld pipa](./media/backup-restore/yes.png) |
| Adatbázisok visszaállítása SSMS vagy Transact-SQL-parancsfájlokkal | ![Zöld pipa](./media/backup-restore/yes.png) |   | ![Zöld pipa](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Következő lépések

Ha SQL Server Azure-beli virtuális gépen történő üzembe helyezését tervezi, akkor a következő útmutatóban talál kiépítési útmutatót: [Windows SQL Server virtuális gép kiépítése a Azure Portal](create-sql-vm-portal.md).

Bár a biztonsági mentés és a visszaállítás használható az adatai áttelepítésére, a virtuális gépeken való SQL Server lehetséges, hogy könnyebb az adatáttelepítési útvonalak. Az áttelepítési lehetőségek és javaslatok teljes körű megvitatását lásd: [adatbázis áttelepítése az Azure virtuális gépen SQL Server](migrate-to-vm-from-sql-server.md).

---
title: SQL Server-adatbázis migrálása SQL Server virtuális gépre | Microsoft Docs
description: Ismerje meg, hogyan telepíthet át egy helyszíni felhasználói adatbázist egy Azure-beli virtuális gépen SQL Server.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6e9009040d2d02702f8a71c352716491d07d1f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704304"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>SQL Server adatbázis migrálása egy Azure-beli virtuális gépen SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A helyszíni SQL Server felhasználói adatbázis áttelepíthető egy Azure-beli virtuális gépen (VM) SQL Serverba. Ez a cikk röviden bemutatja a különböző módszereket, és javaslatot tesz a legjobb módszerre a különböző forgatókönyvekhez.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 és SQL Server 2008 R2 a helyszíni példányok támogatási életciklusának [végére](https://www.microsoft.com/sql-server/sql-server-2008) közeledik. A támogatás kiterjesztéséhez áttelepítheti SQL Server példányát egy Azure-beli virtuális gépre, vagy megvásárolhatja a kibővített biztonsági frissítéseket, hogy a helyszínen is megmaradjon. További információ: a [2008-es és a 2008 R2-es verzió SQL Server támogatásának kiterjesztése az Azure](sql-server-2008-extend-end-of-support.md) -ban

## <a name="what-are-the-primary-migration-methods"></a>Mik az elsődleges áttelepítési módszerek?

Az elsődleges áttelepítési módszerek a következők:

* Végezzen el egy helyszíni biztonsági mentést a tömörítés használatával, majd manuálisan másolja a biztonságimásolat-fájlt az Azure-beli virtuális gépre.
* Készítsen biztonsági másolatot az URL-címre, majd állítsa vissza az Azure-beli virtuális gépre az URL-címről.
* Válassza le az adatok és naplófájlok fájljait, másolja őket az Azure Blob Storage-ba, majd csatolja őket az Azure-beli virtuális gépen lévő SQL Serverhoz az URL-címről.
* Alakítsa át a helyszíni fizikai gépet egy Hyper-V virtuális merevlemezre, töltse fel az Azure Blob Storage-ba, majd telepítse azt új virtuális gépként a feltöltött VHD használatával.
* A merevlemez szállítása a Windows import/export szolgáltatás használatával.
* Ha a helyszíni AlwaysOn rendelkezésre állási csoporttal rendelkezik, az Azure- [replika hozzáadása varázslóval](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) hozzon létre egy replikát az Azure-ban, a feladatátvételt, és irányítsa a felhasználókat az Azure Database-példányra.
* SQL Server [tranzakciós replikálással](/sql/relational-databases/replication/transactional/transactional-replication) konfigurálja az Azure SQL Server-példányt előfizetőként, tiltsa le a replikációt, és irányítsa a felhasználókat az Azure Database-példányra.

> [!TIP]
> Ugyanezeket a technikákat használhatja az adatbázisok SQL Server virtuális gépek közötti áthelyezéséhez az Azure-ban. Nem támogatott például, hogy egy SQL Server Gallery-rendszerkép virtuális gépet egy verzióról vagy kiadásról egy másikra frissítsen. Ebben az esetben létre kell hoznia egy új SQL Server VMt az új verzióval/kiadással, majd a cikkben ismertetett áttelepítési módszerek egyikének használatával kell áthelyeznie az adatbázisokat. 

## <a name="choose-a-migration-method"></a>Áttelepítési módszer kiválasztása

A legjobb adatátviteli teljesítmény érdekében a tömörített biztonságimásolat-fájllal telepítse át az adatbázisfájlok az Azure-beli virtuális gépre.

Az adatbázis-áttelepítési folyamat során az állásidő minimalizálásához használja a AlwaysOn vagy a tranzakciós replikálási lehetőséget.

Ha a fenti metódusok használata nem lehetséges, akkor manuálisan telepítse át az adatbázist. Általánosságban elmondható, hogy az adatbázis biztonsági mentését választja, majd az adatbázis biztonsági másolatát az Azure-ba másolja, majd visszaállítja az adatbázist. Az adatbázisfájlok saját maguk is másolhatók az Azure-ba, majd csatolhatók. Az adatbázisok Azure-beli virtuális gépekre való áttelepítésének több módja is van.

> [!NOTE]
> Ha a SQL Server régebbi verzióiról SQL Server 2014-re vagy SQL Server 2016-re frissít, érdemes megfontolnia, hogy szükség van-e a módosításokra. Javasoljuk, hogy az áttelepítési projekt részeként a SQL Server új verziója által nem támogatott szolgáltatások összes függőségét foglalkozzon. További információ a támogatott kiadásokról és forgatókönyvekről: [frissítés SQL Serverra](/sql/database-engine/install-windows/upgrade-sql-server).

A következő táblázat felsorolja az egyes elsődleges áttelepítési módszereket, és bemutatja, hogy az egyes módszerek használata a legmegfelelőbb-e.

| Metódus | Forrás adatbázis verziója | Céladatbázis verziója | Forrás-adatbázis biztonsági másolatának mérete korlátozás | Jegyzetek |
| --- | --- | --- | --- | --- |
| [Helyszíni biztonsági mentés tömörítéssel és a biztonságimásolat-fájl manuális másolása az Azure-beli virtuális gépre](#back-up-and-restore) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](../../../index.yml) | Ez a technika egyszerű és jól tesztelt az adatbázisok számítógépek közötti áthelyezéséhez. |
| [Készítsen biztonsági másolatot az URL-címre, és állítsa vissza az Azure-beli virtuális gépre az URL-címről](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 vagy újabb | SQL Server 2012 SP1 CU2 vagy újabb | < 12,8 TB SQL Server 2016, ellenkező esetben < 1 TB | Ez a módszer csak egy másik módszer a biztonságimásolat-fájl áthelyezésére a virtuális gépre az Azure Storage használatával. |
| [Válassza le, majd másolja az adatok és a naplófájlokat az Azure Blob Storage-ba, majd csatolja SQL Server az Azure-beli virtuális gépen az URL-címről](#detach-and-attach-from-a-url) | SQL Server 2005 vagy újabb |SQL Server 2014 vagy újabb | [Azure VM-tároló korlátja](../../../index.yml) | Akkor használja ezt a módszert, ha [ezeket a fájlokat az Azure Blob Storage szolgáltatással tervezi tárolni](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) , és csatolja őket egy Azure-beli virtuális gépen futó SQL Serverhoz, különösen nagy méretű adatbázisokhoz |
| [Helyszíni gép konvertálása Hyper-V virtuális merevlemezekre, feltöltés az Azure Blob Storage-ba, majd új virtuális gép üzembe helyezése feltöltött VHD használatával](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](../../../index.yml) |Akkor használja, ha [saját SQL Server-licencet](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)használ, és egy olyan adatbázist telepít át, amelyet a SQL Server egy régebbi verzióján fog futtatni, vagy ha a rendszer-és felhasználói adatbázisokat más felhasználói adatbázisokból és/vagy rendszer-adatbázisokból származó áttelepítésének részeként együtt telepíti. |
| [Merevlemez-meghajtó kiszállítása a Windows import/export szolgáltatással](#ship-a-hard-drive) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](../../../index.yml) |A [Windows import/export szolgáltatás](../../../import-export/storage-import-export-service.md) használata, ha a manuális másolási módszer túl lassú, például nagyon nagy méretű adatbázisokkal |
| [Az Azure-replika hozzáadása varázsló használata](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 vagy újabb |SQL Server 2012 vagy újabb |[Azure VM-tároló korlátja](../../../index.yml) |Csökkentse az állásidőt, ha mindig helyszíni üzembe helyezést használ |
| [SQL Server tranzakciós replikáció használata](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](../../../index.yml) |Akkor használja, ha csökkentenie kell az állásidőt, és nincs mindig helyszíni üzembe helyezése |

## <a name="back-up-and-restore"></a>Biztonsági mentés és visszaállítás

Készítsen biztonsági másolatot az adatbázisról a tömörítéssel, másolja a biztonsági másolatot a virtuális gépre, majd állítsa vissza az adatbázist. Ha a biztonságimásolat-fájl 1 TB-nál nagyobb, létre kell hoznia egy csíkozott készletet, mert a virtuális gép lemezének maximális mérete 1 TB. A következő általános lépésekkel telepítheti át a felhasználói adatbázisokat a manuális módszer használatával:

1. Teljes adatbázis biztonsági mentése egy helyszíni helyre.
2. Hozzon létre vagy töltsön fel egy virtuális gépet a SQL Server kívánt verziójával.
3. A kapcsolat beállítása a követelmények alapján. Lásd: [kapcsolódás SQL Server virtuális géphez az Azure-ban (Resource Manager)](ways-to-connect-to-sql.md).
4. Másolja a biztonságimásolat-fájl (oka) t a virtuális gépre a távoli asztal, a Windows Intéző vagy egy parancssorból a Másolás parancs használatával.

## <a name="backup-to-url-and-restore-from-url"></a>Biztonsági mentés URL-címre és visszaállítás URL-címről

Helyi fájlra történő biztonsági mentés helyett használhatja a [biztonsági mentést az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url) , majd visszaállíthatja a virtuális gépre az URL-címet. A SQL Server 2016 támogatja a csíkozott biztonságimásolat-készleteket. Ajánlott teljesítményre, és a Blobok méretének túllépéséhez szükségesek. A nagy méretű adatbázisok esetében ajánlott a [Windows import/export szolgáltatás](../../../import-export/storage-import-export-service.md) használata.

## <a name="detach-and-attach-from-a-url"></a>Leválasztás és csatolás URL-címről

Válassza le az adatbázist és a naplófájlokat, majd vigye át őket az [Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)-ba. Ezután csatolja az adatbázist az Azure-beli virtuális gépen lévő URL-címről. Ezt a módszert akkor használja, ha azt szeretné, hogy a fizikai adatbázisfájlok a blob Storage-ban legyenek tárolva, ami nagyon nagy adatbázisok esetén hasznos lehet. A következő általános lépésekkel telepítheti át a felhasználói adatbázisokat a manuális módszer használatával:

1. Válassza le az adatbázisfájlok a helyszíni adatbázis-példányból.
2. Másolja a leválasztott adatbázisfájlok az Azure Blob Storage-ba a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy-v10.md)használatával.
3. Csatolja az adatbázisfájlok az Azure URL-címről az SQL Server példányhoz az Azure-beli virtuális gépen.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Konvertálás virtuális gépre, feltölthető egy URL-címre, és üzembe helyezése új virtuális gépként

Ezzel a módszerrel az összes rendszer-és felhasználói adatbázist áttelepítheti egy helyszíni SQL Server-példányból egy Azure-beli virtuális gépre. A következő általános lépésekkel telepítheti át a teljes SQL Server példányt a manuális metódus használatával:

1. Fizikai vagy virtuális gépek átalakítása Hyper-V virtuális merevlemezekre.
2. Töltse fel a VHD-fájlokat az Azure Storage [-ba az Add-AzureVHD parancsmag](/previous-versions/azure/dn495173(v=azure.100))használatával.
3. Helyezzen üzembe egy új virtuális gépet a feltöltött virtuális merevlemez használatával.

> [!NOTE]
> Egy teljes alkalmazás áttelepíthetők a [Azure site Recovery](../../../site-recovery/site-recovery-overview.md)] használatával.

## <a name="ship-a-hard-drive"></a>Merevlemez-meghajtó kiszállítása

A [Windows import/export szolgáltatás módszerével](../../../import-export/storage-import-export-service.md) nagy mennyiségű fájlt vihet át az Azure Blob Storage-ba olyan helyzetekben, amikor a hálózaton keresztüli feltöltés megfizethetetlenül drága vagy nem valósítható meg. Ezzel a szolgáltatással egy vagy több olyan merevlemezt küld egy Azure-adatközpontba, ahol az adatai fel lesznek töltve a Storage-fiókba.

## <a name="next-steps"></a>Következő lépések

További információ: [SQL Server az Azure Virtual Machines áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).

A rögzített lemezképből származó SQL Server Azure-beli virtuális gépeken való létrehozásával kapcsolatos utasításokért lásd: [tippek & trükkök a "klónozás" Azure SQL-alapú virtuális gépeken a rögzített lemezképek](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images) alapján a CSS SQL Server Engineers blogban.
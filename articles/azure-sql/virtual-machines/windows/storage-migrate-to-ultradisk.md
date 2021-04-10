---
title: A naplófájl átmigrálása Ultra lemezre
description: Ismerje meg, hogyan telepítheti át az Azure-beli virtuális gépek (VM) SQL Server az Azure Ultradisk, hogy kihasználhassa a nagy teljesítményt és az alacsony késést.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e8410b4e0997798eba5ee91f361c3a5f1ce47ef1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586301"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>A naplófájl átmigrálása Ultra lemezre
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az Azure Ultra Disks nagy teljesítményű, magas IOPS és következetesen alacsony késésű lemezes tárolást biztosít az Azure-beli virtuális gépen (VM) SQL Server. 

Ebből a cikkből megtudhatja, hogyan telepítheti át a naplófájlt egy ultra SSD-meghajtóra, hogy kihasználhassa az ultra-lemezek által kínált teljesítménybeli előnyöket. 

## <a name="back-up-database"></a>Adatbázis biztonsági mentése

Készítsen [teljes biztonsági másolatot](backup-restore.md) az adatbázisról. 

## <a name="attach-disk"></a>Lemez csatolása

Ha engedélyezte a ultradisk kompatibilitását a virtuális gépen, csatlakoztassa a ultra SSDt a virtuális géphez. 

Az ultra Disk a virtuálisgép-méretek és-régiók egy részhalmazán támogatott. A továbblépés előtt ellenőrizze, hogy a virtuális gép az ultra diskt támogató régióban, zónában és méretben van-e. Az Azure CLI vagy a PowerShell használatával [meghatározhatja és érvényesítheti a virtuális gépek méretét és régióját](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) . 

### <a name="enable-compatibility"></a>Kompatibilitás engedélyezése

A kompatibilitás engedélyezéséhez kövesse az alábbi lépéseket:

1. A [Azure Portalban](https://portal.azure.com/)nyissa meg a virtuális gépet. 
1. A virtuális gép leállítása/felszabadítása. 
1. Válassza ki a **lemezek** elemet a **Beállítások** területen, majd válassza a **További beállítások** lehetőséget. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Válassza ki a lemezek további beállításait a Azure Portal beállítások területén.":::

1. Az **Igen** lehetőség kiválasztásával **engedélyezheti az ultravékony lemezek kompatibilitását**. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Képernyőkép, amely az Igen lehetőséget mutatja.":::

1. Kattintson a **Mentés** gombra. 



### <a name="attach-disk"></a>Lemez csatolása

A Azure Portal használatával csatlakoztathat egy ultra-lemezt a virtuális géphez. Részletekért lásd: [Ultra-lemez csatlakoztatása](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

A lemez csatolása után indítsa el a virtuális gépet a Azure Portal használatával. 



## <a name="format-disk"></a>Lemez formázása

Kapcsolódjon a virtuális géphez, és formázza az ultra-lemezt.  

Az ultravékony lemez formázásához kövesse az alábbi lépéseket:

1. Kapcsolódjon a virtuális géphez RDP protokoll (RDP) használatával.
1. A [Lemezkezelés](/windows-server/storage/disk-management/overview-of-disk-management) eszköz használatával formázza és particionálja az újonnan csatlakoztatott Ultra-lemezt. 


## <a name="use-disk-for-log"></a>Lemez használata a naplóhoz

Konfigurálja SQL Server az új naplófájl használatára. Ezt a Transact-SQL (T-SQL) vagy a SQL Server Management Studio (SSMS) használatával teheti meg. A SQL Server szolgáltatási fiókhoz használt fióknak teljes hozzáféréssel kell rendelkeznie az új naplófájl helyéhez. 

### <a name="configure-permissions"></a>Engedélyek konfigurálása

1. Ellenőrizze az SQL Server által használt szolgáltatásfiókot. Ezt a SQL Server Konfigurációkezelő vagy a Services. msc használatával teheti meg.
1. Navigáljon az új lemezre. 
1. Hozzon létre egy mappát (vagy több mappát) a naplófájlhoz való használatra. 
1. Kattintson a jobb gombbal a mappára, és válassza a **Tulajdonságok** lehetőséget.
1. A **Biztonság** lapon adjon meg teljes hozzáférést a SQL Server szolgáltatásfiók számára. 
1. A beállítások mentéséhez kattintson **az OK gombra**  . 
1. Ismételje meg ezt a műveletet minden olyan gyökérszintű mappánál, amelyben SQL-adatértéket szeretne készíteni. 

### <a name="use-new-log-drive"></a>Új log meghajtó használata 

Az engedély megadása után a Transact-SQL (T-SQL) vagy a SQL Server Management Studio (SSMS) használatával válassza le az adatbázist, és helyezze át a meglévő naplófájlokat az új helyre.

   > [!CAUTION]
   > Az adatbázis leválasztása offline állapotba kerül, bezárja a kapcsolatokat, és visszahelyezi a repülés közbeni tranzakciókat. Folytassa a figyelmeztetéssel és a karbantartási időszak alatt. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

A T-SQL használatával helyezze át a meglévő fájlokat egy új helyre:

1. Kapcsolódjon SQL Server Management Studio-adatbázishoz, és nyisson meg egy **új lekérdezési** ablakot. 
1. A meglévő fájlok és helyszínek beszerzése:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Az adatbázis leválasztása: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. A fájlkezelővel helyezze át a naplófájlt az ultra lemez új helyére. 

1. Csatolja az adatbázist az új fájlok helyeinek megadásához: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

Ezen a ponton az adatbázis online állapotba kerül az új helyen található bejelentkezéssel. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

A SSMS használatával helyezze át a meglévő fájlokat egy új helyre:

1. Kapcsolódjon SQL Server Management Studio (SSMS) adatbázisához. 
1. Kattintson a jobb gombbal az adatbázisra, válassza a **Tulajdonságok** lehetőséget, majd válassza a **fájlok** lehetőséget. 
1. Jegyezze fel a meglévő fájlok elérési útját. 
1. A párbeszédpanel bezárásához kattintson **az OK gombra** . 
1. Kattintson a jobb gombbal az adatbázisra, válassza a **feladatok**  >  **Leválasztás** lehetőséget. 
1. Az adatbázis leválasztásához kövesse a varázslót. 
1. A fájlkezelő használatával manuálisan helyezheti át a naplófájlt az új helyre.
1. Az adatbázis csatolása SQL Server Management Studio
   1. Kattintson a jobb gombbal a **Object Explorer** található **adatbázisok** elemre, majd válassza az **adatbázis csatolása** lehetőséget. 
   1. A párbeszédpanel használatával adja hozzá az egyes fájlokat, beleértve a naplófájlt az új helyen. 
   1. Az adatbázis csatolásához kattintson **az OK gombra** . 

Ezen a ponton az adatbázis online állapotba kerül az új helyen található bejelentkezéssel.

---


## <a name="next-steps"></a>Következő lépések

A teljesítmény javítása érdekében további beállításokért tekintse át a [teljesítménnyel kapcsolatos ajánlott eljárásokat](performance-guidelines-best-practices.md) . 

Az Azure Virtual Machines SQL Serverának áttekintését a következő cikkekben találja:

- [A Windows rendszerű virtuális gépeken futó SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [SQL Server on Linux virtuális gépek áttekintése](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

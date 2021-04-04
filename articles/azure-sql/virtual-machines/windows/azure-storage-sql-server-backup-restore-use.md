---
title: Az Azure Storage használata SQL Server biztonsági mentéshez és visszaállításhoz | Microsoft Docs
description: Ismerje meg, hogyan készíthet biztonsági mentést SQL Server az Azure Storage-ba. Ismerteti az SQL-adatbázisok Azure Storage-ba történő biztonsági mentésének előnyeit.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: 35fff49a53f5a0a9532fd0dff841356c5deaf3ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724782"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Az Azure Storage használata SQL Server biztonsági mentéshez és visszaállításhoz
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A SQL Server 2012 SP1 CU2 kezdődően mostantól közvetlenül az Azure Blob Storage-ba is írhat SQL Server-adatbázisokról készített biztonsági mentést. Ezzel a funkcióval biztonsági mentést készíthet az Azure Blob Storage-ból, és visszaállíthatja azt. A felhőbe történő biztonsági mentés a rendelkezésre állás előnyeit, a korlátlan, földrajzilag replikált, helyszíni tárolást és az adatok felhőbe történő áttelepítését is biztosítja. `BACKUP` `RESTORE` A Transact-SQL vagy a SMO használatával adhat ki vagy állíthat be utasításokat.

## <a name="overview"></a>Áttekintés
A SQL Server 2016 új képességeket vezet be; a [fájl-pillanatkép biztonsági mentés](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) használatával szinte azonnali biztonsági mentéseket és hihetetlenül gyors visszaállításokat végezhet.

Ez a témakör azt ismerteti, hogy miért dönthet úgy, hogy az Azure Storage-t használja SQL Server biztonsági mentésekhez, majd leírja az érintett összetevőket. A cikk végén található források használatával elérheti a szolgáltatást a SQL Server biztonsági másolatokkal, és további információkat érhet el a szolgáltatás használatának megkezdéséhez.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Az Azure Blob Storage SQL Server biztonsági mentések használatának előnyei
A SQL Server biztonsági mentésekor több kihívás is van. Ezek a kihívások közé tartoznak a tárolók kezelése, a tárolási hibák kockázata, a helyszíni tároláshoz való hozzáférés és a hardverkonfiguráció. A kihívások nagy része az Azure Blob Storage SQL Server biztonsági mentésekhez való használatával foglalkozik. Vegye figyelembe a következő előnyöket:

* **Egyszerű használat**: a biztonsági mentések Azure-blobokban való tárolása kényelmes, rugalmas és könnyen elérhető a helyszínen kívüli lehetőségekkel. Ha a SQL Server biztonsági mentések számára a helyszíni tárterületet szeretné létrehozni, egyszerűen módosíthatja a meglévő parancsfájlokat/feladatokat, hogy a **biztonsági mentés URL-** szintaxisát használja. A telephelyen kívüli tárterületnek általában elég messzinek kell lennie az éles adatbázis helyétől, így elkerülhető egyetlen olyan katasztrófa, amely hatással lehet a helyszínen kívüli és az üzemi adatbázis helyeire is. Az [Azure-Blobok földrajzi replikálásának](../../../storage/common/storage-redundancy.md)kiválasztásával extra védelmi réteggel rendelkezik, amely hatással lehet az egész régióra.
* **Biztonsági mentési Archívum**: az Azure Blob Storage jobb alternatíva a gyakran használt szalagos lehetőség a biztonsági másolatok archiválásához. A szalagos tároláshoz fizikai szállításra lehet szükség a telephelyen kívüli létesítményre, valamint az adathordozók elleni védelemre vonatkozó mértékeket. A biztonsági másolatok Azure Blob Storage-tárolóban való tárolása egy azonnali, magasan elérhető és tartós archiválási lehetőséget biztosít.
* **Felügyelt hardver**: az Azure-szolgáltatásokkal nem áll rendelkezésre a hardveres felügyelet. Az Azure-szolgáltatások kezelik a hardvert, és földrajzi replikálást biztosítanak a redundancia és a hardveres hibák elleni védelem érdekében.
* **Korlátlan tárterület**: az Azure-Blobok közvetlen biztonsági mentésének engedélyezésével gyakorlatilag korlátlan tárhelyet érhet el. Azt is megteheti, hogy egy Azure-beli virtuálisgép-lemezre való biztonsági mentés a számítógép méretétől függően korlátozott. A biztonsági mentések esetében korlátozva van egy Azure-beli virtuális géphez csatolható lemezek száma. Ez a korlát 16 lemez egy extra nagy példányhoz, és kevesebb a kisebb példányok esetében.
* **Biztonsági mentés rendelkezésre állása**: az Azure-blobokban tárolt biztonsági másolatok bárhonnan és bármikor elérhetők, és könnyen elérhetők a SQL Server példányok számára, anélkül, hogy az adatbázis csatlakoztatása/leválasztása vagy letöltése és a virtuális merevlemez csatolása szükséges.
* **Cost**: csak a használt szolgáltatásért kell fizetnie. Költséghatékony lehet a helyszíni és a biztonsági mentési archiválási lehetőséggel. További információkért tekintse meg az [Azure díjszabási számológépét](https://go.microsoft.com/fwlink/?LinkId=277060 "Díjkalkulátor")és az [Azure díjszabását ismertető cikket](https://go.microsoft.com/fwlink/?LinkId=277059 "Díjszabási cikk") .
* **Tárolási Pillanatképek**: Ha az adatbázisfájlok egy Azure-blobban tárolódnak, és SQL Server 2016-et használ, a [fájl-pillanatkép biztonsági mentés](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) használatával szinte azonnali biztonsági mentéseket és hihetetlenül gyors visszaállításokat végezhet.

További részletek: [SQL Server biztonsági mentés és visszaállítás az Azure Blob Storage-](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)ban.

Az alábbi két rész az Azure Blob Storage-t mutatja be, beleértve a szükséges SQL Server összetevőket is. Fontos megérteni az összetevőket és azok interakcióját, hogy sikeresen használják az Azure Blob Storage-ból történő biztonsági mentést és visszaállítást.

## <a name="azure-blob-storage-components"></a>Azure Blob Storage-összetevők
A következő Azure-összetevők az Azure Blob Storage-ba történő biztonsági mentéskor használatosak.

| Összetevő | Leírás |
| --- | --- |
| **Storage-fiók** |A Storage-fiók az összes tárolási szolgáltatás kiindulási pontja. Az Azure Blob Storage eléréséhez először hozzon létre egy Azure Storage-fiókot. Az Azure Blob Storage szolgáltatással kapcsolatos további információkért lásd: [Az Azure Blob Storage használata](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/). |
| **Tároló** |A tároló Blobok egy csoportját biztosítja, és korlátlan számú blob tárolására képes. SQL Server biztonsági másolat Azure Blob Storage-tárolóba való írásához legalább a létrehozott gyökér-tárolóval kell rendelkeznie. |
| **Blob** |Bármilyen típusú és méretű fájl. A Blobok a következő URL-formátummal érhetők el: `https://<storageaccount>.blob.core.windows.net/<container>/<blob>` . További információ a lapok Blobokról: a [blokk-és Blobok ismertetése](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) |

## <a name="sql-server-components"></a>Összetevők SQL Server
Az alábbi SQL Server összetevőket használja az Azure Blob Storage-ba történő biztonsági mentéshez.

| Összetevő | Leírás |
| --- | --- |
| **URL-cím** |Az URL-cím egy Uniform Resource Identifier (URI) értéket ad meg egy egyedi biztonságimásolat-fájlhoz. Az URL-cím megadja a SQL Server biztonságimásolat-fájljának helyét és nevét. Az URL-címnek tényleges blobra kell mutatnia, nem csak egy tárolóra. Ha a blob nem létezik, az Azure létrehozza azt. Ha meg van adva egy meglévő blob, a Backup parancs sikertelen lesz, kivéve, ha a `WITH FORMAT` beállítás meg van adva. A következő példa a BACKUP parancsban megadható URL-címet írja be: `https://<storageaccount>.blob.core.windows.net/<container>/<FILENAME.bak>` .<br><br> A HTTPS használata ajánlott, de nem kötelező. |
| **Hitelesítőadat** |Az Azure Blob Storage-hoz való kapcsolódáshoz és hitelesítéshez szükséges információk tárolása hitelesítő adatként történik. Ahhoz, hogy a SQL Server a biztonsági mentéseket egy Azure-Blobba vagy annak visszaállítására lehessen írni, létre kell hoznia egy SQL Server hitelesítő adatot. További információ: [SQL Server hitelesítő adat](/sql/t-sql/statements/create-credential-transact-sql). |

> [!NOTE]
> A SQL Server 2016 frissült a blokk Blobok támogatásához. További részletekért tekintse meg a következő [oktatóanyagot: Microsoft Azure Blob Storage használata SQL Server 2016-adatbázisokkal](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) .
> 

## <a name="next-steps"></a>Következő lépések

1. Ha még nem rendelkezik Azure-fiókkal, hozzon létre egyet. Ha kiértékeli az Azure-t, vegye figyelembe az [ingyenes próbaverziót](https://azure.microsoft.com/free/).
2. Ezután folytassa a következő oktatóanyagok egyikét, amely végigvezeti a Storage-fiók létrehozásán és a visszaállításon.
   
   * **SQL Server 2014**: [oktatóanyag: SQL Server 2014 biztonsági mentés és visszaállítás Microsoft Azure Blob Storage](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url)-ba.
   * **SQL Server 2016**: [oktatóanyag: a Microsoft Azure Blob storage használata SQL Server 2016-adatbázisokkal](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. Tekintse át a [SQL Server biztonsági mentéssel és visszaállítással kezdődő további dokumentációt Microsoft Azure Blob Storage-](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)ban.

Ha bármilyen problémája van, tekintse át a [SQL Server biztonsági mentés az URL-címekkel kapcsolatos ajánlott eljárásokat és hibaelhárítást](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)ismertető témakört.

Más SQL Server biztonsági mentési és visszaállítási lehetőségekért lásd: [az Virtual Machines Azure-beli SQL Server biztonsági mentése és visszaállítása](backup-restore.md).
---
title: Fájlok és könyvtárak Azure Batch
description: Ismerje meg a fájlokat és a címtárakat, valamint azt, hogyan használják őket egy Azure Batch munkafolyamatban fejlesztési szempontból.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: e7babb7e2cfdbbe78f61be766c549c1e80cacf98
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791119"
---
# <a name="files-and-directories-in-azure-batch"></a>Fájlok és könyvtárak Azure Batch

Azure Batch minden egyes feladathoz tartozik egy munkakönyvtár, amelyben nulla vagy több fájlt és könyvtárat hoz létre. Ez a munkakönyvtár használható a tevékenység által futtatott program, az ez által feldolgozott adatok, valamint az eredményként létrejövő kimenet tárolására. A tevékenységhez tartozó összes fájlnak és könyvtárnak a tevékenység felhasználója a tulajdonosa.

A Batch szolgáltatás *gyökérkönyvtárként*elérhetővé teszi a csomópont fájlrendszerének egy részét. A tevékenységek az `AZ_BATCH_NODE_ROOT_DIR` környezeti változóra hivatkozva tudják elérni a gyökérkönyvtárat. A környezeti változók használatával kapcsolatos további információért lásd: [Környezeti beállítások tevékenységekhez](jobs-and-tasks.md#environment-settings-for-tasks).

A gyökérkönyvtár a következő könyvtárstruktúrát tartalmazza:

! [Számítási csomópont címtárának szerkezete] [media\files-and-directories\node-folder-structure.png]

- **alkalmazások**: a számítási csomóponton telepített alkalmazáscsomag részleteiről tartalmaz információkat. A tevékenységek az `AZ_BATCH_APP_PACKAGE` környezeti változóra hivatkozva tudják elérni ezt a könyvtárat.

- **fsmounts**: a könyvtár minden olyan fájlrendszert tartalmaz, amely egy számítási csomópontra van csatlakoztatva. A tevékenységek az `AZ_BATCH_NODE_MOUNTS_DIR` környezeti változóra hivatkozva tudják elérni ezt a könyvtárat. További információ: [virtuális fájlrendszer csatlakoztatása batch-készlethez](virtual-file-mount.md).

- **shared**: ez a könyvtár olvasási/írási hozzáférést nyújt a csomóponton futó *összes* tevékenység számára. Ebben a könyvtárban a csomóponton futó összes tevékenység jogosult fájlokat létrehozni, olvasni, módosítani és törölni. A tevékenységek az `AZ_BATCH_NODE_SHARED_DIR` környezeti változóra hivatkozva tudják elérni ezt a könyvtárat.

- **startup**: az indítási tevékenység ezt a könyvtárat használja munkakönyvtárként. Az indítási tevékenység által a csomópontra letöltött összes fájlt ez a könyvtár tárolja. Ebben a könyvtárban az indítási tevékenység jogosult fájlokat létrehozni, olvasni, módosítani és törölni. A tevékenységek az `AZ_BATCH_NODE_STARTUP_DIR` környezeti változóra hivatkozva tudják elérni ezt a könyvtárat.

- **változékony**: Ez a könyvtár belső célokra szolgál. Nem garantálható, hogy a címtárban lévő fájlok vagy maga a címtár a jövőben is létezni fog.

- **workitems**: Ez a könyvtár tartalmazza a feladatok könyvtárait és azok feladatait a számítási csomóponton.

    A **workitems** könyvtárban a csomóponton futó minden egyes feladathoz létrejön egy **feladatok** könyvtár. Ez a könyvtár a környezeti változóra hivatkozó hivatkozással érhető el `AZ_BATCH_TASK_DIR` .
    
    Az egyes **feladatok** könyvtárában a Batch szolgáltatás létrehoz egy munkakönyvtárat ( `wd` ), amelynek egyedi elérési útját a `AZ_BATCH_TASK_WORKING_DIR` környezeti változó adja meg. Ez a könyvtár olvasási/írási hozzáférést nyújt a tevékenységhez. A tevékenység jogosult fájlokat létrehozni, olvasni, módosítani és törölni ebben a könyvtárban. A rendszer a tevékenységhez beállított *RetentionTime* korlátozás értékének megfelelően őrzi meg a könyvtárat.

    A `stdout.txt` és a `stderr.txt` fájlok a **feladatok** mappába kerülnek a feladat végrehajtása során.

> [!IMPORTANT]
> Amikor eltávolít egy csomópontot a készletből, a csomóponton tárolt összes fájlt is eltávolítja.

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg a [hibák és az észlelés](error-handling.md) Azure Batchával.
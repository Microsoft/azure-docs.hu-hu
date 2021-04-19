---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718199"
---
Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Storage-fiókok** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Tárfiókok lehetőséget.**
1. A megjelenő **Tárfiókok** ablakban válassza az **+ Új lehetőséget.**
1. Az Alapvető **beállítások panelen** válassza ki azt az előfizetést, amelyben létre kell hoznia a tárfiókot.
1. Az **Erőforráscsoport mezőben** válassza ki a kívánt erőforráscsoportot, vagy hozzon létre egy újat.  Az Azure-erőforráscsoportokkal kapcsolatos további információkért lásd [a Azure Resource Manager áttekintését.](../articles/azure-resource-manager/management/overview.md)
1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3–24 karakter hosszúságúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.
1. Válasszon ki egy régiót a tárfiókhoz, vagy használja az alapértelmezett régiót.
1. Válasszon teljesítményszintet. Az alapértelmezett szint a *Standard.*
1. Adja meg, hogyan replikálja a rendszer a tárfiókot. Az alapértelmezett redundancia a *georedundáns tárolás (GRS).* További információ az elérhető replikációs lehetőségekről: [Azure Storage redundancia.](../articles/storage/common/storage-redundancy.md)
1. További beállítások a **Speciális,** Hálózat, **Adatvédelem** és Címkék **panelen érhetők** el.  A név Azure Data Lake Storage válassza a Speciális **panelt,** majd állítsa a **Hierarchikus névtér beállítást** Engedélyezve **beállításra.** További információ: Azure Data Lake Storage Gen2 [bemutatása](../articles/storage/blobs/data-lake-storage-introduction.md)
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Válassza a **Létrehozás** lehetőséget.

Az alábbi képen az  új tárfiók Alapvető beállítások panelének beállításai láthatóak:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Képernyőkép a tárfiók létrehozásáról a Azure Portal." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::

---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98109345"
---
Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Storage-fiókok** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Storage-fiókok** lehetőséget.
1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Az **alapvető beállítások** lapon válassza ki azt az előfizetést, amelyben létre szeretné hozni a Storage-fiókot.
1. Az **erőforráscsoport** mezőben válassza ki a kívánt erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot.  További információ az Azure-erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../articles/azure-resource-manager/management/overview.md)).
1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.
1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
1. Válasszon teljesítményszint-szintet. Az alapértelmezett szint a *standard*.
1. Állítsa a **Fiók típusa** mezőt a *Storage v2 (általános célú v2)* értékre.
1. Annak meghatározása, hogy a rendszer hogyan replikálja a Storage-fiókot. Az alapértelmezett replikációs beállítás a következő: *olvasási hozzáférés geo-redundáns tárolás (ra-GRS)*. Az elérhető replikációs lehetőségekkel kapcsolatos további információkért lásd: [Azure Storage redundancia](../articles/storage/common/storage-redundancy.md).
1. A **hálózatkezelés**, az **Adatvédelem**, a **speciális** és a **címkék** lapon további beállítások érhetők el. A Azure Data Lake Storage használatához válassza a **speciális** fület, majd állítsa be a **hierarchikus névteret** **engedélyezve** értékre. További információ: [Azure Data Lake Storage Gen2 bevezetés](../articles/storage/blobs/data-lake-storage-introduction.md)
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Válassza a **Létrehozás** lehetőséget.

Az alábbi képen az új Storage-fiók **alapjai** lapon található beállítások láthatók:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Tárfiók Azure Portalon történő létrehozását bemutató képernyőkép":::

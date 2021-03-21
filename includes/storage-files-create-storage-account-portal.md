---
title: tároló-fájlok-létrehozás-tároló-fiók-portál
description: Tárfiók létrehozása az Azure Files számára.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03cf20e5c796a7092dc16c466934f377c945ad48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96509308"
---
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. A tárfiókok korlátlan számú megosztást tartalmazhatnak, a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

Tárfiók létrehozása:

1. A bal oldali menüben válassza ki **+** az erőforrás létrehozásához.
1. Írja be a **tárfiók** kifejezést a keresőmezőbe, válassza a **Tárfiók – blob, fájl, tábla, üzenetsor** elemet, majd válassza a **Létrehozás** gombot.
    ![Az erőforrás-keresési párbeszédpanelen megjelenő tárfiókbejegyzés kinézetének képernyőképe](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. A **Név** mezőbe írja be a *mystorageacct* kifejezést, majd írjon utána néhány véletlenszerű számot, amíg meg nem jelenik a név egyediségét jelző zöld pipa. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Jegyezze fel a tárfiók nevét. Erre később még szüksége lesz. 
1. A **telepítési modellben** hagyja meg a **Resource Manager** alapértelmezett értékét. További információ az Azure Resource Manager és a klasszikus üzembe helyezési modell közötti különbségekről: [Az üzembe helyezési modellek és az erőforrások állapotának ismertetése](../articles/azure-resource-manager/management/deployment-models.md).
1. A **teljesítmény** területen tartsa meg az alapértelmezett **standard** értéket.
    
    > [!NOTE]
    > Ez a rövid útmutató egy szabványos fájlmegosztást hoz létre, de ha prémium szintű fájlmegosztást szeretne használni, válassza a **prémium** helyet.

1. A **Fiók típusa** mezőben válassza a **StorageV2** lehetőséget. További információ a különböző típusú tárfiókokkal kapcsolatban: [Az Azure-tárfiókok ismertetése](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

    > [!NOTE]
    > Ez a rövid útmutató egy általános célú v2-fiókot hoz létre. Ha a prémium szintű fájlmegosztást szeretné használni, válassza a **FileStorage** helyet.

1. A **Replikáció** mezőben válassza a **Helyileg redundáns tárolás (LRS)** lehetőséget. 
1. A **Biztonságos átvitelre van szükség** mezőben ajánlott mindig az **Engedélyezve** értéket választani. További információ erről a beállításról: [Az átvitel közbeni titkosítás ismertetése](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
1. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létrehozza a tárfiókot. Ha csak egy előfizetéssel rendelkezik, akkor az az alapértelmezett.
1. Az **Erőforráscsoport** területen válassza az **Új létrehozása** lehetőséget. Névként adja meg a *myResourceGroup* elnevezést.
1. A **Hely** mezőben válassza az **USA keleti régiója** lehetőséget.
1. A **Virtuális hálózatok** mezőben hagyja meg a **Letiltva** alapértelmezett beállítást. 
1. A tárfiók könnyebb megtalálásához jelölje be a **Rögzítés az irányítópulton** lehetőséget.
1. Ha végzett, válassza a **Létrehozás** gombot az üzembe helyezés indításához.
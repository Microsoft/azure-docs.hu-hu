---
title: Törölt irányítópultok visszaállítása az Azure Portalon
description: Ha töröl egy közzétett irányítópultot a Azure Portal, akkor helyreállíthatja az irányítópultot.
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.openlocfilehash: 095964691a3cb22f8a805af2e8fe37af4c47cb28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745621"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Törölt irányítópultok visszaállítása az Azure Portalon

Ha a nyilvános Azure-felhőben van, és töröl egy _közzétett_ irányítópultot a Azure Portal, akkor a törléstől számított 14 napon belül helyreállíthatja az irányítópultot. Ha egy Azure Government-felhőben van, vagy az irányítópult nincs közzétéve, nem állíthatja helyre, és újra kell építenie. Az irányítópultok közzétételével kapcsolatos további információkért lásd: [irányítópult közzététele](azure-portal-dashboard-share-access.md#publish-dashboard). A közzétett irányítópult helyreállításához kövesse az alábbi lépéseket:

1. A Azure Portal menüben válassza az **erőforráscsoportok** elemet, majd válassza ki azt az erőforráscsoportot, amelyben az irányítópultot közzétette (alapértelmezés szerint az **irányítópultok** neve).

1. A **műveletnapló** területen bontsa ki az **irányítópult törlése** műveletet. Válassza a **változások előzményei** lapot, majd válassza a lehetőséget **\<deleted resource\>** .

    ![Képernyőkép a változási előzmények lapról](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Válassza ki és másolja ki a bal oldali ablaktábla tartalmát, majd mentse a fájlt egy _. JSON_ kiterjesztésű szövegfájlba. A portál a JSON-fájllal hozza létre újra az irányítópultot.

    ![Képernyőkép a Change History diffről](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. A Azure Portal menüben válassza ki az **irányítópultok** elemet, majd válassza a **feltöltés** lehetőséget.

    ![Az irányítópult feltöltésének képernyőképe](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Válassza ki a mentett JSON-fájlt. A portál újból létrehozza az irányítópultot a törölt irányítópulttal megegyező névvel és elemekkel.

1. Válassza a **megosztás** lehetőséget az irányítópult közzétételéhez, majd hozza létre újra a megfelelő hozzáférés-vezérlést.

    ![Képernyőkép az irányítópult megosztásáról](media/recover-shared-deleted-dashboard/dashboard-share.png)

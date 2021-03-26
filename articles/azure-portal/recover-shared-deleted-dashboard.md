---
title: Törölt irányítópultok visszaállítása az Azure Portalon
description: Ha töröl egy közzétett irányítópultot a Azure Portal, akkor helyreállíthatja az irányítópultot.
ms.date: 03/25/2021
ms.topic: troubleshooting
ms.openlocfilehash: 96d330872327f8719e7cc4287415d86fd0ae5fd4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559713"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Törölt irányítópultok visszaállítása az Azure Portalon

Ha a globális Azure-felhőben van, és töröl egy _közzétett_ irányítópultot a Azure Portal, akkor a törléstől számított 14 napon belül helyreállíthatja az irányítópultot. Ha Azure Government-felhőben vagy az irányítópulton nincs közzétéve, nem állíthatja helyre, és újra kell építenie. Az irányítópultok közzétételével kapcsolatos további információkért lásd: [irányítópult közzététele](azure-portal-dashboard-share-access.md#publish-a-dashboard). A közzétett irányítópult helyreállításához kövesse az alábbi lépéseket:

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

---
title: Blockchain-alkalmazás verziószámozása – Azure Blockchain Workbench
description: Az alkalmazások verzióinak használata az Azure Blockchain Workbench előzetes verziójában.
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 2a70112fd0ab6e2f664ca48265c121936b01e58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85209878"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Az Azure Blockchain Workbench előzetes verziójának alkalmazása

Létrehozhat és használhat egy Azure Blockchain Workbench előzetes verziójú alkalmazás több verzióját is. Ha ugyanannak az alkalmazásnak több verziója van feltöltve, a korábbi verziók is elérhetők, és a felhasználók kiválaszthatják, hogy melyik verziót szeretnék használni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy Blockchain Workbench üzembe helyezése. További információ: az [Azure Blockchain Workbench üzembe](deploy.md) helyezése az üzembe helyezés részleteiről
* Egy üzembe helyezett blockchain-alkalmazás a Blockchain Workbenchben. Lásd: [blockchain-alkalmazás létrehozása az Azure Blockchain workbenchben](create-app.md)

## <a name="add-an-app-version"></a>Alkalmazás verziójának hozzáadása

Új verzió hozzáadásához töltse fel az új konfigurációs és intelligens szerződési fájlokat a Blockchain Workbenchbe.

1. Egy böngészőben nyissa meg a Blockchain Workbench webcímet. Ha például `https://{workbench URL}.azurewebsites.net/` meg szeretné tudni, hogyan keresheti meg a Blockchain Workbench webcímet, tekintse meg a [Blockchain Workbench webes URL-](deploy.md#blockchain-workbench-web-url) címét.
2. Jelentkezzen be [Blockchain Workbench-rendszergazdaként](manage-users.md#manage-blockchain-workbench-administrators).
3. Válassza ki a frissíteni kívánt blockchain alkalmazást egy másik verzióval.
4. Válassza a **verzió hozzáadása** lehetőséget. Megjelenik a **verzió hozzáadása** panel.
5. Válassza ki az új verzióra vonatkozó szerződés konfigurációját és a feltöltendő szerződési kód fájljait. A konfigurációs fájl automatikusan érvényesítve lesz. Az alkalmazás központi telepítése előtt javítsa ki az érvényesítési hibákat.
6. Válassza a **verzió hozzáadása** lehetőséget az új blockchain-alkalmazás verziójának hozzáadásához.

    ![Új verzió hozzáadása](media/version-app/add-version.png)

A blockchain alkalmazás üzembe helyezése néhány percet is igénybe vehet. Az üzembe helyezés befejezésekor frissítse az alkalmazás oldalát. Az alkalmazás kiválasztása és a **korábbi verziók** gomb kiválasztásával az alkalmazás korábbi verzióit jelenítheti meg.

> [!IMPORTANT]
> Az alkalmazás korábbi verziói le vannak tiltva. Egyénileg újra engedélyezheti a korábbi verziókat.
>
> Előfordulhat, hogy a tagokat újra hozzá kell adnia az alkalmazás szerepköreihez, ha az új verzióban módosult az alkalmazás szerepkörei.

## <a name="using-app-versions"></a>Alkalmazás-verziók használata

Alapértelmezés szerint az alkalmazás legújabb engedélyezett verziója a Blockchain Workbenchben van használatban. Ha egy alkalmazás egy korábbi verzióját szeretné használni, először ki kell választania az alkalmazás oldalának verzióját.

1. A Blockchain Workbench alkalmazás szakaszban jelölje be a használni kívánt szerződést tartalmazó alkalmazás jelölőnégyzetét. Ha a korábbi verziók engedélyezve vannak, a verziótörténete gomb elérhető.
2. Válassza ki a **verziótörténete** gombot.
3. A korábbi verziók ablaktáblán válassza ki az alkalmazás verzióját a *Módosítás dátuma* oszlopban található hivatkozásra kattintva.

    ![Korábbi verzió kiválasztása](media/version-app/use-version.png)

    Létrehozhat új szerződéseket, vagy műveleteket hajthat végre a korábbi verziójú szerződések esetében. Az alkalmazás verziója megjelenik az alkalmazás neve után, és egy figyelmeztetés jelenik meg a régebbi verzióról.

## <a name="next-steps"></a>Következő lépések

* [Azure Blockchain Workbench – hibaelhárítás](troubleshooting.md)

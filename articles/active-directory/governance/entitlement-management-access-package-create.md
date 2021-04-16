---
title: Új hozzáférési csomag létrehozása a jogosultságkezelésben – Azure AD
description: Megtudhatja, hogyan hozhat létre új hozzáférési csomagot a jogosultságkezelésben megosztani Azure Active Directory erőforrásokból.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0312d905284f8c5a9817e9550d340bf6135032
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532200"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Új hozzáférési csomag létrehozása az Azure AD-jogosultságkezelésben

A hozzáférési csomagok lehetővé teszik az erőforrások és szabályzatok egy alkalommal való beállítását, amely automatikusan felügyeli a hozzáférést a hozzáférési csomag teljes életciklusa alatt. Ez a cikk bemutatja, hogyan hozhat létre új hozzáférési csomagot.

## <a name="overview"></a>Áttekintés

Minden hozzáférési csomagot katalógusnak nevezett tárolóba kell helyezni. A katalógus határozza meg, hogy milyen erőforrásokat adhat hozzá a hozzáférési csomaghoz. Ha nem ad meg katalógust, a hozzáférési csomag az Általános katalógusba kerül. A meglévő hozzáférési csomagokat jelenleg nem lehet másik katalógusba áthelyezni.

Ha Ön hozzáférésicsomag-kezelő, nem adhat hozzá saját erőforrásait a katalógushoz. A katalógusban elérhető erőforrások használata korlátozott. Ha erőforrásokat kell hozzáadnia egy katalógushoz, kérdezze meg a katalógus tulajdonosát.

Minden hozzáférési csomagnak legalább egy szabályzatot kell tartalmaznie. A szabályzatok határozzák meg, hogy ki kérheti a hozzáférési csomagot, valamint a jóváhagyási és életciklus-beállításokat. Új hozzáférési csomag létrehozásakor létrehozhat egy kezdeti szabályzatot a címtárban található felhasználók, a címtárban nem található felhasználók, csak a rendszergazdai közvetlen hozzárendelések számára, vagy dönthet úgy, hogy később létrehozza a szabályzatot.

![Hozzáférési csomag létrehozása](./media/entitlement-management-access-package-create/access-package-create.png)

Az új hozzáférési csomag létrehozásához szükséges magas szintű lépések a következőek.

1. Az Identity Governanceban indítsa el az új hozzáférési csomag létrehozási folyamatát.

1. Válassza ki azt a katalógust, amelyben létre szeretné hozni a hozzáférési csomagot.

1. Erőforrások hozzáadása a katalógusból a hozzáférési csomaghoz.

1. Rendeljen erőforrás-szerepköröket az egyes erőforrásokhoz.

1. Adja meg azokat a felhasználókat, akik hozzáférést kérhetnek.

1. Adja meg a jóváhagyási beállításokat.

1. Adja meg az életciklus beállításait.

## <a name="start-new-access-package"></a>Új hozzáférési csomag létrehozása

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda, katalógustulajdonos vagy hozzáférési csomagkezelő

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Kattintson **a Azure Active Directory,** majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Hozzáférési csomagok elemre.**

1. Kattintson **az Új hozzáférési csomag elemre.**
   
    ![Jogosultságkezelés a Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Alapvető beállítások

Az Alapvető **beállítások lapon** adjon nevet a hozzáférési csomagnak, és adja meg, hogy melyik katalógusban hozza létre a hozzáférési csomagot.

1. Adja meg a hozzáférési csomag megjelenítendő nevét és leírását. A felhasználók ezt az információt a hozzáférési csomagra vonatkozó kérés elküldésekor látják.

1. A Katalógus **legördülő** listában válassza ki azt a katalógust, amelybe létre szeretné hozni a hozzáférési csomagot. Előfordulhat például, hogy rendelkezik egy katalógustulajdonossal, aki az összes lekért marketingerőforrást kezeli. Ebben az esetben kiválaszthatja a marketingkatalógust.

    Csak olyan katalógusokat fog látni, amelyekben hozzáférési csomagok létrehozására van engedélye. Egy meglévő katalógusban lévő hozzáférési csomag létrehozásához rendszergazdai vagy felhasználói globális rendszergazda kell lennie, vagy katalógustulajdonosnak vagy hozzáférésicsomag-kezelőnek kell lennie a katalógusban.

    ![Hozzáférési csomag – Alapok](./media/entitlement-management-access-package-create/basics.png)

    Ha Ön egy globális rendszergazda, egy felhasználói rendszergazda vagy katalógus létrehozója, és a hozzáférési csomagot egy új katalógusban szeretné létrehozni, amely nem szerepel a listában, kattintson az Új katalógus **létrehozása elemre.** Adja meg a katalógus nevét és leírását, majd kattintson a **Létrehozás gombra.**

    A létrehozott hozzáférési csomag és a benne foglalt erőforrások hozzá lesznek adva az új katalógushoz. Később további katalógustulajdonosokat is hozzáadhat.

1. Kattintson a **Tovább** gombra.

## <a name="resource-roles"></a>Erőforrás-szerepkörök

Az **Erőforrás-szerepkörök** lapon kiválaszthatja a hozzáférési csomagba foglalni kívánt erőforrásokat. Azok a felhasználók, akik lekérték és megkapják a hozzáférési csomagot, megkapják a hozzáférési csomagban található összes erőforrás-szerepkört.

1. Kattintson a hozzáadni kívánt erőforrástípusra (Csoportok és **csapatok,** **Alkalmazások** vagy **SharePoint-webhelyek**).

1. A megjelenő Kijelölés panelen válasszon ki egy vagy több erőforrást a listából.

    ![Hozzáférési csomag – Erőforrás-szerepkörök](./media/entitlement-management-access-package-create/resource-roles.png)

    Ha az általános katalógusban vagy egy új katalógusban létrehozza a hozzáférési csomagot, bármely erőforrást választhat a saját címtárából. Legalább egy felhasználónak, globális rendszergazda rendszergazdának vagy katalóguskészítőnek kell lennie.

    Ha egy meglévő katalógusban hoz létre hozzáférési csomagot, akkor a katalógusban már meglévő erőforrásokat is kiválaszthatja anélkül, hogy annak a tulajdonában lenne.

    Ha Ön egy globális rendszergazda, egy felhasználói rendszergazda vagy egy katalógus tulajdonosa, akkor olyan erőforrásokat is kiválaszthatja, amelyek még nem találhatóak meg a katalógusban. Ha olyan erőforrásokat választ ki, amelyek jelenleg nem a kijelölt katalógusban vannak, akkor ezek az erőforrások a katalógushoz lesznek hozzáadva, hogy más katalógus-rendszergazdák hozzáférési csomagokat hoznak létre a segítségével. A katalógushoz hozzáadható összes erőforrást a  Kijelölés panel tetején található Összes megjelenítése jelölőnégyzet bejelével láthatja. Ha csak azokat az erőforrásokat szeretné kiválasztani, amelyek jelenleg  a kiválasztott katalógusban vannak, hagyja bejelölve az Összes kijelölése (alapértelmezett állapot) jelölőnégyzetet.

1. Miután kiválasztotta az erőforrásokat, a Szerepkör **listában** válassza ki azt a szerepkört, amely szerint a felhasználókat hozzá szeretné rendelni az erőforráshoz.

    ![Hozzáférési csomag – Erőforrás-szerepkör kiválasztása](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kattintson a **Tovább** gombra.

>[!NOTE]
>Dinamikus csoportokat hozzáadhat egy katalógushoz és egy hozzáférési csomaghoz. A dinamikus csoporterőforrás hozzáférési csomagban való kezelésekor azonban csak a Tulajdonos szerepkört választhatja ki.

## <a name="requests"></a>Kérelmek

A **Kérelmek lapon hozza létre** az első szabályzatot, amely meghatározza, hogy ki kérheti a hozzáférési csomagot és a jóváhagyási beállításokat. Később további kérelem-szabályzatokat hozhat létre, amelyek lehetővé teszik, hogy a felhasználók további csoportjai saját jóváhagyási beállításokkal kérelmezik a hozzáférési csomagot.

![Hozzáférési csomag – Kérelmek lap](./media/entitlement-management-access-package-create/requests.png)

Attól függően, hogy kitől szeretné kérni ezt a hozzáférési csomagot, hajtsa végre a következő szakaszokban található lépéseket.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Ellenőrzés és létrehozás

Az Áttekintés **+ létrehozás lapon** áttekintheti a beállításokat, és ellenőrizheti az érvényesítési hibákat.

1. A hozzáférési csomag beállításainak áttekintése

    ![Hozzáférési csomag – Szabályzatbeállítás engedélyezése](./media/entitlement-management-access-package-create/review-create.png)

1. A **hozzáférési csomag létrehozásához** kattintson a Létrehozás gombra.

    Az új hozzáférési csomag megjelenik a hozzáférési csomagok listájában.

## <a name="creating-an-access-package-programmatically"></a>Hozzáférési csomag programozott módon történő létrehozása

Hozzáférési csomagot a használatával is Microsoft Graph.  Egy megfelelő szerepkörben, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználó a következőhöz `EntitlementManagement.ReadWrite.All` hívhatja az API-t:

1. [Listizálja az accessPackageResources](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta&preserve-view=true) erőforrást a katalógusban, és hozzon létre [egy accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta&preserve-view=true) elérést a katalógusban még nem található erőforrásokhoz.
1. [Listaz accessPackageResourceRoles minden](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) egyes accessPackageResource egy accessPackageCatalogban. Ezt a szerepkörlistát használjuk majd egy szerepkör kiválasztásához az accessPackageResourceRoleScope későbbi létrehozásakor.
1. [Hozzon létre egy accessPackage et.](/graph/tutorial-access-package-api&view=graph-rest-beta&preserve-view=true)
1. [Hozzon létre egy accessPackageAssignmentPolicy létrehozására.](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta&preserve-view=true)
1. [Hozzon létre egy accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) hatókört a hozzáférési csomagban szükséges összes erőforrás-szerepkörhöz.

## <a name="next-steps"></a>Következő lépések

- [Hivatkozás megosztása hozzáférési csomag kérése érdekében](entitlement-management-access-package-settings.md)
- [Hozzáférési csomag erőforrás-szerepkörének módosítása](entitlement-management-access-package-resources.md)
- [Felhasználó közvetlen hozzárendelése a hozzáférési csomaghoz](entitlement-management-access-package-assignments.md)
- [Hozzáférési csomag hozzáférési felülvizsgálatának létrehozása](entitlement-management-access-reviews-create.md)

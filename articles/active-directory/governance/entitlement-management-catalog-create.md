---
title: Erőforrás& katalógus létrehozása jogosultságkezelésben – Azure AD
description: Megtudhatja, hogyan hozhat létre erőforrásokat és hozzáférési csomagokat tartalmazó új tárolót a Azure Active Directory kezelésével.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8cea26bcb0926cd3af360a6489377767d681079
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532553"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Erőforráskatalógus létrehozása és kezelése az Azure AD-jogosultságkezelésben

## <a name="create-a-catalog"></a>Katalógus létrehozása

A katalógus erőforrásokat és hozzáférési csomagokat tartalmazó tároló. Katalógust akkor hoz létre, ha a kapcsolódó erőforrásokat és hozzáférési csomagokat szeretné csoportosíteni. Aki létrehozza a katalógust, az lesz az első katalógustulajdonos. A katalógus tulajdonosa további katalógustulajdonosokat adhat hozzá.

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda vagy katalógus létrehozója

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Katalógusok elemre.**

    ![Jogosultságkezelési katalógusok a Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Kattintson az **Új katalógus elemre.**

1. Adjon meg egy egyedi nevet a katalógusnak, és adjon meg egy leírást.

    A felhasználók ezt az információt egy hozzáférési csomag részleteiben láthatják.

1. Ha azt szeretné, hogy a katalógusban található hozzáférési csomagok a létrehozásuk  után a felhasználók számára is elérhetők legyen, állítsa Az engedélyezve beállítást **Igenre.**

1. Ha engedélyezni szeretné, hogy a kiválasztott külső könyvtárak felhasználói hozzáférési csomagokat kérjenek ebben a katalógusban, állítsa Az engedélyezve **külső** felhasználók számára beállítást **Igen beállításra.**

    ![Új katalógus panel](./media/entitlement-management-shared/new-catalog.png)

1. Kattintson **a Létrehozás gombra** a katalógus létrehozásához.

### <a name="creating-a-catalog-programmatically"></a>Katalógus programozott módon történő létrehozása

Katalógust a katalógus használatával is Microsoft Graph.  A megfelelő szerepkörű, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználók az API-t hívva létrehozhatnak `EntitlementManagement.ReadWrite.All` [egy accessPackageCatalog elérést.](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)

## <a name="add-resources-to-a-catalog"></a>Erőforrások hozzáadása katalógushoz

Ahhoz, hogy az erőforrásokat egy hozzáférési csomagba foglalja, az erőforrásoknak léteznie kell egy katalógusban. A felvehető erőforrások típusai a csoportok, az alkalmazások és a SharePoint Online-webhelyek. A csoportok a felhőben vagy Microsoft 365-csoportok Azure AD biztonsági csoportokban is létre lehet hozva. Az alkalmazások vállalati Azure AD-alkalmazások is, beleértve a SaaS-alkalmazásokat és az Azure AD-be összevont saját alkalmazásokat is. A webhelyek SharePoint Online-webhelyek vagy SharePoint Online-webhelycsoportok is lehetnek.

**Előfeltételként szükséges szerepkör:** Lásd: [Erőforrások katalógushoz való hozzáadásához szükséges szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Katalógusok** elemre, majd nyissa meg azt a katalógust, amelybe erőforrásokat szeretne hozzáadni.

1. A bal oldali menüben kattintson az Erőforrások **elemre.**

1. Kattintson **az Erőforrások hozzáadása elemre.**

1. Kattintson egy erőforrástípusra: **Csoportok és csapatok,** **Alkalmazások**, vagy **SharePoint-webhelyek**.

    Ha nem látja a hozzáadni kívánt erőforrást, vagy nem tud hozzáadni egy erőforrást, győződjön meg arról, hogy az Azure AD-címtárhoz és a jogosultságok kezeléséhez szükséges szerepkört használja. Előfordulhat, hogy a szükséges szerepkörökkel rendelkezőknek hozzá kell adniuk az erőforrást a katalógushoz. További információ: Erőforrások katalógushoz való hozzáadásához [szükséges szerepkörök.](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Válasszon ki egy vagy több, a katalógushoz hozzáadni kívánt típusú erőforrást.

    ![Erőforrások hozzáadása katalógushoz](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Ha végzett, kattintson az **Add (Hozzáadás) gombra.**

    Ezek az erőforrások mostantól a katalógusban található hozzáférési csomagokban is szerepelnek.

### <a name="add-a-multi-geo-sharepoint-site-preview"></a>Multi-geo SharePoint-webhely hozzáadása (előzetes verzió)

1. Ha a [Multi-Geo engedélyezve](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) van a SharePointhoz, válassza ki azt a környezetet, amelyből webhelyeket szeretne választani.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Hozzáférési csomag – Erőforrás-szerepkörök hozzáadása – SharePoint Multi-geo-helyek kiválasztása":::

1. Ezután válassza ki a katalógushoz hozzáadni kívánt webhelyeket. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Erőforrás hozzáadása katalógushoz programozott módon

Erőforrást a katalógushoz is hozzáadhat a Microsoft Graph.  A megfelelő szerepkörben vagy katalógusban és erőforrás-tulajdonosban, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználók az API-t hívva létrehozhatnak `EntitlementManagement.ReadWrite.All` [egy accessPackageResourceRequest kérelmet.](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)

## <a name="remove-resources-from-a-catalog"></a>Erőforrások eltávolítása katalógusból

Az erőforrásokat eltávolíthatja a katalógusból. Egy erőforrás csak akkor távolítható el a katalógusból, ha a katalógus egyik hozzáférési csomagja sem használja.

**Előfeltételként szükséges szerepkör:** Lásd: [Erőforrások katalógushoz való hozzáadásához szükséges szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Katalógusok** elemre, majd nyissa meg azt a katalógust, amelyből erőforrásokat szeretne eltávolítani.

1. A bal oldali menüben kattintson az Erőforrások **elemre.**

1. Válassza ki az eltávolítani kívánt erőforrásokat.

1. Kattintson **az Eltávolítás** gombra (vagy kattintson a három pontra (**...**), majd az Erőforrás **eltávolítása elemre.**


## <a name="add-additional-catalog-owners"></a>További katalógustulajdonosok hozzáadása

A katalógust létrehozó felhasználó lesz az első katalógustulajdonos. A katalógusok felügyeletének delegálható, ha felhasználókat ad hozzá a katalógus tulajdonosi szerepköréhez. Ez segít a katalóguskezelési feladatok megosztásában. 

Kövesse az alábbi lépéseket egy felhasználó katalógustulajdonosi szerepkörhöz való hozzárendeléshez:

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda vagy katalógustulajdonos

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Katalógusok** elemre, majd nyissa meg azt a katalógust, amelybe rendszergazdákat szeretne hozzáadni.

1. A bal oldali menüben kattintson a **Szerepkörök és rendszergazdák elemre.**

    ![Katalógusok szerepkörei és rendszergazdái](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kattintson **a Tulajdonosok hozzáadása elemre** a szerepkörök tagjainak kiválasztásához.

1. A **tagok hozzáadásához** kattintson a Kijelölés gombra.

## <a name="edit-a-catalog"></a>Katalógus szerkesztése

Szerkesztheti a katalógus nevét és leírását. A felhasználók ezt az információt egy hozzáférési csomag részleteiben látják.

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda vagy katalógustulajdonos

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Katalógusok** elemre, majd nyissa meg a szerkeszteni kívánt katalógust.

1. A katalógus Áttekintés **lapján** kattintson a Szerkesztés **elemre.**

1. Szerkessze a katalógus nevét, leírását vagy engedélyezett beállításait.

    ![Katalógusbeállítások szerkesztése](./media/entitlement-management-shared/catalog-edit.png)

1. Kattintson a **Mentés** gombra.

## <a name="delete-a-catalog"></a>Katalógus törlése

A katalógusok törölhetők, de csak akkor, ha nem tartalmaz hozzáférési csomagokat.

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda vagy katalógustulajdonos

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Katalógusok** elemre, majd nyissa meg a törölni kívánt katalógust.

1. A katalógus Áttekintés **áttekintésében kattintson** a **Törlés elemre.**

1. A megjelenő üzenetmezőben kattintson az Igen **gombra.**

### <a name="deleting-a-catalog-programmatically"></a>Katalógus programozott módon történő törlése

A katalógusokat a katalógusok használatával Microsoft Graph.  Egy megfelelő szerepkörben, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználó hívhatja meg az API-t egy `EntitlementManagement.ReadWrite.All` [accessPackageCatalog törléséhez.](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférés-irányítás delegálásának delegálása hozzáférésicsomag-kezelőknek](entitlement-management-delegate-managers.md)

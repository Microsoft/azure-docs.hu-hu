---
title: Erőforrások katalógusának létrehozása & a jogosultságok kezelése – Azure AD
description: Ismerje meg, hogyan hozhat létre új erőforrás-tárolót és hozzáférési csomagokat a Azure Active Directory jogosultságok kezelésében.
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
ms.openlocfilehash: d21783f0ef32289aad4b7d136dc8200d61fc0852
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592931"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Erőforrások katalógusának létrehozása és kezelése az Azure AD-jogosultságok kezelésében

## <a name="create-a-catalog"></a>Katalógus létrehozása

A katalógus erőforrások és hozzáférési csomagok tárolója. Létre kell hoznia egy katalógust, ha a kapcsolódó erőforrásokat és a hozzáférési csomagokat szeretné csoportosítani. Aki létrehozza a katalógust, az első katalógus tulajdonosa lesz. A katalógus tulajdonosa további katalógus-tulajdonosokat adhat hozzá.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus létrehozója

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **katalógusok** elemre.

    ![Jogosultsági felügyeleti katalógusok a Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Kattintson az **új katalógus** lehetőségre.

1. Adjon meg egy egyedi nevet a katalógus számára, és adjon meg egy leírást.

    A felhasználók ezeket az információkat egy hozzáférési csomag részleteiben látják.

1. Ha azt szeretné, hogy a katalógushoz tartozó hozzáférési csomagok elérhetők legyenek a felhasználók számára a létrehozásuk után, állítsa az **engedélyezve** beállítást **Igen** értékre.

1. Ha engedélyezni szeretné a kiválasztott külső címtárakban lévő felhasználók számára a hozzáférési csomagok kérését ebben a katalógusban, akkor a **külső felhasználók számára engedélyezze a** beállítást **Igen** értékre.

    ![Új katalógus panel](./media/entitlement-management-shared/new-catalog.png)

1. A katalógus létrehozásához kattintson a **Létrehozás** gombra.

### <a name="creating-a-catalog-programmatically"></a>Katalógus létrehozása programozott módon

Microsoft Graph használatával katalógust is létrehozhat.  Egy megfelelő szerepkörbe tartozó felhasználó, aki a delegált engedéllyel rendelkezik, `EntitlementManagement.ReadWrite.All` meghívhatja az API-t [egy accessPackageCatalog létrehozására](/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Erőforrások hozzáadása egy katalógushoz

Ahhoz, hogy egy hozzáférési csomagban erőforrásokat tartalmazzon, az erőforrásoknak léteznie kell egy katalógusban. A felvehető erőforrások típusai: csoportok, alkalmazások és SharePoint Online-webhelyek. A csoportok lehetnek felhőben létrehozott Microsoft 365 csoportok vagy felhőalapú Azure AD-alapú biztonsági csoportok. Az alkalmazások lehetnek az Azure ad vállalati alkalmazások, például az SaaS-alkalmazások és az Azure AD-ben összevont saját alkalmazások is. A webhelyek lehetnek SharePoint Online-webhelyek vagy SharePoint Online-webhelycsoportok.

**Előfeltételként szükséges szerepkör:** [Erőforrások katalógusba való felvételéhez szükséges szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyhez erőforrásokat szeretne hozzáadni.

1. A bal oldali menüben kattintson az **erőforrások** elemre.

1. Kattintson az **erőforrások hozzáadása** lehetőségre.

1. Kattintson egy erőforrás típusára: **csoportok, csapatok**, **alkalmazások** vagy **SharePoint-webhelyek**.

    Ha nem lát olyan erőforrást, amelyet hozzá szeretne adni, vagy nem tud hozzáadni egy erőforrást, győződjön meg arról, hogy rendelkezik a szükséges Azure AD-címtárbeli szerepkörrel és jogosultság-kezelési szerepkörrel. Előfordulhat, hogy a szükséges szerepkörökkel rendelkező személynek hozzá kell adnia az erőforrást a katalógushoz. További információ: a [szükséges szerepkörök az erőforrások katalógushoz való hozzáadásához](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Válasszon ki egy vagy több olyan típusú erőforrást, amelyet hozzá szeretne adni a katalógushoz.

    ![Erőforrások hozzáadása egy katalógushoz](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Ha elkészült, kattintson a **Hozzáadás** gombra.

    Ezek az erőforrások mostantól a katalógusban lévő hozzáférési csomagokba is belefoglalhatók.

### <a name="add-a-multi-geo-sharepoint-site"></a>Multi-geo SharePoint-webhely hozzáadása

1. Ha a SharePointhoz [több földrajzi](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) hely van engedélyezve, válassza ki azt a környezetet, amelyben a helyeket ki szeretné választani.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – SharePoint multi-Geo-helyek kiválasztása":::

1. Ezután válassza ki azokat a helyeket, amelyeket fel szeretne venni a katalógusba. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Erőforrás hozzáadása egy katalógushoz programozott módon

Microsoft Graph használatával is hozzáadhat erőforrást egy katalógushoz.  A megfelelő szerepkörben vagy katalógusban és erőforrás-tulajdonosban lévő felhasználó egy olyan alkalmazással, amely delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkezik, meghívhatja az API-t [egy accessPackageResourceRequest létrehozásához](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Erőforrások eltávolítása egy katalógusból

A katalógusból eltávolíthatja az erőforrásokat. Egy erőforrás csak akkor távolítható el a katalógusból, ha a katalógus egyik hozzáférési csomagja sem használja.

**Előfeltételként szükséges szerepkör:** [Erőforrások katalógusba való felvételéhez szükséges szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyből el szeretné távolítani az erőforrásokat.

1. A bal oldali menüben kattintson az **erőforrások** elemre.

1. Válassza ki az eltávolítani kívánt erőforrásokat.

1. Kattintson az **Eltávolítás** gombra (vagy kattintson a három pontra (**...**), majd az **erőforrás eltávolítása** gombra.


## <a name="add-additional-catalog-owners"></a>További katalógus-tulajdonosok hozzáadása

A katalógust létrehozó felhasználó az első katalógus tulajdonosa lesz. A katalógus felügyeletének delegálásához felhasználókat kell hozzáadnia a katalógus tulajdonosi szerepköréhez. Ez segít megosztani a katalógus-felügyeleti feladatokat. 

Az alábbi lépéseket követve rendelhet hozzá felhasználókat a katalógus tulajdonosi szerepköréhez:

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyhez hozzá szeretné adni a rendszergazdákat.

1. A bal oldali menüben kattintson a **szerepkörök és rendszergazdák** elemre.

    ![A szerepkörök és a rendszergazdák katalógusa](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kattintson a **tulajdonosok hozzáadása** lehetőségre a szerepkörök tagjainak kiválasztásához.

1. A tagok hozzáadásához kattintson a **kiválasztás** gombra.

## <a name="edit-a-catalog"></a>Katalógus szerkesztése

Szerkesztheti a katalógus nevét és leírását. A felhasználók ezt az információt egy hozzáférési csomag részleteiben látják.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg a szerkeszteni kívánt katalógust.

1. A katalógus **Áttekintés** lapján kattintson a **Szerkesztés** elemre.

1. Szerkessze a katalógus nevét, leírását vagy engedélyezett beállításait.

    ![Katalógus beállításainak szerkesztése](./media/entitlement-management-shared/catalog-edit.png)

1. Kattintson a **Mentés** gombra.

## <a name="delete-a-catalog"></a>Katalógus törlése

A katalógust törölheti, de csak akkor, ha nincs hozzáférési csomagja.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg a törölni kívánt katalógust.

1. A katalógus **áttekintésében** kattintson a **Törlés** elemre.

1. A megjelenő üzenetablakban kattintson az **Igen** gombra.

### <a name="deleting-a-catalog-programmatically"></a>Katalógus programozott törlése

A katalógust Microsoft Graph használatával is törölheti.  Egy megfelelő szerepkörbe tartozó felhasználó, aki a delegált engedéllyel rendelkezik, `EntitlementManagement.ReadWrite.All` meghívhatja az API-t [egy accessPackageCatalog törlésére](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési szabályozás delegálása a csomagkezelő-kezelők eléréséhez](entitlement-management-delegate-managers.md)
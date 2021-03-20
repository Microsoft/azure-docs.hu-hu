---
title: VÁLLALATKÖZI együttműködéssel rendelkező felhasználók felvétele információs feldolgozóként – Azure AD
description: A B2B-együttműködés lehetővé teszi, hogy az informatikai szakemberek és az alkalmazások tulajdonosai a vendég felhasználókat az Azure AD-hez hozzáférjenek. Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345cbe2efb644fe2b84d4a6021ff962337e29285
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219912"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Hogyan hívhatják meg a felhasználók a vendégeket egy alkalmazásba

Miután hozzáadta a vendég felhasználóját a címtárhoz az Azure AD-ben, az alkalmazás tulajdonosa elküldheti a vendégnek egy közvetlen hivatkozást a megosztani kívánt alkalmazáshoz. Az Azure AD-rendszergazdák az Azure AD-bérlőben is beállíthat önkiszolgáló kezelést a katalógushoz vagy SAML-alapú alkalmazásokhoz. Így az alkalmazások tulajdonosai kezelhetik a saját vendég felhasználókat, még akkor is, ha a vendég felhasználói még nem lettek hozzáadva a címtárhoz. Ha egy alkalmazás önkiszolgáló használatára van konfigurálva, az alkalmazás tulajdonosa a hozzáférési panellel hívja meg a vendég felhasználót egy alkalmazáshoz, vagy adjon hozzá egy vendég felhasználót egy olyan csoporthoz, amely hozzáféréssel rendelkezik az alkalmazáshoz. A Gallery és az SAML-alapú alkalmazások önkiszolgáló alkalmazás-felügyeletéhez szükség van egy rendszergazda kezdeti beállítására. Az alábbi összefoglalás a telepítési lépésekről (részletesebb útmutatásért lásd a lap [Előfeltételek](#prerequisites) című részét):

 - Önkiszolgáló csoportok felügyeletének engedélyezése a bérlő számára
 - Hozzon létre egy csoportot, amelyet hozzá szeretne rendelni az alkalmazáshoz, és tegye a felhasználót a tulajdonosnak
 - Az alkalmazás konfigurálása önkiszolgáló használatra és a csoport társítása az alkalmazáshoz

> [!NOTE]
> * Ez a cikk bemutatja, hogyan állíthat be önkiszolgáló felügyeletet az Azure AD-bérlőhöz hozzáadott katalógushoz és SAML-alapú alkalmazásokhoz. [Beállíthat önkiszolgáló Microsoft 365 csoportokat](../enterprise-users/groups-self-service-management.md) is, így a felhasználók kezelhetik a saját Microsoft 365 csoportjaihoz való hozzáférést. A felhasználók az Office-fájlok és-alkalmazások megoszthatók a vendég felhasználókkal, a [vendég hozzáférés Microsoft 365 csoportokban](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) és a [SharePoint-fájlok és-mappák megosztása](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c)című témakörben talál további lehetőségeket.
> * A felhasználók csak akkor hívhatják meg a vendégeket, ha a **vendég meghívói** szerepkörrel rendelkeznek.
## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Vendég felhasználó meghívása egy alkalmazásra a hozzáférési panelen

Ha egy alkalmazás önkiszolgálásra van konfigurálva, az alkalmazás tulajdonosai a saját hozzáférési paneljén meghívhatnak egy vendég felhasználót a megosztani kívánt alkalmazáshoz. A vendég felhasználót nem feltétlenül kell az Azure AD-be előre hozzáadni. 

1. Nyissa meg a hozzáférési panelt a következő lépésekkel: `https://myapps.microsoft.com` .
2. Mutasson az alkalmazásra, válassza a három pontot (**...**), majd válassza az **alkalmazás kezelése** lehetőséget.
 
   ![Képernyőfelvétel: a Salesforce alkalmazáshoz tartozó alkalmazás almenüjének kezelése](media/add-users-iw/access-panel-manage-app.png)
 
3. A felhasználók lista tetején válassza a elemet **+** .
   
   ![A tagok az alkalmazásba való felvételének plusz szimbólumát ábrázoló képernyőkép](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. A **Tagok hozzáadása** keresőmezőbe írja be a vendég felhasználó e-mail-címét. Ha szeretné, üdvözlő üzenetet is megadhat.
   
   ![A tagok hozzáadása ablakban a vendég hozzáadását bemutató képernyőkép](media/add-users-iw/access-panel-invitation.png)
   
5. Válassza a **Hozzáadás** lehetőséget, ha meghívót szeretne küldeni a vendég felhasználónak. Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Kérje meg valakinek, hogy csatlakozzon egy olyan csoporthoz, amely hozzáféréssel rendelkezik az alkalmazáshoz
Ha egy alkalmazás önkiszolgáló használatára van konfigurálva, az alkalmazások tulajdonosai meghívhatják a vendég felhasználókat az általuk felügyelt csoportoknak, amelyek hozzáférnek a megosztani kívánt alkalmazásokhoz. A vendég felhasználóknak nem kell már léteznie a címtárban. Az alkalmazás tulajdonosa ezeket a lépéseket követve meghív egy vendég felhasználót a csoportba, hogy hozzáférhessen az alkalmazáshoz.

1. Győződjön meg arról, hogy az önkiszolgáló csoport tulajdonosa, amely hozzáfér a megosztani kívánt alkalmazáshoz.
2. Nyissa meg a hozzáférési panelt a következő lépésekkel: `https://myapps.microsoft.com` .
3. Válassza ki a **csoportok** alkalmazást.
   
   ![A csoportok alkalmazást bemutató képernyőkép a hozzáférési panelen](media/add-users-iw/access-panel-groups.png)
   
4. A **saját csoportok** területen válassza ki azt a csoportot, amely hozzáféréssel rendelkezik a megosztani kívánt alkalmazáshoz.
   
   ![Képernyőfelvétel a saját csoportokban lévő csoportok kijelöléséről](media/add-users-iw/access-panel-groups-i-own.png)
   
5. A csoporttagok lista tetején válassza a elemet **+** .
   
   ![A tagok csoportba való felvételének plusz szimbólumát ábrázoló képernyőkép](media/add-users-iw/access-panel-groups-add-member.png)
   
6. A **Tagok hozzáadása** keresőmezőbe írja be a vendég felhasználó e-mail-címét. Ha szeretné, üdvözlő üzenetet is megadhat.
   
   ![A tagok hozzáadása ablakban a vendég hozzáadását bemutató képernyőkép](media/add-users-iw/access-panel-invitation.png)
   
7. A **Hozzáadás** gombra kattintva automatikusan elküldheti a meghívót a vendég felhasználónak. Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.


## <a name="prerequisites"></a>Előfeltételek

Az önkiszolgáló alkalmazások felügyeletéhez a globális rendszergazda és az Azure AD-rendszergazda által igényelt kezdeti beállítás szükséges. A telepítő részeként konfigurálja az alkalmazást az önkiszolgáló szolgáltatáshoz, és hozzárendel egy csoportot az alkalmazáshoz, amelyet az alkalmazás tulajdonosa kezelhet. A csoportot úgy is beállíthatja, hogy bárki megkérje a tagságot, de a csoport tulajdonosának jóváhagyása szükséges. (További információ az [önkiszolgáló csoport kezeléséről](../enterprise-users/groups-self-service-management.md).) 

> [!NOTE]
> Nem vehet fel vendég felhasználókat dinamikus csoportba vagy olyan csoportba, amely szinkronizálva van a helyszíni Active Directoryával.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Önkiszolgáló csoportok felügyeletének engedélyezése a bérlő számára
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként.
2. A navigációs panelen válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **csoportok** lehetőséget.
4. A **Beállítások** területen válassza az **általános** lehetőséget.
5. Az **önkiszolgáló csoport felügyelete** alatt **a tulajdonosok lehetőség mellett a hozzáférési panelen is kezelhet csoporttagság-kérelmeket**, és válassza az **Igen** lehetőséget.
6. Kattintson a **Mentés** gombra.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Hozzon létre egy csoportot, amelyet hozzá szeretne rendelni az alkalmazáshoz, és tegye a felhasználót a tulajdonosnak
1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként vagy globális rendszergazdaként.
2. A navigációs panelen válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **csoportok** lehetőséget.
4. Válassza az **új csoport** lehetőséget.
5. A **csoport típusa** területen válassza a **Biztonság** elemet.
6. Gépelje be a **Csoport nevét** és a **Csoport leírását**.
7. A **tagság típusa** területen válassza a **hozzárendelt** elemet.
8. Válassza a **Létrehozás** lehetőséget, majd a **csoport** lap bezárásához.
9. A **csoportok – minden csoport** lapon nyissa meg a csoportot. 
10. A **kezelés** területen válassza a **tulajdonosok**  >  **tulajdonos hozzáadása** elemet. Keresse meg azt a felhasználót, akinek az alkalmazáshoz való hozzáférést kezelnie kell. Válassza ki a felhasználót, majd kattintson a **kiválasztás** elemre.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Az alkalmazás konfigurálása önkiszolgáló használatra és a csoport társítása az alkalmazáshoz
1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként vagy globális rendszergazdaként.
2. A navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
3. A **kezelés** területen válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
4. Az alkalmazás listában keresse meg és nyissa meg az alkalmazást.
5. A **kezelés** területen válassza az **egyszeri bejelentkezés** lehetőséget, és konfigurálja az alkalmazást az egyszeri bejelentkezéshez. (Részletekért lásd: [egyszeri bejelentkezés kezelése vállalati alkalmazásokhoz](../manage-apps/add-application-portal-setup-sso.md).)
6. A **kezelés** területen válassza az **önkiszolgáló** lehetőséget, és állítsa be az önkiszolgáló alkalmazás-hozzáférést. (Részletekért lásd: az önkiszolgáló [alkalmazás-hozzáférés használata](../manage-apps/access-panel-manage-self-service-access.md).) 

    > [!NOTE]
    > Ahhoz a beállításhoz, **amelyhez hozzá** kívánja adni a felhasználókat, adja hozzá az előző szakaszban létrehozott csoportot.
7. A **kezelés** területen válassza a **felhasználók és csoportok** lehetőséget, majd ellenőrizze, hogy az Ön által létrehozott önkiszolgáló csoport megjelenik-e a listában.
8. Ha hozzá szeretné adni az alkalmazást a csoport tulajdonosának hozzáférési paneljéhez, válassza a **felhasználói**  >  **felhasználók és csoportok** hozzáadása lehetőséget. Keresse meg a csoport tulajdonosát, és válassza ki a felhasználót, kattintson a **kiválasztás** elemre, majd kattintson a **hozzárendelés** gombra a felhasználó az alkalmazáshoz való hozzáadásához.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Hogyan vehetik Azure Active Directory a rendszergazdák a B2B együttműködési felhasználókat?](add-users-administrator.md)
- [VÁLLALATKÖZI együttműködés meghívásának beváltása](redemption-experience.md)
- [Az External Identities díjszabása](external-identities-pricing.md)
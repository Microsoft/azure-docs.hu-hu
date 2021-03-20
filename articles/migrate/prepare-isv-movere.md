---
title: Azure Migrate előkészítése ISV-eszközzel vagy-mozgatóval való együttműködéshez
description: Ez a cikk azt ismerteti, hogyan lehet előkészíteni a Azure Migratet egy ISV-eszközzel vagy-mozgatóval való együttműködésre, majd az eszköz használatának megkezdésére.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753773"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Felkészülés az ISV-eszköz vagy a mozgató tevékenységre

Ha [ISV-eszközt](migrate-services-overview.md#isv-integration) adott hozzá, vagy egy Azure Migrate projektre mutat, néhány lépést követnie kell, mielőtt összekapcsolja az eszközt, és nem küldi el az adatAzure Migratenak. 

## <a name="check-azure-ad-permissions"></a>Azure AD-engedélyek keresése

Az Azure-beli felhasználói fióknak a következő engedélyekkel kell rendelkeznie:

- Engedély Azure Active Directory (Azure AD) alkalmazás Azure-Bérlővel való regisztrálásához
- Jogosultság a szerepkör az Azure AD-alkalmazáshoz való kiosztására az előfizetés szintjén


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Engedélyek beállítása Azure AD-alkalmazás regisztrálásához

1. Az Azure AD-ben keresse meg a fiókja szerepkört.
2. Ha rendelkezik a felhasználói szerepkörrel, válassza a bal oldali **felhasználói beállítások** elemet, és ellenőrizze, hogy a felhasználók regisztrálhatnak-e alkalmazásokat. Ha az **Igen** értékre van állítva, akkor az Azure ad-bérlő bármely felhasználója regisztrálhat egy alkalmazást. Ha a **nem** értékre van állítva, akkor csak a rendszergazda felhasználók regisztrálhatják az alkalmazásokat.   
3. Ha nincs engedélye, a rendszergazda felhasználó megadhatja a felhasználói fiókját az [alkalmazás-rendszergazdai](../active-directory/roles/permissions-reference.md#application-administrator) szerepkörrel, így regisztrálhatja az alkalmazást.
4. Az eszköz Azure Migratehoz való csatolása után a rendszergazda el tudja távolítani a szerepkört a fiókból.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Engedélyek beállítása egy szerepkör hozzárendeléséhez egy Azure AD-alkalmazáshoz
 
Az Azure-előfizetésében a fióknak **Microsoft. Authorization/*/Write** hozzáférésre van szüksége ahhoz, hogy szerepkört rendeljen egy Azure ad-alkalmazáshoz. 

1. Az Azure Portalon nyissa meg az **Előfizetések** oldalt.
2. Válassza ki az adott előfizetést. Ha nem látja, válassza a **globális előfizetések szűrőt**. 
3. Válassza **a saját engedélyek** lehetőséget. Ezután **kattintson ide az előfizetés teljes hozzáférés részleteinek megtekintéséhez**.
4. A **szerepkör-hozzárendelések**  >  **nézetben** tekintse át az engedélyeket. Ha a fiókja nem rendelkezik jogosultságokkal, kérje meg az előfizetés rendszergazdáját, hogy vegye fel a [felhasználót a felhasználói hozzáférés rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkörbe vagy a [tulajdonos](../role-based-access-control/built-in-roles.md#owner) szerepkörbe.

## <a name="allow-access-to-urls"></a>URL-címek elérésének engedélyezése

Az ISV-eszközök és az Azure Database Migration Assistant esetében engedélyezze a hozzáférést a táblázatban összefoglalt nyilvános Felhőbeli URL-címekhez. Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, győződjön meg arról, hogy a proxy feloldja az URL-címek keresése során fogadott CNAME-rekordokat. 

**URL-cím** | **Részletek**
--- | ---
*.portal.azure.com  | Lépjen az Azure Portalra. 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *. live.com   | Jelentkezzen be az Azure-előfizetésbe. 
*.microsoftonline.com<br/> *.microsoftonline-p.com | Hozzon létre Azure Active Directory-(AD-) alkalmazásokat a berendezés számára a Azure Migratesal való kommunikációhoz. 
management.azure.com | Azure Resource Manager hívásokat kezdeményez a Azure Migrate projekthez.
*.servicebus.windows.net | Kommunikáció a készülék és a EventHub között az üzenetek küldéséhez.


## <a name="start-using-the-tool"></a>Az eszköz használatának megkezdése

1. Ha még nem rendelkezik licenccel vagy ingyenes próbaverzióval az eszközhöz, a Azure Migrate eszköz bejegyzésében, a **regisztráció** területen válassza a **továbbiak** lehetőséget.
2. Az eszközben kövesse az utasításokat az eszközről a Azure Migrate projekthez való hivatkozáshoz, és az adatok Azure Migrateba való küldéséhez.

## <a name="next-steps"></a>Következő lépések

Kövesse az ISV vagy a mozgató utasításait az adatok Azure Migrate való elküldéséhez.

   

---
title: Gyors útmutató – hozzáférés & új bérlő létrehozása – Azure AD
description: Útmutatás a Azure Active Directory megkereséséhez és új bérlő létrehozásához a szervezet számára.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeea88d8c21ba754fbeadbb24891126b639616c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437240"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Gyors útmutató: új bérlő létrehozása Azure Active Directory
Az Azure Active Directory (Azure AD) portált használva elvégezheti valamennyi felügyeleti feladatát, a szervezet számára új bérlő létrehozását is beleértve. 

Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure portált és az Azure Active Directory-t, és megtudhatja, hogyan hozhat létre alapszintű bérlőt a szervezete számára.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-new-tenant-for-your-organization"></a>Új bérlő létrehozása a szervezetén belül
Miután bejelentkezett az Azure portálra, létrehozhat egy új bérlőt a szervezet számára. Az új bérlő a szervezetét képviseli és segíti a Microsoft felhőszolgáltatások konkrét példányának kezelését a belső és külső felhasználók számára.

### <a name="to-create-a-new-tenant"></a>Új bérlő létrehozása

1. Jelentkezzen be a szervezet [Azure Portalba](https://portal.azure.com/).

1. A Azure Portal menüben válassza a **Azure Active Directory** lehetőséget.  

    <kbd>![Azure Active Directory – Áttekintés lap – bérlő létrehozása](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Válassza **a bérlő létrehozása** lehetőséget.

1. Az alapvető beállítások lapon válassza ki a létrehozni kívánt bérlő típusát **Azure Active Directory** vagy **Azure Active Directory (B2C)**.

1. Válassza a **Tovább: konfigurálás** elemet a konfiguráció lapra való áttéréshez.

    <kbd>![Azure Active Directory – bérlői lap létrehozása – konfiguráció lap ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  A konfiguráció lapon adja meg a következő adatokat:
    
    - Írja be a _contoso-szervezet_ **nevet a szervezet neve** mezőbe.

    - Írja  be a Contosoorg **nevet a kezdeti tartománynév** mezőbe.

    - Hagyja meg az _Egyesült Államok_ beállítást az **Ország vagy régió** mezőben.

1. Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet. Tekintse át a megadott adatokat, és ha az adatok helyesek, válassza a **Létrehozás** lehetőséget.

    <kbd>![Azure Active Directory – bérlői oldal áttekintése és létrehozása](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Az új bérlő létrejön a contoso.onmicrosoft.com tartománnyal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem folytatja az alkalmazás használatát, a következő lépésekkel törölheti a bérlőt:

- Győződjön meg arról, hogy be van jelentkezve a törölni kívánt könyvtárba a Azure Portal **Directory + előfizetés** szűrője segítségével, és szükség esetén váltson a célhelyre.
- Válassza ki **Azure Active Directoryt**, majd a **Contoso – áttekintés** lapon a **Címtár törlése** lehetőséget.

    A bérlő és a hozzá kapcsolódó információk törlődnek.

    <kbd>![Áttekintő lap, a kijelölt könyvtár törlése gombbal](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Következő lépések
- Tartománynevek módosítása és továbbiak felvétele: [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](add-custom-domain.md)

- Felhasználók felvétele: [Új felhasználó hozzáadása és törlése](add-users-azure-active-directory.md)

- Csoportok és tagok hozzáadása: [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- Tudnivalók a [szerepköralapú hozzáférésről Privileged Identity Management](../../role-based-access-control/best-practices.md) és [feltételes hozzáférés](../../role-based-access-control/conditional-access-azure-management.md) használatával a szervezet alkalmazás-és erőforrás-hozzáférésének kezeléséhez.

- Tudnivalók az Azure Active Directoryról, beleértve [az alapszintű licenceléssel, a terminológiával és a társított szolgáltatásokkal](active-directory-whatis.md) kapcsolatos információkat.

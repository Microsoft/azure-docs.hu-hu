---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/05/2021
ms.author: mimart
ms.openlocfilehash: 5a2382146cd8b85b8eef54b924a206dda7107b0f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382460"
---
#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/) 

1. A **kezelés** területen válassza az **API-engedélyek** lehetőséget.
1. A **konfigurált engedélyek** területen válassza **az engedély hozzáadása** elemet.
1. Válassza a **Microsoft API** -k fület, majd válassza a **Microsoft Graph** lehetőséget.
1. Válassza ki az **alkalmazás engedélyeit**.
1. Bontsa ki a megfelelő jogosultságokat tartalmazó csoportot, és jelölje be a felügyeleti alkalmazásnak nyújtandó engedély jelölőnégyzetét. Például:
    * **Felhasználó**  >  **User. ReadWrite. All**: felhasználói áttelepítési vagy felhasználói kezelési forgatókönyvek esetén.
    * **Csoport**  >  **Group. ReadWrite. All**: csoportok létrehozásához, csoporttagság olvasásához és frissítéséhez, valamint csoportok törléséhez.
    * **AuditLog**  >  **AuditLog. Read. All**: a könyvtár naplóinak olvasására.
    * **Házirend**  >  **Policy. ReadWrite. TrustFramework**: folyamatos integrációs/folyamatos kézbesítési (CI/CD) forgatókönyvek esetén. Például az egyéni házirend üzembe helyezése az Azure-folyamatokkal.
1. Válassza az **engedélyek hozzáadása** lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblép a következő lépésre.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Ha jelenleg nincs bejelentkezve globális rendszergazdai fiókkal, jelentkezzen be egy olyan fiókkal a Azure AD B2C-bérlőben, amely legalább a *Cloud Application Administrator* szerepkörhöz van rendelve, majd válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Válassza a **frissítés** lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." az **állapot** területen jelenik meg. Eltarthat néhány percig, amíg az engedélyek propagálása megtörténik.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. A **regisztrált alkalmazás** áttekintése lapon válassza a **Beállítások** lehetőséget.
1. Az **API-hozzáférés** területen válassza a **szükséges engedélyek** lehetőséget.
1. Válassza a **Microsoft Graph** lehetőséget.
1. Az **alkalmazás engedélyei** területen jelölje be a felügyeleti alkalmazásnak nyújtandó engedély jelölőnégyzetét. Például:
    * Az **összes naplózási napló információjának olvasása**: válassza ezt az engedélyt a könyvtár naplóinak olvasásához.
    * **Címtáradatok olvasása és írása**: válassza ezt az engedélyt a felhasználói áttelepítéshez vagy a felhasználói felügyeleti forgatókönyvekhez.
    * **A szervezeti megbízhatósági keretrendszer házirendjeinek olvasása és írása**: válassza ezt az engedélyt a folyamatos integráció/folyamatos teljesítés (CI/CD) forgatókönyvekhez. Például az egyéni házirend üzembe helyezése az Azure-folyamatokkal.
1. Kattintson a **Mentés** gombra.
1. Válassza az **engedélyek megadása** lehetőséget, majd válassza az **Igen** lehetőséget. Az engedélyek teljes propagálása eltarthat néhány percig.

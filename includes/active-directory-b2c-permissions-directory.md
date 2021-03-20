---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: f8c972bdb9195008c2983d3993e8d9369749b284
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85200183"
---
#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/) 

1. A **kezelés** területen válassza az **API-engedélyek** lehetőséget.
1. A **konfigurált engedélyek** területen válassza **az engedély hozzáadása** elemet.
1. Válassza a **Microsoft API** -k fület, majd válassza a **Microsoft Graph** lehetőséget.
1. Válassza ki az **alkalmazás engedélyeit**.
1. Bontsa ki a megfelelő jogosultságokat tartalmazó csoportot, és jelölje be a felügyeleti alkalmazásnak nyújtandó engedély jelölőnégyzetét. Például:
    * **AuditLog**  >  **AuditLog. Read. All**: a könyvtár naplóinak olvasására.
    * **Címtár**  >  **Directory. ReadWrite. All**: felhasználói áttelepítési vagy felhasználói kezelési forgatókönyvek esetén.
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

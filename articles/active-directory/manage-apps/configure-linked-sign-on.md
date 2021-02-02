---
title: A Azure Active Directory csatolt bejelentkezésének megismerése
description: A Azure Active Directory csatolt bejelentkezésének megismerése.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3e2d3ab6a23ce588c3aa393e5096ac75e88a5365
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255283"
---
# <a name="understand-linked-sign-on"></a>A csatolt bejelentkezés ismertetése

Az alkalmazások felügyeletének rövid útmutató [sorozatában](view-applications-portal.md) megtudhatta, hogyan használhatja az Azure ad-t az alkalmazás identitás-szolgáltatója (identitásszolgáltató) használatával. A rövid útmutatóban SAML-alapú vagy OIDC-alapú egyszeri bejelentkezést konfigurálhat. Egy másik lehetőség van **csatolva**. Ez a cikk részletesebben ismerteti a csatolt kapcsolót.

A **csatolt** beállítás lehetővé teszi a célhely konfigurálását, amikor a felhasználó kiválasztja az alkalmazást a szervezet [saját alkalmazások](https://myapps.microsoft.com/) vagy Office 365 portálon.

Néhány gyakori forgatókönyv, ahol a hivatkozás lehetőség értékes:
- Vegyen fel egy hivatkozást egy olyan egyéni webalkalmazásra, amely jelenleg összevonást használ, például Active Directory összevonási szolgáltatások (AD FS) (AD FS).
- Hozzáadhat részletes hivatkozásokat bizonyos SharePoint-lapokhoz vagy más weblapokhoz, amelyeket csak szeretne megjeleníteni a felhasználó hozzáférési panelén.
- Olyan alkalmazásra mutató hivatkozás hozzáadása, amely nem igényel hitelesítést. 
 
 A **csatolt** lehetőség nem biztosít bejelentkezési funkciókat az Azure ad hitelesítő adataival. Azonban továbbra is használhatja a **vállalati alkalmazások** egyéb funkcióit. Használhatja például a naplókat, és hozzáadhat egy egyéni emblémát és egy alkalmazás nevét.

## <a name="before-you-begin"></a>Előkészületek

Az ismeretek gyors bejárásához járjon végig az alkalmazás-felügyeleti útmutató [sorozatán](view-applications-portal.md) . A rövid útmutatóban, ahol az egyszeri bejelentkezést konfigurálja, megtalálhatja a **csatolt** lehetőséget is. 

A **csatolt** lehetőség nem nyújt bejelentkezési funkciókat az Azure ad-n keresztül. A beállítás egyszerűen beállítja, hogy a rendszer a felhasználók számára küldje el az [alkalmazást az alkalmazásokban vagy a](https://myapps.microsoft.com/) Microsoft 365 app launcherben.  Mivel a bejelentkezés nem biztosít bejelentkezési funkciókat az Azure AD-n keresztül, a feltételes hozzáférés nem érhető el a csatolt egyszeri bejelentkezéssel konfigurált alkalmazásokhoz.

> [!IMPORTANT] 
> Vannak olyan helyzetek, amikor az **egyszeri bejelentkezési** lehetőség nem lesz a **vállalati alkalmazásokban** lévő alkalmazás navigációjában. 
>
> Ha az alkalmazás a **Alkalmazásregisztrációk** használatával lett regisztrálva, akkor az egyszeri bejelentkezés funkció alapértelmezés szerint a OIDC OAuth használatára van beállítva. Ebben az esetben az **egyszeri bejelentkezési** lehetőség nem jelenik meg a **vállalati alkalmazások** navigációs sávján. Ha a **Alkalmazásregisztrációk** használatával adja hozzá az egyéni alkalmazást, a jegyzékfájlban konfigurálhatja a beállításokat. További információ a jegyzékfájlról: [Azure Active Directory app manifest](../develop/reference-app-manifest.md). Az SSO-szabványokkal kapcsolatos további tudnivalókért tekintse meg a [hitelesítés és engedélyezés a Microsoft Identity platform használatával](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)című témakört. 
>
> Egyéb forgatókönyvek, amelyekben az **egyszeri bejelentkezés** hiányzik a navigálásból, ha egy alkalmazás egy másik bérlőn fut, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás-rendszergazda vagy az egyszerű szolgáltatásnév tulajdonosa). Az engedélyek olyan eseteket is okozhatnak, ahol megnyithatja az **egyszeri bejelentkezést** , de nem fogja tudni menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Hivatkozás konfigurálása

Egy alkalmazás hivatkozásának megadásához válassza az **egyszeri bejelentkezés** lapon a **csatolt** lehetőséget. Ezután adja meg a hivatkozást, és kattintson a **Mentés** gombra. Szüksége van egy emlékeztetőre, ahol megtalálja ezeket a beállításokat? Tekintse meg a gyors üzembe helyezési [sorozatot](view-applications-portal.md).
 
Miután konfigurált egy alkalmazást, rendeljen hozzá felhasználókat és csoportokat. A felhasználók hozzárendelésével szabályozhatja, hogy mikor jelenik meg az alkalmazás a [saját alkalmazások](https://myapps.microsoft.com/) vagy a Microsoft 365 app Launcher használatával.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók vagy csoportok társítása az alkalmazáshoz](./assign-user-or-group-access-portal.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)

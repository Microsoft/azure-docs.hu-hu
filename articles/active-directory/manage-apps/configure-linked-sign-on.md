---
title: A csatolt bejelentkezés Azure Active Directory
description: Az összekapcsolt bejelentkezések Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 6ed6f6b69326157573ea043457dbc8d8a3079146
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374574"
---
# <a name="understand-linked-sign-on"></a>A csatolt bejelentkezések

Az [alkalmazáskezelés](view-applications-portal.md) gyorsútmutató-sorozatában megtanulta, hogyan használhatja az Azure AD-t identitásszolgáltatóként (IdP) egy alkalmazáshoz. A rövid útmutatóban SAML-alapú vagy OIDC-alapú SSO-t konfigurál. Egy másik lehetőség a **Csatolt.** Ez a cikk a hivatkozott lehetőséggel kapcsolatos további részleteket ismerteti.

A **Csatolt** beállítással konfigurálhatja a célhelyet, amikor egy [](https://myapps.microsoft.com/) felhasználó kiválasztja az alkalmazást a Saját alkalmazások Office 365 portálján.

Néhány gyakori forgatókönyv, ahol a hivatkozási lehetőség hasznos:
- Adjon hozzá egy olyan egyéni webalkalmazásra mutató hivatkozást, amely jelenleg összevonást használ, például Active Directory összevonási szolgáltatások (AD FS) (AD FS).
- Adjon hozzá mélyhivatkozásokat adott SharePoint-oldalakhoz vagy más weboldalakhoz, amelyek csak a felhasználói hozzáférési paneleken jelennek meg.
- Adjon hozzá egy olyan alkalmazásra mutató hivatkozást, amely nem igényel hitelesítést. 
 
 A **Csatolt** beállítás nem biztosít bejelentkezési funkciót az Azure AD hitelesítő adataival. A vállalati alkalmazások egyéb funkcióit azonban továbbra **is használhatja.** Használhat például auditnaplókat, és hozzáadhat egy egyéni emblémát és alkalmazásnevet.

## <a name="before-you-begin"></a>Előkészületek

A tudás gyors kigyomlálása érdekében végig kell mennie az [alkalmazáskezelés](view-applications-portal.md) gyorsútmutató-sorozatán. A rövid útmutatóban, ahol az egyszeri bejelentkezést konfigurálja, a Csatolt lehetőséget **is megtalálja.** 

A **Csatolt** beállítás nem biztosít bejelentkezési funkciókat az Azure AD-on keresztül. A beállítás egyszerűen beállítja azt a helyet, a [](https://myapps.microsoft.com/) helyre, a rendszer a felhasználók számára, amikor kiválasztják az alkalmazást Saját alkalmazások vagy az Microsoft 365 appindítóban.  Mivel a bejelentkezés nem biztosít bejelentkezési funkciót az Azure AD-val, a feltételes hozzáférés nem érhető el a csatolt egyszeri bejelentkezéssel konfigurált alkalmazásokhoz.

> [!IMPORTANT] 
> Vannak olyan forgatókönyvek,  amelyekben az Egyszeri bejelentkezés lehetőség nem lesz az alkalmazás navigációs sávján a **vállalati alkalmazásokban.** 
>
> Ha az alkalmazást a **Alkalmazásregisztrációk,** akkor az egyszeri bejelentkezés funkció alapértelmezés szerint az OIDC OAuth használatára van beállítva. Ebben az esetben az **Egyszeri bejelentkezés** lehetőség nem fog mutatni a Vállalati alkalmazások alatti **navigációs sávban.** Amikor a **Alkalmazásregisztrációk** egyéni alkalmazást ad hozzá, a jegyzékfájlban konfigurálja a beállításokat. A jegyzékfájlról további információt az alkalmazásjegyzék Azure Active Directory [olvashat.](../develop/reference-app-manifest.md) Az SSO-szabványokkal kapcsolatos további információkért lásd: Hitelesítés és engedélyezés [a Microsoft identitásplatformján.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Egyéb forgatókönyvek,  amelyekben az egyszeri bejelentkezés hiányzik a navigációs menüből, például ha egy alkalmazást egy másik bérlő üzemeltet, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa). Az engedélyek olyan forgatókönyvet is okozhatnak, amelyben meg lehet nyitni az egyszeri **bejelentkezést,** de nem lehet menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Hivatkozás konfigurálása

Egy alkalmazás hivatkozásának beállításhoz válassza a **Csatolt lehetőséget** az Egyszeri **bejelentkezés oldalon.** Ezután írja be a hivatkozást, és válassza a **Mentés lehetőséget.** Emlékeztetőre van szüksége arról, hogy hol találhatja meg ezeket a lehetőségeket? Tekintse meg a [gyorsútmutató-sorozatot.](view-applications-portal.md)
 
Miután konfigurált egy alkalmazást, rendeljen hozzá felhasználókat és csoportokat. A felhasználók hozzárendelésekor szabályozhatja, hogy mikor jelenjen meg az alkalmazás Saját alkalmazások [vagy](https://myapps.microsoft.com/) a Microsoft 365 alkalmazásindítójában.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz](./assign-user-or-group-access-portal.md)
- [Felhasználói fiókok automatikus üzembe építésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)

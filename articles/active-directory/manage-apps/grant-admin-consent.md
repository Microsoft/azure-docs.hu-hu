---
title: Bérlői szintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz – Azure AD
description: Megtudhatja, hogyan adhat meg bérlői szintű jóváhagyást egy alkalmazáshoz, hogy a végfelhasználók ne kérjenek jóváhagyást egy alkalmazásba való bejelentkezéskor.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 646c2216c3d71aa441d33dde0ab3e2ef7bb4fd89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643558"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz

  Megtudhatja, hogyan biztosíthat bérlői szintű rendszergazdai jóváhagyást egy alkalmazáshoz. Ez a cikk a megvalósításának különböző módjait ismerteti.

További információ az alkalmazásokkal való hozzájárulásról: [Azure Active Directory beleegyezési keretrendszer](../develop/consent-framework.md).

## <a name="prerequisites"></a>Előfeltételek

A bérlői szintű rendszergazdai jóváhagyás megadása megköveteli, hogy olyan felhasználóként jelentkezzen be, amely engedéllyel rendelkezik a szervezet nevében való jóváhagyásra. Ebbe beletartozik a [globális rendszergazda](../roles/permissions-reference.md#global-administrator) és a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator), valamint egyes alkalmazások, az [alkalmazás-rendszergazda](../roles/permissions-reference.md#application-administrator) és a [felhőalapú alkalmazás rendszergazdája](../roles/permissions-reference.md#cloud-application-administrator). A felhasználó jogosult arra is, hogy a bérlői szintű hozzájárulást biztosítson, ha olyan [Egyéni címtár-szerepkört](../roles/custom-create.md) rendel hozzá, amely magában foglalja az [alkalmazások engedélyeinek megadására vonatkozó engedélyt](../roles/custom-consent-permissions.md).

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás megadása egy alkalmazás számára megadja az alkalmazásnak és az alkalmazás közzétevője számára a szervezet adataihoz való hozzáférést. A jóváhagyás megadása előtt körültekintően tekintse át az alkalmazás által kért engedélyeket.

> [!IMPORTANT]
> Ha egy alkalmazás teljes körű rendszergazdai jogosultsággal rendelkezik, az összes felhasználó be tud jelentkezni az alkalmazásba, kivéve, ha úgy van konfigurálva, hogy felhasználói hozzárendelést igényeljen. Ha szeretné korlátozni, hogy mely felhasználók jelentkezhetnek be egy alkalmazásba, megkövetelheti a felhasználói hozzárendelést, majd felhasználókat vagy csoportokat rendelhet hozzá az alkalmazáshoz. További információ: [felhasználók és csoportok hozzárendelésének módszerei](./assign-user-or-group-access-portal.md).

## <a name="grant-admin-consent-from-the-azure-portal"></a>Rendszergazdai jóváhagyás megadása a Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Rendszergazdai jóváhagyás megadása a vállalati alkalmazásokban

Ha az alkalmazás már üzembe lett helyezve a bérlőben, a bérlői szintű rendszergazdai jóváhagyást is megadhatja a *vállalati alkalmazásokon* keresztül. Egy alkalmazás például kiépíthető a bérlőben, ha legalább egy felhasználó már beleegyezett az alkalmazásba. További információ: [how és Why alkalmazások hozzáadása a Azure Active Directoryhoz](../develop/active-directory-how-applications-are-added.md).

A bérlői szintű rendszergazdai jóváhagyás biztosítása a **vállalati alkalmazásokban** felsorolt alkalmazásokhoz:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../roles/permissions-reference.md#global-administrator), alkalmazás- [rendszergazdaként](../roles/permissions-reference.md#application-administrator)vagy [Felhőbeli alkalmazás-rendszergazdaként](../roles/permissions-reference.md#cloud-application-administrator).
2. Válassza **Azure Active Directory** majd a **vállalati alkalmazások** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez bérlői szintű rendszergazdai jogosultságot szeretne adni.
4. Válassza az **engedélyek** lehetőséget, majd kattintson a **rendszergazdai jóváhagyás megadása** lehetőségre.
5. Körültekintően tekintse át az alkalmazás által igényelt engedélyeket.
6. Ha elfogadja az alkalmazás által igényelt engedélyeket, engedélyezze a jóváhagyást. Ha nem, kattintson a **Mégse** gombra, vagy az ablak bezárásához.

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás a **nagyvállalati alkalmazásokon** keresztül való megadásával visszavon minden olyan engedélyt, amely korábban már megkapta a bérlői szintű engedélyeket. A felhasználók által korábban a saját nevében megadott engedélyeket a rendszer nem érinti. 

### <a name="grant-admin-consent-in-app-registrations"></a>Rendszergazdai jóváhagyás megadása Alkalmazásregisztrációk

A szervezet által fejlesztett, vagy közvetlenül az Azure AD-bérlőben regisztrált alkalmazások esetében a bérlői szintű rendszergazdai jóváhagyást is megadhatja **Alkalmazásregisztrációk** Azure Portal.

A bérlői szintű rendszergazdai jóváhagyás engedélyezése **Alkalmazásregisztrációktól**:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../roles/permissions-reference.md#global-administrator), alkalmazás- [rendszergazdaként](../roles/permissions-reference.md#application-administrator)vagy [Felhőbeli alkalmazás-rendszergazdaként](../roles/permissions-reference.md#cloud-application-administrator).
2. Válassza a **Azure Active Directory** majd **Alkalmazásregisztrációk** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez bérlői szintű rendszergazdai jogosultságot szeretne adni.
4. Válassza az **API-engedélyek** lehetőséget, majd kattintson a **rendszergazdai jóváhagyás megadása** lehetőségre.
5. Körültekintően tekintse át az alkalmazás által igényelt engedélyeket.
6. Ha elfogadja az alkalmazás által igényelt engedélyeket, engedélyezze a jóváhagyást. Ha nem, kattintson a **Mégse** gombra, vagy az ablak bezárásához.

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás **Alkalmazásregisztrációkon** keresztül történő megadása visszavonja azokat az engedélyeket, amelyek korábban megadták a bérlői szintű jogosultságokat. A felhasználók által korábban a saját nevében megadott engedélyeket a rendszer nem érinti. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Az URL-cím létrehozása a bérlői szintű rendszergazdai jóváhagyás biztosításához

Amikor a fent ismertetett módon megadta a bérlői szintű rendszergazdai jóváhagyást, egy ablak nyílik meg a Azure Portalból, amely a bérlői szintű rendszergazdai hozzájárulás megadására kéri. Ha ismeri az alkalmazás ügyfél-AZONOSÍTÓját (más néven az alkalmazás AZONOSÍTÓját), ugyanazt az URL-címet is létrehozhatja a bérlői szintű rendszergazdai jóváhagyás biztosításához.

A bérlői szintű rendszergazdai beleegyező URL-cím a következő formátumot követi:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

ahol:

* `{client-id}` az alkalmazás ügyfél-azonosítója (más néven alkalmazás-azonosító).
* `{tenant-id}` a szervezete bérlői azonosítója vagy bármely ellenőrzött tartománynév.

Ahogy mindig, körültekintően tekintse át az alkalmazások kérelmének engedélyeit a jóváhagyás megadása előtt.

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás ezen az URL-címen keresztül történő megadása visszavonja azokat az engedélyeket, amelyek korábban már megadták a bérlői szintű jogosultságokat. A felhasználók által korábban a saját nevében megadott engedélyeket a rendszer nem érinti. 

## <a name="next-steps"></a>Következő lépések

[A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása](configure-user-consent.md)

[Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)

[Engedélyek és beleegyezett a Microsoft Identity platform](../develop/v2-permissions-and-consent.md)

[Azure AD a Microsoft Q&A](/answers/topics/azure-active-directory.html)

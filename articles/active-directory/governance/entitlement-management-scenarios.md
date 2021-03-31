---
title: A jogosultságok kezelésének gyakori forgatókönyvei – Azure AD
description: Ismerkedjen meg a Azure Active Directory jogosultságok kezelésének gyakori eseteivel kapcsolatos magas szintű lépésekkel.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f45a1481661aa350815560d795ab7411f99545
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317843"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Gyakori forgatókönyvek az Azure AD-jogosultságok kezelésében

Több módon is konfigurálhatja a jogosultságok kezelését a szervezet számára. Ha azonban csak most kezdi meg az első lépéseket, érdemes megismerni a rendszergazdák, a katalógus-tulajdonosok, a hozzáférési csomag-kezelők, a jóváhagyók és a kérelmezők általános forgatókönyveit.

## <a name="delegate"></a>Delegált

### <a name="administrator-delegate-management-of-resources"></a>Rendszergazda: erőforrások kezelésének delegálása

1. [Videó megtekintése: delegálás onnan a Department Managerbe](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Felhasználók delegálása a katalógus létrehozói szerepkörbe](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Katalógus létrehozója: erőforrások kezelésének delegálása

- [Új katalógus létrehozása](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalógus tulajdonosa: erőforrások kezelésének delegálása

1. [Közös tulajdonosok hozzáadása a katalógushoz](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Erőforrások hozzáadása a katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalógus tulajdonosa: hozzáférési csomagok delegálása

1. [Videó megtekintése: delegálás a katalógus tulajdonosától a Package Manager eléréséhez](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Felhasználók delegálása a Package Manager szerepkör eléréséhez](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>A szervezeten belüli felhasználók hozzáférésének szabályozása

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Hozzáférés a Package Managerhez: a szervezet alkalmazottai hozzáférhetnek az erőforrásokhoz való hozzáféréshez

1. [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md#start-new-access-package)
1. [Csoportok, csoportok, alkalmazások vagy SharePoint-helyek hozzáadása a csomag eléréséhez](entitlement-management-access-package-create.md#resource-roles)
1. [Kérési házirend hozzáadása, amely lehetővé teszi a címtárban lévő felhasználók számára a hozzáférés kérését](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Lejárati beállítások megadása](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Kérelmező: hozzáférés kérése az erőforrásokhoz

1. [Bejelentkezés a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Hozzáférési csomag keresése
1. [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Jóváhagyó: kérelmek jóváhagyása az erőforrásokhoz

1. [Kérelem megnyitása a saját hozzáférési portálon](entitlement-management-request-approve.md#open-request)
1. [Hozzáférési kérelem jóváhagyása vagy megtagadása](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Kérelmező: megtekintheti azokat az erőforrásokat, amelyeknek már van hozzáférése

1. [Bejelentkezés a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Aktív hozzáférési csomagok megtekintése

## <a name="govern-access-for-users-outside-your-organization"></a>A szervezeten kívüli felhasználók hozzáférésének szabályozása

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Rendszergazda: együttműködés külső partner szervezettel

1. [Olvassa el, hogyan működik a hozzáférés a külső felhasználók számára](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Külső felhasználók beállításainak áttekintése](entitlement-management-external-users.md#settings-for-external-users)
1. [Külső szervezethez való kapcsolódás hozzáadása](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Access Package Manager: együttműködés külső partneri szervezettel

1. [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md#start-new-access-package)
1. [Csoportok, csoportok, alkalmazások vagy SharePoint-helyek hozzáadása a csomag eléréséhez](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Házirend hozzáadása, amely lehetővé teszi a címtárban lévő felhasználók számára a hozzáférés kérését](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Lejárati beállítások megadása](entitlement-management-access-package-create.md#lifecycle)
1. [Másolja a hivatkozást a hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md)
1. A külső partner Contact partnerének hivatkozásának elküldése a felhasználókkal való megosztásra

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Kérelmező: az erőforrásokhoz való hozzáférés kérése külső felhasználóként

1. A partnertől kapott hozzáférési csomag hivatkozásának megkeresése
1. [Bejelentkezés a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Jóváhagyó: kérelmek jóváhagyása az erőforrásokhoz

1. [Kérelem megnyitása a saját hozzáférési portálon](entitlement-management-request-approve.md#open-request)
1. [Hozzáférési kérelem jóváhagyása vagy megtagadása](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Kérelmező: megtekintheti azokat az erőforrásokat, amelyeknek már van hozzáférése

1. [Bejelentkezés a saját hozzáférési portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Aktív hozzáférési csomagok megtekintése

## <a name="day-to-day-management"></a>Napi felügyelet

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Access Package Manager: egy projekt erőforrásainak frissítése

1. [Videó megtekintése: a mindennapos felügyelet: a dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. A hozzáférési csomag megnyitása
1. [Csoportok, csoportok, alkalmazások vagy SharePoint-webhelyek hozzáadása vagy eltávolítása](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Access Package Manager: egy projekt időtartamának frissítése

1. [Videó megtekintése: a mindennapos felügyelet: a dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. A hozzáférési csomag megnyitása
1. [Az életciklus beállításainak megnyitása](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [A lejárat beállításainak frissítése](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Hozzáférés a Package Managerhez: a projekthez való hozzáférés jóváhagyásának frissítése

1. [Videó megtekintése: a mindennapos felügyelet: a dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [A kérelmek beállításainak meglévő házirendjének megnyitása](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [A jóváhagyási beállítások frissítése](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Access Package Manager: egy projekt felhasználóinak frissítése

1. [Videó megtekintése: a mindennapos felügyelet: a dolgok megváltoztak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [A már nem szükséges felhasználók eltávolítása](entitlement-management-access-package-assignments.md)
1. [A kérelmek beállításainak meglévő házirendjének megnyitása](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Hozzáférésre szoruló felhasználók hozzáadása](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Hozzáférés a Package Managerhez: adott felhasználók közvetlen kiosztása egy hozzáférési csomaghoz

1. [Ha a felhasználóknak eltérő életciklus-beállításokra van szükségük, vegyen fel egy új szabályzatot a hozzáférési csomagba](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Konkrét felhasználók közvetlen kiosztása a hozzáférési csomaghoz](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Hozzárendelések és jelentések

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Rendszergazda: egy hozzáférési csomag hozzárendeléseinek megtekintése

1. Hozzáférési csomag megnyitása
1. [Hozzárendelések megtekintése](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Jelentések és naplók archiválása](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Rendszergazda: a felhasználókhoz rendelt erőforrások megtekintése

1. [Felhasználó hozzáférési csomagjainak megtekintése](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Felhasználó erőforrás-hozzárendeléseinek megtekintése](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programozott felügyelet

Microsoft Graph használatával is kezelheti a hozzáférési csomagokat, a katalógusokat, a szabályzatokat, a kérelmeket és a hozzárendeléseket.  Egy megfelelő szerepkörrel rendelkező felhasználó, aki a delegált engedéllyel rendelkező alkalmazással rendelkezik, `EntitlementManagement.ReadWrite.All` meghívhatja a [jogosultsági felügyeleti API](/graph/tutorial-access-package-api?view=graph-rest-beta)-t.

## <a name="next-steps"></a>Következő lépések

- [Delegálás és szerepkörök](entitlement-management-delegate.md)
- [Kérelmek feldolgozása és e-mail-értesítések](entitlement-management-process.md)
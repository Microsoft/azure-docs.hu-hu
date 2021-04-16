---
title: A jogosultságkezelés gyakori forgatókönyvei – Azure AD
description: Megismeri a jogosultságkezelés gyakori forgatókönyveit Azure Active Directory magas szintű lépéseket.
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
ms.openlocfilehash: 28c16e4d73fc2379806e1a2bce2fa5dbb3247fed
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531969"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Az Azure AD jogosultságkezelésének gyakori forgatókönyvei

A szervezet jogosultságkezelését többféleképpen is konfigurálhatja. Ha azonban csak most ismerkedik, hasznos lehet megérteni a rendszergazdák, katalógustulajdonosok, hozzáférésicsomag-kezelők, jóváhagyók és kérelmezők gyakori forgatókönyvét.

## <a name="delegate"></a>Delegált

### <a name="administrator-delegate-management-of-resources"></a>Rendszergazda: Erőforrások felügyeletének delegálása

1. [Videó megtekintése: Delegálás az it-részlegtől a részlegvezetőhöz](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Felhasználók delegálása katalógus-létrehozói szerepkörbe](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Katalógus létrehozója: Erőforrások felügyeletének delegálása

- [Új katalógus létrehozása](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalógus tulajdonosa: Erőforrások felügyeletének delegálása

1. [Társtulajdonosok hozzáadása a katalógushoz](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Erőforrások hozzáadása a katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalógus tulajdonosa: A hozzáférési csomagok felügyeletének delegálása

1. [Videó megtekintése: Delegálás a katalógus tulajdonosától a hozzáférési csomagkezelőhöz](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Felhasználók delegálása csomagkezelői szerepkörhöz](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>A szervezeti felhasználók hozzáférésének szabályozása

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Hozzáférésicsomag-kezelő: A szervezet alkalmazottai hozzáférést kérhetnek az erőforrásokhoz

1. [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md#start-new-access-package)
1. [Csoportok, Teams-, alkalmazások vagy SharePoint-webhelyek hozzáadása a csomag eléréséhez](entitlement-management-access-package-create.md#resource-roles)
1. [Kérelem-szabályzat hozzáadása, amely lehetővé teszi a címtárban található felhasználók számára a hozzáférés kérését](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Elévülési beállítások megadása](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Kérelmező: Erőforrásokhoz való hozzáférés kérése

1. [Jelentkezzen be a Saját hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Hozzáférési csomag megkeresve
1. [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Jóváhagyó: Erőforrásokra vonatkozó kérelmek jóváhagyása

1. [Kérés megnyitása a Saját hozzáférés portálon](entitlement-management-request-approve.md#open-request)
1. [Hozzáférési kérelem jóváhagyása vagy megtagadása](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Kérelmező: Megtekintheti az erőforrásokat, amelyekhez már hozzáféréssel rendelkezik

1. [Jelentkezzen be a Saját hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Aktív hozzáférési csomagok megtekintése

## <a name="govern-access-for-users-outside-your-organization"></a>Hozzáférés szabályozása a szervezeten kívüli felhasználók számára

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Rendszergazda: Együttműködés külső partnerszervezettel

1. [A külső felhasználók hozzáférésének működése](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Külső felhasználók beállításainak áttekintése](entitlement-management-external-users.md#settings-for-external-users)
1. [Kapcsolat hozzáadása a külső szervezethez](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Hozzáférésicsomag-kezelő: Együttműködés külső partnerszervezettel

1. [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md#start-new-access-package)
1. [Csoportok, Teams-, alkalmazások vagy SharePoint-webhelyek hozzáadása a csomag eléréséhez](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Kérelem-szabályzat hozzáadása annak érdekében, hogy a címtárban nem található felhasználók hozzáférést kérnek](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Elévülési beállítások megadása](entitlement-management-access-package-create.md#lifecycle)
1. [A hozzáférési csomag kérése hivatkozásának másolása](entitlement-management-access-package-settings.md)
1. Küldje el a hivatkozást a külső partner kapcsolattartó partnerének, hogy megosztja a felhasználókkal

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Kérelmező: Erőforrás-hozzáférés kérése külső felhasználóként

1. Keresse meg a kapcsolattartótól kapott hozzáférésicsomag-hivatkozást
1. [Jelentkezzen be a Saját hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Hozzáférés kérése](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Jóváhagyó: Erőforrásokra vonatkozó kérelmek jóváhagyása

1. [Kérés megnyitása a Saját hozzáférés portálon](entitlement-management-request-approve.md#open-request)
1. [Hozzáférési kérelem jóváhagyása vagy megtagadása](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Kérelmező: Megtekintheti az erőforrásokat, amelyekhez már hozzáférése van

1. [Jelentkezzen be a Saját hozzáférés portálra](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Aktív hozzáférési csomagok megtekintése

## <a name="day-to-day-management"></a>Napi felügyelet

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Hozzáférésicsomag-kezelő: Projekt erőforrásainak frissítése

1. [Videó megtekintése: Napi felügyelet: Dolgok változtak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Nyissa meg a hozzáférési csomagot
1. [Csoportok, Teams-, alkalmazások vagy SharePoint-webhelyek hozzáadása vagy eltávolítása](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Hozzáférésicsomag-kezelő: Egy projekt időtartamának frissítése

1. [Videó megtekintése: Napi felügyelet: Dolgok változtak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Nyissa meg a hozzáférési csomagot
1. [Az életciklus-beállítások megnyitása](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Az elévülési beállítások frissítése](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Hozzáférésicsomag-kezelő: A projekthez való hozzáférés jóváhagyásának frissítése

1. [Videó megtekintése: Napi felügyelet: Dolgok változtak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [A kérelembeállítások meglévő szabályzatának megnyitása](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [A jóváhagyási beállítások frissítése](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Hozzáférésicsomag-kezelő: A projekthez szükséges személyek frissítése

1. [Videó megtekintése: Napi felügyelet: Dolgok változtak](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Távolítsa el azokat a felhasználókat, akikhez már nincs szükség hozzáférésre](entitlement-management-access-package-assignments.md)
1. [A kérelembeállítások meglévő szabályzatának megnyitása](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Hozzáférésre van szüksége a felhasználóknak](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Hozzáférésicsomag-kezelő: Adott felhasználók közvetlen hozzárendelése egy hozzáférési csomaghoz

1. [Ha a felhasználóknak eltérő életciklus-beállításokra van szükségük, adjon hozzá egy új szabályzatot a hozzáférési csomaghoz](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Adott felhasználók közvetlen hozzárendelése a hozzáférési csomaghoz](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Hozzárendelések és jelentések

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Rendszergazda: Hozzáférési csomaghoz hozzárendelt felhasználók megtekintése

1. Hozzáférési csomag megnyitása
1. [Hozzárendelések megtekintése](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Jelentések és naplók archiválása](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Rendszergazda: Felhasználókhoz rendelt erőforrások megtekintése

1. [Felhasználó hozzáférési csomagjainak megtekintése](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Felhasználó erőforrás-hozzárendelésének megtekintése](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programozott felügyelet

A hozzáférési csomagokat, katalógusokat, szabályzatokat, kéréseket és hozzárendeléseket az Microsoft Graph.  A megfelelő szerepkörben, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználók a jogosultságkezelési `EntitlementManagement.ReadWrite.All` [API-t hívhatja meg.](/graph/tutorial-access-package-api)

## <a name="next-steps"></a>Következő lépések

- [Delegálás és szerepkörök](entitlement-management-delegate.md)
- [Folyamat- és e-mail-értesítések kérése](entitlement-management-process.md)
---
title: Alkalmazás-engedélyezési engedélyek a Azure Active Directory egyéni szerepköreihez | Microsoft Docs
description: A Azure Portal, a PowerShell vagy a Graph API egyéni Azure AD-szerepkörökhöz tartozó alkalmazás-engedélyezési engedélyek előnézete.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: f9c2c15bbfcf9a9271e629ef26c11ecc4cbaaa6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740108"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Alkalmazás-engedélyezési engedélyek a Azure Active Directory egyéni szerepköreihez

Ez a cikk a jelenleg elérhető alkalmazás-engedélyezési engedélyeket tartalmazza a Azure Active Directory (Azure AD) egyéni szerepkör-definícióinak esetében. Ebből a cikkből megtudhatja, milyen engedélyek szükségesek az alkalmazáshoz való hozzáféréshez és engedélyekhez kapcsolódó gyakori forgatókönyvekhez.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

A szolgáltatás használatához prémium szintű Azure AD P1 licencre van szükség az Azure AD-szervezet számára. A követelményeinek leginkább megfelelő licenc kiválasztásáról lásd [az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásait összehasonlító cikket](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Alkalmazás hozzájárulási engedélyei

Az ebben a cikkben felsorolt engedélyekkel kezelheti az alkalmazás-engedélyezési szabályzatokat, valamint engedélyezheti az alkalmazások beleegyezését.

> [!NOTE]
> Az Azure AD felügyeleti portálon még nem támogatott az ebben a cikkben felsorolt engedélyek hozzáadása egy egyéni címtárbeli szerepkör-definícióhoz. Az [Azure ad PowerShell használatával létre kell hoznia egy egyéni címtárbeli szerepkört](custom-create.md#create-a-role-using-powershell) a cikkben felsorolt engedélyekkel.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Delegált engedélyek megadása az alkalmazásokhoz saját (felhasználói jóváhagyás) nevében

Annak lehetővé tétele, hogy a felhasználók saját maguk nevében (felhasználói beleegyezett) megadják az alkalmazásoknak a jóváhagyást.

- Microsoft. Directory/servicePrincipals/managePermissionGrantsForSelf. ID

Ahol az egy `{id}` [alkalmazás-engedélyezési szabályzat](../manage-apps/manage-app-consent-policies.md) azonosítójának helyébe lép, amely megadja azokat a feltételeket, amelyeknek teljesülniük kell ahhoz, hogy ez az engedély aktív legyen.

Ha például lehetővé szeretné tenni, hogy a felhasználók a saját nevükben adják meg a hozzájárulásukat, a beépített alkalmazás-engedélyezési szabályzatnak AZONOSÍTÓval kell `microsoft-user-default-low` rendelkeznie, az engedélyt kell használnia `...managePermissionGrantsForSelf.microsoft-user-default-low` .

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Engedélyek megadása az alkalmazásoknak az összes nevében (rendszergazdai jóváhagyás)

A bérlői szintű rendszergazdai jogosultság delegálása az alkalmazásokhoz mind a delegált engedélyek, mind az alkalmazási engedélyek (alkalmazás szerepkörei) esetében:

- Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. ID

Ahol az egy `{id}` [alkalmazás-engedélyezési szabályzat](../manage-apps/manage-app-consent-policies.md) azonosítójának helyébe lép, amely megadja azokat a feltételeket, amelyeknek teljesülniük kell ahhoz, hogy ez az engedély használható legyen.

Ha például lehetővé szeretné tenni a szerepkör-engedményesek számára, hogy a bérlői szintű rendszergazdai jóváhagyást adjanak az alkalmazásoknak, amelyek egy AZONOSÍTÓval rendelkező egyéni [alkalmazás-engedélyezési szabályzatnak](../manage-apps/manage-app-consent-policies.md) vannak alávetve `low-risk-any-app` , akkor az engedélyt kell használniuk `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app`

### <a name="managing-app-consent-policies"></a>Alkalmazás-engedélyezési házirendek kezelése

Az [alkalmazás-engedélyezési szabályzatok](../manage-apps/manage-app-consent-policies.md)létrehozásának, frissítésének és törlésének delegálására.

- Microsoft. Directory/permissionGrantPolicies/Create
- Microsoft. Directory/permissionGrantPolicies/standard/olvasás
- Microsoft. Directory/permissionGrantPolicies/Basic/Update
- Microsoft. Directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>Engedélyek teljes listája

Engedély | Leírás
---------- | -----------
Microsoft. Directory/servicePrincipals/managePermissionGrantsForSelf. ID | Lehetővé teszi, hogy az alkalmazásokhoz való hozzájárulást önmaga (felhasználói hozzájárulás) nevében adja meg, az alkalmazás-engedélyezési szabályzat hatálya alá tartozik `{id}` .
Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. ID | Engedélyt ad az alkalmazásoknak az összes (bérlői szintű rendszergazdai hozzájárulás) nevében való hozzájárulásra az alkalmazásra vonatkozó engedélyezési szabályzat hatálya alá `{id}` .
Microsoft. Directory/permissionGrantPolicies/standard/olvasás | Lehetővé teszi az alkalmazás-engedélyezési házirendek olvasását.
Microsoft. Directory/permissionGrantPolicies/Basic/Update | A meglévő alkalmazás-engedélyezési szabályzatok alapszintű tulajdonságainak frissítését teszi lehetővé.
Microsoft. Directory/permissionGrantPolicies/Create | Lehetővé teszi az alkalmazás-engedélyezési házirendek létrehozását.
Microsoft. Directory/permissionGrantPolicies/delete | Lehetővé teszi az alkalmazás-engedélyezési házirendek törlését.

## <a name="next-steps"></a>Következő lépések

- [Egyéni szerepkörök létrehozása a Azure Portal, az Azure AD PowerShell és a Graph API használatával](custom-create.md)
- [Egyéni szerepkör hozzárendeléseinek megtekintése](../roles/view-assignments.md)

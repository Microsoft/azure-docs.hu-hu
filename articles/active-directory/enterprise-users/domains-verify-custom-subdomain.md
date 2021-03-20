---
title: Altartomány hitelesítési típusának módosítása a PowerShell és a Graph-Azure Active Directory használatával | Microsoft Docs
description: A Azure Active Directory gyökértartomány-beállításaitól örökölt alapértelmezett altartomány-hitelesítési beállítások módosítása.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95503670"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Az altartomány-hitelesítés típusának módosítása Azure Active Directory

Miután hozzáadta a legfelső szintű tartományt a Azure Active Directoryhoz (Azure AD), az Azure AD-szervezetben az adott gyökérhez hozzáadott összes további altartomány automatikusan örökli a gyökértartomány hitelesítési beállításait. Ha azonban a tartományi hitelesítési beállításokat a legfelső szintű tartományi beállításoktól függetlenül szeretné kezelni, mostantól a Microsoft Graph API-t is használhatja. Ha például egy összevont gyökértartomány, például a contoso.com, ez a cikk segítséget nyújt egy altartomány (például a child.contoso.com) ellenőrzéséhez az összevont helyett.

Az Azure AD-portálon, amikor a szülőtartomány összevont, és a rendszergazda megpróbál ellenőrizni egy felügyelt altartományt az **Egyéni tartománynevek** lapon, a "nem sikerült hozzáadni a tartományhoz" hibát az "egy vagy több tulajdonság érvénytelen értékeket tartalmaz" okból. Ha ezt az altartományt a Microsoft 365 felügyeleti központból próbálja meg hozzáadni, akkor hasonló hibaüzenetet fog kapni. A hibával kapcsolatos további információkért tekintse meg [a gyermektartomány nem öröklik az Office 365, az Azure vagy az Intune tartománybeli változásait](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Egyéni altartomány ellenőrzése

Mivel az altartományok alapértelmezés szerint öröklik a gyökértartomány hitelesítési típusát, az altartományt az Azure AD-ben lévő legfelső szintű tartományba kell előléptetni az Microsoft Graph segítségével, így beállíthatja a hitelesítési típust a kívánt típusra.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Adja hozzá az altartományt, és tekintse meg a hitelesítési típusát

1. A PowerShell használatával adja hozzá az új altartományt, amelynek a legfelső szintű tartománya alapértelmezett hitelesítési típusa. Az Azure AD és a Microsoft 365 felügyeleti központja még nem támogatja ezt a műveletet.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. A tartomány beszerzéséhez használja az [Azure ad Graph Explorert](https://graphexplorer.azurewebsites.net) . Mivel a tartomány nem legfelső szintű tartomány, a rendszer örökli a gyökértartomány-hitelesítés típusát. A parancs és az eredmények az alábbiak szerint jelenhetnek meg a saját bérlői AZONOSÍTÓjának használatával:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Az Azure AD Graph Explorer API használata a legfelső szintű tartomány létrehozásához

Az altartomány előléptetéséhez használja a következő parancsot:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Az altartomány-hitelesítés típusának módosítása

1. Az altartomány-hitelesítés típusának módosításához használja a következő parancsot:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Ellenőrizze az Azure AD Graph Explorerben, hogy az altartomány hitelesítési típusa már felügyelve van-e:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Következő lépések

- [Egyéni tartománynevek hozzáadása](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Tartománynevek kezelése](domains-manage.md)
- [ForceDelete egy egyéni tartománynevet Microsoft Graph API-val](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)
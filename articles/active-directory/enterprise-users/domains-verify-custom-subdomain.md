---
title: Altartomány hitelesítési típusának módosítása a PowerShell és a Graph használatával – Azure Active Directory | Microsoft Docs
description: Módosítsa a tartomány gyökértartomány-beállításaiból örökölt alapértelmezett altartomány-Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4acf01a6672d17e62b6ebf5c6f43c8d6145f95a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739313"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Altartomány hitelesítési típusának módosítása a Azure Active Directory

Miután hozzáadta a gyökértartományt az Azure Active Directory (Azure AD) szolgáltatáshoz, az Azure AD-szervezetben az adott gyökértartományhoz hozzáadott összes további altartomány automatikusan örökli a hitelesítési beállítást a gyökértartománytól. Ha azonban a tartományhitelesítési beállításokat a gyökértartomány beállításaitól függetlenül szeretné kezelni, mostantól használhatja a Microsoft Graph API-t. Ha például egy összevont gyökértartománya van (például contoso.com, ez a cikk segít ellenőrizni egy altartományt, például child.contoso.com felügyeltként, nem pedig összevontként.

Az Azure AD-portálon, ha a szülőtartomány összevont, és a rendszergazda megpróbál  ellenőrizni egy felügyelt altartományt az Egyéni tartománynevek oldalon, "A tartomány hozzáadása sikertelen" hibaüzenetet fog kapni azzal az okkal, hogy "Egy vagy több tulajdonság érvénytelen értékeket tartalmaz". Ha megpróbálja hozzáadni ezt az altartományt a Microsoft 365 Felügyeleti központ, hasonló hibaüzenetet fog kapni. További információ a hibáról: A gyermektartomány nem örökli a szülőtartomány változásait az [Office 365-ben,](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)az Azure-ban vagy az Intune-ban.

## <a name="how-to-verify-a-custom-subdomain"></a>Egyéni altartomány ellenőrzése

Mivel az altartományok alapértelmezés szerint öröklik a gyökértartomány hitelesítési típusát, az altartományt az Microsoft Graph használatával elő kell előléptetnie gyökértartományba az Azure AD-ban, hogy a hitelesítési típust a kívánt típusra állítsa be.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Adja hozzá az altartományt, és tekintse meg a hitelesítési típusát

1. A PowerShell használatával adja hozzá az új altartományt, amely a gyökértartomány alapértelmezett hitelesítési típusát használja. Az Azure AD és Microsoft 365 felügyeleti központ még nem támogatja ezt a műveletet.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Az [Azure AD Graph Explorerrel szerezze](https://graphexplorer.azurewebsites.net) be a tartományt. Mivel a tartomány nem gyökértartomány, örökli a gyökértartomány hitelesítési típusát. A parancs és az eredmények a következőképpen néznek ki, a saját bérlőazonosítóját használva:

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

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Az Azure AD Graph Explorer API használata gyökértartományként való használatra

Az altartományt a következő paranccsal előléptetheti:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Az altartomány hitelesítési típusának módosítása

1. Az altartomány hitelesítési típusának a következő paranccsal módosíthatja:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Ellenőrizze az Azure AD Graph Explorer GET szolgáltatásán keresztül, hogy az altartomány hitelesítési típusa mostantól felügyelt-e:

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
- [Egyéni tartománynév kényszerített Microsoft Graph API-val](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)
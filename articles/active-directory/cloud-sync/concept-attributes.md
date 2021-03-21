---
title: Az Azure AD-séma és az egyéni kifejezések ismertetése
description: Ez a cikk az Azure AD-sémát, a kiépítési ügynök által forgalmazott attribútumokat és az egyéni kifejezéseket ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 750bc2f4f535238dbb757afc8b615fa71063d74d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613901"
---
# <a name="understand-the-azure-ad-schema"></a>Az Azure AD-séma ismertetése
A Azure Active Directory (Azure AD) egyik objektuma, például bármely címtár, egy programozott, magas szintű adatszerkezet, amely felhasználók, csoportok és névjegyek számára is jelent. Amikor új felhasználót vagy kapcsolatot hoz létre az Azure AD-ben, az adott objektum új példányát hozza létre. Ezek a példányok a tulajdonságaik alapján különböztethetők meg.

Az Azure AD-beli tulajdonságok az Azure AD-beli objektumok egy példányával kapcsolatos információk tárolására felelős elemek.

Az Azure AD-séma határozza meg azokat a szabályokat, amelyekhez tulajdonságokat lehet használni egy adott bejegyzésben, a tulajdonságokat tartalmazó értékek fajtáit, valamint azt, hogy a felhasználók hogyan használhatják ezeket az értékeket. 

Az Azure AD két típusú tulajdonsággal rendelkezik:
- **Beépített tulajdonságok**: az Azure ad-séma által előre definiált tulajdonságok. Ezek a tulajdonságok eltérő felhasználást biztosítanak, és lehetséges, hogy nem érhetők el.
- **Directory-bővítmények**: az Azure ad saját használatra való testreszabásához megadott tulajdonságok. Ha például kibővítette a helyszíni Active Directory egy bizonyos attribútummal, és az attribútumot szeretné átdolgozni, a megadott egyéni tulajdonságok egyikét használhatja. 

## <a name="attributes-and-expressions"></a>Attribútumok és kifejezések
Ha egy objektum, például egy felhasználó az Azure AD-be van kiépítve, a rendszer létrehozza a felhasználói objektum egy új példányát. Ez a létrehozás magában foglalja az objektum tulajdonságait is, amelyek más néven attribútumok. Kezdetben az újonnan létrehozott objektum attribútumai a szinkronizálási szabályok által meghatározott értékekre vannak beállítva. Ezeket az attribútumokat a rendszer a felhőalapú kiépítési ügynökön keresztül naprakészen tartja.

![Objektum kiépítés](media/concept-attributes/attribute-1.png)

Előfordulhat például, hogy egy felhasználó tagja egy marketing részlegnek. Az Azure AD Department attribútuma kezdetben a kiépítés után jön létre, az érték pedig marketing. Hat hónappal később, ha az értékesítésre változnak, a helyszíni Active Directory Department attribútuma értékesítésre változik. Ez a változás szinkronizál az Azure AD-vel, és az Azure AD felhasználói objektumában jelenik meg.

Az attribútumok szinkronizálása lehet közvetlen, ahol az Azure AD-ban lévő érték közvetlenül a helyszíni attribútum értékére van beállítva. Vagy egy programozott kifejezés is kezelheti a szinkronizálást. Szükség van egy programozott kifejezésre olyan esetekben, amikor valamilyen logikát vagy meghatározást kell végezni az érték feltöltéséhez.

Ha például a "" e-mail-attribútummal rendelkezik, john.smith@contoso.com és a " @contoso.com " részének és a folyamatnak csak a "John. Smith" értéket kell kimutatnia, akkor a következőhöz hasonlót kell használnia:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Minta bemenet/kimenet:** <br>

* **Bemenet** (e-mail): " john.smith@contoso.com "
* **Kimenet**: "John. Smith"

Az egyéni kifejezések és a szintaxis megírásával kapcsolatos további információkért lásd: [kifejezések írása az attribútumok megfeleltetéséhez Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).

Az alábbi táblázat a gyakori attribútumokat és az Azure AD-vel való szinkronizálást ismerteti.


|Helyszíni Active Directory|Leképezés típusa|Azure AD|
|-----|-----|-----|
|CN|Direct|Köznapinév
|Országhívószám|Direct|Országhívószám|
|displayName|Direct|displayName|
|givenName|Expression|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress|

## <a name="view-the-schema"></a>A séma megtekintése
> [!WARNING]
> A felhő-szinkronizálás konfigurációja létrehoz egy szolgáltatásnevet. Az egyszerű szolgáltatásnév a Azure Portal látható. Ne módosítsa az attribútum-hozzárendeléseket a Azure Portal egyszerű szolgáltatásnév használatával.  Ez nem támogatott.

A séma megtekintéséhez és ellenőrzéséhez kövesse az alábbi lépéseket.

1.  Lépjen a [Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer).
1.  Jelentkezzen be a globális rendszergazdai fiókjával.
1.  A bal oldalon válassza az **engedélyek módosítása** lehetőséget, és győződjön meg arról, hogy a  **könyvtár. ReadWrite. All** .
1.  Futtassa a lekérdezést `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')` . Ez a lekérdezés az egyszerű szolgáltatások szűrt listáját adja vissza.
1.  Keresse meg `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` és jegyezze fel a értékét `"id"` .
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Cserélje le az `{Service Principal id}` értéket a értékre, és futtassa a lekérdezést `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/` .
1. Keresse meg `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` és jegyezze fel a értékét `"id"` .
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Most futtassa a lekérdezést `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` .
 
    Például: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Cserélje le az `{Service Principal Id}` `{AD2ADD Provisioning Id}` értékeket az értékekre.

1. Ez a lekérdezés a sémát adja vissza.

   ![Visszaadott séma](media/concept-attributes/schema-1.png)
 
## <a name="next-steps"></a>Következő lépések

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
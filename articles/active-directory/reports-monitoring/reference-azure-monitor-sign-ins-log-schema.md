---
title: Az Azure Monitor (előzetes verzió) az Azure Active Directory bejelentkezési napló séma értelmezése |} A Microsoft Docs
description: A napló-sémát a az Azure Monitor (előzetes verzió) használata az Azure AD bejelentkezési leírása
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 4a610780d11452a8207a1108e4469bc9de62ecb8
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51620918"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor-preview"></a>Az Azure Monitor (előzetes verzió) az Azure AD bejelentkezési naplók séma értelmezése

Ez a cikk ismerteti az Azure monitorban az Azure Active Directory (Azure AD) bejelentkezési napló séma. A legtöbb bejelentkezési kapcsolatos információ áll rendelkezésre a a *tulajdonságok* attribútuma a `records` objektum.

```json
{ 
    "records": [ 
    { 
        "time": "2018-05-16T16:09:58.4634578Z", 
        "resourceId": null, 
        "operationName": "Sign-in activity", 
        "operationVersion": "1.0", 
        "category": "SignIn", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "50140", 
        "resultSignature": "None", 
        "resultDescription": "Other", 
        "durationMs": 0, 
        "callerIpAddress": "167.220.0.158", 
        "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
        "identity": "arvind harinder", 
        "Level": 4, 
        "location": "US", 
        "properties": { 
            "id": "0782c515-08b6-4029-a65c-29d9a3d20800", 
            "createdDateTime": "2018-05-16T16:09:58.4634578+00:00", 
            "userDisplayName": "Arvind Harinder", 
            "userPrincipalName": "ah@wingtiptoysonline.onmicrosoft.com", 
            "userId": "5b9f356d-9592-42fd-9ec4-d70963909534", 
            "appId": "c44b4083-3bb0-49c1-b47d-974e53cbdf3c", 
            "appDisplayName": "Azure Portal", 
            "ipAddress": "167.220.0.158", 
            "status": { 
                "errorCode": 50140, 
                "failureReason": "Other" 
            }, 
            "clientAppUsed": "Browser", 
            "deviceDetail": { 
                "operatingSystem": "Windows 10", 
                "browser": "Chrome 66.0.3359" 
            }, 
            "location": { 
                "city": "Sammamish", 
                "state": "Washington", 
                "countryOrRegion": "US", 
                "geoCoordinates": { 
                    "latitude": 47.66630935668945, 
                    "longitude": -122.09821319580078 
                } 
            }, 
            "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
            "conditionalAccessStatus": 2, 
            "conditionalAccessPolicies": [ 
            { 
                "id": "de7e60eb-ed89-4d73-8205-2227def6b7c9", 
                "displayName": "[billg] SharePoint limited access policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "7412a2d8-cbb1-4f1c-96cf-8410b4b8b37b", 
                "displayName": "[BillG] AIP MFA Policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "727ed8ea-059d-4d8f-aba5-c1dc500e8b06", 
                "displayName": "[billg] mfa for mail", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "6701123a-b4c6-48af-8565-565c8bf7cabc", 
                "displayName": "Medium signin risk block", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "fbafa2da-cf7f-4ec3-83cf-281188e53f76", 
                "displayName": "Require MFA for admins [Ignite talk] ", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "15339054-709d-4e06-a9ec-342bf043ea56", 
                "displayName": "Enhanced proofing for Azure portal [Ignite talk]", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "2ff9436f-bc72-4ce6-b17e-e7e51153146e", 
                "displayName": "[calebb] AIP policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "46ab586b-9447-4847-a889-e60705d96e56", 
                "displayName": "Test policy, OR", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "ceb6e17e-a5d0-4b3a-a150-6c2be2d5b0e9", 
                "displayName": "mm policy with Duo", 
                "enforcedGrantControls": [ 
                    "Require Duo Mfa" 
                ], 
            "enforcedSessionControls": [], 
            "result": 2 
            }, 
            ], 
            "isRisky": false 
            } 
        } 
    } 
```

## <a name="field-descriptions"></a>Mező leírása

| Mező neve | Leírás |
|------------|-------------|
| Time | Dátuma és időpontja (UTC). |
| ResourceId | Ez az érték nem leképezett, és biztonságosan figyelmen kívül hagyhatja ezt a mezőt.  |
| OperationName | A bejelentkezések, az értéke mindig *bejelentkezési tevékenység*. |
| operationVersion | Az ügyfél által kért REST API-verzió. |
| Kategória | A bejelentkezések, az értéke mindig *bejelentkezési*. | 
| TenantId | GUID, a naplók társított tenant. |
| ResultType | A bejelentkezési művelet eredménye lehet *sikeres* vagy *hiba*. | 
| ResultSignature | Tartalmazza a hiba kódja, ha bármely, a bejelentkezési művelet. |
| ResultDescription | A bejelentkezési művelet hiba leírását. |
| durationMs |  Ez az érték nem leképezett, és biztonságosan figyelmen kívül hagyhatja ezt a mezőt.|
| CallerIpAddress | A kérést leadó ügyfél IP-címét. | 
| CorrelationId | A nem kötelező GUID, amely az ügyfél által átadott. Ez az érték segít összevetését ügyféloldali műveletek a kiszolgálóoldali műveletek, és ez akkor hasznos, ha követi nyomon, amelyek szolgáltatások naplók. |
| Identitás | Az identitás a tokenben szereplő leadta a kérelmet. Lehet, hogy egy felhasználói fiókot, rendszerfiók vagy egyszerű szolgáltatást. |
| Szint | Üzenet típusa biztosít. A naplózás, a rendszer mindig *tájékoztató*. |
| Hely | Itt a bejelentkezési tevékenységek helyét. |
| Tulajdonságok | Az összes társított bejelentkezési tulajdonságok listája. További információkért lásd: [Microsoft Graph API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Ebben a sémában a bejelentkezési erőforrásként attribútum ugyanazokat a neveket használja az olvashatóság érdekében.

## <a name="next-steps"></a>További lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [További információ az Azure diagnosztikai naplók](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

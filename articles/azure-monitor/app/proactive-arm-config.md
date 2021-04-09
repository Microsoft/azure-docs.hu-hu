---
title: Intelligens észlelési szabályok beállításai – Azure Application Insights
description: Az Azure Application Insights intelligens észlelési szabályok kezelésének és konfigurálásának automatizálása Azure Resource Manager-sablonokkal
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726121"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Application Insights intelligens észlelési szabályok kezelése Azure Resource Manager-sablonok használatával

A Application Insightsban található intelligens észlelési szabályok kezelhetők és konfigurálhatók [Azure Resource Manager sablonok](../../azure-resource-manager/templates/template-syntax.md)használatával.
Ez a módszer akkor használható, ha új Application Insights erőforrásokat telepít Azure Resource Manager automatizálással, vagy a meglévő erőforrások beállításainak módosítására.

## <a name="smart-detection-rule-configuration"></a>Intelligens észlelési szabály konfigurációja

Az intelligens detektálási szabályokhoz a következő beállításokat konfigurálhatja:
- Ha a szabály engedélyezve van (az alapértelmezett érték **igaz**.)
- Ha az e-maileket el kell juttatni az előfizetés [figyelési olvasójának](../../role-based-access-control/built-in-roles.md#monitoring-reader) felhasználóinak, illetve a [közreműködői szerepkörök figyelésére](../../role-based-access-control/built-in-roles.md#monitoring-contributor) , ha észlelés található (az alapértelmezett érték **igaz**.)
- Minden további e-mail-címzett, akinek értesítést kell kapnia, amikor észlelés található.
    -  Az e-mail-konfiguráció nem érhető el az _előzetes_ verzióként megjelölt intelligens észlelési szabályokhoz.

Ha engedélyezni szeretné a szabályok beállításait a Azure Resource Manageron keresztül, az intelligens észlelési szabály konfigurációja mostantól belső erőforrásként is elérhető a **ProactiveDetectionConfigs** nevű Application Insights erőforráson belül.
A maximális rugalmasság érdekében minden egyes intelligens észlelési szabályt egyedi értesítési beállításokkal lehet konfigurálni.

## <a name="examples"></a>Példák

Az alábbiakban néhány példát láthat arra, hogyan konfigurálhatja az intelligens észlelési szabályok beállításait Azure Resource Manager-sablonok használatával.
Az összes minta egy _"myApplication"_ nevű Application Insights erőforrásra, valamint a "hosszú függőségi időtartam intelligens észlelési szabályára" vonatkozik, amely belsőleg _"longdependencyduration"_ néven szerepel.
Ügyeljen rá, hogy cserélje le a Application Insights erőforrás nevét, és adja meg a vonatkozó intelligens észlelési szabály belső nevét. Az egyes intelligens észlelési szabályokhoz tartozó belső Azure Resource Manager-nevek listáját az alábbi táblázatban találja.

### <a name="disable-a-smart-detection-rule"></a>Intelligens észlelési szabály letiltása

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>E-mail-értesítések küldésének letiltása intelligens észlelési szabályhoz

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>További e-mail-címzettek hozzáadása intelligens észlelési szabályhoz

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Intelligens észlelési szabályok nevei

Az alábbi táblázat az intelligens észlelési szabályok neveit mutatja, ahogy azok a portálon jelennek meg, a belső nevükkel együtt, amelyeket a Azure Resource Manager sablonban kell használni.

> [!NOTE]
> Az _előzetesként_ megjelölt intelligens észlelési szabályok nem támogatják az e-mailes értesítéseket. Ezért csak a szabályok _engedélyezve_ tulajdonságát állíthatja be. 

| Azure Portal szabály neve | Belső név
|:---|:---|
| Lassú oldal betöltési ideje | slowpageloadtime |
| Lassú kiszolgáló válaszideje | slowserverresponsetime |
| Hosszú függőségi időtartam | longdependencyduration |
| Romlás a kiszolgáló válaszideje | degradationinserverresponsetime |
| Romlás a függőségi időtartamban | degradationindependencyduration |
| A nyomkövetés súlyossági arányának csökkenése (előzetes verzió) | extension_traceseveritydetector |
| Rendellenes növekedés a kivétel mennyisége (előzetes verzió) | extension_exceptionchangeextension |
| Potenciális memóriavesztés észlelhető (előzetes verzió) | extension_memoryleakextension |
| Lehetséges biztonsági probléma észlelhető (előzetes verzió) | extension_securityextensionspackage |
| A napi adatmennyiség rendellenes növekedése (előzetes verzió) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Hibák rendellenességének riasztási szabálya

Ez a Azure Resource Manager sablon azt mutatja be, hogyan állíthatók be a meghibásodási rendellenességek riasztási szabálya 2 súlyossággal.

> [!NOTE]
> A hiba anomália egy globális szolgáltatás, ezért a szabály helye a globális helyen jön létre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Ez a Azure Resource Manager sablon egyedi a hiba-rendellenességek riasztási szabálya számára, és eltér a jelen cikkben ismertetett klasszikus intelligens észlelési szabályoktól. Ha manuálisan szeretné kezelni a hibákra vonatkozó rendellenességeket, ezt Azure Monitor riasztásokban hajtja végre, míg az összes többi intelligens észlelési szabályt a felhasználói felület intelligens észlelés paneljén kezelheti.

## <a name="next-steps"></a>Következő lépések

További információ az automatikus észlelésről:

- [Hibaanomáliák](./proactive-failure-diagnostics.md)
- [Memóriavesztés](./proactive-potential-memory-leak.md)
- [Teljesítményanomáliák](./proactive-performance-diagnostics.md)


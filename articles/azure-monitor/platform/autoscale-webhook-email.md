---
title: Az e-mailek és a webhookok riasztási értesítéseinek küldése az autoscale használatával
description: Útmutató a webes URL-címek meghívásához vagy e-mail-értesítések küldéséhez Azure Monitorban.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009007"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>E-mailek és webhookok riasztási értesítéseinek küldése a Azure Monitorban az autoskálázási műveletek használatával
Ebből a cikkből megtudhatja, hogyan állíthatja be az eseményindítókat, hogy konkrét webes URL-címeket hívjon fel, vagy az Azure-ban végzett autoskálázási műveletek alapján küldjön e-mailt.  

## <a name="webhooks"></a>Webhookok
A webhookok lehetővé teszik, hogy az Azure riasztási értesítéseket más rendszerekre irányítsa a feldolgozás utáni vagy egyéni értesítések esetén. Tegyük fel például, hogy a riasztást olyan szolgáltatásokra irányítja, amelyek képesek az SMS-küldésre, a hibák naplózására és a csapatnak a csevegési vagy üzenetküldési szolgáltatásokkal való értesítésére. A webhook URI azonosítójának érvényes HTTP-vagy HTTPS-végpontnak kell lennie.

## <a name="email"></a>E-mail
Az e-maileket bármely érvényes e-mail-címre lehet elküldeni. A rendszergazdák és az előfizetés azon előfizetések rendszergazdái, akiknél a szabály fut, szintén értesítést kapnak.

## <a name="cloud-services-and-app-services"></a>Cloud Services és App Services
Cloud Services és kiszolgálófarm (App Services) Azure Portal is bejelentkezhet.

* Válassza a **skála mérőszám alapján** lehetőséget.

![skálázás](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok
A Resource Managerrel (virtuálisgép-méretezési csoportokkal) létrehozott újabb Virtual Machines a REST API, a Resource Manager-sablonok, a PowerShell és a parancssori felület használatával konfigurálható. Egy portál felülete még nem érhető el.
A REST API vagy Resource Manager-sablon használatakor a következő beállításokkal adja meg az értesítések elemet a [autoscalesettings](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) .

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Mező | Kötelező? | Leírás |
| --- | --- | --- |
| művelet |yes |az értéknek "Scale" értékűnek kell lennie |
| sendToSubscriptionAdministrator |yes |az értéknek "true" vagy "false" értékűnek kell lennie |
| sendToSubscriptionCoAdministrators |yes |az értéknek "true" vagy "false" értékűnek kell lennie |
| customEmails |yes |az érték lehet null [] vagy az e-mailek karakterlánc-tömbje. |
| webhookok |yes |az érték lehet null vagy érvényes URI |
| serviceUri |yes |érvényes HTTPS URI |
| properties |yes |az értéknek üresnek kell lennie {} , vagy kulcs-érték párokat is tartalmazhat. |

## <a name="authentication-in-webhooks"></a>Hitelesítés webhookokban
A webhook hitelesítése jogkivonat-alapú hitelesítéssel történik, ahol a webhook URI-JÁT lekérdezési paraméterként egy jogkivonat-AZONOSÍTÓval menti. Például: https: \/ /mysamplealert/webcallback? következőből tokenid = sometokenid&someparameter = érték1

## <a name="autoscale-notification-webhook-payload-schema"></a>Értesítési webhook-adattartalom sémájának autoskálázása
Az autoskálázási értesítés létrehozásakor a webhook hasznos adatai a következő metaadatokat tartalmazzák:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Mező | Kötelező? | Leírás |
| --- | --- | --- |
| status |yes |Az az állapot, amely azt jelzi, hogy egy autoskálázási művelet létrejött |
| művelet |yes |A példányok növekedése a "vertikális felskálázás", a példányok csökkenése pedig a "skálázás" lesz. |
| összefüggésben |yes |Az autoscale művelet kontextusa |
| időbélyeg |yes |Az autoskálázási művelet elindítására szolgáló időbélyegző |
| id |Yes |Az autoskálázási beállítás Resource Manager-azonosítója |
| name |Yes |Az autoskálázási beállítás neve |
| Részletek |Yes |Az autoskálázási szolgáltatás és a példányszám változásának magyarázata |
| subscriptionId |Yes |A méretezni kívánt cél erőforrás előfizetés-azonosítója |
| resourceGroupName |Yes |A méretezni kívánt cél erőforrás erőforráscsoport-neve |
| resourceName |Yes |A méretezni kívánt cél erőforrás neve |
| resourceType |Yes |A három támogatott érték: "Microsoft. classiccompute/tartománynév/bővítőhely/szerepkörök" – Cloud Service roles, "Microsoft. számítás/virtualmachinescalesets"-Virtual Machine Scale Sets és "Microsoft. Web/kiszolgálófarmok" – Web App |
| resourceId |Yes |A méretezni kívánt cél erőforrás Resource Manager-azonosítója |
| portalLink |Yes |Azure Portal hivatkozás a cél erőforrás Összegzés lapjára |
| oldCapacity |Yes |Az aktuális (régi) példányok száma, ha az autoskálázás skálázási műveletet vett igénybe |
| newCapacity |Yes |Az új példányszám az erőforrás méretezése |
| properties |No |Választható. <kulcs, érték> párok (például szótár <karakterlánc, karakterlánc>) készlete. A Properties (Tulajdonságok) mező nem kötelező. Egyéni felhasználói felületen vagy logikai alkalmazáson alapuló munkafolyamatban megadhatja azokat a kulcsokat és értékeket, amelyek átadhatók a hasznos adatok használatával. Ha az egyéni tulajdonságokat vissza szeretné adni a kimenő webhook-hívásra, akkor a webhook URI-ja (lekérdezési paraméterekként) is használható. |

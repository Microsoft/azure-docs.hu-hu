---
title: Resource Health riasztásokat létrehozó sablon
description: Programozott módon hozhat létre riasztásokat, amelyek értesítik, ha az Azure-erőforrások elérhetetlenné válnak.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 4f1cbe1e2d2c185906feb4ccba380cb31df864f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588202"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Resource Health-riasztások konfigurálása Resource Manager-sablonok használatával

Ez a cikk bemutatja, hogyan hozhat létre Resource Health tevékenység-naplózási riasztásokat programozott módon Azure Resource Manager sablonok és Azure PowerShell használatával.

Azure Resource Health folyamatosan tájékoztat az Azure-erőforrások aktuális és korábbi állapotáról. Az Azure Resource Health-riasztások segítségével közel valós időben értesülhet arról, ha az erőforrások állapotában változás áll be. Resource Health riasztások létrehozása programozott módon lehetővé teszi a felhasználók számára a riasztások tömeges létrehozását és testreszabását.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oldalon található utasítások követéséhez előre be kell állítania néhány dolgot:

1. Telepítenie kell a [Azure PowerShell modult](/powershell/azure/install-az-ps)
2. [Létre kell hoznia vagy újra kell használnia egy](../azure-monitor/alerts/action-groups.md) , az értesítésre konfigurált műveleti csoportot.

## <a name="instructions"></a>Utasítások
1. A PowerShell használatával jelentkezzen be az Azure-ba a fiókjával, és válassza ki a használni kívánt előfizetést

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > A használatával `Get-AzSubscription` listázhatja azokat az előfizetéseket, amelyekhez hozzáfér.

2. A műveleti csoport teljes Azure Resource Manager-AZONOSÍTÓjának megkeresése és mentése

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Hozzon létre és mentsen egy Resource Manager-sablont a Resource Health riasztásokhoz `resourcehealthalert.json` ([lásd az alábbi részleteket](#resource-manager-template-options-for-resource-health-alerts))

4. Új Azure Resource Manager központi telepítés létrehozása a sablon használatával

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. A rendszer kérni fogja, hogy írja be a riasztás nevét és a műveleti csoport erőforrás-AZONOSÍTÓját, amelyet korábban másolt:

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. Ha minden sikeresen működött, megerősítő lesz a PowerShellben

    ```output
    DeploymentName          : ExampleDeployment
    ResourceGroupName       : <resourceGroup>
    ProvisioningState       : Succeeded
    Timestamp               : 11/8/2017 2:32:00 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                            Name                     Type       Value
                            ===============          =========  ==========
                            activityLogAlertName     String     <Alert Name>
                            activityLogAlertEnabled  Bool       True
                            actionGroupResourceId    String     /...

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Vegye figyelembe, hogy ha a folyamat teljes automatizálását tervezi, egyszerűen szerkessze a Resource Manager-sablont, hogy ne Kérdezzen rá az 5. lépésben szereplő értékekre.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Resource Manager-sablon beállításai Resource Health riasztásokhoz

Ez az alapsablon kiindulási pontként használható Resource Health riasztások létrehozásához. Ez a sablon írásnak megfelelően működik, és a rendszer aláírja Önt, hogy riasztásokat kapjon az összes újonnan aktivált erőforrás-állapot eseményeiről az előfizetés összes erőforrása esetében.

> A cikk alján egy összetettebb riasztási sablont is tartalmaz, amely a Resource Health riasztások esetében az ehhez a sablonhoz viszonyítva növelheti a jelet a zaj arányának.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Azonban az ehhez hasonló széles körű riasztás általában nem ajánlott. Ismerje meg, hogyan szűkítheti a riasztást, hogy az alább látható eseményekre koncentráljon.

### <a name="adjusting-the-alert-scope"></a>A riasztás hatókörének módosítása

Resource Health riasztások beállítható úgy, hogy három különböző hatókörben figyelje az eseményeket:

 * Előfizetés szintje
 * Erőforráscsoport szintje
 * Erőforrás szintje

A riasztási sablon az előfizetés szintjén van konfigurálva, de ha úgy szeretné konfigurálni a riasztást, hogy csak bizonyos erőforrásokról vagy erőforrás-csoportokon belüli erőforrásokról kapjon értesítést, egyszerűen módosítania kell a `scopes` fenti sablon szakaszát.

Erőforráscsoport-szintű hatókör esetén a hatókörök szakasznak az alábbihoz hasonlóan kell kinéznie:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

Az erőforrás-szintű hatókörhöz hasonlóan a hatókör szakasznak kell kinéznie:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Például: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Nyissa meg az Azure Portalt, és tekintse meg az URL-címet, amikor megtekinti az Azure-erőforrást a karakterlánc beszerzéséhez.

### <a name="adjusting-the-resource-types-which-alert-you"></a>A riasztást igénylő erőforrástípusok módosítása

Az előfizetés vagy az erőforráscsoport szintjén lévő riasztások különböző típusú erőforrásokkal rendelkezhetnek. Ha korlátozni szeretné a riasztásokat, hogy csak az erőforrástípusok bizonyos részhalmazára érkezzenek, megadhatja, hogy a `condition` sablon szakaszában a következőhöz hasonló legyen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Itt a burkoló segítségével `anyOf` engedélyezheti, hogy az erőforrás-állapot riasztása megfeleljen az általunk megadott feltételeknek, ami lehetővé teszi, hogy a riasztások adott típusú erőforrásokra legyenek érvényesek.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>A Resource Health riasztási események módosítása
Ha az erőforrások bekerülnek az állapotba, akkor a következő állapotot képviselő szakaszok egy sorozatán keresztül haladnak:,, `Active` `In Progress` `Updated` és `Resolved` .

Előfordulhat, hogy csak akkor szeretne értesítést kapni, ha egy erőforrás nem megfelelő állapotba kerül, ebben az esetben a riasztást úgy kell konfigurálni, hogy csak akkor kapjon értesítést, ha a `status` `Active` . Ha azonban más fázisokban is értesítést szeretne kapni, az alábbihoz hasonló adatokat is hozzáadhat:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Ha az állapotadatok mind a négy fázisa esetében értesítést szeretne kapni, ezt a feltételt egyszerre távolíthatja el, a riasztás pedig a tulajdonságtól függetlenül értesíti Önt `status` .

> [!NOTE]
> Minden "anyOf" szakasz csak egyetlen mezőtípus-értéket tartalmazhat.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>A Resource Health riasztások módosítása az "ismeretlen" események elkerüléséhez

Azure Resource Health az erőforrások legújabb állapotát a tesztelési futók használatával folyamatosan felügyelheti. A kapcsolódó jelentett állapotok a következők: "elérhető", "nem érhető el" és "csökkentett teljesítményű". Azonban olyan helyzetekben, amikor a futó és az Azure-erőforrás nem tud kommunikálni, "ismeretlen" állapotot jelent az erőforrás, és az "aktív" állapotú eseménynek számít.

Ha azonban egy erőforrás jelentése "ismeretlen", az állapota valószínűleg nem módosult az utolsó pontos jelentés óta. Ha el szeretné távolítani a riasztásokat az "ismeretlen" eseményekről, megadhatja azt a következő sablonban:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

Ebben a példában csak olyan eseményekről értesítünk, amelyekben az aktuális és az előző állapot nem "Unknown". Ez a változás hasznos lehet, ha a riasztásokat közvetlenül a mobiltelefonjára vagy e-mail-címére küldik. 

Vegye figyelembe, hogy a currentHealthStatus és a previousHealthStatus tulajdonság bizonyos eseményeknél null értékű lehet. Ha például egy frissített esemény következik be, valószínű, hogy az erőforrás állapota az utolsó jelentés óta nem módosult, csak a további eseményekre vonatkozó információk érhetők el (például ok). Ezért a fenti záradék használatával bizonyos riasztások nem indíthatók el, mert a Properties. currentHealthStatus és a Properties. previousHealthStatus értékek NULL értékre lesznek állítva.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>A riasztás módosítása a felhasználó által kezdeményezett események elkerüléséhez

Resource Health eseményeket a platform által kezdeményezett és a felhasználó által kezdeményezett események indíthatják el. Előfordulhat, hogy csak akkor küldjön értesítést, ha az Azure platform okozza az állapotfigyelő eseményt.

Egyszerűen beállíthatja, hogy a riasztás csak az ilyen típusú események szűrésére legyen használható:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Vegye figyelembe, hogy előfordulhat, hogy az OK mező értéke null értékű bizonyos eseményekben. Ez azt eredményezi, hogy az állapot átalakulása zajlik (például elérhető a nem érhető el), és az eseményt azonnal naplózza az értesítési késések elkerülése érdekében. Ezért a fenti záradék használatával a riasztás nem indítható el, mert a Properties. záradék tulajdonság értéke NULL lesz.

## <a name="complete-resource-health-alert-template"></a>Resource Health riasztási sablon befejezése

Az előző szakaszban leírt különböző beállítások használatával itt látható egy minta sablon, amely a jel és a zaj arányának maximalizálására van konfigurálva. Vegye figyelembe azokat a figyelmeztetéseket, amelyek felett a currentHealthStatus, a previousHealthStatus és az OK tulajdonság értéke null lehet bizonyos eseményekben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Azonban tudni fogja, hogy milyen konfigurációk érvényesek az Ön számára, ezért használja a dokumentációban ismertetett eszközöket a saját testreszabásának elvégzéséhez.

## <a name="next-steps"></a>Következő lépések

További információ a Resource Healthról:
-  [Azure Resource Health áttekintése](Resource-health-overview.md)
-  [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapotellenőrzések](resource-health-checks-resource-types.md)


Service Health riasztások létrehozása:
-  [Riasztások konfigurálása Service Healthhoz](./alerts-activity-log-service-notifications-portal.md) 
-  [Azure Activity log esemény sémája](../azure-monitor/essentials/activity-log-schema.md)

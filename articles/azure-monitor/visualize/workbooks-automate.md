---
title: Azure Monitor munkafüzetek és Azure Resource Manager sablonok
description: Egyszerűsítse a komplex jelentéskészítést az előre összeállított és egyéni paraméteres Azure Monitor Azure Resource Manager-sablonokkal üzembe helyezett munkafüzetek használatával
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d4aac17ca823f4eaa0f52ab260b1daca3f52f94
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109747"
---
# <a name="programmatically-manage-workbooks"></a>Munkafüzetek programozott kezelése

Az erőforrás-tulajdonosok lehetőségük van a munkafüzetek programozott módon történő létrehozására és kezelésére Resource Manager-sablonok használatával.

Ez olyan esetekben lehet hasznos, mint például:
* Szervezeti vagy tartomány-specifikus elemzési jelentések üzembe helyezése az erőforrás-telepítésekkel együtt. Előfordulhat például, hogy üzembe helyezi az új alkalmazások vagy virtuális gépek szervezeti teljesítményével és meghibásodásával kapcsolatos munkafüzeteket.
* Szabványos jelentések vagy irányítópultok üzembe helyezése meglévő erőforrások munkafüzetek használatával.

A munkafüzet a kívánt alcsoportban/erőforrás-csoportban és a Resource Manager-sablonokban megadott tartalommal lesz létrehozva.

A munkafüzet-erőforrások két típusa felügyelhető programozott módon:
* [Munkafüzet-sablonok](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Munkafüzet példányai](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Munkafüzet-sablon üzembe helyezéséhez Azure Resource Manager sablon

1. Nyisson meg egy olyan munkafüzetet, amelyet programozott módon kíván üzembe helyezni.
2. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
3. Nyissa meg a _speciális szerkesztő_ az _</>_ eszköztáron található gomb használatával.
4. Győződjön meg arról, hogy a katalógus _sablonja_ lapon van.

    ![Galéria sablonja lap](./media/workbooks-automate/gallery-template.png)
1. Másolja a JSON-t a katalógus sablonjában a vágólapra.
2. Alább látható egy példa Azure Resource Manager sablon, amely egy munkafüzet-sablont helyez üzembe Azure Monitor munkafüzet-gyűjteménybe. Illessze be a helyére másolt JSON-t `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . [Itt](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)található egy hivatkozás Azure Resource Manager sablon, amely létrehoz egy munkafüzet-sablont.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. Az `galleries` objektumban töltse ki az `name` értékeket a és a `category` kulcsokkal. További információ a [paraméterekről](#parameters) a következő szakaszban.
2. Telepítse ezt a Azure Resource Manager sablont a [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), a [parancssori felület](../../azure-resource-manager/templates/deploy-cli.md), a [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)stb. használatával.
3. Nyissa meg a Azure Portalt, és navigáljon a Azure Resource Manager sablonban kiválasztott munkafüzet-gyűjteményhez. A példa sablonban navigáljon a Azure Monitor munkafüzet-gyűjteményhez:
    1. Nyissa meg a Azure Portal, és navigáljon a Azure Monitor
    2. Megnyitás `Workbooks` a tartalomjegyzékből
    3. Keresse meg a sablont a katalógusban a kategória alatt `Deployed Templates` (a következő lesz a lila elemek egyike).

### <a name="parameters"></a>Paraméterek

|Paraméterek                |Magyarázat                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | A munkafüzet-sablon erőforrásának neve Azure Resource Managerban.                                  |
|`type`                    | Mindig Microsoft. bepillantást/workbooktemplates                                                            |
| `location`               | Az Azure-beli hely, ahová a munkafüzetet létre kívánja hozni.                                               |
| `apiVersion`             | 2019-10-17 előzetes verzió                                                                                     |
| `type`                   | Mindig Microsoft. bepillantást/workbooktemplates                                                            |
| `galleries`              | Azon galériák gyűjteménye, amelyekben ez a munkafüzet-sablon látható.                                                |
| `gallery.name`           | A munkafüzet sablonjának rövid neve a katalógusban.                                             |
| `gallery.category`       | A gyűjteményben lévő csoport a sablon elhelyezéséhez.                                                     |
| `gallery.order`          | Egy szám, amely egy kategórián belüli sablon megjelenítését határozza meg a katalógusban. Az alacsonyabb sorrend nagyobb prioritást jelent. |
| `gallery.resourceType`   | A gyűjteményhez tartozó erőforrástípus. Ez általában az erőforrás típusának megfelelő karakterlánc (például Microsoft. operationalinsights/workspaces). |
|`gallery.type`            | Ez egy olyan egyedi kulcs, amely megkülönbözteti a katalógust egy adott erőforrás-típuson belül. Application Insights például a `workbook` `tsg` különböző munkafüzet-gyűjteményekhez tartozó típusokkal és azokkal. |

### <a name="galleries"></a>Katalógusok

| Katalógus                                        | Erőforrás típusa                                       | Munkafüzet típusa |
|:-----------------------------------------------|:----------------------------------------------------|:--------------|
| Munkafüzetek Azure Monitor                     | `Azure Monitor`                                     | `workbook`    |
| VM-bepillantást Azure Monitor                   | `Azure Monitor`                                     | `vm-insights` |
| Munkafüzetek a log Analytics-munkaterületen           | `microsoft.operationalinsights/workspaces`          | `workbook`    |
| Munkafüzetek Application Insights              | `microsoft.insights/components`                     | `workbook`    |
| Hibaelhárítási útmutatók a Application Insights | `microsoft.insights/components`                     | `tsg`         |
| Használat Application Insights                  | `microsoft.insights/components`                     | `usage`       |
| Munkafüzetek a Kubernetes szolgáltatásban                | `Microsoft.ContainerService/managedClusters`        | `workbook`    |
| Erőforráscsoportok munkafüzetek                   | `microsoft.resources/subscriptions/resourcegroups`  | `workbook`    |
| Munkafüzetek Azure Active Directory            | `microsoft.aadiam/tenant`                           | `workbook`    |
| Virtuálisgép-bepillantást a Virtual Machines szolgáltatásban                | `microsoft.compute/virtualmachines`                 | `insights`    |
| A virtuálisgép-méretezési csoportokban található virtuális gépek      | `microsoft.compute/virtualmachinescalesets`         | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Munkafüzet-példány üzembe helyezésének Azure Resource Manager sablonja

1. Nyisson meg egy olyan munkafüzetet, amelyet programozott módon szeretne üzembe helyezni.
2. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
3. Nyissa meg a _speciális szerkesztő_ az _</>_ eszköztáron található gomb használatával.
4. A szerkesztőben váltson a _sablon típusára_ _Resource Manager-sablonra_.
5. A létrehozáshoz használt Resource Manager-sablon megjelenik a szerkesztőben. Másolja a tartalmat, és használja a as-t, vagy egyesítse egy nagyobb sablonnal, amely a célként megadott erőforrást is telepíti.

    ![A Resource Manager-sablonnak a munkafüzet felhasználói felületéről való beszerzését bemutató kép](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Minta Azure Resource Manager sablon
Ez a sablon bemutatja, hogyan helyezhet üzembe egy egyszerű munkafüzetet, amely a következőt jeleníti meg: "Hello World!"
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Sablon paraméterei

| Paraméter | Magyarázat |
| :------------- |:-------------|
| `workbookDisplayName` | A katalógusban vagy a mentett listán használt munkafüzet rövid neve. Egyedinek kell lennie az erőforráscsoport és a forrás hatókörében |
| `workbookType` | A katalógus, amelyben a munkafüzet megjelenik. A támogatott értékek a következők: munkafüzet, `tsg` , Azure monitor stb. |
| `workbookSourceId` | Annak az erőforrás-példánynak az azonosítója, amelyhez a munkafüzet társítva lesz. Az új munkafüzet megjelenik az adott erőforrás-példánnyal kapcsolatban – például az erőforrás tartalomjegyzékében a _munkafüzetben_. Ha azt szeretné, hogy a munkafüzet megjelenjen a Azure Monitor munkafüzet-katalógusában, használja az erőforrás-azonosító helyett _Azure monitor_ karakterláncot. |
| `workbookId` | A munkafüzet-példány egyedi GUID azonosítója. A _[newGuid ()]_ használatával hozzon létre automatikusan egy új GUID azonosítót. |
| `kind` | Annak megadására szolgál, hogy a létrehozott munkafüzet megosztott vagy privát. A megosztott munkafüzetekhez és a _felhasználókhoz_ _megosztott_ értéket használjon magánjellegűként. |
| `location` | Az Azure-beli hely, ahol a munkafüzet létre lesz hozva. A _[resourceGroup (). location]_ használatával hozza létre ugyanazt a helyet, mint az erőforráscsoportot. |
| `serializedData` | A munkafüzetben használandó tartalmat vagy adattartalmat tartalmazza. Az érték beszerzéséhez használja a munkafüzetek felhasználói felületének Resource Manager-sablonját. |

### <a name="workbook-types"></a>Munkafüzetek típusai
A munkafüzetek típusai határozzák meg, hogy melyik munkafüzet-gyűjteménybe írja be az új munkafüzet-példányt. A lehetőségek a következők:

| Típus | Gyűjtemény helye |
| :------------- |:-------------|
| `workbook` | A legtöbb jelentésben használt alapértelmezett érték, beleértve a munkafüzetek Application Insights, Azure Monitor stb. gyűjteményét.  |
| `tsg` | A hibaelhárítási útmutatók gyűjteménye Application Insights |
| `usage` | A _további_ katalógus a _használat_ alatt Application Insights |

### <a name="limitations"></a>Korlátozások
Technikai okokból ez a mechanizmus nem használható munkafüzet-példányok létrehozására a Application Insights _munkafüzetek_ galériájában. Dolgozunk ennek a korlátozásnak a kezelésén. Addig is javasoljuk, hogy a hibaelhárítási útmutató (workbookType: `tsg` ) használatával Application Insights kapcsolódó munkafüzeteket telepítsen.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használják a munkafüzetek az új Azure Monitor a [tárolási élményhez](../insights/storage-insights-overview.md).

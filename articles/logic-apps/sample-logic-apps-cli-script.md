---
title: Azure CLI-példaszkret – logikai alkalmazás létrehozása
description: Példaszk szkript logikai alkalmazás létrehozásához az Azure CLI Logic Apps bővítményével.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: b81d9b4a637965dd103d8fa89305424686a0c72c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789914"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI-példaszkret – logikai alkalmazás létrehozása

Ez a szkript létrehoz egy logikai mintaalkalmazást az [Azure CLI-Logic Apps ,](/cli/azure/ext/logic/logic)( ) `az logic` használatával. A logikai alkalmazások Azure CLI-alapú létrehozásának és kezelésének részletes útmutatóját az Azure [CLI-hez Logic Apps rövid útmutatójában láthatja.](quickstart-logic-apps-azure-cli.md)

> [!WARNING]
> Az Azure CLI Logic Apps bővítmény jelenleg *kísérleti,* és nem fedezi az *ügyfélszolgálat.* Ezt a CLI-bővítményt körültekintően használja, különösen akkor, ha éles környezetben használja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A helyi számítógépen telepített [Azure CLI.](/cli/azure/install-azure-cli)
* A [Logic Apps telepített Azure CLI-bővítmény.](/cli/azure/azure-cli-extensions-list) A bővítmény telepítéséhez használja a következő parancsot: `az extension add --name logic`
* A [logikai alkalmazás](quickstart-logic-apps-azure-cli.md#workflow-definition) munkafolyamat-definíciója. Ennek a JSON-fájlnak a [munkafolyamat-definíciós nyelvi sémát kell követnie.](logic-apps-workflow-definition-language.md)
* Api-kapcsolat egy e-mail-fiókhoz egy támogatott Logic Apps [összekötőn keresztül,](../connectors/apis-list.md) ugyanabban az erőforráscsoportban, mint a logikai alkalmazás. Ez a példa az [Office 365](../connectors/connectors-create-api-office365-outlook.md) Outlook-összekötőt használja, de más összekötőket is használhat, például a [Outlook.com.](../connectors/connectors-create-api-outlook.md)

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

A környezet ellenőrzése a kezdés előtt:

* Jelentkezzen be a Azure Portal és a futtatásával ellenőrizze, hogy az előfizetés `az login` aktív-e.

* Ellenőrizze az Azure CLI verzióját egy terminál- vagy parancsablakban a parancs `az --version` futtatásával. A legújabb verzióért tekintse meg a [legújabb kibocsátási megjegyzéseket.](/cli/azure/release-notes-azure-cli)

  * Ha nem a legújabb verzióval működik, frissítse a telepítést az operációs rendszer vagy a platform telepítési [útmutatója alapján.](/cli/azure/install-azure-cli)

### <a name="sample-workflow-explanation"></a>Példa a munkafolyamat magyarázatára

Ez a munkafolyamat-definíciós példafájl ugyanazt az alapszintű logikai alkalmazást hozza létre, mint Logic Apps [gyorsútmutató a Azure Portal.](quickstart-create-first-logic-app-workflow.md) 

Ez a minta-munkafolyamat: 

1. A logikai alkalmazás `$schema` sémáját határozza meg.

1. Meghatározza a logikai alkalmazás eseményindítóját az eseményindítók `triggers` listájában. Az eseményindító 3 óránként ismétlődik ( `recurrence` ). A műveletek akkor aktiválódnak, ha új hírcsatornaelem () van közzétéve a `When_a_feed_item_is_published` megadott RSS-hírcsatornához ( `feedUrl` ).

1. Definiál egy műveletet a logikai alkalmazáshoz a műveletek `actions` listájában. A művelet egy e-mailt ( ) küld Microsoft 365 a művelet bemenetének törzs szakaszában () megadott RSS-hírcsatornaelemek `Send_an_email_(V2)` `body` részleteivel ( `inputs` ).

## <a name="sample-workflow-definition"></a>Munkafolyamat-definíció mintája

A mintaszkprogram futtatása előtt létre kell hoznia egy [minta-munkafolyamat-definíciót.](#prerequisites)

1. Hozzon létre egy JSON-fájlt `testDefinition.json` a számítógépén. 

1. Másolja a következő tartalmat a JSON-fájlba: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Frissítse a helyőrző értékeket a saját adataival:

    1. Cserélje le a helyőrző e-mail-címét ( `"To": "test@example.com"` ). Olyan e-mail-címet kell használnia, amely kompatibilis a Logic Apps összekötőivel. További információkért lásd az [előfeltételeket.](#prerequisites)

    1. Ha az Office 365 Outlook-összekötő helyett más e-mail-összekötőt használ, cserélje le az összekötő további adatait.

    1. Cserélje le a kapcsolati azonosítók ( és ) helyőrző előfizetési értékeit ( ) a connections paraméter ( ) alatt a `00000000-0000-0000-0000-000000000000` `connectionId` saját `id` `$connections` előfizetési értékeire.

1. Mentse a módosításokat.

## <a name="sample-script"></a>Példaszkript

> [!NOTE]
> Ez a minta a rendszerhéjhoz `bash` van írva. Ha ezt a mintát egy másik rendszerhéjban szeretné futtatni, például Windows PowerShell vagy parancssorban, előfordulhat, hogy módosítania kell a szkriptet.

A mintaparancsprogram futtatása előtt futtassa ezt a parancsot az Azure-hoz való csatlakozáshoz:

```azurecli-interactive

az login

```

Ezután lépjen ahhoz a könyvtárhoz, amelyben létrehozta a munkafolyamat-definíciót. Ha például létrehozta a munkafolyamat-definíció JSON-fájlját az asztalon:

```azurecli

cd ~/Desktop

```

Ezután futtassa ezt a szkriptet egy logikai alkalmazás létrehozásához. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Miután végzett a mintaparancsprogram használatával, futtassa a következő parancsot az erőforráscsoport és az összes beágyazott erőforrás eltávolításához, beleértve a logikai alkalmazást is.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a példaszkprogram a következő parancsokat használja egy új erőforráscsoport és logikai alkalmazás létrehozásához.

| Parancs | Jegyzetek |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amelyben a logikai alkalmazás erőforrásai tárolva vannak. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Létrehoz egy logikai alkalmazást a paraméterben meghatározott munkafolyamat `--definition` alapján. |
| [`az group delete`](/cli/azure/vm/extension) | Töröl egy erőforráscsoportot és annak összes beágyazott erőforrását. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-ről az [Azure CLI dokumentációjában talál további információt.](/cli/azure/)

További cli-Logic Apps a [Microsoft kódmintaböngészőjében.](/samples/browse/?products=azure-logic-apps)

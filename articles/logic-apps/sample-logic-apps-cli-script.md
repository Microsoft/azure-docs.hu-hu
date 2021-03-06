---
title: Azure CLI parancsfájl-minta – logikai alkalmazás létrehozása
description: Parancsfájl egy logikai alkalmazás létrehozásához az Azure CLI Logic Apps bővítménnyel.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: a4553ceee482fb232e9ab56deca650be93f9dc6b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218043"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI parancsfájl-minta – logikai alkalmazás létrehozása

Ez a szkript egy minta logikai alkalmazást hoz létre az [Azure CLI Logic apps bővítménnyel](/cli/azure/ext/logic/logic)( `az logic` ). A Logic apps Azure CLI-n keresztüli létrehozásával és kezelésével kapcsolatos részletes útmutatóért tekintse meg az [Azure cli Logic apps](quickstart-logic-apps-azure-cli.md)gyors üzembe helyezési útmutatóját.

> [!WARNING]
> Az Azure CLI Logic Apps bővítménye jelenleg *kísérleti jellegű* , és *nem vonatkozik az ügyfélszolgálatra*. Ezt a CLI-bővítményt körültekintően használhatja, különösen akkor, ha a bővítményt éles környezetben használja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az [Azure CLI](/cli/azure/install-azure-cli) telepítve van a helyi számítógépen.
* A számítógépre telepített [Logic apps Azure CLI-bővítmény](/cli/azure/azure-cli-extensions-list) . A bővítmény telepítéséhez használja a következő parancsot: `az extension add --name logic`
* [Munkafolyamat-definíció](quickstart-logic-apps-azure-cli.md#workflow-definition) a logikai alkalmazáshoz. A JSON-fájlnak meg kell felelnie a [munkafolyamat-definíció nyelvi sémájának](logic-apps-workflow-definition-language.md).
* API-csatlakozás egy e-mail-fiókhoz egy támogatott [Logic apps-összekötőn](../connectors/apis-list.md) keresztül ugyanabban az erőforráscsoportban, mint a logikai alkalmazás. Ez a példa az [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) Connectort használja, de más összekötőket is használhat, mint például a [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

A Kezdés előtt ellenőrizze a környezetet:

* Jelentkezzen be a Azure Portalba, és győződjön meg arról, hogy az előfizetése aktív a futtatásával `az login` .

* A futtatásával tekintse meg az Azure CLI-verziót egy terminálon vagy parancsablakban `az --version` . A legújabb verzióra vonatkozó megjegyzések a [legújabb kiadási megjegyzésekben](/cli/azure/release-notes-azure-cli)találhatók.

  * Ha nem rendelkezik a legújabb verzióval, frissítse a telepítést az [operációs rendszer vagy a platform telepítési útmutatóját](/cli/azure/install-azure-cli)követve.

### <a name="sample-workflow-explanation"></a>Példa a munkafolyamat magyarázatára

Ebben a példában a munkafolyamat-definíciós fájl ugyanazt az alapszintű logikai alkalmazást hozza létre, mint a [Azure Portal Logic apps](quickstart-create-first-logic-app-workflow.md). 

Ez a minta munkafolyamat: 

1. Meghatározza a `$schema` logikai alkalmazás sémáját.

1. Meghatározza a logikai alkalmazás eseményindítóját az eseményindítók listájában `triggers` . Az trigger ( `recurrence` ) 3 óránként ismétlődik. A műveletek a megadott RSS-hírcsatorna () új hírcsatorna-elemek közzétételekor () lesznek aktiválva `When_a_feed_item_is_published` `feedUrl` .

1. Definiál egy műveletet a logikai alkalmazáshoz a műveletek listájában `actions` . A művelet elküld egy e-mailt ( `Send_an_email_(V2)` ) Microsoft 365 az RSS-hírcsatorna elemeinek részleteit a művelet bemenetei () törzs szakaszában () megadott módon `body` `inputs` .

## <a name="sample-workflow-definition"></a>Példa a munkafolyamat-definícióra

A minta parancsfájl futtatása előtt létre kell hoznia egy minta munkafolyamat- [definíciót](#prerequisites).

1. Hozzon létre egy JSON-fájlt a `testDefinition.json` számítógépen. 

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

1. Módosítsa a helyőrző értékeket a saját adataival:

    1. Cserélje le a helyőrző e-mail-címét ( `"To": "test@example.com"` ). Logic Apps összekötővel kompatibilis e-mail-címet kell használnia. További információ: [Előfeltételek](#prerequisites).

    1. Ha más e-mail-összekötőt használ, mint az Office 365 Outlook Connector, cserélje le a további összekötők részleteit.

    1. Cserélje le a kapcsolati azonosítók (és) helyőrző előfizetési értékeit a `00000000-0000-0000-0000-000000000000` `connectionId` `id` saját előfizetés értékeire a Connections paraméterben ( `$connections` ).

1. Mentse a módosításokat.

## <a name="sample-script"></a>Példaszkript

> [!NOTE]
> Ez a minta a `bash` rendszerhéjhoz van írva. Ha ezt a mintát egy másik rendszerhéjban szeretné futtatni, például a Windows PowerShellben vagy a parancssorban, lehetséges, hogy módosítania kell a parancsfájlt.

A minta parancsfájl futtatása előtt futtassa ezt a parancsot az Azure-hoz való kapcsolódáshoz:

```azurecli-interactive

az login

```

Ezután Navigáljon arra a könyvtárra, amelyben létrehozta a munkafolyamat-definíciót. Ha például létrehozta a munkafolyamat-definíció JSON-fájlját az asztalon:

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

A minta parancsfájl használatának befejezése után a következő parancs futtatásával távolítsa el az erőforráscsoportot és az összes beágyazott erőforrását, beleértve a logikai alkalmazást is.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a minta parancsfájl a következő parancsokat használja egy új erőforráscsoport és logikai alkalmazás létrehozásához.

| Parancs | Jegyzetek |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amelyben a logikai alkalmazás erőforrásai tárolódnak. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Létrehoz egy logikai alkalmazást a paraméterben meghatározott munkafolyamat alapján `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Töröl egy erőforráscsoportot és annak összes beágyazott erőforrását. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információkért tekintse meg az [Azure CLI dokumentációját](/cli/azure/).

További Logic Apps CLI-szkripteket a [Microsoft Code Samples böngészőben](/samples/browse/?products=azure-logic-apps)talál.

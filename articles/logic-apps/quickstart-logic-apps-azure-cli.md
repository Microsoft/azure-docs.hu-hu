---
title: Logikai alkalmazások létrehozása és kezelése az Azure CLI-vel
description: Az Azure CLI használatával létrehozhat egy logikai alkalmazást, majd kezelheti a logikai alkalmazást a következő műveletek, például a lista, a Megjelenítés (Get), a frissítés és a törlés használatával.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: bc172fd1702addf8f4e34094452a779b09320a4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033374"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Gyors útmutató: logikai alkalmazások létrehozása és kezelése az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre és kezelhet Logic apps-t az [Azure CLI Logic apps bővítmény](/cli/azure/ext/logic/logic) ( `az logic` ) használatával. A parancssorból létrehozhat egy logikai alkalmazást a logikai alkalmazások munkafolyamat-definíciójának JSON-fájljának használatával. Ezután kezelheti a logikai alkalmazást olyan műveletek futtatásával, mint `list` `show` a, ( `get` ), `update` és `delete` a parancssorból.

> [!WARNING]
> Az Azure CLI Logic Apps bővítménye jelenleg *kísérleti jellegű* , és *nem vonatkozik az ügyfélszolgálatra*. Ezt a CLI-bővítményt körültekintően használhatja, különösen akkor, ha a bővítményt éles környezetben használja.

Ha még nem ismeri a Logic Appst, megtudhatja, hogyan hozhatja létre az első logikai alkalmazásait [a Azure Portal](quickstart-create-first-logic-app-workflow.md), a [Visual Studióban](quickstart-create-logic-apps-with-visual-studio.md)és a [Visual Studio Code-ban](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az [Azure CLI](/cli/azure/install-azure-cli) telepítve van a helyi számítógépen.
* A számítógépre telepített [Logic apps Azure CLI-bővítmény](/cli/azure/azure-cli-extensions-list) . A bővítmény telepítéséhez használja a következő parancsot: `az extension add --name logic`
* Egy [Azure-erőforráscsoport](#example---create-resource-group) , amelyben létre kívánja hozni a logikai alkalmazást.

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

A Kezdés előtt ellenőrizze a környezetet:

* Jelentkezzen be a Azure Portalba, és győződjön meg arról, hogy az előfizetése aktív a futtatásával `az login` .
* A futtatásával tekintse meg az Azure CLI-verziót egy terminálon vagy parancsablakban `az --version` . A legújabb verzióra vonatkozó megjegyzések a [legújabb kiadási megjegyzésekben](/cli/azure/release-notes-azure-cli?tabs=azure-cli)találhatók.
  * Ha nem rendelkezik a legújabb verzióval, frissítse a telepítést az [operációs rendszer vagy a platform telepítési útmutatóját](/cli/azure/install-azure-cli)követve.

### <a name="example---create-resource-group"></a>Példa – erőforráscsoport létrehozása

Ha még nem rendelkezik erőforráscsoporthoz a logikai alkalmazáshoz, hozza létre a csoportot a paranccsal `az group create` . A következő parancs például létrehoz egy nevű erőforráscsoportot `testResourceGroup` a helyen `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

A kimenet a `provisioningState` következőképpen jelenik meg, mint az `Succeeded` erőforráscsoport sikeres létrehozásakor:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Munkafolyamat-definíció

[Új logikai alkalmazás létrehozása](#create-logic-apps-from-cli) vagy [meglévő logikai alkalmazás](#update-logic-apps-from-cli) Azure CLI használatával történő frissítése előtt a logikai alkalmazáshoz munkafolyamat-definíció szükséges. A Azure Portal a logikai alkalmazás mögöttes munkafolyamat-definícióját JSON formátumban tekintheti meg, ha a **tervező** nézetből a **kód nézetre** vált.

A logikai alkalmazás létrehozásához vagy frissítéséhez szükséges parancsok futtatásakor a munkafolyamat-definíció kötelező paraméterként () lesz feltöltve `--definition` . Létre kell hoznia a munkafolyamat-definíciót JSON-fájlként, amely követi a [munkafolyamat-definíció nyelvi sémáját](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-cli"></a>Logikai alkalmazások létrehozása a parancssori felületről

Az Azure CLI-ből létrehozhat egy Logic apps-munkafolyamatot a [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) definícióhoz tartozó JSON-fájllal.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

A parancsnak tartalmaznia kell a következő [szükséges paramétereket](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters):

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Munkafolyamat-definíció | `--definition` | Egy JSON-fájl a logikai alkalmazás [munkafolyamatának definíciójában](#workflow-definition). |
| Hely | `--location -l` | Az az Azure-régió, amelyben a logikai alkalmazás található. |
| Name | `--name -n` | A logikai alkalmazás neve. A név csak betűket, számokat, kötőjeleket () `-` , aláhúzásokat ( `_` ), zárójeleket ( `()` ) és pontokat ( `.` ) tartalmazhat. A névnek egyedinek kell lennie régiók között is. |
| Erőforráscsoport neve | `--resource-group -g` | Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) , amelyben létre szeretné hozni a logikai alkalmazást. [Hozzon létre egy erőforráscsoportot](#example---create-resource-group) , mielőtt elkezdené, ha még nem rendelkezik ilyennel a logikai alkalmazáshoz. |

További [választható paramétereket](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) is beállíthat a logikai alkalmazás hozzáférés-vezérlésének, végpontjának, integrációs fiókjának, integrációs szolgáltatási környezetének, állapotának és erőforrás-címkének konfigurálásához.

### <a name="example---create-logic-app"></a>Példa – logikai alkalmazás létrehozása

Ebben a példában egy nevű munkafolyamat `testLogicApp` jön létre az erőforráscsoport `testResourceGroup` helyén `westus` . A JSON-fájl `testDefinition.json` tartalmazza a munkafolyamat-definíciót.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

A munkafolyamat sikeres létrehozása után a CLI az új munkafolyamat-definíció JSON-kódját jeleníti meg. Ha a munkafolyamat létrehozása sikertelen, tekintse meg a [lehetséges hibák listáját](#errors).

## <a name="update-logic-apps-from-cli"></a>Logikai alkalmazások frissítése a parancssori felületről

A Logic apps munkafolyamatát az Azure CLI-vel is frissítheti a paranccsal [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) .

A parancsnak ugyanazokat a [paramétereket](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) kell tartalmaznia, mint a [logikai alkalmazások létrehozásakor](#create-logic-apps-from-cli). A logikai alkalmazások létrehozásakor ugyanazok a [választható paraméterek](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) is hozzáadhatók.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Példa – logikai alkalmazás frissítése

Ebben a példában az [előző szakaszban létrehozott minta munkafolyamat](#example---create-logic-app) egy másik JSON-definíciós fájl használatára frissül, `newTestDefinition.json` és két erőforrás-címkét ad hozzá, `testTag1` valamint `testTag2` a Leírás értékeit.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

A munkafolyamat sikeres frissítésekor a CLI megjeleníti a logikai alkalmazás frissített munkafolyamat-definícióját. Ha a frissítés sikertelen, tekintse meg a [lehetséges hibák listáját](#errors).

## <a name="delete-logic-apps-from-cli"></a>Logikai alkalmazások törlése a parancssori felületről

A Logic app-munkafolyamatot a paranccsal törölheti az Azure CLI-ből [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) .

A parancsnak tartalmaznia kell a következő [szükséges paramétereket](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters):

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Név | `--name -n` | A logikai alkalmazás neve. |
| Erőforráscsoport neve | `-resource-group -g` | Az az erőforráscsoport, amelyben a logikai alkalmazás található. |

A megerősítési kérések kihagyásához [választható paramétert](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) is hozzáadhat `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

A CLI ezután megkéri, hogy erősítse meg a logikai alkalmazás törlését. A megerősítési kérést a parancs opcionális paraméterével is kihagyhatja `--yes -y` .

```azurecli

Are you sure you want to perform this operation? (y/n):

```

A logikai alkalmazás törlését a parancssori felületén [található logikai alkalmazások listázásával](#list-logic-apps-in-cli), vagy a logikai alkalmazások a Azure Portalban való megtekintésével ellenőrizheti.

### <a name="example---delete-logic-app"></a>Példa – logikai alkalmazás törlése

Ebben a példában az [előző szakaszban létrehozott minta munkafolyamatot](#example---create-logic-app) törli a rendszer.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Miután válaszolt a megerősítő kérésre `y` , a rendszer törli a logikai alkalmazást.

## <a name="show-logic-apps-in-cli"></a>Logikai alkalmazások megjelenítése a CLI-ben

Az paranccsal egy adott Logic apps-munkafolyamatot szerezhet be [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

A parancsnak tartalmaznia kell a következő [szükséges paramétereket](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters)

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Név | `--name -n` | A logikai alkalmazás neve. |
| Erőforráscsoport neve | `--resource-group -g` | Azon erőforráscsoport neve, amelyben a logikai alkalmazás található. |

### <a name="example---get-logic-app"></a>Példa – logikai alkalmazás beolvasása

Ebben a példában az erőforráscsoport logikai alkalmazását a `testLogicApp` rendszer a `testResourceGroup` hibakereséshez teljes naplókkal adja vissza.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Logikai alkalmazások listázása a CLI-ben

A Logic apps-t előfizetéssel is listázhatja az parancs használatával [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) . Ez a parancs a Logic apps-munkafolyamatok JSON-kódját adja vissza.

Az eredményeket a következő [választható paraméterek](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters)alapján szűrheti:

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Erőforráscsoport neve | `--resource-group -g` | Azon erőforráscsoport neve, amelynek alapján szűrni kívánja az eredményeket. |
| Elemek száma | `--top` | Az eredményekben szereplő elemek száma. |
| Szűrő | `--filter` | A listában használt szűrő típusa. Szűrheti az állapot ( `State` ), a trigger ( `Trigger` ) és a hivatkozott erőforrás () azonosítóját `ReferencedResourceId` . |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Példa – logikai alkalmazások listázása

Ebben a példában az erőforráscsoport összes engedélyezett munkafolyamata `testResourceGroup` ASCII-táblázatos formátumban lesz visszaadva.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Hibák

A következő hiba azt jelzi, hogy nincs telepítve a Azure Logic Apps CLI-bővítmény. Kövesse az előfeltételek című témakörben ismertetett lépéseket a [Logic apps bővítmény telepítéséhez](#prerequisites) a számítógépen.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

A következő hiba azt jelezheti, hogy a munkafolyamat-definíció feltöltésének elérési útja helytelen.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Globális paraméterek

A parancsokkal a következő opcionális globális Azure CLI-paramétereket használhatja `az logic` :

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Kimeneti formátum | `--output -o` | Módosítsa a [kimeneti formátumot](/cli/azure/format-output-azure-cli) az alapértelmezett JSON-fájlból. |
| Csak hibák megjelenítése | `--only-show-errors` | Figyelmeztetések mellőzése és csak hibák megjelenítése. |
| Részletes | `--verbose` | Részletes naplók megjelenítése. |
| Hibakeresés | `--debug` | Megjeleníti az összes hibakeresési naplót. |
| Súgó üzenet | `--help -h` | Súgó megjelenítése párbeszédpanel. |
| Lekérdezés | `--query` | JMESPath lekérdezési karakterlánc beállítása a JSON-kimenethez. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információkért tekintse meg az [Azure CLI dokumentációját](/cli/azure/).

További Logic Apps CLI-szkripteket a [Microsoft Code Samples böngészőben](/samples/browse/?products=azure-logic-apps)talál.

Ezután létrehozhat egy példaként szolgáló alkalmazás-logikát az Azure CLI-n keresztül egy minta parancsfájl és egy munkafolyamat-definíció használatával.

> [!div class="nextstepaction"]
> [Logikai alkalmazás létrehozása minta parancsfájl használatával](sample-logic-apps-cli-script.md).

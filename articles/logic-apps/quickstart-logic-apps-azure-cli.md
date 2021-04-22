---
title: Logikai alkalmazások létrehozása és kezelése az Azure CLI használatával
description: Az Azure CLI használatával hozzon létre egy logikai alkalmazást, majd kezelje a logikai alkalmazást olyan műveletekkel, mint a lista, a show (get), a frissítés és a törlés.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: afc39673a30f5c99455696c7a075cb1a6a33ecd1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875502"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Rövid útmutató: Logikai alkalmazások létrehozása és kezelése az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre és kezelhet logikai alkalmazásokat az [Azure CLI Logic Apps bővítmény () használatával.](/cli/azure/logic) `az logic` A parancssorból létrehozhat egy logikai alkalmazást a logikai alkalmazás munkafolyamat-definíciójának JSON-fájljának használatával. Ezután a parancssorból futtathat olyan műveleteket, mint a , ( ), és a logikai `list` `show` `get` `update` `delete` alkalmazás.

> [!WARNING]
> Az Azure CLI Logic Apps bővítmény jelenleg *kísérleti,* és nem fedezi az *ügyfélszolgálat.* Ezt a CLI-bővítményt körültekintően használja, különösen akkor, ha éles környezetben használja.

Ha most ismerkedik a Logic Apps, azt is megtudhatja, hogyan hozhatja létre első logikai alkalmazásait a [Azure Portal](quickstart-create-first-logic-app-workflow.md), a [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)és a [Visual Studio használatával.](quickstart-create-logic-apps-visual-studio-code.md)

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A helyi számítógépen telepített [Azure CLI.](/cli/azure/install-azure-cli)
* A [Logic Apps telepített Azure CLI-bővítmény.](/cli/azure/azure-cli-extensions-list) A bővítmény telepítéséhez használja a következő parancsot: `az extension add --name logic`
* Egy [Azure-erőforráscsoport,](#example---create-resource-group) amelyben létrehozza a logikai alkalmazást.

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

A környezet ellenőrzése a kezdés előtt:

* Jelentkezzen be a Azure Portal és a futtatásával ellenőrizze, hogy az előfizetés `az login` aktív-e.
* Ellenőrizze az Azure CLI verzióját egy terminál- vagy parancsablakban a parancs `az --version` futtatásával. A legújabb verzióért tekintse meg a [legújabb kibocsátási megjegyzéseket.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Ha nem a legújabb verzióval működik, frissítse a telepítést az operációs rendszer vagy a platform telepítési [útmutatója alapján.](/cli/azure/install-azure-cli)

### <a name="example---create-resource-group"></a>Példa – erőforráscsoport létrehozása

Ha még nem rendelkezik erőforráscsoporttal a logikai alkalmazáshoz, hozza létre a csoportot a `az group create` paranccsal. A következő parancs például létrehoz egy nevű erőforráscsoportot a `testResourceGroup` `westus` helyen.

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

A kimenetben az `provisioningState` `Succeeded` látható, amikor az erőforráscsoport sikeresen létrejött:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Munkafolyamat-definíció

Mielőtt létrehoz egy új [](#update-logic-apps-from-cli) [logikai alkalmazást,](#create-logic-apps-from-cli) vagy frissít egy meglévő logikai alkalmazást az Azure CLI használatával, szüksége lesz egy munkafolyamat-definícióra a logikai alkalmazáshoz. A Azure Portal nézetben megtekintheti a logikai alkalmazás mögöttes munkafolyamat-definícióját JSON formátumban, ha a **Tervező** nézetről a **Kódnézetre vált.**

Amikor futtatja a parancsokat a logikai alkalmazás létrehozásához vagy frissítéséhez, a munkafolyamat definíciója kötelező paraméterként () lesz `--definition` feltöltve. A munkafolyamat-definíciót A munkafolyamat-definíciós nyelv sémáját követő JSON-fájlként [kell létrehoznia.](./logic-apps-workflow-definition-language.md)

## <a name="create-logic-apps-from-cli"></a>Logikai alkalmazások létrehozása a CLI-ről

Logikaialkalmazás-munkafolyamatot az Azure CLI-vel, a definíció JSON-fájllal való használatával [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) hozhat létre a paranccsal.

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

A parancsnak a következő kötelező [paramétereket kell tartalmaznia:](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters)

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Munkafolyamat-definíció | `--definition` | Egy JSON-fájl a logikai alkalmazás [munkafolyamat-definícióját használva.](#workflow-definition) |
| Hely | `--location -l` | Az Az Azure-régió, amelyben a logikai alkalmazás található. |
| Name | `--name -n` | A logikai alkalmazás neve. A név csak betűket, számokat, kötőjeleket ( ), aláhúzásjeleket ( ), zárójeleket ( ) és `-` `_` `()` pontokat ( `.` ) tartalmazhat. A névnek a régiók között is egyedinek kell lennie. |
| Erőforráscsoport neve | `--resource-group -g` | Az [Az Azure-erőforráscsoport,](../azure-resource-manager/management/overview.md) amelyben létre szeretné hozni a logikai alkalmazást. [Mielőtt hozzákezd,](#example---create-resource-group) hozzon létre egy erőforráscsoportot, ha még nem rendelkezik erőforráscsoporttal a logikai alkalmazáshoz. |

További választható paramétereket is tartalmazhat a logikai alkalmazás hozzáférés-vezérlésének, végpontjainak, integrációs fiókjának, integrációs szolgáltatási környezetének, állapotának és erőforráscímkéinek konfigurálása érdekében. [](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters)

### <a name="example---create-logic-app"></a>Példa – logikai alkalmazás létrehozása

Ebben a példában egy nevű munkafolyamatot hozunk létre a helyen `testLogicApp` található `testResourceGroup` erőforráscsoportban. `westus` A JSON-fájl `testDefinition.json` tartalmazza a munkafolyamat definícióját.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

A munkafolyamat sikeres létrehozása után a CLI megjeleníti az új munkafolyamat-definíció JSON-kódját. Ha a munkafolyamat létrehozása sikertelen, tekintse meg [a lehetséges hibák listáját.](#errors)

## <a name="update-logic-apps-from-cli"></a>Logikai alkalmazások frissítése a CLI-ről

A logikai alkalmazás munkafolyamatát az Azure CLI-ről is frissítheti a [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) paranccsal.

A parancsnak tartalmaznia kell a [logikai](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters) alkalmazás létrehozásakor szükséges [paramétereket.](#create-logic-apps-from-cli) A választható paramétereket a logikai [alkalmazások](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) létrehozásakor is hozzáadhatja.

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

Ebben a példában az előző szakaszban létrehozott minta-munkafolyamatot egy másik JSON-definíciós fájl () használatára frissítjük, és hozzáadunk két erőforráscímkét és egy [](#example---create-logic-app) `newTestDefinition.json` `testTag1` `testTag2` leírásértéket.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

A munkafolyamat sikeres frissítése után a CLI megjeleníti a logikai alkalmazás frissített munkafolyamat-definícióját. Ha a frissítés sikertelen, tekintse meg [a lehetséges hibák listáját.](#errors)

## <a name="delete-logic-apps-from-cli"></a>Logikai alkalmazások törlése a CLI-ről

A logikai alkalmazás munkafolyamatát az paranccsal törölheti az Azure CLI-ről. [`az logic workflow delete`](/cli/azure/logic/workflow#az_logic_workflow_delete)

A parancsnak a következő kötelező [paramétereket kell tartalmaznia:](/cli/azure/logic/workflow#az_logic_workflow_delete-required-parameters)

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Név | `--name -n` | A logikai alkalmazás neve. |
| Erőforráscsoport neve | `-resource-group -g` | Az erőforráscsoport, amelyben a logikai alkalmazás található. |

A megerősítési kérések kihagyása egy [opcionális paraméterrel](/cli/azure/logic/workflow#az_logic_workflow_delete-optional-parameters) is lehetséges. `--yes -y`

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

A parancssori felület ezután arra kéri, hogy erősítse meg a logikai alkalmazás törlését. A nem kötelező paraméterrel a paranccsal kihagyhatja a `--yes -y` megerősítési kérést.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

A logikai alkalmazások törlését a [](#list-logic-apps-in-cli)CLI-beli listázással, vagy a logikai alkalmazások megtekintésével erősítheti meg a Azure Portal.

### <a name="example---delete-logic-app"></a>Példa – logikai alkalmazás törlése

Ebben a példában az előző szakaszban [létrehozott minta-munkafolyamatot](#example---create-logic-app) töröljük.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Miután válaszol a megerősítési kérésre a következővel: , a `y` logikai alkalmazás törlődik.

## <a name="show-logic-apps-in-cli"></a>Logikai alkalmazások megjelenítése a CLI-ban

Az paranccsal lekért egy adott logikaialkalmazás-munkafolyamatot. [`az logic workflow show`](/cli/azure/logic/workflow#az_logic_workflow_show)

```azurecli

az logic workflow show --name
                       --resource-group

```

A parancsnak a következő kötelező paramétereket [kell tartalmaznia:](/cli/azure/logic/workflow#az_logic_workflow_show-required-parameters)

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Név | `--name -n` | A logikai alkalmazás neve. |
| Erőforráscsoport neve | `--resource-group -g` | Annak az erőforráscsoportnak a neve, amelyben a logikai alkalmazás található. |

### <a name="example---get-logic-app"></a>Példa – logikai alkalmazás lekérte

Ebben a példában az erőforráscsoportban található logikai alkalmazás teljes naplókat ad vissza a `testLogicApp` `testResourceGroup` hibakereséshez.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Logikai alkalmazások listája a CLI-ban

A logikai alkalmazásokat előfizetés szerint listhatja a [`az logic workflow list`](/cli/azure/logic/workflow#az_logic_workflow_list) paranccsal. Ez a parancs visszaadja a logikai alkalmazások munkafolyamatához szükséges JSON-kódot.

Az eredményeket a következő választható paraméterekkel [szűrheti:](/cli/azure/logic/workflow#az_logic_workflow_list-optional-parameters)

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Erőforráscsoport neve | `--resource-group -g` | Annak az erőforráscsoportnak a neve, amely alapján szűrni szeretné az eredményeket. |
| Elemek száma | `--top` | Az eredményekben szereplő elemek száma. |
| Szűrő | `--filter` | A listában használt szűrő típusa. Állapot ( ), trigger ( ) és a hivatkozott erőforrás azonosítója ( ) alapján `State` `Trigger` `ReferencedResourceId` szűrhet. |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Példa – logikai alkalmazások listába sorolás

Ebben a példában az erőforráscsoportban található összes engedélyezett munkafolyamat `testResourceGroup` ASCII-táblaformátumban lesz visszaadva.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Hibák

A következő hiba azt jelzi, Azure Logic Apps CLI-bővítmény nincs telepítve. Az előfeltételként szükséges lépéseket követve telepítse a Logic Apps [bővítményt](#prerequisites) a számítógépre.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

A következő hiba azt jelezheti, hogy a munkafolyamat-definíció feltöltésére vonatkozó fájl elérési útja helytelen.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Globális paraméterek

A következő választható globális Azure CLI-paramétereket használhatja a `az logic` parancsokkal:

| Paraméter | Érték | Leírás |
| --------- | ----- | ----------- |
| Kimeneti formátum | `--output -o` | Módosítsa a [kimeneti formátumot](/cli/azure/format-output-azure-cli) az alapértelmezett JSON-fájlról. |
| Csak a hibák megjelenítése | `--only-show-errors` | A figyelmeztetések mellőzése és csak a hibák megjelenítése. |
| Részletes | `--verbose` | Részletes naplók megjelenítése. |
| Hibakeresés | `--debug` | Megjeleníti az összes hibakeresési naplót. |
| Súgóüzenet | `--help -h` | Súgó párbeszédpanel megjelenítése. |
| Lekérdezés | `--query` | Állítson be egy JMESPath lekérdezési sztringet a JSON-kimenethez. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-ről az [Azure CLI dokumentációjában](/cli/azure/)talál további információt.

További CLI-Logic Apps a [Microsoft kódmintaböngészőjében talál.](/samples/browse/?products=azure-logic-apps)

Következő lépésként létrehozhat egy példa alkalmazáslogikát az Azure CLI-t használva egy mintaszk szkript és egy munkafolyamat-definíció használatával.

> [!div class="nextstepaction"]
> [Hozzon létre egy logikai alkalmazást a példaszk szkript használatával.](sample-logic-apps-cli-script.md)

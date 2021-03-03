---
title: Erőforrások üzembe helyezése az Azure CLI-vel és sablonnal
description: Erőforrások üzembe helyezése az Azure-ban a Azure Resource Manager és az Azure CLI használatával. Az erőforrások egy Resource Manager-sablonban vagy egy bicep-fájlban vannak meghatározva.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 547b860869738f3cfe12d6a22262829ef132a671
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741123"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel

Ez a cikk azt ismerteti, hogyan használható az Azure CLI Azure Resource Manager-sablonokkal (ARM-sablonokkal) vagy a bicep-fájllal az erőforrások Azure-beli üzembe helyezéséhez. Ha nem ismeri az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmakat, tekintse meg a [sablonok üzembe helyezésének áttekintése](overview.md) vagy a [bicep – áttekintés](bicep-overview.md)című témakört.

Az üzembe helyezési parancsok az Azure CLI 2.2.0-as verziójában változtak. A cikkben szereplő példákhoz az Azure CLI 2.2.0 vagy újabb verziójára van szükség.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Ha nincs telepítve az Azure CLI, használhatja a Azure Cloud Shell. További információ: [ARM-sablonok üzembe helyezése Azure Cloud Shellról](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy erőforráscsoport, egy előfizetés, egy felügyeleti csoport vagy egy bérlő számára is megcélozhatja. A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

* Egy **erőforráscsoporthoz** való üzembe helyezéshez használja [az az Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* Egy **előfizetésre** való üzembe helyezéshez használja az [az Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

* Egy **felügyeleti csoportba** való központi telepítéshez használja az [az Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

* Egy **bérlőn** való üzembe helyezéshez használja az [az Deployment bérlő Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  A bérlői szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforrások létrehozása a bérlő szintjén](deploy-to-tenant.md).

Minden hatókör esetében a sablont vagy a bicep-fájlt telepítő felhasználónak rendelkeznie kell az erőforrások létrehozásához szükséges engedélyekkel.

## <a name="deploy-local-template-or-bicep-file"></a>Helyi sablon vagy bicep-fájl üzembe helyezése

A sablont telepítheti a helyi gépről, vagy egy külső tárolóból. Ez a szakasz egy helyi sablon központi telepítését ismerteti.

Ha nem létező erőforráscsoporthoz végez üzembe helyezést, hozzon létre egy erőforráscsoportot. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. A név nem végződhet ponttal.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Helyi sablon vagy bicep-fájl üzembe helyezéséhez használja a `--template-file` paramétert a telepítési parancsban. Az alábbi példa azt is bemutatja, hogyan lehet megadni egy paraméter értékét.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza a következő eredményt:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

> [!NOTE]
> Az Azure CLI jelenleg nem támogatja a bicep-fájlok eltávolítását.

Az ARM-sablonok helyi gépen való tárolása helyett érdemes lehet őket külső helyen tárolni. Sablonokat tárolhat egy olyan verziókövetési rendszer adattárában is, mint a GitHub. Tárolhatja azokat egy Azure-tárfiókban is, ahol megosztva érhető el a vállalat számára.

Ha nem létező erőforráscsoporthoz végez üzembe helyezést, hozzon létre egy erőforráscsoportot. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. A név nem végződhet ponttal.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Külső sablon üzembe helyezésére használja a `template-uri` paramétert.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Az előző példa egy nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb forgatókönyv esetében működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban a sablonhoz való hozzáférést szeretné kezelni, érdemes lehet a [sablonhoz tartozó specifikációkat](#deploy-template-spec)használni.

Ha olyan távoli csatolt sablonokat kíván üzembe helyezni, amelyek egy Storage-fiókban tárolt relatív elérési úttal rendelkeznek, a következő paranccsal `query-string` adhatja meg az SAS-tokent:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

További információ: [relatív elérési út használata csatolt sablonokhoz](./linked-templates.md#linked-template).

## <a name="deployment-name"></a>Központi telepítés neve

ARM-sablon telepítésekor megadhatja a központi telepítés nevét. Ez a név segítséget nyújt a központi telepítésnek a telepítési előzményekből való lekéréséhez. Ha nem adja meg a központi telepítés nevét, a rendszer a sablonfájl nevét használja. Ha például központilag telepít egy nevű sablont `azuredeploy.json` , és nem ad meg központi telepítési nevet, akkor a központi telepítés neve `azuredeploy` .

Minden alkalommal, amikor futtat egy központi telepítést, a rendszer egy bejegyzést ad hozzá az erőforráscsoport telepítési előzményeihez a központi telepítési névvel. Ha egy másik központi telepítést futtat, és ugyanazt a nevet adja, a korábbi bejegyzést a rendszer a jelenlegi telepítéssel helyettesíti. Ha az üzembe helyezési előzményekben egyedi bejegyzéseket kíván fenntartani, adjon meg minden egyes központi telepítést egyedi nevet.

Egyedi név létrehozásához véletlenszerű számot rendelhet hozzá.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Vagy adjon hozzá egy dátumérték értéket.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Ha egyazon erőforráscsoporthoz futtat egyidejű központi telepítéseket ugyanazzal a központi telepítési névvel, akkor a rendszer csak az utolsó telepítést hajtja végre. A rendszer a legutóbbi telepítés helyett minden olyan központi telepítést lecserél, amelynek a neve nem fejeződött be. Ha például egy nevű központi telepítést futtat, amely egy nevű `newStorage` Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage2` , csak egy Storage-fiókot telepít. Az eredményül kapott Storage-fiók neve `storage2` .

Ha azonban egy nevű központi telepítést futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage1` , és közvetlenül a befejezése után futtat egy másik nevű központi telepítést `newStorage` , amely telepíti a nevű Storage `storage2` -fiókot, akkor két Storage-fiókkal rendelkezik. Az egyik neve `storage1` , a másik pedig a neve `storage2` . Azonban csak egy bejegyzés szerepel az üzembe helyezési előzményekben.

Amikor egyedi nevet ad meg az egyes központi telepítésekhez, ütközés nélkül is futtathatja őket. Ha olyan nevű központi telepítést futtat `newStorage1` , amely egy nevű Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage2` , amely egy nevű Storage-fiókot telepít `storage2` , akkor két Storage-fiókkal és két bejegyzéssel rendelkezik az üzembe helyezési előzményekben.

Az egyidejű központi telepítésekkel való ütközések elkerülése érdekében, valamint a telepítési előzmények egyedi bejegyzéseinek biztosításához adjon egyedi nevet a központi telepítésnek.

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

> [!NOTE]
> Az Azure CLI jelenleg nem támogatja a sablonhoz tartozó specifikációk létrehozását a bicep-fájlok biztosításával. Azonban létrehozhat egy ARM-sablont vagy egy bicep-fájlt a [Microsoft. Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) erőforrással a sablon specifikációjának telepítéséhez. Íme egy [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).

Helyi vagy távoli sablon telepítése helyett hozzon létre egy [specifikációt](template-specs.md). A sablon spec egy ARM-sablont tartalmazó Azure-előfizetésben található erőforrás. Megkönnyíti a sablon biztonságos megosztását a szervezetben lévő felhasználókkal. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával hozzáférést biztosíthat a sablon specifikációjának. Ez a funkció jelenleg előzetes verzióban érhető el.

Az alábbi példák bemutatják, hogyan hozhat létre és helyezhet üzembe egy sablon-specifikációt.

Először hozza létre a sablon specifikációját az ARM-sablon biztosításával.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Ezután szerezze be a sablon specifikációjának AZONOSÍTÓját, és telepítse azt.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

További információ: [Azure Resource Manager template specs (előzetes verzió)](template-specs.md).

## <a name="preview-changes"></a>Előnézeti változások

A sablon üzembe helyezése előtt megtekintheti, hogy a sablon milyen módosításokat hajt végre a környezetben. A [mi-if művelettel](template-deploy-what-if.md) ellenőrizheti, hogy a sablon elvégzi-e a várt módosításokat. Mi a teendő, ha a hibát is ellenőrzi a sablonban.

## <a name="parameters"></a>Paraméterek

A paraméterek értékének átadásához használhat beágyazott paramétereket vagy egy paraméter-fájlt.

### <a name="inline-parameters"></a>Beágyazott paraméterek

A beágyazott paraméterek átadásához adja meg az értékeket a következőben: `parameters` . Ha például egy karakterláncot és tömböt szeretne átadni egy bash-rendszerhéjban található sablonra, használja a következőt:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Ha az Azure CLI-t a Windows parancssorral (CMD) vagy a PowerShell-lel használja, adja át a tömböt a következő formátumban: `exampleArray="['value1','value2']"` .

Emellett beolvashatja a fájl tartalmát, és megadhatja a tartalmat beágyazott paraméterként.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Egy paraméter értékének beolvasása a fájlból hasznos lehet, ha konfigurációs értékeket kell megadnia. Megadhat például [egy Linux rendszerű virtuális gép számára a Cloud-init értékeket](../../virtual-machines/linux/using-cloud-init.md).

A _arrayContent.js_ formátuma:

```json
[
    "value1",
    "value2"
]
```

Egy objektum átadásához, például címkék beállításához használja a JSON-t. Előfordulhat például, hogy a sablon egy olyan paramétert tartalmaz, amely a következőhöz hasonló:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

Ebben az esetben egy JSON-karakterláncot adhat át a paraméternek a következő bash-parancsfájlban látható módon történő beállításához:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Használjon idézőjeleket a JSON-körben, amelyeket át szeretne adni az objektumba.

### <a name="parameter-files"></a>Paraméter fájljai

Ahelyett, hogy a paramétereket beágyazott értékekként adná át a szkriptben, egyszerűbbnek találhatja egy olyan JSON-fájl használatát, amely tartalmazza a paraméterértékeket. A paraméter fájljának helyi fájlnak kell lennie. A külső paraméterek fájljai nem támogatottak az Azure CLI-vel. Mindkét ARM-sablon és a bicep-fájl JSON-paramétereket használ.

A paraméterfájlról a [Resource Manager-paraméterfájl létrehozása](parameter-files.md) című cikk nyújt további információkat.

Helyi paraméterérték `@` átadásához a paranccsal adjon meg egy _storage.parameters.js_ nevű helyi fájlt.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Kiterjesztett JSON-formátum kezelése

Ha többsoros karakterláncokkal vagy megjegyzésekkel rendelkező sablont szeretne üzembe helyezni az Azure CLI-vel, a 2.3.0 vagy régebbi verziójával, akkor a kapcsolót kell használnia `--handle-extended-json-format` .  Például:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Következő lépések

* Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
* Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
* Ha meg szeretné tudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című részt.
* A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).

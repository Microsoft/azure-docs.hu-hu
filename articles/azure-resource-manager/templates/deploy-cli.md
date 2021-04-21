---
title: Erőforrások üzembe helyezése az Azure CLI és a sablon használatával
description: Az Azure Resource Manager és az Azure CLI használatával helyezhet üzembe erőforrásokat az Azure-ban. Az erőforrások egy sablonban Resource Manager Bicep-fájlban vannak definiálva.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: f616a40f2683268f0cc26314fcc88ecca23bdbcf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782062"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-val

Ez a cikk azt ismerteti, hogyan használhatja az Azure CLI-t Azure Resource Manager sablonokkal (ARM-sablonokkal) vagy Bicep-fájlokkal az erőforrások Azure-ban való üzembe helyezéséhez. Ha nem ismeri az Azure-megoldások üzembe helyezésének és kezelésének [](overview.md) fogalmait, tekintse meg a sablonok üzembe helyezésének áttekintését vagy a [Bicep áttekintését.](bicep-overview.md)

Az üzembe helyezési parancsok módosulnak az Azure CLI 2.2.0-s verziójában. A cikkben olvasható példákhoz az Azure CLI 2.2.0-s vagy újabb verziójára van szükség. Bicep-fájlok üzembe helyezéséhez az [Azure CLI 2.20.0-s](/cli/azure/install-azure-cli)vagy újabb verziójára lesz szükség.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Ha nincs telepítve az Azure CLI, használhatja a Azure Cloud Shell. További információ: [ARM-sablonok üzembe helyezése a Azure Cloud Shell.](deploy-cloud-shell.md)

## <a name="deployment-scope"></a>Üzembe helyezés hatóköre

Az üzembe helyezést erőforráscsoportra, előfizetésre, felügyeleti csoportra vagy bérlőre célozhatja. Az üzembe helyezés hatókörétől függően különböző parancsokat kell használnia.

* Az erőforráscsoportba **való üzembe helyezéshez használja** az az deployment group create [et:](/cli/azure/deployment/group#az_deployment_group_create)

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* Az előfizetésben való **üzembe helyezéshez** használja [az az deployment sub create lehetőséget:](/cli/azure/deployment/sub#az_deployment_sub_create)

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  További információ az előfizetés-szintű üzemelő példányokkal kapcsolatban: Erőforráscsoportok és erőforrások létrehozása [előfizetési szinten.](deploy-to-subscription.md)

* Felügyeleti csoportban való **üzembe helyezéshez** használja az az deployment mg create használhatja a [következőt:](/cli/azure/deployment/mg#az_deployment_mg_create)

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  A felügyeleti csoport szintű központi telepítésekkel kapcsolatos további információkért lásd: [Erőforrások létrehozása a felügyeleti csoport szintjén.](deploy-to-management-group.md)

* A bérlőben való **üzembe helyezéshez** használja [az az deployment tenant create et:](/cli/azure/deployment/tenant#az_deployment_tenant_create)

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  A bérlői szintű üzemelő példányokkal kapcsolatos további információkért lásd: [Erőforrások létrehozása bérlői szinten.](deploy-to-tenant.md)

A sablont üzembe helyező felhasználónak vagy a Bicep-fájlnak minden hatókörben rendelkeznie kell az erőforrások létrehozásához szükséges engedélyekkel.

## <a name="deploy-local-template-or-bicep-file"></a>Helyi sablon vagy Bicep-fájl üzembe helyezése

Üzembe helyezhet egy sablont a helyi gépről, vagy egy külsőleg tárolt sablont. Ez a szakasz a helyi sablonok üzembe helyezését ismerteti.

Ha nem létező erőforráscsoportban helyez üzembe helyezést, hozza létre az erőforráscsoportot. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakterből állhat. A név nem végződhet pontra.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Helyi sablon vagy Bicep-fájl üzembe helyezéséhez használja a `--template-file` paramétert az üzembe helyezési parancsban. Az alábbi példa azt is bemutatja, hogyan állíthat be paraméterértéket.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredményt:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

> [!NOTE]
> Az Azure CLI jelenleg nem támogatja a távoli Bicep-fájlok üzembe helyezését. A [Bicep CLI használatával](./bicep-install.md#development-environment) fordítsa le a Bicep-fájlt egy JSON-sablonba, majd töltse be a JSON-fájlt a távoli helyre.

Ahelyett, hogy ARM-sablonokat tárol a helyi gépen, inkább külső helyen szeretné tárolni őket. Sablonokat tárolhat egy olyan verziókövetési rendszer adattárában is, mint a GitHub. Tárolhatja azokat egy Azure-tárfiókban is, ahol megosztva érhető el a vállalat számára.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Ha nem létező erőforráscsoportban helyez üzembe helyezést, hozza létre az erőforráscsoportot. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakterből állhat. A név nem végződhet pontra.

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

Az előző példához nyilvánosan elérhető URI szükséges a sablonhoz, amely a legtöbb esetben működik, mivel a sablonnak nem szabad bizalmas adatokat tartalmaznia. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja meg ezt az értéket biztonságos paraméterként. Ha azonban kezelni szeretné a sablonhoz való hozzáférést, fontolja meg a [sablonsokkal való hozzáférést.](#deploy-template-spec)

Távoli csatolt sablonok tárfiókban tárolt relatív elérési úttal való üzembe helyezéséhez használja az sas-jogkivonat `query-string` megadását:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

További információ: Relatív elérési [út használata csatolt sablonokhoz.](./linked-templates.md#linked-template)

## <a name="deployment-name"></a>Üzemelő példány neve

ARM-sablon üzembe helyezésekor nevet adhat az üzemelő példánynak. Ez a név segíthet lekérni az üzembe helyezést az üzembe helyezési előzményekből. Ha nem adja meg az üzemelő példány nevét, a rendszer a sablonfájl nevét használja. Ha például üzembe helyez egy nevű sablont, és nem ad meg üzembe helyezési nevet, az üzemelő `azuredeploy.json` példány neve `azuredeploy` lesz.

Minden üzembe helyezés futtatásakor a rendszer hozzáad egy bejegyzést az erőforráscsoport üzembe helyezési előzményeihez az üzembe helyezés nevével. Ha egy másik központi telepítést futtat, és ugyanazt a nevet adja neki, a korábbi bejegyzés az aktuális üzemelő példányra lesz lecserélve. Ha egyedi bejegyzéseket szeretne karbantartani az üzembe helyezési előzményekben, minden üzemelő példánynak adjon egyedi nevet.

Egyedi név létrehozásához véletlenszerű számot rendelhet hozzá.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Vagy adjon hozzá egy dátumértéket.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Ha egyidejű üzembe helyezéseket futtat ugyanazon az erőforráscsoporton ugyanazokkal az üzembe helyezési névvel, csak az utolsó üzembe helyezés lesz befejezve. Az azonos nevű, még nem befejezett üzemelő példányokat a legutóbbi üzemelő példány váltja fel. Ha például egy nevű üzemelő példány futtatásával üzembe helyez egy nevű tárfiókot, és ezzel egyidejűleg futtat egy nevű másik üzemelő példányt, amely egy nevű tárfiókot helyez üzembe, akkor csak egy tárfiókot kell `newStorage` `storage1` üzembe `newStorage` `storage2` helyeznie. Az eredményül kapott tárfiók neve `storage2` .

Ha azonban egy nevű központi telepítést futtat, amely egy nevű tárfiókot helyez üzembe, és közvetlenül a befejezése után egy másik, nevű üzemelő példányot futtat, amely egy nevű tárfiókot helyez üzembe, akkor két tárfiókkal `newStorage` fog kell majd rendelkezésre `storage1` `newStorage` `storage2` állni. Az egyik neve `storage1` , a másik neve `storage2` . Az üzembe helyezési előzményekben azonban csak egy bejegyzés van.

Ha minden üzemelő példányhoz egyedi nevet ad meg, ütközés nélkül futtathatja őket egyidejűleg. Ha egy nevű központi telepítést futtat, amely egy nevű tárfiókot helyez üzembe, és ezzel egyidejűleg futtat egy nevű másik üzemelő példányt, amely egy nevű tárfiókot helyez üzembe, akkor két tárfiókkal és két bejegyzéssel fog a telepítési `newStorage1` `storage1` `newStorage2` `storage2` előzményekben.

Az egyidejű központi telepítésekkel való ütközések elkerülése és az egyedi bejegyzéseknek az üzembe helyezési előzményekben való biztosítása érdekében minden üzemelő példánynak egyedi nevet adjon.

## <a name="deploy-template-spec"></a>Sablonsokdiméter üzembe helyezése

> [!NOTE]
> Az Azure CLI jelenleg nem támogatja a sablonsokok Bicep-fájlok használatával való létrehozását. Azonban létrehozhat egy Bicep-fájlt a [Microsoft.Resources/templateSpecs erőforrással](/azure/templates/microsoft.resources/templatespecs) a sablon specifikációjának üzembe helyezéséhez. Példa a [következőre:](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).

Helyi vagy távoli sablon üzembe helyezése helyett létrehozhat egy [sablonsokaját.](template-specs.md) A sablon specifikációja egy arm-sablont tartalmazó erőforrás az Azure-előfizetésben. Megkönnyíti a sablon biztonságos megosztását a szervezet felhasználóival. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosít hozzáférést a sablon specifikációihoz. Ez a funkció jelenleg előzetes verzióban érhető el.

Az alábbi példák bemutatják, hogyan hozhat létre és helyezhet üzembe sablonsokaját.

Első lépésként hozza létre a sablon specifikációját az ARM-sablon meg biztosításával.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Ezután szerezze be a sablon specifikációjának azonosítóját, és telepítse azt.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

További információ: Azure Resource Manager [specifikációi (előzetes verzió).](template-specs.md)

## <a name="preview-changes"></a>Módosítások előnézete

A sablon üzembe helyezése előtt megtekintheti a sablon által a környezetben végrehajtott módosítások előnézetét. A [what-if művelettel ellenőrizheti,](template-deploy-what-if.md) hogy a sablon végrehajtotta-e a várt módosításokat. A What-if emellett ellenőrzi, hogy a sablon tartalmaz-e hibákat.

## <a name="parameters"></a>Paraméterek

Paraméterértékek átadása beágyazott paraméterekkel vagy paraméterfájllal is lehetséges.

### <a name="inline-parameters"></a>Beágyazott paraméterek

Beágyazott paramétereket úgy lehet átadni, hogy a értékeket a következőben adja `parameters` meg: . Ha például egy sztringet és tömböt ad át egy Bash-rendszerhéj sablonjának, használja a következőt:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Ha az Azure CLI-t Windows parancssorral (CMD) vagy PowerShell-sel használja, adja át a tömböt a következő formátumban: `exampleArray="['value1','value2']"` .

Le is kaphatja a fájl tartalmát, és meg is használhatja beágyazott paraméterként.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

A paraméterérték fájlból való lekért értéke akkor hasznos, ha konfigurációs értékeket kell adnia. Megadhatja például a [cloud-init értékét egy Linux rendszerű virtuális géphez.](../../virtual-machines/linux/using-cloud-init.md)

A _arrayContent.jsformátum a_ következő:

```json
[
    "value1",
    "value2"
]
```

Ha például egy objektumot ad át címkék beállításához, használja a JSON-t. A sablon tartalmazhat például egy ehhez hasonló paramétert:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

Ebben az esetben átadhat egy JSON-sztringet, hogy beállítsa a paramétert az alábbi Bash-szkriptben látható módon:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Használjon idézőjeleket a objektumnak átadni kívánt JSON körül.

### <a name="parameter-files"></a>Paraméterfájlok

Ahelyett, hogy a paramétereket beágyazott értékekként adná át a szkriptben, egyszerűbbnek találhatja egy olyan JSON-fájl használatát, amely tartalmazza a paraméterértékeket. A paraméterfájlnak helyi fájlnak kell lennie. Az Azure CLI nem támogatja a külső paraméterfájlokat. Az ARM-sablon és a Bicep-fájl is JSON-paraméterfájlokat használ.

A paraméterfájlról a [Resource Manager-paraméterfájl létrehozása](parameter-files.md) című cikk nyújt további információkat.

Helyi paraméterfájl megadásához a használatával adjon meg egy nevű helyistorage.parameters.js`@` _a következőn:_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Kiterjesztett JSON-formátumok kezelés

Ha többsoros sztringeket vagy megjegyzéseket használó sablont az Azure CLI 2.3.0-s vagy régebbi verziójával helyez üzembe, a kapcsolót kell `--handle-extended-json-format` használnia.  Például:

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

* Ha hibaüzenetet kap, a sikeres üzembe helyezésre való visszaállításhoz tekintse meg a Visszaállítás hiba esetén a [sikeres üzembe helyezéshez útmutatót.](rollback-on-error.md)
* Az erőforráscsoportban található, de a sablonban nem definiált erőforrások kezeléséhez tekintse meg az üzembe helyezési módokról [Azure Resource Manager útmutatót.](deployment-modes.md)
* A paraméterek sablonban való meghatározásának mikéntjére az ARM-sablonok szerkezetének és szintaxisának a bemutatja a [következőt:](template-syntax.md).
* A gyakori üzembe helyezési hibák elhárításával kapcsolatos tippekért lásd: Az Azure-beli üzembehelyezés gyakori [hibáinak elhárítása a Azure Resource Manager.](common-deployment-errors.md)

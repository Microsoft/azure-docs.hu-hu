---
title: Erőforrások üzembe helyezése a PowerShell és a sablon használatával
description: Erőforrások üzembe helyezése az Azure-ban Azure Resource Manager és Azure PowerShell használatával. Az erőforrások egy Resource Manager-sablonban vagy egy bicep-fájlban vannak meghatározva.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: e1d2197064b94d428855bbb36aadbb5eb88d4032
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950858"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell

Ez a cikk azt ismerteti, hogyan Azure PowerShell használhatók az erőforrások az Azure-ban való üzembe helyezéséhez Azure Resource Manager sablonokkal (ARM-sablonokkal) vagy bicep-fájlokkal. Ha nem ismeri az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmakat, tekintse meg a [sablonok üzembe helyezésének áttekintése](overview.md) vagy a [bicep – áttekintés](bicep-overview.md)című témakört.

A bicep-fájlok telepítéséhez [Azure PowerShell 5.6.0 vagy újabb verzióra](/powershell/azure/install-az-ps)van szükség.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy telepítendő sablonra. Ha még nem rendelkezik ilyennel, töltsön le és mentsen egy [példaként szolgáló sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) az Azure Gyorsindítás sablonok tárházból. A cikkben használt helyi fájl neve _C:\MyTemplates\azuredeploy.js_.

Telepítenie kell a Azure PowerShellt, és kapcsolódnia kell az Azure-hoz:

- **Telepítse Azure PowerShell parancsmagokat a helyi számítógépen.** További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).
- **Kapcsolódjon az Azure [-](/powershell/module/az.accounts/connect-azaccount)hoz a AZAccount használatával**. Ha több Azure-előfizetéssel rendelkezik, előfordulhat, hogy a [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)parancsot is futtatnia kell. További információ: [több Azure-előfizetés használata](/powershell/azure/manage-subscriptions-azureps).

Ha nincs telepítve a PowerShell, használhatja a Azure Cloud Shell. További információ: [ARM-sablonok üzembe helyezése Azure Cloud Shellról](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy erőforráscsoport, egy előfizetés, egy felügyeleti csoport vagy egy bérlő számára is megcélozhatja. A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

- Egy **erőforráscsoporthoz** való üzembe helyezéshez használja a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template-or-bicep>
  ```

- Egy **előfizetésre** való üzembe helyezéshez használja a [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment) , amely a `New-AzDeployment` parancsmag aliasa:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

- Ha **felügyeleti csoportba** kíván üzembe helyezni, használja a [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

- Egy **bérlőn** való üzembe helyezéshez használja a [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  A bérlői szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforrások létrehozása a bérlő szintjén](deploy-to-tenant.md).

Minden hatókör esetében a sablont telepítő felhasználónak rendelkeznie kell az erőforrások létrehozásához szükséges engedélyekkel.

## <a name="deployment-name"></a>Központi telepítés neve

ARM-sablon telepítésekor megadhatja a központi telepítés nevét. Ez a név segítséget nyújt a központi telepítésnek a telepítési előzményekből való lekéréséhez. Ha nem adja meg a központi telepítés nevét, a rendszer a sablonfájl nevét használja. Ha például központilag telepít egy nevű sablont `azuredeploy.json` , és nem ad meg központi telepítési nevet, akkor a központi telepítés neve `azuredeploy` .

Minden alkalommal, amikor futtat egy központi telepítést, a rendszer egy bejegyzést ad hozzá az erőforráscsoport telepítési előzményeihez a központi telepítési névvel. Ha egy másik központi telepítést futtat, és ugyanazt a nevet adja, a korábbi bejegyzést a rendszer a jelenlegi telepítéssel helyettesíti. Ha az üzembe helyezési előzményekben egyedi bejegyzéseket kíván fenntartani, adjon meg minden egyes központi telepítést egyedi nevet.

Egyedi név létrehozásához véletlenszerű számot rendelhet hozzá.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Vagy adjon hozzá egy dátumérték értéket.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Ha egyazon erőforráscsoporthoz futtat egyidejű központi telepítéseket ugyanazzal a központi telepítési névvel, akkor a rendszer csak az utolsó telepítést hajtja végre. A rendszer a legutóbbi telepítés helyett minden olyan központi telepítést lecserél, amelynek a neve nem fejeződött be. Ha például egy nevű központi telepítést futtat, amely egy nevű `newStorage` Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage2` , csak egy Storage-fiókot telepít. Az eredményül kapott Storage-fiók neve `storage2` .

Ha azonban egy nevű központi telepítést futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage1` , és közvetlenül a befejezése után futtat egy másik nevű központi telepítést `newStorage` , amely telepíti a nevű Storage `storage2` -fiókot, akkor két Storage-fiókkal rendelkezik. Az egyik neve `storage1` , a másik pedig a neve `storage2` . Azonban csak egy bejegyzés szerepel az üzembe helyezési előzményekben.

Amikor egyedi nevet ad meg az egyes központi telepítésekhez, ütközés nélkül is futtathatja őket. Ha olyan nevű központi telepítést futtat `newStorage1` , amely egy nevű Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage2` , amely egy nevű Storage-fiókot telepít `storage2` , akkor két Storage-fiókkal és két bejegyzéssel rendelkezik az üzembe helyezési előzményekben.

Az egyidejű központi telepítésekkel való ütközések elkerülése érdekében, valamint a telepítési előzmények egyedi bejegyzéseinek biztosításához adjon egyedi nevet a központi telepítésnek.

## <a name="deploy-local-template-or-bicep-file"></a>Helyi sablon vagy bicep-fájl üzembe helyezése

A sablont telepítheti a helyi gépről, vagy egy külső tárolóból. Ez a szakasz egy helyi sablon központi telepítését ismerteti.

Ha nem létező erőforráscsoporthoz végez üzembe helyezést, hozzon létre egy erőforráscsoportot. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. A név nem végződhet ponttal.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Helyi sablon vagy bicep-fájl üzembe helyezéséhez használja a `-TemplateFile` paramétert a telepítési parancsban. Az alábbi példa azt is bemutatja, hogyan lehet beállítani a sablonból származó paraméterérték értékét.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep>
```

Az üzembe helyezés több percet is igénybe vehet.

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

> [!NOTE]
> A Azure PowerShell jelenleg nem támogatja a távoli bicep-fájlok telepítését. A [BICEP CLI](./bicep-install.md#development-environment) használatával fordítsa le a bicep-fájlt egy JSON-sablonba, majd töltse be a JSON-fájlt a távoli helyre.

Az ARM-sablonok helyi gépen való tárolása helyett érdemes lehet őket külső helyen tárolni. Sablonokat tárolhat egy olyan verziókövetési rendszer adattárában is, mint a GitHub. Tárolhatja azokat egy Azure-tárfiókban is, ahol megosztva érhető el a vállalat számára.

Ha nem létező erőforráscsoporthoz végez üzembe helyezést, hozzon létre egy erőforráscsoportot. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. A név nem végződhet ponttal.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Külső sablon üzembe helyezésére használja a `-TemplateUri` paramétert.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name remoteTemplateDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Az előző példa egy nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb forgatókönyv esetében működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban a sablonhoz való hozzáférést szeretné kezelni, érdemes lehet a [sablonhoz tartozó specifikációkat](#deploy-template-spec)használni.

Ha olyan távoli csatolt sablonokat kíván üzembe helyezni, amelyek egy Storage-fiókban tárolt relatív elérési úttal rendelkeznek, a következő paranccsal `QueryString` adhatja meg az SAS-tokent:

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

További információ: [relatív elérési út használata csatolt sablonokhoz](./linked-templates.md#linked-template).

## <a name="deploy-template-spec"></a>Sablon üzembe helyezése – spec

> [!NOTE]
> A Azure PowerShell jelenleg nem támogatja a sablon specifikációinak létrehozását a bicep-fájlok biztosításával. Létrehozhat azonban egy olyan bicep-fájlt, amely a [Microsoft. Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) erőforrás segítségével helyezi üzembe a sablon specifikációját. Íme egy [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).
Helyi vagy távoli sablon telepítése helyett hozzon létre egy [specifikációt](template-specs.md). A sablon spec egy ARM-sablont tartalmazó Azure-előfizetésben található erőforrás. Megkönnyíti a sablon biztonságos megosztását a szervezetben lévő felhasználókkal. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával hozzáférést biztosíthat a sablon specifikációjának. Ez a funkció jelenleg előzetes verzióban érhető el.

Az alábbi példák bemutatják, hogyan hozhat létre és helyezhet üzembe egy sablon-specifikációt.

Először hozza létre a sablon specifikációját az ARM-sablon biztosításával.

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

Ezután szerezze be a sablon specifikációjának AZONOSÍTÓját, és telepítse azt.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

További információ: [Azure Resource Manager template specs (előzetes verzió)](template-specs.md).

## <a name="preview-changes"></a>Előnézeti változások

A sablon üzembe helyezése előtt megtekintheti, hogy a sablon milyen módosításokat hajt végre a környezetben. A [mi-if művelettel](template-deploy-what-if.md) ellenőrizheti, hogy a sablon elvégzi-e a várt módosításokat. Mi a teendő, ha a hibát is ellenőrzi a sablonban.

## <a name="pass-parameter-values"></a>Paraméter értékeinek továbbítása

A paraméterek értékének átadásához használhat beágyazott paramétereket vagy egy paraméter-fájlt.

### <a name="inline-parameters"></a>Beágyazott paraméterek

A beágyazott paraméterek továbbításához adja meg a paraméter nevét a `New-AzResourceGroupDeployment` paranccsal. Ha például egy karakterláncot és tömböt szeretne átadni egy sablonnak, használja a következőt:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Emellett beolvashatja a fájl tartalmát, és megadhatja a tartalmat beágyazott paraméterként.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Egy paraméter értékének beolvasása a fájlból hasznos lehet, ha konfigurációs értékeket kell megadnia. Megadhat például [egy Linux rendszerű virtuális gép számára a Cloud-init értékeket](../../virtual-machines/linux/using-cloud-init.md).

Ha objektumok tömbjét kell átadnia, hozzon létre kivonatoló táblákat a PowerShellben, és adja hozzá őket egy tömbhöz. Adja át ezt a tömböt paraméterként az üzembe helyezés során.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Paraméter fájljai

Ahelyett, hogy a paramétereket beágyazott értékekként adná át a szkriptben, egyszerűbbnek találhatja egy olyan JSON-fájl használatát, amely tartalmazza a paraméterértékeket. A paraméter fájl lehet helyi fájl vagy egy elérhető URI-val rendelkező külső fájl is. Mindkét ARM-sablon és a bicep-fájl JSON-paramétereket használ.

A paraméterfájlról a [Resource Manager-paraméterfájl létrehozása](parameter-files.md) című cikk nyújt további információkat.

Helyi paraméter fájljának átadásához használja a következő `TemplateParameterFile` paramétert:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Külső paraméter eléréséhez használja a következő `TemplateParameterUri` paramétert:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Következő lépések

- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- Ha meg szeretné tudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című részt.
- A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [Private ARM-sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).

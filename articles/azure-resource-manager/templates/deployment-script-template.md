---
title: Telepítési parancsfájlok használata a sablonokban | Microsoft Docs
description: az üzembe helyezési parancsfájlok használata Azure Resource Manager-sablonokban.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: jgao
ms.openlocfilehash: 3240cce34a6fa645986a58ab43b28ad38485e97b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308965"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Üzembe helyezési parancsfájlok használata ARM-sablonokban

Ismerje meg, hogyan használhatók az üzembe helyezési parancsfájlok az Azure Resource templates szolgáltatásban (ARM-sablonok). A nevű új erőforrástípus használatával a `Microsoft.Resources/deploymentScripts` felhasználók parancsfájlokat futtathatnak a sablonok központi telepítésében, és áttekinthetik a végrehajtás eredményeit. Ezek a parancsfájlok olyan egyéni lépések végrehajtásához használhatók, mint például a következők:

- felhasználók hozzáadása egy címtárhoz
- adatsík műveletek végrehajtása, például a Blobok vagy a vetőmag-adatbázisok másolása
- licenckulcs megkeresése és érvényesítése
- önaláírt tanúsítvány létrehozása
- objektum létrehozása az Azure AD-ben
- IP-címterület kikeresése egyéni rendszerből

Az üzembe helyezési parancsfájl előnyei:

- Egyszerű kód, használat és hibakeresés. Az üzembe helyezési parancsfájlokat kedvenc fejlesztői környezetekben is kifejlesztheti. A szkriptek sablonokba vagy külső parancsfájlokban is beágyazva lehetnek.
- Megadhatja a parancsfájl nyelvét és platformját. Jelenleg a Linux-környezetben a Azure PowerShell és az Azure CLI üzembe helyezési parancsfájljai támogatottak.
- A parancssori argumentumok parancsfájlba való átadásának engedélyezése.
- Megadhatja a parancsfájlok kimeneteit, és visszaküldheti azokat az üzembe helyezéshez.

Az üzembe helyezési parancsfájl erőforrása csak azokon a régiókban érhető el, ahol elérhető az Azure Container instance.  Tekintse [meg az Azure-régiók Azure Container instances erőforrás-elérhetőségét](../../container-instances/container-instances-region-availability.md)ismertető témakört.

> [!IMPORTANT]
> A parancsfájlok végrehajtásához és a hibaelhárításhoz szükség van egy Storage-fiókra és egy tároló példányra. Lehetősége van meglévő Storage-fiók megadására, máskülönben a Storage-fiókkal együtt automatikusan létrejön a tároló-példány. A szkript szolgáltatás általában törli a két automatikusan létrehozott erőforrást, amikor a telepítési parancsfájl végrehajtása egy terminál állapotba kerül. Az erőforrások számlázása az erőforrások törlése után történik. További információért lásd: [telepítési parancsfájl-erőforrások tisztítása](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> A deploymentScripts Resource API 2020-10-01-es verziója támogatja a [OnBehalfofTokens (OBO)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md). Az OBO használatával az üzembe helyezési parancsfájl szolgáltatás a rendszerbiztonsági tag tokenjét használja az üzembe helyezési parancsfájlok futtatásához, például az Azure Container instance, az Azure Storage-fiók és a felügyelt identitáshoz tartozó szerepkör-hozzárendelések létrehozásához. A régebbi API-verzióban a felügyelt identitás használatával hozhatók létre ezek az erőforrások.
> Az Azure-bejelentkezéshez szükséges újrapróbálkozási logika mostantól a burkoló parancsfájlba van beépítve. Ha az engedélyeket ugyanabban a sablonban adja meg, ahol az üzembehelyezési parancsfájlokat futtatja. A telepítési parancsfájl-szolgáltatás 10 percen belül újrapróbálkozik a bejelentkezéssel, amíg a felügyelt identitás szerepkör-hozzárendelését nem replikálja a rendszer.

## <a name="configure-the-minimum-permissions"></a>A minimális engedélyek konfigurálása

Az üzembe helyezési parancsfájl API 2020-10-01-es vagy újabb verziója esetén az üzembe helyezési tag az üzembe helyezési parancsfájl erőforrásának végrehajtásához szükséges mögöttes erőforrások létrehozására szolgál, amely egy Storage-fiók és egy Azure Container-példány. Ha a szkriptnek hitelesítenie kell az Azure-ban, és Azure-specifikus műveleteket kell végrehajtania, javasoljuk, hogy a parancsfájlt felhasználó által hozzárendelt felügyelt identitással adja meg. A felügyelt identitásnak rendelkeznie kell a szükséges hozzáféréssel a művelet végrehajtásához a parancsfájlban.

A legkevesebb jogosultsággal rendelkező engedélyek konfigurálásához a következők szükségesek:

- Rendeljen hozzá egy egyéni szerepkört a következő tulajdonságokkal az üzembe helyezési rendszerbiztonsági tag számára:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Ha az Azure Storage és az Azure Container instance erőforrás-szolgáltató nincs regisztrálva, hozzá kell adnia a és a t is `Microsoft.Storage/register/action` `Microsoft.ContainerInstance/register/action` .

- Felügyelt identitás használata esetén az üzembe helyezési feladatnak szüksége van a felügyelt identitás erőforráshoz rendelt **felügyelt identitás-kezelő** szerepkörre (beépített szerepkör).

## <a name="sample-templates"></a>Példasablonok

A következő JSON egy példa. További információ: a legújabb [sablon sémája](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> A példa demonstrációs célokat szolgál. A tulajdonságok `scriptContent` és a `primaryScriptUri` sablonban nem használhatók.

> [!NOTE]
> A _scriptContent_ több sorral rendelkező parancsfájlt mutat be.  A Azure Portal és az Azure DevOps-folyamat nem tudja elemezni az üzembe helyezési parancsfájlt több sorral. A PowerShell-parancsokat (pontosvesszővel vagy _\\ r \\ n_ vagy _\\ n_ használatával) összekapcsolhatja egy sorba, vagy használhatja a `primaryScriptUri` tulajdonságot egy külső parancsfájllal. Számos ingyenes JSON-karakterlánc Escape/unescape-eszköz érhető el. Például: [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Tulajdonság értékének részletei:

- `identity`: Az üzembe helyezési parancsfájl API 2020-10-01-es vagy újabb verziója esetén a felhasználó által hozzárendelt felügyelt identitás nem kötelező, kivéve, ha a parancsfájlban nem kell végrehajtania az Azure-specifikus műveleteket.  Az API 2019-10-01-es verziójának előzetes verziója esetén felügyelt identitásra van szükség, mivel a telepítési parancsfájl-szolgáltatás azt használja a parancsfájlok végrehajtásához. Jelenleg csak a felhasználó által hozzárendelt felügyelt identitás támogatott.
- `kind`: Adja meg a parancsfájl típusát. Jelenleg a Azure PowerShell és az Azure CLI-parancsfájlok támogatottak. Az értékek a következők: **AzurePowerShell** és **AzureCLI**.
- `forceUpdateTag`: Ha módosítja ezt az értéket a sablon központi telepítései között, akkor az üzembe helyezési parancsfájlt újra végre kell hajtani. Ha a `newGuid()` vagy a `utcNow()` függvényt használja, akkor mindkét függvény csak a paraméter alapértelmezett értékében használható. További információ: [parancsfájl futtatása](#run-script-more-than-once)többször.
- `containerSettings`: Itt adhatja meg az Azure Container instance testreszabásához szükséges beállításokat. Az üzembe helyezési parancsfájlhoz új Azure Container-példány szükséges. Meglévő Azure Container-példányt nem adhat meg. A tároló csoport nevét azonban a használatával szabhatja testre `containerGroupName` . Ha nincs megadva, a rendszer automatikusan létrehozza a csoport nevét.
- `storageAccountSettings`: A meglévő Storage-fiók használatára vonatkozó beállítások megadása. Ha `storageAccountName` nincs megadva, a rendszer automatikusan létrehozza a Storage-fiókot. Lásd: [meglévő Storage-fiók használata](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: Itt adhatja meg a használni kívánt modul verzióját. Tekintse meg a [támogatott Azure PowerShell verziók](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)listáját. Tekintse meg a [támogatott Azure CLI-verziók](https://mcr.microsoft.com/v2/azure-cli/tags/list)listáját.

  >[!IMPORTANT]
  > A telepítési parancsfájl a Microsoft Container Registry (MCR) által elérhető CLI-rendszerképeket használja. Egy hónapot vesz igénybe, hogy az üzembe helyezési parancsfájlhoz tartozó CLI-rendszerképet hitelesítse. Ne használja a 30 napon belül kiadott CLI-verziókat. A képek kiadási dátumait az [Azure CLI kibocsátási megjegyzései](/cli/azure/release-notes-azure-cli)című témakörben találja. Ha nem támogatott verziót használ, a hibaüzenet felsorolja a támogatott verziókat.

- `arguments`: Határozza meg a paraméterek értékeit. Az értékeket szóközök választják el egymástól.

  Az üzembe helyezési parancsfájlok karakterláncok tömbje szerint osztják el az argumentumokat a [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) rendszerhívás meghívásával. Erre a lépésre azért van szükség, mert az argumentumok [parancs tulajdonságként](/rest/api/container-instances/containergroups/createorupdate#containerexec) lesznek átadva az Azure Container instance számára, a Command tulajdonság pedig sztring.

  Ha az argumentumok Escape-karaktereket tartalmaznak, a [JsonEscaper](https://www.jsonescaper.com/) használatával megduplázhatja a karaktereket. Illessze be az eredeti Escape-karakterláncot az eszközbe, majd válassza a **Escape** lehetőséget.  Az eszköz egy dupla Escape-karakterláncot ad vissza. Az előző minta sablonban például az argumentum a következő: `-name \"John Dole\"` . Az Escape-sztring `-name \\\"John Dole\\\"` .

  Ha argumentumként egy ARM-sablon típusú paramétert szeretne átadni, alakítsa át az objektumot egy sztringre a [karakterlánc ()](./template-functions-string.md#string) függvény használatával, majd a [replace ()](./template-functions-string.md#replace) függvény használatával cserélje le a szöveget a következőre: `\"` `\\\"` . Például:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  További információ: [sablon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Adja meg azokat a környezeti változókat, amelyeket át kell adni a parancsfájlnak. További információ: [telepítési parancsfájlok fejlesztése](#develop-deployment-scripts).
- `scriptContent`: Adja meg a parancsfájl tartalmát. Külső parancsfájl futtatásához használja `primaryScriptUri` helyette a parancsot. Példák: [beágyazott parancsfájl használata](#use-inline-scripts) és [külső parancsfájl használata](#use-external-scripts).
- `primaryScriptUri`: Adjon meg egy nyilvánosan elérhető URL-címet az elsődleges telepítési parancsfájl számára a támogatott fájlkiterjesztések használatával. További információ: [külső parancsfájlok használata](#use-external-scripts).
- `supportingScriptUris`: Adja meg a nyilvánosan elérhető URL-címek tömbjét, amely támogatja a vagy a által meghívott fájlokat `scriptContent` `primaryScriptUri` . További információ: [külső parancsfájlok használata](#use-external-scripts).
- `timeout`: Adja meg az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)megadott maximálisan engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett érték a **P1D**.
- `cleanupPreference`. Adja meg a telepítési erőforrások törlésének előnyét, ha a parancsfájl végrehajtása terminál állapotba kerül. Az alapértelmezett beállítás **mindig**, ami azt jelenti, hogy a rendszer a terminál állapota (sikeres, sikertelen, megszakított) ellenére törli az erőforrásokat. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](#clean-up-deployment-script-resources).
- `retentionInterval`: Adja meg azt az időközt, ameddig a szolgáltatás megtartja a telepítési parancsfájl erőforrásait, miután a telepítési parancsfájl végrehajtása eléri a terminál állapotát. Az üzembe helyezési parancsfájl erőforrásai törlődnek, ha ez az időtartam lejár. Az időtartam az [ISO 8601 minta](https://en.wikipedia.org/wiki/ISO_8601)alapján történik. Az adatmegőrzési időköz 1 és 26 óra közötti (PT26H). Ezt a tulajdonságot akkor használja a rendszer, ha a `cleanupPreference` értéke **OnExpiration**. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>További minták

- [1. példa](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): kulcstartó létrehozása és üzembe helyezési parancsfájl használata tanúsítvány hozzárendeléséhez a kulcstartóhoz.
- [2. minta](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): hozzon létre egy erőforráscsoportot az előfizetési szinten, hozzon létre egy kulcstartót az erőforráscsoporthoz, majd az üzembehelyezési parancsfájl használatával rendeljen hozzá egy tanúsítványt a kulcstartóhoz.
- [3. példa](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, rendelje hozzá a közreműködői szerepkört az identitáshoz az erőforráscsoport szintjén, hozzon létre egy kulcstartót, majd a telepítési parancsfájl használatával rendeljen hozzá egy tanúsítványt a kulcstartóhoz.

## <a name="use-inline-scripts"></a>Beágyazott parancsfájlok használata

A következő sablon egyetlen erőforrással van definiálva a `Microsoft.Resources/deploymentScripts` típussal. A kiemelt rész a beágyazott parancsfájl.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Mivel a beágyazott telepítési parancsfájlok idézőjelek közé vannak ágyazva, az üzembe helyezési parancsfájlokban lévő karakterláncokat fordított perjel (**&#92;**) használatával kell megszökni, vagy aposztrófok közé kell foglalni. Azt is megteheti, hogy a karakterlánc-helyettesítést használja az előző JSON-mintában látható módon.

A parancsfájl egy paramétert fogad, és kiírja a paraméter értékét. `DeploymentScriptOutputs` kimenetek tárolására szolgál. A kimenetek szakaszban a `value` sor azt mutatja be, hogyan lehet hozzáférni a tárolt értékekhez. `Write-Output` hibakeresési célra szolgál. A kimeneti fájl elérésének megismeréséhez lásd: [telepítési parancsfájlok figyelése és hibáinak megoldása](#monitor-and-troubleshoot-deployment-scripts). A tulajdonságok leírását lásd: [példák a sablonokra](#sample-templates).

A parancsfájl futtatásához válassza a **kipróbálás** lehetőséget a Cloud Shell megnyitásához, majd illessze be a következő kódot a rendszerhéj ablaktáblába.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

A kimenet a következőképpen fog kinézni:

![Resource Manager-sablon üzembe helyezési parancsfájl Hello World output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Külső parancsfájlok használata

A beágyazott parancsfájlok mellett külső parancsfájlokat is használhat. Csak a _ps1_ fájlnévkiterjesztéssel rendelkező elsődleges PowerShell-parancsfájlok támogatottak. A CLI-parancsfájlok esetében az elsődleges parancsfájlok rendelkezhetnek kiterjesztéssel (vagy kiterjesztés nélkül), feltéve, hogy a parancsfájlok érvényes bash-parancsfájlok. A külső parancsfájlok használatához cserélje le a parancsot a következőre: `scriptContent` `primaryScriptUri` . Például:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

További információ: [példa a sablonra](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

A külső parancsfájloknak elérhetőnek kell lenniük. Az Azure Storage-fiókokban tárolt parancsfájlok biztonságossá tételéhez állítson be egy SAS-tokent, és vegye fel azt a sablon URI-kódjába. Állítsa be a lejárati időt, hogy elegendő idő legyen a telepítés befejezésére. További információ: [Private ARM-sablon üzembe helyezése sas-tokenrel](./secure-template-with-sas-token.md).

Ön felelős az üzembe helyezési parancsfájl által hivatkozott parancsfájlok integritásának biztosításáért, `primaryScriptUri` vagy `supportingScriptUris` . Csak a megbízható parancsfájlokra hivatkozzon.

## <a name="use-supporting-scripts"></a>Támogató parancsfájlok használata

A bonyolult logikai műveleteket egy vagy több támogató parancsfájlba is elkülönítheti. A `supportingScriptUris` tulajdonság lehetővé teszi, hogy szükség esetén az URI-k tömbjét adja meg a támogató parancsfájl-fájlokhoz:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

A támogató parancsfájlok a beágyazott parancsfájlokból és az elsődleges parancsfájlokból is meghívhatók. A támogató parancsfájlok nem rendelkeznek korlátozásokkal a fájlkiterjesztés esetében.

A rendszer átmásolja a támogató fájlokat a `azscripts/azscriptinput` futtatókörnyezetbe. Relatív elérési út használatával hivatkozhat a beágyazott parancsfájlokból és az elsődleges parancsfájlokból származó támogató fájlokra.

## <a name="work-with-outputs-from-powershell-script"></a>Kimenetek használata PowerShell-parancsfájlból

A következő sablon azt mutatja be, hogyan lehet értékeket átadni két `deploymentScripts` erőforrás között:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

Az első erőforrásban definiál egy nevű változót `$DeploymentScriptOutputs` , és a kimeneti értékek tárolására használhatja azt. A sablonban lévő másik erőforrás kimeneti értékének eléréséhez használja a következőt:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Kimenetek használata a CLI-szkriptből

Eltér a PowerShell telepítési parancsfájltól, a CLI/bash-támogatás nem teszi elérhetővé egy közös változót a parancsfájlok kimenetének tárolásához, hanem egy nevű környezeti változót, `AZ_SCRIPTS_OUTPUT_PATH` amely a parancsfájl kimeneti fájljának helyét tárolja. Ha egy üzembe helyezési parancsfájl egy Resource Manager-sablonból fut, akkor a bash rendszerhéj automatikusan beállítja ezt a környezeti változót.

A telepítési szkriptek kimeneteit a helyen kell menteni `AZ_SCRIPTS_OUTPUT_PATH` , a kimeneteknek pedig érvényes JSON karakterlánc-objektumnak kell lenniük. A fájl tartalmát kulcs-érték párokként kell menteni. A karakterláncok tömbje például a következőként van tárolva: `{ "MyResult": [ "foo", "bar"] }` .  Csak a tömb eredményeinek tárolása, például `[ "foo", "bar" ]` érvénytelen.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

a [jQ](https://stedolan.github.io/jq/) az előző mintában van használatban. A tároló lemezképeit tartalmazza. Lásd: a [fejlesztési környezet konfigurálása](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Meglévő Storage-fiók használata

A parancsfájlok végrehajtásához és a hibaelhárításhoz szükség van egy Storage-fiókra és egy tároló példányra. Lehetősége van meglévő Storage-fiók megadására, máskülönben a Storage-fiókkal együtt automatikusan létrejön a tároló-példány. Meglévő Storage-fiók használatára vonatkozó követelmények:

- A támogatott Storage-fiókok a következők:

    | Termékváltozat             | Támogatott típus     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Tárolás, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Tárolás, StorageV2 |
    | Standard_RAGRS  | Tárolás, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Ezek a kombinációk támogatják a fájlmegosztás használatát. További információ: Azure- [fájlmegosztás](../../storage/files/storage-how-to-create-file-share.md) és- [típusú tárolási fiókok](../../storage/common/storage-account-overview.md)létrehozása.

- A Storage-fiók tűzfalszabályok még nem támogatottak. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md).
- A központi telepítési résztvevőnek rendelkeznie kell engedéllyel a Storage-fiók kezeléséhez, beleértve az olvasás, a létrehozás és a fájlmegosztás törlését.

Meglévő Storage-fiók megadásához adja hozzá a következő JSON-t a tulajdonság eleméhez `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: adja meg a Storage-fiók nevét.
- `storageAccountKey`: a Storage-fiók kulcsainak egyikét kell megadnia. A kulcs lekéréséhez a [listkeys műveletének beolvasása ()](./template-functions-resource.md#listkeys) függvényt használhatja. Például:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Tekintse meg a teljes definíciós minta [sablonjait](#sample-templates) `Microsoft.Resources/deploymentScripts` .

Meglévő Storage-fiók használatakor a parancsfájl-szolgáltatás egy egyedi névvel rendelkező fájlmegosztást hoz létre. Tekintse meg a [telepítési parancsfájl-erőforrások tisztítása](#clean-up-deployment-script-resources) című témakört, amely azt ismerteti, hogyan törli a parancsfájl-szolgáltatás a fájlmegosztást.

## <a name="develop-deployment-scripts"></a>Üzembe helyezési parancsfájlok fejlesztése

### <a name="handle-non-terminating-errors"></a>Nem megszakítást okozó hibák kezelése

A telepítési parancsfájlban található változó használatával szabályozhatja, hogyan válaszol a PowerShell a nem megszakítást okozó hibákra `$ErrorActionPreference` . Ha a változó nincs beállítva a telepítési parancsfájlban, a parancsfájl-szolgáltatás az **alapértelmezett értéket fogja** használni.

A parancsfájl-szolgáltatás az erőforrás-kiépítési állapotot úgy állítja be, hogy **sikertelen** legyen, amikor a parancsfájl hibát észlel a beállítása ellenére `$ErrorActionPreference` .

### <a name="pass-secured-strings-to-deployment-script"></a>Biztonságos karakterláncok továbbítása a telepítési parancsfájlba

A környezeti változók (EnvironmentVariable) beállítása a Container instances szolgáltatásban lehetővé teszi a tároló által futtatott alkalmazás vagy parancsfájl dinamikus konfigurációját. Az üzembe helyezési parancsfájl ugyanúgy kezeli a nem védett és a biztonságos környezeti változókat, mint az Azure Container instance. További információ: [környezeti változók beállítása a Container instances](../../container-instances/container-instances-environment-variables.md#secure-values)szolgáltatásban. Példaként lásd: [példák a sablonokra](#sample-templates).

A környezeti változók maximálisan megengedett mérete 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Üzembe helyezési parancsfájlok figyelése és hibáinak megoldása

A parancsfájl-szolgáltatás létrehoz egy [Storage-fiókot](../../storage/common/storage-account-overview.md) (kivéve, ha megad egy meglévő Storage-fiókot) és egy tároló- [példányt](../../container-instances/container-instances-overview.md) a parancsfájlok végrehajtásához. Ha ezeket az erőforrásokat a parancsfájl-szolgáltatás automatikusan hozza létre, akkor mindkét erőforrásnak van `azscripts` utótagja az erőforrás nevében.

![Resource Manager-sablon telepítési parancsfájljának erőforrásainak nevei](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

A felhasználói parancsfájl, a végrehajtás eredményei és az stdout-fájl a Storage-fiók Files megosztásában tárolódik. Van egy nevű mappa `azscripts` . A mappában két további mappa található a bemeneti és a kimeneti fájlokhoz: `azscriptinput` és `azscriptoutput` .

A kimeneti mappa tartalmaz egy _executionresult.jst_ és a parancsfájl kimeneti fájlját. Megtekintheti a parancsfájl-végrehajtási hibaüzenetet _executionresult.json_. A kimeneti fájl csak akkor jön létre, ha a parancsfájl végrehajtása sikeresen megtörtént. A bemeneti mappa egy PowerShell-parancsfájlt és a felhasználói telepítési parancsfájlokat tartalmaz. A felhasználói telepítési parancsfájlt lecserélheti egy módosítottra, majd újra futtathatja az üzembe helyezési parancsfájlt az Azure Container instanceból.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az üzembe helyezési parancsfájl erőforrásának üzembe helyezése után az erőforrás a Azure Portal erőforráscsoport alatt jelenik meg. Az alábbi képernyőfelvételen a telepítési parancsfájl erőforrásának **Áttekintés** lapja látható:

![Resource Manager-sablonok üzembe helyezési parancsfájl-portálja – áttekintés](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Az Áttekintés oldalon az erőforrás néhány fontos információja látható, például a **kiépítési állapot**, a **Storage-fiók**, a **Container instance** és a **naplók**.

A bal oldali menüben megtekintheti a telepítési parancsfájl tartalmát, a parancsfájlnak átadott argumentumokat, valamint a kimenetet. A telepítési parancsfájlhoz is exportálhat sablont, beleértve az üzembehelyezési parancsfájlt is.

### <a name="use-powershell"></a>A PowerShell használata

A Azure PowerShell használatával a telepítési parancsfájlokat az előfizetés vagy az erőforráscsoport hatóköre szerint kezelheti:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): üzembe helyezési parancsfájlok beolvasása vagy felsorolása.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): lekéri a telepítési parancsfájl végrehajtásának naplóját.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): eltávolítja a telepítési parancsfájlt és a hozzá tartozó erőforrásokat.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): menti a telepítési parancsfájl naplóját a lemezre.

A kimenet a következőhöz `Get-AzDeploymentScript` hasonló:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Az Azure CLI használatával a telepítési parancsfájlokat az előfizetés vagy az erőforráscsoport hatóköre szerint kezelheti:

- [az Deployment-Scripts delete](/cli/azure/deployment-scripts#az-deployment-scripts-delete): telepítési parancsfájl törlése.
- [az Deployment-Scripts List](/cli/azure/deployment-scripts#az-deployment-scripts-list): az összes üzembehelyezési parancsfájl listázása.
- [az Deployment-Scripts show](/cli/azure/deployment-scripts#az-deployment-scripts-show): telepítési parancsfájl beolvasása.
- [az Deployment-Scripts show-log: az](/cli/azure/deployment-scripts#az-deployment-scripts-show-log)üzembe helyezési parancsfájl naplófájljainak megjelenítése.

A LIST parancs kimenete a következőhöz hasonló:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>REST API használata

Az üzembe helyezési parancsfájl erőforrás-telepítési információit az erőforráscsoport szintjén és az előfizetés szintjén szerezheti be REST API használatával:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

A következő példa a [ARMClient](https://github.com/projectkudu/ARMClient)használja:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

A következő REST API a naplót adja vissza:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Ez csak az üzembe helyezési parancsfájl erőforrásainak törlése előtt működik.

Ha meg szeretné tekinteni a deploymentScripts-erőforrást a portálon, válassza a **rejtett típusok megjelenítése** lehetőséget:

![Resource Manager-sablon telepítési parancsfájlja, rejtett típusok megjelenítése, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Üzembe helyezési parancsfájl erőforrásainak karbantartása

A parancsfájlok végrehajtásához és a hibaelhárításhoz szükség van egy Storage-fiókra és egy tároló példányra. Lehetősége van megadnia egy meglévő Storage-fiókot, ellenkező esetben a rendszer automatikusan létrehozza a Storage-fiókot a tároló-példánnyal együtt. A parancsfájl-szolgáltatás két automatikusan létrehozott erőforrást töröl, amikor a telepítési parancsfájl végrehajtása egy terminál állapotba kerül. Az erőforrások számlázása az erőforrások törlése után történik. Az árakra vonatkozó információkért lásd: [Container instances díjszabás](https://azure.microsoft.com/pricing/details/container-instances/) és az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Ezeknek az erőforrásoknak a életciklusát a sablon következő tulajdonságai vezérlik:

- `cleanupPreference`: Ha a parancsfájl végrehajtása egy terminál állapotba kerül, a rendszer megtisztítja a beállítást. A támogatott értékek a következők:

  - **Mindig**: törölje az automatikusan létrehozott erőforrásokat, ha a parancsfájl végrehajtása terminál állapotba kerül. Ha egy meglévő Storage-fiókot használ, a parancsfájl-szolgáltatás törli a Storage-fiókban létrehozott fájlmegosztást. Mivel az `deploymentScripts` erőforrás továbbra is megtalálható az erőforrások tisztítása után, a parancsfájl-szolgáltatás megőrzi a parancsfájlok végrehajtásának eredményét, például az stdout, a kimenetek és a visszatérési értéket az erőforrások törlése előtt.
  - **OnSuccess**: csak akkor törölje az automatikusan létrehozott erőforrásokat, ha a parancsfájl végrehajtása sikeres. Ha egy meglévő Storage-fiókot használ, a parancsfájl-szolgáltatás csak akkor távolítja el a fájlmegosztást, ha a parancsfájl végrehajtása sikeres. A hibakeresési adatok megkereséséhez továbbra is hozzáférhet az erőforrásokhoz.
  - **OnExpiration**: csak akkor törölje az automatikusan létrehozott erőforrásokat, ha a `retentionInterval` beállítás lejárt. Ha meglévő Storage-fiókot használ, a parancsfájl-szolgáltatás eltávolítja a fájlmegosztást, de megőrzi a Storage-fiókot.

- `retentionInterval`: Adja meg azt az időintervallumot, ameddig a rendszer megőrzi a parancsfájl-erőforrást, majd azt követően, hogy lejárt és törölve lesz.

> [!NOTE]
> A Storage-fiók és a parancsfájl-szolgáltatás által más célra létrehozott tároló-példány használata nem ajánlott. Előfordulhat, hogy a parancsfájl életciklusa alapján a két erőforrás el lesz távolítva.

A tároló-példány és a Storage-fiók a következő szerint törlődik: `cleanupPreference` . Ha azonban a parancsfájl meghibásodik, és a `cleanupPreference` nem **mindig** értékre van állítva, a telepítési folyamat automatikusan egy órára tartja a tárolót. Ezt az órát használhatja a parancsfájl hibakereséséhez. Ha meg szeretné tartani a tárolót a sikeres telepítések után, vegyen fel egy alvó lépést a parancsfájlba. Például adja hozzá a [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) parancsot a szkript végéhez. Ha nem adja hozzá az alvó lépést, a tároló egy terminál állapotra van beállítva, és még akkor sem érhető el, ha még nincs törölve.

## <a name="run-script-more-than-once"></a>Parancsfájl többszöri futtatása

A telepítési parancsfájl végrehajtása egy idempotens művelet. Ha a `deploymentScripts` rendszer nem módosítja az erőforrás-tulajdonságokat (beleértve a beágyazott parancsfájlt is), a parancsfájl nem lesz végrehajtva a sablon újbóli telepítésekor. Az üzembe helyezési parancsfájl szolgáltatás összehasonlítja a sablonban lévő erőforrás-neveket az ugyanabban az erőforráscsoport meglévő erőforrásaival. Két lehetőség közül választhat, ha ugyanazt az üzembe helyezési parancsfájlt többször szeretné végrehajtani:

- Módosítsa az `deploymentScripts` erőforrás nevét. Használja például az [utcNow](./template-functions-date.md#utcnow) -sablon függvényt az erőforrás neve vagy az erőforrás neve részeként. Az erőforrás nevének módosítása új `deploymentScripts` erőforrást hoz létre. A parancsfájl-végrehajtás előzményeinek megőrzése jó.

    > [!NOTE]
    > A `utcNow` függvény csak a paraméter alapértelmezett értékében használható.

- Egy másik értéket kell megadnia a `forceUpdateTag` sablon tulajdonságban. Használja például `utcNow` az értéket.

> [!NOTE]
> Írja be a idempotens telepítési parancsfájlokat. Ez biztosítja, hogy ha véletlenül újra futnak, a rendszer nem fog változásokat okozni. Ha például az üzembe helyezési parancsfájl egy Azure-erőforrás létrehozásához használatos, ellenőrizze, hogy az erőforrás nem létezik-e a létrehozása előtt, így a parancsfájl sikeres lesz, vagy nem hozza létre újra az erőforrást.

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

Egy előre konfigurált tároló-rendszerképet is használhat a telepítési parancsfájl fejlesztési környezete számára. További információ: a [fejlesztői környezet konfigurálása a sablonokban történő üzembe helyezési parancsfájlokhoz](./deployment-script-template-configure-dev.md).

A parancsfájl sikeres tesztelése után a sablonban használható üzembe helyezési parancsfájlként.

## <a name="deployment-script-error-codes"></a>Üzembehelyezési parancsfájl hibakódai

| Hibakód | Description |
|------------|-------------|
| DeploymentScriptInvalidOperation | Az üzembehelyezési parancsfájl erőforrás-definíciója a sablonban érvénytelen tulajdonságokat tartalmaz. |
| DeploymentScriptResourceConflict | Nem lehet törölni a nem terminál állapotú központi telepítési parancsfájl erőforrását, és a végrehajtás nem haladja meg az 1 órát. Vagy nem lehet újból futtatni ugyanazt az üzembe helyezési parancsfájlt ugyanazzal az erőforrás-azonosítóval (az előfizetés, az erőforráscsoport neve és az erőforrás neve), de a parancsfájl szövegtörzse is egy időben. |
| DeploymentScriptOperationFailed | Az üzembe helyezési parancsfájl művelete belső hiba miatt meghiúsult. Forduljon a Microsoft ügyfélszolgálatához. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Nincs megadva a hozzáférési kulcs a meglévő Storage-fiókhoz.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Az üzembe helyezési parancsfájl szolgáltatás által létrehozott tároló csoport külsőleg módosítva lett, és érvénytelen tárolók lettek hozzáadva. |
| DeploymentScriptContainerGroupInNonterminalState | Két vagy több üzembe helyezési parancsfájl erőforrás ugyanazt az Azure Container instance-nevet használja ugyanabban az erőforráscsoportban, és egyikük még nem fejezte be a végrehajtást. |
| DeploymentScriptStorageAccountInvalidKind | A BlobBlobStorage vagy BlobStorage-típus meglévő Storage-fiókja nem támogatja a fájlmegosztás használatát, és nem használható. |
| DeploymentScriptStorageAccountInvalidKindAndSku | A meglévő Storage-fiók nem támogatja a fájlmegosztást. A támogatott Storage-fiókok listáját a [meglévő Storage-fiók használata](#use-existing-storage-account)című témakörben tekintheti meg. |
| DeploymentScriptStorageAccountNotFound | A Storage-fiók nem létezik, vagy egy külső folyamat vagy eszköz törölte. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | A megadott Storage-fiók rendelkezik szolgáltatási végponttal. A szolgáltatási végponttal rendelkező Storage-fiókok nem támogatottak. |
| DeploymentScriptStorageAccountInvalidAccessKey | A meglévő Storage-fiókhoz megadott hozzáférési kulcs érvénytelen. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | A Storage-fiók kulcsának formátuma érvénytelen. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | A telepítési parancsfájl végrehajtási ideje túllépte a telepítési parancsfájl erőforrás-definíciójában megadott időtúllépési értéket. |
| DeploymentScriptInvalidOutputs | Az üzembehelyezési parancsfájl kimenete nem érvényes JSON-objektum. |
| DeploymentScriptContainerInstancesServiceLoginFailure | A felhasználó által hozzárendelt felügyelt identitás nem tudott bejelentkezni az 1 perces intervallummal rendelkező 10 próbálkozás után. |
| DeploymentScriptContainerGroupNotFound | Az üzembe helyezési parancsfájl szolgáltatás által létrehozott tároló csoportot egy külső eszköz vagy folyamat törölte. |
| DeploymentScriptDownloadFailure | Nem sikerült letölteni egy támogató parancsfájlt. Lásd: [támogató parancsfájl használata](#use-supporting-scripts).|
| DeploymentScriptError | A felhasználói parancsfájl hibát jelzett. |
| DeploymentScriptBootstrapScriptExecutionFailed | A rendszerindítási parancsfájl hibát jelzett. A rendszerindítási parancsfájl a telepítési parancsfájl végrehajtásának előkészítésére szolgáló rendszerparancsfájl. |
| DeploymentScriptExecutionFailed | Ismeretlen hiba történt a telepítési parancsfájl végrehajtása során. |
| DeploymentScriptContainerInstancesServiceUnavailable | Az Azure Container instance (ACI) létrehozásakor az ACI a szolgáltatás nem érhető el hibát váltott ki. |
| DeploymentScriptContainerGroupInNonterminalState | Az Azure Container instance (ACI) létrehozásakor egy másik telepítési parancsfájl ugyanazt az ACI-nevet használja ugyanabban a hatókörben (az előfizetés, az erőforráscsoport neve és az erőforrás neve). |
| DeploymentScriptContainerGroupNameInvalid | Az Azure Container instance megadott neve (ACI) nem felel meg az ACI-követelményeknek. Lásd: [Azure Container instances gyakori problémáinak elhárítása](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a telepítési parancsfájlokat. Útmutató az üzembe helyezési parancsfájlhoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: telepítési parancsfájlok használata Azure Resource Manager-sablonokban](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [A modul megismerése: ARM-sablonok kiterjesztése telepítési parancsfájlok használatával](/learn/modules/extend-resource-manager-template-deployment-scripts/)

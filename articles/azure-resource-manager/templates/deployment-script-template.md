---
title: Üzembe helyezési szkriptek használata a sablonokban | Microsoft Docs
description: üzembe helyezési szkripteket használhat Azure Resource Manager sablonokban.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: d35deb978b3b60b73ac393b241471cb528817d35
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536956"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Üzembe helyezési szkriptek használata ARM-sablonokban

Megtudhatja, hogyan használhatja az üzembe helyezési szkripteket az Azure-erőforrássablonok (ARM-sablonok) használatával. A nevű új erőforrástípussal a felhasználók szkripteket hajthatnak végre a sablontelepítések során, `Microsoft.Resources/deploymentScripts` és áttekintheti a végrehajtási eredményeket. Ezek a szkriptek az alábbi egyéni lépések végrehajtásához használhatók:

- felhasználók hozzáadása címtárhoz
- adatsíkműveleteket hajthat végre, például blobokat vagy magadatbázist másolhat
- licenckulcsok ki- és ellenőrzése
- önaírt tanúsítvány létrehozása
- objektum létrehozása az Azure AD-ban
- IP-címblokkok egyéni rendszerből való ki keresnie

Az üzembe helyezési szkript előnyei:

- Könnyen kódolt, használható és hibakeresési. Üzembe helyezési szkripteket a kedvenc fejlesztési környezetében fejleszthet. A szkriptek beágyazhatók sablonokba vagy külső szkriptfájlokba.
- Megadhatja a szkript nyelvét és platformját. Jelenleg a Azure PowerShell Azure CLI üzembehelyi szkriptek linuxos környezetben támogatottak.
- Parancssori argumentumok parancsfájlnak való átadásának engedélyezése.
- Megadhatja a szkriptkimeneteket, és visszaadhatja azokat az üzembe helyezésnek.

Az üzembe helyezési szkript erőforrása csak abban a régióban érhető el, ahol az Azure Container Instance elérhető.  Lásd: [Erőforrások rendelkezésre állása Azure Container Instances Azure-régiókban.](../../container-instances/container-instances-region-availability.md)

> [!IMPORTANT]
> A szkriptek végrehajtáshoz és hibaelhárításhoz tárfiókra és tárolópéldányra van szükség. Megadhat egy meglévő tárfiókot, ellenkező esetben a szkriptszolgáltatás automatikusan létrehoz egy tárfiókot és a tárolópéldányt. A szkriptszolgáltatás általában törli a két automatikusan létrehozott erőforrást, amikor az üzembe helyezési szkript végrehajtása terminálállapotba kerül. Az erőforrásokért a törlésig kell fizetni. További információ: Az üzembe [helyezési szkript erőforrásainak megtisztítása.](#clean-up-deployment-script-resources)

> [!IMPORTANT]
> A deploymentScripts erőforrás API 2020-10-01-es verziója támogatja az [OnBehalfofTokens(OBO) használatát.](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md) Az OBO használatával az üzembe helyezési szkript szolgáltatás az üzembe helyezési rendszerbiztonsági tag jogkivonatát használja az üzembehelyezés-szkriptek futtatásához szükséges mögöttes erőforrások létrehozásához, amelyek közé tartozik az Azure Container Instance, az Azure Storage-fiók és a felügyelt identitás szerepkör-hozzárendelései. A régebbi API-verziókban a felügyelt identitással hozhatók létre ezek az erőforrások.
> Az Azure-bejelentkezés újrapróbálkozási logikája már be van építve a burkoló szkriptbe. Ha ugyanabban a sablonban adja meg az engedélyeket, ahol az üzembe helyezési szkripteket futtatja. Az üzembe helyezési szkript szolgáltatás 10 percig, 10 másodperces időközzel újra bejelentkezik a felügyelt identitás szerepkör-hozzárendelésének replikálásáig.

## <a name="configure-the-minimum-permissions"></a>A minimális engedélyek konfigurálása

A 2020-10-01-es vagy újabb verziójú üzembe helyezési szkript api-ja esetén az üzembe helyezési rendszerbiztonsági tag az üzembe helyezési szkript végrehajtásához szükséges mögöttes erőforrások – egy tárfiók és egy Azure-tárolópéldány – létrehozására szolgál. Ha a szkriptnek hitelesítenie kell magát az Azure-ban, és Azure-specifikus műveleteket kell végrehajtania, javasoljuk, hogy a szkriptet egy felhasználó által hozzárendelt felügyelt identitással biztosítsa. A felügyelt identitásnak a szkriptben a művelet végrehajtásához szükséges hozzáféréssel kell rendelkezik.

A legkevesebb jogosultsággal rendelkező engedélyek konfiguráláshoz a következőre van szükség:

- Rendeljen hozzá egy egyéni szerepkört az alábbi tulajdonságokkal az üzembe helyezési rendszerbiztonsági taghoz:

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

  Ha az Azure Storage és az Azure Container Instance erőforrás-szolgáltatók még nincsenek regisztrálva, akkor a és a szolgáltatásokat is hozzá kell `Microsoft.Storage/register/action` `Microsoft.ContainerInstance/register/action` adni.

- Felügyelt identitás használata esetén az üzembe helyezési rendszerbiztonsági tagnak a felügyelt identitás-erőforráshoz rendelt **felügyelt** identitáskezelői szerepkörre (beépített szerepkörre) van szüksége.

## <a name="sample-templates"></a>Példasablonok

Az alábbi JSON egy példa. További információkért lásd a legújabb [sablonsémát.](/azure/templates/microsoft.resources/deploymentscripts)

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
> A példa szemléltető célokat szolgál. A és a tulajdonság nem használható együtt `scriptContent` `primaryScriptUri` egy sablonban.

> [!NOTE]
> A _scriptContent egy_ többsoros szkriptet mutat.  A Azure Portal és az Azure DevOps-folyamat nem tudja több sokkal együtt elemezni az üzembe helyezési szkriptet. A PowerShell-parancsokat (pontosvesszők vagy r _\\ \\ n_ vagy _\\ n_ használatával) egyetlen sorba láncolhatja, vagy használhatja a tulajdonságot egy külső `primaryScriptUri` szkriptfájllal. Számos ingyenes JSON-sztring escape/une escape eszköz érhető el. Például: [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Tulajdonságérték részletei:

- `identity`: Az üzembe helyezési szkript API 2020-10-01-es vagy újabb verziója esetén a felhasználó által hozzárendelt felügyelt identitás használata nem kötelező, kivéve, ha a szkriptben azure-specifikus műveleteket kell végrehajtania.  A 2019-10-01-preview API-verzióhoz felügyelt identitásra van szükség, mivel az üzembe helyezési szkriptszolgáltatás ezt használja a szkriptek végrehajtásához. Ha az identity tulajdonság meg van adva, a szkriptszolgáltatás a felhasználói szkript hívása előtt hívja meg `Connect-AzAccount -Identity` a hívást. Jelenleg csak a felhasználó által hozzárendelt felügyelt identitás támogatott. Ha másik identitással szeretne bejelentkezni, hívja meg a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsprogramot a szkriptben.
- `kind`: Adja meg a parancsfájl típusát. Jelenleg a Azure PowerShell Azure CLI-szkriptek támogatottak. Az értékek az **AzurePowerShell és** az **AzureCLI.**
- `forceUpdateTag`: Ha ezt az értéket a sablontelepítések között módosítja, az újra végre kell hajtania az üzembe helyezési szkriptet. Ha a vagy a függvényt használja, mindkét függvény csak egy paraméter `newGuid()` `utcNow()` alapértelmezett értékében használható. További információ: [Szkript futtatása egynél több alkalommal.](#run-script-more-than-once)
- `containerSettings`: Adja meg az Azure Container Instance testreszabási beállításait. Az üzembe helyezési szkripthez egy új Azure Container Instance szükséges. Meglévő Azure Container Instance-példányt nem lehet megadni. A tárolócsoport nevét azonban testreszabhatja a `containerGroupName` használatával. Ha nincs megadva, a csoport neve automatikusan létrejön.
- `storageAccountSettings`: Adja meg a meglévő tárfiókok használatára vonatkozó beállításokat. Ha `storageAccountName` nincs megadva, a rendszer automatikusan létrehoz egy tárfiókot. Lásd: [Meglévő tárfiók használata.](#use-existing-storage-account)
- `azPowerShellVersion`/`azCliVersion`: Adja meg a használni kívánt modulverziót. Tekintse meg a támogatott [Azure PowerShell listáját.](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) Tekintse meg a támogatott [Azure CLI-verziók listáját.](https://mcr.microsoft.com/v2/azure-cli/tags/list)

  >[!IMPORTANT]
  > Az üzembe helyezési szkript a rendelkezésre álló CLI-rendszerképeket használja Microsoft Container Registry (MCR). Egy CLI-rendszerkép tanúsítása az üzembe helyezési szkripthez körülbelül egy hónapba telik. Ne használja a 30 napon belül kiadott CLI-verziókat. A rendszerképek kiadási dátumának megkeresésében tekintse meg az [Azure CLI kibocsátási megjegyzéseit.](/cli/azure/release-notes-azure-cli) Ha nem támogatott verziót használ, a hibaüzenet felsorolja a támogatott verziókat.

- `arguments`: Adja meg a paraméterértékeket. Az értékeket szóközök választják el egymástól.

  Az üzembehelyezési szkriptek sztringtömbökre osztjuk fel az argumentumokat a [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) rendszerhívás hívásával. Erre a lépésre azért van szükség, [](/rest/api/container-instances/containergroups/createorupdate#containerexec) mert az argumentumok parancstulajdonságként vannak átkényzve az Azure Container Instance-nek, és a parancstulajdonság egy sztringtömb.

  Ha az argumentumok escape-karaktereket tartalmaznak, a [JsonEscaper](https://www.jsonescaper.com/) használatával duplázhatja a karaktereket. Illessze be az eredeti escape-karakteres sztringet az eszközbe, majd válassza az **Escape (Escape) lehetőséget.**  Az eszköz egy dupla escape-karakteres sztringet ad vissza. Az előző mintasablonban például a Argumentum a `-name \"John Dole\"` . Az escape-karakteres sztring `-name \\\"John Dole\\\"` a következő: .

  Ha egy objektum típusú ARM-sablonparamétert argumentumként kell átadni, konvertálja az objektumot sztringgé a [string()](./template-functions-string.md#string) függvény használatával, majd a [replace()](./template-functions-string.md#replace) függvény használatával cserélje le bármelyiket a értékre. `\"` `\\\"` Például:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  További információért tekintse meg a [mintasablont.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)

- `environmentVariables`: Adja meg a szkriptnek átadni kívánt környezeti változókat. További információ: [Üzembe helyezési szkriptek fejlesztése.](#develop-deployment-scripts)
- `scriptContent`: Adja meg a szkript tartalmát. Külső szkript futtatásához használja a `primaryScriptUri` parancsprogramot. Példákért lásd: Beágyazott szkript [használata](#use-inline-scripts) és [Külső szkript használata.](#use-external-scripts)
- `primaryScriptUri`: Adjon meg egy nyilvánosan elérhető URL-címet az elsődleges telepítési szkripthez, támogatott fájlkiterjesztésekkel. További információ: [Külső szkriptek használata.](#use-external-scripts)
- `supportingScriptUris`: Adja meg a vagy a által hívott támogató fájlok nyilvánosan elérhető URL-címeit `scriptContent` tartalmazó `primaryScriptUri` tömböt. További információ: [Külső szkriptek használata.](#use-external-scripts)
- `timeout`: Adja meg az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)formátumban megadott maximális engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett érték **a P1D.**
- `cleanupPreference`. Adja meg az üzembe helyezési erőforrások tisztításának preferencia-beállítását, amikor a szkript végrehajtása terminálállapotba kerül. Az alapértelmezett beállítás **a Mindig**, ami azt jelenti, hogy a terminál állapota ellenére törli az erőforrásokat (Sikeres, Sikertelen, Megszakítva). További információ: Az üzembe [helyezési parancsfájl erőforrásainak megtisztítása.](#clean-up-deployment-script-resources)
- `retentionInterval`: Adja meg, hogy a szolgáltatás milyen időközönként őrizze meg az üzembe helyezési szkript erőforrásait, miután az üzembe helyezési szkript végrehajtása elérte a terminálállapotot. Az üzembe helyezési szkript erőforrásai az időtartam lejártakor törlődnek. Az időtartam az [ISO 8601 mintán alapul.](https://en.wikipedia.org/wiki/ISO_8601) A megőrzési időtartam 1 és 26 óra között van (PT26H). Ez a tulajdonság akkor `cleanupPreference` használatos, ha **OnExpiration (Eltördítéskor) van beállítva.** További információ: Az üzembe [helyezési parancsfájl erőforrásainak megtisztítása.](#clean-up-deployment-script-resources)

### <a name="additional-samples"></a>További minták

- [1. minta:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)kulcstartó létrehozása és üzembe helyezési szkript használata tanúsítvány kulcstartóhoz való hozzárendeléséhez.
- [2.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)minta: hozzon létre egy erőforráscsoportot az előfizetés szintjén, hozzon létre egy kulcstartót az erőforráscsoportban, majd az üzembe helyezési szkripttel rendeljen hozzá egy tanúsítványt a kulcstartóhoz.
- [3. minta:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, rendelje hozzá a közreműködői szerepkört az identitáshoz az erőforráscsoport szintjén, hozzon létre egy kulcstartót, majd az üzembe helyezési szkripttel rendeljen hozzá egy tanúsítványt a kulcstartóhoz.

## <a name="use-inline-scripts"></a>Beágyazott szkriptek használata

Az alábbi sablonban egy erőforrás van meghatározva a `Microsoft.Resources/deploymentScripts` típussal. A kiemelt rész a beágyazott szkript.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Mivel a beágyazott üzembe helyezési szkriptek idézőjelek között vannak, az üzembe helyezési szkriptek sztringjéhez egy perjelet **(&#92;**) kell használni, vagy a sztringeket akként kell használni. Érdemes lehet sztringhelyettesítést is használni, ahogyan az előző JSON-mintában látható.

A szkript egy paramétert vesz fel, és kiírta a paraméter értékét. `DeploymentScriptOutputs` A a kimenetek tárolására használható. A kimenetek szakaszban a sor bemutatja, hogyan férhet `value` hozzá a tárolt értékekhez. `Write-Output` A hibakeresési céllal használható. A kimeneti fájl elérésével kapcsolatos további információkért lásd az üzembe helyezési szkriptek figyelése és [hibaelhárítása témakört.](#monitor-and-troubleshoot-deployment-scripts) A tulajdonságleírásokat lásd: [Mintasablonok.](#sample-templates)

A szkript futtatásához válassza a Próbálja **ki gombra** a Cloud Shell megnyitásához, majd illessze be a következő kódot a rendszerhéj paneljére.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

A kimenet a következőképpen fog kinézni:

![Resource Manager üzembehely helyezési szkript hello world kimenete](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Külső szkriptek használata

A beágyazott szkriptek mellett külső szkriptfájlokat is használhat. Csak a _ps1_ fájlkiterjesztésű elsődleges PowerShell-parancsfájlok támogatottak. A CLI-szkriptek számára az elsődleges szkriptek bármilyen kiterjesztéssel (vagy anélkül) használhatók, ha a szkriptek érvényes Bash-szkriptek. Külső szkriptfájlokhoz cserélje le a helyére a `scriptContent` `primaryScriptUri` következőt: . Például:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

További információért tekintse meg a [példasablont.](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)

A külső szkriptfájloknak elérhetőnek kell lenniük. Az Azure Storage-fiókokban tárolt szkriptfájlok biztonságának biztosítása érdekében hozzon létre egy SAS-jogkivonatot, és foglalja bele a sablon URI-jában. Állítsa be a lejárati időt úgy, hogy elegendő időt hagyjon az üzembe helyezés befejezésére. További információ: [Privát ARM-sablon üzembe helyezése SAS-jogkivonattal.](./secure-template-with-sas-token.md)

Az Ön felelőssége, hogy biztosítsa az üzembehelyezés parancsfájlja által hivatkozott szkriptek integritását ( `primaryScriptUri` vagy `supportingScriptUris` ). Csak megbízható szkriptekre hivatkozhat.

## <a name="use-supporting-scripts"></a>Támogató szkriptek használata

A bonyolult logikákat egy vagy több támogató szkriptfájlra különheti el. A tulajdonság lehetővé teszi, hogy szükség esetén URI-k tömbjeként adja meg a támogató `supportingScriptUris` szkriptfájlokat:

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

A támogató szkriptfájlok beágyazott és elsődleges parancsfájlból is hívhatóak. A parancsfájlok támogatása nem korlátozza a fájlkiterjesztést.

A rendszer a futtatáskor átmásolja a támogató `azscripts/azscriptinput` fájlokat a fájlba. A relatív elérési út használatával hivatkozhat a beágyazott és az elsődleges parancsfájlok támogató fájljaira.

## <a name="work-with-outputs-from-powershell-script"></a>PowerShell-szkript kimenetének használata

Az alábbi sablon bemutatja, hogyan lehet értékeket átadni két erőforrás `deploymentScripts` között:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

Az első erőforrásban definiál egy nevű változót, és annak használatával tárolja `$DeploymentScriptOutputs` a kimeneti értékeket. A sablonon belüli másik erőforrás kimeneti értékének eléréséhez használja a következőt:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>A CLI-szkript kimenetének használata

A PowerShell üzembe helyezési szkriptjétől eltérő módon a CLI-/Bash-támogatás nem teszi elérhetővé a szkriptkimenetek tárolására használt gyakori változókat, hanem egy nevű környezeti változót, amely a szkript kimeneti fájljának helyét `AZ_SCRIPTS_OUTPUT_PATH` tárolja. Ha egy üzembe helyezési szkriptet egy Resource Manager sablonból futtat, ezt a környezeti változót a Bash-rendszerhéj automatikusan beállít önnek. A értéke `AZ_SCRIPTS_OUTPUT_PATH` */mnt/azscripts/azscriptoutput/scriptoutputs.jsa következőn:*.

Az üzembe helyezési szkript kimenetét a helyre kell menteni, és a kimenetnek egy érvényes `AZ_SCRIPTS_OUTPUT_PATH` JSON-sztringobjektumnak kell lennie. A fájl tartalmát kulcs-érték párként kell menteni. A sztringek tömbje például a következőként van `{ "MyResult": [ "foo", "bar"] }` tárolva: .  A csak a tömberedmények (például ) `[ "foo", "bar" ]` tárolása érvénytelen.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

Az előző mintában a [jq-t](https://stedolan.github.io/jq/) használtuk. A tároló rendszerképeket is tartalmaz. Lásd: [A fejlesztési környezet konfigurálása.](#configure-development-environment)

## <a name="use-existing-storage-account"></a>Meglévő tárfiók használata

A szkriptek végrehajtáshoz és hibaelhárításhoz tárfiókra és tárolópéldányra van szükség. Megadhat egy meglévő tárfiókot, ellenkező esetben a szkriptszolgáltatás automatikusan létrehoz egy tárfiókot és a tárolópéldányt. A meglévő tárfiókok használatának követelményei:

- A támogatott tárfiók-fajtái a következőek:

    | Termékváltozat             | Támogatott kind (támogatott)     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage (Fájltartó)        |
    | Premium_ZRS     | FileStorage (Fájltartó)        |
    | Standard_GRS    | Storage, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage, StorageV2 |
    | Standard_RAGRS  | Storage, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Ezek a kombinációk támogatják a fájlmegosztásokat. További információ: [Azure-fájlmegosztás létrehozása](../../storage/files/storage-how-to-create-file-share.md) és [Tárfiókok típusai.](../../storage/common/storage-account-overview.md)

- A tárfiók tűzfalszabálya még nem támogatott. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md).
- Az üzembe helyezési rendszerbiztonsági tagnak rendelkeznie kell a tárfiók kezeléséhez szükséges engedélyekkel, beleértve a fájlmegosztások olvasását, létrehozására és törlését.

Meglévő tárfiók megadásához adja hozzá a következő JSON-t a `Microsoft.Resources/deploymentScripts` tulajdonságelemhez:

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: adja meg a tárfiók nevét.
- `storageAccountKey`: adja meg az egyik tárfiókkulcsot. A kulcs [lekérése a listKeys()](./template-functions-resource.md#listkeys) függvény használatával használhatja. Például:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Teljes [definíciós minta:](#sample-templates) `Microsoft.Resources/deploymentScripts` Mintasablonok.

Meglévő tárfiók használatakor a szkriptszolgáltatás létrehoz egy fájlmegosztást egyedi névvel. Az [üzembe helyezési szkripterőforrások megtisztítása a](#clean-up-deployment-script-resources) parancsfájl-szolgáltatás fájlmegosztásának tisztítását lásd: Az üzembe helyezési parancsfájl erőforrásainak megtisztítása.

## <a name="develop-deployment-scripts"></a>Üzembe helyezési szkriptek fejlesztése

### <a name="handle-non-terminating-errors"></a>Megszakítást nem végződő hibák eltávolítása

Az üzembe helyezési szkript változójának használatával szabályozhatja, hogy a PowerShell hogyan reagáljon a megszakítást nem végződő `$ErrorActionPreference` hibákra. Ha a változó nincs beállítva az üzembe helyezési szkriptben, a szkriptszolgáltatás az alapértelmezett Continue értéket **használja.**

A szkriptszolgáltatás Sikertelenre állítja  az erőforrás-kiépítési állapotot, ha a szkript a beállítása ellenére hibát `$ErrorActionPreference` jelez.

### <a name="use-environment-variables"></a>Környezeti változók használata

Az üzembe helyezési szkript a következő környezeti változókat használja:

|Környezeti változó|Alapértelmezett érték|Rendszer fenntartva|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration (Eltördítés alatt)|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLI: userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.jsbe|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.jsbe|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

A használatával kapcsolatos további információkért `AZ_SCRIPTS_OUTPUT_PATH` lásd: [A CLI-szkript kimenetének használata.](#work-with-outputs-from-cli-script)

### <a name="pass-secured-strings-to-deployment-script"></a>Biztonságos sztringek bérlete az üzembe helyezési szkriptnek

A környezeti változók (EnvironmentVariable) tárolópéldányban való beállítása lehetővé teszi a tároló által futtatott alkalmazás vagy szkript dinamikus konfigurációját. Az üzembe helyezési szkript ugyanúgy kezeli a nem biztonságos és biztonságos környezeti változókat, mint az Azure Container Instance-t. További információ: [Set environment variables in container instances (Környezeti változók beállítása tárolópéldányban).](../../container-instances/container-instances-environment-variables.md#secure-values) Példaként tekintse meg a [mintasablonokat.](#sample-templates)

A környezeti változók maximálisan engedélyezett mérete 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Üzembehelyi telepítési szkriptek figyelése és hibaelhárítása

A szkriptszolgáltatás létrehoz egy [tárfiókot](../../storage/common/storage-account-overview.md) (hacsak nem ad meg egy meglévő tárfiókot) és egy tárolópéldányt [a](../../container-instances/container-instances-overview.md) szkriptek végrehajtáshoz. Ha ezeket az erőforrásokat a szkriptszolgáltatás automatikusan hozta létre, mindkét erőforrás utótagja az `azscripts` erőforrásnevekben lesz.

![Resource Manager üzembehely helyezési szkript erőforrásnevének létrehozása](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

A felhasználói szkript, a végrehajtási eredmények és az stdout fájl a tárfiók fájlmegosztásában van tárolva. Van egy nevű `azscripts` mappa. A mappában két további mappa található a bemeneti és a kimeneti fájlokhoz: `azscriptinput` és `azscriptoutput` .

A kimeneti mappa tartalmaz egy _executionresult.jsés_ a szkript kimeneti fájlját. A szkriptvégrehajtási hibaüzenet a következő _executionresult.jsjelenik meg:_. A kimeneti fájl csak akkor jön létre, ha a szkript sikeresen végre lett hajtva. A bemeneti mappa tartalmaz egy rendszer PowerShell-parancsfájlt és a felhasználótelepítési parancsfájlfájlokat. Lecserélheti a felhasználói üzembe helyezési szkriptfájlt egy módosított fájlra, és újra futtathatja az üzembe helyezési szkriptet az Azure Container Instance-ről.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Miután üzembe helyezett egy üzembe helyezési szkripterőforrást, az erőforrás megjelenik az erőforráscsoportban a Azure Portal. Az alábbi képernyőképen egy üzembe **helyezési** szkripterőforrás Áttekintés lapja látható:

![Resource Manager üzembe helyezési szkriptportál áttekintése](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Az áttekintő oldal fontos információkat jelenít meg az erőforrásról, például a **kiépítési** állapotról, a **Tárfiókról,** a **Tárolópéldányról** és a **Naplókról.**

A bal oldali menüben megtekintheti az üzembe helyezési szkript tartalmát, a szkriptnek átadott argumentumokat és a kimenetet. Az üzembe helyezési szkript sablonját is exportálhatja, beleértve az üzembe helyezési szkriptet is.

### <a name="use-powershell"></a>A PowerShell használata

A Azure PowerShell előfizetés vagy erőforráscsoport hatókörében kezelheti az üzembe helyezési szkripteket:

- [Get-AzDeploymentScript: Lekért](/powershell/module/az.resources/get-azdeploymentscript)vagy listázza az üzembe helyezési szkripteket.
- [Get-AzDeploymentScriptLog: Lekérte](/powershell/module/az.resources/get-azdeploymentscriptlog)az üzembe helyezési szkript végrehajtásának naplóját.
- [Remove-AzDeploymentScript:](/powershell/module/az.resources/remove-azdeploymentscript)Eltávolít egy üzembe helyezési szkriptet és a hozzá tartozó erőforrásokat.
- [Save-AzDeploymentScriptLog:](/powershell/module/az.resources/save-azdeploymentscriptlog)Az üzembe helyezési szkript végrehajtásának naplóját lemezre menti.

A `Get-AzDeploymentScript` kimenet a következőre hasonlít:

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

Az Azure CLI használatával előfizetésben vagy erőforráscsoporti hatókörben kezelheti az üzembe helyezési szkripteket:

- [az deployment-scripts delete:](/cli/azure/deployment-scripts#az-deployment-scripts-delete)Üzembe helyezési szkript törlése.
- [az deployment-scripts list:](/cli/azure/deployment-scripts#az-deployment-scripts-list)List all deployment scripts.
- [az deployment-scripts show:](/cli/azure/deployment-scripts#az-deployment-scripts-show)Üzembe helyezési szkript lekérése.
- [az deployment-scripts show-log:](/cli/azure/deployment-scripts#az-deployment-scripts-show-log)Show deployment script logs.

A list parancs kimenete az alábbihoz hasonló:

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

### <a name="use-rest-api"></a>A REST API használata

Az üzembe helyezési szkript erőforrás üzembe helyezési információit az erőforráscsoport és az előfizetés szintjén is lekérte a következő REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

Az alábbi példa az [ARMClientet használja:](https://github.com/projectkudu/ARMClient)

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

Ez csak az üzembe helyezési szkript erőforrásainak törlése előtt működik.

A deploymentScripts erőforrást a portálon a **Rejtett típusok megjelenítése lehetőséget választva láthatja:**

![Resource Manager sablontelepítési szkript, rejtett típusok megjelenítése, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Az üzembe helyezési szkript erőforrásainak megtisztítása

A szkriptek végrehajtáshoz és hibaelhárításhoz tárfiókra és tárolópéldányra van szükség. Megadhat egy meglévő tárfiókot, ellenkező esetben a szkriptszolgáltatás automatikusan létrehoz egy tárfiókot és egy tárolópéldányt. A szkriptszolgáltatás törli a két automatikusan létrehozott erőforrást, amikor az üzembe helyezési szkript végrehajtása terminálállapotba kerül. Az erőforrásokért a törlésig kell fizetni. Az árakkal kapcsolatos információkért lásd [a Container Instances](https://azure.microsoft.com/pricing/details/container-instances/) [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)díjszabását.

Ezen erőforrások életciklusát a sablon következő tulajdonságai vezérlik:

- `cleanupPreference`: Tisztítási beállítás, ha a szkript végrehajtása terminálállapotba kerül. A támogatott értékek a következőek:

  - **Mindig:** Törölje az automatikusan létrehozott erőforrásokat, ha a szkript végrehajtása terminálállapotba kerül. Meglévő tárfiók használata esetén a szkriptszolgáltatás törli a tárfiókban létrehozott fájlmegosztást. Mivel az erőforrás továbbra is jelen lehet az erőforrások törlése után, a parancsfájl-szolgáltatás megőrzi a szkriptvégrehajtás eredményeit, például az stdout, a kimenetek és a visszaadott értéket az erőforrások `deploymentScripts` törlése előtt.
  - **OnSuccess:** Csak akkor törölje az automatikusan létrehozott erőforrásokat, ha a szkript végrehajtása sikeres volt. Meglévő tárfiók használata esetén a szkriptszolgáltatás csak akkor távolítja el a fájlmegosztást, ha a szkript végrehajtása sikeres volt. Továbbra is elérheti az erőforrásokat, hogy megtalálja a hibakeresési információkat.
  - **OnExpiration**: Csak akkor törölje az automatikusan létrehozott erőforrásokat, ha `retentionInterval` a beállítás lejárt. Meglévő tárfiók használata esetén a szkriptszolgáltatás eltávolítja a fájlmegosztást, de megtartja a tárfiókot.

- `retentionInterval`: Adja meg azt az intervallumot, amely után egy szkripterőforrás meg lesz őrizve, és amely után a rendszer lejár és törölve lesz.

> [!NOTE]
> Nem ajánlott a szkriptszolgáltatás által más célokra létrehozott tárfiókot és tárolópéldányt használni. A szkript életciklusától függően előfordulhat, hogy a két erőforrás el lesz távolítva.

A tárolópéldány és a tárfiók a következő szerint `cleanupPreference` törlődik: . Ha azonban a szkript sikertelen, és nincs Always (Mindig) beállításra állítva, az üzembe helyezési folyamat egy órán keresztül automatikusan futtatja `cleanupPreference` a tárolót.  Ebben az órában a szkript hibaelhárítását is használhatja. Ha a sikeres üzembe helyezés után is futni szeretné a tárolót, adjon hozzá egy alvó lépést a szkripthez. Például adja hozzá [a Start-Sleep parancsprogramot](/powershell/module/microsoft.powershell.utility/start-sleep) a szkript véghez. Ha nem adja hozzá az alvó lépést, a tároló terminálállapotra lesz beállítva, és akkor sem érhető el, ha még nem lett törölve.

## <a name="run-script-more-than-once"></a>Szkript futtatása egynél több alkalommal

Az üzembe helyezési szkript végrehajtása idepotent művelet. Ha az erőforrás tulajdonságai (beleértve a beágyazott szkriptet is) egyike sem módosul, a szkript nem lesz végrehajtva a sablon `deploymentScripts` ismételt üzembe végrehajtásához. Az üzembe helyezési szkript szolgáltatás összehasonlítja a sablonban lévő erőforrásneveket az ugyanabban az erőforráscsoportban lévő meglévő erőforrásokkal. Ha ugyanazt az üzembe helyezési szkriptet többször is végre szeretné hajtani, két lehetőség áll rendelkezésre:

- Módosítsa az erőforrás `deploymentScripts` nevét. Használja például az [utcNow](./template-functions-date.md#utcnow) sablonf függvényt az erőforrás neveként vagy az erőforrás nevének részeként. Az erőforrás nevének módosítása új erőforrást `deploymentScripts` hoz létre. Jó, ha a szkriptek végrehajtási előzményeit tartja meg.

    > [!NOTE]
    > A függvény csak egy paraméter `utcNow` alapértelmezett értékében használható.

- Adjon meg egy másik értéket a `forceUpdateTag` sablontulajdonságban. Használja például a `utcNow` értéket értékként.

> [!NOTE]
> Írja meg az idempotent telepítési szkripteket. Ez biztosítja, hogy ha véletlenül újra futnak, az nem okoz rendszerváltozást. Ha például az üzembe helyezési szkripttel hoz létre egy Azure-erőforrást, ellenőrizze, hogy az erőforrás nem létezik-e a létrehozása előtt, hogy a szkript sikeres legyen, vagy ne hozza létre újra az erőforrást.

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

Üzembe helyezési szkript fejlesztőkörnyezeteként előre konfigurált tárolórendszerképet is használhat. További információ: [Configure development environment for deployment scripts in templates (Fejlesztési környezet konfigurálása üzembe helyezési szkriptek számára a sablonokban).](./deployment-script-template-configure-dev.md)

A szkript sikeres tesztelése után használhatja üzembe helyezési szkriptként a sablonokban.

## <a name="deployment-script-error-codes"></a>Üzembehelyi szkript hibakódja

| Hibakód | Leírás |
|------------|-------------|
| DeploymentScriptInvalidOperation | A sablonban található üzembe helyezési szkript erőforrás-definíciója érvénytelen tulajdonságneveket tartalmaz. |
| DeploymentScriptResourceConflict | Nem lehet törölni egy nem termináli állapotban található üzembe helyezési szkripterőforrást, és a végrehajtás nem haladta meg az 1 órát. Vagy nem futtathatja újra ugyanazt az üzembe helyezési szkriptet ugyanazokkal az erőforrás-azonosítóval (ugyanaz az előfizetés, erőforráscsoport-név és erőforrásnév), de különböző szkript törzstartalmakkal egyidejűleg. |
| DeploymentScriptOperationFailed | Az üzembe helyezési szkript művelete belsőleg meghiúsult. Forduljon a Microsoft ügyfélszolgálatához. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | A hozzáférési kulcs nem lett megadva a meglévő tárfiókhoz.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Az üzembe helyezési szkriptszolgáltatás által létrehozott tárolócsoport külsőleg módosult, és érvénytelen tárolókat adott hozzá. |
| DeploymentScriptContainerGroupInNonterminalState | Két vagy több üzembe helyezési szkripterőforrás ugyanazt az Azure-tárolópéldánynevet használja ugyanabban az erőforráscsoportban, és az egyik még nem fejezte be a végrehajtását. |
| DeploymentScriptStorageAccountInvalidKind | A BlobBlobStorage vagy BlobStorage típusú meglévő tárfiók nem támogatja a fájlmegosztásokat, és nem használható. |
| DeploymentScriptStorageAccountInvalidKindAndSku | A meglévő tárfiók nem támogatja a fájlmegosztásokat. A támogatott tárfiókok fajtáiért lásd: [Meglévő tárfiók használata.](#use-existing-storage-account) |
| DeploymentScriptStorageAccountNotFound | A tárfiók nem létezik, vagy egy külső folyamat vagy eszköz törölte. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | A megadott tárfiók rendelkezik szolgáltatásvégponttal. A szolgáltatásvégponttal nem használható tárfiók. |
| DeploymentScriptStorageAccountInvalidAccessKey | Érvénytelen hozzáférési kulcs van megadva a meglévő tárfiókhoz. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Érvénytelen tárfiókkulcs-formátum. Lásd: [Manage storage account access keys (Tárfiók hozzáférési kulcsának kezelése).](../../storage/common/storage-account-keys-manage.md) |
| DeploymentScriptExceededMaxAllowedTime | Az üzembe helyezési szkript végrehajtási ideje túllépte az üzembe helyezési szkript erőforrás-definíciójában megadott időtúllépési értéket. |
| DeploymentScriptInvalidOutputs | Az üzembe helyezési szkript kimenete nem érvényes JSON-objektum. |
| DeploymentScriptContainerInstancesServiceLoginFailure | A felhasználó által hozzárendelt felügyelt identitás 10 kísérlet után, 1 perces időközzel nem tudott bejelentkezni. |
| DeploymentScriptContainerGroupNotFound | Az üzembe helyezési szkriptszolgáltatás által létrehozott tárolócsoportot egy külső eszköz vagy folyamat törölte. |
| DeploymentScriptDownloadFailure | Nem sikerült letölteni egy támogató szkriptet. Lásd: [Támogató szkript használata.](#use-supporting-scripts)|
| DeploymentScriptError | A felhasználói szkript hibát jelez. |
| DeploymentScriptBootstrapScriptExecutionFailed | A bootstrap-szkript hibát jelez. A Bootstrap-szkript az üzembehelyési szkript végrehajtását vezénylő rendszerszk szkript. |
| DeploymentScriptExecutionFailed | Ismeretlen hiba történt az üzembe helyezési szkript végrehajtása során. |
| DeploymentScriptContainerInstancesServiceUnavailable | Az Azure Container Instance (ACI) létrehozásakor az ACI hibát jelez, amely miatt a szolgáltatás nem érhető el. |
| DeploymentScriptContainerGroupInNonterminalState | Az Azure Container Instance (ACI) létrehozásakor egy másik üzembe helyezési szkript ugyanazt az ACI-nevet használja ugyanabban a hatókörben (ugyanaz az előfizetés, erőforráscsoport neve és erőforrásnév). |
| DeploymentScriptContainerGroupNameInvalid | A megadott Azure Container Instance Name (ACI) nem felel meg az ACI követelményeinek. Lásd: [Gyakori problémák elhárítása a Azure Container Instances.](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment)|

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja az üzembe helyezési szkripteket. Az üzembe helyezési szkriptek oktatóanyagának részletes útmutatója:

> [!div class="nextstepaction"]
> [Oktatóanyag: Üzembe helyezési szkriptek használata Azure Resource Manager sablonokban](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Learn-modul: ARM-sablonok kiterjesztése üzembe helyezési szkriptek használatával](/learn/modules/extend-resource-manager-template-deployment-scripts/)

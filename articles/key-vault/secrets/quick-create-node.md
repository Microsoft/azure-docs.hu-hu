---
title: Gyors útmutató – Azure Key Vault titkos ügyféloldali kódtár JavaScripthez (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat egy Azure Key vaultból a JavaScript ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 70416daced2cbdebb70fb8e1defbcbcb599710f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705483"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Gyors útmutató: Azure Key Vault titkos ügyféloldali kódtár a JavaScripthez (4-es verzió)

Ismerkedjen meg a JavaScript Azure Key Vault Secret ügyféloldali függvénytárával. A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkok számára. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat egy Azure Key vaultból a JavaScript ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciák dokumentációja](/javascript/api/overview/azure/key-vault-index)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/keyvault-secrets)

A Key Vault és a titkokkal kapcsolatos további információkért lásd:
- [Key Vault áttekintése](../general/overview.md)
- A [titkok áttekintése](about-secrets.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Az operációs rendszer jelenlegi [Node.js](https://nodejs.org) .
- [Azure CLI](/cli/azure/install-azure-cli)
- A Key Vault létrehozhat egyet [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)használatával, vagy [Azure PowerShell](../general/quick-create-powershell.md)

Ez a rövid útmutató feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli)-t futtatja.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

    Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

## <a name="create-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Ezután hozzon létre egy Node.js alkalmazást, amely üzembe helyezhető a felhőben. 

1. A parancs-rendszerhéjban hozzon létre egy nevű mappát `key-vault-node-app` :

```terminal
mkdir key-vault-node-app
```

1. Váltson az újonnan létrehozott *Key-Vault-Node-app* könyvtárra, és futtassa az "init" parancsot a Node projekt inicializálásához:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault csomagok telepítése

A konzol ablakban telepítse a Node.js Azure Key Vault [Secrets könyvtárát](https://www.npmjs.com/package/@azure/keyvault-secrets) .

```terminal
npm install @azure/keyvault-secrets
```

Az [Azure. Identity](https://www.npmjs.com/package/@azure/identity) csomag telepítése Key Vault hitelesítéséhez

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Környezeti változók beállítása

Ez az alkalmazás a Key Vault nevét használja a nevű környezeti változóként `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS vagy Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely titkos engedélyeket biztosít a felhasználói fióknak

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek megmutatják, hogyan hozhat létre egy ügyfelet, hogyan állíthat be titkos kulcsot, beolvashat egy titkos kulcsot, és törölhet egy titkos kulcsot. 

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

1. Új szövegfájl létrehozása és mentése a következőként: "index.js"

1. Az Azure-és Node.js-modulok betöltéséhez szükséges hívások hozzáadása

1. A program struktúrájának létrehozása, beleértve az alapszintű kivételek kezelését

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa az [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) ["DefaultAzureCredential ()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy a különböző környezetekben ugyanazt a kódot használja, különböző beállításokkal az identitás biztosításához. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Adja hozzá a következő kódot a "Main ()" függvényhez

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy az alkalmazás hitelesítése megtörtént, a [setSecret metódussal](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setSecret_string__string__SetSecretOptions_) a kulcstartóba helyezheti a titkos kulcsot – ebben a példában a "keresési kifejezésként" nevet kell használnia.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket a [getSecret metódussal](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

A titkos kód most már mentve van `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje és ürítse ki a titkos kulcsot a Key vaultból a [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) és a [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_) metódussal.

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Mintakód

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

1. Az alkalmazás futtatásához hajtsa végre a következő parancsokat.

    ```terminal
    npm install
    node index.js
    ```

1. Ha a rendszer kéri, adjon meg egy titkos értéket. Például: mySecretPassword.

    Megjelenik a következő kimenet egy változata:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, egy titkos kulcsot, és beolvasta ezt a titkot. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- [Azure Key Vault titkok áttekintése](about-secrets.md)
- [A Key vaulthoz való hozzáférés biztonságossá tétele](../general/secure-your-key-vault.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)

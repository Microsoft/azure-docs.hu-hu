---
title: Rövid útmutató – Azure Key Vault JavaScript titkos ügyféloldali kódtárának létrehozása (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet titkos kódokat egy Azure-kulcstartóból a JavaScript ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 3d8f20f598243754e3fd39f649e7c84d55b64817
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751669"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Rövid útmutató: Azure Key Vault JavaScript titkos ügyféloldali kódtárának létrehozása (4-es verzió)

A JavaScripthez Azure Key Vault titkos ügyféloldali kódtár első lépések. [Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a titkos kulcsok számára. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, olvashat be és törölhet titkos kódokat egy Azure Key Vaultból a JavaScript ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciadokumentáció](/javascript/api/overview/azure/key-vault-index)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Csomag (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

További információ a titkos Key Vault és a titkos kulcsokról:
- [Key Vault áttekintés](../general/overview.md)
- [Titkos kulcsok áttekintése.](about-secrets.md)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Aktuális [Node.js](https://nodejs.org) az operációs rendszer számára.
- [Azure CLI](/cli/azure/install-azure-cli)
- Egy Key Vault – létrehozhat egyet az [Azure CLI Azure Portal](../general/quick-create-cli.md) [](../general/quick-create-powershell.md) vagy a Azure PowerShell [](../general/quick-create-portal.md)

Ez a rövid útmutató feltételezi, hogy az [Azure CLI-t futtatja.](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és adja meg a terminálon [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

## <a name="create-new-nodejs-application"></a>Új Node.js létrehozása

Ezután hozzon létre egy Node.js, amely üzembe helyezhető a felhőben. 

1. A parancshéjban hozzon létre egy nevű `key-vault-node-app` mappát:

```terminal
mkdir key-vault-node-app
```

1. Váltsa át az újonnan *létrehozott key-vault-node-app* könyvtárat, és futtassa az "init" parancsot a node-projekt inicializálásához:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>A Key Vault csomagok telepítése

A konzolablakban telepítse a Azure Key Vault [titkos kódtárat](https://www.npmjs.com/package/@azure/keyvault-secrets) a Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Telepítse az [azure.identity](https://www.npmjs.com/package/@azure/identity) csomagot a hitelesítéshez egy Key Vault

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Környezeti változók beállítása

Ez az alkalmazás a kulcstartó nevét használja a nevű környezeti `KEY_VAULT_NAME` változóként.

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

## <a name="grant-access-to-your-key-vault"></a>Hozzáférés megadása a kulcstartóhoz

Hozzáférési szabályzat létrehozása a kulcstartóhoz, amely titkos engedélyeket ad a felhasználói fióknak

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódminták megmutatják, hogyan hozhat létre ügyfelet, állíthat be titkos kódot, olvashat be titkos kódot, és hogyan törölhet titkos kódot. 

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

1. Hozzon létre egy új szövegfájlt, és mentse "index.js"

1. Hívásokra van szükség az Azure és a Node.js hozzáadásához

1. A program struktúrájának létrehozása, beleértve az alapszintű kivételkezelést

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

### <a name="add-directives"></a>Direktíva hozzáadása

Adja hozzá a következő irányelveket a kód tetejéhez:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a key vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: Felügyelt identitás [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ebben a példában az Azure Identity [Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) osztályát használjuk, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használjuk különböző identitás-beállításokkal. További információ a Key Vault való hitelesítésről: [Fejlesztői útmutató.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Adja hozzá a következő kódot a main()' függvényhez

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Titkos fájl mentése

Most, hogy az alkalmazás hitelesítve van, a [setSecret](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_) metódussal titkos kulcsot is betehet a kulcstartóba. Ehhez a titkos kulcsnak nevet kell ad– ebben a példában a "mySecret" nevet használjuk.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Titkos adat lekérése

Most már lekérheti a korábban beállított értéket a [getSecret metódussal.](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_)

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

A titkos adatokat a rendszer a következőként `retrievedSecret.value` menti: .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül töröljük és töröljük a titkos adatokat a kulcstartóból [a beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) és a [purgeDeletedSecret metódussal.](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_)

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

1. Amikor a rendszer kéri, adjon meg egy titkos értéket. Például: mySecretPassword.

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

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy titkos kulcsot, és lekérte a titkos adatokat. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Olvassa el a [titkos Azure Key Vault áttekintését](about-secrets.md)
- [Kulcstartóhoz való hozzáférés biztonságossá tere](../general/security-overview.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- A biztonsági [Key Vault áttekintése](../general/security-overview.md)

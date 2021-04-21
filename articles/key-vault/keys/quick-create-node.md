---
title: Rövid útmutató – Azure Key Vault JavaScripthez (4-es verzió) használt fő ügyféloldali kódtár létrehozása
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet kulcsokat egy Azure-kulcstartóból a JavaScript ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 08de0045a19b0f07308778f8716fa48abded445b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815539"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Rövid útmutató: Azure Key Vault javascriptes ügyféloldali kódtár (4-es verzió) létrehozása

Első lépések a Azure Key Vault JavaScripthez való ügyféloldali kódtárával. [Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a titkosítási kulcsok számára. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, olvashat be és törölhet kulcsokat egy Azure Key Vaultból a JavaScript-kulcs ügyféloldali kódtárával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciadokumentáció](/javascript/api/overview/azure/key-vault-index)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Csomag (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

A kulcsokkal és kulcsokkal kapcsolatos Key Vault lásd:
- [Key Vault áttekintés](../general/overview.md)
- [Kulcsok áttekintése.](about-keys.md)

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

```azurecli
mkdir key-vault-node-app
```

1. Váltsa át az újonnan *létrehozott key-vault-node-app* könyvtárat, és futtassa az "init" parancsot a node-projekt inicializálásához:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>A Key Vault csomagok telepítése

A konzolablakban telepítse a Azure Key Vault [kulcstárát](https://www.npmjs.com/package/@azure/keyvault-keys) a Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Telepítse az [azure.identity](https://www.npmjs.com/package/@azure/identity) csomagot a hitelesítéshez egy Key Vault

```azurecli
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

Hozzáférési szabályzat létrehozása a kulcstartóhoz, amely kulcsengedélyeket ad a felhasználói fióknak

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódminták megmutatják, hogyan hozhat létre ügyfelet, állíthat be kulcsot, hogyan olvashatja be és törölheti a kulcsokat. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a key vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: Felügyelt identitás [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ebben a példában az Azure Identity [Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) osztályát használjuk, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használjuk különböző identitás-beállításokkal. További információ a Key Vaultban való hitelesítésről: [Fejlesztői útmutató.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Adja hozzá a következő kódot a main()' függvényhez

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Kulcs mentése

Most, hogy az alkalmazás hitelesítve van, a [createKey](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) metódussal egy kulcsot is be lehet tenni a kulcstartóba. A metódus paraméterei elfogadják a kulcsnevet és a [kulcstípust](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Kulcs lekérése

Most már lekérheti a korábban beállított értéket a [getKey metódussal.](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_)

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Kulcs törlése

Végül töröljük és töröljük a kulcsot a kulcstartóból [a beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) és [a purgeDeletedKey metódussal.](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_)

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Mintakód

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés

Az alkalmazás futtatásához hajtsa végre a következő parancsokat.

```azurecli
npm install
npm index.js
```

Megjelenik a következő kimenet egy változata:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy kulcsot, és lekérte azt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Olvassa el [az áttekintést a Azure Key Vault kulcsokról](about-keys.md)
- [Kulcstartóhoz való hozzáférés biztonságossá tere](../general/security-features.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)

---
title: Gyors útmutató – Azure Key Vault fő ügyféloldali kódtár a JavaScripthez (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet kulcsokat egy Azure Key vaultból a JavaScript ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: db6bb5c204bfe79b9d7470f651081aa4f4dcf2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97932707"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Gyors útmutató: Azure Key Vault fő ügyféloldali kódtár a JavaScripthez (4-es verzió)

Ismerkedjen meg a JavaScript Azure Key Vault Key ügyféloldali függvénytárával. A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkosítási kulcsokhoz. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, kérhet le és törölhet kulcsokat egy Azure Key vaultból a JavaScript-kulcs ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciák dokumentációja](/javascript/api/overview/azure/key-vault-index)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/keyvault-keys)

A Key Vault és a kulcsokkal kapcsolatos további információkért lásd:
- [Key Vault áttekintése](../general/overview.md)
- [Kulcsok áttekintése](about-keys.md).

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

```azurecli
mkdir key-vault-node-app
```

1. Váltson az újonnan létrehozott *Key-Vault-Node-app* könyvtárra, és futtassa az "init" parancsot a Node projekt inicializálásához:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault csomagok telepítése

A konzol ablakban telepítse a Node.js Azure Key Vault [kulcsok könyvtárát](https://www.npmjs.com/package/@azure/keyvault-keys) .

```azurecli
npm install @azure/keyvault-keys
```

Az [Azure. Identity](https://www.npmjs.com/package/@azure/identity) csomag telepítése Key Vault hitelesítéséhez

```azurecli
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

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely a felhasználói fiók számára biztosít kulcsfontosságú engedélyeket

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek bemutatják, hogyan hozhat létre egy ügyfelet, beállíthat egy kulcsot, lekérhet egy kulcsot, és törölhet egy kulcsot. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa az [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) ["DefaultAzureCredential ()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Adja hozzá a következő kódot a "Main ()" függvényhez

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Kulcs mentése

Most, hogy az alkalmazás hitelesítése megtörtént, elhelyezheti a kulcstartót a [createKey metódus](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) használatával, a metódus paraméterei elfogadják a kulcs nevét és a [kulcs típusát](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype) .

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Kulcs lekérése

Most már lekérheti a korábban beállított értéket a [getKey metódussal](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Kulcs törlése

Végül törölje és ürítse ki a kulcsot a Key vaultból a [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) és a [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_) metódussal.

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

Ebben a rövid útmutatóban létrehozott egy Key vaultot, tárolt egy kulcsot, és lekérte a kulcsot. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- [Azure Key Vault kulcsok áttekintésének](about-keys.md) beolvasása
- [A Key vaulthoz való hozzáférés biztonságossá tétele](../general/secure-your-key-vault.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)

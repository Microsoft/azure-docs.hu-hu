---
title: Rövid útmutató – Azure Key Vault JavaScripthez (4-es verzió) elérhető tanúsítvány ügyféloldali kódtára
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet tanúsítványokat egy Azure-kulcstartóból a JavaScript ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 3f933a564e62bf3aae1ec05c6dde048100c22967
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815845"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Rövid útmutató: Azure Key Vault JavaScripthez (4-es verzió) elérhető tanúsítvány ügyféloldali kódtára

A JavaScripthez Azure Key Vault tanúsítvány ügyféloldali kódtárának első lépések. [Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a tanúsítványok számára. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, olvashat be és törölhet tanúsítványokat egy Azure Key Vaultból a JavaScript ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciadokumentáció](/javascript/api/overview/azure/key-vault-index)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Csomag (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

További információ a tanúsítványokról Key Vault tanúsítványokról:
- [Key Vault áttekintés](../general/overview.md)
- [Tanúsítványok áttekintése.](about-certificates.md)

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

A konzolablakból telepítse a Azure Key Vault [tanúsítványtárát](https://www.npmjs.com/package/@azure/keyvault-certificates) a Node.js.

```azurecli
npm install @azure/keyvault-certificates
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

Hozzáférési szabályzat létrehozása a kulcstartóhoz, amely tanúsítványengedélyeket ad a felhasználói fióknak

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódminták megmutatják, hogyan hozhat létre ügyfelet, állíthat be tanúsítványt, olvashat be tanúsítványt, és hogyan törölhet tanúsítványt. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a key vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: Felügyelt identitás [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ebben a példában az Azure Identity [Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) osztályát használjuk, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használjuk különböző identitás-beállításokkal. További információ a Key Vaultban való hitelesítésről: [Fejlesztői útmutató.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Adja hozzá a következő kódot a main()' függvényhez

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Most, hogy az alkalmazás hitelesítve van, a [beginCreateCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) metódussal tanúsítványt is betehet a[](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) kulcstartóba. Ez megköveteli a tanúsítvány nevét és a tanúsítványházi házirend tanúsítvány-házirendjéhez a tanúsítványházi házirend tulajdonságait. [](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Ha létezik tanúsítványnév, a fenti kód létrehozza a tanúsítvány új verzióját.
### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

Most már lekérheti a korábban beállított értéket a [getCertificate metódussal.](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption)

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

Végül töröljük és töröljük a tanúsítványt a kulcstartóból a [beginDeleteCertificate] és https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) [a purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) metódussal.

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Mintakód

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy tanúsítványt, és lekérte a tanúsítványt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Olvassa el a [tanúsítványok áttekintését](about-certificates.md)
- Hozzáférési útmutató [Key Vault App Service alkalmazásból](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférés-Key Vault virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)

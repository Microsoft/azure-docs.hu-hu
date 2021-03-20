---
title: Gyors útmutató – Azure Key Vault tanúsítvány ügyféloldali kódtára a JavaScripthez (4-es verzió)
description: Ismerje meg, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure Key vaultból a JavaScript ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1064c6a1e2dddaae98e94ccceca7b1d550897393
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930854"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Gyors útmutató: Azure Key Vault tanúsítvány ügyféloldali kódtára a JavaScripthez (4-es verzió)

Ismerkedjen meg az Azure Key Vault Certificate Client Library for JavaScript szolgáltatással. A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a tanúsítványokhoz. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure Key vaultból a JavaScript ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciák dokumentációja](/javascript/api/overview/azure/key-vault-index)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/keyvault-certificates)

A Key Vault és a tanúsítványokról további információt a következő témakörben talál:
- [Key Vault áttekintése](../general/overview.md)
- A [tanúsítványok áttekintése](about-certificates.md).

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

A konzol ablakban telepítse a Node.js Azure Key Vault [tanúsítványok könyvtárát](https://www.npmjs.com/package/@azure/keyvault-certificates) .

```azurecli
npm install @azure/keyvault-certificates
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

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely engedélyt ad a felhasználói fióknak.

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek bemutatják, hogyan hozhat létre ügyfelet, beállíthat tanúsítványt, kérhet le egy tanúsítványt, és törölhet egy tanúsítványt. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa az [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) ["DefaultAzureCredential ()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Adja hozzá a következő kódot a "Main ()" függvényhez

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Most, hogy az alkalmazás hitelesítése megtörtént, a [beginCreateCertificate metódussal](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) a kulcstartóba helyezheti a tanúsítványt, és a[tanúsítvány házirend-](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) házirendjének [tulajdonságai](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Ha a tanúsítvány neve létezik, a fenti kód a tanúsítvány új verzióját fogja létrehozni.
### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

Most már lekérheti a korábban beállított értéket a [GetCertificate hívásakor metódussal](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

Végül töröljük és kiürítjük a tanúsítványt a kulcstartóból a [beginDeleteCertificate] és a https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) metódussal.

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

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy tanúsítványt, és lekérte a tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- [A tanúsítványok áttekintésének](about-certificates.md) beolvasása
- [Hozzáférési Key Vault megtekintése app Service alkalmazásról – oktatóanyag](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférési Key Vault megtekintése a virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)

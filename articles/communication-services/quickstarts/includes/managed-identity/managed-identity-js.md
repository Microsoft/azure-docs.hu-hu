---
ms.openlocfilehash: d70514e81bc838b4193862e66b0c03440d006128
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307460"
---
## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

A futtatásával `npm init -y` **package.js** hozhat létre az alapértelmezett beállításokkal rendelkező fájlon.

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>Az SDK-csomagok telepítése

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>Új fájl létrehozása

Nyisson meg egy új fájlt egy szövegszerkesztőben, és mentse a `index.js` fájlt a fájlba.

### <a name="use-the-sdk-packages"></a>Az SDK-csomagok használata

Adja hozzá a következő `require` irányelveket a tetejéhez az `index.js` Azure Identity és az Azure Storage SDK-k használatához.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential létrehozása

Ennek a rövid útmutatónak a [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential) fogjuk használni. Ez a hitelesítő adat megfelelő az éles és a fejlesztési környezetekhez. Ahogy az egyes műveletekhez szükség van, hozzuk létre a fájl felső részén `index.js` . 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Identitás létrehozása és a jogkivonat kiadása felügyelt identitásokkal

Ezután egy olyan függvényt írunk, amely létrehoz egy új identitást, és kiállít egy jogkivonatot ehhez az identitáshoz, ezt később a felügyelt identitás beállításának tesztelésére fogjuk használni.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-managed-identity"></a>SMS küldése felügyelt identitással

Most pedig olyan függvényt írhat, amely felügyelt identitásokat használ SMS-küldéshez:

```JavaScript
async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}
```

## <a name="write-the-main-method"></a>A Main metódus írása

A függvények létrehozásával most már írhatunk egy fő függvényt, amely meghívja őket, és bemutatjuk a felügyelt identitások használatát:
```JavaScript
async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

A végső `index.js` fájlnak így kell kinéznie:
```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");

const credential = new DefaultAzureCredential();

async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}

async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}

async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

## <a name="run-the-program"></a>A program futtatása

Minden készen áll, ha a projekt könyvtára alapján futtatja a fájlt `node index.js` . Ha minden jól járt, az alábbihoz hasonlónak kell megjelennie.

```Bash
    $ node index.js
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```

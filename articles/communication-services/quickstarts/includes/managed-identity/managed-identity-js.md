---
ms.openlocfilehash: ce29158c2a44f01fa06649e4b4497bf5c5076866
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512072"
---
## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancsablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

Futtassa `npm init -y` a parancsot, **package.jsalapértelmezett beállításokkal hozzon** létre egy fájlt.

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

Nyisson meg egy új fájlt egy szövegszerkesztővel, és mentse a következőként: . Ebben a fájlban fogjuk elhelyezni a `index.js` kódot.

### <a name="use-the-sdk-packages"></a>Az SDK-csomagok használata

Adja hozzá a következő irányelveket a lap tetejéhez az Azure Identity és az `require` `index.js` Azure Storage SDK-k használata érdekében.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential létrehozása

Ebben a rövid útmutatóban a [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential) értéket fogjuk használni. Ez a hitelesítő adat éles és fejlesztési környezetekhez megfelelő. Mivel minden művelethez szükség van rá, hozzunk létre a fájl `index.js` tetején. 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Identitás létrehozása és jogkivonat kiállítása felügyelt identitásokkal

A következő lépés egy olyan függvény megírása, amely létrehoz egy új identitást, és kiállít egy jogkivonatot ehhez az identitáshoz. Később ezt fogjuk használni a felügyelt identitás beállításának tesztelésére.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-managed-identity"></a>SMS küldése felügyelt identitással

Most írjunk egy függvényt, amely felügyelt identitások használatával küld SMS-t:

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

## <a name="write-the-main-function"></a>A fő függvény megírása

A függvények létrehozása után most már megírhat egy fő függvényt, amely meg tudja hívni őket, és bemutatja a felügyelt identitások használatát:
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

Ha minden elkészült, futtathatja a fájlt a projekt könyvtárában a `node index.js` beírásával. Ha minden jól ment, az alábbihoz hasonlót kell látnia.

```Bash
    $ node index.js
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```

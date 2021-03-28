---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 8fe8b853fe07af40603950a61c0dd2a1df74d14e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644371"
---
Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások JavaScript SMS SDK használatával SMS-üzenetek küldéséhez.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- SMS-kompatibilis telefonszám. [Telefonszám beolvasása](../get-phone-number.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A terminál vagy parancssori ablakban futtassa a parancsot `node --version` a Node.js telepítésének megadásához.
- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Először nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir sms-quickstart && cd sms-quickstart
```

A futtatásával `npm init -y` **package.js** hozhat létre az alapértelmezett beállításokkal rendelkező fájlon.

```console
npm init -y
```

Szövegszerkesztő használatával hozzon létre egy **send-sms.js** nevű fájlt a projekt gyökérkönyvtárában. Ehhez a rövid útmutatóhoz tartozó forráskódot a következő részekben adja hozzá ehhez a fájlhoz.

### <a name="install-the-package"></a>A csomag telepítése

Az `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatások SMS SDK-t a javascripthez.

```console
npm install @azure/communication-sms --save
```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Node.js Azure Communication Services SMS SDK főbb funkcióit kezelik.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Ez az osztály minden SMS-funkcióhoz szükséges. Létrehozhatja az előfizetési adataival, és SMS-üzenetek küldéséhez használhatja azt. |
| SmsSendRequest | Ez az interfész az SMS-kérelem létrehozási modellje (például adja meg a telefonszámokat és az SMS-tartalmat. |
| SmsSendOptions | Ez az interfész lehetőséget biztosít a kézbesítési jelentéskészítés konfigurálására. Ha `enableDeliveryReport` a értéke `true` , akkor egy esemény lesz kibocsátva, ha a kézbesítés sikeres. |
| SmsSendResult               | Ez az osztály az SMS szolgáltatás eredményét tartalmazza.                                          |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Importálja a **SmsClient** az SDK-ból, és hozza létre azt a kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).

Hozzon létre és nyisson meg egy **send-sms.js** nevű fájlt, és adja hozzá a következő kódot:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>1: N SMS-üzenet küldése

Ha SMS-üzenetet szeretne küldeni a címzettek listájára, hívja meg a `send` függvényt a SmsClient a címzettek telefonszámait tartalmazó listával (ha üzenetet szeretne küldeni egyetlen címzettnek, csak egy számot adjon meg a listában). Adja hozzá ezt a kódot a **send-sms.js** végéhez:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
A lecserélni kívánt `<from-phone-number>` SMS-kompatibilis telefonszámot a kommunikációs szolgáltatások erőforrásaihoz és `<to-phone-number-1>` `<to-phone-number-2>` azokhoz a telefonszám (ok) hoz, amelyekről üzenetet kíván küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>1: N SMS-üzenet küldése a következő beállításokkal

Egy Options objektum is megadható annak megadásához, hogy a kézbesítési jelentést engedélyezni kell-e, és egyéni címkéket kell-e beállítani.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

A lecserélni kívánt `<from-phone-number>` SMS-kompatibilis telefonszámot a kommunikációs szolgáltatások erőforrásaihoz és `<to-phone-number-1>` `<to-phone-number-2>` azon telefonszám (ok) hoz, amelyhez üzenetet szeretne küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

A `enableDeliveryReport` paraméter egy opcionális paraméter, amely a kézbesítési jelentéskészítés konfigurálására használható. Ez olyan esetekben hasznos, amikor az SMS-üzenetek kézbesítése során eseményeket szeretne kibocsátani. Tekintse meg az [SMS-események kezelése](../handle-sms-events.md) rövid útmutatót az SMS-üzenetek kézbesítési jelentéskészítésének konfigurálásához.
`tag` a egy opcionális paraméter, amelynek használatával címkét alkalmazhat a kézbesítési jelentésre.

## <a name="run-the-code"></a>A kód futtatása

A `node` paranccsal futtassa a **send-sms.js** fájlba felvett kódot.

```console

node ./send-sms.js

```

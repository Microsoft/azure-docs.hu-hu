---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: fc20396053dee32ac7976139a634b4592389ab5f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488315"
---
Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások JavaScript SMS ügyféloldali kódtár használatával SMS-üzenetek küldéséhez.

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

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatások SMS ügyféloldali kódtárat a javascripthez.

```console
npm install @azure/communication-sms --save
```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik az Azure kommunikációs szolgáltatások SMS ügyféloldali kódtár főbb funkcióit a Node.jshoz.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Ez az osztály minden SMS-funkcióhoz szükséges. Létrehozhatja az előfizetési adataival, és SMS-üzenetek küldéséhez használhatja azt. |
| SendSmsOptions | Ez az interfész lehetőséget biztosít a kézbesítési jelentéskészítés konfigurálására. Ha `enable_delivery_report` a értéke `true` , akkor egy esemény lesz kibocsátva, ha a kézbesítés sikeres volt. |
| SendMessageRequest | Ez az interfész az SMS-kérelem létrehozási modellje (például adja meg a telefonszámokat és az SMS-tartalmat. |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Importálja a **SmsClient** az ügyfél-függvénytárból, és hozza létre azt a kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a **send-sms.jshoz**:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>SMS küldése

SMS-üzenet küldése a metódus meghívásával `send` . Adja hozzá ezt a kódot a **send-sms.js** végéhez:

```javascript
async function main() {
  await smsClient.send({
    from: "<leased-phone-number>",
    to: ["<to-phone-number>"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true //Optional parameter
  });
}

main();
```

A lecserélni kívánt `<leased-phone-number>` SMS-kompatibilis telefonszámot a kommunikációs szolgáltatások erőforrásaihoz és `<to-phone-number>` azon telefonszámhoz kell cserélni, amelyhez üzenetet szeretne küldeni.

A `enableDeliveryReport` paraméter egy opcionális paraméter, amely a kézbesítési jelentéskészítés konfigurálására használható. Ez olyan esetekben hasznos, amikor az SMS-üzenetek kézbesítése során eseményeket szeretne kibocsátani. Tekintse meg az [SMS-események kezelése](../handle-sms-events.md) rövid útmutatót az SMS-üzenetek kézbesítési jelentéskészítésének konfigurálásához.

## <a name="run-the-code"></a>A kód futtatása

A `node` paranccsal futtassa a **send-sms.js** fájlba felvett kódot.

```console

node ./send-sms.js

```

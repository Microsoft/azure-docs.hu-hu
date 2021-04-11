---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113138"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A terminál vagy parancssori ablakban futtassa a parancsot `node --version` a Node.js telepítésének megadásához.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Először nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

A futtatásával `npm init -y` **package.js** hozhat létre az alapértelmezett beállításokkal rendelkező fájlon.

```console
npm init -y
```

Hozzon létre egy **phone-numbers-quickstart.js** nevű fájlt az imént létrehozott címtár gyökerében. Adja hozzá a következő kódrészletet:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>A csomag telepítése

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatások telefonszámok ügyféloldali függvénytárát a javascripthez.

```console
npm install @azure/communication-phone-numbers --save
```

A (z `--save` ) beállítás a **package.js** fájlhoz való függőségként adja a könyvtárat.

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Importálja a **PhoneNumbersClient** az ügyfél-függvénytárból, és hozza létre azt a kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a **phone-numbers-quickstart.js** tetejéhez:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Telefonszámok kezelése

### <a name="search-for-available-phone-numbers"></a>Elérhető telefonszámok keresése

A telefonszámok megvásárlásához először a rendelkezésre álló telefonszámokat kell megkeresnie. A telefonszámok kereséséhez adja meg a körzetszámot, a hozzárendelés típusát, a [telefonszám-képességeket](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), a [telefonszám típusát](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)és a mennyiséget. Vegye figyelembe, hogy az ingyenesen hívható telefonszámok esetében a körzetszám megadása nem kötelező.

Adja hozzá a következő kódrészletet a `main` függvényhez:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Vásárlás telefonszáma

A telefonszámok keresésének eredménye a következő: `PhoneNumberSearchResult` . Ez tartalmaz egy, a Buy `searchId` Numbers API-nak átadandó számot, amely a keresésben szereplő számok beszerzésére használható. Vegye figyelembe, hogy a Purchase Phone Numbers API meghívásával díjat számítunk fel az Azure-fiókra.

Adja hozzá a következő kódrészletet a `main` függvényhez:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Telefonszám-képességek frissítése

Ha már megvásárolt telefonszámot, adja hozzá a következő kódot a képességeinek frissítéséhez:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Vásárolt telefonszám (ok) beolvasása

A vásárlást követően lekérheti azt az ügyfélről. Adja hozzá a következő kódot a `main` függvényhez a most megvásárolt telefonszám beszerzéséhez:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Lekérheti az összes megvásárolt telefonszámot is.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Kiadási telefonszám

Most már felszabadíthatja a megvásárolt telefonszámot. Adja hozzá az alábbi kódrészletet a `main` függvényhez:

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>A kód futtatása

A `node` paranccsal futtassa a **phone-numbers-quickstart.js** fájlba felvett kódot.

```console
node phone-numbers-quickstart.js
```
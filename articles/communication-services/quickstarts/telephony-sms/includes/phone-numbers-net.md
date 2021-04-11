---
ms.openlocfilehash: 0bfb23977f6553568da24df614621bdf1eb9d06d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112998"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Az operációs rendszerhez tartozó legújabb [.net Core ügyféloldali kódtár](https://dotnet.microsoft.com/download/dotnet-core) .
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A terminál vagy a parancssorablakban futtassa a `dotnet` parancsot a .net Ügyféloldali kódtár telepítésének megadásához.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `PhoneNumbersQuickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: **program. cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>A csomag telepítése

Miközben még mindig az alkalmazás könyvtárában van, telepítse az Azure Communication PhoneNumbers a .NET-csomaghoz a `dotnet add package` parancs használatával.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.6
```

Adjon hozzá egy `using` direktívát a **programhoz. cs** , hogy tartalmazza a névtereket.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

Frissítse `Main` a függvény aláírását aszinkron értékre.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A telefonszám-ügyfeleket a [Azure Portal] [azure_portal] Azure kommunikációs erőforrásaiból beszerzett kapcsolati karakterlánc használatával lehet hitelesíteni.

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

A telefonszám-ügyfelek Azure Active Directory hitelesítéssel is végezhetnek hitelesítést. Ezzel a beállítással a `AZURE_CLIENT_SECRET` , `AZURE_CLIENT_ID` és `AZURE_TENANT_ID` környezeti változókat kell beállítani a hitelesítéshez.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Telefonszámok kezelése

### <a name="search-for-available-phone-numbers"></a>Elérhető telefonszámok keresése

A telefonszámok megvásárlásához először a rendelkezésre álló telefonszámokat kell megkeresnie. A telefonszámok kereséséhez adja meg a körzetszámot, a hozzárendelés típusát, a [telefonszám-képességeket](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), a [telefonszám típusát](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)és a mennyiséget. Vegye figyelembe, hogy az ingyenesen hívható telefonszámok esetében a körzetszám megadása nem kötelező.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Telefonszámok vásárlása

A telefonszámok keresésének eredménye a következő: `PhoneNumberSearchResult` . Ez tartalmaz egy, a Buy `SearchId` Numbers API-nak átadandó számot, amely a keresésben szereplő számok beszerzésére használható. Vegye figyelembe, hogy a Purchase Phone Numbers API meghívásával díjat számítunk fel az Azure-fiókra.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>Telefonszám (ok) beolvasása

A vásárlást követően lekérheti azt az ügyfélről.
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Lekérheti az összes megvásárolt telefonszámot is.
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Telefonszám-képességek frissítése

A megvásárolt számokkal frissítheti a képességeket.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Kiadási telefonszám

Megvásárolt telefonszámot is felszabadíthat.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```console
dotnet run
```

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)

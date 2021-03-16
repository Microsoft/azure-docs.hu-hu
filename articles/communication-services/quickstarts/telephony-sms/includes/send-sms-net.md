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
ms.openlocfilehash: a118dfceb73aca0897ba0f116ce3c5462368f6c3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488318"
---
Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások C# SMS ügyféloldali kódtár használatával SMS-üzenetek küldéséhez.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Az operációs rendszerhez tartozó legújabb [.net Core ügyféloldali kódtár](https://dotnet.microsoft.com/download/dotnet-core) .
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- SMS-kompatibilis telefonszám. [Telefonszám beolvasása](../get-phone-number.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A terminál vagy a parancssorablakban futtassa a `dotnet` parancsot a .net Ügyféloldali kódtár telepítésének megadásához.
- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `SmsQuickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájlban: **program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>A csomag telepítése

Miközben az alkalmazás könyvtára továbbra is elérhető, telepítse az Azure Communication Services SMS ügyféloldali kódtárat a .NET-csomaghoz a `dotnet add package` parancs használatával.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.3
```

Adjon hozzá egy `using` direktívát a **program.cs** tetejéhez, hogy tartalmazza a `Azure.Communication` névteret.

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a C#-hoz készült Azure kommunikációs szolgáltatások SMS ügyféloldali függvénytárának főbb funkcióit kezelik.

| Név                                       | Leírás                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Ez az osztály minden SMS-funkcióhoz szükséges. Létrehozhatja az előfizetési adataival, és SMS-üzenetek küldéséhez használhatja azt.                           |
| SendSmsOptions | Ez az osztály a kézbesítési jelentéskészítés konfigurálásának lehetőségeit tartalmazza. Ha a enable_delivery_report értéke TRUE (igaz), akkor a sikeres kézbesítés után egy esemény lesz kibocsátva |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

 Nyissa meg a **program.cs** egy szövegszerkesztőben, és cserélje le a metódus törzsét `Main` kóddal, hogy inicializáljon egy- `SmsClient` t a kapcsolódási karakterlánccal. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>SMS küldése

SMS-üzenet küldése a küldési metódus meghívásával. Adja hozzá ezt a kódot a metódus végéhez a `Main` **program.cs**-ben:

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

A lecserélni kívánt `<leased-phone-number>` SMS-kompatibilis telefonszámot a kommunikációs szolgáltatások erőforrásaihoz és `<to-phone-number>` azon telefonszámhoz kell cserélni, amelyhez üzenetet szeretne küldeni.

A `EnableDeliveryReport` paraméter egy opcionális paraméter, amely a kézbesítési jelentéskészítés konfigurálására használható. Ez olyan esetekben hasznos, amikor az SMS-üzenetek kézbesítése során eseményeket szeretne kibocsátani. Tekintse meg az [SMS-események kezelése](../handle-sms-events.md) rövid útmutatót az SMS-üzenetek kézbesítési jelentéskészítésének konfigurálásához.

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```console
dotnet run
```

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)

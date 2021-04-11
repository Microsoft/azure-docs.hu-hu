---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 5fd209c612f90e3912e244daf60d20edf30a08c6
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112851"
---
Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások C# SMS SDK használatával SMS-üzenetek küldéséhez.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Az operációs rendszer legújabb verziója [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) .
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- SMS-kompatibilis telefonszám. [Telefonszám beolvasása](../get-phone-number.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A terminál vagy a parancssori ablakban futtassa a `dotnet` parancsot a .net SDK telepítésének megadásához.
- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `SmsQuickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: **program. cs**.

```console
dotnet new console -o SmsQuickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>A csomag telepítése

Még az alkalmazás könyvtára alatt telepítse az Azure kommunikációs szolgáltatások SMS SDK for .NET-csomagot a `dotnet add package` paranccsal.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

Adjon hozzá egy `using` direktívát a **program. cs** felső részéhez, hogy tartalmazza a `Azure.Communication` névteret.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a C#-hoz készült Azure Communication Services SMS SDK főbb funkcióit.

| Név                                       | Leírás                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Ez az osztály minden SMS-funkcióhoz szükséges. Létrehozhatja az előfizetési adataival, és SMS-üzenetek küldéséhez használhatja azt.                           |
| SmsSendOptions | Ez az osztály a kézbesítési jelentéskészítés konfigurálásának lehetőségeit tartalmazza. Ha a enable_delivery_report értéke TRUE (igaz), akkor a sikeres kézbesítés után egy esemény lesz kibocsátva |
| SmsSendResult               | Ez az osztály az SMS szolgáltatás eredményét tartalmazza.                                          |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

 Nyissa meg a **program. cs programot** egy szövegszerkesztőben, és cserélje le a metódus törzsét `Main` kóddal, hogy inicializáljon egy- `SmsClient` t a kapcsolódási karakterlánccal. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>1:1 SMS-üzenet küldése

Ha SMS-üzenetet szeretne küldeni egyetlen címzettnek, hívja meg a `Send` vagy a `SendAsync` függvényt a SmsClient. Adja hozzá ezt a kódot a `Main` **program. cs** metódusának végéhez:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
A lecserélni kívánt `<from-phone-number>` SMS-kompatibilis telefonszámot a kommunikációs szolgáltatások erőforrásaihoz és `<to-phone-number>` azon telefonszámhoz kell cserélni, amelyhez üzenetet szeretne küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>1: N SMS-üzenet küldése a következő beállításokkal
Ha SMS-üzenetet szeretne küldeni a címzettek listájára, hívja `Send` meg a vagy a `SendAsync` függvényt a címzett telefonszámait tartalmazó SmsClient. A választható paramétereket is megadhatja annak megadásához, hogy a kézbesítési jelentést engedélyezni kell-e, és egyéni címkéket kell-e beállítani.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

A lecserélni kívánt `<from-phone-number>` SMS-kompatibilis telefonszámot a kommunikációs szolgáltatások erőforrásaihoz és `<to-phone-number-1>` `<to-phone-number-2>` azon telefonszám (ok) hoz, amelyhez üzenetet szeretne küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

A `enableDeliveryReport` paraméter egy opcionális paraméter, amely a kézbesítési jelentéskészítés konfigurálására használható. Ez olyan esetekben hasznos, amikor az SMS-üzenetek kézbesítése során eseményeket szeretne kibocsátani. Tekintse meg az [SMS-események kezelése](../handle-sms-events.md) rövid útmutatót az SMS-üzenetek kézbesítési jelentéskészítésének konfigurálásához.

`Tag` címkének a kézbesítési jelentésre való alkalmazására szolgál.

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```console
dotnet run
```

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)

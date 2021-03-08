---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: f98c34a53de60adc7866c1b57918232b966a0783
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445805"
---
[Dokumentáció](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title=" hozzon létre egy személyre szabott erőforrást "  target="_blank"> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Az alkalmazás a személyre szabott API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben.

A konzol ablakban (például cmd, PowerShell vagy bash) a DotNet `new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `personalizer-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl használatával: `Program.cs` .

```console
dotnet new console -n personalizer-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse az alkalmazás könyvtárában a .NET személyre szabott ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

> [!TIP]
> Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

A projekt könyvtárában nyissa meg a `Program.cs` fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következőt az irányelvek használatával:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;
```

## <a name="object-model"></a>Objektummodell

A személyre szabott ügyfél egy [PersonalizerClient](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient) objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

Ha szeretné megkérdezni a tartalom egyetlen legjobb elemét, hozzon létre egy [RankRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest), majd továbbítsa azt az [ügyfélnek. Rangsor](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank) metódusa. A Rank metódus egy RankResponse ad vissza.

A jutalom pontszámának személyre szabásához hozzon létre egy [RewardRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest), majd továbbítsa azt az [ügyfélnek. Jutalmazási](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward) módszer.

A jutalom pontszám meghatározása ebben a rövid útmutatóban triviális. Éles rendszerekben annak meghatározása, hogy milyen hatással van a [jutalom pontszáma](../concept-rewards.md) , és hogy mekkora mértékben lehet egy összetett folyamat, dönthet úgy, hogy idővel változhat. Ennek a tervezési döntésnek a személyre szabott architektúrájának egyik elsődleges döntésének kell lennie.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a személyre szabott ügyféloldali kódtáraval a .NET-hez:

* [Személyre szabott ügyfél létrehozása](#authenticate-the-client)
* [Rangsor API](#request-the-best-action)
* [Jutalom API](#send-a-reward)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ebben a szakaszban két dolgot kell tennie:
* Adja meg a kulcsot és a végpontot
* Személyre szabott ügyfél létrehozása

Először adja hozzá a következő sorokat a program osztályhoz. Ügyeljen arra, hogy hozzáadja a kulcsot és a végpontot a személyre szabott erőforrásból.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
private static readonly string ApiKey = "REPLACE-WITH-YOUR-PERSONALIZER-KEY";
private static readonly string ServiceEndpoint = "https://REPLACE-WITH-YOUR-PERSONALIZER-RESOURCE-NAME.cognitiveservices.azure.com";
```

Ezután adjon hozzá egy metódust a programhoz egy új személyre szabott ügyfél létrehozásához.

```csharp
static PersonalizerClient InitializePersonalizerClient(string url)
{
    PersonalizerClient client = new PersonalizerClient(
        new ApiKeyServiceClientCredentials(ApiKey)) { Endpoint = url };

    return client;
}
```

## <a name="get-food-items-as-rankable-actions"></a>Élelmiszeripari elemek beolvasása sorba besorolt műveletként

A műveletek azokat a tartalmi beállításokat jelentik, amelyeknek a személyre szabásával ki kell választania a legjobb tartalmi elemet. Adja hozzá a következő metódusokat a program osztályhoz a műveletek és a hozzájuk tartozó funkciók megjelenítéséhez. 

```csharp
static IList<RankableAction> GetActions()
{
    IList<RankableAction> actions = new List<RankableAction>
    {
        new RankableAction
        {
            Id = "pasta",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
        },

        new RankableAction
        {
            Id = "ice cream",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
        },

        new RankableAction
        {
            Id = "juice",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
        },

        new RankableAction
        {
            Id = "salad",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
        }
    };

    return actions;
}
```

## <a name="get-user-preferences-for-context"></a>A környezet felhasználói beállításainak beolvasása

Adja hozzá a következő metódusokat a program osztályhoz, hogy beolvassa a felhasználó bemenetét a parancssorból a nap időpontjában és az aktuális élelmiszer-preferencia alapján. Ezek környezeti funkciókként lesznek felhasználva.

```csharp
static string GetUsersTimeOfDay()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetUsersTastePreference()
{
    string[] tasteFeatures = new string[] { "salty", "sweet" };

    Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
    if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
        tasteIndex = 1;
    }

    return tasteFeatures[tasteIndex - 1];
}
```

Mindkét módszer a `GetKey` metódus használatával olvassa be a felhasználó kijelölését a parancssorból.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A személyre szabott tanulási hurok a [Range](#request-the-best-action) és a [jutalmazási](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban a tartalom személyre szabásához a rangsorban megjelenő minden egyes hívást egy jutalmazási hívás követ, amely alapján eldöntheti, hogy milyen jól teljesíti a szolgáltatást.

A következő kód hurkokat mutat be a felhasználónak a parancssorban való megadására, az információknak a személyre szabására való kiválasztásához, az ügyfélnek a listából való kiválasztásához, majd a jutalom pontszámának elküldéséhez, hogy a szolgáltatás milyen jól jelenjen meg a kiválasztásában.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    IList<RankableAction> actions = GetActions();

    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        IList<string> excludeActions = new List<string> { "juice" };

        string eventId = Guid.NewGuid().ToString();

        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        client.Reward(response.EventId, new RewardRequest(reward));

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Adja hozzá a következő metódusokat, amelyek [megkapják a tartalom választási lehetőségeit](#get-food-items-as-rankable-actions)a kódlap futtatása előtt:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>A legjobb művelet kérése

A rangsorolási kérelem teljesítéséhez a program megkéri a felhasználó beállításait, hogy hozzon létre egy `currentContext` tartalom-választási lehetőséget. A folyamat létrehozhat olyan tartalmat, amely kizárható a műveletekből, a következő módon: `excludeActions` . A válasz fogadásához a Rank kérelemnek szüksége van a műveletekre és azok szolgáltatásaira, a LicenseManager CurrentContext szolgáltatásaira, a excludeActions és egy egyedi esemény-AZONOSÍTÓra.

Ez a rövid útmutató a napszak és a felhasználói élelmiszer-beállítások egyszerű kontextusát tartalmazza. Az éles rendszerekben a [műveletek és szolgáltatások](../concepts-features.md) meghatározása és [értékelése](../concept-feature-evaluation.md) nem triviális kérdés lehet.

```csharp
string timeOfDayFeature = GetUsersTimeOfDay();
string tasteFeature = GetUsersTastePreference();

IList<object> currentContext = new List<object>() {
    new { time = timeOfDayFeature },
    new { taste = tasteFeature }
};

IList<string> excludeActions = new List<string> { "juice" };

string eventId = Guid.NewGuid().ToString();

var request = new RankRequest(actions, currentContext, excludeActions, eventId);
RankResponse response = client.Rank(request);
```

## <a name="send-a-reward"></a>Jutalom küldése

Ahhoz, hogy a jutalom pontszáma a jutalom iránti kérelemben legyen elküldve, a program beolvassa a felhasználó kijelölését a parancssorból, hozzárendel egy numerikus értéket a kijelöléshez, majd elküldi az egyedi eseményazonosító és a jutalom pontszámát a jutalmazási API-nak megfelelő numerikus értékként.

Ez a rövid útmutató egy egyszerű számot rendel hozzá a jutalom pontszámához, vagy nulla vagy 1 értéket. Az éles rendszerekben az adott igényektől függően nem triviális kérdés lehet annak meghatározása, hogy mikor és mit kell elküldeni a [jutalmazási](../concept-rewards.md) hívásnak.

```csharp
float reward = 0.0f;
string answer = GetKey();

if (answer == "Y")
{
    reward = 1;
    Console.WriteLine("\nGreat! Enjoy your food.");
}
else if (answer == "N")
{
    reward = 0;
    Console.WriteLine("\nYou didn't like the recommended food choice.");
}
else
{
    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
}

Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
foreach (var rankedResponse in response.Ranking)
{
    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
}

// Send the reward for the action based on user response.
client.Reward(response.EventId, new RewardRequest(reward));
```

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a DotNet `run` paranccsal az alkalmazás könyvtárából.

```console
dotnet run
```

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

A rövid útmutató [forráskódja](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) elérhető.

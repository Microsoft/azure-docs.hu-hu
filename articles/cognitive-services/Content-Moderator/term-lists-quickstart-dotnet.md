---
title: Szöveg ellenőrzése egyéni kifejezéslisták alapján a C# használatával – Content Moderator
titleSuffix: Azure Cognitive Services
description: A cikk bemutatja, hogyan végezhető el szövegek moderálása egyéni kifejezéslistákkal a C#-hoz készült Content Moderator SDK-val.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: e32f18e7ff9e222586b1c9ca328e0f6c4eb3ba51
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055552"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Szöveg keresése egy egyéni kifejezési listáról C-ben #

Az Azure Content Moderator alapértelmezett globális kifejezéslistája a legtöbb tartalommoderálási igénynek megfelel. Azonban szükség lehet bizonyos szervezetspecifikus kifejezések szűrésére is. Például elképzelhető, hogy meg szeretné címkézni az üzleti vetélytársak neveit további ellenőrzés céljából. 

A [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) segítségével létrehozhat egyéni listákat a szövegmoderálási API-val való használatra.

Ez a cikk ahhoz biztosít információt és kódmintákat, hogy elvégezhesse a következő műveleteket a Content Moderator SDK for .NET segítségével:
- Lista létrehozása.
- Kifejezések hozzáadása egy listához.
- Egy listán szereplő kifejezések szűrése.
- Kifejezések törlése egy listából.
- Lista törlése.
- Listaadatok szerkesztése.
- Frissítse az indexet, hogy az új beolvasások észleljék a lista módosításait.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="sign-up-for-content-moderator-services"></a>Regisztráció a Content Moderator szolgáltatásaiba

Ahhoz, hogy a REST API-n vagy az SDK-n keresztül használhassa a Content Moderator szolgáltatásait, előbb be kell szereznie egy előfizetői azonosítót. Ennek beszerzéséhez iratkozzon fel a Content Moderator szolgáltatásra az [Azure Portalon](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

1. Adja a projektnek a **TermLists** (Kifejezéslisták) nevet. Válassza ki ezt a projektet a megoldás egyedüli kezdőprojektjeként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Telepítse a következő NuGet-csomagokat a TermLists projekthez:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Adja hozzá a következő `using`-utasításokat.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Content Moderator-ügyfél létrehozása

Adja meg a következő kódot, hogy létrehozzon egy Content Moderator-ügyfelet az előfizetéséhez. Frissítse a `AzureEndpoint` és a `CMSubscriptionKey` mezőket a végpont URL-címének és előfizetési kulcsának értékeivel. Ezeket a Azure Portalban található erőforrás **gyors üzembe helyezés** lapján találja.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Privát tulajdonságok hozzáadása

Adja hozzá az alábbi privát tulajdonságokat a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Kifejezéslista létrehozása

Kifejezéslistát a **ContentModeratorClient.ListManagementTermLists.Create** segítségével hozhat létre. A **létrehozás** első paramétere egy sztring, amely egy MIME-típust tartalmaz, ez pedig az „application/json” típus kell, hogy legyen. További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). A második paraméter egy **Törzs** objektum, amely az új kifejezéslista nevét és leírását tartalmazza.

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

> [!NOTE]
> A Content Moderator-szolgáltatáskulcs rendelkezik egy RPS-alapú (a kérések másodpercenkénti számát jelölő) sebességkorláttal, amelyet ha túllép, az SDK egy 429-es hibakódú kivételt jelez. Az ingyenes szint kulcsának a sebességkorlátja 1 RPS.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Kifejezéslista nevének és leírásának frissítése

A kifejezéslista adatai a **ContentModeratorClient.ListManagementTermLists.Update** segítségével frissíthetők. A **Frissítés** első paramétere a kifejezéslista azonosítója. A második paraméter egy MIME-típus, ez pedig az „application/json” kell, hogy legyen. További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). A harmadik paraméter egy **Törzs** objektum, amely az új nevet és leírást tartalmazza.

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Kifejezés hozzáadása kifejezéslistához

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Egy kifejezéslista minden kifejezésének lekérése

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Kód hozzáadása a keresési index frissítése céljából

Miután módosít egy kifejezéslistát, frissítse a lista keresési indexét, hogy a rendszer figyelembe vegye a módosításokat, amikor legközelebb szűr egy szöveget a lista használatával. Ez hasonlít arra, ahogy egy asztali keresőmotor (ha engedélyezve van) vagy webes keresőmotor folyamatosan frissíti az indexét, hogy az tartalmazza az új fájlokat és oldalakat.

A kifejezéslista keresési index a **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod** segítségével frissíthető.

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Szöveg szűrése egy kifejezéslista használatával

Szövegek egy adott kifejezéslista alapján való szűrését a **ContentModeratorClient.TextModeration.ScreenText** segítségével lehet elvégezni, amelyhez az alábbi paraméterek tartoznak.

- A kifejezéslistában szereplő kifejezések nyelve.
- Egy MIME-típus, amely lehet „text/html”, „text/xml”, „text/markdown” vagy „text/plain”.
- Az átvizsgálandó szöveg.
- Logikai érték. Ebben a mezőben adja meg az **igaz** értéket a szöveg automatikus javításához az átvizsgálást megelőzően.
- Logikai érték. A mező értékét állítsa **igaz** értékre a szövegben tárolt személyes adatfelderítéshez.
- A kifejezéslista azonosítója.

További információkért lásd az [API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

A **ScreenText** egy **Screen** objektumot ad vissza, amely rendelkezik egy **Terms** tulajdonsággal. Ez felsorolja az összes kifejezést, amelyeket a Content Moderator észlelt a szöveg átvizsgálásakor. Ha a Content Moderator nem talált egyetlen kifejezést sem az átvizsgálás során, akkor a **Terms** tulajdonság értéke **null**.

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Kifejezések és listák törlése

Egy kifejezés vagy egy lista törlése nagyon egyszerű. Az SDK segítségével végezhetők el a következő feladatok:

- Egy kifejezés törlése. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Egy listán szereplő összes kifejezés törlése a lista törlése nélkül. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Egy lista törlése annak teljes tartalmával együtt. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Egy kifejezés törlése

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Egy kifejezéslista összes kifejezésének törlése

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Egy kifejezéslista törlése

Adja hozzá az alábbi metódusdefiníciót a TermLists névtér Program osztályához.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>A Main metódus összeállítása

Adja hozzá a **fő** metódus definícióját a Namespace **TermLists**, a Class **programhoz**. Végül zárjuk be a **program** osztályt és a **TermLists** névteret.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Futtassa az alkalmazást a kimenet megtekintéséhez

A konzol kimenete az alábbihoz hasonlóan fog kinézni:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Következő lépések

Szerezze be a kapcsolódó [Content Moderator .NET SDK-t](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) és [Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a .NET-es Content Moderator ezen és további rövid útmutatóihoz, hogy nekikezdhessen az integrációnak.

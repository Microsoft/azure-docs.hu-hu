---
title: Az aktív tanulás javasolt kérdései – QnA Maker
description: Az aktív tanulással javíthatja a Tudásbázis minőségét. Áttekintheti, elfogadhatja vagy elutasíthatja a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3f2e8fef35095a007051999d806f2942089ae19a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584752"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Az aktív tanulás javasolt kérdéseinek elfogadása a Tudásbázisban


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Az aktív tanulás megváltoztatja a tudásbázist vagy Search Service a javaslat jóváhagyása után, majd a Mentés és a betanítás lehetőségre. Ha jóváhagyja a javaslatot, azt a rendszer alternatív kérdésként adja hozzá.

## <a name="turn-on-active-learning"></a>Az aktív tanulás bekapcsolása

A javasolt kérdések megjelenítéséhez [be kell kapcsolni a QnA Maker erőforrás aktív tanulását](../concepts/active-learning-suggestions.md) .

## <a name="view-suggested-questions"></a>Javasolt kérdések megtekintése

1. A javasolt kérdések megtekintéséhez a Tudásbázis **szerkesztése** lapon válassza a **megtekintési beállítások**, majd az **aktív tanulási javaslatok megjelenítése** lehetőséget. Ez a lehetőség akkor lesz letiltva, ha a kérdés-és a válasz párokhoz nem állnak rendelkezésre javaslatok.  

    [![A portál szerkesztés szakaszában válassza a javaslatok megjelenítése lehetőséget, hogy megtekintse az aktív tanulás új kérdéseit.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. A tudásbázist a kérdések és válaszok párok alapján szűrheti, hogy csak a javaslatok jelenjenek meg a **szűrési javaslatok** lehetőség kiválasztásával.

    [![A szűrés javaslatok szerint váltógomb csak az aktív tanulás javasolt kérdéseit jeleníti meg.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Minden QnA pár az új kérdéses alternatívákat javasolja a pipa jelzéssel, a `✔` kérdés elfogadásához vagy a `x` javaslatok elutasításához. Jelölje be a jelölőnégyzetet a kérdés hozzáadásához.

    [![Válassza ki vagy utasítsa el az aktív tanulás javasolt kérdésének alternatívákat a zöld pipa vagy a vörös törlés jelölésének kiválasztásával.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Az összes _javaslat_ hozzáadásához vagy törléséhez kattintson az összes **hozzáadása** vagy az **összes elutasítása** lehetőségre a környezetfüggő eszköztáron.

1. Válassza a **Mentés és a betanítás** lehetőséget a Tudásbázis módosításainak mentéséhez.

1. Válassza a **Közzététel** lehetőséget, hogy a módosítások elérhetők legyenek a [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)-ból.

    Ha 5 vagy több hasonló lekérdezés van csoportosítva, 30 percenként QnA Maker javasolja az elfogadására vagy elutasítására vonatkozó alternatív kérdéseket.


<a name="#score-proximity-between-knowledge-base-questions"></a>

## <a name="active-learning-suggestions-are-saved-in-the-exported-knowledge-base"></a>Az aktív tanulási javaslatok az exportált Tudásbázisban lesznek mentve

Ha az alkalmazás aktív tanulási lehetőséggel rendelkezik, és exportálja az alkalmazást, a `SuggestedQuestions` TSV-fájlban lévő oszlop megőrzi az aktív tanulási adatait.

Az `SuggestedQuestions` oszlop az implicit, a `autosuggested` és a kifejezett VISSZAJELZÉSek JSON-objektuma `usersuggested` . Példa erre a JSON-objektumra egyetlen felhasználó által beküldött kérdés esetén `help` :

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Ha újraimportálja az alkalmazást, az aktív tanulás továbbra is gyűjti az adatokat, és javaslatokat javasol a tudásbázishoz.


### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Építészeti folyamat a GenerateAnswer és a Train API-k egy robotból való használatához

A bot vagy más ügyfélalkalmazás a következő építészeti folyamatot használja az aktív tanulás használatához:

* A robot a GenerateAnswer API-val beolvassa [a Tudásbázisból a választ](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) , és a `top` tulajdonság használatával számos választ kaphat.

#### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>A GenerateAnswer kérelemben szereplő Top tulajdonsággal több egyező választ kaphat

Ha egy kérdés beküldésekor QnA Maker választ, a JSON- `top` törzs tulajdonsága beállítja a visszaadott válaszok számát.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

* A bot explicit visszajelzést határoz meg:
    * A saját [egyéni üzleti logikájának](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)használatával kiszűrheti az alacsony pontszámot.
    * A bot vagy az ügyfél alkalmazásban a lehetséges válaszok megjelenítése a felhasználó számára, és a felhasználó kiválasztott válaszának beolvasása.
* A robot a [betanítási API](#train-api) [használatával visszaküldi a kiválasztott választ a QnA Makerra](#bot-framework-sample-code) .


### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>A score (pontszám) tulajdonság és az üzleti logika használata a felhasználók számára megjelenített válaszok listájának lekéréséhez

Ha az ügyfélalkalmazás (például egy csevegési robot) megkapja a választ, a rendszer az első 3 kérdést adja vissza. A `score` tulajdonság használatával elemezheti a pontszámok közötti közelséget. A közelségi tartományt a saját üzleti logikája határozza meg.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Ügyfélalkalmazás követése, ha a kérdések hasonló pontszámokkal rendelkeznek

Az ügyfélalkalmazás megjeleníti azokat a kérdéseket, amelyekkel a felhasználó kiválaszthatja a céljuknak leginkább megfelelő _egyetlen kérdést_ .

Miután a felhasználó kiválasztja az egyik meglévő kérdést, az ügyfélalkalmazás a QnA Maker 's Train API-val visszajelzést küld a felhasználónak. Ez a visszajelzés befejezi az aktív tanulási visszajelzési ciklust.

## <a name="train-api"></a>API betanítása

Az aktív tanulási visszajelzéseket a rendszer a Train API POST kérelemmel QnA Maker küldi el. Az API-aláírás:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP-kérelem tulajdonsága|Név|Típus|Cél|
|--|--|--|--|
|URL-útvonal paraméter|Tudásbázis-azonosító|sztring|A Tudásbázis GUID azonosítója.|
|Egyéni altartomány|QnAMaker-erőforrás neve|sztring|Az erőforrás neve a QnA Maker egyéni altartománya lesz. Ez a Tudásbázis közzététele után a beállítások lapon érhető el. A lista a `host` .|
|Fejléc|Content-Type|sztring|Az API-nak eljuttatott törzs adathordozó-típusa. Az alapértelmezett érték: `application/json`|
|Fejléc|Engedélyezés|sztring|A végpont kulcsa (EndpointKey XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX).|
|Post törzs|JSON-objektum|JSON|A betanítási visszajelzés|

A JSON-törzs több beállítással rendelkezik:

|JSON-törzs tulajdonság|Típus|Cél|
|--|--|--|--|
|`feedbackRecords`|array|Visszajelzések listája.|
|`userId`|sztring|A javasolt kérdéseket elfogadó személy felhasználói azonosítója. A felhasználói azonosító formátuma. Például egy e-mail-cím lehet érvényes felhasználói azonosító az architektúrában. Választható.|
|`userQuestion`|sztring|A felhasználó lekérdezésének pontos szövege. Kötelező.|
|`qnaID`|szám|A [GenerateAnswer válaszban](metadata-generateanswer-usage.md#generateanswer-response-properties)található kérdés azonosítója. |

A JSON-törzs például a következőképpen néz ki:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

A sikeres válasz 204 állapotot ad vissza, és nem a JSON-válasz törzsét.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch – sok visszajelzési rekord egyetlen hívásba

Az ügyféloldali alkalmazásban, például egy robotban tárolhatja az adatokat, majd több rekordot is küldhet egyetlen JSON-törzsbe a `feedbackRecords` tömbben.

A JSON-törzs például a következőképpen néz ki:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Robot Framework-mintakód

A robot Framework kódjának meg kell hívnia a Train API-t, ha a felhasználó lekérdezését az aktív tanuláshoz kell használni. A kód kétféleképpen írható:

* Annak megállapítása, hogy kell-e lekérdezést használni az aktív tanuláshoz
* Lekérdezés küldése vissza a QnA Maker Train API-nak az aktív tanuláshoz

Az [Azure bot Sample](https://github.com/microsoft/BotBuilder-Samples)-ben mindkét tevékenység programozása megtörtént.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Példa C#-kód a Train API-hoz a robot Framework 4. x használatával

Az alábbi kód bemutatja, hogyan küldhet vissza adatokat QnA Makerra a Train API-val.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Példa Node.js kód a Train API-hoz a robot Framework 4. x használatával

Az alábbi kód bemutatja, hogyan küldhet vissza adatokat QnA Makerra a Train API-val.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="best-practices"></a>Ajánlott eljárások

Az aktív tanulás használata esetén ajánlott eljárások: [ajánlott eljárások](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Metaadatok használata a GenerateAnswer API-val](metadata-generateanswer-usage.md)

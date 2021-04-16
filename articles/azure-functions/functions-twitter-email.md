---
title: Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása
description: Hozzon létre egy függvényt, amely integrálható Azure Logic Apps és Azure Cognitive Services. Az eredményül kapott munkafolyamat kategorizálja a tweetek hangulatát, és e-mail-értesítéseket küld.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388695"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Oktatóanyag: Függvény létrehozása a Azure Logic Apps

Az Azure Functions integrálható az Azure Logic Apps szolgáltatással a Logic Apps Designerben. Ez az integráció lehetővé teszi, hogy a Functions számítási funkcióit más Azure- és külső szolgáltatásokkal vezényelje.

Ez az oktatóanyag bemutatja, hogyan hozhat létre munkafolyamatot Twitter-tevékenységek elemzéséhez. A tweetek kiértékelésekor a munkafolyamat értesítést küld, ha pozitív véleményeket észlel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy Cognitive Services API-erőforrás létrehozása.
> * Olyan függvény létrehozása, amely kategorizálja a tweetek hangulatát.
> * A Twitterhez csatlakozó logikai alkalmazás létrehozása.
> * A hangulatfelismerés hozzáadása a logikai alkalmazáshoz.
> * A logikai alkalmazás csatlakoztatása a függvényhez.
> * E-mail küldése a függvénytől érkező válasz alapján.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív [Twitter](https://twitter.com/)-fiók.
* Egy [Outlook.com](https://outlook.com/)-fiók (az értesítések küldéséhez).

> [!NOTE]
> Ha a Gmail-összekötőt szeretné használni, csak a G-Suite üzleti fiókok használhatja ezt az összekötőt a logikai alkalmazások korlátozásai nélkül. Ha rendelkezik Gmail felhasználói fiókkal, használhatja a Gmail-összekötőt csak bizonyos Google által jóváhagyott alkalmazásokkal és szolgáltatásokkal, vagy létrehozhat egy Google-ügyfélalkalmazást a Gmail-összekötőben való [hitelesítéshez.](/connectors/gmail/#authentication-and-bring-your-own-application) <br><br>További információ: Adatbiztonsági és adatvédelmi szabályzatok [Google-összekötőkhöz a Azure Logic Apps.](../connectors/connectors-google-data-security-privacy-policy.md)

## <a name="create-text-analytics-resource"></a>Erőforrás Text Analytics létrehozása

A Cognitive Services API-k egyéni erőforrásként érhetők el az Azure-ban. A Text Analytics API-val észlelheti a közzétett tweetek hangulatát.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

1. A _Kategóriák alatt_ válassza az **AI + Machine Learning**

1. A _Text Analytics_ válassza a **Létrehozás lehetőséget.**

1. A Create Text Analytics _képernyőn_ adja meg a következő értékeket.

    | Beállítás | Érték | Megjegyzések |
    | ------- | ----- | ------- |
    | Előfizetés | Az Ön Azure-előfizetésének neve | |
    | Erőforráscsoport | Hozzon létre egy **új erőforráscsoportot tweet-sentiment-tutorial névvel** | Később törölnie kell ezt az erőforráscsoportot az oktatóanyag során létrehozott összes erőforrás eltávolításához. |
    | Region | Válassza ki az Önhöz legközelebb eső régiót | |
    | Name | **TweetSentimentApp** | |
    | Tarifacsomag | Válassza az **Ingyenes F0 lehetőséget** | |

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.

1. Az üzembe helyezés befejezése után válassza az **Erőforráshoz való ugrás lehetőséget.**

## <a name="get-text-analytics-settings"></a>A Text Analytics lekért beállításai

A Text Analytics erőforrás létrehozása után átmásol néhány beállítást, és félre fogja őket tenni későbbi használatra.

1. Válassza a **Kulcsok és végpont lehetőséget.**

1. Másolja **az 1.** kulcsot a beviteli mező végén található ikonra kattintva.

1. Illessze be az értéket egy szövegszerkesztőbe.

1. Másolja a **Végpontot** a beviteli mező végén található ikonra kattintva.

1. Illessze be az értéket egy szövegszerkesztőbe.

## <a name="create-the-function-app"></a>A függvényalkalmazás létrehozása

1. A felső keresőmezőben keresse meg és válassza a **Függvényalkalmazás lehetőséget.**

1. Válassza a **Létrehozás** lehetőséget.

1. Írja be a következő értékeket.

    | Beállítás | Ajánlott érték | Megjegyzések |
    | ------- | ----- | ------- |
    | Előfizetés | Az Ön Azure-előfizetésének neve | |
    | Erőforráscsoport | **tweet-sentiment-tutorial** | Használja ugyanazt az erőforráscsoport-nevet az oktatóanyag során. |
    | Függvényalkalmazás neve | **TweetSentimentAPI** + egyedi utótag | A függvényalkalmazások nevei globálisan egyediek. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`. |
    | Közzététel | **Kód** | |
    | Futtatókörnyezet verme | **.NET** | Az Ön számára megadott függvénykód a C#-ban található. |
    | Verzió | Válassza ki a legújabb verziószámot | |
    | Region | Válassza ki az Önhöz legközelebb eső régiót | |

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.

1. Az üzembe helyezés befejezése után válassza az **Ugrás az erőforráshoz lehetőséget.**

## <a name="create-an-http-triggered-function"></a>HTTP által indított függvény létrehozása  

1. A Függvények ablak bal oldali _menüjében_ válassza a **Függvények lehetőséget.**

1. A **felső menüben** válassza a Hozzáadás lehetőséget, és adja meg a következő értékeket.

    | Beállítás | Érték | Megjegyzések |
    | ------- | ----- | ------- |
    | Fejlesztési környezet | **Fejlesztés a portálon** | |
    | Sablon | **HTTP-eseményindító** | |
    | Új függvény | **TweetSentimentFunction** | Ez a függvény neve. |
    | Engedélyszint | **Függvény** | |

1. Kattintson a **Hozzáadás gombra.**

1. Válassza a **Kód + teszt gombot.**

1. Illessze be az alábbi kódot a kódszerkesztő ablakába.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    A rendszer egy véleménypontszámot ad át a függvénynek, amely az értékhez tartozó kategórianevet adja vissza.

1. A módosítások **mentéséhez** kattintson az eszköztár Mentés gombjára.

    > [!NOTE]
    > A függvény teszteléshez válassza a **felső menü Teszt/Futtatás** parancsát. A Bemenet _lapon_ adja meg a értéket a Törzs `0.9` beviteli  mezőben, majd válassza a **Futtatás lehetőséget.** Ellenőrizze, hogy a _Kimenet_ szakasz _HTTP-válasz tartalmának_ mezője pozitív értéket _ad-e_ vissza.

Ezután hozzon létre egy logikai alkalmazást, amely integrálható a Azure Functions, a Twitter és a Cognitive Services API-val.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. A felső keresőmezőben keresse meg és válassza a **Logic Apps.**

1. Válassza a **Hozzáadás** lehetőséget.

1. Válassza **a Felhasználás** lehetőséget, és adja meg a következő értékeket.

    | Beállítás | Ajánlott érték |
    | ------- | --------------- |
    | Előfizetés | Az Ön Azure-előfizetésének neve |
    | Erőforráscsoport | **tweet-sentiment-tutorial** |
    | Logikai alkalmazás neve | **TweetSentimentApp** |
    | Region | Válassza ki az Önhöz legközelebb eső régiót, lehetőleg ugyanazt a régiót, mint az előző lépésekben. |

    Fogadja el az alapértelmezett értékeket az összes többi beállításnál.

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.

1. Az üzembe helyezés befejezése után válassza az **Erőforráshoz való ugrás lehetőséget.**

1. Válassza az **Üres logikai alkalmazás gombot.**

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Üres logikai alkalmazás gomb":::

1. A folyamat **mentéséhez** kattintson az eszköztár Mentés gombjára.

Most már használhatja a Logic Apps Designert, hogy szolgáltatásokat és eseményindítókat adjon az alkalmazáshoz.

## <a name="connect-to-twitter"></a>Csatlakozás a Twitterhez

Hozzon létre egy kapcsolatot a Twitterhez, hogy az alkalmazás lekérdezni tudja az új tweeteket.

1. Keressen rá a **Twitter** kifejezésre a felső keresőmezőben.

1. Válassza a **Twitter ikont.**

1. Válassza az **Új tweet közzétételekor** eseményindítót.

1. Adja meg a következő értékeket a kapcsolat beállításhoz.

    | Beállítás |  Érték |
    | ------- | ---------------- |
    | Kapcsolat neve | **MyTwitterConnection** |
    | Hitelesítés típusa | **Alapértelmezett megosztott alkalmazás használata** |

1. Válassza a **Bejelentkezés** lehetőséget.

1. Kövesse az előugró ablakban megjelenő utasításokat a Twitterbe való bejelentkezés befejezéséhez.

1. Ezután adja meg a következő értékeket _az Új tweet közzétévője mezőben._

    | Beállítás | Érték |
    | ------- | ----- |
    | Keresett szöveg | **#my-twitter-tutorial** |
    | Milyen étkeket szeretne ellenőrizni? | **15** a szövegmezőben, és <br> **Perc** a legördülő menüben |

1. A folyamat **mentéséhez** kattintson az eszköztár Mentés gombjára.

Ezután csatlakozzon a szövegelemzéshez az összegyűjtött tweetek hangulatának észlelése céljából.

## <a name="add-text-analytics-sentiment-detection"></a>Hangulatfelismerés Text Analytics hozzáadása

1. Válassza az **Új lépés** lehetőséget.

1. Keressen rá **Text Analytics** a keresőmezőbe.

1. Válassza a **Text Analytics** ikont.

1. Válassza a **Hangulat felismerése lehetőséget,** és adja meg a következő értékeket.

    | Beállítás | Érték |
    | ------- | ----- |
    | Kapcsolat neve | **TextAnalyticsConnection (SzöveganalyticsConnection)** |
    | Fiókkulcs | Illessze be Text Analytics korábban félretett fiókkulcsot. |
    | Webhely URL-címe | Illessze be Text Analytics korábban félretért végpontot. |

1. Válassza a **Létrehozás** lehetőséget.

1. Kattintson az _Add new parameter (Új_ paraméter  hozzáadása) mezőre, és jelölje be az előugró ablakban megjelenő dokumentumok melletti jelölőnégyzetet.

1. Kattintson az _Id - 1 szövegmezőn_ belülre a dinamikus tartalmak előugró ablakának megnyitásához.

1. A dinamikus _tartalom keresőmezőjében_ keresse meg az **azonosítót,** majd kattintson a **Tweet-azonosító elemre.**

1. Kattintson a _dokumentumokon belüli Szöveg – 1 szövegmezőre_ a dinamikus tartalom előugró ablakának megnyitásához.

1. A dinamikus _tartalom keresőmezőjében_ keressen a szövegre, **majd** kattintson a Tweet szövege **elemre.**

1. A **műveletek választására szolgáló** területen írja be a **Text Analytics** kifejezést, majd kattintson a **Hangulat felismerése** műveletre.

1. A folyamat **mentéséhez** kattintson az eszköztár Mentés gombjára.

A _Detect Sentiment mezőnek_ az alábbi képernyőképhez hasonlónak kell lennie.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Hangulat felismerése beállítások":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Hangulatkimenet csatlakoztatása függvényvégponthoz

1. Válassza az **Új lépés** lehetőséget.

1. Keressen rá **Azure Functions** a keresőmezőbe.

1. Válassza a **Azure Functions** ikont.

1. Keresse meg a függvény nevét a keresőmezőben. Ha követte a fenti útmutatást, a függvény neve **TweetSentimentAPI kifejezéssel kezdődik.**

1. Válassza a függvény ikont.

1. Válassza ki a **TweetSentimentFunction** elemet.

1. Kattintson a _Kérelem törzse mezőbe,_ és válassza a _Hangulat felismerése_ **pontszám** elemet az előugró ablakban.

1. A folyamat **mentéséhez** kattintson az eszköztár Mentés gombjára.

## <a name="add-conditional-step"></a>Feltételes lépés hozzáadása

1. Válassza a **Művelet hozzáadása** gombot.

1. Kattintson a _Vezérlő mezőbe,_ és keresse meg és válassza ki a **Vezérlő gombra** az előugró ablakban.

1. Válassza a **Feltétel lehetőséget.**

1. Kattintson az _Érték kiválasztása mezőbe,_ és válassza ki a _TweetSentimentFunction_ **törzs** elemet az előugró ablakban.

1. A **Choose** a value (Érték _kiválasztása) mezőben_ adja meg a Positive (Pozitív) értéket.

1. A folyamat **mentéséhez** kattintson az eszköztár Mentés gombjára.

## <a name="add-email-notifications"></a>E-mail-értesítések hozzáadása

1. Az Igaz _mező_ alatt válassza a **Művelet hozzáadása** gombot.

1. Keresse meg és válassza az **Office 365 Outlook** lehetőséget a szövegmezőben.

1. Keressen a **send (küldés) kifejezésre,** és válassza a Send an email (E-mail **küldése)** lehetőséget a szövegmezőben.

1. Válassza **a Bejelentkezés** gombot.

1. Kövesse az előugró ablakban megjelenő utasításokat az Office 365 Outlookba való bejelentkezés befejezéséhez.

1. A Címzett mezőbe írja be az _e-mail-címét._

1. Kattintson a _Tárgy mezőbe,_ majd a _TweetSentimentFunction_ alatti Törzs elemre.  Ha a _Törzs_ elem nem jelenik meg a listában, kattintson a **További információk** hivatkozásra a lehetőségek listájának kibontásához.

1. A Tárgy _szöveg Törzs_ eleme _után_ írja be a **Tweet innen: szöveget.**

1. A _Tweet a következőből:_ szöveg után  kattintson ismét a mezőre, és válassza a Felhasználónév lehetőséget az Új tweet _közzétévője_ lehetőségek listájában.

1. Kattintson a _Törzs mezőbe,_ és válassza a **Tweet** szövege lehetőséget az Új _tweet közzétételése lehetőséglistában._ Ha a _Tweet szövege_ elem nem jelenik meg a listában, kattintson a **További információ** hivatkozásra a beállításlista kibontásához.

1. A folyamat **mentéséhez** kattintson az eszköztár Mentés gombjára.

Az e-mail mezőnek most az alábbi képernyőképhez hasonlónak kell lennie.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="E-mail-értesítés":::

## <a name="run-the-workflow"></a>A munkafolyamat futtatása

1. Twitter-fiókjából tweetelje a következő szöveget: **#my-twitter-tutorial.**

1. Térjen vissza a Logic Apps Designerbe, és válassza a **Futtatás** gombot.

1. Ellenőrizze az e-mailben, hogy érkezett-e üzenet a munkafolyamatból.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag során létrehozott összes Azure-szolgáltatás és -fiók törléséhez törölje az erőforráscsoportot.

1. Keressen rá az **Erőforráscsoportok kifejezésre** a felső keresőmezőben.

1. Válassza ki **a tweet-sentiment-tutorial et.**

1. Válassza **az Erőforráscsoport törlése lehetőséget**

1. Írja **be a tweet-sentiment-tutorial** szöveget a szövegmezőbe.

1. Válassza a **Törlés** gombot.

Ha szeretné, visszatérhet Twitter-fiókjához, és törölheti a teszt tweetjeit a hírcsatornából.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kiszolgáló nélküli API létrehozása az Azure Functions használatával](functions-create-serverless-api.md)

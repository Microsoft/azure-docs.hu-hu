---
title: Több-turn beszélgetések – QnA Maker
description: A kérdésekkel és környezettel kezelheti a több fordulatot, más néven a robotot az egyik kérdésből a másikba. A többszörös bekapcsolás lehetősége van arra, hogy olyan háttérbeli beszélgetést lehessen használni, ahol az előző kérdés kontextusa befolyásolja a következő kérdést és választ.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 313d1a390c30e7e5612b8d9bab7783b6698c35fd
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618491"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Követő kérések használata több kérdés–válasz párból álló beszélgetések létrehozásához

A nyomon követéssel és a kontextussal kezelheti a több fordulatot, más néven _több bekapcsolást_ az egyik kérdésből a másikba.

Ha szeretné megtekinteni, hogyan működik a több funkció, tekintse meg az alábbi bemutató videót:

[![Több-turn beszélgetés a QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Mi az a több fordulatos beszélgetés?

Néhány kérdés nem válaszolható meg egyetlen fordulatban sem. Amikor megtervezi az ügyfélalkalmazás (chat bot) beszélgetéseit, a felhasználók megtehetnek egy olyan kérdést, amelyet szűrni vagy finomítani kell a helyes válasz megállapítása érdekében. Ezt a folyamatot a lehetséges kérdések segítségével teheti meg, ha a felhasználót a *követő kérésekkel* mutatja be.

Amikor egy felhasználó kérdést tesz fel, QnA Maker visszaadja a választ _és_ a követő utasításokat. Ez a válasz lehetővé teszi, hogy választási lehetőségként bemutassa a követő kérdéseket.

> [!CAUTION]
> A többszörös kapcsolású kérések nem a GYIK-dokumentumokból lettek kinyerve. Ha több kibontásra van szüksége, távolítsa el azokat a kérdőjeleket, amelyek a QnA-párokat GYIK-ként jelölik.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Multi-turn beszélgetés a csevegő robottal

A többszörös bekapcsolással a csevegési bot a felhasználóval folytatott beszélgetést kezeli a végső válasz meghatározásához, ahogy az az alábbi képen is látható:

![Egy Többablakos párbeszédpanel, amely a felhasználóknak egy beszélgetésen keresztül útmutatást nyújt.](../media/conversational-context/conversation-in-bot.png)

Az előző képen egy felhasználó elindított egy beszélgetést **a fiókom beírásával**. A Tudásbázis három csatolt kérdés-válasz párral rendelkezik. A válasz pontosításához a felhasználó kiválasztja a Tudásbázis három választási lehetőségének egyikét. A kérdés (#1) három utólagos figyelmeztetést tartalmaz, amelyek a csevegési robotban három lehetőségként jelennek meg (#2).

Ha a felhasználó kiválaszt egy beállítást (#3), a rendszer a finomítási beállítások (#4) következő listáját mutatja be. Ez a folyamat addig folytatódik (#5), amíg a felhasználó nem határozza meg a helyes, végleges választ (#6).

### <a name="use-multi-turn-in-a-bot"></a>Több bekapcsoló használata a robotban

Miután közzétette a KB-ot, a **robot létrehozása** gombra kattintva üzembe helyezheti QnA Maker robotját az Azure bot Service-be. A promptok megjelennek a csevegési ügyfeleken, amelyeken engedélyezve van a robot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Többfordulatú beszélgetés létrehozása a dokumentumok struktúrájából

Ha létrehoz egy tudásbázist, a **tudásbáziscikk feltöltése** szakasz megjeleníti az **URL-címek,. PDF vagy. docx fájlok jelölőnégyzetének többszörös kibontásának engedélyezése** beállítást.

![A többszörös Kibontás engedélyezésének jelölőnégyzete](../media/conversational-context/enable-multi-turn.png)

Ha ezt a beállítást választja, QnA Maker kibontja a dokumentum struktúrájában lévő hierarchiát. A rendszer átalakítja a hierarchiát, hogy kövesse az utasításokat, és a hierarchia gyökerének szülő QnA kell szolgálnia. Egyes dokumentumokban a hierarchia gyökere nem rendelkezik olyan tartalommal, amely válaszként szolgálhat, megadhatja az "alapértelmezett válasz szövege" karakterláncot helyettesítő válaszként a hierarchiák kinyeréséhez.

A többszörös kapcsolási struktúra csak URL-címekből, PDF-fájlokból vagy DOCX-fájlokból következtethető ki. A struktúra példájának megtekintéséhez tekintse meg a [Microsoft Surface felhasználói manuális PDF-fájljának](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)képét.

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="A képernyőképen egy példa látható a struktúrára egy felhasználói kézikönyvben." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>Saját multi-turn dokumentum kiépítése

Ha több bekapcsoló dokumentumot hoz létre, vegye figyelembe a következő irányelveket:

* Fejlécek és alfejlécek használatával jelezheti a hierarchiát. Például a H1 paraméterrel jelezheti, hogy a szülő QnA és a H2 jelzi a QnA, amelyet a rendszer kérni fog. A kisebb fejlécek méretének használata a későbbi hierarchia jelölésére. Ne használjon stílust, színt vagy valamilyen más mechanizmust a dokumentum struktúrájának megjelenítéséhez, QnA Maker nem fogja kibontani a többszörös bekapcsolási utasításokat.

* A fejléc első karakterének tőkésített kell lennie.

* Ne fejezze be a fejlécet kérdőjeltel `?` .

* Példaként használhatja a [minta dokumentumot](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) a saját többfunkciós dokumentum létrehozásához.

### <a name="adding-files-to-a-multi-turn-kb"></a>Fájlok hozzáadása több fordulatos KB-hoz

Hierarchikus dokumentum hozzáadásakor a QnA Maker a folyamaton belüli követő kéréseket határozza meg a beszélgetési folyamat létrehozásához.

1. A QnA Maker területen válasszon ki egy meglévő tudásbázist, amely az **URL-címek,. PDF vagy. docx fájlok többszörös kinyerésének engedélyezése** beállítással lett létrehozva. engedélyezve.
1. Lépjen a **Beállítások** lapra, és válassza ki a hozzáadni kívánt fájlt vagy URL-címet.
1. **Mentse és tanítsa** a tudásbázist.

> [!Caution]
> Az exportált TSV vagy XLS multi-turn Tudásbázis-fájl adatforrásként való használatának támogatása nem támogatott az új vagy az üres Tudásbázisban. Ezt a fájltípust a QnA Maker portál **Beállítások** oldaláról kell **importálnia** , hogy az exportált többszörös bekapcsolási kérések egy tudásbázishoz legyenek hozzáadva.

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Tudásbázis létrehozása több bekapcsolási kéréssel a Create API használatával

A [QnA Maker Create API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)használatával létrehozhat egy, a több bekapcsolást kérő kérdésekkel kapcsolatos tudásbázist. A rendszer hozzáadja a kéréseket a `context` tulajdonság `prompts` tömbéhez.

## <a name="show-questions-and-answers-with-context"></a>Kérdések és válaszok megjelenítése a kontextusban

Csökkentse a megjelenő kérdés-válasz párokat, hogy csak a környezetfüggő beszélgetésekkel rendelkezők jelenjenek meg.

Válassza a **megtekintési beállítások**, majd a **környezet megjelenítése** lehetőséget. A lista a követő kérdéseket tartalmazó kérdés-válasz párokat jeleníti meg.

![Kérdés-válasz párok szűrése környezetfüggő beszélgetések alapján](../media/conversational-context/filter-question-and-answers-by-context.png)

A többszörös kapcsolási környezet az első oszlopban jelenik meg.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="A képernyőképen a környezeti szakasz jelenik meg." lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

Az előző képen a **#1** félkövér szöveget jelöl az oszlopban, amely az aktuális kérdést jelzi. A szülő kérdés a sorban lévő legfelső elem. Az alábbi kérdések a csatolt kérdés-válasz párok. Ezek az elemek kiválaszthatók, így azonnal átléphet a többi környezeti elemre.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Egy meglévő kérdés-válasz pár hozzáadása követő üzenetként

Az eredeti kérdés, **a fiókom**, a követési kérések, például a **fiókok és a bejelentkezés**.

![A "fiókok és bejelentkezés" válaszok és követő kérések](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Olyan meglévő kérdés-válasz párokhoz adja hozzá a követési kérést, amely jelenleg nincs összekapcsolva. Mivel a kérdés nem kapcsolódik a kérdés-válasz párokhoz, a jelenlegi nézet beállítást módosítani kell.

1. Ha egy meglévő kérdés-válasz párokat szeretne felvenni egy követő parancssorba, válassza ki a kérdés-válasz párok sorát. A felszín manuális megjelenítéséhez keressen rá a **kijelentkezés** gombra a lista csökkentése érdekében.
1. A **kijelentkezés** sorában, a **Válasz** oszlopban válassza a **követő kérés hozzáadása** lehetőséget.
1. A következő értékeket kell megadnia a **követési kérés** előugró ablakának mezőiben:

    |Mező|Érték|
    |--|--|
    |Megjelenítendő szöveg|Adja meg **az eszköz kikapcsolását**. Ez a követési parancssorban megjelenítendő egyéni szöveg.|
    |Csak kontextus| Jelölje be ezt a jelölőnégyzetet. A rendszer csak akkor adja vissza a választ, ha a kérdés a kontextust adja meg.|
    |Hivatkozás a válaszra|Adja meg a **bejelentkezési képernyőt** a meglévő kérdés-válasz párok megkereséséhez.|

1.  Az egyik egyezést adja vissza. Válassza ki ezt a választ követő műveletként, majd kattintson a **Mentés** gombra.

    ![A "követő figyelmeztetés (előzetes verzió)" oldal](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Miután hozzáadta a követő parancssort, válassza a **Mentés és betanítás** lehetőséget a felső navigációs sávon.

### <a name="edit-the-display-text"></a>A megjelenített szöveg szerkesztése

Ha a rendszer egy követő kérdést hoz létre, és egy meglévő kérdés-válasz pár jelenik meg a **válaszra mutató hivatkozásként**, megadhat új **megjelenítendő szöveget**. Ez a szöveg nem helyettesíti a meglévő kérdést, és nem ad hozzá új alternatív kérdést. Ezek az értékek eltérnek egymástól.

1. A megjelenítendő szöveg szerkesztéséhez keresse meg és válassza ki a kérdést a **környezeti** mezőben.
1. A kérdés sorában válassza ki a követő kérdést a válasz oszlopban.
1. Jelölje ki a szerkeszteni kívánt megjelenítendő szöveget, majd válassza a **Szerkesztés** lehetőséget.

    ![A megjelenített szöveg szerkesztési parancsa](../media/conversational-context/edit-existing-display-text.png)

1. A **követő üzenet** előugró ablakában módosítsa a meglévő megjelenítendő szöveget.
1. Ha elkészült a megjelenítési szöveg szerkesztésével, válassza a **Mentés** lehetőséget.
1. A felső navigációs sávban **mentse és betanítása**.

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Új kérdés-válasz pár hozzáadása követő üzenetként

Amikor új kérdés-válasz párokat ad hozzá a tudásbázishoz, az egyes párokat nyomon követő rákérdezésként egy meglévő kérdéssel kell összekapcsolni.

1. A Tudásbázis eszköztárán keresse meg és válassza ki a meglévő kérdés-válasz párokat a fiókok számára, és **Jelentkezzen** be.

1. A kérdéshez tartozó **Válasz** oszlopban válassza a **következő üzenet hozzáadása** lehetőséget.
1. A követő **üzenet (előzetes verzió)** alatt hozzon létre egy új követő kérdést a következő értékek megadásával:

    |Mező|Érték|
    |--|--|
    |Megjelenítendő szöveg|*Hozzon létre egy Windows-fiókot*. A követő parancssorban megjelenítendő egyéni szöveg|
    |Csak kontextus|Jelölje be ezt a jelölőnégyzetet. A rendszer csak akkor adja vissza ezt a választ, ha a kérdés a kontextust adja meg.|
    |Hivatkozás a válaszra|Válaszként adja meg a következő szöveget:<br>*[Hozzon létre](https://account.microsoft.com/) egy Windows-fiókot új vagy meglévő e-mail-fiókkal*.<br>Az adatbázis mentésekor és betanításakor ez a szöveg lesz konvertálva. |
    |||

    ![Új kérdés és válasz létrehozása](../media/conversational-context/create-child-prompt-from-parent.png)

1. Válassza az **új létrehozása**, majd a **Mentés** lehetőséget.

    Ez a művelet létrehoz egy új kérdés-válasz párokat, és a kiválasztott kérdést követő üzenetként csatolja. A **környezeti** oszlop mindkét kérdés esetében egy követő prompt-kapcsolatot jelez.

1. Válassza a **megtekintési beállítások**, majd a [**környezet megjelenítése (előzetes verzió)**](#show-questions-and-answers-with-context)lehetőséget.

    Az új kérdés bemutatja, hogyan kapcsolódik egymáshoz.

    ![Új követő figyelmeztetés létrehozása](../media/conversational-context/new-qna-follow-up-prompt.png)

    A szülő kérdés egy új kérdést jelenít meg az egyik lehetőség közül.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="A képernyőfelvételen a környezeti oszlop látható, a két kérdés esetében pedig követő kérdéses kapcsolat látható." lightbox="../media/conversational-context/child-prompt-created.png":::

1. Miután hozzáadta a követő parancssort, válassza a **Mentés és betanítás** lehetőséget a felső navigációs sávon.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Többszörös bekapcsolás megtekintése a követő kérések tesztelése során

Ha a **teszt** ablaktáblán a követési kérésekkel kapcsolatos kérdést teszteli, a válasz tartalmazza a követő utasításokat.

![A válasz tartalmazza a követő utasításokat](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Egy JSON-kérelem, amely egy kezdeti választ ad vissza, és követő kéréseket küld

Az üres `context` objektum használatával kérje a választ a felhasználó kérdésére, és vegyen fel követő utasításokat.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Egy kezdeti válasz és követő kérések visszaadására szolgáló JSON-válasz

Az előző szakaszban választ kértek, és a rendszer a **fiókokat és a bejelentkezést** követő utasításokat kér. A válasz tartalmazza a kérdéses adatokat, amelyek a következő *válaszokban találhatók: [0]. Context*, valamint a felhasználónak megjelenítendő szöveg.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n  Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

A `prompts` tömb szövegeket biztosít a `displayText` tulajdonságban és az `qnaId` értékben. Ezeket a válaszokat a beszélgetési folyamat következő megjelenő lehetőségeiként jelenítheti meg, majd az alábbi kérelemben elküldheti a kijelölt `qnaId` visszaQnA Makernak.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Egy JSON-kérelem, amely nem kezdeti választ ad vissza, és követő kéréseket küld

Töltse ki az `context` objektumot az előző környezet belefoglalásához.

A következő JSON-kérelemben az aktuális kérdés a *Windows Hello használata a bejelentkezéshez* , az előző kérdés pedig a *fiókok és a bejelentkezés*.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Egy nem kezdeti válasz és követő kérések visszaadására szolgáló JSON-válasz

Az QnA Maker _GenerateAnswer_ JSON-válasz tartalmazza az `context` objektum első eleme tulajdonságában található követő utasításokat `answers` :

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>A Tudásbázis lekérdezése a QnA Maker azonosítója alapján

Ha többfordulatos funkciót használó egyéni alkalmazást hoz létre. Az első kérdés válaszában a rendszer a követő lépéseket és a hozzá tartozó kapcsolódó kéréseket `qnaId` adja vissza. Most, hogy már rendelkezik AZONOSÍTÓval, átadhatja ezt a követési kérés törzsében. Ha a kérelem törzse tartalmazza a `qnaId` és a környezeti objektumot (amely az előző QnA Maker tulajdonságokat tartalmazza), akkor a GenerateAnswer a pontos kérdést az azonosító alapján adja vissza ahelyett, hogy a rangsorolási algoritmus használatával megkeresse a választ a kérdés szövege alapján.

## <a name="display-order-is-supported-in-the-update-api"></a>A megjelenítési sorrend támogatott a frissítési API-ban

A JSON-válaszban visszaadott [szöveg és megjelenítési sorrend](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)is támogatott a [frissítési API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)-val való szerkesztéshez.

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Több bekapcsolási kérés hozzáadása vagy törlése a frissítési API-val

A [QnA Maker Update API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)használatával több bekapcsolási kérést is hozzáadhat vagy törölhet.  A rendszer hozzáadja a kéréseket a `context` tulajdonság `promptsToAdd` tömbéhez és a `promptsToDelete` tömbhöz.

## <a name="export-knowledge-base-for-version-control"></a>Tudásbázis exportálása a verziókövetés számára

A QnA Maker támogatja a verziókövetés használatát, többek között az exportált fájlban lévő több lépésből álló beszélgetés lépéseivel.

## <a name="next-steps"></a>Következő lépések

További információ a [párbeszédpanel](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) környezetfüggő beszélgetésekről, vagy további információ a [fogalmi robot kialakításáról a több-turn beszélgetések esetében](/azure/bot-service/bot-builder-conversations).

> [!div class="nextstepaction"]
> [Tudásbázis migrálása](../Tutorials/migrate-knowledge-base.md)
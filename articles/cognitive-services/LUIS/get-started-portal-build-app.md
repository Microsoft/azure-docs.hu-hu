---
title: 'Rövid útmutató: új alkalmazás létrehozása a LUIS portálon'
description: Ebben a rövid útmutatóban létrehoz egy alkalmazás, szándékok és entitások alaprészeit, valamint teszteli a minta-kisegítő lehetőséget a LUIS-portálon.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: c2f2bafe68dd3f3dad704ba12cdd4d01bf4f6588
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278762"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Rövid útmutató: új alkalmazás létrehozása a LUIS portálon

Ebben a rövid útmutatóban egy új alkalmazást hoz létre a LUIS portálon. Először hozza létre az alkalmazás, a **szándékok** és az **entitások** alapvető részeit. Ezt követően tesztelje az alkalmazást úgy, hogy az interaktív tesztelési panelen megadta a minta felhasználótól az előre jelzett szándékot.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Alkalmazás létrehozása

Alkalmazás létrehozásához válassza az **+ új alkalmazás** lehetőséget. 

A megjelenő ablakban adja meg a következő adatokat:

|Név  |Leírás  |
|---------|---------|
|Név     | Az alkalmazás neve. Például: "Home Automation". Kötelező.        |
|Kultúra     | A nyelv, amelyet az alkalmazás megértett és beszél. Kötelező.   |
|Leírás | Az alkalmazás leírása. Választható.
|Előrejelzési erőforrás | Az előrejelzési erőforrás, amely a lekérdezéseket fogja kapni. Választható. |

Válassza a **Kész** lehetőséget.

>[!NOTE]
>A kulturális környezet az alkalmazás létrehozása után nem módosítható.


## <a name="create-intents"></a>Szándékok létrehozása

A LUIS-alkalmazás létrehozása után szándékait kell létrehoznia. A leképezések lehetővé teszik szöveg besorolását a felhasználóktól. Előfordulhat például, hogy egy emberi erőforrás alkalmazásnak két funkciója van. Az emberek segítése:

 1. Feladatok keresése és alkalmazása
 1. A feladatokra alkalmazandó űrlapok keresése

Az alkalmazás két különböző _szándéka az alábbi célokhoz_ igazodik:

|Szándék|Példa szövegre a felhasználótól<br>más néven a _Kimondás_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

A leképezések létrehozásához hajtsa végre a következő lépéseket:

1. Az alkalmazás létrehozása után győződjön meg arról, hogy a **Build** szakasz **cél** lapján található. Válassza a **Létrehozás** lehetőséget.

   [![Képernyőkép: a Létrehozás gombra kattintva hozzon létre új leképezést](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Adja meg a leképezés nevét, `FindForm` majd kattintson a **kész** gombra.

## <a name="add-an-example-utterance"></a>Példa Kimondás hozzáadása

A leképezések létrehozása után példaként adja hozzá a hosszúságú kimondott szöveg. Például a hosszúságú kimondott szöveg olyan szöveg, amelyet a felhasználó egy csevegési robotba vagy más ügyfélalkalmazásba ír. A felhasználó szövegének szándékát egy LUIS-szándékra képezik le. Ebben a példában az alkalmazás `FindForm` célja, például a hosszúságú kimondott szöveg fogja tartalmazni az űrlapot. Az ügyfélalkalmazás számára szükséges, hogy megfeleljen a felhasználó kérelmének, ezért fontos, hogy belefoglalja azt a teljességbe.

A **cél** lapon `FindForm` adja hozzá a következő példát a hosszúságú kimondott szöveg ( **példa: Kimondás**). 

|#|Példák kimondott szövegekre|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Ez a példa a hosszúságú kimondott szöveg a következő módokon változhat:

* Kimondás hossza
* [központozási](luis-reference-application-settings.md#punctuation-normalization)
* szó választása
* a művelet feszült (is, volt, lesz)
* Word-sorrend


## <a name="create-a-regular-expression-entity"></a>Reguláris kifejezés entitásának létrehozása

Ha vissza szeretné állítani az űrlap számát a futásidejű előrejelzési válaszban, az űrlap számát entitásként kell kinyerni. Mivel az űrlap-szám szövege nagyon strukturált, reguláris kifejezési entitást használhat. Hozza létre a reguláris kifejezés entitását a következő lépésekkel:

1. Válassza az **entitások** lehetőséget a bal oldali menüben.

1. Válassza a **Létrehozás** lehetőséget az **entitások** lapon.

1. Adja meg a nevet `FormNumber` , válassza ki a **regex** entitás típusát.

1. Adja meg a reguláris kifejezést a `hrf-[0-9]{6}` **regex** mezőben. Ez a bejegyzés megegyezik a literális karakterekkel, `hrf-` és lehetővé teszi pontosan hat számjegy használatát, majd válassza a **Létrehozás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A reguláris kifejezés entitás létrehozásának képernyőképe](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Ez az entitás Kinyer minden olyan szöveget, amely megfelel a reguláris kifejezésnek bármelyik szándékban.

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz

A **none** cél a tartalék szándék, és nem hagyható üresen. Ennek a szándéknak tartalmaznia kell minden 10 példa hosszúságú kimondott szöveg, amelyet az alkalmazás többi szándékához hozzáadott.

A **nincs** leképezési példa hosszúságú kimondott szöveg az ügyfélalkalmazás tartományán kívül kell lennie.

1. Válassza a bal oldali menüben a **leképezések** lehetőséget, majd válassza a **nincs** lehetőséget a leképezések listából.

1. Adja hozzá a következő példa hosszúságú kimondott szöveg a szándékhoz:

   |Nincs leképezési példa hosszúságú kimondott szöveg|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Ebben az alkalmazásban a példa hosszúságú kimondott szöveg a tartományon kívül esnek. Ha a tartománya állatokat vagy az óceánt is tartalmazza, akkor a **none (nincs** ) cél hosszúságú kimondott szöveg eltérő példát kell használnia.

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Tekintse meg a reguláris kifejezés entitást a példában szereplő hosszúságú kimondott szöveg

1. Győződjön meg arról, hogy az entitás a **FindForm**  szándékban található, a bal oldali menüben a **leképezések** lehetőség kiválasztásával. Ezután válassza a **FindForm** szándék lehetőséget.

   Az entitás meg van jelölve, ahol megjelenik a példában szereplő hosszúságú kimondott szöveg.

   > [!div class="mx-imgBorder"]
   > [![Képernyőkép az entitásokkal jelölt összes példa hosszúságú kimondott szöveg](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Tesztelje az új alkalmazást az interaktív teszt panel segítségével

A LUIS-portál interaktív **teszt** paneljén ellenőrizheti, hogy az entitás kinyerve lett-e az új hosszúságú kimondott szöveg, az alkalmazás még nem látott.

1. Válassza a **tesztelés** lehetőséget a jobb felső menüben.

1. Adjon hozzá egy új értéket, majd nyomja le az ENTER billentyűt:

   ```Is there a form named hrf-234098```

    Válassza a **vizsgálat** lehetőséget az entitások előrejelzésének megtekintéséhez.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép: az új kifejezés tesztelése a teszt ablaktáblán](./media/get-started-portal-build-app/test-new-utterance.png)

   A felső előre jelzett szándék megfelelően **FindForm** , több mint 90%-os megbízhatósággal (0,977). A **FormNumber** entitás a hrf-234098 értékkel van kinyerve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezzel a rövid útmutatóval végzett, és nem a következő rövid útmutatóra vált, válassza a **saját alkalmazások** lehetőséget a felső navigációs menüből. Ezután jelölje be az alkalmazás bal oldali jelölőnégyzetét a listából, majd válassza a  **Törlés** lehetőséget a lista fölötti helyi eszköztáron.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazás üzembe helyezése](get-started-portal-deploy-app.md)

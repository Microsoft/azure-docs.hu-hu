---
title: 'Oktatóanyag: mérsékelt Facebook-tartalom – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Ebből az oktatóanyagból megtudhatja, hogyan használható a gépi tanuláson alapuló Content Moderator a Facebook-bejegyzések és-megjegyzések moderálásának megkönnyítésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.openlocfilehash: f3b43ed6a86276b308599f9091d581423b0f363c
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220990"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Oktatóanyag: mérsékelt Facebook-bejegyzések és-parancsok az Azure Content Moderator

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Content Moderatort a hozzászólások és megjegyzések moderálásához egy Facebook-oldalon. A Facebook elküldi a látogatók által közzétett tartalmat a Content Moderator szolgáltatásnak. Ezután a Content Moderator-munkafolyamatok közzéteszik a tartalmat, vagy a felülvizsgálati eszközön hozhatnak létre felülvizsgálatokat a tartalom pontszámai és a küszöbértékek függvényében. Ennek a forgatókönyvnek a működéséhez tekintse meg a [Build 2017 bemutató videóját](https://channel9.msdn.com/Events/Build/2017/T6033) .

> [!IMPORTANT]
> A 2018-ben a Facebook szigorúbb bevezetési szabályzatot vezetett be a Facebook-alkalmazásokhoz. Nem fogja tudni befejezni az oktatóanyag lépéseit, ha az alkalmazást a Facebook felülvizsgálati csapata nem tekinti át és hagyta jóvá.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Content Moderator-csapat létrehozása.
> * A Content Moderator és a Facebook HTTP-eseményeit figyelő Azure Functions-függvények létrehozása.
> * Facebook-oldal csatolása Content Moderator Facebook-alkalmazás használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

Ez az ábra a forgatókönyv egyes összetevőit szemlélteti:

![A Facebook információinak a "FBListener" és a "CMListener" használatával történő elküldésére szolgáló Content Moderator ábrája](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. A Content Moderator szolgáltatásra való előfizetéshez és a kulcs lekéréséhez kövesse az [Cognitive Services fiók létrehozása](../cognitive-services-apis-create-account.md) című témakör utasításait.
- Egy [Facebook-fiók](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Felülvizsgálati csapat létrehozása

Tekintse át a [Content moderator kipróbálása a webes](quick-start.md) útmutatóban című témakört, amely útmutatást nyújt a [Content moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) regisztrálásához és egy felülvizsgálati csapat létrehozásához. Jegyezze fel a **csoport azonosító** értékét a **hitelesítő adatok** lapon.

## <a name="configure-image-moderation-workflow"></a>Képek moderálási munkafolyamatának konfigurálása

Egyéni rendszerkép-munkafolyamatok létrehozásához tekintse [meg a Definiálás, tesztelés és használat munkafolyamatok](review-tool-user-guide/workflows.md) útmutatót. A Content Moderator ezt a munkafolyamatot fogja használni a képek Facebookban való automatikus ellenőrzéséhez és a felülvizsgálati eszköz elküldéséhez. Jegyezze fel a munkafolyamat **nevét**.

## <a name="configure-text-moderation-workflow"></a>Szöveges moderálási munkafolyamat konfigurálása

Ismét tekintse meg a [munkafolyamatok meghatározása, tesztelése és használata](review-tool-user-guide/workflows.md) útmutatót; Ezúttal hozzon létre egy egyéni szöveges munkafolyamatot. A Content Moderator ezt a munkafolyamatot fogja használni a szöveges tartalom automatikus vizsgálatához. Jegyezze fel a munkafolyamat **nevét**.

![Szövegalapú munkafolyamat konfigurálása](images/text-workflow-configure.PNG)

Tesztelje a munkafolyamatot a **munkafolyamat végrehajtása** gomb használatával.

![Szövegalapú munkafolyamat tesztelése](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions-függvény létrehozása

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-függvényalkalmazást az [Azure Functions](../../azure-functions/functions-create-function-app-portal.md) oldalon leírtak szerint.
1. Nyissa meg az újonnan létrehozott függvényalkalmazás.
1. Az alkalmazáson belül lépjen a **platform szolgáltatások** lapra, és válassza a **konfiguráció** lehetőséget. A következő lap **Alkalmazásbeállítások** szakaszában válassza az **új Alkalmazásbeállítás** lehetőséget a következő kulcs/érték párok hozzáadásához:
    
    | Alkalmazás-beállítás neve | érték   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | A Content Moderator csapatazonosítójának beállítása  | 
    | `cm:SubscriptionKey` | A Content Moderator előfizetői azonosítója – lásd: [Hitelesítő adatok](./review-tool-user-guide/configure.md#credentials) |
    | `cm:Region` | A Content Moderator-régió neve szóközök nélkül. Ezt a nevet az Azure-erőforrás **Áttekintés** lapjának **Location (hely** ) mezőjében tekintheti meg.|
    | `cm:ImageWorkflow` | A képek esetében futtatandó munkafolyamat neve |
    | `cm:TextWorkflow` | A szövegek esetében futtatandó munkafolyamat neve |
    | `cm:CallbackEndpoint` | Az útmutatóban később létrehozandó CMListener-függvényalkalmazás URL-címe |
    | `fb:VerificationToken` | A Facebook-hírcsatornák eseményeire való előfizetéshez használt titkos jogkivonat |
    | `fb:PageAccessToken` | A Facebook Graph API hozzáférési jogkivonatának nincs lejárati ideje, és lehetővé teszi, hogy a függvény az Ön nevében elrejtsen vagy töröljön bejegyzéseket. Ezt a tokent egy későbbi lépésben kapja meg. |

    Kattintson a **Save (Mentés** ) gombra az oldal tetején.

1. Lépjen vissza a **platform szolgáltatásai** lapra. A **+** bal oldali ablaktábla gombjának használatával hozza létre az **új függvény** ablaktáblát. A létrehozandó függvény a Facebooktól érkező eseményeket fogja kapni.

    ![Azure Functions panel a funkció hozzáadása gomb kiemelve.](images/new-function.png)

    1. Kattintson a **http-triggert** tartalmazó csempére.
    1. Adja meg az **FBListener** nevet. Az **Engedélyszint** mező értéke legyen **Függvény**.
    1. Kattintson a **Létrehozás** lehetőségre.
    1. Cserélje le a **Run. CSX** tartalmát a **FbListener/Run. CSX** tartalmára.

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Hozzon létre egy új, **CMListener** nevű **http-trigger** függvényt. Ez a függvény Content Moderator-események fogadását végzi. Cserélje le a **Run. CSX** tartalmát a **CMListener/Run. CSX** tartalmára.

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Facebook-oldal és -alkalmazás konfigurálása

1. Facebook-alkalmazás létrehozása.

    ![Facebook fejlesztői oldal](images/facebook-developer-app.png)

    1. Lépjen a [Facebook fejlesztői webhelyére](https://developers.facebook.com/).
    1. Válassza a **saját alkalmazások** lehetőséget.
    1. Adjon hozzá egy új alkalmazást.
    1. Adjon meg egy nevet
    1. **Webhookok kiválasztása – > beállítása**
    1. Válassza az **oldal** lehetőséget a legördülő menüből, és válassza az **előfizetés erre az objektumra** lehetőséget.
    1. Visszahívási URL-címként adja meg **FBListener URL**-címét, valamint a **Függvényalkalmazás beállításai** területen konfigurált **Ellenőrzési jogkivonatot**.
    1. Ha a feliratkozás megtörtént, görgessen le a hírcsatornához, és kattintson a **Feliratkozás** gombra.
    1. A **hírcsatorna** -sor **tesztelés** gombjára kattintva Tesztüzenet küldése a FBListener Azure-függvénynek, majd a **Küldés a saját kiszolgálóra** gomb megnyomásával. Ekkor meg kell jelennie a FBListener érkező kérésnek.

1. Facebook-oldal létrehozása.

    > [!IMPORTANT]
    > 2018-ben a Facebook a Facebook-alkalmazások szigorúbb ellenőrzését is végrehajtotta. A 2., 3. és 4. szakaszt nem fogja tudni végrehajtani, ha az alkalmazást a Facebook felülvizsgálati csapata nem tekinti át és hagyta jóvá.

    1. Lépjen a [Facebookra](https://www.facebook.com/bookmarks/pages), és hozzon létre egy **új Facebook-oldalt**.
    1. Az alábbi lépéseket követve engedélyezze a Facebook alkalmazás számára az oldalhoz való hozzáférést:
        1. Nyissa meg a [Graph API Explorert](https://developers.facebook.com/tools/explorer/).
        1. Válassza az **alkalmazás** lehetőséget.
        1. Válassza a **Page Access Token** (Oldalhozzáférési jogkivonat) elemet, majd küldjön egy **GET** kérést.
        1. Válassza ki az **oldal azonosítóját** a válaszban.
        1. Fűzze hozzá a **/subscribed_apps** sztringet az URL-címhez, majd küldjön egy **GET** (üres válasz) kérést.
        1. Küldjön el egy **POST** kérést. A következő választ fogja kapni: **success: true**.

3. Lejárati idővel nem rendelkező Graph API hozzáférési jogkivonat létrehozása.

    1. Nyissa meg a [Graph API Explorert](https://developers.facebook.com/tools/explorer/).
    2. Válassza az **Application** (Alkalmazás) elemet.
    3. Válassza a **Get User Access Token** (Felhasználói hozzáférési jogkivonat beszerzése) lehetőséget.
    4. A **Select Permissions** (Engedélyek kiválasztása) területen válassza a **manage_pages** és a **publish_pages** elemeket.
    5. A **hozzáférési jogkivonatot** (Rövid élettartamú jogkivonat) a következő lépésben fogjuk használni.

4. A következő lépésekhez a Postmant használjuk.

    1. Nyissa meg a **Postmant** (vagy szerezze be [innen](https://www.getpostman.com/)).
    2. Importálja a következő két fájlt:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json) (Postman-gyűjtemény)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json) (Postman-környezet)       
    3. Frissítse a következő környezeti változókat:
    
        | Kulcs | Érték   | 
        | -------------------- |-------------|
        | appId   | Ide szúrja be a Facebook-alkalmazás azonosítóját  | 
        | appSecret | Ide szúrja be a Facebook-alkalmazás titkos kódját | 
        | short_lived_token | Ide szúrja be az előző lépésben létrehozott rövid élettartamú felhasználói hozzáférési jogkivonatot |
    4. Futtassa a gyűjteményben lévő három API-t: 
        1. Válassza a **Generate Long-Lived Access Token** (Hosszú élettartamú hozzáférési jogkivonat létrehozása) elemet, majd kattintson a **Send** (Küldés) gombra.
        2. Válassza a **Get User ID** (Felhasználói azonosító lekérése) lehetőséget, majd kattintson a **Send** (Küldés) gombra.
        3. Válassza a **Get Permanent Page Access Token** (Állandó oldalhozzáférési jogkivonat lekérése) elemet, majd kattintson a **Send** (Küldés) gombra.
    5. Másolja ki a válaszból az **access_token** értékét, és rendelje hozzá az **fb:PageAccessToken** alkalmazásbeállításhoz.

A megoldás a Facebook-oldalon közzétett összes képet és szöveget elküldi a Content Moderatornek. Ezután meghívja a korábban konfigurált munkafolyamatokat. A munkafolyamatokban definiált feltételeknek nem megfelelő tartalom a felülvizsgálati eszközön belüli felülvizsgálatokra lesz átadva. A tartalom többi része automatikusan közzé lesz téve.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan programot állít be, amellyel elemezheti a termék rendszerképeit, megcímkézheti a termékeket, és lehetővé teheti, hogy a felülvizsgálati csapat tájékozott döntéseket hozhasson a tartalom moderálásával kapcsolatban. Következő lépésként tekintse meg a képek moderálásának részleteit.

> [!div class="nextstepaction"]
> [Képmoderálás](./image-moderation-api.md)
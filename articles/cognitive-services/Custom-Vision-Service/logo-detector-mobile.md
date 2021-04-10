---
title: 'Oktatóanyag: egyéni emblémás detektor használata az Azure-szolgáltatások felismeréséhez – Custom Vision'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy, a Custom Visiont használó minta alkalmazást használ a logo észlelési forgatókönyv részeként. Megtudhatja, hogyan használhatók a Custom Vision a többi összetevővel egy végpontok közötti alkalmazás továbbításához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 76e7b061670f603007cefd6d7aed503e0e0d3d6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98941780"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Oktatóanyag: az Azure szolgáltatás emblémáinak felismerése kamerás képeken

Ebben az oktatóanyagban egy olyan minta alkalmazást fog vizsgálni, amely Custom Visiont használ egy nagyobb forgatókönyv részeként. A Xamarin. Forms alkalmazás mobil platformokhoz készült AI Visual kiépítő alkalmazás, amely az Azure-szolgáltatások emblémáit elemzi, majd üzembe helyezi a tényleges szolgáltatásokat a felhasználó Azure-fiókjával. Itt megtudhatja, hogyan használja a Custom Vision a más összetevőkkel együttműködve, hogy egy hasznos, végpontok közötti alkalmazást nyújtson. Saját maga is futtathatja a teljes alkalmazási forgatókönyvet, vagy elvégezheti a telepítés Custom Visionét, és megvizsgálhatja, hogy az alkalmazás hogyan használja azt.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Hozzon létre egy egyéni objektum-detektort az Azure-szolgáltatás emblémáinak felismeréséhez.
> - Az alkalmazás összekötése az Azure Computer Vision és Custom Vision használatával.
> - Hozzon létre egy Azure-beli egyszerű fiókot az Azure-szolgáltatások alkalmazásból való üzembe helyezéséhez.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)
- A Xamarin munkaterhelés a Visual studióhoz (lásd: [Xamarin telepítése](/xamarin/cross-platform/get-started/installation/windows))
- IOS-vagy Android-emulátor a Visual studióhoz
- Az [Azure CLI](/cli/azure/install-azure-cli-windows) (opcionális)

## <a name="get-the-source-code"></a>A forráskód lekérése

Ha a megadott webalkalmazást szeretné használni, akkor a GitHubon a [mesterséges intelligencia vizualizáció](https://github.com/Microsoft/AIVisualProvision) kiosztási tárházában lévő forráskódot klónozással vagy letöltéssel töltheti le. Nyissa meg a *Source/VisualProvision. SLN* fájlt a Visual Studióban. Később a projekt egyes fájljait is szerkesztheti, így futtathatja az alkalmazást.

## <a name="create-an-object-detector"></a>Objektum-detektor létrehozása

Jelentkezzen be a [Custom Vision webhelyére](https://customvision.ai/) , és hozzon létre egy új projektet. Egy objektum-észlelési projektet kell megadni, és az embléma tartományt kell használnia; Ez lehetővé teszi, hogy a szolgáltatás az embléma észlelésére optimalizált algoritmust használjon. 

![Új – projekt ablak a Chrome böngésző Custom Vision webhelyén](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ezután a logo észlelési algoritmust betaníthatja az Azure-szolgáltatás emblémáinak képeinek feltöltésével, és manuálisan is megcímkézheti őket. A AIVisualProvision-tárház a használható betanítási lemezképek készletét tartalmazza. A webhelyen válassza a **képek hozzáadása** gombot a **betanítási lemezképek** lapon. Ezután lépjen az adattár **Documents/images/Training_DataSet** mappájába. Az emblémákat manuálisan kell címkéznie az egyes képekben, így ha csak a projekt tesztelését végzi, érdemes lehet csak a képek egy részhalmazát feltölteni. Töltsön fel legalább 15 példányt a használni kívánt címkék közül.

A betanítási lemezképek feltöltése után válassza ki az elsőt a kijelzőn. Ekkor megjelenik a címkézési ablak. Mezők rajzolása és címkék társítása minden egyes képhez. 

![Embléma címkézése a Custom Vision webhelyén](media/azure-logo-tutorial/tag-logos.png)

Az alkalmazás úgy van konfigurálva, hogy az adott címkével rendelkező karakterláncokkal működjön. A definíciók a *Source\VisualProvision\Services\Recognition\RecognitionService.cs* fájlban találhatók:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

A képek címkézése után lépjen a jobb oldalon a következőre. A befejezéskor a címkézés ablak bezárásához.

## <a name="train-the-object-detector"></a>Az objektum-detektor betanítása

A bal oldali ablaktáblában állítsa a **címkéket** a **címkézve** értékre a képek megjelenítéséhez. Ezután válassza a lap tetején található zöld gombot a modell betanításához. Az algoritmus az új képeken megjelenő címkék felismerésére lesz kiképezve. A modell a meglévő lemezképeken is tesztelni fogja a pontossági pontszámok létrehozását.

![A Custom Vision webhely a betanítási lemezképek lapon. Ezen a képernyőfelvételen a vonat gombja van aláhúzva](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Az előrejelzési URL-cím lekérése

A modell képzése után készen áll az alkalmazásba való integrálásra. Be kell szereznie a végpont URL-címét (az alkalmazás által lekérdezett modell címét) és az előrejelzési kulcsot (az alkalmazásnak az előrejelzési kérelmekhez való hozzáférésének biztosításához). A **teljesítmény** lapon kattintson az **előrejelzési URL** gombra az oldal tetején.

![Az URL-címet és API-kulcsot megjelenítő előrejelzési API-ablakot bemutató Custom Vision webhely](media/azure-logo-tutorial/cusvis-endpoint.png)

Másolja a végponti URL-címet és az **előrejelzési kulcs** értékét a *Source\VisualProvision\AppSettings.cs* fájl megfelelő mezőibe:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Custom Vision használatának vizsgálata

Nyissa meg a *Source/VisualProvision/Services/Recognition/CustomVisionService. cs* fájlt, és tekintse meg, hogyan használja az alkalmazás a Custom Vision kulcsot és a végpont URL-címét. A **PredictImageContentsAsync** metódus a képfájlok egy bájtos streamjét veszi fel, és egy lemondási tokent (aszinkron Feladatkezelő esetén), meghívja a Custom Vision ELŐREJELZÉSi API-t, és visszaadja az előrejelzés eredményét. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Ez az eredmény egy **PredictionResult** -példány formáját ölti, amely magában foglalja az **előrejelzési** példányok listáját. Az **Előrejelzés** tartalmaz egy észlelt címkét és a hozzá tartozó határolókeret helyét a képen.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Ha többet szeretne megtudni arról, hogy az alkalmazás hogyan kezeli ezeket az információkat, kezdje a **GetResourcesAsync** metódussal. Ez a metódus a *Source/VisualProvision/Services/Recognition/RecognitionService. cs* fájlban van definiálva.  

## <a name="add-computer-vision"></a>Computer Vision hozzáadása

Az oktatóanyag Custom Vision része befejeződött. Ha futtatni szeretné az alkalmazást, a Computer Vision szolgáltatást is integrálnia kell. Az alkalmazás a Computer Vision Text felismerése funkciót használja az embléma észlelési folyamatának kiegészítéséhez. Az Azure-emblémák a megjelenésük alapján *vagy* a közelében kinyomtatott szöveg alapján felismerhetők. A Custom Vision-modellekkel ellentétben a rendszer bizonyos műveleteket hajt végre a képeken vagy videókon, Computer Vision.

Fizessen elő a Computer Vision szolgáltatásra a kulcs és végpont URL-címének lekéréséhez. A lépéssel kapcsolatos segítségért lásd: [előfizetés-kulcsok beszerzése](../cognitive-services-apis-create-account.md?tabs=singleservice%2Cwindows).

![A Azure Portal Computer Vision szolgáltatása a kiválasztott rövid útmutató menüjével. A kulcsok hivatkozását a rendszer az API-végpont URL-címének megfelelően ismerteti](media/azure-logo-tutorial/comvis-keys.png)

Ezután nyissa meg a *Source\VisualProvision\AppSettings.cs* fájlt, és töltse ki a `ComputerVisionEndpoint` és a `ComputerVisionKey` változókat a megfelelő értékekkel.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az alkalmazáshoz egy Azure egyszerű szolgáltatás fiókra van szükség a szolgáltatások Azure-előfizetéshez való telepítéséhez. Az egyszerű szolgáltatásnév lehetővé teszi, hogy az Azure szerepköralapú hozzáférés-vezérlés használatával meghatározott engedélyeket delegáljon egy alkalmazáshoz. További tudnivalókat az [egyszerű szolgáltatásokról szóló útmutatóban](/azure-stack/operator/azure-stack-create-service-principals)talál.

Az itt látható módon létrehozhat egy egyszerű szolgáltatásnevet Azure Cloud Shell vagy az Azure CLI használatával. A kezdéshez jelentkezzen be, és válassza ki a használni kívánt előfizetést.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Ezután hozza létre az egyszerű szolgáltatásnevet. (Ez a folyamat hosszabb időt is igénybe vehet.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

A sikeres befejezést követően a következő JSON-kimenetnek kell megjelennie, beleértve a szükséges hitelesítő adatokat is.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Jegyezze fel a `clientId` és az `tenantId` értékeket. Adja hozzá őket a megfelelő mezőkhöz a *Source\VisualProvision\AppSettings.cs* fájlban.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezen a ponton a következőhöz férhet hozzá az alkalmazáshoz:

- Egy betanított Custom Vision modell
- A Computer Vision szolgáltatás
- Egy egyszerű szolgáltatás fiókja

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. A Visual Studio Megoldáskezelő válassza ki a **VisualProvision. Android** projektet vagy a **VisualProvision. iOS** projektet. Válasszon ki egy megfelelő emulátort vagy csatlakoztatott mobileszköz elemet a fő eszköztár legördülő menüjében. Ezután futtassa az alkalmazást.

    > [!NOTE]
    > IOS-emulátor futtatásához MacOS-eszközre lesz szüksége.

1. Az első képernyőn adja meg az egyszerű szolgáltatás ügyfél-AZONOSÍTÓját, a bérlő AZONOSÍTÓját és a jelszót. Válassza a **Bejelentkezés** gombot.

    > [!NOTE]
    > Egyes emulátorokban előfordulhat, hogy a **bejelentkezési** gomb nem aktiválódik ebben a lépésben. Ha ez történik, állítsa le az alkalmazást, nyissa meg a *Source/VisualProvision/Pages/LoginPage. XAML* fájlt, keresse meg a `Button` login (bejelentkezés) **gombot**, majd távolítsa el a következő sort, majd futtassa újra az alkalmazást.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Az alkalmazás képernyője, amely az egyszerű szolgáltatásnév hitelesítő adatainak mezőit jeleníti meg](media/azure-logo-tutorial/app-credentials.png)

1. A következő képernyőn válassza ki az Azure-előfizetését a legördülő menüből. (Ez a menü tartalmazza az összes olyan előfizetést, amelyhez az egyszerű szolgáltatásnév hozzáfér.) Kattintson a **Folytatás** gombra. Ezen a ponton az alkalmazás kérheti, hogy hozzáférést biztosítson az eszköz kamerája és a fénykép tárterülete számára. Adja meg a hozzáférési engedélyeket.

    ![Az alkalmazás képernyője, amely a cél Azure-előfizetés legördülő mezőjét jeleníti meg](media/azure-logo-tutorial/app-az-subscription.png)


1. Az eszközön lévő kamera aktiválva lesz. Készítsen fotót a betanított Azure-szolgáltatás egyik emblémáján. A központi telepítési ablaknak meg kell kérnie, hogy válasszon ki egy régiót és egy erőforráscsoportot az új szolgáltatásokhoz (ahogy ezt tenné, ha a Azure Portalról telepítette őket). 

    ![Egy okostelefon-kamera képernyője, amely az Azure-emblémák két papír kivágására koncentrál](media/azure-logo-tutorial/app-camera-capture.png)

    ![Egy alkalmazás képernyője, amely a központi telepítési régió és az erőforráscsoport mezőit jeleníti meg](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha követte a forgatókönyv összes lépését, és az alkalmazást használta az Azure-szolgáltatások üzembe helyezéséhez a fiókjában, lépjen a [Azure Portal](https://ms.portal.azure.com/). Itt szakítsa meg a használni nem kívánt szolgáltatásokat.

Ha azt tervezi, hogy létrehoz egy saját objektum-észlelési projektet a Custom Vision, akkor érdemes törölni az oktatóanyagban létrehozott embléma-észlelési projektet. A Custom Vision ingyenes előfizetése csak két projektet tesz lehetővé. Az embléma-észlelési projekt törléséhez nyissa meg a [Custom Vision webhelyén](https://customvision.ai)a **projektek** elemet, majd válassza a Kuka ikont az **új projekt** területen.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy teljes funkcionalitású Xamarin. Forms-alkalmazást állít be és vizsgál meg, amely a Custom Vision szolgáltatást használja a mobil kamera-lemezképekben található emblémák észlelésére. Következő lépésként Ismerkedjen meg a Custom Vision-modell létrehozásával kapcsolatos ajánlott eljárásokkal, hogy a saját alkalmazásaihoz hozzon létre egyet, így hatékony és pontos lehet.

> [!div class="nextstepaction"]
> [Az osztályozó javítása](getting-started-improving-your-classifier.md)

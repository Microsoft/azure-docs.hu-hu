---
title: Az Video Indexer összekötők használata a Logic App és Power Automate oktatóanyag segítségével.
description: Ez az oktatóanyag bemutatja, hogyan oldható fel új élmények és bevételszerzési lehetőségek Video Indexer a Logic App és a Power Automate.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: caff6a2496e907da1bdc140860c47476d1842df4
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600675"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Oktatóanyag: az Video Indexer használata a Logic App és a Power Automate

Azure Media Services Video Indexer [v2 REST API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Video) a kiszolgálók közötti és az ügyfelek közötti kommunikációt is támogatja, és lehetővé teszi a Video Indexer-felhasználók számára, hogy könnyen integrálják a videó- és hangelemzéseket az alkalmazás logikába, új élményeket és bevételi lehetőségeket teremtve.

Az integráció még egyszerűbbé válik, ha Logic Apps [API Power Automate](https://azure.microsoft.com/services/logic-apps/)kompatibilis    [](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)   összekötőket. Az összekötők használatával egyéni munkafolyamatokat állíthat be, amelyek hatékonyan indexelnek és nyerhetnek ki elemzéseket nagy mennyiségű video- és hangfájlból anélkül, hogy egyetlen sor kódot írnál. Emellett az összekötők integrációhoz való használata jobb rálátást biztosít a munkafolyamat állapotára, és megkönnyíti a hibakeresést.  

A Video Indexer összekötők gyors Power Automate érdekében egy példa logikai alkalmazást és egy Power Automate bemutatót mutatunk be. Ez az oktatóanyag bemutatja, hogyan állíthat be folyamatokat a Logic Apps. A szerkesztők és a képességek azonban majdnem azonosak mindkét megoldásban, így a diagramok és a magyarázatok egyaránt Logic Apps és Power Automate.

Az oktatóanyagban szereplő "videó automatikus feltöltése és indexelése" forgatókönyv két különböző, egymással együtt használható folyamatból áll. 
* Az első folyamat akkor aktiválódik, ha blobot adnak hozzá vagy módosítnak egy Azure Storage-fiókban. Feltölti az új fájlt a Video Indexer egy visszahívási URL-címet, hogy értesítést küldjön az indexelési művelet befejezése után. 
* A második folyamat a visszahívási URL-cím alapján aktiválódik, és a kinyert elemzéseket visszamenti egy JSON-fájlba az Azure Storage-ban. Ez a két folyamat módszer a nagyobb fájlok aszinkron feltöltésének és indexelésének hatékony támogatására használható. 

Ez az oktatóanyag a Logic App használatával mutatja be a következőt:

> [!div class="checklist"]
> * A fájlfeltöltési folyamat beállítása
> * A JSON-kinyerési folyamat beállítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Először is szüksége lesz egy Video Indexer-fiókra, valamint az API-k API-kulcson [keresztüli elérésére.](video-indexer-use-apis.md) 
* Szüksége lesz egy Azure Storage-fiókra is. Jegyezze fel a Tárfiók hozzáférési kulcsát. Hozzon létre két tárolót – egyet a videók tárolására, egyet pedig a Video Indexer számára.  
* A következő lépésként két külön folyamat megnyitására lesz szükség a Logic Apps vagy Power Automate (attól függően, hogy melyiket használja). 

## <a name="set-up-the-first-flow---file-upload"></a>Az első folyamat beállítása – fájlfeltöltés   

Az első folyamat akkor aktiválódik, amikor blobot ad hozzá az Azure Storage-tárolóhoz. Az aktiválás után létrehoz egy SAS URI-t, amely segítségével feltöltheti és indexelheti a videót a Video Indexer. Ebben a szakaszban a következő folyamatot fogja létrehozni. 

![Fájlfeltöltési folyamat](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Az első folyamat beállításához meg kell adnia a Video Indexer API-kulcsát és az Azure Storage hitelesítő adatait. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Kapcsolat neve és API-kulcs](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Ha korábban azure storage-fiókot vagy Video Indexer egy logikai alkalmazáshoz, a rendszer tárolja a kapcsolati adatokat, és automatikusan csatlakozik. <br/>A kapcsolat szerkesztéséhez kattintson  az Azure Storage (a tárolási ablak) alján található Kapcsolat módosítása elemre, vagy Video Indexer (a lejátszóablak) műveletre.

Miután csatlakozott az Azure Storage-hoz és Video Indexer-fiókjaihoz, keresse meg és válassza a "Blob hozzáadásakor vagy módosításakor" eseményindítót a **Logic Apps Designerben.**

Válassza ki azt a tárolót, amelybe a videófájlokat helyezni fogja. 

![A Blob hozzáadásakor vagy módosításakor párbeszédpanel képernyőképe, ahol kiválaszthat egy tárolót.](./media/logic-apps-connector-tutorial/container.png)

Ezután keresse meg és válassza ki a "SAS URI létrehozása elérési út alapján" műveletet. A művelet párbeszédpanelén válassza a Fájl elérési útja listát a Dinamikus tartalom beállításai közül.  

Emellett adjon hozzá egy új "Shared Access Protocol" paramétert. A paraméter értékeként válassza a HttpsOnly lehetőséget.

![SAS URI elérési út szerint](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

A fiók [helyének és](regions.md) azonosítójának [kitöltésével](./video-indexer-use-apis.md#account-id)szerezze be   Video Indexer-jogkivonatot.

![Fiók-hozzáférési jogkivonat beszerzése](./media/logic-apps-connector-tutorial/account-access-token.png)

A "Videó és index feltöltése" mezőben adja meg a szükséges paramétereket és a Videó URL-címét. Válassza az "Új paraméter hozzáadása" lehetőséget, majd válassza a Visszahívási URL-cím lehetőséget. 

![Feltöltés és indexelés](./media/logic-apps-connector-tutorial/upload-and-index.png)

A visszahívási URL-címet most üresen hagyja. Csak a visszahívási URL-címet létrehozó második folyamat befejezése után fogja hozzáadni. 

Használhatja az alapértelmezett értéket a többi paraméterhez, vagy beállíthatja őket az igényeinek megfelelően. 

Kattintson **a Mentés** gombra, és most már konfigurálhatja a második folyamatot, amely a feltöltés és az indexelés befejezése után kinyeri az elemzéseket. 

## <a name="set-up-the-second-flow---json-extraction"></a>A második folyamat beállítása – JSON-kinyerés  

Az első folyamat feltöltésének és indexelésének befejezése egy HTTP-kérést küld a megfelelő visszahívási URL-címekkel a második folyamat aktiválására. Ezután lekéri a Video Indexer. Ebben a példában az indexelési feladat kimenetét az Azure Storage-ban fogja tárolni.  A kimenetet azonban Ön használhatja.  

Hozza létre a második folyamatot az elsőtől elkülönítve. 

![JSON-kinyerési folyamat](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

A folyamat beállításához újra meg kell adnia a Video Indexer API-kulcsot és az Azure Storage hitelesítő adatait. Frissítenie kell az első folyamat paramétereit. 

Az eseményindítóhoz egy HTTP POST URL-cím mező látható. Az URL-cím csak a folyamat mentése után jön létre; végül azonban szüksége lesz az URL-címre. Erre visszatérünk. 

A fiók [helyének és](regions.md) azonosítójának [kitöltésével](./video-indexer-use-apis.md#account-id)szerezze be   Video Indexer-fiók jogkivonatát.  

Ugrás a "Videóindex lehívása" műveletre, és töltse ki a szükséges paramétereket. Videóazonosítóként a következő kifejezést kell használni: triggerOutputs()['queries']['id'] 

![videoindexelő műveletinformációi](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Ez a kifejezés arra utasítja a kötőt, hogy az eseményindító kimenetében szerezze be a Videó-azonosítót. Ebben az esetben az eseményindító kimenete az első eseményindító "Videó és index feltöltése" kimenete lesz. 

Ugrás a "Blob létrehozása" műveletre, és válassza ki annak a mappának az elérési útját, amelybe az elemzéseket menteni fogja. Adja meg a létrehozott blob nevét. Blobtartalomhoz a következő kifejezést helyezze el: body('Get_Video_Index') 

![Blob létrehozása művelet](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Ez a kifejezés a folyamat "Videóindex lehívása" műveletének kimenetét veszi fel. 

Kattintson **a Folyamat mentése gombra.** 

A folyamat mentése után egy HTTP POST URL-cím jön létre az eseményindítóban. Másolja ki az URL-címet az eseményindítóból. 

![URL-eseményindító mentése](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Most tért vissza az első folyamathoz, és illessze be az URL-címet a Visszahívási URL-cím paraméter "Videó és index feltöltése" műveletbe. 

Győződjön meg arról, hogy mindkét folyamat mentve van, és készen is van! 

Próbálja ki az újonnan létrehozott logikai alkalmazást vagy az Power Automate-megoldást úgy, hogy hozzáad egy videót az Azure Blobs-tárolóhoz, majd néhány perccel később visszatérve láthatja, hogy az elemzések megjelennek a célmappában. 

## <a name="generate-captions"></a>Feliratok létrehozása

A következő blogban talál olyan lépéseket, amelyek azt mutatják be, hogyan hozhat létre feliratokat Video Indexer [és Logic Apps.](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198) 

A cikk azt is bemutatja, hogyan indexelhatja automatikusan a videót a OneDrive-ba való másolással, és hogyan tárolhatja a Video Indexer a OneDrive-ban.
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel az oktatóanyagtal, nyugodtan megtarthatja ezt a logikai alkalmazást vagy Power Automate megoldását, ha szüksége van rá. Ha azonban nem szeretné tovább futni, és nem szeretné, hogy ki legyen számlázva, kapcsolja ki mindkét folyamatát, ha a Power Automate. Tiltsa le mindkét folyamat, ha az Logic Apps. 

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag csak egy Video Indexer mutat be összekötőkre. Az összekötőket Video Indexer bármely API-híváshoz használhatja, amelyet a Video Indexer. Például: elemzések feltöltése és lekérése, az eredmények lefordítása, beágyazható widgetek lekérése és akár a modellek testreszabása. Ezeket a műveleteket emellett különböző források, például a fájltárak frissítései vagy az elküldött e-mailek alapján is aktiválhatja. Ezután dönthet úgy, hogy frissíti az eredményeket a megfelelő infrastruktúrára vagy alkalmazásra, vagy bármilyen számú műveletelemet generál.  

> [!div class="nextstepaction"]
> [A Video Indexer API használata](video-indexer-use-apis.md)

További forrásokért tekintse meg ezt a [videoindexelőről készült dokumentumot.](/connectors/videoindexer-v2/)

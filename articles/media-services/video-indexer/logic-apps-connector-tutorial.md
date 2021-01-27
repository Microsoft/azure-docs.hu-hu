---
title: A Video Indexer összekötők a Logic app és a Power automatizáló oktatóanyaggal.
description: Ez az oktatóanyag bemutatja, hogyan oldhatja fel az új tapasztalatokat és a bevételi lehetőségeket, Video Indexer összekötőket a Logic app és a Power automatizáló használatával.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f3504ca4a706e92081209f4eaaa86af9f71c52b3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880911"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Oktatóanyag: Video Indexer használata a Logic app és a Power automatizáló használatával

Azure Media Services [video Indexer v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) a kiszolgálók közötti és az ügyfél és a kiszolgáló közötti kommunikációt is támogatja, és lehetővé video Indexer teszi a felhasználók számára, hogy a videó-és hangelemzéseket egyszerűen integrálják az alkalmazási logikába, új tapasztalatokat és bevételi lehetőségeket lehessen kizárni.

Ahhoz, hogy az integráció még könnyebbé váljon, [](https://azure.microsoft.com/services/logic-apps/)támogatjuk az   API-val kompatibilis Logic apps és [automatizálási](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)   összekötőket. Az összekötők segítségével egyéni munkafolyamatokat állíthat be, amelyekkel hatékonyan indexelheti és kinyerheti a nagy mennyiségű videó-és hangfájlból származó elemzéseket anélkül, hogy egyetlen sor kódot kellene írnia. Emellett az integrációs összekötők segítségével jobb láthatóságot biztosít a munkafolyamat állapotával és a hibakereséshez.  

Ha segítségre van szüksége a Video Indexer-összekötők gyors megkezdéséhez, egy példát láthat a logikai alkalmazásra és a beállítható automatizálási megoldásra. Ez az oktatóanyag bemutatja, hogyan állíthatja be a folyamatokat a Logic Apps használatával. A szerkesztők és a képességek azonban szinte azonosak mindkét megoldásban, így a diagramok és a magyarázatok egyaránt alkalmazhatók a Logic Apps és a teljesítmény automatizálására is.

Az oktatóanyagban szereplő "a videó automatikus feltöltése és indexelése" forgatókönyv két különböző, együttesen működő folyamatból áll. 
* Az első folyamat akkor aktiválódik, ha egy blobot hozzáadnak vagy módosítanak egy Azure Storage-fiókban. Feltölti az új fájlt Video Indexer egy visszahívási URL-címmel, hogy értesítést küldjön az indexelési művelet befejeződése után. 
* A második folyamat a visszahívási URL-cím alapján aktiválódik, és elmenti a kinyert adatmennyiségeket egy JSON-fájlba az Azure Storage-ban. Ez a kétfolyamatos megközelítés a nagyobb fájlok aszinkron feltöltésének és indexelésének támogatására szolgál. 

Ez az oktatóanyag a Logic App használatával mutatja be a következőket:

> [!div class="checklist"]
> * A fájlfeltöltés folyamatának beállítása
> * A JSON-kibontási folyamat beállítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Először is szüksége lesz egy Video Indexer fiókra, valamint az API-k [API-kulcson keresztüli elérésére](video-indexer-use-apis.md). 
* Szüksége lesz egy Azure Storage-fiókra is. Tartsa szem előtt a Storage-fiókhoz tartozó hozzáférési kulcsot. Hozzon létre két tárolót – az egyiket, hogy a-ben, a-ben és a-ban a Video Indexer által generált bepillantást tárolja.  
* Ezután két különálló folyamatot kell megnyitnia Logic Apps vagy energiagazdálkodási automatizáláson (attól függően, hogy melyiket használja). 

## <a name="set-up-the-first-flow---file-upload"></a>Az első folyamat – fájl feltöltésének beállítása   

Az első folyamat akkor aktiválódik, amikor egy blob bekerül az Azure Storage-tárolóba. Az aktiválás után a rendszer létrehoz egy SAS URI-t, amelynek használatával feltöltheti és indexelheti a videót a Video Indexerban. Ebben a szakaszban a következő folyamatot fogja létrehozni. 

![Fájlfeltöltés folyamata](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Az első folyamat beállításához meg kell adnia a Video Indexer API-kulcsot és az Azure Storage-beli hitelesítő adatait. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Kapcsolat neve és API-kulcs](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Ha korábban csatlakoztatott egy Azure Storage-fiókot vagy Video Indexer fiókot egy logikai alkalmazáshoz, a kapcsolati adatok tárolódnak, és a rendszer automatikusan csatlakozik. <br/>A kapcsolat szerkesztéséhez kattintson a **kapcsolat módosítása** elemre az Azure Storage (a tárolási ablak) alján, vagy a video Indexer (a játékos ablak) művelettel.

Miután kapcsolódhat az Azure Storage-hoz, és Video Indexer fiókokhoz, megkeresheti és kiválaszthatja a "blob hozzáadása vagy módosítása" triggert **Logic apps Designerben**.

Válassza ki azt a tárolót, ahová a videofájlokat helyezni fogja. 

![Képernyőfelvétel: a blob hozzáadásakor vagy módosításakor megjelenő párbeszédpanel, ahol kijelölhet egy tárolót.](./media/logic-apps-connector-tutorial/container.png)

Ezután keresse meg és válassza ki az "SAS URI létrehozása elérési út alapján" műveletet. A művelethez tartozó párbeszédpanelen válassza ki a fájlok listájának elérési útját a dinamikus tartalom beállításainál.  

Emellett adjon hozzá egy új "Shared Access Protocol" paramétert. A paraméter értékeként válassza a HttpsOnly lehetőséget.

![SAS URI elérési út alapján](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Adja meg a [fiók helyét](regions.md) és a [fiók azonosítóját](./video-indexer-use-apis.md#account-id)   az video Indexer Account token beszerzéséhez.

![Fiók hozzáférési jogkivonatának beolvasása](./media/logic-apps-connector-tutorial/account-access-token.png)

A "videó és index feltöltése" lehetőségnél töltse ki a szükséges paramétereket és a videó URL-címét. Válassza az "új paraméter hozzáadása" lehetőséget, és válassza a visszahívási URL-címet. 

![Feltöltés és index](./media/logic-apps-connector-tutorial/upload-and-index.png)

Ekkor üresen hagyja a visszahívási URL-címet. A rendszer csak a visszahívási URL-cím létrehozása utáni második folyamat befejezése után adja hozzá. 

Használhatja az alapértelmezett értéket a többi paraméterhez, vagy beállíthatja az igényeinek megfelelően. 

Kattintson a **Save (Mentés**) gombra, és térjünk át a második folyamat konfigurálásához, hogy a feltöltés és az indexelés befejezése után kinyerje a bepillantást. 

## <a name="set-up-the-second-flow---json-extraction"></a>A második folyamat beállítása – JSON-Kibontás  

Az első folyamat feltöltésének és indexelésének befejezése HTTP-kérést küld a megfelelő visszahívási URL-címmel a második folyamat elindításához. Ezután lekéri a Video Indexer által generált megállapításokat. Ebben a példában az indexelési feladatoknak az Azure Storage-ban tárolt kimenetét fogja tárolni.  Azonban a kimenettel elvégezhető.  

Hozza létre a második folyamatot az elsőtől külön. 

![JSON-kinyerési folyamat](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

A folyamat beállításához újra meg kell adnia a Video Indexer API-kulcsot és az Azure Storage-beli hitelesítő adatait. Ugyanezeket a paramétereket kell frissítenie az első folyamat során. 

Az trigger esetében megjelenik egy HTTP POST URL-cím mező. Az URL-cím csak a folyamat mentése után hozható létre. Végül azonban szüksége lesz az URL-címre. Vissza fogunk térni erre. 

Adja meg a [fiók helyét](regions.md) és a [fiók azonosítóját](./video-indexer-use-apis.md#account-id)   az video Indexer Account token beszerzéséhez.  

Nyissa meg a "video index beolvasása" műveletet, és töltse ki a szükséges paramétereket. A videó azonosítója mezőben adja meg a következő kifejezést: triggerOutputs () [' kérdezgető '] [' id '] 

![videó-indexelő műveleti adatai](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Ez a kifejezés közli a összekötővel, hogy lekérje a videó AZONOSÍTÓját az trigger kimenetéről. Ebben az esetben az trigger kimenete a "videó feltöltése és index" kimenete lesz az első triggerben. 

Nyissa meg a "blob létrehozása" műveletet, és válassza ki annak a mappának az elérési útját, amelyben az elemzéseket menteni fogja. Állítsa be a létrehozandó blob nevét. A blob tartalmát a következő kifejezéssel kell elhelyezni: Body (' Get_Video_Index ') 

![BLOB létrehozása művelet](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Ez a kifejezés a "video index beolvasása" művelet kimenetét veszi át ebből a folyamatból. 

Kattintson a **folyamat mentése** gombra. 

A folyamat mentése után a rendszer egy HTTP POST URL-címet hoz létre az triggerben. Másolja az URL-címet az triggerből. 

![URL-cím mentése trigger](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Most térjen vissza az első folyamathoz, és illessze be az URL-címet a visszahívási URL-cím paraméter "videó feltöltése és index" műveletében. 

Győződjön meg arról, hogy mindkét folyamat el van mentve, és máris elvégezhető! 

Próbálja ki az újonnan létrehozott logikai alkalmazást vagy a Power automatizáló megoldást egy videó hozzáadásával az Azure Blobs-tárolóba, és térjen vissza néhány perccel később, hogy az információk megjelenjenek a célmappában. 

## <a name="generate-captions"></a>Feliratok készítése

Tekintse meg a következő blogot, amely bemutatja, [hogyan hozhatja ki a video Indexer és a Logic apps feliratokat](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

A cikk azt is bemutatja, hogyan indexelheti a videót automatikusan úgy, hogy átmásolja a OneDrive, és hogyan tárolja a Video Indexer által generált feliratokat a OneDrive-ben.
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült ezzel az Oktatóanyaggal, nyugodtan megtarthatja ezt a logikai alkalmazást, vagy automatizálhatja a megoldást, ha szükséges. Ha azonban nem szeretné megtartani ezt a futást, és nem szeretné számlázni, kapcsolja ki mindkét folyamatot, ha a Power automatizálás szolgáltatást használja. Ha Logic Apps használ, tiltsa le mindkét folyamatot. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag csak egy Video Indexer összekötőt mutat be. A Video Indexer által biztosított API-hívások Video Indexer összekötőket is használhat. Például: az adatok feltöltése és lekérése, az eredmények lefordítása, a beágyazható widgetek beolvasása és a modellek testreszabása is. Ezen kívül dönthet úgy is, hogy a műveleteket különböző forrásokon, például a fájlok adattárakon vagy az elküldött e-maileken alapuló módon indítja el. Ezután megadhatja, hogy az eredmények frissítése a megfelelő infrastruktúrára vagy alkalmazásra történjen, vagy tetszőleges számú műveleti elemet állítson elő.  

> [!div class="nextstepaction"]
> [A Video Indexer API használata](video-indexer-use-apis.md)

További forrásokért tekintse meg ezt a dokumentumot a [video Indexer](/connectors/videoindexer-v2/) -ben.
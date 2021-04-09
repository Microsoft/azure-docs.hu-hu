---
title: A portál használata tartalom feltöltéséhez, kódolásához és streameléséhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan tölthet fel, kódolhat és továbbíthat tartalmakat Azure Media Services használatával a portálon.
ms.topic: quickstart
ms.date: 08/31/2020
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.openlocfilehash: 9b66fa6cd688cf0680e742ffa9fbe4d61a444ab2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632996"
---
# <a name="quickstart-upload-encode-and-stream-content-with-portal"></a>Gyors útmutató: tartalom feltöltése, kódolása és továbbítása a portálon

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan töltheti fel, kódolhatja és továbbíthatja a tartalmat a Azure Media Services segítségével a Azure Portal használatával.
  
## <a name="overview"></a>Áttekintés

* A médiatartalmak Azure-ban való kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot, és fel kell töltenie a kiváló minőségű digitális médiafájlt egy **eszközbe**. 
    
    > [!NOTE]
    > Ha a videó korábban fel lett töltve a Media Services-fiókba Media Services V3 API-val, vagy a tartalom egy élő kimenet alapján lett létrehozva, akkor nem jelenik meg a **kódolás**, az **elemzés** vagy a **titkosítás** gomb a Azure Portal. Ezeket a feladatokat a Media Services V3 API-k használatával hajthatja végre.

    Tekintse át a következőket: 

  * [Felhőbe történő feltöltés és tárolás](storage-account-concept.md)
  * [Eszközök koncepciója](assets-concept.md)
* Miután feltölti a kiváló minőségű digitális médiafájlt egy adategységbe (egy bemeneti eszközre), feldolgozhatja (kódolja vagy elemezheti). A feldolgozott tartalom egy másik eszközre (kimeneti eszközre) mutat. 
    * [Kódolja](encoding-concept.md) a feltöltött fájlt olyan formátumokba, amelyek számos böngészőben és eszközön játszhatók le.
    * [Elemezze](analyzing-video-audio-files-concept.md) a feltöltött fájlt. 

        Jelenleg a Azure Portal használatakor a következőket teheti: létrehoz egy TTML és WebVTT zárt feliratú fájlokat. Az ezekben a formátumokban lévő fájlok használatával a fogyatékkal élők számára elérhetővé teheti a hang-és videofájlokat. A tartalomból kulcsszavakat is kinyerhet.

        A videó-és hangfájlokból származó elemzések kinyerését lehetővé tevő széles körű élmény érdekében használja a Media Services v3-es előállítók használatát (lásd a következő [oktatóanyagot: videók elemzése az Media Services v3](analyze-videos-tutorial-with-api.md)használatával). <br/>Ha részletesebb információkra van szüksége, használja a [video Indexer](../video-indexer/index.yml) közvetlenül.    
* A tartalom feldolgozása után médiatartalmakat is továbbíthat az ügyfelek számára. Ahhoz, hogy a kimeneti eszközön a videók elérhetők legyenek az ügyfelek számára a lejátszáshoz, létre kell hoznia egy **folyamatos átviteli lokátort**. A **folyamatos átviteli lokátor** létrehozásakor meg kell adnia egy **folyamatos átviteli házirendet**. A **folyamatos átviteli szabályzatok** lehetővé teszik a folyamatos átviteli protokollok és a titkosítási beállítások (ha vannak) definiálását a **streaming-lokátorok** számára.
    
    Tekintse át

    * [Streameléskeresők](streaming-locators-concept.md)
    * [Streamelési szabályzatok](streaming-policy-concept.md)
    * [Csomagolás és kézbesítés](dynamic-packaging-overview.md)
    * [Szűrők](filters-concept.md)
* A tartalom védelme érdekében Titkosítsa a Advanced Encryption Standard (AES-128) vagy a három fő DRM-rendszer valamelyikét: Microsoft PlayReady, Google Widevine és Apple FairPlay. A [tartalom titkosítása a Azure Portal](encrypt-content-quickstart.md) rövid útmutatóval azt mutatja be, hogyan konfigurálható a tartalom védelme.
        
## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[Media Services-fiók létrehozása](create-account-howto.md)

## <a name="upload"></a>Feltöltés

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg a Media Services-fiókját, és kattintson rá.
1. Válassza az **eszközök (új)** lehetőséget.
1. Kattintson a **feltöltés** gombra az ablak tetején. 
1. Húzással vagy tallózással keresse meg a feltölteni kívánt fájlt.

Ha az eszközök ablakra navigál, látni fogja, hogy a listához új eszköz lett hozzáadva:

![Képernyőkép a Azure Portal az eszközök ablak megnyitásával, amely az eszközök (új) elem kiválasztásával, valamint a feltöltés gomb kiválasztásával hozzáadott új eszközzel bővült.](./media/manage-assets-quickstart/upload.png)

## <a name="encode"></a>Kódolás

1. Válassza az **eszközök (új)** lehetőséget.
1. Válassza ki az új eszközt (az utolsó lépésben hozzáadva).
1. Kattintson a **kódolás** elemre az ablak tetején.

    A gomb megnyomásával elindítja a kódolási feladatot. Ha a művelet sikeresen befejeződik, a rendszer létrehoz egy kimeneti eszközt, amely tartalmazza a kódolt tartalmat.

Ha az eszközök ablakra navigál, látni fogja, hogy a kimeneti eszköz hozzá lett adva a listához:

![A Azure Portal eszközök ablakának képernyőképe, amely az adategység ignite.mp4 a média által kódolt standard kódolású adatforrást mutatja.](./media/manage-assets-quickstart/encode.png)

## <a name="monitor-the-job-progress"></a>A feladatok előrehaladásának figyelése

A feladat állapotának megtekintéséhez navigáljon a **feladatok** elemhez. A feladatok általában a következő állapotokon haladnak át: ütemezett, várólistán lévő, feldolgozás, befejezett (végső állapot). Ha a feladat hibát észlelt, a Hiba állapot jelenik meg.

![Állapot](./media/manage-assets-quickstart/job-status.png)

## <a name="publish-and-stream"></a>Közzététel és stream

Egy eszköz közzétételéhez most hozzá kell adnia egy folyamatos átviteli lokátort az objektumhoz.

### <a name="streaming-locator"></a>Folyamatos átviteli lokátor 

1. A **folyamatos átviteli lokátor** szakaszban nyomja meg **a + streaming-lokátor hozzáadása** lehetőséget.
    Ez közzéteszi az eszközt, és létrehozza a streaming URL-címeket.

    > [!NOTE]
    > Ha azt szeretné, hogy a stream titkosítva legyen, létre kell hoznia egy tartalmi kulcsra vonatkozó házirendet, és be kell állítania azt a folyamatos átviteli lokátoron. Részletekért lásd: [tartalom titkosítása a Azure Portal](encrypt-content-quickstart.md).
1. A **folyamatos átviteli lokátor hozzáadása** ablakban kiválaszthatja az előre definiált folyamatos átviteli szabályzatok egyikét. Részletes információk: [folyamatos átviteli házirendek](streaming-policy-concept.md)

    ![Folyamatos átviteli lokátor](./media/manage-assets-quickstart/streaming-locator.png)

Az eszköz közzétételét követően közvetlenül a portálon keresztül továbbíthatja. 

![Play](./media/manage-assets-quickstart/publish.png)

Vagy másolja a streaming URL-címet, és használja azt az ügyfél-lejátszóban.

> [!NOTE]
> Ellenőrizze, hogy fut-e a [folyamatos átviteli végpont](streaming-endpoint-concept.md) . Amikor először hoz létre egy Media Service-fiókot, a rendszer létrehozza az alapértelmezett folyamatos átviteli végpontot, és leállított állapotban van, ezért el kell indítania a tartalom továbbítása előtt.<br/>Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont futó állapotban van.

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi rövid útmutatót, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portalra, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben futtatta ezt a rövid útmutatót, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

[A portál használata tartalom titkosításához](encrypt-content-quickstart.md)

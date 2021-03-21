---
title: Videofájlok továbbítása Azure Media Services-.NET-tel
description: Kövesse ennek az oktatóanyagnak a lépéseit, hogy a .NET használatával új Azure Media Services fiókot hozzon létre, kódoljon egy fájlt, és továbbítsa a Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: dc6b240a2d97e0b4aa313f858b3965f241dd0b08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898189"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a video-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ebből az oktatóanyagból megtudhatja, hogy milyen egyszerűen kódolhatja és indíthatja el a streaming-videókat számos különböző böngészőn és eszközön a Azure Media Services használatával. A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg.
A témakörben szereplő minta olyan tartalmakat kódol, amelyeket HTTPS URL-cím segítségével tehet elérhetővé. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

Az oktatóanyag végére egy videót is továbbíthat.  

![Videó lejátszása](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nincs telepítve a Visual Studio, szerezze be a [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)-et.
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).<br/>Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.
- Kövesse a [Azure Media Services API-nak az Azure CLI-vel való elérésének](./access-api-howto.md) lépéseit, és mentse a hitelesítő adatokat. Ezeket az API-k eléréséhez kell használnia.

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

A minta az [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) mappában található.

Nyissa meg [appsettings.jsa](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) letöltött projektben. Cserélje le az értékeket az API-k [eléréséhez](./access-api-howto.md)kapott hitelesítő adatokkal.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítót** (először ellenőrzi, hogy létezik-e a megadott átalakító). 
2. Létrehozza a kódolási **feladatok** **kimenetéhez használt kimeneti objektumot** .
3. Egy HTTPS URL-cím alapján hozza létre a **feladathoz** tartozó bemenetet.
4. Elküldi a kódolási **feladatot** a korábban létrehozott bemenet és kimenet használatával.
5. Ellenőrzi a feladat állapotát.
6. Adatfolyam- **keresőt** hoz létre.
7. Streamelési URL-címeket épít fel.

A minta egyes funkcióinak leírásáért vizsgálja meg a kódot, és tekintse meg az [ebben a forrásfájlban](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) található megjegyzéseket.

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

Az alkalmazás futtatásakor megjelennek a videó különböző protokollokkal való lejátszására szolgáló URL-ek. 

1. Az *EncodeAndStreamFiles* alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.
2. Válassza az Apple **HLS** protokollját (a vége *manifest(format=m3u8-aapl)*), és másolja a streamelési URL-címet a konzolról.

![Képernyőkép a Visual Studióban található EncodeAndStreamFiles alkalmazás kimenetéről, amely a Azure Media Playerben használható három streaming URL-címet jeleníti meg.](./media/stream-files-tutorial-with-api/output.png)

A minta [forráskódjában](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) láthatja az URL felépítését. A létrehozásához össze kell fűznie a streamelési végpont gazdanevét és a streamelési lokátor elérési útját.  

## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, és navigáljon a következőhöz: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Az **URL** mezőbe illessze be az alkalmazás futtatásakor kapott streamelési URL-értékek egyikét. 
 
     Az URL-címet HLS, Dash vagy Smooth formátumban is beillesztheti, és a Azure Media Player a megfelelő folyamatos átviteli protokollra vált az eszközön való automatikus lejátszás érdekében.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyik erőforrására sem, beleértve az oktatóanyaghoz létrehozott Media Services-és Storage-fiókokat, törölje az erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>A kód vizsgálata

A minta egyes funkcióinak leírásáért vizsgálja meg a kódot, és tekintse meg az [ebben a forrásfájlban](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) található megjegyzéseket.

A [fájlok feltöltésével, kódolásával és streamelésével](stream-files-tutorial-with-api.md) foglalkozó oktatóanyag egy összetettebb streamelési példát biztosít részletesebb magyarázatokkal. 

### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Több szál használata

Az Azure Media Services v3 SDK-k nem szálbiztosak. Többszálas alkalmazások használatakor minden szálhoz ajánlott létrehozni egy új AzureMediaServicesClient objektumot.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: fájlok feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)

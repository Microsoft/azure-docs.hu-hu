---
title: Igény szerinti videotartalom-továbbítás az Azure Portal használatával | Microsoft Docs
description: Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási szolgáltatást hozhat létre az Azure Portalon egy Azure Media Services alkalmazással.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: fc908f909cdb64e968db78b2c98b8f0edf1c61dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009691"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Igény szerinti tartalomtovábbítás az Azure Portal használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez az oktatóanyag végigvezeti a lépéseken, amelyek segítségével alapszintű igény szerinti videotartalom-továbbítási szolgáltatást hozhat létre az Azure Portalon egy Azure Media Services alkalmazással.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzésének feltételei a következők:

* Egy Azure-fiók. Részletekért lásd: az [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/). 
* Egy Media Services-fiók. Media Services fiók létrehozásával kapcsolatban lásd: [Media Services-fiók létrehozása](media-services-portal-create-account.md).

Az oktatóanyag a következő feladatokat tartalmazza:

1. Indítsa el a streamvégpontot.
2. Videofájl feltöltése
3. A forrásfájl kódolása egy adaptív sávszélességű MP4-fájlsorozattá.
4. Végezze el az objektum közzétételét, majd a streamelési és a progresszív letöltési URL-cím lekérését.  
5. Tartalom lejátszása

## <a name="start-the-streaming-endpoint"></a>A streamvégpont elindítása

Az Azure Media Services egyik leggyakrabban használt funkciója a videók továbbítása az adaptív sávszélességű streamelés használatával. A Media Services biztosítja a dinamikus csomagolás lehetőségét. A dinamikus csomagolás használatával adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében továbbíthatja igény szerint. Példák a támogatott formátumokra: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming, HTTP protokollon keresztüli dinamikus adaptív streamelés (DASH, más néven MPEG-DASH). A Media Services által biztosított adaptív sávszélességű streamelés segítségével videóit anélkül továbbíthatja, hogy az adott streamformátumban előcsomagolt verziót tárolna.

> [!NOTE]
> A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

A streamvégpont elindítása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Beállítások**  >  **streaming végpontok** lehetőséget. 
3. Válassza ki az alapértelmezett streamvégpontot. Megjelenik a **DEFAULT STREAMING ENDPOINT DETAILS** (Alapértelmezett streamvégpont adatai) ablak.
4. Válassza ki a **Start** ikont.
5. Válassza ki a **Mentés** gombot.

## <a name="upload-files"></a>Fájlok feltöltése
Ha a Media Services használatával kíván videókat streamelni, fel kell töltenie a forrásvideókat, különböző bitsebességekre kell kódolnia azokat, majd közzé kell tennie az eredményt. Ez a rész a folyamat első lépését írja le. 

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Válassza a **Beállítások**  >  **eszközök** elemet. Ezután válassza ki a **Feltöltés** gombot.
   
    ![Fájlok feltöltése](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Megjelenik az **Upload a video asset** (Videóobjektum feltöltése) ablak.
   
   > [!NOTE]
   > A Media Services nem korlátozza a feltölteni kívánt videók méretét.
   > 
   > 
3. Keresse meg a számítógépén a feltölteni kívánt videót. Jelölje ki a videót, majd válassza az **OK** lehetőséget.  
   
    Megkezdődik a feltöltés. A folyamat előrehaladását a fájlnév alatt követheti nyomon.  

A feltöltést követően az új objektum megjelenik az **Objektumok** panelen. 

## <a name="encode-assets"></a>Objektumok kódolása
Annak érdekében, hogy kihasználhassa a dinamikus csomagolást, kódolja többszörös sávszélességű MP4-fájlokká a forrásfájlt. A kódolás lépéseit ebben a részben találja meg.

### <a name="encode-assets-in-the-portal"></a>Objektumok kódolása a portálon
Kódolás a Media Encoder Standard használatával az Azure Portalon:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Válassza a **Beállítások**  >  **eszközök** elemet. Válassza ki a kódolni kívánt objektumot.
3. Válassza ki a **Kódolás** gombot.
4. Az **Objektum kódolása** ablakban válassza a **Media Encoder Standard** feldolgozóeszközt, és egy beállításkészletet. A beállításkészletekkel kapcsolatos információkért lásd a [sávszélességi skála automatikus létrehozását](media-services-autogen-bitrate-ladder-with-mes.md) és a [Media Encoder Standard előre beállított feladatait](media-services-mes-presets-overview.md) ismertető részeket. Fontos, hogy a bemeneti videóhoz legmegfelelőbb beállításkészlet legyen kiválasztva. Ha például tudja, hogy a bemeneti videó felbontása 1920 &#215; 1080 képpont, akkor választhatja a **H264 Multiple Bitrate 1080p** beállításkészletet. Ha a videó alacsony (640 &#215; 360) felbontású, ne használja a **H264 Multiple Bitrate 1080p** beállításkészletet.
   
   Az erőforrások egyszerűbb kezelése érdekében lehetősége van módosítani a kimeneti objektum nevét, illetve a feladat nevét.
   
   ![Objektumok kódolása](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Válassza a **Létrehozás** lehetőséget.

### <a name="monitor-encoding-job-progress"></a>Kódolási feladatok előrehaladásának figyelése
A kódolási feladat előrehaladásának nyomon követéséhez válassza az oldal tetején található **Beállítások**, majd pedig a **Feladatok** elemet.

![Feladatok](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Tartalom közzététele
Ahhoz, hogy átadhassa a tartalmak streamelésére vagy letöltésére használható URL-címet a felhasználónak, először közzé kell tennie az objektumot. Ehhez létre kell hoznia egy lokátort. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. Az Azure Media Services két lokátortípust támogat: 

* **Streamelési (OnDemandOrigin) lokátorok**. A streamelési lokátorok adaptív streameléshez használhatók. Példák adaptív streamelésre: HLS, Smooth Streaming és MPEG DASH. A streamelési lokátorok létrehozásához az objektumnak tartalmaznia kell egy .ism fájlt. 
* **Progresszív (SAS-) lokátorok**. A progresszív lokátorok a videók progresszív letöltésen keresztül történő továbbítására használatosak.

HLS streaming URL-cím létrehozásához fűzze hozzá a *(Format = m3u8-AAPL)* címet az URL-címhez:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

A Smooth Streaming-objektumok lejátszására használható streamelési URL-cím formátuma a következő:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest`

MPEG-DASH-streamelési URL-cím létrehozásához fűzze hozzá a *(format=mpd-time-csf)* karakterláncot az URL-címhez:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

A közös hozzáférésű jogosultságkód URL-címének formátuma a következő:

`{blob container name}/{asset name}/{file name}/{shared access signature}`

> [!NOTE]
> Az Azure Portalon 2015 márciusa előtt létrehozott lokátorok lejárati ideje két év.  
> 
> 

A lokátor lejárati idejének módosításához használjon [REST API](/rest/api/media/operations/locator#update_a_locator)-t vagy [.NET API](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator)-t. 

> [!NOTE]
> Az SAS-lokátorok lejárati dátumának frissítésekor az URL-cím is módosul.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Az objektum portál segítségével történő közzététele
1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Válassza a **Beállítások**  >  **eszközök** elemet. Válassza ki a közzétenni kívánt objektumot.
3. Válassza ki a **Közzététel** gombot.
4. Válassza ki a lokátor típusát.
5. Válassza a **Hozzáadás** lehetőséget.
   
    ![A videó közzététele](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Az URL-cím bekerül a **Közzétett URL-címek** listájába.

## <a name="play-content-from-the-portal"></a>Tartalom lejátszása a portálról
Az Azure Portalon talál egy tartalomlejátszót, amellyel tesztelheti a videót.

Jelölje ki a videót, majd válassza ki a **Lejátszás** gombot.

![Videó lejátszása az Azure Portalon](./media/media-services-portal-vod-get-started/media-services-play.png)

Vegye figyelembe a következőket:

* A streamelés megkezdéséhez futtassa az alapértelmezett streamvégpontot.
* Ellenőrizze, hogy közzétette-e a videót.
* Az Azure Portal médialejátszója az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, jelölje ki és másolja ki az URL-címet, és illessze be azt egy másik lejátszóba. Az [Azure Media Player](https://aka.ms/azuremediaplayer) lejátszón is tesztelheti például a videót.

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
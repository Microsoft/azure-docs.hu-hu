---
title: Tartalom közzététele a Azure Portalban | Microsoft Docs
description: Ez az oktatóanyag végigvezeti a tartalomnak a Azure Portalban való közzétételének lépésein.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c22570153200b9daeae44701c814faa1a28916c8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009810"
---
# <a name="publish-content-in-the-azure-portal"></a>Tartalom közzététele a Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portál](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Áttekintés
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Részletekért lásd: az [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Ahhoz, hogy átadhassa a tartalmak streamelésére vagy letöltésére használható URL-címet a felhasználónak, először közzé kell tennie az objektumot. Ehhez létre kell hoznia egy lokátort. A lokátorok hozzáférést biztosítanak az adategység fájljaihoz. Az Azure Media Services két lokátortípust támogat: 

* **Streamelési (OnDemandOrigin) lokátorok**. A streamelési lokátorok adaptív streameléshez használhatók. Az adaptív adatfolyam például az Apple HTTP Live Streaming (HLS), a Microsoft Smooth Streaming és a dinamikus adaptív streaming HTTP-n keresztül (kötőjel, más néven MPEG-DASH). A streamelési lokátorok létrehozásához az objektumnak tartalmaznia kell egy .ism fájlt. Például: `http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest`.
* **Progresszív (SAS-) lokátorok**. A progresszív lokátorok a videók progresszív letöltésen keresztül történő továbbítására használatosak.

HLS streaming URL-cím létrehozásához fűzze hozzá a *(Format = m3u8-AAPL)* címet az URL-címhez:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

A Smooth Streaming-objektumok lejátszására használható streamelési URL-cím formátuma a következő:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest`

MPEG-DASH-streamelési URL-cím létrehozásához fűzze hozzá a *(format=mpd-time-csf)* karakterláncot az URL-címhez:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

A közös hozzáférésű jogosultságkód URL-címének formátuma a következő:

`{blob container name}/{asset name}/{file name}/{shared access signature}`

További információ: a [tartalom továbbításának áttekintése](media-services-deliver-content-overview.md).

> [!NOTE]
> Az Azure Portalon 2015 márciusa előtt létrehozott lokátorok lejárati ideje két év.  
> 
> 

Egy lokátor lejárati dátumának frissítéséhez használja a [REST API](/rest/api/media/operations/locator#update_a_locator) vagy a [.NET API](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator)-t. 

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

## <a name="play-content-in-the-portal"></a>Tartalom lejátszása a portálon
Az Azure Portalon talál egy tartalomlejátszót, amellyel tesztelheti a videót.

Jelölje ki a videót, majd válassza ki a **Lejátszás** gombot.

![Videó lejátszása az Azure Portalon](./media/media-services-portal-vod-get-started/media-services-play.png)

Vegye figyelembe a következőket:

* Ellenőrizze, hogy közzétette-e a videót.
* Az Azure Portal médialejátszója az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, jelölje ki és másolja ki az URL-címet, és illessze be azt egy másik lejátszóba. Az [Azure Media Player](https://aka.ms/azuremediaplayer) lejátszón is tesztelheti például a videót.
* A folyamatos átviteli végpontnak futnia kell.  

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
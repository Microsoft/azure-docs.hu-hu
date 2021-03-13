---
title: A Wirecast-kódoló beállítása egyetlen bitráta élő stream küldésére | Microsoft Docs
description: 'Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Wirecast élő kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra. '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016831"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Egyetlen bitráta élő stream küldése a Wirecast Encoder használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [Wirecast](https://www.telestream.net/wirecast/overview.htm) élő kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti Azure Media Services (AMS) eszközt a Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak Windows rendszerű SZÁMÍTÓGÉPeken fut. Ha Mac vagy Linux operációs rendszert használ, [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md)létrehozásához használja a Azure Portal.

> [!NOTE]
> A kódolók számára a TLS 1,2-et a RTMP protokoll használatakor kell támogatni. A TLS 1,2-követelmény miatt használja a Wirecast 13.0.2 vagy újabb verzióját.

## <a name="prerequisites"></a>Előfeltételek
* [Azure Media Services-fiók létrehozása](media-services-portal-create-account.md)
* Ellenőrizze, hogy fut-e a folyamatos átviteli végpont. További információ: [streaming-végpontok kezelése Media Services-fiókban](media-services-portal-manage-streaming-endpoints.md)
* Telepítse a [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) eszköz legújabb verzióját.
* Indítsa el az eszközt, és kapcsolódjon az AMS-fiókhoz.

## <a name="tips"></a>Tippek
* Lehetőség szerint használjon vezetékes internetkapcsolatot.
* A sávszélesség-követelmények meghatározásának jól használható módszere a streamelés átviteli sebességének megduplázása. Habár ez nem kötelező követelmény, segít csökkenteni a hálózati torlódás hatását.
* A szoftveres alapú kódolók használatakor zárjunk be minden szükségtelen programot.

## <a name="create-a-channel"></a>Csatorna létrehozása
1. A AMSE eszközben navigáljon az **élő** lapra, és kattintson a jobb gombbal a csatorna területére. Válassza a **csatorna létrehozása... lehetőséget.** a menüből.

    ![Képernyőfelvétel: a menüből kiválasztott csatorna létrehozása.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Adja meg a csatorna nevét, a Description (Leírás) mező megadása nem kötelező. A Channel Settings (csatorna beállításai) területen válassza a **standard** lehetőséget a Live Encoding beállításnál, a bemeneti protokollt pedig az **RTMP** értékre állítva. Az összes többi beállítást is meghagyhatja.

    Győződjön meg arról, hogy az **új csatorna elindítása most** lehetőség van kiválasztva.

3. Kattintson a **csatorna létrehozása** gombra.

   ![A képernyőképen megjelenik az élő csatorna létrehozása párbeszédpanel.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> A csatorna elindításának ideje akár 20 percet is igénybe vehet.
>
>

A csatorna elindítása után beállíthatja [a kódolót](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A számlázás azonnal elindul, amint a csatorna üzemkész állapotba kerül. További információ: [csatorna állapotai](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />A Wirecast-kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállítások használatosak. A szakasz további részében részletesen ismertetjük a konfigurációs lépéseket.

**Videó**:

* Kodek: H.264
* Profil: Jó minőség (4.0-ás szint)
* Bitráta: 5000 kbps
* Kulcsképek: 2 másodperc (60 másodperc)
* Képkocka sebessége: 30

**Hang**:

* Kodek: AAC (LC)
* Átviteli sebesség: 192 kbps
* Mintavételi sebesség: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurációs lépések
1. Nyissa meg a Wirecast-alkalmazást a használt gépen, és állítsa be az RTMP streaming szolgáltatást.
2. Konfigurálja a kimenetet a **kimenet** lapra való navigálással, és válassza a **kimeneti beállítások...** lehetőséget.

    Győződjön meg arról, hogy a **kimeneti célhely** az **RTMP-kiszolgáló** értékre van beállítva.
3. Kattintson az **OK** gombra.
4. A beállítások lapon adja meg, hogy a **cél** mező **Azure Media Services** legyen.

    A kódolási profil előre ki van választva az **Azure H. 264 720p 16:9 (1280x720)** szolgáltatásban. A beállítások testreszabásához válassza a legördülő menü jobb oldalán lévő fogaskerék ikont, majd válassza az **új beállításkészlet** lehetőséget.

    ![A képernyőképen a sablon választása párbeszédpanel jelenik meg, amelyen a BlobTrigger be van jelölve.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Kódoló-beállításkészletek konfigurálása.

    Nevezze el a beállításkészletet, és keresse meg a következő ajánlott beállításokat:

    **Videó**

   * Kódoló: MainConcept H. 264
   * Képkockák másodpercenként: 30
   * Átlagos átviteli sebesség: 5000 kbit/s (a hálózati korlátozások alapján módosítható)
   * Profil: Main
   * Kulcstartó minden: 60 keret

     **Audió**

   * Cél átviteli sebessége: 192 kbit/s
   * Mintavételi sebesség: 44,100 kHz

     ![A képernyőképen a AzureTest1 tartozó kódoló készlete látható.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Kattintson a **Mentés** gombra.

    A kódolás mezőben most már elérhető az újonnan létrehozott profil a kiválasztáshoz.

    Győződjön meg arról, hogy az új profil van kiválasztva.
7. Szerezze be a csatorna bemeneti URL-címét a Wirecast **RTMP-végponthoz** való hozzárendeléshez.

    Váltson vissza a AMSE eszközre, és jelölje be a csatorna befejezésének állapota lehetőséget. Ha az állapot már **megkezdődött** a **futtatástól** kezdve, beolvashatja a bemeneti URL-címet.

    Amikor a csatorna fut, kattintson a jobb gombbal a csatorna nevére, navigáljon lefelé a **beviteli URL másolása a vágólapra** , majd válassza az **elsődleges bemeneti URL-cím** elemet.  

    ![A képernyőképen a bemeneti U R L másolása a vágólapra lehetőség jelenik meg az elsődleges bemenet U R L esetében.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. A Wirecast **kimeneti beállítások** ablakban illessze be ezeket az információkat a kimenet szakasz **címe** mezőjébe, és rendeljen hozzá egy stream-nevet.

    ![A képernyőképen a kimeneti beállítások láthatók.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Válassza az **OK** lehetőséget.
2. A fő **Wirecast** képernyőn ellenőrizze, hogy a videó és hang bemeneti forrásai készen állnak-e, majd kattintson a bal felső sarokban található **stream** elemre.

    ![Képernyőfelvétel: a Wirecast stream gomb.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Mielőtt a **stream** elemre kattint, meg **kell** győződnie arról, hogy a csatorna készen áll.
> Győződjön meg arról is, hogy a csatornát nem hagyja készenléti állapotba, és nem > 15 percnél hosszabb ideig.
>
>

## <a name="test-playback"></a>Lejátszás tesztelése

Navigáljon a AMSE eszközhöz, és kattintson a jobb gombbal a tesztelni kívánt csatornára. A menüben vigye az egérmutatót **az előnézet lejátszásához** , és válassza a **Azure Media Player** lehetőséget.  

![A képernyőképen az előnézet megjelenítése Azure Media Player lehetőség van kiválasztva.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Ha az adatfolyam megjelenik a lejátszóban, a kódoló megfelelően konfigurálva van az AMS-hez való kapcsolódásra.

Ha hiba érkezik, a csatornát alaphelyzetbe kell állítani, és a kódoló beállításait módosítani kell. Útmutatásért tekintse meg a [hibaelhárítással](media-services-troubleshooting-live-streaming.md) foglalkozó cikket.  

## <a name="create-a-program"></a>Program létrehozása
1. A csatorna lejátszásának megerősítése után hozzon létre egy programot. A AMSE eszköz **élő** lapján kattintson a jobb gombbal a program területére, és válassza az **új program létrehozása** lehetőséget.  

    ![Képernyőfelvétel: a kiválasztott program létrehozása lehetőség.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Adja meg a program nevét, és ha szükséges, állítsa be az **archiválási időszak hosszát** (amely alapértelmezés szerint négy óra). Megadhat egy tárolási helyet is, vagy meghagyhatja az alapértelmezett értéket.  
3. Jelölje be a **program indítása** jelölőnégyzetet.
4. Kattintson a **program létrehozása** lehetőségre.  

   >[!NOTE]
   >A program létrehozása kevesebb időt vesz igénybe, mint a csatornák létrehozása.
       
5. A program futása után erősítse meg a lejátszást. ehhez kattintson a jobb gombbal a programra, és navigáljon **a program (ok) lejátszásához** , majd válassza **a Azure Media Player** lehetőséget.  
6. A megerősítést követően kattintson a jobb gombbal a programra, majd válassza a **kimeneti URL-cím másolása a vágólapra** lehetőséget (vagy olvassa be ezt az információt a **program információi és beállítások** menüpontból a menüből).

Az adatfolyam most már készen áll a lejátszóba való beágyazásra, vagy az élő megtekintésre a közönség számára.  

## <a name="troubleshooting"></a>Hibaelhárítás
Útmutatásért tekintse meg a [hibaelhárítással](media-services-troubleshooting-live-streaming.md) foglalkozó cikket.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

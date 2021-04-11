---
title: Konfigurálja a Haivision KB-kódolót úgy, hogy egyetlen sávszélességű élő streamet küldjön az Azure-nak | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan konfigurálhatja az Haivision KB élő kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 20c75672bc7198fb028278dc5432cc6a77e3766e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016865"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Egyetlen bitráta élő stream küldése a Haivision KB Live Encoder használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a [HAVISION kb élő kódoló](https://www.haivision.com/products/kb-series/) kódolóját úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti Azure Media Services (AMS) eszközt a Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak Windows rendszerű SZÁMÍTÓGÉPeken fut. Ha Mac vagy Linux operációs rendszert használ, [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md)létrehozásához használja a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek
*   Hozzáférés egy Haivision KB-kódolóhoz, amely az SW v 5.01-es vagy újabb verzióját futtatja.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Adja meg a csatorna nevét, a Description (Leírás) mező megadása nem kötelező. A Channel Settings (csatorna beállításai) területen válassza a **standard** lehetőséget a Live Encoding beállításnál, a bemeneti protokollt pedig az **RTMP** értékre állítva. Az összes többi beállítást is meghagyhatja. Győződjön meg arról, hogy az **új csatorna elindítása most** lehetőség van kiválasztva.
3. Kattintson a **csatorna létrehozása** gombra.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> A csatorna elindításának ideje akár 20 percet is igénybe vehet.

## <a name="configure-the-haivision-kb-encoder"></a>A Haivision KB-kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállítások használatosak. A szakasz további részében részletesen ismertetjük a konfigurációs lépéseket.

Videó:
-   Kodek: H.264
-   Profil: Jó minőség (4.0-ás szint)
-   Bitráta: 5000 kbps
-   Kulcsképek: 2 másodperc (60-es keret)
-   Képkocka sebessége: 30

Hang
-   Kodek: AAC (LC)
-   Átviteli sebesség: 192 kbps
-   Mintavételi sebesség: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurációs lépések
1.  Jelentkezzen be a Haivision KB felhasználói felületére.
2.  Kattintson a Channel Control Center **menü gombjára** , majd válassza a **csatorna hozzáadása** elemet.  
    ![Képernyőkép a 2017-08-14-es 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Írja be a **csatorna nevét** a név mezőbe, és kattintson a Tovább gombra.  
    ![Képernyőkép a 2017-08-14-es 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Válassza ki a **csatorna bemeneti forrását** a **bemeneti forrás** legördülő menüből, és kattintson a Tovább gombra.
    ![Képernyőkép a 2017-08-14-es 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  A **kódoló sablon** legördülő menüből válassza a **H264-720-AAC-192** elemet, majd kattintson a Tovább gombra.
    ![Képernyőkép a 2017-08-14-es 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Az **új kimenet kiválasztása** legördülő menüből válassza az **RTMP** elemet, majd kattintson a Tovább gombra.  
    ![Képernyőkép a 2017-08-14-es 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  A **csatorna kimenete** ablakban töltse ki az Azure stream-információkat. Illessze be a **RTMP** hivatkozást a **kiszolgáló** területének kezdeti csatornájának beállításához. A **kimeneti név** területen adja meg a csatorna nevét. Az adatfolyam neve sablon területen használja a RTMPStreamName_% video_bitrate% sablont a stream elnevezéséhez.
    ![Képernyőkép a 2017-08-14-es 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kattintson a Tovább gombra, majd kattintson a kész gombra.
9.  A kódoló csatorna elindításához kattintson a **Lejátszás gombra** .  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Lejátszás tesztelése
Navigáljon a AMSE eszközhöz, és kattintson a jobb gombbal a tesztelni kívánt csatornára. A menüben vigye az egérmutatót az előnézet lejátszásához, és válassza a Azure Media Player lehetőséget.

Ha az adatfolyam megjelenik a lejátszóban, a kódoló megfelelően konfigurálva van az AMS-hez való kapcsolódásra.

Ha hiba érkezik, a csatornát alaphelyzetbe kell állítani, és a kódoló beállításait módosítani kell. Útmutatásért tekintse meg a hibaelhárítással foglalkozó cikket.

## <a name="create-a-program"></a>Program létrehozása
1.  A csatorna lejátszásának megerősítése után hozzon létre egy programot. A AMSE eszköz élő lapján kattintson a jobb gombbal a program területére, és válassza az új program létrehozása lehetőséget.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Adja meg a program nevét, és ha szükséges, állítsa be az archiválási időszak hosszát (amely alapértelmezés szerint négy óra). Megadhat egy tárolási helyet is, vagy meghagyhatja az alapértelmezett értéket.
2.  Jelölje be a program indítása jelölőnégyzetet.
3.  Kattintson a program létrehozása lehetőségre.
4.  A program futása után erősítse meg a lejátszást. ehhez kattintson a jobb gombbal a programra, és navigáljon a program (ok) lejátszásához, majd válassza a Azure Media Player lehetőséget.
5.  A megerősítést követően kattintson a jobb gombbal a programra, majd válassza a kimeneti URL-cím másolása a vágólapra lehetőséget (vagy olvassa be ezt az információt a program információi és beállítások menüpontból a menüből).

Az adatfolyam most már készen áll a lejátszóba való beágyazásra, vagy az élő megtekintésre a közönség számára.

> [!NOTE]
> A program létrehozása kevesebb időt vesz igénybe, mint a csatornák létrehozása.

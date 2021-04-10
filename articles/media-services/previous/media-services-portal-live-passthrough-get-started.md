---
title: Élő stream helyszíni kódolókkal Azure Portal használatával | Microsoft Docs
description: Ez az ismertető végigkalauzolja egy olyan csatorna létrehozásának folyamatán, amely átmenő közvetítésre van konfigurálva.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6dd2f8a228c582e99f7d7281bd2d31faff8cadda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010796"
---
# <a name="perform-live-streaming-with-on-premises-encoders-using-azure-portal"></a>Élő stream továbbítása helyszíni kódolókkal Azure Portal használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portál](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](/rest/api/media/operations/channel)
> 
> 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez az ismertető végigkalauzolja egy olyan **csatorna** létrehozásának folyamatán, amely átmenő közvetítésre van konfigurálva az Azure Portalon. 

## <a name="prerequisites"></a>Előfeltételek
Az ismertetett eljárás végrehajtásához a következők szükségesek:

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.
* Egy webkamera. Például a [Telestream Wirecast kódoló](media-services-configure-wirecast-live-encoder.md). 

Kifejezetten ajánljuk, hogy olvassa el a következő cikkeket:

* [Azure Media Services RTMP Support and Live Encoders (Az Azure Media Services RTMP-támogatása és az élő kódolók)](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Overview of Live Steaming using Azure Media Services (Az Azure Media Services segítségével történő élő streamelés áttekintése)](media-services-manage-channels-overview.md)
* [Live streaming with on-premises encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md) (Élő stream továbbítása többszörös átviteli sebességű streamet létrehozó helyszíni kódolókkal)

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Az élő streamelés egy gyakori alaphelyzete

A következő lépések ismertetik, hogy milyen lépésekkel lehet olyan streamelő alkalmazásokat létrehozni, amelyek átmenő közvetítésre vannak konfigurálva. Ez az oktatóprogram bemutatja, hogyan hozhat létre és kezelhet átmenő csatornát és élő eseményeket.

> [!NOTE]
> Győződjön meg arról, hogy a tartalomstreameléshez használt streamvégpont **Fut** állapotban legyen. 
    
1. Csatlakoztasson a számítógéphez egy videokamerát. <br/>A beállítási ötletekért tekintse meg az [egyszerű és a hordozható eseményekre vonatkozó videós eszközöket]( https://link.medium.com/KNTtiN6IeT).
1. Indítson el és konfiguráljon egy élő helyszíni kódolót, amely többszörös sávszélességű RTMP- vagy fragmentált MP4-streamet állít elő. További tájékoztatást az [Azure Media Services RTMP Support and Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824) (Az Azure Media Services RTMP-támogatása és az élő kódolók) című cikk nyújt.<br/>Továbbá tekintse meg ezt a blogot: [élő streaming Production with OBS](https://link.medium.com/ttuwHpaJeT).
   
    Ezt a lépést a csatorna létrehozása után is elvégezheti.
1. Hozzon létre és indítson el egy átmenő csatornát.
1. Kérje le a Channel ingest URL (Csatorna betöltési URL-címe) értékét. 
   
    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
1. Kérje le a csatorna előnézeti URL-címét. 
   
    Ezen az URL-címen győződhet meg róla, hogy a csatorna rendben megkapja-e az élő streamet.
1. Hozzon létre egy élő eseményt/programot. 
   
    Az Azure portál használata esetén az élő esemény létrehozása egy objektumot is létrehoz. 

1. Amikor készen áll a streamelésre és az archiválásra, indítsa el az eseményt/programot.
1. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
1. Amikor le kívánja állítani az esemény streamelését és archiválását, állítsa le az eseményt/programot.
1. Törölje az eseményt/programot (és ha kívánja, törölje az objektumot).     

> [!IMPORTANT]
> Az [élő stream többszörös átviteli sebességű streamet létrehozó helyszíni kódolókkal történő továbbítását](media-services-live-streaming-with-onprem-encoders.md) ismertető cikkből tájékozódhat a helyszíni kódolókkal és átmenő csatornákkal történő élő streamelés fogalmairól és fontos szempontjairól.
> 
> 

## <a name="to-view-notifications-and-errors"></a>Az értesítések és a hibák megtekintése
Ha meg szeretné tekinteni az Azure portál által előállított értesítéseket és hibaüzeneteket, kattintson az Értesítés ikonra.

![Értesítések](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>Átmenő csatornák és események létrehozása és elindítása.
A csatornákhoz események/programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. Az eseményeket a csatornák kezelik. 

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási időtartam határozza meg azt is, hogy mennyi idővel ugorhatnak vissza az ügyfelek az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan eldobja azt a tartalmat, amely korábbi a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden esemény egy objektumhoz van társítva. Az esemény közzétételéhez létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfelek számára megadható streamelő URL-cím összeállítását.

Egy csatorna három egyidejűleg zajló esemény támogat, hogy több archívumot lehessen létrehozni ugyanabból a bejövő streamből. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

A meglévő élő eseményeket nem szabad újra felhasználni. Ehelyett hozzon létre egy új eseményt minden eseményhez, és indítsa el.

Amikor készen áll a streamelésre és az archiválásra, indítsa el az eseményt. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt. 

Az archivált tartalom törléséhez állítsa le és törölje az eseményt, majd törölje a hozzá társított objektumot. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

### <a name="to-use-the-portal-to-create-a-channel"></a>Csatorna létrehozása a portállal
Ez a szakasz azt mutatja be, hogyan lehet átmenő csatornát létrehozni a **Gyorslétrehozás** beállítással.

Az átmenő csatornákról az [élő stream többszörös átviteli sebességű streamet létrehozó helyszíni kódolókkal történő továbbítását](media-services-live-streaming-with-onprem-encoders.md) ismertető cikk nyújt részletes tájékoztatást.

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Kattintson a **Settings** (Beállítások) ablak **Live streaming** (Élő stream) elemére. 
   
    ![Első lépések](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    Megjelenik a **Live streaming** (Élő stream) ablak.
3. A **Quick Create** (Gyorslétrehozás) gombra kattintva létrehozhat egy az RTMP betöltési protokollt használó átmenő csatornát.
   
    Megjelenik a **CREATE A NEW CHANNEL** (Új csatorna létrehozása) ablak.
4. Nevezze el az új csatornát, és kattintson a **Create** (Létrehozás) gombra. 
   
    Ennek hatására létrejön egy, az RTMP-betöltési protokollt használó csatorna.

## <a name="create-events"></a>Események létrehozása
1. Válassza ki azt a csatornát, amelyhez eseményt szeretne hozzáadni.
2. Kattintson a **Live Event** (Élő esemény) gombra.

![Esemény](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása
A csatorna létrehozása után beolvashatja a betöltési URL-címeket. Ezeket kell megadnia az élő kódolónak. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

![Képernyőkép, amely az "élő közvetítés" oldalt jeleníti meg egy kiválasztott csatornával és a csatorna ablaktáblán.](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>Esemény megtekintése
Ha meg szeretne tekinteni egy eseményt, kattintson az Azure Portal **Watch** (Megtekintés) elemére. 

![Létrehozva](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Leállítása után az élő esemény automatikusan átalakul igény szerinti tartalommá.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Az átmenő csatornákról az [élő stream többszörös átviteli sebességű streamet létrehozó helyszíni kódolókkal történő továbbítását](media-services-live-streaming-with-onprem-encoders.md) ismertető cikk nyújt részletes tájékoztatást.

* Egy csatornát csak akkor lehet  leállítani, ha már leállítottak minden, a csatornán közvetített eseményt/programot.  A csatorna leállítását követően nem számítunk fel további díjakat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
* Egy csatornát csak akkor lehet törölni, ha töröltek minden, a csatornán közvetített élő eseményt.

## <a name="view-archived-content"></a>Archivált tartalom megtekintése
Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

Az eszközök kezeléséhez válassza a **beállítás** lehetőséget, majd kattintson az **eszközök** elemre.

![Objektumok](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

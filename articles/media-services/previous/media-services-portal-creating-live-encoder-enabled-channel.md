---
title: Élő közvetítés végrehajtása a Azure Media Services használatával többszörös sávszélességű streamek létrehozásához Azure Portal segítségével | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy az Azure Portal használatával hogyan hozhat létre egy egyszeres átviteli sebességű élő streamet fogadó csatornát, amely többszörös átviteli sebességű streammé kódolja a fogadott bemenetet.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 36a4eb2856beb3ae2b0227e92f0db26e01a1b616
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012751"
---
# <a name="perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-azure-portal"></a>Élő adatfolyam-továbbítás Media Services használatával többszörös átviteli sebességű streameket hozhat létre a Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portál](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST API](/rest/api/media/operations/channel)
> 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy egyszeres sávszélességű élő streamet fogadó **csatornát**, amely többszörös sávszélességű streammé kódolja azt.

További elméleti információk a valós idejű kódolásra képes csatornákról: [Élő adatfolyam továbbítása az Azure Media Services használatával, és többféle sávszélességű adatfolyamok létrehozása](media-services-manage-live-encoder-enabled-channels.md)

## <a name="common-live-streaming-scenario"></a>Gyakori élő adatfolyam-továbbítási forgatókönyv

A leggyakrabban használt streamelési alkalmazások kialakításához általában az alábbi lépések szükségesek.

> [!NOTE]
> Jelenleg az élő stream maximális javasolt időtartama 8 óra. Ha ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amshelp@microsoft.com e-mail-címen.

1. Csatlakoztasson a számítógéphez egy videokamerát. <br/>A beállítási ötletekért tekintse meg az [egyszerű és a hordozható eseményekre vonatkozó videós eszközöket]( https://link.medium.com/KNTtiN6IeT).

    Ha nem rendelkezik hozzáféréssel egy kamerához, az eszközök, például a [Wirecast](media-services-configure-wirecast-live-encoder.md) használhatók élő hírcsatornák létrehozásához.
1. Indítson el és állítson be egy helyszíni valós idejű kódolót, amely képes egy egyféle sávszélességű kimeneti adatfolyam továbbítására a következő protokollok valamelyikével: RTMP vagy Smooth Streaming. További tájékoztatást az [Azure Media Services RTMP Support and Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824) (Az Azure Media Services RTMP-támogatása és az élő kódolók) című cikk nyújt. <br/>Továbbá tekintse meg ezt a blogot: [élő streaming Production with OBS](https://link.medium.com/ttuwHpaJeT).

    Ezt a lépést a csatorna létrehozása után is elvégezheti.
1. Hozzon létre és indítson el egy csatornát.
1. Kérje le a Channel ingest URL (Csatorna betöltési URL-címe) értékét.

    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
1. Kérje le a csatorna előnézeti URL-címét.

    Ezen az URL-címen győződhet meg róla, hogy a csatorna rendben megkapja-e az élő streamet.
1. Hozzon létre egy eseményt/programot (ezzel egy objektumot is létrehoz).
1. Tegye közzé az eseményt (ezzel létrehozza a kapcsolódó objektumhoz tartozó OnDemand-lokátort is).
1. Amikor készen áll a streamelésre és az archiválásra, indítsa el az eseményt.
1. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
1. Amikor le szeretné állítani az esemény streamelését és archiválását, állítsa le az eseményt.
1. Törölje az eseményt (és ha szeretné, törölje az objektumot is).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot.
  További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók. A Media Services-fiók létrehozásával kapcsolatban lásd: [Create Account](media-services-portal-create-account.md) (Fiók létrehozása).
* Egy webkamera és egy egyszeres sávszélességű élő stream továbbítására alkalmas kódoló.

## <a name="create-a-channel"></a>Csatorna létrehozása

1. Az [Azure-portálon](https://portal.azure.com/) kattintson a Media Services elemre, majd Media Services-fiókja nevére.
2. Válassza a **Live Streaming** (Élő adatfolyam) lehetőséget.
3. Válassza a **Custom create** (Egyéni létrehozás) lehetőséget. Ez a beállítás lehetővé teszi egy olyan csatorna létrehozását, amellyel használható a Live Encoding.

    ![Csatorna létrehozása](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Kattintson a **Settings** (Beállítások) lehetőségre.

   1. Válassza ki a **Live Encoding**-csatorna típusát. Ez a típus azt jelenti, hogy élő kódolásra használható csatornát szeretne létrehozni. Ez azt jelenti, hogy a bejövő egyszeres sávszélességű streamet a rendszer elküldi a csatornára, majd az élő kódoló beállításai szerint többszörös sávszélességű streammé kódolja. További információk: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében). Kattintson az OK gombra.
   2. Adja meg a csatorna nevét.
   3. Kattintson a képernyő alján található OK gombra.
5. Kattintson az **Ingest** (Betöltés) fülre.

   1. Ezen a lapon választhat egy streamprotokollt. A **Live Encoding** csatornatípus esetén az érvényes protokollok a következők:

      * Single bitrate Fragmented MP4 (Egyszeres sávszélességű, fragmentált MP4) (Smooth Streaming)
      * Single bitrate RTMP (Egyszeres sávszélességű RTMP)

        A protokollok részletesebb leírását lásd: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).

        Ha a csatorna vagy a hozzá tartozó események/programok már elindultak, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szükség, hozzon létre külön-külön csatornákat az egyes streamprotokollokhoz.  
   2. Alkalmazhatja az IP-korlátozást a betöltésre.

       Megadhatja azokat az IP-címeket, amelyek jogosultak videókat betölteni erre a csatornára. Az engedélyezett IP-címek megadhatók egyetlen IP-címként (például "10.0.0.1"), egy IP-cím és egy CIDR alhálózati maszk használatával (pl. "10.0.0.1/22"), vagy egy IP-tartománnyal, egy IP-cím és egy pontozott decimális alhálózati maszk (például "10.0.0.1 (255.255.252.0)") használatával.

       Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.
6. A **Preview** (Előnézet) lapon alkalmazza az IP-korlátozást az előnézetre.
7. Az **Encoding** (Kódolás) lapon adja meg a kódolási előbeállítást.

    Jelenleg kizárólag a **Default 720p** (Alapértelmezett 720p) rendszerbeállítás-készletet választhatja. Egyéni előbeállítás megadásához nyisson egy Microsoft támogatási jegyet. Ezt követően adja meg az előbeállítás Ön számára létrehozott nevét.

> [!NOTE]
> A csatorna indítása jelenleg akár 30 percet is igénybe vehet. A csatorna újraindítása legfeljebb 5 percbe telik.

A csatorna létrehozását követően a csatornára kattintva, majd a **Settings** (Beállítások) lehetőséget kiválasztva megtekintheti a csatornakonfigurációkat.

További információk: [Live streaming using Azure Media Services to create multi-bitrate streams](media-services-manage-live-encoder-enabled-channels.md) (Élő streamelés az Azure Media Services segítségével többszörös sávszélességű streamek létrehozása érdekében).

## <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása

A csatorna létrehozása után beolvashatja a betöltési URL-címeket. Ezeket kell megadnia az élő kódolónak. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

![betöltési URL-címek](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Események létrehozása és kezelése

### <a name="overview"></a>Áttekintés

A csatornákhoz események/programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. Az eseményeket/programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol a csatornák folyamatosan közvetítik a különböző tartalmakat, amelyek adott időtartamon át tartó részeit programoknak nevezzük.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni az esemény felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási időtartam határozza meg azt is, hogy mennyi idővel ugorhatnak vissza az ügyfelek az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan eldobja azt a tartalmat, amely korábbi a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden esemény egy objektumhoz van társítva. Az esemény közzétételéhez létre kell hoznia egy OnDemand lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

Egy csatorna három egyidejűleg zajló esemény támogat, hogy több archívumot lehessen létrehozni ugyanabból a bejövő streamből. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Tegyük fel, hogy az üzleti igény szerint például 6 órát kell archiválni egy eseményből, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló esemény létrehozásával érheti el. Állítsa be az egyik eseményt az esemény 6 órájának archiválására, de ne tegye közzé a programot. A másik eseményt 10 perc archiválására állítsa be, és tegye is közzé a programot.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett inkább hozzon létre új programokat az új eseményekhez.

Amikor készen áll a streamelésre és az archiválásra, indítson el egy eseményt/programot. Amikor le szeretné állítani az esemény streamelését és archiválását, állítsa le az eseményt.

Az archivált tartalom törléséhez állítsa le és törölje az eseményt, majd törölje a hozzá társított objektumot. Az esemény által használt objektumok nem törölhetők. Először az eseményt kell törölni.

Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

### <a name="createstartstop-events"></a>Események létrehozása/indítása/leállítása

Ha elvégezte a stream és a csatorna összekapcsolását, elindíthatja a streamelési eseményt. Ehhez létre kell hoznia egy objektumot, egy programot és egy streamelési lokátort. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

Az esemény két különböző módon indítható el:

1. Új esemény hozzáadásához kattintson a **Channel** (Csatorna) oldal **Live Event** (Élő esemény) elemére.

    Adja meg az esemény nevét, az objektum nevét, az archiválás időtartamát és a titkosítási beállítást.

    ![program létrehozása](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    Ha nem törli a jelet a **Publish this live event now** (Program azonnali közzététele) lehetőség mellől, a rendszer létrehozza az eseményt és a közzétételi URL-címeket.

    Ha készen áll az esemény streamelésére, kattintson a **Start** (Indítás) gombra.

    Az esemény elindítását követően a **Watch** (Megtekintés) gombra kattintva elindíthatja a tartalom lejátszását.
2. Alternatív megoldásként gyorsabb utat is választhat: kattintson a **Channel** (Csatorna) oldal **Go Live** (Élő közvetítés indítása) gombjára. Ekkor létrejön az alapértelmezett objektum, program és streamelési lokátor.

    Az eseményt a rendszer **default** néven hozza létre, és 8 órás archiválási időtartamot ad meg hozzá.

A közzétett eseményt a **Live event** (Élő esemény) oldalon tekintheti meg.

Az **Off Air** (Közvetítés leállítása) gombbal az összes élő eseményt leállíthatja.

## <a name="watch-the-event"></a>Esemény megtekintése

Ha meg szeretne tekinteni egy eseményt, kattintson az Azure Portal **Watch** (Megtekintés) elemére.

![Létrehozva](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

A leállítása után az élő esemény automatikusan átalakítja az eseményeket igény szerinti tartalommá.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha befejezte az esemény streamelését, és törölni szeretné a korábban kiosztott erőforrásokat, kövesse az alábbi eljárást.

* Állítsa le a stream továbbítását a kódolóban.
* Állítsa le a csatornát. A csatorna leállítását követően nem számítunk fel további díjakat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
* A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. A leállított állapotú csatornák után nem számítunk fel díjakat.

## <a name="view-archived-content"></a>Archivált tartalom megtekintése

Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli.

> [!WARNING]
> Egy eszköz **nem** törölhető, ha egy esemény használja. először törölni kell az eseményt.

Az eszközök kezeléséhez válassza a **beállítás** lehetőséget, majd kattintson az **eszközök** elemre.

![Objektumok](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Megfontolandó szempontok

* Jelenleg az élő stream maximális javasolt időtartama 8 óra. Ha ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amshelp@microsoft.com e-mail-címen.
* Győződjön meg arról, hogy a tartalomstreameléshez használt streamvégpont **Fut** állapotban van.

## <a name="next-steps"></a>Következő lépések

Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

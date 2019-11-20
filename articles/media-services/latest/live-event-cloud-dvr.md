---
title: Igény szerinti videolejátszás létrehozásához használja az időeltolást és az élő kimeneteket
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan használhat időeltolásos és élő kimeneteket élő streamek rögzítésére és igény szerinti lejátszás létrehozására.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: acba251a57f39c07d690d0c55665b8914feaf06c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186230"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Igény szerinti videolejátszás létrehozásához használja az időeltolást és az élő kimeneteket

A Azure Media Services egy [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs) objektum, például egy digitális videomagnó, amely az élő streamet az Media Services-fiókban lévő adategységbe fogja fogni és rögzíteni. A rögzített tartalom megmarad az [eszköz](https://docs.microsoft.com/rest/api/media/assets) erőforrása által meghatározott tárolóban (a tároló a fiókjához csatolt Azure Storage-fiókban található). Az élő kimenet lehetővé teszi a kimenő élő stream bizonyos tulajdonságainak szabályozását is, például azt, hogy a stream mekkora részét tárolja az archívumban (például a felhőalapú DVR kapacitása), vagy ha a nézők megkezdhetik az élő stream megtekintését. A lemezen lévő archiválás körkörös archív "ablak", amely csak az élő kimenet **archiveWindowLength** tulajdonságában megadott tartalom mennyiségét tárolja. Az ezen az ablakon kívül eső tartalmat a rendszer automatikusan elveti a tárolóból, és nem helyreállítható. A archiveWindowLength érték egy ISO-8601 TimeSpan időtartamot jelöl (például PTHH: PP: MM), amely meghatározza a DVR kapacitását. Az érték legalább három percből legfeljebb 25 órára állítható be.

Az élő esemény és a hozzá tartozó élő kimenetek közötti kapcsolat hasonlít a hagyományos televíziós közvetítéshez, abban az esetben, ha egy csatorna (élő esemény) a videó állandó streamjét jelöli, és a rögzítés (élő kimenet) egy adott időszakra vonatkozik (például esti Hírek innen: 6: a következő időpontig: 16:00 – 7. Ha a stream az élő eseménybe áramlik, megkezdheti a folyamatos átviteli eseményt egy eszköz, egy élő kimenet és a folyamatos átviteli lokátor létrehozásával. Az élő kimenet archiválja a streamet, és elérhetővé teszi a nézők számára a [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints)keresztül. Létrehozhat több élő kimenetet (legfeljebb három maximumot) egy élő eseményen, eltérő archiválási hosszúságokkal és beállításokkal. Az élő adatfolyam-továbbítási munkafolyamattal kapcsolatos információkért lásd az [általános lépések](live-streaming-overview.md#general-steps) szakaszt.

## <a name="using-a-dvr-during-an-event"></a>DVR használata egy esemény során

Ebből a szakaszból megtudhatja, hogyan használhatja a DVR-t egy esemény során annak szabályozására, hogy a stream milyen részeit használja a "Rewind" művelethez.

A `archiveWindowLength` érték azt határozza meg, hogy a megjelenítők mennyi idő alatt térhetnek el az aktuális élő pozíciótól. Az `archiveWindowLength` érték azt is meghatározza, hogy mennyi ideig lehet növekedni az ügyfelek jegyzékfájlja.

Tegyük fel, hogy egy futball-játékot közvetít, és `ArchiveWindowLength` csak 30 percet tartalmaz. Egy megjelenítőt, aki a játék elindítása után 45 perccel megtekinti az eseményt, legfeljebb 15 perces jelet kereshet vissza. A játékhoz tartozó élő kimenetek az élő esemény leállítása után folytatódnak. A archiveWindowLength kívül eső tartalmat a rendszer folyamatosan elveti a tárolóból, és nem helyreállítható. Ebben a példában az esemény kezdete és a 15 perces megjelölés közötti videó törölve lett a DVR-ből, és az eszköz blob Storage tárolójában található tárolóból. Az Archívum nem helyreállítható, és el lett távolítva a tárolóból az Azure Blob Storage-ban.

Az élő események legfeljebb három párhuzamosan futó élő kimenetet támogatnak (egyszerre legfeljebb 3 felvételt/archívumot hozhat létre egy élő streamből egyszerre). Ez a támogatás lehetővé teszi az események különböző részeinek szükség szerinti közzétételét és archiválását. Tegyük fel, hogy egy nonstop élő lineáris hírcsatornát kell közvetíteni, és a nap folyamán a különböző programok felvételeit kell létrehoznia, hogy az ügyfelek igény szerinti tartalmat kapjanak a felzárkózás megtekintéséhez. Ebben az esetben először létre kell hoznia egy elsődleges élő kimenetet egy rövid, 1 órás vagy annál rövidebb archív ablakból – ez az az elsődleges élő stream, amelyet a nézőknek be kell hangolni. Hozzon létre egy streaming-lokátort ehhez az élő kimenethez, és tegye közzé azt az alkalmazásban vagy a webhelyen az "élő" hírcsatorna használatával. Az élő esemény futása közben programozott módon hozhat létre egy második egyidejű élő kimenetet egy program elején (vagy 5 perc múlva, hogy a későbbiekben néhány leírót biztosítson). A második élő kimenet a program befejezése után 5 perccel törölhető. Ezzel a második adategységgel létrehozhat egy új folyamatos átviteli lokátort a program igény szerinti eszközként való közzétételéhez az alkalmazás katalógusában. Ezt a folyamatot többször is megismételheti a többi program határán, vagy kiemeli, hogy az igény szerinti videókként szeretne megosztani. az első élő kimenetből származó "élő" hírcsatornák továbbra is közvetítik a lineáris hírcsatornát.

## <a name="creating-an-archive-for-on-demand-playback"></a>Archiválás létrehozása igény szerinti lejátszáshoz

Az az eszköz, amelyet az élő kimenet archivál, automatikusan egy igény szerinti eszközvé válik az élő kimenet törlésekor. Az élő események leállításához minden élő kimenetet törölnie kell. A nem kötelező jelző [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) használatával automatikusan eltávolíthatja az élő kimeneteket a leállítás után.

Az esemény leállítása és törlése után a felhasználók az archivált tartalmat igény szerint videóként is továbbíthatja, ha nem törli az eszközt. Egy eszköz nem törölhető, ha egy esemény használja. először törölni kell az eseményt.

Ha közzétette az élő kimenet adategységét egy streaming-lokátor használatával, az élő esemény (a DVR-ablak hosszára számítva) továbbra is megtekinthető marad, amíg a streaming-kereső lejárata vagy törlése megkezdődik.

További információkért lásd:

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)

> [!NOTE]
> Ha törli az élő kimenetet, nem törli az objektum mögöttes eszközét és tartalmát.

## <a name="next-steps"></a>Következő lépések

* [Videók kivágása](subclip-video-rest-howto.md).
* [Az eszközökhöz tartozó szűrők definiálása](filters-dynamic-manifest-rest-howto.md).

---
title: A térbeli elemzés áttekintése
titleSuffix: Azure Cognitive Services
description: Ez a dokumentum ismerteti a Computer Vision térbeli elemzési tárolójának alapvető fogalmait és funkcióit.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100575373"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>A Computer Vision térbeli elemzés áttekintése

Computer Vision térbeli elemzés az Azure Computer Vision Cognitive Services új funkciója, amely segít a szervezeteknek a fizikai helyük értékének maximalizálásában azáltal, hogy egy adott területen belül megismerik a személyek mozgásait és jelenlétét. Lehetővé teszi a CCTV vagy a térfigyelő kamerákból származó videók betöltését, AI-műveletek futtatásával kinyerheti az adatforrásokat a videó streamekről, és más rendszerek által használt eseményeket is létrehozhat. A kamerás adatfolyamok bemenetével egy AI-művelet elvégezheti az olyan műveleteket, mint például a tárhely beírása vagy a szembenéző maszkkal való megfelelőség mérése, illetve a közösségi elhatárolja vonatkozó irányelvek.

## <a name="the-basics-of-spatial-analysis"></a>A térbeli elemzés alapjai

Napjainkban a térbeli elemzések alapvető műveletei mind a videó betöltését, a videóban szereplő személyeket észlelik, nyomon követi a személyeket, ahogy az idő múlásával mozognak, és eseményeket generálnak, mivel az emberek a fontos régiókkal működnek együtt.

## <a name="spatial-analysis-terms"></a>Térbeli elemzési feltételek

| Időszak | Definíció |
|------|------------|
| Emberek észlelése | Ez az összetevő a "hol vannak a képen emberek?" kérdésre ad választ? Megkeresi az embereket egy képen, és átadja a határoló dobozt, amely feltünteti az egyes személyek helyét a People Tracking összetevőnek. |
| Személyek követése | Ez az összetevő összekapcsolja a személyek észleléseit az idő múlásával, ahogy a felhasználók egy kamera előtt mozognak. Időbeli logikát használ arról, hogy az emberek általában hogyan helyezik át a felhasználók általános megjelenésével kapcsolatos alapvető információkat. Nem követi nyomon a több kamera közötti személyeket. Ha egy személy már körülbelül egy percnél hosszabb ideig van a kamera nézete, majd újra belép a kamera nézetbe, a rendszer ezt új személynek fogja észlelni. Az emberek nyomon követése nem egyedi módon azonosítja a kamerák közötti személyeket. Nem használ arc-felismerést vagy járási követést. |
| Álarcok észlelése | Ez az összetevő észleli egy személy arcát a kamera mezőjében, és azonosítja az álarc jelenlétét. Ehhez az AI-művelet képeket keres a videóból; az arc észlelése esetén a szolgáltatás egy határoló mezőt biztosít az arc körül. Az objektum-észlelési képességek használatával azonosíthatja az arc maszkok jelenlétét a határolókereton belül. Az álarcok észlelése nem jelenti azt, hogy az egyik arcot megkülönbözteti a másiktól, az arc-attribútumok előrejelzését vagy osztályozását, illetve az Arcfelismerés végrehajtását. |
| Érdekes régió | Ez egy, a bemeneti videóban definiált zóna vagy vonal a konfiguráció részeként. Ha egy személy a videó régióját használja, a rendszer létrehoz egy eseményt. A PersonCrossingLine művelet esetében például egy sor van definiálva a videóban. Ha egy személy keresztezi az eseményt, az esemény létrejön. |
| Esemény | Az esemény a térbeli elemzés elsődleges kimenete. Az egyes műveletek egy adott eseményt küldenek rendszeresen (pl.: percenként egyszer) vagy egy adott trigger bekövetkeztekor. Az esemény információt tartalmaz arról, hogy mi történt a bemeneti videóban, de nem tartalmaz képeket vagy videókat. A PeopleCount művelet például kibocsáthat egy eseményt, amely a frissített számot tartalmazza minden alkalommal, amikor a személyek száma megváltozik (trigger), vagy percenként (rendszeres időközönként). |

## <a name="responsible-use-of-spatial-analysis-technology"></a>A térbeli elemzési technológia felelősségteljes használata

A térbeli elemzési technológia felelősségteljes használatának megismeréséhez tekintse meg az [átláthatóság megjegyzését](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). A Microsoft átláthatósági megjegyzései segítenek megérteni, hogyan működik a mesterséges intelligencia technológiája, a választható rendszertulajdonosok pedig befolyásolhatják a rendszerteljesítményt és a működést, valamint a teljes rendszerre való gondolkodás fontosságát, beleértve a technológiát, a személyeket és a környezetet.

## <a name="spatial-analysis-gating-for-public-preview"></a>Nyilvános előzetes verzió térbeli elemzési kapuzás

Annak biztosítása érdekében, hogy a rendszer a térbeli elemzést használja, az alkalmazási folyamat keretében elérhetővé tesszük ezt a technológiát az ügyfeleknek. A térbeli elemzéshez való hozzáféréshez először ki kell töltenie az online bevitel űrlapot. [Kezdje el az alkalmazást itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

A térbeli elemzés nyilvános előzetes verziójához való hozzáférésre a Microsoft saját belátása szerint a jogosultsági feltételek, a beléptetési folyamat és a rendelkezésre állás alapján korlátozott számú ügyfél támogatására jogosult. A nyilvános előzetes verzióban olyan ügyfeleket keresünk, akik jelentős kapcsolattal rendelkeznek a Microsofttal, és akik a javasolt felhasználási esetekben, valamint a felelősségteljes AI-kötelezettségvállalásokkal összhangban álló további forgatókönyvekkel foglalkoznak.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a térbeli elemzési tárolóval](spatial-analysis-container.md)
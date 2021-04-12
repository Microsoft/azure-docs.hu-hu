---
title: Mi a térbeli analízis?
titleSuffix: Azure Cognitive Services
description: Ez a dokumentum ismerteti a Computer Vision térbeli elemzési tárolójának alapvető fogalmait és funkcióit.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284542"
---
# <a name="what-is-spatial-analysis"></a>Mi a térbeli analízis?

A térbeli elemzési szolgáltatás segít a szervezeteknek a fizikai helyük értékének maximalizálásában azáltal, hogy megismerik az adott területen belüli mozgásokat és jelenlétet. Lehetővé teszi a CCTV vagy a térfigyelő kamerákból származó videók betöltését, AI-műveletek futtatásával kinyerheti az adatforrásokat a videó streamekről, és más rendszerek által használt eseményeket is létrehozhat. A kamerás adatfolyamok bemenetével egy AI-művelet elvégezheti az olyan műveleteket, mint például a tárhely beírása vagy a szembenéző maszkkal való megfelelőség mérése, illetve a közösségi elhatárolja vonatkozó irányelvek.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Művelet

A térbeli elemzések alapvető műveletei mindegyike egy olyan folyamatra épül, amely videó betöltését, észleli a videóban lévő személyeket, nyomon követi a személyeket az idő múlásával, és az eseményeket úgy hozza létre, hogy az emberek a fontos régiókkal kommunikálnak.

## <a name="spatial-analysis-features"></a>Térbeli elemzési funkciók

| Szolgáltatás | Definíció |
|------|------------|
| **Emberek észlelése** | Ez az összetevő a következő kérdésre ad választ: "hol vannak az emberek ebben a képen?" Megkeresi a rendszerképekben szereplő személyeket, és átadja az egyes személyeknek a személyek nyomon követésére szolgáló összetevőt jelző határoló mezőt. |
| **Személyek követése** | Ez az összetevő az emberek észleléseit az idő múlásával kapcsolja össze, ahogy a felhasználók egy kamera előtt mozognak. Időbeli logikát használ arról, hogy az emberek általában hogyan helyezik át a személyek általános megjelenésével kapcsolatos alapvető információkat. Nem követi nyomon a több kamera közötti személyeket. Ha egy személy körülbelül egy percnél hosszabb időre kilép a nézetből, majd újra belép a kamera nézetbe, a rendszer ezt új személynek fogja észlelni. Az emberek nyomon követése nem egyedi módon azonosítja a kamerák közötti személyeket. Nem használ arc-felismerést vagy járási követést. |
| **Álarcok észlelése** | Ez az összetevő észleli egy személy arcát a kamera mezőjében, és azonosítja az álarc jelenlétét. Az AI-művelet képeket keres a videóból; az arc észlelése esetén a szolgáltatás egy határoló mezőt biztosít az arc körül. Az objektum-észlelési képességek használatával azonosíthatja az arc maszkok jelenlétét a határolókereton belül. Az álarcok észlelése nem jelenti azt, hogy az egyik arcot megkülönbözteti a másiktól, az arc-attribútumok előrejelzését vagy osztályozását, illetve az Arcfelismerés végrehajtását. |
| **Érdekes régió** | Ez egy felhasználó által definiált zóna vagy vonal a bemeneti videó keretében. Ha egy személy a videóban ezzel a régióval kommunikál, a rendszer létrehoz egy eseményt. A PersonCrossingLine művelet esetében például egy sor van definiálva a videóban. Ha egy személy keresztezi az eseményt, az esemény létrejön. |
| **Esemény** | Az esemény a térbeli elemzés elsődleges kimenete. Az egyes műveletek egy adott eseményt (például percenként egyszer) vagy egy adott eseményindító bekövetkezett eseményeit bocsátanak ki. Az esemény információt tartalmaz arról, hogy mi történt a bemeneti videóban, de nem tartalmaz képeket vagy videókat. A PeopleCount művelet például kibocsáthat egy eseményt, amely a frissített számot tartalmazza minden alkalommal, amikor a személyek száma megváltozik (trigger), vagy percenként (rendszeres időközönként). |

## <a name="get-started"></a>Bevezetés

### <a name="public-preview-gating"></a>Nyilvános előzetes kapuzás

Annak biztosítása érdekében, hogy a rendszer a térbeli elemzést használja, az alkalmazási folyamat keretében elérhetővé tesszük ezt a technológiát az ügyfeleknek. A térbeli elemzéshez való hozzáféréshez először ki kell töltenie az online bevitel űrlapot. [Kezdje el az alkalmazást itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

A térbeli elemzés nyilvános előzetes verziójához való hozzáférésre a Microsoft saját belátása szerint a jogosultsági feltételek, a beléptetési folyamat és a rendelkezésre állás alapján korlátozott számú ügyfél támogatására jogosult. A nyilvános előzetes verzióban olyan ügyfeleket keresünk, akik jelentős kapcsolattal rendelkeznek a Microsofttal, ezért a javasolt használati esetekben, valamint a felelősségteljes AI-kötelezettségvállalásokkal folytatott további forgatókönyvekkel foglalkozunk.

### <a name="follow-a-quickstart"></a>Útmutató követése

Miután hozzáférést kapott a térbeli elemzéshez, kövesse a gyors üzembe helyezést a tároló beállításához [és a videó](spatial-analysis-container.md) elemzésének megkezdéséhez.

## <a name="responsible-use-of-spatial-analysis-technology"></a>A térbeli elemzési technológia felelősségteljes használata

A térbeli elemzési technológia felelősségteljes használatának megismeréséhez tekintse meg az [átláthatóság megjegyzését](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). A Microsoft átláthatósági megjegyzései segítenek megérteni, hogyan működik a mesterséges intelligencia technológiája, a választható rendszertulajdonosok pedig befolyásolhatják a rendszerteljesítményt és a működést, valamint a teljes rendszerre való gondolkodás fontosságát, beleértve a technológiát, a személyeket és a környezetet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> Gyors útmutató [: térbeli elemzési tároló](spatial-analysis-container.md)("" "" "" "" "" "")
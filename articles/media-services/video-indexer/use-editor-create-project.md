---
title: Projektek létrehozása és videoklipek hozzáadása a Video Indexer Editor használatával
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan hozhat létre projekteket, és hogyan adhat hozzá videoklipeket a Video Indexer Editor használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 07e4b05e12a5994c707a171c5736aea04a9c9723
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632926"
---
# <a name="add-video-clips-to-your-projects"></a>Videoklipek hozzáadása a projektekhez

A [video Indexer](https://www.videoindexer.ai/) webhely lehetővé teszi, hogy a videó mélyreható megállapításait használja: keresse meg a megfelelő médiatartalom tartalmát, keresse meg a kívánt részeket, és használja az eredményeket egy teljesen új projekt létrehozásához. 

A létrehozást követően a projekt megjeleníthető és letölthető Video Indexer, és használható a saját szerkesztési alkalmazásaiban vagy az alárendelt munkafolyamatokban.

Bizonyos esetekben hasznos lehet a funkció: 

* Movie Highlights for Trailers létrehozása
* A videók régi videoklipek használata a hírekben.
* Rövidebb tartalom létrehozása a közösségi médiához.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy projektet, és hogyan adhat hozzá a videókból a projekthez kiválasztott klipeket. 

## <a name="create-new-project-and-manage-videos"></a>Új projekt létrehozása és videók kezelése

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
1. Válassza a **projektek** fület. Ha korábban már létrehozott projekteket, az összes többi projektet itt fogja látni.
1. Kattintson az **új projekt létrehozása** lehetőségre.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Új projekt létrehozása":::
1. A ceruza ikonra kattintva adjon nevet a projektnek. Cserélje le a "névtelen projekt" nevű szöveget a projekt nevére, és kattintson az ellenőrzési lehetőségre.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Új projekt":::
    
### <a name="add-videos-to-the-project"></a>Videók hozzáadása a projekthez

> [!NOTE]
> A projektek jelenleg csak ugyanabban a nyelven indexelt videókat tartalmazhatnak. </br>Miután kiválasztott egy videót egy adott nyelven, nem adhat hozzá olyan videókat a fiókjában, amelyek más nyelveken vannak, a más nyelvekkel rendelkező videók szürkén jelennek meg/le lesznek tiltva.

1. A **videók hozzáadása** lehetőség kiválasztásával adhat hozzá a projektben használni kívánt videókat.

    Ekkor megjelenik a fiókjában található összes videó, valamint egy keresőmező, amely a "szöveg keresése, kulcsszavak vagy vizuális tartalom keresése" kifejezést tartalmazza. Kereshet olyan videókat, amelyekben megadott személy, címke, márka, kulcsszó vagy előfordulás szerepel az átiratban és az OCR-ben.
    
    Az alábbi képen például az "egyéni jövőkép" kifejezést tartalmazó videók szerepelnek az átiratban (a **szűrő** használata, ha szűrni szeretné a keresési eredményeket).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="A képernyőképen az egyéni jövőképet megemlítő videók keresése látható":::
1. A **Hozzáadás** gombra kattintva hozzáadhat videókat a projekthez.
1. Most megjelenik az összes kiválasztott videó. Ezek a videók, amelyekről a projekthez tartozó klipeket fogja kiválasztani.

    Átrendezheti a videók sorrendjét húzással vagy eldobással, vagy kiválaszthatja a lista menü gombot, majd a lejjebb vagy a **mozgatás** **lehetőséget választva.** A lista menüből a projektből is el tudja távolítani a videót. 
    
    A **videók hozzáadása** lehetőség választásával bármikor hozzáadhat további videókat a projekthez. A projekthez ugyanazt a videót több előfordulás is hozzáadhatja. Erre akkor lehet szükség, ha egy videóból egy klipet szeretne megjeleníteni, majd egy másikat, majd egy másikat az első videóból. 

### <a name="select-clips-to-use-in-your-project"></a>Válassza ki a projektben használni kívánt klipeket

Ha az egyes videók jobb oldalán lévő lefelé mutató nyílra kattint, akkor a videóban az időbélyegek (a videó klipei) alapján megnyithatja a bepillantást. 

1. Adott klipekhez tartozó lekérdezések létrehozásához használja a "keresés átiratban, vizuális szöveg, emberek és címkék" kifejezést.
1. Az információk **megtekintése** elemre kattintva testreszabhatja, hogy mely információt szeretné megtekinteni, és melyeket nem szeretne látni. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Képernyőfelvétel: a kognitív szolgáltatásokat kipróbáló videók keresése":::
1. Szűrők hozzáadásával további részleteket adhat meg a keresett jelenetek közül a **szűrési beállítások lehetőség** kiválasztásával.

    Több szűrőt is hozzáadhat. 
1. Ha elégedett az eredménnyel, válassza ki a projekthez hozzáadni kívánt klipeket a hozzáadni kívánt szegmens kiválasztásával. A klip kijelölését a szegmensre kattintva törölheti.
    
    Adja hozzá a videó összes szegmensét (vagy a keresés után az összeset) a videó melletti lista menüpontra kattintva, majd **válassza az összes kijelölése** lehetőséget. 

A klipek kiválasztása és sorrendbe állítása közben megtekintheti a videót az oldal jobb oldalán lévő lejátszóban. 

> [!IMPORTANT]
> Ha módosításokat hajt végre, ne felejtse el menteni a projektet a lap tetején található **projekt mentése** gombra kattintva. 

### <a name="render-and-download-the-project"></a>A projekt renderelése és letöltése

> [!NOTE]
> Video Indexer fizetős fiókok esetében a projekt kódolásának költségei. Video Indexer próbaverziós fiókok esetében 5 órányi renderelésre van korlátozva.

1. Ha elkészült, győződjön meg arról, hogy a projekt el lett mentve. Most már elvégezheti a projekt megjelenítését. Kattintson a **Render (megjelenítés**) elemre, és megjelenik egy előugró ablak, amely azt jelzi, hogy a video Indexer egy fájlt fog megjeleníteni, majd a letöltési hivatkozást elküldi az e-mail-címre. Válassza a Folytatás lehetőséget. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="A képernyőfelvétel megjeleníti Video Indexer a projekt megjelenítésének és letöltésének lehetőségével":::
    
    Megjelenik egy értesítés is arról, hogy a projekt az oldal tetején jelenik meg. Miután megtörtént a megjelenítés, egy új értesítés jelenik meg, amely szerint a projekt sikeresen meg lett jelenítve. A projekt letöltéséhez kattintson az értesítésre. MP4 formátumban tölti le a projektet.
1. A mentett projektek a **projektek** lapról érhetők el. 

    Ha ezt a projektet választja, megjelenik a projekt összes bepillantást és idővonala. Ha a **videó szerkesztőt** választja, akkor folytathatja a projekt szerkesztését. A szerkesztések közé tartoznak a videók és a klipek hozzáadása vagy eltávolítása, illetve a projekt átnevezése.
    
## <a name="create-a-project-from-your-video"></a>Projekt létrehozása a videóból

Létrehozhat egy új projektet közvetlenül a fiókjában található videóból. 

1. Nyissa meg a Video Indexer webhely **könyvtár** lapját.
1. Nyissa meg a projekt létrehozásához használni kívánt videót. Az áttekintések és az idővonal lapon válassza a **videó szerkesztő** gombot.

    Ezzel az oldalra kerül, amelyet egy új projekt létrehozásához használt. Az új projekttől eltérően a videó időbélyegzővel ellátott bepillantást nyerhet, amelyet korábban már elkezdett szerkeszteni.

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)


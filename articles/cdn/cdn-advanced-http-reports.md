---
title: Használati statisztikák elemzése Azure CDN Advanced HTTP-jelentésekkel | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre speciális HTTP-jelentéseket a Microsoft Azure CDN-ben. Ezek a jelentések részletes információkat biztosítanak a CDN-tevékenységről.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e2ef1367cbbb1fb71159c4520c3d1481904c1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779322"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Használati statisztikák elemzése az Azure CDN speciális HTTP-jelentéseivel
## <a name="overview"></a>Áttekintés
Ez a dokumentum a speciális HTTP-jelentéskészítést ismerteti Microsoft Azure CDN-ben. Ezek a jelentések részletes információkat biztosítanak a CDN-tevékenységről.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>A speciális HTTP-jelentések elérése
1. A CDN-profil panelen kattintson a **kezelés** gombra.
   
    ![CDN-profil panel kezelése gomb](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Vigye az egérmutatót az **elemzés** lapra, majd vigye a kurzort a **speciális http-jelentések** menü fölé.  Kattintson a **http nagyméretű platform** elemre.
   
    ![CDN felügyeleti portál – speciális jelentések menü](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Megjelenik a jelentés beállításai.

## <a name="geography-reports-map-based"></a>Földrajzi jelentések (Térkép-alapú)
Öt jelentés érhető el, amelyek kihasználják a térképet, hogy jelezze a tartalmat kérő régiókat. Ezek a jelentések globális Térkép, Egyesült Államok Térkép, Kanada Térkép, Európa térkép és Ázsia és a Csendes-óceáni térség Térkép.

Minden térképes jelentés földrajzi entitásokat (például országokat/régiókat) sorakoztat fel, amelyek segítenek megjeleníteni azokat a helyszíneket, amelyekről a tartalmat kérik. Ezt úgy teheti meg, hogy az adott régióban igénybe venni kívánt mennyiségnek megfelelően színkódolással teszi lehetővé az egyes régiókat. A világosabban árnyékolt régiók a tartalom alacsonyabb igényét jelzik, míg a sötétebb régiók a tartalom magasabb szintű igényét jelzik.

Az egyes régiók részletes forgalmi és sávszélesség-információi közvetlenül a térképen láthatók. Így megtekintheti a találatok teljes számát, a találatok százalékos arányát, az átvitt adatmennyiséget (GB-ban), valamint az egyes régiókba továbbított adatmennyiség százalékos arányát. Tekintse meg az egyes mérőszámok leírását. Végül, ha az egérmutatót egy régióra (például országra/régióra, államra vagy tartományra) viszi, a régióban megjelenő találatok aránya elemleírásként jelenik meg.

Az alábbiakban egy rövid leírást talál az egyes Térkép alapú földrajzi jelentésekhez.

| Jelentés neve | Leírás |
| --- | --- |
| Globális Térkép |Ez a jelentés lehetővé teszi a CDN-tartalom globális igényének megtekintését. Az egyes országok/régiók színkóddal rendelkeznek a világ térképén, hogy jelezze az adott régióból származó találatok százalékos arányát. |
| Egyesült Államok Térkép |Ez a jelentés lehetővé teszi, hogy megtekintse a CDN-tartalom iránti igényt a Egyesült Államokban. Az egyes állapotok színkódoltak ezen a térképen, hogy jelezze az adott régióból származó találatok százalékos arányát. |
| Kanada Térkép |Ez a jelentés lehetővé teszi, hogy megtekintse a CDN-tartalom iránti igényét Kanadában. Ezen a térképen minden tartomány színkódolt, hogy jelezze az adott régióból származó találatok százalékos arányát. |
| Európa térképe |Ez a jelentés lehetővé teszi, hogy megtekintse a CDN-tartalom iránti igényét Európában. Ezen a térképen minden ország/régió színre van kódolva, hogy jelezze az adott régióból származó találatok százalékos arányát. |
| Ázsia és a Csendes-óceáni térség Térkép |Ez a jelentés lehetővé teszi, hogy megtekintse a CDN-tartalom iránti igényét Ázsiában. Ezen a térképen minden ország/régió színre van kódolva, hogy jelezze az adott régióból származó találatok százalékos arányát. |

## <a name="geography-reports-bar-charts"></a>Földrajzi jelentések (sávdiagramok)
Két további jelentés is rendelkezésre áll, amelyek statisztikai adatokat biztosítanak a földrajz, a legnépszerűbb városok és a legnépszerűbb országok alapján. Ezek a jelentések az adott országokból/régiókból származó találatok száma szerint rangsorolják a városokat és országokat/régiókat. Az ilyen típusú jelentések létrehozásakor a sávdiagram azon tíz várost vagy országot/régiót jelzi, amely egy adott platformon keresztül kért tartalmat. Ez a sávdiagram lehetővé teszi, hogy gyorsan mérje fel azokat a régiókat, amelyek a legnagyobb számú kérelmet létrehozzák a tartalomhoz.

A gráf bal oldali (y tengely) azt jelzi, hogy hány találat történt a megadott régióban. Közvetlenül a gráf alatt (x tengely) az első 10 régióhoz tartozó címkét talál.

### <a name="using-the-bar-charts"></a>A sávdiagramok használata
* Ha egy sáv fölé viszi a kurzort, a rendszer elemleírásként jeleníti meg a régióban bekövetkezett találatok teljes számát.
* A legfontosabb városok elemleírása a város nevét, államát/tartományát, valamint ország/régió rövidítését azonosítja.
* Ha a város vagy régió (azaz állam/megye) nem határozható meg, és nem állapítható meg, akkor a rendszer nem tudja megállapítani, hogy ismeretlenek-e. Ha az ország/régió ismeretlen, akkor a rendszer két kérdőjelet (például??) jelenít meg.
* Egy jelentés tartalmazhat az "Európa" vagy az "ázsiai/csendes-óceáni régió" mérőszámokat. Ezeknek az elemeknek nem célja, hogy statisztikai adatokat szolgáltassanak az adott régiókban lévő összes IP-címről. Ehelyett csak azokra az IP-címekről származó kérelmekre érvényesek, amelyek az Európai vagy az ázsiai/csendes-óceáni régión kívül, egy adott városra vagy országra/régióra terjednek ki.

A sávdiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt megtalálja a találatok teljes számát, a találatok százalékos arányát, az átvitt adatmennyiséget (GB-ban), valamint a legfontosabb 250-régiókhoz továbbított adatmennyiség százalékos arányát. Tekintse meg az egyes mérőszámok leírását.

Az alábbi jelentések mindkét típusához rövid leírást kell megadnia.

| Jelentés neve | Leírás |
| --- | --- |
| Legnépszerűbb városok |Ez a jelentés az adott régióból származó találatok számának megfelelően rangsorolja a városokat. |
| Leggyakoribb országok |Ez a jelentés az adott országból/régióból származó találatok száma szerint rangsorolja az országokat és a régiókat. |

## <a name="daily-summary"></a>Napi összefoglalás
A napi összefoglaló jelentés segítségével megtekintheti az adott platformon a találatok és az adatok napi rendszerességgel történő átadásának teljes számát. Ez az információ a CDN-tevékenység mintáinak gyors felismerésére használható. Ez a jelentés például segítséget nyújt annak észlelésében, hogy mely napoknál nagyobb vagy alacsonyabb a várt forgalom.

Az ilyen típusú jelentések létrehozásakor a sávdiagramok vizuális jelzést adnak a jelentés által jelzett időszakra vonatkozóan napi szinten tapasztalt platform-specifikus igényről. Ezt úgy teheti meg, hogy megjeleníti a jelentés minden egyes napjához tartozó sávot. Ha például a "múlt hét" nevű időszakot választja, a oszlopdiagram hét sávval fog létrejönni. Az egyes sávok az adott napon tapasztalt találatok teljes számát jelzik.

A gráf bal oldali (y tengely) azt jelzi, hogy hány találat történt a megadott dátumon. Közvetlenül a gráf alatt (x tengely) a jelentésbe foglalt napokon a dátum (Format: éééé-hh-nn) felirat jelenik meg.

> [!TIP]
> Ha egy sáv fölé viszi az egérmutatót, az adott napon bekövetkezett találatok teljes száma elemleírásként jelenik meg.
> 
> 

A sávdiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt megtalálhatja a találatok teljes számát és az átvitt adatmennyiséget (GB-ban) a jelentés által lefedett napokon.

## <a name="by-hour"></a>Óránként
Az óránkénti jelentéssel megtekintheti a találatok és az adott platformon továbbított adatok óránkénti teljes számát. Ez az információ a CDN-tevékenység mintáinak gyors felismerésére használható. Ez a jelentés például segítséget nyújt a vártnál nagyobb vagy alacsonyabb időtartamú időszakok észlelésében a nap folyamán.

Az ilyen típusú jelentések létrehozásakor a sávdiagramok vizuális jelzést adnak a jelentés által jelzett időszakra vonatkozóan óradíjban tapasztalt platform-specifikus igényről. Ezt úgy teheti meg, hogy megjeleníti a jelentés által jelzett óránkénti sávot. Ha például egy 24 órás időszakot jelöl ki, akkor egy sávdiagram húsz négy sávval fog létrejönni. Az egyes sávok az adott órában tapasztalt találatok teljes számát jelzik.

A gráf bal oldali (y tengely) azt jelzi, hogy hány találat történt a megadott órában. Közvetlenül a gráf (x tengely) alatt megtalál egy címkét, amely a jelentésben szereplő minden órában megadja a dátumot és az időt (Format: éééé-hh-nn óó: PP). Az idő 24 órás formátummal van jelezve, és az UTC/GMT időzóna használatával van megadva.

> [!TIP]
> Ha egy sáv fölé viszi az egérmutatót, a rendszer elemleírásként jeleníti meg az adott órában bekövetkezett találatok teljes számát.
> 
> 

A sávdiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt megtalálhatja a találatok teljes számát és az átvitt adatmennyiséget (GB-ban) a jelentés által jelzett óránként.

## <a name="by-file"></a>Fájl szerint
A fájl alapján jelentéssel megtekintheti az igény mennyiségét, valamint egy adott platformon felmerülő forgalmat a leggyakrabban kért eszközökhöz. Az ilyen típusú jelentések létrehozásakor a rendszer egy sávdiagram-diagramot hoz létre az első 10 leggyakrabban kért eszközön a megadott időszakban.

> [!NOTE]
> A jelentés esetében a peremhálózati CNAME URL-címek az egyenértékű CDN URL-címekre lesznek konvertálva. Ez lehetővé teszi az eszközhöz kapcsolódó találatok teljes számának pontos megkeresését, a CDN vagy a Edge CNAME URL-címétől függetlenül.
> 
> 

A gráf (y tengely) bal oldalán az egyes eszközökre vonatkozó kérelmek száma látható a megadott időszakban. Közvetlenül a gráf (x tengely) alatt található egy címke, amely az első 10 kért eszköz fájlnevét jelzi.

A sávdiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt az alábbi információkat találhatja meg a legfontosabb 250 kért eszközökhöz: relatív elérési út, a találatok teljes száma, a találatok százalékos aránya, az átvitt adatok mennyisége (gigabájtban) és az átvitt adatok százalékos aránya.

## <a name="by-file-detail"></a>Fájl részletei szerint
A by file detail jelentés lehetővé teszi, hogy megtekintse az igény mennyiségét és egy adott eszköz adott platformján felmerülő forgalmat. A jelentés legfelső részén a fájl részleteit kell megkeresni. Ez a beállítás a kiválasztott platformon a leggyakrabban kért eszközök listáját tartalmazza. A fájlokra vonatkozó részletes jelentés létrehozásához ki kell választania a kívánt eszközt a fájl részletei közül a beállításnál. Ezután egy sávdiagram jelzi, hogy a megadott időszakban milyen napi keresletet generált a rendszer.

A gráf (y tengely) bal oldalán az adott napon tapasztalt adategységek teljes száma látható. Közvetlenül a gráf (x tengely) alatt megtalál egy címkét, amely azt a dátumot (Format: éééé-hh-nn) jelzi, amelynek a CDN iránti igényét jelentették.

A sávdiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt megtalálhatja a találatok teljes számát és az átvitt adatmennyiséget (GB-ban) a jelentés által lefedett napokon.

## <a name="by-file-type"></a>Fájl típusa szerint
A by file Type jelentés lehetővé teszi, hogy megtekintse az igénybe venni kívánt mennyiségét és a fájltípusban felmerülő forgalmat. Az ilyen típusú jelentések létrehozásakor a fánk diagramon a felső 10 fájltípus által generált találatok százalékos aránya látható.

> [!TIP]
> Ha a fánk diagramon egy szelet fölé viszi a kurzort, akkor az adott fájltípus internetes adathordozó-típusa elemleírásként jelenik meg.
> 
> 

A fánk diagram létrehozásához használt összes adattal megtekinthető. Itt megtalálja a fájlnévkiterjesztés/internetes adathordozó típusát, a találatok teljes számát, a találatok százalékos arányát, az átvitt adatmennyiséget (gigabájtban), valamint az egyes legfontosabb 250-fájltípusokhoz továbbított adatmennyiség százalékos arányát.

## <a name="by-directory"></a>Címtár szerint
A by Directory-jelentés lehetővé teszi, hogy megtekintse az igénybe venni kívánt mennyiségét és az adott platformon az adott címtárban felmerülő forgalmat. Az ilyen típusú jelentések létrehozásakor a sávdiagram az első 10 könyvtárban található tartalom által generált találatok teljes számát jelzi.

### <a name="using-the-bar-chart"></a>A oszlopdiagram használata
* Vigye az egérmutatót egy sáv fölé a megfelelő könyvtár relatív elérési útjának megtekintéséhez.
* A címtár egy almappájában tárolt tartalom nem számít az igény címtár szerinti kiszámításakor. Ez a számítás kizárólag az aktuális könyvtárban tárolt tartalomhoz generált kérelmek számán alapul.
* A jelentés esetében a peremhálózati CNAME URL-címek az egyenértékű CDN URL-címekre lesznek konvertálva. Ez lehetővé teszi az eszközhöz kapcsolódó összes statisztika pontos egyezését, a CDN vagy a Edge CNAME URL-címétől függetlenül.

A gráf (y tengely) bal oldalán az első 10 könyvtárban tárolt tartalomra vonatkozó kérelmek teljes száma látható. A diagram minden sávja a könyvtárat jelképezi. A színkódolási sémával összehasonlíthatja a sávokat a Top 250 teljes címtárak szakaszban felsorolt könyvtárral.

A sávdiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt az alábbi információk találhatók meg a leggyakoribb 250-címtárakhoz: relatív elérési út, a találatok teljes száma, a találatok százalékos aránya, az átvitt adatok mennyisége (gigabájtban) és az átvitt adatok százalékos aránya.

## <a name="by-browser"></a>Böngésző szerint
A by Browser jelentés lehetővé teszi, hogy megtekintse, mely böngészőket használták a tartalom kéréséhez. Az ilyen típusú jelentések létrehozásakor a tortadiagram az első 10 böngésző által kezelt kérelmek százalékos arányát jelzi.

### <a name="using-the-pie-chart"></a>A tortadiagram használata
* Vigye a kurzort egy szelet fölé a tortadiagramon a böngésző nevének és verziójának megtekintéséhez.
* A jelentés esetében minden egyes egyedi böngésző/verzió kombináció más böngészőnek tekintendő.
* A "other" nevű szelet az összes többi böngésző és verzió által kezelt kérelmek százalékos arányát jelzi.

A tortadiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt megtalálja a böngésző típusa/verziószáma, a találatok teljes számát és a találatok százalékos arányát a legfontosabb 250-böngészőknél.

## <a name="by-referrer"></a>Szponzor szerint
A by referrer jelentés lehetővé teszi, hogy megtekintse a legfontosabb hivatkozókat a kiválasztott platformon lévő tartalomhoz. A hivatkozó megadja azt az állomásnevet, amelyből a rendszer létrehozta a kérést. Az ilyen típusú jelentések létrehozásakor a sávdiagram az első 10 hivatkozó által generált igény (azaz találatok) mennyiségét jelzi.

A gráf (y tengely) bal oldalán a kérelmek teljes száma látható, amelyeket az adott eszköz az egyes hivatkozók számára észlelt. A diagram minden sávja egy hivatkozót jelöl. A színkódolási sémával egyeztetheti a sávokat a Top 250 referrer szakaszban felsorolt partnerekkel.

A sávdiagram létrehozásához használt összes adattal megtekintheti a diagramot. Itt megtalálhatja az URL-címet, a találatok teljes számát, valamint az egyes legfontosabb 250-hivatkozásokból generált találatok százalékos arányát.

## <a name="by-download"></a>Letöltéssel
A letöltési jelentés segítségével elemezheti a legtöbb kért tartalom letöltési mintáit. A jelentés tetején egy sávdiagram szerepel, amely összehasonlítja a megkísérelt letöltéseket a legelső 10 kért eszköz esetében. Az egyes sávok színkódoltak, attól függően, hogy megkísérelt-e a letöltés (kék) vagy egy befejezett Letöltés (zöld).

> [!NOTE]
> A jelentés esetében a peremhálózati CNAME URL-címek az egyenértékű CDN URL-címekre lesznek konvertálva. Ez lehetővé teszi az eszközhöz kapcsolódó összes statisztika pontos egyezését, a CDN vagy a Edge CNAME URL-címétől függetlenül.
> 
> 

A gráf bal oldali (y tengely) a 10 legfontosabb kért eszköz fájlnevét jelzi. Közvetlenül a gráf (x tengely) alatt található feliratok a megkísérelt/befejezett letöltések teljes számát jelzik.

Közvetlenül a sávdiagram alatt a következő információk szerepelnek a legfontosabb 250 kért eszközökhöz: relatív elérési út (beleértve a fájlnevet is), a befejezésének időpontját, a kért időpontok számát és a teljes letöltést eredményező kérelmek százalékos arányát.

> [!TIP]
> A CDN-t nem tájékoztatjuk egy HTTP-ügyfél (azaz böngésző), ha egy eszköz teljesen le lett töltve. Ennek eredményeképpen ki kell számítania, hogy egy eszköz teljesen le lett-e töltve az állapotkódok és a byte-Range kérelmek alapján. Ha ezt a számítást végzi, az első dolog, hogy a kérelem egy 200 OK-állapotkódot eredményez. Ha igen, akkor megvizsgáljuk a bájtok közötti kérelmeket, hogy azok a teljes eszközre kiterjedjenek. Végezetül összehasonlítjuk a kért adategység méretére továbbított adatmennyiséget. Ha az átvitt adatmennyiség egyenlő vagy nagyobb, mint a fájlméret, és a bájt-tartomány kérelmek megfelelnek az adott eszköznek, akkor a találatok teljes letöltésnek számítanak.
> 
> A jelentés értelmező jellege miatt érdemes szem előtt tartani a következő pontokat, amelyek megváltoztathatják a jelentés egységességét és pontosságát.
> 
> * A forgalmi mintákat nem lehet pontosan megjósolni, ha a felhasználói ügynökök másképp viselkednek. Ez a művelet a 100%-nál nagyobb befejezett letöltési eredményeket eredményezheti.
> * Előfordulhat, hogy a jelentés nem fogja pontosan megjeleníteni a HTTP progresszív letöltés előnyeit kihasználó eszközöket. Ennek oka az, hogy a felhasználók egy videóban különböző pozíciókat keresnek.
> 
> 

## <a name="by-404-errors"></a>404 hiba miatt
A by 404 errors jelentés lehetővé teszi annak a tartalomnak a meghatározását, amely a legtöbb 404 nem található állapotkódot hozza létre. A jelentés tetején található egy sávdiagram az első 10 olyan objektumhoz, amely esetében az 404 nem található állapotkód lett visszaadva. Ez a oszlopdiagram összehasonlítja a kérelmek teljes számát, amely a 404 nem talált állapotkódot eredményezett kérelmeket. Mindegyik sáv színkódolású. A sárga sáv azt jelzi, hogy a kérelem a 404 nem talált állapotkódot eredményezett. A rendszer egy vörös sáv használatával jelzi az eszközre vonatkozó kérelmek teljes számát.

> [!NOTE]
> A jelentés esetében vegye figyelembe a következőket:
> 
> * A találatok az adott eszközre vonatkozó összes kérelmet jelentik, tekintet nélkül az állapotkódot.
> * A peremhálózati CNAME URL-címek az egyenértékű CDN URL-címekre lesznek konvertálva. Ez lehetővé teszi az eszközhöz kapcsolódó összes statisztika pontos egyezését, a CDN vagy a Edge CNAME URL-címétől függetlenül.
> 
> 

A gráf (y tengely) bal oldalán a 404 nem talált állapotkódot eredményező első 10 kért eszköz fájlnevét adja meg. Közvetlenül a gráf (x tengely) alatt található a kérelmek teljes számát és a 404 nem talált állapotkódot eredményező kérelmek számát jelző címkék.

Közvetlenül a sávdiagram alatt a következő információk szerepelnek a legfontosabb 250 kért eszközökhöz: relatív elérési út (beleértve a fájlnevet), a 404 nem talált állapotkódot eredményező kérések száma, az eszköz kérelmének teljes száma, valamint a 404 nem talált állapotkódot eredményező kérelmek százalékos aránya.

## <a name="see-also"></a>Lásd még
* [Azure tartalomkézbesítési hálózat (CDN) – áttekintés](cdn-overview.md)
* [Microsoft Azure CDN valós idejű statisztikái](cdn-real-time-stats.md)
* [Az alapértelmezett HTTP-viselkedés felülbírálása a szabályok motor használatával](./cdn-verizon-premium-rules-engine.md)
* [Edge-teljesítmény elemzése](cdn-edge-performance.md)
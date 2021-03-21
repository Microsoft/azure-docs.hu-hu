---
title: Durva helyzet-újrameghatározás
description: Megtudhatja, hogyan és mikor kell használni a durva újrahonosítást. A durva újrahonosítás segít megtalálni a közeli horgonyokat.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656175"
---
# <a name="coarse-relocalization"></a>Durva helyzet-újrameghatározás

A durva újrahonosítás egy olyan szolgáltatás, amely nagy léptékű honosítást tesz lehetővé azáltal, hogy hozzávetőleges, de gyors választ biztosít ezekre a kérdésekre: 
- *Hol van most az eszközem?* 
- *Milyen tartalmat érdemes megfigyelni?* 
 
A válasz nem pontos. Ezen az űrlapon jelennek meg: *Ön ezen horgonyokhoz közeledik. Keresse meg az egyiket*.

A durva újrahonosítás a különböző, a gyors lekérdezéshez használatos, az eszközön lévő érzékelőkkel rendelkező, különféle szenzorokkal rendelkező adatolvasások címkézésével működik. A kültéri forgatókönyvek esetében az érzékelő adatok általában az eszköz GPS (globális helymeghatározási rendszer) pozíciója. Ha a GPS nem érhető el vagy nem megbízható, például ha bezárták, az érzékelő az Wi-Fi hozzáférési pontokból és a hatótávolságon belüli Bluetooth-figyelőből áll. Az összegyűjtött érzékelők adatai hozzájárulnak az Azure térbeli horgonyok által használt térbeli indexek fenntartásához, hogy gyorsan meghatározhassa, hogy mely horgonyok vannak az eszközhöz közeledve.

## <a name="when-to-use-coarse-relocalization"></a>Mikor használjon durva újrahonosítást

Ha több mint 35 térbeli horgonyt szeretne kezelni egy olyan térben, amely nagyobb, mint egy teniszpálya, akkor valószínűleg kihasználhatja a durva újrahonosítási térbeli indexelést.

A durva újrahonosítás által engedélyezett horgonyok gyors keresése úgy lett kialakítva, hogy leegyszerűsítse az alkalmazások globális gyűjteményekkel támogatott fejlesztését, azaz több millió földrajzilag elosztott horgonyt. A térbeli indexelés összetettsége minden rejtett, így az alkalmazás logikája is koncentrálhat. Az Azure térbeli horgonyai az összes nehéz munkát elvégzik a színfalak mögött.

## <a name="using-coarse-relocalization"></a>Durva újrahonosítás használata

Az alábbi tipikus munkafolyamat a durva újrahonosítással rendelkező Azure térbeli horgonyok létrehozásához és lekérdezéséhez használható:
1.  Hozzon létre és konfiguráljon egy érzékelő ujjlenyomat-szolgáltatót a kívánt Sensor-adatok gyűjtéséhez.
2.  Indítsa el az Azure térbeli horgonyok munkamenetét, és hozza létre a horgonyokat. Mivel az érzékelő ujjlenyomatának engedélyezése engedélyezve van, a rendszer a horgonyokat a durva újrahonosítással végzi.
3.  A térbeli horgonyok munkamenetben a dedikált keresési feltételek használatával a környező horgonyokat durva újrahonosítással keresheti meg.

Az alábbi oktatóanyagok egyikével megadhatja az alkalmazásban a durva újrahonosítást:
* [Durva újrahonosítás az egységben](../how-tos/set-up-coarse-reloc-unity.md)
* [Durva újrahonosítás az Objective-C-ben](../how-tos/set-up-coarse-reloc-objc.md)
* [Durva újrahonosítás a Swift-ben](../how-tos/set-up-coarse-reloc-swift.md)
* [Durva újrahonosítás Java-ban](../how-tos/set-up-coarse-reloc-java.md)
* [Durva újrahonosítás a C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Durva újrahonosítás a C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Érzékelők és platformok

### <a name="platform-availability"></a>Platform rendelkezésre állása

Ezeket az adattípusokat elküldheti a Anchor szolgáltatásnak:

* GPS-pozíció: szélesség, hosszúság, magasság
* Wi-Fi hozzáférési pontok jelerőssége a tartományon belül
* Bluetooth-figyelők jelerőssége a tartományon belül

Ez a táblázat összefoglalja az érzékelő által támogatott platformokon elérhető adatok rendelkezésre állását, és információt nyújt arról, hogy tisztában legyenek az alábbiakkal:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Nem<sup>1</sup>  | Igen<sup>2</sup> | Igen<sup>3</sup> |
| **Wi-Fi**        | Igen<sup>4</sup> | Igen<sup>5</sup> | No  |
| **Egyazon figyelő** | Igen<sup>6</sup> | Igen<sup>6</sup> | Igen<sup>6</sup>|


<sup>1</sup> külső GPS-eszköz társítható a HoloLens. Vegye fel [a kapcsolatot a támogatási szolgálattal](../spatial-anchor-support.md) , ha szeretné használni a HoloLens-t egy GPS Tracker használatával.<br/>
<sup>2</sup> támogatott a [LocationManager][3] API-kon keresztül (a GPS és a hálózat is).<br/>
<sup>3</sup> támogatott a [CLLocationManager][4] API-kon keresztül.<br/>
<sup>4</sup> minden 3 másodpercenként körülbelül egy vizsgálattal támogatott. <br/>
<sup>5</sup> a 28. API-val kezdődően a Wi-Fi vizsgálatok két percenként négy hívásra vannak szabályozva. Az Android 10 verziótól kezdődően letilthatja ezt a szabályozást a **fejlesztői beállítások** menüből. További információt az [Android dokumentációjában][5]talál.<br/>
<sup>6</sup> a [Eddystone][1] és a [iBeacon][2].

### <a name="which-sensor-to-enable"></a>Az engedélyezni kívánt érzékelő

Az érzékelő kiválasztása a fejlesztett alkalmazástól és a platformtól függ.
Ez az ábra egy kiindulási pontot biztosít annak meghatározásához, hogy mely érzékelők legyenek engedélyezve, a honosítási forgatókönyvtől függően:

![A különböző forgatókönyvekhez engedélyezett érzékelőket megjelenítő diagram.](media/coarse-relocalization-enabling-sensors.png)

A következő szakaszokban részletesebben ismertetjük az egyes érzékelők típusának előnyeit és korlátozásait.

### <a name="gps"></a>GPS

A GPS a kültéri forgatókönyvek esetében is elérhető.
Ha az alkalmazásban GPS-t használ, vegye figyelembe, hogy a hardver által megadott olvasások jellemzően a következők:

* Aszinkron és alacsony gyakoriságú (kevesebb mint 1 Hz).
* Megbízhatatlan/zajos (átlagosan 7 méteres szórás).

Általánosságban elmondható, hogy az eszköz operációs rendszere és a térbeli horgonyok is kiszűrik és kikövetkeztetik a nyers GPS-jelet a problémák enyhítésére tett kísérlet során. A további feldolgozáshoz idő szükséges a konvergenciához, ezért a legjobb eredmény érdekében a következőket kell tennie:

* A lehető leghamarabb hozzon létre egy érzékelő ujjlenyomat-szolgáltatót az alkalmazásban.
* Tartsa életben az érzékelő ujjlenyomat-szolgáltatóját több munkamenet között.
* Ossza meg az érzékelő ujjlenyomat-szolgáltatóját több munkamenet között.

A fogyasztói minőségű GPS-eszközök jellemzően nem pontosak. A [Zandenbergen és a Barbeau (2011)][6] által készített tanulmány azt jelenti, hogy a GPS-t (a-GPS-t) támogató mobiltelefonok medián pontossága körülbelül 7 méter. Ez elég nagy érték a mellőzéshez! Ezeknek a mérési hibáknak a kiszámításához a szolgáltatás a GPS-térben lévő valószínűségi Eloszlásként kezeli a horgonyokat. Tehát a horgony az a terület, amely a legvalószínűbb (több mint 95%-os megbízhatósággal) tartalmazza az igaz, ismeretlen GPS-pozíciót.

Ugyanez az indoklás a GPS használatával történő lekérdezés esetén is érvényes. Az eszköz egy másik térbeli megbízhatósági régióként jelenik meg a valódi, ismeretlen GPS-pozíciója körül. A közeli horgonyok felderítése lefordítja, hogy a megbízhatósági régiókban található horgonyok *elég közel* legyenek az eszköz bizalmas régiójához, ahogy az itt látható:

![Diagram, amely bemutatja a horgony jelöltek a GPS használatával való megtalálását.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

A HoloLens és az Android esetében Wi-Fi a jelerősség jó módszer lehet a beltéri, durva újrahonosítást is lehetővé tenni.
Az előny az Wi-Fi hozzáférési pontok (például az irodai és a bevásárlóközpontokban közös) azonnali rendelkezésre állásának lehetősége, amely nem igényel további telepítést.

> [!NOTE]
> az iOS nem biztosít API-t a Wi-Fi jelerősségének olvasásához, így a Wi-Fi-n keresztül engedélyezett durva újrahonosítás nem használható.

Ha az alkalmazásban Wi-Fi használ, vegye figyelembe, hogy a hardver által megadott olvasások jellemzően a következők:

* Aszinkron és alacsony gyakoriságú (kevesebb mint 0,1 Hz).
* Potenciálisan szabályozható az operációs rendszer szintjén.
* Megbízhatatlan/zajos (átlagosan 3 dBm standard szórás).

A térbeli horgonyok megpróbálják felépíteni Wi-Fi jelerősségű szűrt térképet egy munkamenet során a problémák enyhítésére tett kísérlet során. A legjobb eredmények eléréséhez próbálkozzon a következővel:

* Az első horgony elhelyezése előtt hozza létre a munkamenetet.
* Tartsa életben a munkamenetet, amíg lehetséges. (Azaz hozzon létre minden horgonyt és lekérdezést egy munkamenetben.)

### <a name="bluetooth-beacons"></a>Bluetooth-figyelők
<a name="beaconsDetails"></a>

A Bluetooth-figyelő gondos üzembe helyezése jó megoldás a nagyméretű beltéri, durva áttelepítési forgatókönyvek esetén, ahol a GPS hiányzik vagy pontatlan. Ez az egyetlen olyan beltéri módszer is, amelyet mindhárom platformon támogat.

A figyelők általában sokoldalú eszközök, amelyeken minden konfigurálható, beleértve az UUID-ket és a MAC-címeket is. Az Azure térbeli horgonyok a figyelőket a saját UUID-azonosítóik alapján egyedileg azonosítják. Ha nem biztosítja ezt az egyediséget, valószínűleg helytelen eredményt fog kapni. A legjobb eredmények érdekében:

* Rendeljen egyedi UUID-ket a figyelőhöz.
* A figyelőket olyan módon helyezheti üzembe, amely egységesen fedi le a tárhelyet, így legalább három figyelő elérhető bármely pontról a helyről.
* Adja át az egyedi Beacon UUID-azonosítók listáját az érzékelő ujjlenyomat-szolgáltatójának.

A hangjeleket, például a Bluetooth-jeleket érintik az akadályok, és zavarhatja a többi választógombot is. Így nehéz lehet kitalálni, hogy a tárhelye egységesen van-e lefoglalva. A jobb felhasználói élmény garantálása érdekében javasoljuk, hogy manuálisan tesztelje a figyelők lefedettségét. A tesztet elvégezheti a tárhelynek a tagjelölt eszközökkel való bejárásával, valamint egy olyan alkalmazással, amely a hatótávolságon belüli Bluetooth-t jeleníti A lefedettség tesztelése során győződjön meg arról, hogy legalább három figyelőt tud elérni a tárhely bármely stratégiai pozíciójában. A túl sok figyelővel több interferencia lehet, és nem feltétlenül javítja a durva újrahonosítás pontosságát.

A Bluetooth-figyelők jellemzően 80 métert fedik le, ha nincs akadály a térben.
Tehát egy olyan helyhez, amely nem rendelkezik nagyméretű akadályokkal, a figyelőket üzembe helyezhetjük egy rácsos mintában, 40 méteren.

Az akkumulátoron kívüli jeladók hatással vannak az eredményekre, ezért ügyeljen arra, hogy a központi telepítést időnként figyelje alacsony vagy nem feltöltött akkumulátorok esetén.

Az Azure térbeli horgonyok csak az ismert-Beacon közelségi UUID-i listán szereplő Bluetooth-figyelőket fogják követni. A allowlisted UUID-ket tartalmazó rosszindulatú figyelők azonban negatív hatással lehetnek a szolgáltatás minőségére. Így a legjobb eredményt kapja a kitalált területeken, ahol vezérelheti a Beacon üzembe helyezését.

### <a name="sensor-accuracy"></a>Érzékelő pontossága

A GPS-jel pontossága a horgony létrehozásakor és a lekérdezések során jelentős mértékben befolyásolja a visszaadott horgonyok készletét. Ezzel szemben a Wi-Fi/figyelőn alapuló lekérdezések az összes olyan horgonyt figyelembe veszik, amelynek legalább egy hozzáférési pontja/Beacon-je közös a lekérdezéssel. Ebben az értelemben a Wi-Fi/figyelőn alapuló lekérdezés eredményét többnyire a hozzáférési pontok/irányjelzők és a környezeti akadályok fizikai tartománya határozza meg.
Ez a tábla a várt keresési helyet becsüli meg az egyes érzékelők típusainál:

| Érzékelő      | Keresési terület sugara (hozzávetőleges) | Részletek |
|-------------|:-------:|---------|
| **GPS**         | 20 millió – 30 m | A GPS-bizonytalanság határozza meg más tényezők között. A jelentett számok a-GPS: 7 méteres mobil telefonok átlagos GPS-pontosságának becslése. |
| **Wi-Fi**        | 50 m – 100 m | A vezeték nélküli hozzáférési pontok tartománya határozza meg. A gyakoriságtól, az adó erősségtől, a fizikai akadályoktól, a beavatkozástól és így tovább függ. |
| **Egyazon figyelő** |  70 m | A jeladó tartománya határozza meg. A gyakoriságtól, az átviteli erősségtől, a fizikai akadályoktól, a beavatkozástól és egyebektől függ. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9

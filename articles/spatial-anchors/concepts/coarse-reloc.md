---
title: Durva újrahonosítás
description: Ismerje meg, hogyan használhat durva áthonosítást a közeli horgonyok megtalálásához.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071135"
---
# <a name="coarse-relocalization"></a>Durva helyzet-újrameghatározás

A durva átlokalizáció egy olyan szolgáltatás, amely lehetővé teszi a nagy léptékű honosítást azáltal, hogy hozzávetőleges, de gyors választ ad a kérdésre: *Hol van az eszközem, és milyen tartalmat kell megfigyelni?* A válasz nem pontos, hanem a következő formában van: a *horgonyokhoz közeledik; próbáljon meg az egyiket* megkeresni.

A durva újrahonosítás a különböző, a gyors lekérdezéshez használatos, az eszközön lévő érzékelőkkel rendelkező, különféle szenzorokkal rendelkező adatolvasások címkézésével működik. A kültéri forgatókönyvek esetében az érzékelő adatok általában az eszköz GPS (globális helymeghatározási rendszer) pozíciója. Ha a GPS nem érhető el vagy nem megbízható (például beltérben), az érzékelői információ a hatótávolságon belül található WiFi hozzáférési pontokból és Bluetooth-figyelőből áll. Az összegyűjtött érzékelők adatai hozzájárulnak az Azure térbeli horgonyok által használt térbeli indexek fenntartásához, hogy gyorsan meghatározhassa, hogy mely horgonyok vannak az eszköz közelében.

## <a name="when-to-use-coarse-relocalization"></a>Mikor használjon durva újrahonosítást

Ha több mint 35 térbeli horgonyt szeretne kezelni egy olyan helyen, amely nagyobb, mint egy teniszpálya, akkor a rendszer valószínűleg kihasználja a durva újrahonosítás térbeli indexelést.

A durva újrahonosítást használó horgonyok gyors kinézete úgy lett kialakítva, hogy leegyszerűsítse a globális gyűjteményekkel (több millió geo-terjesztésű) horgonysal támogatott alkalmazások fejlesztését. A térbeli indexelés összetettsége teljesen el van rejtve, így az alkalmazás logikára koncentrálhat. Az Azure térbeli horgonyok a színfalak mögött is megtalálhatók.

## <a name="using-coarse-relocalization"></a>Durva újrahonosítás használata

Az Azure térbeli horgonyok durva újrahonosítással való létrehozásának és lekérdezésének tipikus munkafolyamata a következő:
1.  Hozzon létre és konfiguráljon egy érzékelő ujjlenyomat-szolgáltatót az Ön által választott érzékelők adatainak gyűjtéséhez.
2.  Indítson el egy Azure térbeli horgony-munkamenetet, és hozzon létre horgonyokat. Mivel az érzékelő ujjlenyomatának engedélyezése engedélyezve van, a rendszer a horgonyokat a durva újrahonosítással végzi.
3.  Az Azure térbeli Anchor-munkamenetben a dedikált keresési feltételek használatával lekérdezheti a környező horgonyokat durva újrahonosítással.

A következő oktatóanyagban megtekintheti, hogyan állíthatja be a durva újrahonosítást az alkalmazásban:
* [Durva újrahonosítás az egységben](../how-tos/set-up-coarse-reloc-unity.md)
* [Durva újrahonosítás az Objective-C-ben](../how-tos/set-up-coarse-reloc-objc.md)
* [Durva újrahonosítás a Swift-ben](../how-tos/set-up-coarse-reloc-swift.md)
* [Durva újrahonosítás Java-ban](../how-tos/set-up-coarse-reloc-java.md)
* [Durva újrahonosítás a C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Durva újrahonosítás a C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Érzékelők és platformok

### <a name="platform-availability"></a>Platform rendelkezésre állása

A horgony szolgáltatásnak elküldhető szenzor-adattípusok a következők:

* GPS-pozíció: szélesség, hosszúság, magasság.
* A WiFi-hozzáférési pontok jelerőssége a tartományon belül.
* A hatótávolságon belül a Bluetooth-figyelő jelerőssége.

Az alábbi táblázat összefoglalja a támogatott platformokon elérhető érzékelők adatait, valamint a platformra vonatkozó kikötéseket:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | NEM<sup>1</sup>  | IGEN<sup>2</sup> | IGEN<sup>3</sup> |
| **Fi**        | IGEN<sup>4</sup> | IGEN<sup>5</sup> | NO  |
| **Egyazon figyelő** | IGEN<sup>6</sup> | IGEN<sup>6</sup> | IGEN<sup>6</sup>|


<sup>1</sup> külső GPS-eszköz társítható a HoloLens. Ha szeretné, hogy a HoloLens a GPS-Tracker használatával használhassa, forduljon [a támogatási szolgálathoz](../spatial-anchor-support.md) .<br/>
<sup>2</sup> támogatott [LocationManager][3] API-kon keresztül (a GPS és a hálózat is)<br/>
<sup>3</sup> támogatott a [CLLocationManager][4] API-kon keresztül<br/>
<sup>4</sup> minden 3 másodpercenként körülbelül egy vizsgálatra támogatott <br/>
<sup>5</sup> az API 28. szintjétől kezdve a Wi-Fi vizsgálat 2 percenként 4 hívásra van szabályozva. Az Android 10-es verzióban a szabályozás le lehet tiltani a fejlesztői beállítások menüből. További információt az [Android dokumentációjában][5]talál.<br/>
<sup>6</sup> [Eddystone][1] és [iBeacon][2]

### <a name="which-sensor-to-enable"></a>Az engedélyezni kívánt érzékelő

Az érzékelő kiválasztása a fejlesztés alatt álló alkalmazásra és a platformra jellemző.
A következő ábra egy kiindulási pontot tartalmaz, amelyen az érzékelők kombinációja a honosítási forgatókönyvtől függően engedélyezhető:

![Az engedélyezett érzékelők kiválasztásának ábrája](media/coarse-relocalization-enabling-sensors.png)

A következő fejezetekben részletesebben tájékozódhat az egyes érzékelők típusaival kapcsolatos előnyökről és korlátozásokról.

### <a name="gps"></a>GPS

A GPS a kültéri forgatókönyvek esetében is elérhető.
Ha a GPS-t az alkalmazásban használja, vegye figyelembe, hogy a hardver által megadott olvasások jellemzően a következők:

* aszinkron és alacsony gyakoriságú (kevesebb mint 1 Hz).
* megbízhatatlan/zajos (átlagosan 7 méteres szórás).

Általánosságban elmondható, hogy az eszköz operációs rendszere és az Azure térbeli horgonyai is kiszűrik és kikövetkeztetik a nyers GPS-jelet a problémák enyhítésére tett kísérlet során. A további feldolgozáshoz idő szükséges a konvergenciához, ezért a legjobb eredmények érdekében a következőket kell tennie:

* a lehető leghamarabb hozzon létre egy érzékelő ujjlenyomat-szolgáltatót az alkalmazásban
* az érzékelő ujjlenyomat-szolgáltatójának életben tartása több munkamenet között
* az érzékelő ujjlenyomat-szolgáltatójának megosztása több munkamenet között

A fogyasztói minőségű GPS-eszközök jellemzően nem pontosak. A [Zandenbergen és Barbeau (2011)][6] által készített tanulmány a mobil telefonok közepes pontosságát jelenti a támogatott GPS-vel (A-GPS) körülbelül 7 méterre – elég nagy értéket kell figyelmen kívül hagyni! Ezeknek a mérési hibáknak a kiszámításához a szolgáltatás a GPS-térben a valószínűségi Eloszlásként kezeli a horgonyokat. Ennek megfelelően a horgony mostantól a legnagyobb valószínűséggel (azaz a több mint 95%-os megbízhatósággal rendelkező) területnek az igaz, ismeretlen GPS-pozícióját tartalmazza.

Ugyanezt az indoklást alkalmazza a rendszer a GPS lekérdezése során. Az eszköz egy másik térbeli megbízhatósági régióként jelenik meg a valódi, ismeretlen GPS-pozíciója körül. A közeli horgonyok felderítésével a rendszer egyszerűen megkeresi a megbízhatósági *régiókban található* horgonyokat, az alábbi képen látható módon:

![Horgony jelöltek kiválasztása GPS-sel](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>Fi

A HoloLens és az Android rendszeren a WiFi jel erőssége kiváló megoldás lehet a beltéri, durva áttelepítéshez.
Ennek előnye, hogy a WiFi hozzáférési pontok (például az Office-helyek és a bevásárlóközpontok) azonnali elérhetősége további beállítás nélkül történik.

> [!NOTE]
> az iOS nem biztosít olyan API-t, amely a WiFi-jel erősségét olvassa, és így nem használható a WiFi-kompatibilis, durva újrahonosításhoz.

Ha az alkalmazásban Wi-Fi-t használ, vegye figyelembe, hogy a hardver által megadott olvasások jellemzően a következők:

* aszinkron és alacsony gyakoriságú (kevesebb mint 0,1 Hz).
* potenciálisan szabályozható az operációs rendszer szintjén.
* megbízhatatlan/zajos (átlagosan 3 dBm standard szórás).

Az Azure térbeli horgonyok megkísérlik kiépíteni a szűrt WiFi jel erősségének leképezését egy munkamenet során a problémák enyhítésére tett kísérlet során. A legjobb eredmények érdekében a következőket kell megpróbálkoznia:

* az első horgony elhelyezése előtt hozza létre a munkamenetet.
* tartsa életben a munkamenetet a lehető leghosszabbra (azaz hozzon létre minden horgonyt és lekérdezést egy munkamenetben).

### <a name="bluetooth-beacons"></a>Bluetooth-figyelők
<a name="beaconsDetails"></a>

A Bluetooth-figyelők körültekintő üzembe helyezése jó megoldás a nagyméretű, nagy méretű beltéri durva áttelepítési forgatókönyvek esetén, ahol a GPS hiányzik vagy pontatlan. Ez az egyetlen olyan beltéri módszer is, amely mindhárom platformon támogatott.

A figyelők általában sokoldalú eszközök, ahol minden – beleértve az UUID-ket és a MAC-címeket – is konfigurálható. Az Azure térbeli horgonyok a figyelőket a saját UUID-azonosítóik alapján egyedileg azonosítják. Ha nem biztos benne, hogy ez az egyediség biztosan helytelen eredményeket eredményez. A legjobb eredmények érdekében a következőket kell tennie:

* rendeljen egyedi UUID-ket a figyelőhöz.
* azokat olyan módon helyezheti üzembe, amely egységesen fedi le a tárhelyet, így legalább 3 figyelő elérhető bármely pontról a helyről.
* adja át az egyedi Beacon UUID-azonosítók listáját az érzékelő ujjlenyomat-szolgáltatójának

A hangjeleket, például a Bluetooth-jeleket érintik az akadályok, és más jeleket is képesek befolyásolni. Ezen okok miatt nehéz lehet kitalálni, hogy a tárhelyet egységesen fedi-e le. A jobb felhasználói élmény garantálása érdekében javasoljuk, hogy manuálisan tesztelje a figyelőket. Ezt úgy teheti meg, hogy a helyet a tagjelölt eszközökkel és egy, a hatótávolságon belüli Bluetooth-t megjelenítő alkalmazással helyezi el. A lefedettség tesztelése során győződjön meg arról, hogy legalább 3 figyelőt tud elérni a tárhely bármely stratégiai pozíciójában. A túl sok figyelő beállítása nagyobb interferenciát eredményezhet, és nem feltétlenül javítja a durva újrahonosítási pontosságot.

A Bluetooth-figyelőknek jellemzően 80 méteres lefedettséggel kell rendelkezniük, ha nincsenek akadályok a térben.
Ez azt jelenti, hogy egy olyan helyre, amely nem tartalmaz nagy akadályokat, a figyelőket üzembe helyezhetik egy rácsos mintában, 40 méteren.

Az akkumulátoron kívüli irányjelzők negatívan érintik az eredményeket, ezért ügyeljen arra, hogy a rendszer rendszeres időközönként figyelje az alacsony vagy a Holt elemeket.

Az Azure térbeli horgonyok csak azokat a Bluetooth-figyelőket fogják követni, amelyek az ismert Beacon közelségi UUID-listán vannak. Azok a rosszindulatú figyelők, amelyeken engedélyezve van az Allow-listák, negatív hatással lehetnek a szolgáltatás minőségére. Emiatt a legjobb eredményeket fogja elérni a kihelyezett tárhelyeken, ahol szabályozhatja az üzembe helyezést.

### <a name="sensors-accuracy"></a>Érzékelők pontossága

A GPS-jel pontossága, a horgonyok létrehozásán és a lekérdezések során is nagy hatással van a visszaadott horgonyok készletére. Ezzel szemben a WiFi/figyelőn alapuló lekérdezések figyelembe veszik az összes olyan horgonyt, amelynek legalább egy hozzáférési pontja/Beacon-je közös a lekérdezéssel. Ebben az értelemben a Wi-Fi/figyelőn alapuló lekérdezések eredményét többnyire a hozzáférési pontok/figyelők fizikai tartománya, valamint a környezeti akadályok határozzák meg.
Az alábbi táblázat a várt keresési helyet becsüli meg az egyes érzékelők típusainál:

| Érzékelő      | Keresési terület sugara (kb.) | Részletek |
|-------------|:-------:|---------|
| GPS         | 20 m – 30 m | A többi tényező között a GPS-bizonytalanság határozza meg. A jelentett számok a-GPS-vel ellátott mobiltelefonok medián GPS-pontosságának becslése, amely 7 méter. |
| Fi        | 50 m – 100 m | A vezeték nélküli hozzáférési pontok tartománya határozza meg. A gyakoriságtól, az adó erősségtől, a fizikai akadályoktól, a beavatkozástól és így tovább függ. |
| Egyazon figyelő |  70 m | A jeladó tartománya határozza meg. A gyakoriságtól, az átviteli erősségtől, a fizikai akadályoktól, a beavatkozástól és egyebektől függ. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9

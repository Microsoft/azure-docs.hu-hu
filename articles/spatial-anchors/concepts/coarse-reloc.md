---
title: Durva helyzet-újrameghatározás
description: Megtudhatja, hogyan és mikor használjon durva újraszámítást. A durva újralokalizálás segít megtalálni a közelében lévő horgonyokat.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: d2737f58fa95d1aa45d9952e8b501c1b9be4d1b0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600352"
---
# <a name="coarse-relocalization"></a>Durva helyzet-újrameghatározás

A durva újralokalizálás egy olyan funkció, amely lehetővé teszi a nagy léptékű honosítást azáltal, hogy hozzávetőleges, de gyors választ ad ezekre a kérdésekre: 
- *Hol van az eszközöm?* 
- *Milyen tartalmat kell megfigyelni?* 
 
A válasz nem pontos. Ebben a formában: Közel áll ezekhez a *horgonyokhoz. Próbálja meg megkeresni az egyiket.*

A durva újraméretezés úgy működik, hogy a horgonyokat különböző, később a gyors lekérdezéshez használt, az eszközön található érzékelőkre vonatkozó adatokkal címkézi. A kültéri forgatókönyvekben az érzékelőadatok általában az eszköz GPS-ének (Globális helymegadó rendszer) pozícióját használják. Ha a GPS nem érhető el vagy megbízhatatlan, például ha beltéri, az érzékelő adatai az Wi-Fi hozzáférési pontokból és Bluetooth-adatgyűjtő jelekből állnak, amelyek tartományon belül vannak. Az összegyűjtött érzékelőadatok hozzájárulnak az Azure Spatial Anchors által használt térbeli index fenntartásához, amellyel gyorsan meghatározható, hogy mely horgonyok egymáshoz közel vannak az eszközhöz.

## <a name="when-to-use-coarse-relocalization"></a>Mikor kell durva újralokalizálást használni?

Ha több mint 35 térbeli horgonyt tervez kezelni egy olyan térben, amely nagyobb, mint egy tárgyalóterm, valószínűleg hasznát fogja látni a durva újralokalizációs térindexelésnek.

A coarse relocalization által lehetővé tett horgonyok gyors keresésének célja, hogy leegyszerűsítse a világméretű, például több millió geo elosztott horgonyból áll gyűjteményre alapozható alkalmazások fejlesztését. A térbeli indexelés összetettsége rejtve van, így ön az alkalmazáslogikákra összpontosíthat. Minden nehéz munkát a háttérben végez az Azure Spatial Anchors.

## <a name="using-coarse-relocalization"></a>Durva újraszámítás használata

Az Azure-erőforrások létrehozása és lekérdezése jellemzően a következő Spatial Anchors újralokalizálással:
1.  Érzékelő ujjlenyomat-szolgáltatójának létrehozása és konfigurálása a kívánt érzékelőadatok gyűjtéséhez.
2.  Indítson el egy Azure Spatial Anchors munkamenetet, és hozza létre a horgonyokat. Mivel az érzékelő ujjlenyomat-használata engedélyezve van, a horgonyok térbeli indexelése durva újralokalizálással.
3.  A horgonyokat körülvevő lekérdezések durva újraméretezéssel, az adott munkamenetben a dedikált keresési feltételek Spatial Anchors használatával.

Az alábbi oktatóanyagok egyikében állíthatja be a durva újraméretezést az alkalmazásban:
* [Durva újraméretezés a Unityben](../how-tos/set-up-coarse-reloc-unity.md)
* [Durva újraméretezés az Objective-C-ben](../how-tos/set-up-coarse-reloc-objc.md)
* [Durva újraméretezés a Swiftben](../how-tos/set-up-coarse-reloc-swift.md)
* [Durva újralokalizálás Java nyelven](../how-tos/set-up-coarse-reloc-java.md)
* [Durva újrahelyezés a C++/NDK-ban](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Durva újrahelyezés a C++/WinRT-ben](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Érzékelők és platformok

### <a name="platform-availability"></a>Platform rendelkezésre állása

Ezeket az érzékelőadatokat elküldheti a horgonyszolgáltatásnak:

* GPS-helyzet: szélesség, hosszúság, magasság
* A Wi-Fi hozzáférési pontok jelerősségét
* Bluetooth-jelek jelerősségének tartományon belül

Ez a táblázat összefoglalja az érzékelők adatainak elérhetőségét a támogatott platformokon, és a következő információkat tartalmazza:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **Gps**         | Nem<sup>1</sup>  | Igen<sup>2</sup> | Igen<sup>3</sup> |
| **Wi-Fi**        | Igen<sup>4</sup> | Igen<sup>5</sup> | No  |
| **BLE-jelek** | Igen<sup>6</sup> | Igen<sup>6</sup> | Igen<sup>6</sup>|


<sup>1</sup> Külső GPS-eszköz társítható a HoloLenshez. Ha [szívesen használna](../spatial-anchor-support.md) a HoloLenst GPS-követővel, lépjen kapcsolatba a támogatási szolgálattal.<br/>
<sup>2</sup> A [LocationManager][3] API-kon keresztül támogatott (GPS és NETWORK is).<br/>
<sup>3.</sup> A [CLLocationManager API-kon][4] keresztül támogatott.<br/>
<sup>4</sup> 3 másodpercenként körülbelül egy vizsgálat esetén támogatott. <br/>
<sup>5</sup> A 28. API-szinttől kezdődően a Wi-Fi 2 percenként négy hívásra vannak leszűkve. Az Android 10-től kezdődően ezt a szabályozást a Fejlesztői beállítások **menüből tilthatja** le. További információt az [Android dokumentációjában talál.][5]<br/>
<sup>6</sup> [Eddystone-ra és][1] [iBeacon-ra korlátozva.][2]

### <a name="which-sensor-to-enable"></a>Melyik érzékelőt kell engedélyezni?

Az érzékelő megválasztása a fejlesztésen és a platformon múlik.
Ez az ábra kiindulási pontot biztosít annak meghatározásához, hogy a honosítási forgatókönyvtől függően melyik érzékelőkombináció engedélyezhető:

![Diagram a különböző forgatókönyvek engedélyezett érzékelőiről.](media/coarse-relocalization-enabling-sensors.png)

A következő szakaszok további betekintést nyújtanak az egyes érzékelőtípusok előnyeibe és korlátaiba.

### <a name="gps"></a>Gps

A GPS a szabadban történő használatra vonatkozó lehetőség.
Ha GPS-t használ az alkalmazásban, ne feledje, hogy a hardver által biztosított adatok általában a következő adatok:

* Aszinkron és alacsony gyakoriság (1 Hz-nél kisebb).
* Megbízhatatlan/zajos (átlagosan 7 m szórás).

Általánosságban elmondható, hogy az eszköz operációs Spatial Anchors a nyers GPS-jel szűrését és extrapolálását is el fogja látni a problémák enyhítése érdekében. A további feldolgozáshoz időre van szükség a konvergenciához, ezért a legjobb eredmény érdekében a következőt kell megpróbálni:

* Hozzon létre egy érzékelő ujjlenyomat-szolgáltatót a lehető leghamarabb az alkalmazásban.
* Tartsa az érzékelő ujjlenyomat-szolgáltatóját több munkamenet között.
* Az érzékelő ujjlenyomat-szolgáltatójának megosztása több munkamenet között.

A fogyasztói szintű GPS-eszközök általában nem jól használhatók. Zandosságen és [Barbosság (2011)][6] egyik tanulmánya szerint a GPS-t (A-GPS) használó mobiltelefonok medián pontossága körülbelül 7 méter. Ez egy elég nagy érték, amit figyelmen kívül kell hagyni! A mérési hibák figyelembe vétele érdekében a szolgáltatás a horgonyokat a GPS-tér valószínűségi eloszlásaként kezeli. Tehát a horgony az a terület régiója, ahol a legvalószínűbb (több mint 95%-os megbízhatósággal) a valódi, ismeretlen GPS-pozícióját tartalmazza.

Ugyanez az érvelés érvényes a GPS-sel való lekérdezésre is. Az eszköz egy másik térbeli megbízhatósági régióként van ábrázolva a valódi, ismeretlen GPS-pozíciójához. A közeli horgonyok felfedezése azt jelenti, hogy  az eszköz megbízhatósági régiójához elég közeli megbízhatósági régióval találja meg a horgonyokat, ahogy az alábbi ábrán látható:

![Diagram, amely bemutatja, hogyan lehet megtalálni a horgonyra jelölteket GPS használatával.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

HoloLensen és Androidon Wi-Fi jelerősség jó módja lehet a beltéri durva újraméretezés engedélyezésének.
Ennek az az előnye, hogy Wi-Fi hozzáférési pontok (például az irodai terekben és a bevásárlókban gyakoriak) azonnali rendelkezésre állása további beállítások nélkül.

> [!NOTE]
> Az iOS nem biztosít API-t a jelerősség Wi-Fi olvasásához, így nem használható a Wi-Fi-n keresztül engedélyezett durva újraméretezéshez.

Ha alkalmazásában Wi-Fi használ, ne feledje, hogy a hardver által biztosított adatok általában a következő adatok:

* Aszinkron és kis gyakoriságú (0,1 Hz-nél kisebb).
* Lehetséges, hogy az operációs rendszer szintjén van szabályozás alatt.
* Megbízhatatlan/zajos (átlagosan 3-dBm szórás).

Spatial Anchors megpróbál szűrt térképet összeépíteni a Wi-Fi jelerősségről egy munkamenet során, hogy megpróbálja enyhíteni ezeket a problémákat. A legjobb eredmény érdekében próbálja meg a következőt:

* Az első horgony létrehozása előtt hozza létre a munkamenetet.
* A munkamenetet addig tartsa életben, amíg csak lehetséges. (Ez azt jelenti, hogy egyetlen munkamenetben hozza létre az összes horgonyt és lekérdezést.)

### <a name="bluetooth-beacons"></a>Bluetooth-jeljelzők
<a name="beaconsDetails"></a>

A Bluetooth-jeljelzők gondos üzembe helyezése jó megoldás nagy léptékű beltéri coarse-relokalizációs forgatókönyvekhez, ahol a GPS hiányzik vagy pontatlan. Emellett ez az egyetlen beltéri módszer, amely mindhárom platformon támogatott.

A jelzők jellemzően sokoldalú eszközök, amelyeken minden konfigurálható, beleértve az UUID-ket és a MAC-címeket is. Az Azure Spatial Anchors azt várja, hogy a jelgyűjtő jelek egyedileg azonosíthatók az UUID-jük alapján. Ha nem biztosítja ezt az egyediséget, valószínűleg helytelen eredményeket fog kapni. A legjobb eredmény érdekében:

* Egyedi UUID-ket rendelhet az adatgyűjtő jelekhez.
* A jeljelzőket úgy helyezheti üzembe, hogy egységesen fedje le a teret, és hogy legalább három adatgyűjtő jel a világűr bármely pontjáról elérhető legyen.
* Adja át az egyedi jelgyűjtő UUID-ket az érzékelő ujjlenyomat-szolgáltatójának.

A Bluetooth-jelekhez hasonló választójelekre akadály van hatással, és befolyásolhatják a többi választójelet. Így nehéz lehet kitalálni, hogy a tér egységesen fedve van-e. A jobb felhasználói élmény biztosítása érdekében javasoljuk, hogy manuálisan tesztelje az adatgyűjtő jelek lefedettségét. A teszteket úgy végezheti el, hogy körbeveszi a teret a jelölt eszközökkel és egy olyan alkalmazással, amely a Bluetooth-tartományt mutatja. A lefedettség tesztelése közben győződjön meg arról, hogy legalább három adatgyűjtő jelet el tud érni a térben bármilyen stratégiai pozícióból. A túl sok adatgyűjtő jel nagyobb interferenciát eredményezhet közöttük, és nem feltétlenül javítja a durva újraméretezés pontosságát.

A Bluetooth-jeladók általában 80 métert fednek le, ha nincs akadály a térben.
Így egy olyan térhez, amely nem rendelkezik nagy akadályokkal, adatgyűjtő jelek helyezhetők üzembe egy rácsmintában 40 méterenként.

Az akkumulátorból hamarosan eltöltött adatgyűjtő jelek hatással vannak az eredményekre, ezért rendszeresen figyelje az üzembe helyezést alacsony vagy nem töltött akkumulátorok esetén.

Az Azure Spatial Anchors csak azokat a Bluetooth-jeljelzőket fogja nyomon követni, amelyek az ismert közelségi UUID-listában vannak. Az engedélyezőlistára bevont UUID-ket beprogramozó kártevő kártevők azonban negatívan befolyásolhatják a szolgáltatás minőségét. Így a legjobb eredményeket olyan összecsukott terekben fogja kapni, ahol vezérelheti az adatgyűjtő jelek üzembe helyezését.

### <a name="sensor-accuracy"></a>Érzékelő pontossága

A GPS-jel pontossága a horgonyok létrehozása során és a lekérdezések során is jelentős hatással van a visszaadott horgonyok készletére. Ezzel szemben a Wi-Fi-n/adatgyűjtő jeleken alapuló lekérdezések minden olyan horgonyt figyelembe fognak venni, amely a lekérdezésben közös hozzáférési ponttal/adatgyűjtő jelekkel rendelkezik. Ebben az értelemben a Wi-Fi-n/adatgyűjtő jeleken alapuló lekérdezések eredményét főleg a hozzáférési pontok/adatgyűjtő jelek és a környezeti adatgyűjtő jelek fizikai tartománya határozza meg.
Ez a táblázat az egyes érzékelőtípusokkal várható keresési területet becsüli meg:

| Érzékelő      | Keresési terület sugara (hozzávetőleges) | Részletek |
|-------------|:-------:|---------|
| **Gps**         | 20 m és 30 m között | Többek között a GPS-bizonytalanság határozza meg. A jelentett számok becsült értéke a mobiltelefonok GPS-pontosságának mediánja, 7 méter. |
| **Wi-Fi**        | 50 m és 100 m között | A vezeték nélküli hozzáférési pontok tartománya határozza meg. Függ a gyakoriságtól, a készülék erősségétől, a fizikai sűrűségtől, az interferenciától stb. |
| **BLE-jelek** |  70 m | Az adatgyűjtő jel tartománya határozza meg. Függ a gyakoriságtól, az átvitel erősségétől, a fizikai zavartól, az interferenciától stb. |

<!-- Reference links in article -->
[1]: https://developer.estimote.com/eddystone/
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9

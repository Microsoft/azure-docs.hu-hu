---
title: StorSimple-figyelési mutatók | Microsoft Docs
description: A StorSimple-eszköz állapotának figyelésére szolgáló fénykibocsátó diódákat (LED-eket) és hallható riasztásokat ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015400"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>StorSimple-figyelési mutatók használata az eszköz kezeléséhez


## <a name="overview"></a>Áttekintés
A StorSimple-eszköz olyan fénykibocsátó diódákat (LED-eket) és riasztásokat tartalmaz, amelyeket a StorSimple-eszköz moduljainak és általános állapotának figyelésére használhat. A figyelési mutatók az eszköz elsődleges bekerítésének és a EBOD ház hardveres összetevőin találhatók. A figyelési mutatók lehetnek LED-ek vagy hangos riasztások.

A modul állapota három LED-es állapottal jelezhető: zöld, zöld, piros-sárga vagy piros-sárga.  

* A zöld LED-ek kifogástalan működési állapotot jelentenek.  
* A zöldről vörösre villogó LED-ek jelzik a nem kritikus feltételek jelenlétét, amelyek felhasználói beavatkozást igényelhetnek.  
* A Red-Amber LED jelzi, hogy a modulban kritikus hiba van.  

A cikk további részében a különböző monitorozási mutatók, azok helyei a StorSimple-eszközön, az eszköz állapota a LED állapotok alapján, valamint a hozzájuk tartozó hallható riasztások.

## <a name="front-panel-indicator-leds"></a>Előlap kijelző LED-EK
Az előlap, más néven operatív *panel* vagy *Ops panel*, megjeleníti a rendszeren lévő összes modul összesített állapotát. Az előlap megegyezik a StorSimple elsődleges és a EBOD bekerítésével, és az alábbi ábrán látható.  

   ![Eszköz elülső panelje][1]

Az előlapon a következő mutatók láthatók:  

1. Némítás gomb
2. Power Indicator LED (zöld/piros-sárga)
3. Modul hibás kijelzője LED (piros-Amber/OFF)
4. Logikai hiba jelzője LED (piros-sárga/ki
5. Egység AZONOSÍTÓjának megjelenítése  

Az eszköz és a EBOD ház előlapi LED-je közötti fő különbség a **rendszeregység azonosítójának** a LED-ben megjelenített száma. Az eszközön megjelenő alapértelmezett azonosító a **00**, míg a EBOD ház alapértelmezett azonosítójának értéke **01**. Ez lehetővé teszi, hogy az eszköz bekapcsolásakor gyorsan megkülönböztethető legyen az eszköz és a EBOD ház. Ha az eszköz ki van kapcsolva, használja az [új eszköz bekapcsolásával](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) , hogy megkülönböztesse az eszközt az EBOD házban.  

## <a name="front-panel-led-status"></a>Előlapi LED állapota
Az alábbi táblázat segítségével azonosíthatja a LED-ek által jelzett állapotot az eszköz vagy a EBOD-ház előlapján.  

| Rendszerteljesítmény | Modul hibája | Logikai hiba | Riasztások | Állapot |
| --- | --- | --- | --- | --- |
| Vörös – sárga |KI |KI |N/A |Az AC-áramkimaradás, a biztonsági mentési teljesítmény vagy az AC-bekapcsolás, valamint a vezérlő modulok eltávolítása megtörtént. |
| Zöld |ON |ON |N/A |Ops-panel bekapcsolása (5 mp) – tesztelési állapot |
| Zöld |KI |KI |N/A |Bekapcsolás, minden funkció jó |
| Zöld |ON |N/A |PCM-hibák, ventilátorok meghibásodási LED-EK |Bármilyen PCM-hiba, ventilátor meghibásodása, hőmérséklet felett vagy alatt |
| Zöld |ON |N/A |I/O-modul LED-EK |Bármely vezérlő modul hibája |
| Zöld |ON |N.A. |N.A. |Bekerítés logikai hibája |
| Zöld |Flash |N/A |A modul állapota a vezérlő modulon vezetett. PCM-hibák, ventilátorok meghibásodási LED-EK |Ismeretlen a vezérlő modul típusa telepítve, az I2C Bus meghibásodása, a vezérlő modul létfontosságú termék-(VPD-) konfigurációs hibája |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Power hűtési modul (PCM) kijelző LED-EK
Az energiagazdálkodási modul (PCM) kijelző LED-ek az elsődleges bekerítés vagy a EBOD hátoldalán találhatók minden PCM-modulon. Ez a témakör azt ismerteti, hogyan használható a következő LED-ek a StorSimple-eszköz állapotának figyelésére.  

* PCM-LED-ek az elsődleges ház számára
* A EBOD-ház PCM-LED-je

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM-LED-ek az elsődleges ház számára
A StorSimple-eszközhöz egy 764W PCM-modul tartozik, amely egy további akkumulátort tartalmaz. Az alábbi ábrán az eszköz LED panelje látható.  

   ![PCM-LED-ek az elsődleges házban][2]

LED jelmagyarázata:

1. AC áramszünet
2. Ventilátor meghibásodása
3. Akkumulátor meghibásodása
4. PCM OK
5. TARTOMÁNYVEZÉRLŐi hiba
6. Akkumulátor jó  

A PCM állapota a LED panelen van megjelölve. Az eszköz PCM LED-paneljének hat LED-je van. Ezek közül négy LED a tápegység és a ventilátor állapotát jeleníti meg. A fennmaradó két LED a PCM biztonsági mentési akkumulátor moduljának állapotát jelzi. A PCM állapotának meghatározásához a következő táblázatok használhatók.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM kijelző LED-ek a tápegységhez és a ventilátorhoz
| Állapot | PCM OK (zöld) | AC sikertelen (Amber) | Ventilátor sikertelen (sárga) | TARTOMÁNYVEZÉRLŐi hiba (Amber) |
| --- | --- | --- | --- | --- |
| Nincs AC Power (bekerítés) |KI |KI |KI |KI |
| Nincs AC-teljesítmény (csak ez a PCM) |KI |ON |KI |ON |
| AC jelen PCM ON-OK |ON |KI |KI |KI |
| PCM-hiba (ventilátor sikertelen) |KI |KI |ON |N/A |
| PCM-hiba (több mint amp, over feszültség, aktuális) |KI |ON |ON |ON |
| PCM (ventilátor a tűréshatáron kívül) |ON |KI |KI |ON |
| Készenléti mód |Villogó |KI |KI |KI |
| PCM belső vezérlőprogram letöltése |KI |Villogó |Villogó |Villogó |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>A biztonsági mentési akkumulátorhoz tartozó PCM kijelző LED-EK
| Állapot | Akkumulátor jó (zöld) | Akkumulátor hibája (Amber) |
| --- | --- | --- |
| Az akkumulátor nincs jelen |KI |KI |
| Akkumulátor jelen és felszámítva |ON |KI |
| Az akkumulátor töltése vagy a karbantartás lezárása |Villogó |KI |
| Akkumulátor "Soft" (helyreállítható) hibája |KI |Villogó |
| Az akkumulátor "Hard" hibája (nem helyreállítható) |KI |ON |
| Akkumulátor lefegyverezve |Villogó |KI |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>A EBOD-ház PCM-LED-je
A EBOD-ház 580W PCM-vel rendelkezik, és nincs további akkumulátora. A EBOD-ház PCM panelje csak a tápegységek és a ventilátor kijelző-LED-ekkel rendelkezik. Az alábbi ábrán ezek a LED-ek láthatók.

   ![PCM-LED-ek a EBOD ház][3] 

A PCM állapotának meghatározásához használja a következő táblázatot.  

| Állapot | PCM OK (zöld) | AC sikertelen (Amber) | Ventilátor sikertelen (sárga) | TARTOMÁNYVEZÉRLŐi hiba (Amber) |
| --- | --- | --- | --- | --- |
| Nincs AC Power (bekerítés) |KI |KI |KI |KI |
| Nincs AC-teljesítmény (csak ez a PCM) |KI |ON |KI |ON |
| AC jelen PCM ON – OK |ON |KI |KI |KI |
| PCM-hiba (ventilátor sikertelen) |KI |KI |ON |X |
| PCM-hiba (több mint amp, over feszültség, aktuális |KI |ON |ON |ON |
| PCM (ventilátor a tűréshatáron kívül) |ON |KI |KI |ON |
| Készenléti modell |Villogó |KI |KI |KI |
| PCM belső vezérlőprogram letöltése |KI |Villogó |Villogó |Villogó |

## <a name="controller-module-indicator-leds"></a>Vezérlő modul kijelző LED-EK
A StorSimple eszköz LED-eket tartalmaz az elsődleges vezérlőhöz és a EBOD vezérlő moduljaihoz.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Figyelési LED-ek az elsődleges vezérlőhöz
Az alábbi ábrán az elsődleges vezérlőn lévő LED-ek azonosíthatók. (Az összes összetevő szerepel a Orientációs támogatásban.)  

   ![Figyelő LED-ek – elsődleges vezérlő][4]

A következő táblázat segítségével meghatározhatja, hogy a vezérlő modul megfelelően működik-e.  

### <a name="controller-indicator-leds"></a>Vezérlő kijelző LED-EK
| VEZETETT | Description |
| --- | --- |
| AZONOSÍTÓ LED (kék) |Azt jelzi, hogy a modul azonosítva van. Ha a kék LED egy futó vezérlőn villog, akkor a vezérlő az aktív vezérlő, a másik pedig a készenléti vezérlő. További információ: [az aktív vezérlő azonosítása az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Hiba LED (Amber) |A vezérlő hibáját jelzi. |
| OK LED (zöld) |A folyamatos zöld érték azt jelzi, hogy a vezérlő rendben van. A villogó zöld a vezérlő VPD konfigurációs hibáját jelzi. |
| SAS-tevékenységek LED-ek (zöld) |Az állandó zöld állapot egy aktuális tevékenység nélküli kapcsolatokat jelez. A zöld villogás azt jelzi, hogy a kapcsolatok tevékenysége folyamatban van. |
| Ethernet állapotú LED-EK |A jobb oldalon a kapcsolat/hálózati tevékenység: (állandó zöld) kapcsolat aktív, (zöld villogás) hálózati tevékenység látható. A bal oldalon a hálózati sebesség látható: (sárga) 1000 MB/s, (zöld) 100 MB/s és (ki) 10 MB/s. Az összetevő típusától függően ez a fény akkor is villoghat, ha a hálózati adapter nincs engedélyezve. |
| UTÓLAGOS LED-EK |Jelzi a rendszerindítási folyamatot, amikor a vezérlő be van kapcsolva. Ha a StorSimple-eszköz nem indul el, ez a LED segít Microsoft ügyfélszolgálata azonosítani a rendszerindítási folyamat azon pontját, amelyen a hiba történt. |

> [!IMPORTANT]
> Ha a hiba LED-je világít, a vezérlő újraindításával a vezérlő modullal kapcsolatos probléma merülhet fel. Ha a vezérlő újraindítása nem oldja meg a problémát, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>A EBOD figyelési LED-ek (EBOD ház)
A 6 GB/s SAS EBOD-vezérlők mindegyike olyan LED-ekkel rendelkezik, amelyek az alábbi ábrán látható módon jelzik az állapotukat.  

  ![Figyelő LED-EBOD ház][5]

A következő táblázat segítségével meghatározhatja, hogy a EBOD vezérlő modulja megfelelően működik-e.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD vezérlő modul kijelző LED-EK
| Állapot | I/O-modul OK (zöld) | I/O-modul hibája (Amber) | Gazdagép portjának tevékenysége (zöld) |
| --- | --- | --- | --- |
| Vezérlő modul OK |ON |KI |- |
| Vezérlő modul hibája |KI |ON |- |
| Nincs külső gazda port-csatlakozás |- |- |KI |
| Külső gazda portjának kapcsolatai – nincs tevékenység |- |- |ON |
| Külső gazda portjának kapcsolatai – tevékenység |- |- |Villogó |
| Hiba a vezérlő modul metaadatainak esetében |Villogó |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Lemezmeghajtó-kijelző LED-ek az elsődleges bekerítéshez és a EBOD-ház számára
A StorSimple-eszköz az elsődleges és a EBOD-házban található lemezmeghajtókat is tartalmaz. Az egyes lemezmeghajtók az ebben a szakaszban leírtak szerint figyelő kijelző LED-eket tartalmaznak. 

A lemezmeghajtók esetében a meghajtó állapotát egy zöld LED jelöli, és egy piros-sárga LED, amely az egyes meghajtó-szolgáltatói modulok elejére van csatlakoztatva. Az alábbi ábrán ezek a LED-ek láthatók.

  ![Lemezmeghajtók][6]

Az alábbi táblázat segítségével meghatározhatja az egyes lemezmeghajtók állapotát, ami viszont hatással van az előlapi LED általános állapotára.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Lemezmeghajtó-kijelző LED-ek a EBOD-ház számára
| Állapot | Tevékenység rendben LED (zöld) | Hiba LED (Red-Amber) | Társított Ops panel LED |
| --- | --- | --- | --- |
| Nincs meghajtó telepítve |KI |KI |Nincsenek |
| A meghajtó telepítve és működőképes |Villogás be/ki a tevékenységgel |X |Nincsenek |
| SCSI ház-szolgáltatás (SES) eszköz-identitás beállítása |ON |Villogó 1 másodperc on/1 másodperc |Nincsenek |
| SES eszköz-hibatűrő bit-készlet |ON |ON |Logikai hiba (piros) |
| Energiagazdálkodási áramkör meghibásodása |KI |ON |Modul hibája (piros) |

## <a name="audible-alarms"></a>Hallható riasztások
A StorSimple-eszköz az elsődleges bekerítéssel és a EBOD-bekerítéssel kapcsolatos hallható riasztásokat tartalmaz. Egy hallható riasztás a két ház előlapján (más néven az Ops-panelen) található. A hallható riasztás azt jelzi, hogy van-e meghibásodási feltétel. Az alábbi feltételek aktiválja a riasztást:  

* Ventilátor hibája vagy meghibásodása
* Tartományon kívüli feszültség
* Túlterhelési vagy hőmérsékleti feltétel
* Termikus túllépés
* Rendszerhiba
* Logikai hiba
* Energiaellátási hiba
* Egy Power hűtő modul (PCM) eltávolítása  

Az alábbi táblázat a riasztási állapotok különböző állapotát ismerteti.  

### <a name="alarm-states"></a>Riasztási állapotok
| Riasztás állapota | Művelet | Művelet az Elnémítás gomb megnyomásával |
| --- | --- | --- |
| S0 |Normál mód: csendes |Dupla sípszó |
| S1 |Hiba mód: 1 másodperc be/1 másodpercenként |Áttérés S2-re vagy S3-ra (lásd a megjegyzéseket) |
| S2 |Emlékeztető mód: szaggatott sípszó |Nincsenek |
| S3 |Elnémult mód: csendes |Nincsenek |
| S4 |Kritikus hiba mód: folyamatos riasztás |Nem érhető el: az Elnémítás nem aktív |

> [!NOTE]
> * Ha a riasztási állapot S1, ha nem nyomja meg a MUTE 2 percen belül, az állapot automatikusan az S2 vagy az S3 értékre vált.  
> * A riasztási állapotok S1-től S4-ig visszatérhetnek a S0 a hiba után.  
> * Bármely más állapotból beírhatók a kritikus hiba állapotának S4 állapota.  


A hallható riasztást az Ops panel némító gombjának megnyomásával állíthatja le. Ha az Elnémítás kapcsoló nem működik manuálisan, az automatikus némítás két perc után történik. Ha a riasztás el van némítva, az továbbra is rövid időszakos sípoló hangjelzéssel jelzi, hogy a probléma továbbra is fennáll. Ha az összes problémát törli, a riasztás csendes marad.

A következő táblázat ismerteti a különböző riasztási feltételeket.

### <a name="alarm-conditions"></a>Riasztási feltételek
| Állapot | Súlyosság | Riasztások | Az Ops panel LED |
| --- | --- | --- | --- |
| PCM-riasztás – az egyenáramú teljesítmény elvesztése egyetlen PCM-ből |Hiba – a redundancia elvesztése nélkül |S1 |Modul hibája |
| PCM-riasztás – az egyenáramú teljesítmény elvesztése egyetlen PCM-ből |Hiba – a redundancia elvesztése |S1 |Modul hibája |
| Nem sikerült a PCM ventilátor |Hiba – a redundancia elvesztése |S1 |Modul hibája |
| Az SBB-modul a PCM-hibát észlelte |Hiba |S1 |Modul hibája |
| PCM eltávolítva |Konfigurációs hiba |Nincsenek |Modul hibája |
| Bekerítés konfigurációs hibája |Hiba – kritikus |S1 |Modul hibája |
| Alacsony figyelmeztetési hőmérséklet riasztása |Figyelmeztetés |S1 |Modul hibája |
| Magas figyelmeztetési hőmérséklet riasztása |Figyelmeztetés |S1 |Modul hibája |
| Hőmérsékleti riasztás |Hiba – kritikus |S1 |Modul hibája |
| I2C busz meghibásodása |Hiba – a redundancia elvesztése |S1 |Modul hibája |
| Az Ops panel kommunikációs hibája (I2C) |Hiba – kritikus |S1 |Modul hibája |
| Vezérlő hibája |Hiba – kritikus |S1 |Modul hibája |
| Az SBB illesztőfelületi moduljának hibája |Hiba – kritikus |S1 |Modul hibája |
| SBB illesztőfelület-modul hibája – nem maradt működő modul |Hiba – kritikus |S4 |Modul hibája |
| Az SBB Interface modul eltávolítva |Figyelmeztetés |Nincsenek |Modul hibája |
| Energiaellátás-vezérlési hiba |Figyelmeztetés – a meghajtó tápellátásának elvesztése nélkül |S1 |Modul hibája |
| Energiaellátás-vezérlési hiba |Hiba – kritikus; a meghajtó tápellátásának elvesztése |S1 |Modul hibája |
| Meghajtó eltávolítva |Figyelmeztetés |Nincsenek |Modul hibája |
| Nem áll rendelkezésre elegendő energiaellátás |Figyelmeztetés |Nincs |Modul hibája |

## <a name="next-steps"></a>Következő lépések
További információ a [StorSimple hardveres összetevőiről és állapotáról](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png



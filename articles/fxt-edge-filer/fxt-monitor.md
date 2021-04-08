---
title: Az Azure FXT Edge Filer monitorozása
description: Az Azure FXT Edge Filer Hybrid Storage cache hardveres állapotának figyelése
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219717"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Az Azure FXT Edge Filer-hardver állapotának figyelése

Az Azure FXT Edge Filer Hybrid Storage cache rendszer több állapotjelző lámpával rendelkezik, amelyek segítségével a rendszergazdák megismerhetik a hardver működésének módját.

## <a name="system-health-status"></a>Rendszerállapot állapota

A gyorsítótárazási műveletek magasabb szinten történő figyeléséhez használja a Vezérlőpult **irányítópult** lapján a [Vezérlőpult irányítópultjának útmutatójában](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html) ismertetett lépéseket.

## <a name="hardware-status-leds"></a>Hardver állapotának LED-EK

Ez a szakasz az Azure FXT Edge Filer-hardverbe épített különböző állapotjelző lámpákat ismerteti.

### <a name="hard-drive-status-leds"></a>Merevlemez állapotának LED-EK

![kép a merevlemez-meghajtóról, vízszintes, ábrafelirat-címkék 2 (bal felső sarokban), 1 (bal alsó sarok) és 3 (jobb oldalon)](media/fxt-monitor/fxt-drive-callouts.png)

Mindegyik meghajtón két állapotú LED látható: egy tevékenység-jelző (1) és egy állapotjelző (2).

* A tevékenység LED (1) fények, ha a meghajtó használatban van.
* A LED állapota (2) az alábbi táblázatban szereplő kódok használatával jelzi a meghajtó feltételét.

| Meghajtó állapotának LED-állapota              | Értelmezés  |
|-------------------------------------|----------------------------------------------------------|
| Másodpercenként kétszer villan fel a zöld      | Meghajtó *vagy* <br> Meghajtó előkészítése az eltávolításhoz  |
| Kikapcsolva (nem világít)                         | A rendszer nem fejezte be az indítást *, vagy* <br>A meghajtó készen áll az eltávolításra |
| Zöld, borostyán és kikapcsolt villogás       | A meghajtó meghibásodása előre jelzett   |
| Másodpercenként négy alkalommal villan fel | A meghajtó nem sikerült   |
| Tömör zöld                         | A meghajtó online állapotban van |

A meghajtó jobb oldalán (3) a meghajtó kapacitása és egyéb információi vannak címkézve.

A meghajtón lévő számok a meghajtók közötti területre vannak kinyomtatva. Az Azure FXT Edge Filer-ben a 0. meghajtó a bal felső meghajtó, az 1. meghajtó pedig közvetlenül alatta van. A számozás folytatódik ebben a mintában.

![a FXT váz egyik merevlemez-öblében található, a meghajtók számát és a kapacitás feliratait ábrázoló fénykép](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Bal oldali Vezérlőpult

A bal oldali Vezérlőpulton különböző állapotjelző LED-mutatók (1) és egy nagy, megvilágított rendszerállapot-jelző (2) láthatók.

![bal oldali állapot panel, 1 címkéző állapotjelzővel a bal oldalon, a jobb oldalon pedig a nagy rendszerállapot-jelző jelzőfény](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Vezérlőpult állapotjelzői

A bal oldali állapotjelzők folytonos sárga fényt mutatnak, ha hiba van a rendszeren. Az alábbi táblázat a hibák lehetséges okait és megoldásait ismerteti.

Ha a megoldások kipróbálása után is megjelenik a hibaüzenet, [forduljon az ügyfélszolgálathoz](fxt-support-ticket.md) segítségért.

| Ikon | Leírás | Hibafeltétel | Lehetséges megoldások |
|----------------|---------------|--------------------|----------------------|
| ![meghajtó ikonja](media/fxt-monitor/fxt-hd-icon.jpg) | Meghajtó állapota | Meghajtó-hiba | Ellenőrizze a rendszer eseménynaplóját, és állapítsa meg, hogy a meghajtó hibával rendelkezik-e, vagy <br>Futtassa a megfelelő online diagnosztikai tesztet; Indítsa újra a rendszert, és futtassa a beágyazott diagnosztikát (ePSA), vagy <br>Ha a meghajtók RAID-tömbben vannak konfigurálva, indítsa újra a rendszert, és adja meg a gazdagép-adapter konfigurációs segédprogramjának programját |
|![hőmérséklet ikon](media/fxt-monitor/fxt-temp-icon.jpg) | Hőmérséklet állapota | Termikus hiba – például egy ventilátor meghiúsult, vagy a környezeti hőmérséklet a megengedett tartományon kívül esik. | A következő címezhető feltételek keresése: <br>Egy hűtési ventilátor hiányzik vagy nem sikerült <br>A rendszer fedelét, az Air lepel, a memóriamodul üres vagy a hátsó kitöltő zárójelet eltávolítja <br>A környezeti hőmérséklet túl magas <br>A külső légáram akadályozva van |
|![villamos energia ikon](media/fxt-monitor/fxt-electric-icon.jpg) | Elektromos állapot | Elektromos hiba – például a tartományon kívüli feszültség, a sikertelen PSU vagy egy meghibásodott feszültség szabályzó |  Keresse meg a rendszer eseménynaplóját vagy a rendszerüzeneteket az adott probléma esetén. Ha van PSU-probléma, ellenőrizze a PSU állapotát, és ha szükséges, helyezze át a PSU-t. |
|![memória ikon](media/fxt-monitor/fxt-memory-icon.jpg) | Memória állapota | Memóriahiba | Keresse meg a hibás memória helyét a rendszer eseménynaplójában vagy a rendszerüzenetekben; Helyezze át a memóriamodult. |
|![PCIe ikon](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe-állapot | PCIe kártya hibája | Indítsa újra a rendszert; a PCIe kártya illesztőprogramjainak frissítése; a kártya újratelepítése |

### <a name="system-health-status-indicator"></a>Rendszerállapot-állapotjelző

A bal oldali Vezérlőpult jobb oldalán található nagy megvilágított gomb a rendszer általános állapotát jelzi, és a rendszer-azonosító módban is használja.

A rendszerállapot és az azonosító gomb megnyomásával válthat a rendszerazonosító mód és a rendszerállapot mód között.

|Rendszerállapot állapota | Feltétel |
|-------------------------------------------|-----------------------------------------------|
| Tömör kék | Normál művelet: a rendszer be van kapcsolva, a normál működés, és a rendszer-azonosító mód nem aktív. <br/>Ha rendszerazonosító módra szeretne váltani, nyomja meg a rendszerállapot és az azonosító gombot. |
| Villogó kék | A rendszer-azonosító üzemmód aktív. Ha rendszerállapot-módba szeretne váltani, nyomja meg a rendszerállapot és a rendszerazonosító gombot. |
| Folytonos sárga | A rendszer nem biztonságos módban van. Ha a probléma továbbra is fennáll, [forduljon a Microsoft ügyfél-és támogatási szolgálatához](fxt-support-ticket.md). |
| Villogó sárga | Rendszerhiba. Adott hibaüzenetek esetén keresse meg a rendszer eseménynaplóját. További információ a rendszerszintű belső vezérlőprogram és a rendszerösszetevőket figyelő ügynökök által generált eseményekről és hibákról: a hibakód keresése lap a következő címen: qrl.dell.com. |

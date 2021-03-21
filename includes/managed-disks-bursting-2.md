---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3035b5d2803ff91e84bc6b47a99963185f9195d3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623504"
---
## <a name="disk-level-bursting"></a>Lemez szintű burst

### <a name="on-demand-bursting-preview"></a>Igény szerinti burst (előzetes verzió)

A lemezek igény szerinti felszakítási modelljét használó lemezek az eredeti kiépített célokon túl, a számítási feladatokhoz képest akár a maximális burst célpontig is kitörtek. Egy 1 TiB P30-lemezen például a kiépített IOPS 5000 IOPS. Ha engedélyezve van a lemez kitörése ezen a lemezen, a számítási feladatok az IOs-et a lemezre is kiállíthatják a 30 000 IOPS és 1 000 MBps maximális burst teljesítményével.

Ha azt szeretné, hogy a számítási feladatok gyakran fussanak a kiépített Perf-cél után, a lemez kitörése nem költséghatékony. Ebben az esetben azt javasoljuk, hogy a lemez teljesítményszint [magasabb szintűre](../articles/virtual-machines/disks-performance-tiers.md) változzon, a jobb alapteljesítményhez. Tekintse át a számlázási adatokat, és mérje fel, hogy a számítási feladatok forgalmi mintája alapján.

Az igény szerinti kitörés engedélyezése előtt Ismerje meg a következőket:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Számlázás

Az igény szerinti Felskálázási modellt használó lemezekre óradíjat kell felszámolni, és a tranzakciós költségek minden, a kiépített célponton túli feltört tranzakcióra érvényesek. A tranzakciós költségeket az utólagos elszámolású modell alapján számítjuk fel, a nem gyorsítótárazott lemezes IOs-en alapulva, beleértve az olvasásokat és az írásokat is, amelyek túllépik a kiosztott célokat. A következő példa egy számlázási órában használt lemezes forgalmi mintákat szemlélteti:

Lemez konfigurációja: prémium SSD – 1 TiB (P30), a lemez kitörése engedélyezve.

- 00:00:00 – 00:10:00 lemez IOPS a 5 000 IOPS kiépített célja alatt 
- 00:10:01 – 00:10:10 az alkalmazás egy batch-feladatot adott ki, ami miatt a lemez IOPS a 6 000 IOPS 10 másodpercig. 
- 00:10:11 – 00:59:00 lemez IOPS a 5 000 IOPS kiépített célja alatt 
- 00:59:01 – 01:00:00 az alkalmazás egy másik batch-feladatot adott ki, ami miatt a lemez IOPS a 7 000 IOPS for 60 másodperc 

Ebben a számlázási órában a feltört költségek a következő két költségből állnak:

Az első díj a $X (a régió által meghatározott) burst-előfizetési átalánya. Ezt az átalány-díjat a rendszer mindig a csatolási állapot figyelmen kívül hagyásával veszi fel, amíg le nem áll. 

A második a burst tranzakciós díj. A lemez kitörése két alkalommal történt. 00:10:01 – 00:10:10, a halmozott burst tranzakció (6 000 – 5 000) X 10 = 10 000. 00:59:01 – 01:00:00, a halmozott burst tranzakció (7 000 – 5 000) X 60 = 120 000. A teljes burst tranzakció a 10 000 + 120 000 = 130 000. A burst tranzakciós költséget a $Y 13 10 000 egység (a regionális díjszabás alapján) alapján számítjuk fel.

Ezzel a számlázási óra lemezre bontásának teljes költsége $X + $Y értékkel egyenlő. Ugyanez a számítás érvényes a MBps kiépített céljára. A 256 kb IO-méretével a több mint MB értékű tranzakciókat fordítjuk. Ha a lemez forgalma meghaladja a kiépített IOPS és a MBps-célt is, az alábbi példára kattintva kiszámíthatja a burst tranzakciókat. 

Lemez konfigurációja: prémium SSD – 1 TB (P30), a lemez kitörése engedélyezve.

- 00:00:01 – 00:00:05 az alkalmazás egy batch-feladatot adott ki, ami azt eredményezte, hogy a lemez IOPS a 10 000 IOPS és az 300 MBps-ra 5 másodpercig.
- 00:00:06 – 00:00:10 az alkalmazás kiállított egy helyreállítási feladatot, ami 6 000 miatt a lemez IOPS a-es és az 600 MBps-ra 5 másodpercig.

A burst tranzakciót a rendszer a IOPS vagy a MBps-burst tranzakciók maximális számaként veszi figyelembe. 00:00:01 – 00:00:05, a halmozott burst tranzakció Max ((10 000 – 5 000), (300-200) * 1024/256) * 5 = 25 000 tranzakció. 00:00:06 – 00:00:10, a halmozott burst tranzakció Max ((6 000 – 5 000), (600-200) * 1024/256) * 5 = 8 000 tranzakció. Ezen felül a burst-feltöltési átalány díját is bemutatjuk, hogy az igény szerinti lemez kitörésének teljes díja legyen. 

A számítási feladatok értékeléséhez tekintse meg a [Managed Disks díjszabási oldalát](https://azure.microsoft.com/pricing/details/managed-disks/) , amely részletesen ismerteti a díjszabást, és használja az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/?service=storage) . 

### <a name="credit-based-bursting"></a>Kredit-alapú kitörés

A kredit-alapú adatmennyiség az Azure nyilvános, kormányzati és kínai felhőkben található összes régióban P20 és kisebb méretekben érhető el. Alapértelmezés szerint a lemezes kitörés engedélyezve van a támogatott lemezterületek összes új és meglévő központi telepítésén. A VM-szintű kitörés csak a kredit alapú kitörést használja.

## <a name="virtual-machine-level-bursting"></a>Virtuális gépek – szintű kitörés

A virtuálisgép-szint kitörése csak a kredit alapú modellt használja a kitöréshez, alapértelmezés szerint engedélyezve van minden olyan virtuális gép esetében, amely támogatja azt.

A virtuális gépek szintjének kitörése az Azure nyilvános felhő összes régiójában engedélyezett a következő támogatott méreteken: 
- [Lsv2 sorozat](../articles/virtual-machines/lsv2-series.md)
- [Dv3 és DSv3 sorozat](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3 és Esv3 sorozat](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Feltört folyamat

A feltört kreditrendszer a virtuális gép szintjén és a lemez szintjén is azonos módon használható. Az erőforrás, amely egy virtuális gép vagy egy lemez, a saját burst-gyűjtőben teljes értékű Kredittel fog kezdődni. Ezek a kreditek lehetővé teszik, hogy akár 30 percet is igénybe vehet a maximális burst arányban. A kreditek felhalmozódása akkor történik meg, amikor az erőforrás IOPS vagy MB/s kihasználtsága az erőforrás teljesítményének megcélzása alatt áll. Ha az erőforrás feltörte az elszámolási krediteket, és a számítási feladatnak extra teljesítményre van szüksége, az erőforrás ezeket a krediteket a teljesítménybeli korlátok fölé helyezheti, és növelheti a teljesítményt a munkaterhelés iránti igények kielégítése érdekében.

![Feltört gyűjtő diagramja](media/managed-disks-bursting/bucket-diagram.jpg)

A rendelkezésre álló kreditek elköltése Önnek. A 30 perces burst kreditet a nap folyamán egymás után vagy szórványosan használhatja fel. Az erőforrások üzembe helyezésekor a kreditek teljes kiosztása áll rendelkezésre. A Kimerítés során a rendszer kevesebb mint egy napot vesz igénybe a feltöltéshez. A kreditek a saját belátása szerint elkölthető, a burst gyűjtőnek nem kell teljesnek lennie ahhoz, hogy az erőforrások feltörtek legyenek. A burst felhalmozódás az egyes erőforrásoktól függ, mivel ez a használaton kívüli IOPS és MB/s-on alapul. A magasabb alapszintű teljesítmény-erőforrások az alacsonyabb alapszintű erőforrásoknál gyorsabban felmerülhetnek a feltört kreditek. Például egy P1 lemez üresjárata 120 IOPS-t fog felhalmozni, míg az alapszintű P20 lemezek száma másodpercenként 2 300 IOPS.

## <a name="bursting-states"></a>Feltört állapotok
Három állapottal rendelkezhet, ha az erőforrás a kitört állapotban van:
- **Felhalmozás** – az erőforrás IO-forgalma kevesebb, mint a teljesítményre vonatkozó cél. A IOPS és a MB/s feltört kreditek összegyűjtése egymástól elkülönítve történik. Az erőforrás felhalmozhat IOPS krediteket, és megoszthat MB/s kreditet, vagy fordítva.
- **Burst** – az erőforrás forgalma több, mint a teljesítmény célját használja. A burst forgalom egymástól függetlenül fogja felhasználni a IOPS vagy a sávszélességtől kapott krediteket.
- **Állandó** – az erőforrás forgalma pontosan a teljesítmény célpontja.

## <a name="bursting-examples"></a>Feltört példák

Az alábbi példák azt mutatják be, hogyan működik a kitörés a különböző virtuális gépekkel és lemezes kombinációkkal. Ahhoz, hogy a példák könnyen követhető legyenek, a MB/s-ra fogunk összpontosítani, de ugyanezt a logikát a IOPS függetlenül alkalmazza a rendszer.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Nem betört virtuális gép, amely feltört lemezekkel rendelkezik
**VIRTUÁLIS gépek és lemezek kombinációja:** 
- Standard_D8as_v4 
    - Gyorsítótár nélküli MB/s: 192
- P4 operációsrendszer-lemez
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 
- 2 P10 adatlemez 
    - Kiépített MB/s: 100
    - Maximális burst MB/s: 170

 Amikor a virtuális gép elindul, lekéri az operációs rendszer lemezéről az adatok lekérését. Mivel az operációsrendszer-lemez egy rendszerindító virtuális gép része, az operációsrendszer-lemez tele lesz a feltört kreditekkel. Ezek a kreditek lehetővé teszik, hogy az operációs rendszer lemeze elindítsa az indítást 170 MB/s-on.

![A virtuális gép 192 MB/s sebességre vonatkozó kérést küld az operációsrendszer-lemezre, az operációsrendszer-lemez az 170 MB/s adatokkal válaszol.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

A rendszerindítás befejezése után egy alkalmazás fut a virtuális gépen, és nem kritikus fontosságú számítási feladattal rendelkezik. Ehhez a munkaterheléshez 15 MB/S érték szükséges, amely egyenletesen oszlik el az összes lemez között.

![Az alkalmazás 15 MB/s átviteli sebességre vonatkozó kérést küld a virtuális géphez, a virtuális gép igénybe veszi és elküldi az egyes lemezeket 5 MB/s-ra, az egyes lemezek 5 MB/s értéket adnak vissza, a virtuális gép 15 MB/s értéket ad vissza az alkalmazásnak.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Ezután az alkalmazásnak fel kell dolgoznia egy batch-feladatot, amely 192 MB/s memóriát igényel. az operációsrendszer-lemez 2 MB/s-ot használ, a többi pedig egyenletesen oszlik meg az adatlemezek között.

![Az alkalmazás 192 MB/s sebességű, virtuális gépre vonatkozó kérést küld, a virtuális gép kérést küld, és az adatlemezekre (95 MB/s) és 2 MB/s-ra kéri az operációsrendszer-lemezre küldött kérések nagy részét, az adatlemezek pedig a kereslet kielégítéséhez, és az összes lemez visszaadja a kért átviteli sebességet a virtuális géphez](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Feltört virtuális gép nem feltört lemezekkel
**VIRTUÁLIS gépek és lemezek kombinációja:** 
- Standard_L8s_v2 
    - Gyorsítótár nélküli MB/s: 160
    - Maximális burst MB/s: 1 280
- P50 operációsrendszer-lemez
    - Kiépített MB/s: 250 
- 2 P10 adatlemez 
    - Kiépített MB/s: 250

 A kezdeti rendszerindítás után egy alkalmazás fut a virtuális gépen, és nem kritikus fontosságú számítási feladattal rendelkezik. Ehhez a munkaterheléshez 30 MB/s érték szükséges, amely egyenletesen oszlik el az összes lemez között.
![Az alkalmazás 30 MB/s átviteli sebességre vonatkozó kérelmet küld a virtuális géphez, a virtuális gép igénybe veszi és elküldi az egyes lemezeit 10 MB/s-ra, minden lemez 10 MB/s értéket ad vissza, a virtuális gép 30 MB/s értéket ad vissza az alkalmazásnak.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Ezután az alkalmazásnak fel kell dolgoznia egy batch-feladatot, amely 600 MB/s memóriát igényel. A Standard_L8s_v2 az igények kielégítése érdekében felrobban, majd a lemezekre irányuló kérések egyenletesen oszlanak meg a P50-lemezeken.

![Az alkalmazás 600 MB/s sebességgel küldi el a virtuális gép felé irányuló kérést, a virtuális gép pedig az 200 MB/s-ra irányuló kérelem elvégzését kéri le, és minden lemezre elküldi a kérelmet, a virtuális gépek pedig 200 MB/s értéket adnak 600 vissza.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Leválasztható virtuális gép feltört lemezekkel
**VIRTUÁLIS gépek és lemezek kombinációja:** 
- Standard_L8s_v2 
    - Gyorsítótár nélküli MB/s: 160
    - Maximális burst MB/s: 1 280
- P4 operációsrendszer-lemez
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 
- 2 P4 adatlemez 
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 

A virtuális gép indításakor a rendszer az operációsrendszer-lemezről 1 280 MB/s értékű burst-korlátot kér le, és az operációsrendszer-lemez a 170 MB/s-os terhelési teljesítménnyel fog válaszolni.

![Indításkor a virtuális gép 1 280 MB/s-OS kérést küld az operációsrendszer-lemezre, az operációsrendszer-lemez feltört a 1 280 MB/s értékre.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Indítás után olyan alkalmazást indít el, amely nem kritikus fontosságú számítási feladattal rendelkezik. Az alkalmazáshoz 15 MB/s érték szükséges, amely egyenletesen oszlik el az összes lemez között.

![Az alkalmazás 15 MB/s átviteli sebességre vonatkozó kérelmet küld a virtuális géphez, a virtuális gép igénybe veszi és elküldi az egyes lemezeket 5 MB/s-ra, az egyes lemezek 5 MB/s válaszokat adnak vissza, a virtuális gép 15 MB/s értéket ad vissza az alkalmazásnak.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Ezután az alkalmazásnak fel kell dolgoznia egy batch-feladatot, amely 360 MB/s memóriát igényel. A Standard_L8s_v2 az igények kielégítése érdekében felrobban, majd kéri. Az operációsrendszer-lemez csak 20 MB/s memóriát igényel. A fennmaradó 340 MB/s-ot a kitört P4 adatlemezek kezelik.

![Az alkalmazás 360 MB/s sebességre vonatkozó kérést küld a virtuális géphez, a virtuális gép az operációsrendszer-360 lemezről az 170 MB/s-ra és 20 MB/s-ra küldi a kérést, és elküldi az összes adatlemezét.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)

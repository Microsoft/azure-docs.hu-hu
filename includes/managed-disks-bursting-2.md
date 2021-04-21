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
ms.openlocfilehash: 4162fe12ff54f16cd5f982f6a576905227c9a107
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820929"
---
## <a name="disk-level-bursting"></a>Lemezszintű adatlokozás

### <a name="on-demand-bursting-preview"></a>Igény szerinti bursting (előzetes verzió)

A lemez-adatlökés igény szerinti adatlökés-modelljét használó lemezek az eredeti kiépítési célokon túli, a számítási feladatok által gyakran kiépített céltól a maximális adatlökésig is túllépik a kapacitást. Egy 1 TiB P30-as lemezen például a kiépített IOPS 5000 IOPS. Ha a lemez adatlökének növelése engedélyezve van ezen a lemezen, a számítási feladatok i/S-eket is kiadnak ennek a lemeznek a legfeljebb 30 000 IOPS és 1000 MB/s teljesítményig.

Ha arra számít, hogy a számítási feladatok gyakran túl fognak futni a kiépített teljesítménycélon, a lemez-adatlökés nem lesz költséghatékony. Ebben az esetben azt javasoljuk, hogy a jobb alapteljesítmény érdekében inkább magasabb szintre módosítsa [a](../articles/virtual-machines/disks-performance-tiers.md) lemez teljesítményszintet. Tekintse át a számlázási adatokat, és mérje fel a számítási feladatok forgalmi mintája alapján.

Mielőtt engedélyezné az igény szerinti igény szerinti időlokozást, a következőket kell megértenie:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Számlázás

Az igény szerinti adatlökési modellt használó lemezekre óránkénti, 1000-1000-100 költségekkel kell fizetni, és a tranzakciós költségek a kiépített célon túli adatlökés-tranzakciókra is vonatkoznak. A tranzakciós költségek a használatalapú fizetési modell alapján vannak felszámolva a nem gyorsítótárazó lemez I/I/-jai alapján, beleértve a kiosztott célokat meghaladó olvasási és írási adatokat is. Az alábbiakban egy példát mutatunk be a lemezforgalmi mintákra a számlázási órában:

Lemezkonfiguráció: prémium SSD – 1 TiB (P30), Engedélyezett lemezlúdítás.

- 00:00:00 – 00:10:00 A lemez IOPS-érték a kiépített 5000 IOPS-érték alatt 
- 00:10:01 – 00:10:10 Az alkalmazás egy kötegelt feladatot adott ki, amely miatt a lemez IOPS-értékének 10 másodpercig 6000 IOPS-nek kell megtelnie 
- 00:10:11 – 00:59:00 A lemez IOPS-érték a kiépített 5000 IOPS-érték alatt 
- 00:59:01 – 01:00:00 Az alkalmazás egy másik kötegelt feladatot adott ki, amely miatt a lemez IOPS-értékének 7000 IOPS-értéknél 60 másodpercig kell nőni 

Ebben a számlázási órában a kiesés költsége két díjból áll:

Az első díj a ki- és bekapcsolt $X díj (amelyet a régió határoz meg). Ez az egyendíjas díj a csatolási állapot figyelmen kívül hagyása után mindig díjat számít fel a lemezen, amíg le nem tiltják. 

A második a burst tranzakció költsége. A lemezlúdítás két időhelyen történt. 00:10:01 – 00:10:10 között a halmozott burst tranzakció (6000 – 5000) X 10 = 10 000. 00:59:01 –01:00:00 között a halmozott burst tranzakció (7000 – 5000) X 60 = 120 000. Az összes burst-tranzakció 10 000 + 120 000 = 130 000. A burst tranzakciós költség $Y 13 egység 10 000 tranzakció alapján (a regionális díjszabás alapján).

Ezzel a számlázási óra lemez-adatlokozásának teljes költsége megegyezik a $X + $Y. Ugyanez a számítás vonatkozik a kiépített MBps-re vonatkozó burstingre is. A MB-túlméretet 256 KB-os I/O-méretű tranzakciókra fordítjuk le. Ha a lemez forgalma meghaladja a kiépített IOPS- és MBps-célértékeket, az alábbi példában kiszámíthatja az adatlötty-tranzakciókat. 

Lemezkonfiguráció: prémium SSD – 1 TB (P30), lemez-adatlok- engedélyezett.

- 00:00:01 – 00:00:05 Az alkalmazás egy kötegelt feladatot adott ki, amely miatt a lemez IOPS-értéke öt másodpercig 10 000 IOPS-re és 300 MB/s-ra volt nagyobb.
- 00:00:06 – 00:00:10 Az alkalmazás egy helyreállítási feladatot adott ki, amely miatt a lemez IOPS-értéke öt másodpercig 6000 IOPS-re és 600 MB/s-ra volt hirtelen.

A rendszer a burst tranzakciót az IOPS- vagy MBps-burstingből származó tranzakciók maximális számaként veszi figyelembe. 00:00:01 – 00:00:05 között a halmozott burst tranzakció maximális ((10 000 – 5000), (300 – 200) * 1024 / 256)) * 5 = 25 000 tranzakció. 00:00:06 – 00:00:10 között a halmozott burst tranzakció maximális ((6000 – 5000), (600 – 200) * 1024 / 256)) * 5 = 8000 tranzakció. Ráadásul az igény szerinti lemez-adatlok-igénylés engedélyezéséhez szükséges teljes költség lefektetése érdekében ki kell fizetni az adatlok-igénylést. 

A díjszabásról [](https://azure.microsoft.com/pricing/details/managed-disks/) az Managed Disks oldalon talál további információt, és az [Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) díjkalkulátor használatával értékelést is kaphat a számítási feladatról. 


Az igény szerinti ki- és bekapcsolás engedélyezéséről lásd: Igény szerinti gyorsítás [engedélyezése.](../articles/virtual-machines/disks-enable-bursting.md)

### <a name="credit-based-bursting"></a>Hitelkeret-alapú ki- és felhatolás

Kreditalapú adatlökés érhető el a P20 és kisebb lemezméretekhez az Azure nyilvános, kormányzati és kínai felhők minden régiójában. Alapértelmezés szerint a lemez-adatlökés engedélyezve van a támogatott lemezméretek összes új és meglévő üzemelő példányán. A virtuálisgép-szintű bursting csak kreditalapú burstinget használ.

## <a name="virtual-machine-level-bursting"></a>Virtuálisgép-szintű bursting

A virtuálisgép-szintű adatlökés csak a kreditalapú modellt használja az adatlökéshez, alapértelmezés szerint engedélyezve van az azt támogató összes virtuális gépen.

A virtuálisgép-szintű bursting az Azure nyilvános felhő minden régiójában engedélyezve van a következő támogatott méretekben: 
- [Lsv2 sorozat](../articles/virtual-machines/lsv2-series.md)
- [Dv3 és DSv3 sorozat](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3 és Esv3 sorozat](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Bursting flow

Az adatlukozási jóváírási rendszer ugyanúgy működik a virtuális gép és a lemez szintjén is. Az ön erőforrása( egy virtuális gép vagy lemez) a saját adatlöket-gyűjtőben lévő, teljes mértékben fel nem használt kreditekkel fog kezdeni. Ezekkel a kreditekkel akár 30 percig is meglökkedhat a maximális burst-sebességgel. Krediteket gyűjthet, ha az erőforrás IOPS-értékét vagy MB/s-ját használja az erőforrás teljesítménycélja alatt. Ha az erőforrása egyre több kreditet halmozással jár, és a számítási feladathoz további teljesítményre van szükség, az erőforrás a teljesítménykorlátok túllépése és a teljesítmény növelése érdekében használhatja fel ezeket a krediteket a számítási feladatok igényeinek kielégítése érdekében.

![Bursting bucket diagram.](media/managed-disks-bursting/bucket-diagram.jpg)

A rendelkezésre álló kreditek elkölti a saját maga által biztosított lehetőségeket. A 30 perces meglökkent krediteket egymás után vagy szórványosan használhatja a nap folyamán. Az erőforrások üzembe helyezésekor a kreditek teljes kiosztása szükséges. Ha ezek a kimerülések, az újratelepítés kevesebb mint egy napot vesz igénybe. A krediteket saját belátása szerint lehet elköltni, a meglöket gyűjtőnek nem kell megtelni ahhoz, hogy az erőforrások megtelhessen. Az adatlokozás növelése az egyes erőforrásoktól függően változik, mivel a kihasználatlan IOPS-értéken és a teljesítménycéljaik alatti MB/s-on alapul. A magasabb alapteljesítményű erőforrások gyorsabban halmozást eredményeznek, mint az alacsonyabb szinten teljesítő erőforrások. Egy P1 lemezazonosító például másodpercenként 120 IOPS-t, míg az idling P20 lemez másodpercenként 2300 IOPS-t fog felhalmozott.

## <a name="bursting-states"></a>Bursting states
Az erőforrás három olyan elosztási módban lehet, amelyekben engedélyezve van a ki- és bekapcsolt elosztás:
- **Accruing (Eredmény)** – Az erőforrás I/O-forgalma a teljesítménycélnál kevesebbet használ. Az IOPS- és MB/s-kreditek felhalmozása egymástól elkülönítve történik. Az erőforráshoz IOPS-kreditek és MB/s-kreditek is hozzá lehet használhatja, vagy fordítva.
- **Adatlokozás** – Az erőforrás forgalma a teljesítménycélnál többet használ. Az adatlökés által generált forgalom egymástól függetlenül felhasználja az IOPS-ból vagy a sávszélességből származó krediteket.
- **Konstans** – Az erőforrás forgalma pontosan a teljesítménycélon van.

## <a name="bursting-examples"></a>Bursting -példák

Az alábbi példák bemutatják, hogyan működik a bursting a különböző virtuálisgép- és lemezkombinációkkal. Annak érdekében, hogy a példák könnyen követhetők, MB/s-et kell alkalmaznunk, de ugyanezt a logikát az IOPS-hez függetlenül alkalmazjuk.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Nem adatlökhető virtuális gép adatlökhető lemezekkel
**Virtuális gép és lemez kombinációja:** 
- Standard_D8as_v4 
    - Nem gyorsítótárazó MB/s: 192
- P4 operációsrendszer-lemez
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 
- 2 P10 adatlemez 
    - Kiépítve MB/s: 100
    - Maximális burst MB/s: 170

 Amikor a virtuális gép elindul, adatokat szerez be az operációsrendszer-lemezről. Mivel az operációsrendszer-lemez egy rendszerindítást elindító virtuális gép része, az operációsrendszer-lemezen megtelnek a kreditek. Ezek a kreditek lehetővé teszik, hogy az operációsrendszer-lemez indítási mérete 170 MB/s másodperc.

![A virtuális gép 192 MB/s átviteli sebességre vonatkozó kérést küld az operációsrendszer-lemeznek, az operációsrendszer-lemez pedig 170 MB/s adattal válaszol.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

A rendszerindítás befejezése után a rendszer futtat egy alkalmazást a virtuális gépen, és nem kritikus számítási feladatokkal rendelkezik. Ehhez a számítási feladathoz 15 MB/S szükséges, amely egyenletesen ossok el az összes lemezen.

![Az alkalmazás 15 MB/s átviteli sebességre vonatkozó kérést küld a virtuális gépnek, a virtuális gép kérést küld, és mindegyik lemezét 5 MB/s-re kéri, minden lemez 5 MB/s értéket ad vissza, a virtuális gép pedig 15 MB/s értéket ad vissza az alkalmazásnak.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Ezután az alkalmazásnak egy kötegelt feladatot kell feldolgoznia, amely 192 MB/s-ot igényel. Az operációsrendszer-lemez 2 MB/s-ot használ, a többit pedig egyenletesen osztja el az adatlemezek között.

![Az alkalmazás 192 MB/s átviteli sebességre vonatkozó kérést küld a virtuális gépnek, a virtuális gép kérést küld, és a kérés nagy részét (95 MB/s) és 2 MB/s értéket küld az operációsrendszer-lemeznek, az adatlemezek az igényeknek való megfelelő teljesítményigénynek megfelelően meglöknek, és az összes lemez visszaadja a kért átviteli sebességet a virtuális gépnek, amely visszaküldi azt az alkalmazásnak.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Adatlökökre állítható virtuális gép nem adatlökhető lemezekkel
**Virtuális gép és lemez kombinációja:** 
- Standard_L8s_v2 
    - Gyorsítótárazásmentes MB/s: 160
    - Maximális burst MB/s: 1280
- P50 operációsrendszer-lemez
    - Kiépítve MB/s: 250 
- 2 P10 adatlemez 
    - Kiépített MB/s: 250

 A kezdeti rendszerindítás után a rendszer futtat egy alkalmazást a virtuális gépen, és nem kritikus számítási feladatokkal rendelkezik. Ehhez a számítási feladathoz 30 MB/s szükséges, amely egyenletesen ossok el az összes lemez között.
![Az alkalmazás 30 MB/s átviteli sebességre vonatkozó kérést küld a virtuális gépnek, a virtuális gép kérést kér, és mindegyik lemezét 10 MB/s sebességre kéri, minden lemez 10 MB/s értéket ad vissza, a virtuális gép pedig 30 MB/s értéket ad vissza az alkalmazásnak.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Ezután az alkalmazásnak egy 600 MB/s méretű kötegelt feladatot kell feldolgoznia. A Standard_L8s_v2 meglökken, hogy megfeleljen ennek az igénynek, majd a lemezekre vonatkozó kérések egyenletesen osodnak el P50-lemezekre.

![Az alkalmazás 600 MB/s átviteli sebességre vonatkozó kérést küld a virtuális gépnek, a virtuális gép adatlökökre van szükség a kéréshez, és minden lemezhez 200 MB/s-os kérést küld, minden lemez 200 MB/s értéket ad vissza, a virtuális gépek adatlökései pedig 600 MB/s értéket adnak vissza az alkalmazásnak.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Adatlökökre állítható virtuális gép adatlökökre állítható lemezekkel
**Virtuális gép és lemez kombinációja:** 
- Standard_L8s_v2 
    - Nem gyorsítótárazó MB/s: 160
    - Maximális burst MB/s: 1280
- P4 operációsrendszer-lemez
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 
- 2 P4 adatlemez 
    - Kiépített MB/s: 25
    - Maximális burst MB/s: 170 

A virtuális gép indításakor a rendszer az operációsrendszer-lemezről kéri az 1280 MB/s-os adatlok-korlátot, és az operációsrendszer-lemez 170 MB/s-os adatlok-teljesítményével válaszol.

![Indításkor a virtuális gép 1280 MB/s-os kérést küld az operációsrendszer-lemezre, az operációsrendszer-lemez pedig az 1280 MB/s bájtot adja vissza.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Az indítás után elindít egy alkalmazást, amely nem kritikus számítási feladatokkal rendelkezik. Ehhez az alkalmazáshoz 15 MB/s szükséges, amely egyenletesen el lesz vava az összes lemez között.

![Az alkalmazás 15 MB/s átviteli sebességre vonatkozó kérést küld a virtuális gépnek, a virtuális gép kérést küld, és mindegyik lemezének 5 MB/s-os kérést küld, minden lemez 5 MB/s választ ad vissza, a virtuális gép pedig 15 MB/s értéket ad vissza az alkalmazásnak.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Ezután az alkalmazásnak egy kötegelt feladatot kell feldolgoznia, amely 360 MB/s-ot igényel. A Standard_L8s_v2 meglökken, hogy megfeleljen ennek az igénynek, majd kérések. Az operációsrendszer-lemeznek csak 20 MB/s-re van szüksége. A fennmaradó 340 MB/s-ot a hirtelen meglökő P4 adatlemezek kezelik.

![Az alkalmazás 360 MB/s átviteli sebességre vonatkozó kérést küld a virtuális gépnek, a virtuális gép adatlökökre van szükség, és minden adatlemeze 170 MB/s és 20 MB/s sebességre vonatkozó kérést küld az operációsrendszer-lemezről, és minden lemez visszaadja a kért MB/s-et, a virtuálisgép-adatlökök pedig 360 MB/s értéket adnak vissza az alkalmazásnak.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)

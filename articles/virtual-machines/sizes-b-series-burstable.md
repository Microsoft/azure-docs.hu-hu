---
title: B sorozat – feltört – Azure Virtual Machines
description: Ismerteti a feltört Azure-beli VM-méretek B sorozatát.
services: virtual-machines
ms.subservice: sizes
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: 31a65cab7dfdd478560b7babba156cec7645cf33
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917251"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>B sorozatú virtuális gépek méretei

A B sorozatú virtuális gépek ideálisak olyan számítási feladatokhoz, amelyeknek nincs szükségük a CPU teljes teljesítményére, például webkiszolgálókra, fogalmak igazolására, kis adatbázisokra és fejlesztői Build környezetekre. Ezek a számítási feladatok általában feltört teljesítménnyel kapcsolatos követelményekkel rendelkeznek. A B sorozat lehetővé teszi, hogy olyan virtuálisgép-méretet vásároljon, amely alapteljesítményű, és a kreditek kiépítéséhez alacsonyabb, mint az alapkonfiguráció. Ha a virtuális gép felhalmozott Kredittel rendelkezik, a virtuális gép a vCPU akár 100%-ában is feltörte az alapkonfigurációt, ha az alkalmazása nagyobb CPU-teljesítményt igényel.

A B sorozat a következő virtuálisgép-méretekhez tartozik:

[Azure számítási egység (ACU)](./acu.md): változó *<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott * *<br>

* A B sorozatú virtuális gépek beállíthatók, így a ACU-számok a munkaterheléstől és az alapvető használattól függően eltérőek lesznek.<br>
* * A gyorsított hálózatkezelés csak *Standard_B12ms*, *Standard_B16ms* és *Standard_B20ms* esetén támogatott.
<br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Virtuális gép alapszintű CPU-teljesítmény | Virtuális gép maximális CPU-teljesítmény | Kezdeti kreditek | Banki/óránkénti kreditek | Maximális banki kreditek | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> a B1ls csak Linux rendszeren támogatott

## <a name="workload-example"></a>Számítási feladatok – példa

Vegye fontolóra egy Office-beadási/kimenő alkalmazás alkalmazását. Az alkalmazásnak a munkaidőn kívül kell lennie a CPU-feltöréseknek, de nem sok számítási teljesítmény van a munkaidőn kívül. Ebben a példában a számítási feladathoz a 16vCPU virtuális gép hatékony működésének 64GiB van szükség.

A táblázat az óránkénti forgalmi adatokat mutatja, a diagram pedig az adott forgalom vizuális ábrázolása.

B16 jellemzői:

Maximális CPU-teljesítmény: 16vCPU * 100% = 1600%

Alapterv: 270%

![Óránkénti forgalmi adatok diagramja](./media/b-series-burstable/office-workload.png)

| Eset | Idő | CPU-használat (%) | Halmozott kreditek<sup>1</sup> | Rendelkezésre álló kreditek |
| --- | --- | --- | --- | --- |
| B16ms üzembe helyezése | Üzembe helyezés | Üzembe helyezés  | 480 (kezdeti kreditek) | 480 |
| Nincs forgalom | 0:00 | 0 | 162 | 642 |
| Nincs forgalom | 1:00 | 0 | 162 | 804 |
| Nincs forgalom | 2:00 | 0 | 162 | 966 |
| Nincs forgalom | 3:00 | 0 | 162 | 1128 |
| Nincs forgalom | 4:00 | 0 | 162 | 1290 |
| Nincs forgalom | 5:00 | 0 | 162 | 1452 |
| Alacsony forgalmú | 6:00 | 270 | 0 | 1452 |
| Az alkalmazottak az irodába érkeznek (az alkalmazásnak 80% vCPU) kell lennie | 7:00 | 1280 | – 606 | 846 |
| Az alkalmazottak továbbra is az irodába érkeznek (az alkalmazásnak 80% vCPU) kell lennie | 8:00 | 1280 | – 606 | 240 |
| Alacsony forgalmú | 9:00 | 270 | 0 | 240 |
| Alacsony forgalmú | 10:00 | 100 | 102 | 342 |
| Alacsony forgalmú | 11:00 | 50 | 132 | 474 |
| Alacsony forgalmú | 12:00 | 100 | 102 | 576 |
| Alacsony forgalmú | 13:00 | 100 | 102 | 678 |
| Alacsony forgalmú | 14:00 | 50 | 132 | 810 |
| Alacsony forgalmú | 15:00 | 100 | 102 | 912 |
| Alacsony forgalmú | 16:00 | 100 | 102 | 1014 |
| Kijelentkezési alkalmazottak (az alkalmazásnak 100% vCPU) kell lennie | 17:00 | 1600 | – 798 | 216 |
| Alacsony forgalmú | 18:00 | 270 | 0 | 216 |
| Alacsony forgalmú | 19:00 | 270 | 0 | 216 |
| Alacsony forgalmú | 20:00 | 50 | 132 | 348 |
| Alacsony forgalmú | 21:00 | 50 | 132 | 480 |
| Nincs forgalom | 22:00 | 0 | 162 | 642 |
| Nincs forgalom | 23:00 | 0 | 162 | 804 |

<sup>1</sup> az óránként felhasznált kreditek/kreditek a következővel egyenértékűek: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

Egy olyan D16s_v3 esetében, amely 16 vCPU és 64 GiB memóriával rendelkezik, az óradíj a $0,936/óra (havi $673,92), valamint a 16 vCPU és 64 GiB memóriával rendelkező B16ms esetében a ráta $0,794 óránként (havi $547,86). <b> Ez 15%-os megtakarítást eredményez.</b>

## <a name="q--a"></a>Kérdések és válaszok

### <a name="q-what-happens-when-my-credits-run-out"></a>K: mi történik, ha elfogynak a kreditek?
**A**: a kreditek kimerítése után a virtuális gép az alapkonfiguráció teljesítményére tér vissza.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>K: hogyan szerezhet be 135%-os alapteljesítményt egy virtuális gépről?

**A**: a 135% meg van osztva a 8 vCPU között, amelyek a virtuális gép méretét alkotják. Ha például az alkalmazás a 8 mag használatát használja a Batch-feldolgozásban, és mindegyik 4 vCPU 30%-os kihasználtsággal fut, akkor a virtuális gép CPU-teljesítményének teljes mennyisége 120%-kal egyenlő lenne.  Azt jelenti, hogy a virtuális gép a kiindulási teljesítménytől számított 15%-os különbözet alapján felépíti a kredit időt.  Ez azt is jelenti, hogy ha vannak olyan kreditek, amelyekkel ugyanazon virtuális gép 100%-át használhatja mind a 8 vCPU, így a virtuális gép maximális CPU-teljesítménye 800%.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>K: Hogyan figyelhető meg a kreditek egyenlege és a fogyasztásom?

Válasz: a **kredit** metrika segítségével megtekintheti, hogy a virtuális gép hány kreditet kapott, és a **ConsumedCredit** metrika azt mutatja, hogy a virtuális gép hány CPU-kreditet használ a banktól.    Ezeket a metrikákat a portál mérőszámok paneljéről vagy programozott módon, a Azure Monitor API-kon keresztül tekintheti meg.

További információ az Azure mérőszámok adatainak eléréséről: [a Microsoft Azure metrikáinak áttekintése](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>K: Hogyan történik a kreditek felhalmozódása és felhasználása?

**A**: a virtuális gépek felhalmozódása és a használati díjak úgy vannak beállítva, hogy egy virtuális gép, amely pontosan az alapszintű teljesítmény szintjén fut, sem a nettó, sem a feltört kreditek számától függ.  Ha a virtuális gép az alapteljesítményi szint alatt fut, a kreditek nettó növekedését fogja tartalmazni, amikor a virtuális gép a CPU-t az alapszintnél nagyobb mértékben használja fel.

**Példa**: üzembe helyezhetek egy virtuális gépet a kis idő és a részvételi B1ms mérete alapján. Ez a méret lehetővé teszi, hogy az alkalmazásom a vCPU akár 20%-át használja alaptervként, ami 0,2 kredit, amelyet akár percenként is használhatok.

Az alkalmazásom az alkalmazottak munkanapjának elején és végén foglalt, 7:00-9:00-kor és 4:00-18:00 között. A nap további 20 órájában az alkalmazásom általában üresjáratban van, és csak a vCPU 10%-át használja. A nem maximális óraszámban 0,2 kreditet keresünk, de percenként csak 0,1 kreditet használunk, így a virtuális gép a bank 0,1 x 60 = 6 kredit/óra lesz.  A 20 órán át, hogy én vagyok a csúcson, a bank 120 kreditet kap.  

Az alkalmazás csúcsidőben átlagosan 60%-os vCPU-kihasználtságot használok, de percenként 0,2 kreditet keresek, de percenként 0,6 kreditet használok, és a 100-as vagy 0,4 x 60 = 24 kreditet számítjuk 0,4 fel óránként. Napi 4 óra van használatban, ezért 4 x 24 = 96 kreditet számítunk fel a csúcsérték-használathoz.

Ha megszerezem a 120 kreditet, és kivonják a maximális időponthoz használt 96-kreditet, akkor naponta további 24 kreditet vehetek igénybe, amelyet más feltört tevékenységekhez is használhatok.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>K: Hogyan lehet kiszámítani a halmozott és felhasznált krediteket?

**A**: a következő képletet használhatja:

(Virtuális gép alapszintű CPU-kihasználtsága)/100 = kreditek bankja vagy használat percenként

például a fenti példában az alapkonfiguráció 20%, és ha a processzor 10%-át használja (20%-10%)/100 = 0,1 kredit percenként.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>K: támogatja a B sorozat a Premium Storage adatlemezeket?

Válasz **: igen**, az összes B sorozatú méret támogatja Premium Storage adatlemezeket.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>K: Miért van a fennmaradó kreditem 0-ra az újbóli üzembe helyezés vagy a Leállítás/indítás után?

**A** : Ha egy virtuális gépet újra üzembe helyeznek, és a virtuális gép egy másik csomópontra kerül, a halmozott kredit elvész. Ha a virtuális gép le van állítva/elindult, de továbbra is ugyanazon a csomóponton marad, a virtuális gép megőrzi a halmozott kreditet. Ha a virtuális gép egy csomóponton kezdi meg a friss állapotot, a kezdeti jóváírást kap, Standard_B8ms 240.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>K: mi történik, ha nem támogatott operációsrendszer-lemezképet telepítek a B1ls-on?

**A** : a B1ls csak a Linux-rendszerképeket támogatja, és ha egy másik operációsrendszer-lemezképet telepít, előfordulhat, hogy nem kapja meg a legjobb felhasználói élményt.

## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Számításoptimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)

Árképzési kalkulátor: [árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/)

További információ a lemezek típusairól: [lemezek típusai](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

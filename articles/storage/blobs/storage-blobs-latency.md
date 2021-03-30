---
title: Késés a blob Storage-ban – Azure Storage
description: A blob Storage-műveletek késésének megértése és mérése, valamint a blob Storage-alkalmazások kis késleltetésű kialakításának megtervezése.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75749726"
---
# <a name="latency-in-blob-storage"></a>Késés a Blob Storage-ban

A késés, esetenként a válaszidő, az az időtartam, ameddig egy alkalmazásnak meg kell várnia a kérés befejeződését. A késés közvetlenül befolyásolhatja az alkalmazás teljesítményét. Az alacsony késés gyakran fontos a hurokban lévő emberekkel kapcsolatos forgatókönyvek esetében, például a hitelkártyás tranzakciók lebonyolítása vagy a weblapok betöltése. Azok a rendszerek, amelyeknek a beérkező eseményeket nagy arányban kell feldolgozniuk, például telemetria-naplózási vagy IoT-események, kis késést is igényelnek. Ez a cikk azt ismerteti, hogyan lehet megérteni és mérni a blokkos Blobok műveleteinek késését, valamint azt, hogy miként tervezze meg alkalmazásait az alacsony késés érdekében.

Az Azure Storage két különböző teljesítményt kínál a blokkos Blobok esetében: prémium és standard. A prémium szintű blokk Blobok jelentősen alacsonyabb és konzisztens késéssel rendelkeznek, mint a standard blokkos Blobok nagy teljesítményű SSD-lemezeken keresztül. További információ: prémium szintű **teljesítmény-blokkoló blob Storage** az [Azure Blob Storage-ban: gyors elérésű, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Az Azure Storage késésének ismertetése

Az Azure Storage késése az Azure Storage-műveletek kérelmezési díjaival kapcsolatos. A kérések díjait a másodpercenkénti bemeneti/kimeneti műveletek (IOPS) is nevezik.

A kérelmek arányának kiszámításához először határozza meg, hogy az egyes kérelmek milyen hosszú ideig tartanak, majd számítsa ki, hogy másodpercenként hány kérelem legyen feldolgozható. Tegyük fel például, hogy egy kérelem 50 ezredmásodperc (MS) időt vesz igénybe. Egy olyan alkalmazás, amely egy függőben lévő olvasási vagy írási művelettel egyetlen szálat használ, legalább 20 IOPS kell elérnie (1 másodperc vagy 1000 MS/50 MS per kérelem). Elméletileg, ha a szálak száma két értékre van duplán, akkor az alkalmazásnak képesnek kell lennie a 40 IOPS elérésére. Ha az egyes szálak esetében az aszinkron olvasási vagy írási műveletek két értékre vannak duplázva, akkor az alkalmazásnak képesnek kell lennie 80 IOPS elérésére.

A gyakorlatban a kérelmek díjszabása nem mindig lineárisan méretezhető, mert az ügyfél a feladatütemezés, a környezet-váltás és így tovább. A szolgáltatás oldalán az Azure Storage-rendszer terhelése, a felhasznált adattárolók különbözősége, a más számítási feladatokból származó zaj, a karbantartási feladatok és egyéb tényezők miatt a késések változékonysága is lehetséges. Végül az ügyfél és a kiszolgáló közötti hálózati kapcsolat hatással lehet az Azure tárolási késésére a torlódás, az átirányítás vagy más fennakadások miatt.

Az Azure Storage-sávszélesség (más néven átviteli sebesség) a kérelmek arányára vonatkozik, és a kérések méretének (IOPS) szorzatával számítható ki. Tegyük fel például, hogy a 160-os kérelmek másodpercenkénti száma minden 256 KiB adatátviteli sebesség (40 960 KiB/s), illetve az 40 MiB másodpercenkénti teljesítménye.

## <a name="latency-metrics-for-block-blobs"></a>A blokkbeli Blobok késési mérőszámai

Az Azure Storage két késési metrikát biztosít a blokkos blobokhoz. Ezek a metrikák a Azure Portalban tekinthetők meg:

- A **végpontok közötti (E2E) késés** azt méri, hogy az Azure Storage mikor kapja meg a kérelem első csomagját, amíg az Azure Storage a válasz utolsó csomagjában megkapja az ügyfél-visszaigazolást.

- A **kiszolgáló késése** azt méri, hogy az Azure Storage mikor kapja meg a kérelem utolsó csomagját, amíg a válasz első csomagját vissza nem adja az Azure Storage-ból.

Az alábbi képen látható az **átlagos sikerességi E2E késése** és az **átlagos sikeres kiszolgáló késése** egy olyan számítási feladathoz, amely meghívja a `Get Blob` műveletet:

![A blob lekérése művelet késési mérőszámait bemutató képernyőkép](media/storage-blobs-latency/latency-metrics-get-blob.png)

Normál körülmények között kevés a különbség a végpontok közötti késés és a kiszolgáló késése között, ami azt mutatja, hogy a képen a minta számítási feladatok láthatók.

Ha áttekinti a végpontok közötti és a kiszolgáló késési metrikáit, és úgy találja, hogy a végpontok közötti késés jelentősen meghaladja a kiszolgáló késését, akkor vizsgálja meg és foglalkozzon a további késés forrásával.

Ha a végpontok közötti és a kiszolgáló késése hasonló, de kisebb késésre van szüksége, érdemes lehet áttelepíteni a prémium szintű blokk blob Storage-ba.

## <a name="factors-influencing-latency"></a>A késést befolyásoló tényezők

A késést befolyásoló fő tényező a művelet mérete. Hosszabb időt vesz igénybe a nagyobb műveletek végrehajtása, a hálózaton keresztül továbbított adatmennyiség és az Azure Storage által feldolgozott adatok mennyisége miatt.

Az alábbi ábrán a különböző méretek műveleteinek teljes ideje látható. Kis mennyiségű adat esetén a késési intervallum a kérelem kezelésének megkövetelése, és nem az adatátvitel. A késési intervallum csak kis mértékben növekszik, ahogy a művelet mérete nő (az alábbi ábrán 1 szerepel). Ahogy a művelet mérete tovább nő, az adatok átvitele több időt vesz igénybe, így a késések teljes időtartama a kérelmek kezelése és az adatátvitel között oszlik meg (az alábbi ábrán 2 jelölés szerepel). Nagyobb működési méretek esetén a késési intervallum majdnem kizárólag az adatátvitelre szolgál, és a kérelmek kezelése nagyrészt jelentéktelen (az alábbi ábrán látható 3).

![A művelet méretének teljes működési idejét ábrázoló képernyőfelvétel](media/storage-blobs-latency/operation-time-size-chart.png)

Az ügyfél-konfigurációs tényezők, például a Egyidejűség és a szálak is befolyásolhatják a késést. A teljes átviteli sebesség attól függ, hogy az adott időpontban hány tárolási kérelem kerül be a repülésbe, és hogy az alkalmazás hogyan kezeli a szálat. Az ügyfelek erőforrásai, beleértve a CPU-t, a memóriát, a helyi tárterületet és a hálózati adaptereket is, befolyásolhatják a késést.

Az Azure Storage-kérelmek feldolgozásához az ügyfél CPU-és memória-erőforrásai szükségesek. Ha az ügyfél nyomás alatt áll, mert egy kikapcsolt virtuális gép vagy valamilyen elszabadult folyamat van a rendszerben, az Azure Storage-kérelmek feldolgozásához kevesebb erőforrás áll rendelkezésre. Az ügyfél-erőforrások bármilyen tartalma vagy hiánya növeli a végpontok közötti késést a kiszolgáló késésének növekedése nélkül, így megnő a két metrika közötti különbség.

Ugyanilyen fontos az ügyfél és az Azure Storage közötti hálózati adapter és hálózati csatorna. A fizikai távolság önmagában jelentős tényező lehet, például ha az ügyfél virtuális gépe egy másik Azure-régióban vagy a helyszínen található. Más tényezők, például a hálózati ugrások, az ISP-Útválasztás és az internetes állapot befolyásolhatja a tárterület teljes késését.

A késés felméréséhez először létre kell hoznia a forgatókönyv alapszintű mérőszámait. Az alapkonfiguráció metrikái a teljes körű és a kiszolgáló késését biztosítják az alkalmazási környezet környezetében, a munkaterhelés profiljától, az alkalmazás konfigurációs beállításaitól, az ügyfél erőforrásaitól, a hálózati pipe-tól és más tényezőktől függően. Az alapkonfiguráció metrikáinak használatával könnyebben azonosítható a rendellenes és a normál körülmények. Az alapkonfiguráció metrikái lehetővé teszik a módosított paraméterek (például az alkalmazás konfigurációja vagy a virtuálisgép-méretek) hatásának megfigyelését is.

## <a name="next-steps"></a>Következő lépések

- [A blob Storage méretezhetőségi és teljesítménybeli céljai](scalability-targets.md)
- [A blob Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](storage-performance-checklist.md)

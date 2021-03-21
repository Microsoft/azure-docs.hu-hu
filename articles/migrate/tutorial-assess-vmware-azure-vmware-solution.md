---
title: A VMware virtuális gépek felmérése az Azure VMware-megoldásba (AVS) való Migrálás Azure Migrate
description: Ismerje meg, hogyan értékelheti a VMware virtuális gépeket az AVS-re való áttelepítéshez Azure Migrate Server Assessment használatával.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: c1c56edacbc777b5e8b53da588bc763201379964
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718828"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Oktatóanyag: VMware virtuális gépek felmérése az AVS-re való áttelepítéshez

Az Azure-ba való Migrálás részeként felméri a helyszíni számítási feladatokat a felhő készültségének mérésére, a kockázatok azonosítására, valamint a költségek és a bonyolultság megbecslésére.

Ebből a cikkből megtudhatja, hogyan értékelheti fel a felderített VMware virtuális gépeket (VM) az Azure VMware-megoldásba (AVS) való áttelepítéshez a Azure Migrate: Server Assessment Tool használatával. Az AVS egy felügyelt szolgáltatás, amely lehetővé teszi a VMware platform futtatását az Azure-ban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
- Értékelés futtatása a számítógép metaadatai és konfigurációs adatai alapján.
- Értékelés futtatása a teljesítményadatok alapján.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).



## <a name="prerequisites"></a>Előfeltételek

Mielőtt ezt az oktatóanyagot követve felmérje a gépeket az AVS-re való áttelepítéshez, győződjön meg róla, hogy felderítette az értékelendő gépeket:

- Az Azure Migrate berendezéssel rendelkező gépek felderítéséhez [kövesse ezt az oktatóanyagot](tutorial-discover-vmware.md). 
- Ha egy importált CSV-fájlt használó gépeket szeretne felderíteni, [kövesse ezt az oktatóanyagot](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Döntse el, hogy melyik értékelést szeretné futtatni


Döntse el, hogy szeretne-e értékelést használni a méretezési feltételek alapján, a helyszíni vagy a dinamikus teljesítményadatokat használó számítógép-konfigurációs adatok/metaadatok alapján.

**Értékelés** | **Részletek** | **Ajánlás**
--- | --- | ---
**Módosítás nélküli helyszíni** | Értékelés a számítógép konfigurációs adatok/metaadatok alapján.  | Az AVS-ben javasolt csomópont-méret a helyszíni virtuális gép méretétől függ, valamint a csomópont típusának, tárolási típusának és a meghibásodások által tolerálható beállítás értékelésében megadott beállításoknak.
**Teljesítményalapú** | Az összegyűjtött dinamikus teljesítményadatok alapján történő Értékelés. | Az AVS-ben javasolt csomópont-méret a CPU-és a memóriahasználat, valamint az értékelésben megadott beállításokkal, a tárolási típussal és a meghibásodások által tolerálható beállítással együtt.

> [!NOTE]
> Az Azure VMware-megoldás (AVS) értékelését csak VMware virtuális gépekhez lehet létrehozni.

## <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. A **kiszolgálók** lapon > **Windows-és Linux-kiszolgálók** területen kattintson a **kiszolgálók felmérése és migrálása** elemre.

   ![Az értékelés és a kiszolgálók áttelepítése gomb helye](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. **Azure Migrate: kiszolgáló értékelése**, kattintson az **értékelés** elemre.

1. A **kiszolgálók**  >  **felmérési típusának** értékelése területen válassza az **Azure VMware-megoldás (AVS)** lehetőséget.

1. A **felderítés forrása**:

    - Ha a berendezést használó gépeket észlelt, válassza a **Azure Migrate készülékről felderített gépek** lehetőséget.
    - Ha egy importált CSV-fájlt használó gépeket észlelt, válassza az **importált gépek** lehetőséget. 
    
1. Kattintson a **Szerkesztés** elemre az értékelési tulajdonságok áttekintéséhez.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Az értékelési beállítások kiválasztására szolgáló lap":::
 

1. Az **értékelés tulajdonságai**  >  **cél tulajdonságai**:

    - A **célhely** mezőben válassza ki azt az Azure-régiót, amelyre az áttelepítést szeretné végezni.
       - A méretre és a díjakra vonatkozó javaslatok a megadott helyen alapulnak.
   - A **tárolási típus** alapértelmezett értéke **vSAN**. Ez az AVS Private Cloud alapértelmezett tárolási típusa.
   - A **fenntartott példányok** jelenleg nem támogatottak az AVS-csomópontok esetében.
1. **Virtuális gép mérete**:
    - A **csomópont típusa** alapértelmezés szerint **AV36**. Azure Migrate a virtuális gépek AVS-re való áttelepítéséhez szükséges csomópontok csomópontját javasolja.
    - A **tranzakciós beállítások, RAID szint** területen válassza ki a nem tolerálható és a RAID-kombinációt.  A kiválasztott TRANZAKCIÓs lehetőség a helyszíni virtuálisgép-lemezre vonatkozó követelményével együtt meghatározza az AVS-ben szükséges teljes vSAN-tárolót.
    - A **CPU-előfizetésben** határozza meg az AVS-csomópont egyik fizikai magját társított virtuális magok arányát. Az 4:1-nál nagyobb túllépés a teljesítmény romlását okozhatja, de a webkiszolgáló típusú számítási feladatokhoz is használható.
    - A **memória**-túlterhelési tényező mezőben adja meg a fürtön belüli véglegesítés arányát. Az 1 érték a 100%-os memória-használatot jelöli, a 0,5 például a 50%, a 2 pedig a rendelkezésre álló memória 200%-át használja. 0,5 és 10 közötti értékeket csak egy tizedesjegyre adhat hozzá.
    - A **dedupe és a tömörítési faktor** mezőben adja meg a számítási feladatokhoz tartozó várható dedupe és Compression faktor értéket. A tényleges érték a helyszíni vSAN vagy a tárolási konfigurációból szerezhető be, és ez a munkaterhelés függvényében változhat. A 3 érték azt jelenti, hogy a 300GB lemez csak a 100 GB tárolót használja. Az 1 érték a dedupe vagy a Compression értéket sem fogja jelenteni. Csak 1 és 10 közötti értékeket adhat hozzá egy tizedes tört értékhez.
1. **Csomópont mérete**: 
    - A **méretezési feltétel** területen válassza ki, hogy az értékelést statikus metaadatokon vagy a teljesítményen alapuló adatokon szeretné-e alapozni. Ha teljesítményadatokat használ:
        - A **teljesítmény előzményeiben** adja meg az adatok időtartamát, amely alapján az értékelést alapozni szeretné
        - A **percentilis kihasználtsága** mezőben határozza meg a teljesítmény mintához használni kívánt százalékos értéket. 
    - A **Comfort Factor (kényelmi tényező**) mezőben adja meg az értékelés során használni kívánt puffert. Ez olyan problémákhoz vezetett, mint például a szezonális használat, a rövid teljesítménybeli előzmények és a jövőbeli használat várható növekedése. Ha például két kényelmi tényezőt használ:
    
        **Összetevő** | **Hatékony kihasználtság** | **Komfort tényező hozzáadása (2,0)**
        --- | --- | ---
        Cores | 2  | 4
        Memória | 8 GB | 16 GB  

1. A **díjszabásban**:
    - A szolgáltatásban regisztrált [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/) a kiszolgáló értékelése alapján megbecsüli az ajánlat költségeit.
    - A **Pénznem** területen válassza ki a fiókja számlázási pénznemét.
    - A **kedvezmény (%)** területen adja meg az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%.

1. Ha módosítja a módosításokat, kattintson a **Save (Mentés** ) gombra.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Kiértékelés tulajdonságai":::

1. A **kiszolgálók értékelése** területen kattintson a **tovább** gombra.

1. Az értékelési név kiértékeléséhez a **gépek kiválasztása**  >   > adja meg az értékelés nevét. 
 
1. A **válasszon ki vagy hozzon létre egy csoportot** > válassza az **új létrehozása** elemet, és adja meg a csoport nevét. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Virtuális gépek felvétele egy csoportba":::
 
1. Válassza ki a készüléket, és válassza ki a csoportba felvenni kívánt virtuális gépeket. Ezután kattintson a **Tovább** gombra.

1. A **felülvizsgálat** és Értékelés létrehozása lapon tekintse át az értékelés részleteit, majd kattintson az **Értékelés létrehozása** elemre a csoport létrehozásához és az értékelés futtatásához.

    > [!NOTE]
    > A teljesítmény-alapú felmérések esetében javasoljuk, hogy várjon legalább egy napot a felderítés megkezdése után a felmérés létrehozása előtt. Ezzel a teljesítménnyel kapcsolatos adatok nagyobb megbízhatósággal gyűjthetők. Ideális esetben a felderítés megkezdése után várjon a megadott teljesítménybeli időtartamra (nap/hét/hónap) a magas megbízhatóságú minősítéshez.

## <a name="review-an-assessment"></a>Értékelés áttekintése

Egy AVS-értékelés leírja:

- AVS-készültség: azt határozza meg, hogy a helyszíni virtuális gépek alkalmasak-e az Azure VMware-megoldásra (AVS) való áttelepítésre.
- AVS-csomópontok száma: a virtuális gépek futtatásához szükséges AVS-csomópontok becsült száma.
- Használat az AVS-csomópontok között: a processzor, a memória és a tárhely kihasználtsága az összes csomóponton keresztül.
    - A kihasználtság a következő fürtszolgáltatási (például a vCenter Server, a NSX Manager (nagy), az NSX Edge, a HCX üzembe helyezése esetén a HCX-kezelő és a IX-es készülék, valamint a 44vCPU (11 CPU), a RAM-és a 75GB a tömörítés és a lekettőzés előtt történő tárolását is magában foglalja. 
- Havi költségbecslés: a helyszíni virtuális gépeket futtató összes Azure VMware Solution (AVS) csomópont becsült havi költségei.

## <a name="view-an-assessment"></a>Kiértékelés megtekintése

Értékelés megtekintése:

1. A **kiszolgálók**  >  **Azure Migrate: kiszolgáló értékelése** területen kattintson az **értékelések** melletti számra.

1. Az **Értékelések** területen válasszon ki egy kiértékelést a megnyitáshoz. Példa (csak becslések és költségek például): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS Assessment – összefoglalás":::

1. Tekintse át az értékelés összegzését. Szerkesztheti az értékelési tulajdonságokat is, vagy újraszámíthatja az értékelést.
 

### <a name="review-readiness"></a>Készültség áttekintése

1. Kattintson az **Azure-készültség** elemre.
2. Az **Azure készültségi** területén tekintse át a virtuális gép állapotát.

    - **AVS-re kész**: a gép áttelepíthető az Azure AVS-be, bármilyen módosítás nélkül. A gép AVS-ben indul el, teljes AVS-támogatással.
    - **Feltételekkel kész**: a gép kompatibilitási problémákba ütközhet a jelenlegi vSphere-verzióval. Előfordulhat, hogy a telepített VMware-eszközökre vagy egyéb beállításokra van szükség, mielőtt az AVS-ben teljes funkcionalitással rendelkezik.
    - **Nem áll készen az AVS-re**: a virtuális gép nem indul el az AVS-ben. Ha például egy helyszíni VMware virtuális gépnek van egy külső eszköze (például CD-ROM), amelyhez VMware VMotion használ, a VMotion művelet meghiúsul.
 - **Készültség ismeretlen**: Azure Migrate nem tudta megállapítani a gépek készültségét, mert nem áll rendelkezésre elegendő, a helyszíni környezetből gyűjtött metaadatok.

3. Tekintse át a javasolt eszközt.

    - VMware HCX vagy Enterprise: VMware-es gépek esetén a VMware Hybrid Cloud Extension (HCX) megoldás a javasolt áttelepítési eszköz, amellyel áttelepítheti a helyszíni számítási feladatokat az Azure VMware-megoldás (AVS) privát felhőbe. tudj meg többet.
    - Ismeretlen: A CSV-fájllal importált gépek esetében az alapértelmezett migrálási eszköz ismeretlen. A VMware-gépek esetében azonban javasolt a VMware Hybrid Cloud Extension (HCX) megoldás használata.
4. Kattintson egy AVS-készültségi állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-estimates"></a>Költségbecslések áttekintése

Az értékelés összegzése az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit mutatja. 

1. Tekintse át a havi teljes költséget. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban.

    - A költségbecslés az összes virtuális gép erőforrás-követelményeit figyelembe véve szükséges AVS-csomópontok számától függ.
    - Mivel az AVS díjszabása csomópontként történik, a teljes költség nem rendelkezik számítási költséggel és a tárolási költség eloszlásával.
    - A költségbecslés a helyszíni virtuális gépek AVS-ben való futtatására szolgál. Az AVS Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Tekintse át a havi tárolási becsléseket. A nézet a kiértékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolási lemezek felosztásával. 
3. A részletezést lenyomva megtekintheti az adott virtuális gépek részletes költségeit.

### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

A kiszolgáló értékelése megbízhatósági minősítést rendel a teljesítmény-alapú értékelésekhez. Az értékelés az egyik csillagból (legalacsonyabb) és öt csillagra (a legmagasabbra) mutat.

![Megbízhatósági minősítés](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

A megbízhatósági minősítés segít megbecsülni a méretre vonatkozó ajánlások megbízhatóságát az értékelés során. A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.

> [!NOTE]
> A megbízhatósági minősítések nem vannak hozzárendelve, ha CSV-fájl alapján hoz létre értékelést.

A megbízhatósági minősítések a következők.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További](concepts-assessment-calculation.md#confidence-ratings-performance-based) információ a megbízhatósági minősítésekről.

## <a name="next-steps"></a>Következő lépések

- A [függőségi leképezést](concepts-dependency-visualization.md)használó számítógép-függőségek keresése.
- [Ügynök](how-to-create-group-machine-dependencies-agentless.md) [nélküli vagy ügynök-alapú](how-to-create-group-machine-dependencies.md) függőség leképezésének beállítása.

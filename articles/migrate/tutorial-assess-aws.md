---
title: Az AWS-példányok értékelése az Azure-ba való Migrálás Azure Migrate
description: Ismerje meg, hogyan érheti el az AWS-példányokat az Azure-ba való Migrálás Azure Migrate használatával.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 942adacf5b9a3b1b717e28def5752591ef22ec52
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782333"
---
# <a name="tutorial-assess-aws-instances-for-migration-to-azure"></a>Oktatóanyag: AWS-példányok értékelése az Azure-ba való áttelepítéshez

Az Azure-ba való Migrálás részeként felméri a helyszíni számítási feladatokat a felhő készültségének mérésére, a kockázatok azonosítására, valamint a költségek és a bonyolultság megbecslésére.

Ez a cikk bemutatja, hogyan értékelheti Amazon Web Services (AWS) példányait az Azure-ba való áttelepítéshez a Azure Migrate: Discovery and Assessment Tool használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
- Értékelés futtatása kiszolgálói metaadatok és konfigurációs információk alapján.
- Értékelés futtatása a teljesítményadatok alapján.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené az oktatóanyag lépéseit, végezze el az első oktatóanyagot ebben a sorozatban, hogy [felderítse a helyszíni leltárt](tutorial-discover-aws.md). 
- Győződjön meg arról, hogy az AWS-példányok nem futnak a Windows Server 2003 vagy a SUSE Linux rendszeren. Ezek a kiszolgálók nem támogatják az értékelést.


## <a name="decide-which-assessment-to-run"></a>Döntse el, hogy melyik értékelést szeretné futtatni


Döntse el, hogy szeretne-e értékelést használni a méretezési feltételek alapján, a helyszíni vagy a dinamikus teljesítményadatokat használó kiszolgálói konfigurációs adatok/metaadatok alapján.

**Értékelés** | **Részletek** | **Ajánlás**
--- | --- | ---
**Módosítás nélküli helyszíni** | Értékelés a kiszolgáló konfigurációs adatok/metaadatok alapján.  | Az ajánlott Azure-beli virtuálisgép-méret a helyszíni virtuális gép méretétől függ.<br/><br> Az ajánlott Azure-lemez típusa azon alapul, hogy mit választott ki az értékelés tárolási típusa beállításban.
**Teljesítményalapú** | Az összegyűjtött dinamikus teljesítményadatok alapján történő Értékelés. | Az ajánlott Azure-beli virtuális gépek mérete a processzor-és memóriahasználat adatain alapul.<br/><br/> Az ajánlott lemez típusa a helyszíni lemezek IOPS és átviteli sebessége alapján történik.

## <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Az **Áttekintés** oldalon > **Windows, Linux és SQL Server**, kattintson a **kiszolgálók értékelése és migrálása** elemre.

   ![Az értékelés és a kiszolgálók áttelepítése gomb helye](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. **Azure Migrate: felderítés és értékelés**, kattintson az **értékelés** elemre.

    ![Az értékelés gomb helye](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. A **kiszolgálók**  >  **felmérési típusának** értékelése területen válassza az **Azure virtuális gép** lehetőséget.
4. A **felderítés forrása**:

    - Ha a berendezést használó kiszolgálókat észlelt, válassza ki **a Azure Migrate készülékről felderített kiszolgálókat**.
    - Ha egy importált CSV-fájllal rendelkező kiszolgálókat észlelt, válassza az **importált kiszolgálók** elemet. 
    
1. Kattintson a **Szerkesztés** elemre az értékelési tulajdonságok áttekintéséhez.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="A Szerkesztés gomb helye az értékelési tulajdonságok áttekintéséhez":::

1. Az **értékelés tulajdonságai**  >  **cél tulajdonságai**:
    - A **célhely** mezőben válassza ki azt az Azure-régiót, amelyre az áttelepítést szeretné végezni.
        - A méretre és a díjakra vonatkozó javaslatok a megadott helyen alapulnak. Ha a célhelyet az alapértelmezett értékre módosítja, a rendszer kérni fogja a **fenntartott példányok** és a virtuálisgép- **sorozatok** megadását.
        - Azure Government Az értékeléseket az [alábbi régiókban](migrate-support-matrix.md#supported-geographies-azure-government) lehet megcélozni
    - A **tárolási típus mezőben**
        - Ha teljesítmény-alapú adatmennyiséget szeretne használni az értékelésben, válassza az **automatikus** lehetőséget Azure MIGRATE a IOPS és az átviteli sebesség alapján a tárolási típust ajánljuk.
        - Másik lehetőségként válassza ki azt a tárolási típust, amelyet a virtuális géphez szeretne használni a Migrálás során.
    - A **fenntartott példányok** területen válassza ki, hogy a virtuális gép tartalék példányait szeretné-e használni az áttelepítéskor.
        - Ha fenntartott példány használatát választja, a "**kedvezmény (%)** vagy a **virtuális gép üzemidő** nem adható meg. 
        - [További információ](https://aka.ms/azurereservedinstances).
 1. **Virtuális gép mérete**:
     - A **méretezési feltétel** területen válassza ki, hogy szeretné-e alapozni az értékelést a kiszolgálói konfigurációs adatokra/metaadatokra vagy a teljesítmény alapú adatokra. Ha teljesítményadatokat használ:
        - A **teljesítmény előzményeiben** adja meg az adatok időtartamát, amely alapján az értékelést alapozni szeretné
        - A **percentilis kihasználtsága** mezőben határozza meg a teljesítmény mintához használni kívánt százalékos értéket. 
    - A virtuálisgép- **sorozat** mezőben határozza meg, hogy milyen Azure-beli virtuálisgép-sorozatot szeretne figyelembe venni.
        - Ha teljesítmény-alapú értékelést használ, a Azure Migrate egy értéket javasol Önnek.
        - Szükség szerint módosítsa a beállításokat. Ha például nem rendelkezik olyan éles környezettel, amely az Azure-beli sorozatú virtuális gépeket igényli, kizárhatja a sorozatot a sorozatok listájáról.
    - A **Comfort Factor (kényelmi tényező**) mezőben adja meg az értékelés során használni kívánt puffert. Ez olyan problémákhoz vezetett, mint például a szezonális használat, a rövid teljesítménybeli előzmények és a jövőbeli használat várható növekedése. Ha például két kényelmi tényezőt használ:
    
        **Összetevő** | **Hatékony kihasználtság** | **Komfort tényező hozzáadása (2,0)**
        --- | --- | ---
        Cores | 2  | 4
        Memória | 8 GB | 16 GB
   
1. A **díjszabásban**:
    - Az **ajánlat** mezőben válassza ki az [Azure-ajánlatot](https://azure.microsoft.com/support/legal/offer-details/) , ha regisztrálva van. Az értékelés az ajánlat költségeit becsüli.
    - A **Pénznem** területen válassza ki a fiókja számlázási pénznemét.
    - A **kedvezmény (%)** területen adja meg az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%.
    - A **virtuális gép üzemidő** területen adja meg a virtuális gépek által futtatandó időtartamot (naponta, havonta/órában).
        - Ez olyan Azure-beli virtuális gépek esetében hasznos, amelyek nem futnak folyamatosan.
        - A becsült érték a megadott időtartamon alapul.
        - Az alapértelmezett érték havi 31 nap/napi 24 óra.
    - Az **EA-előfizetés** területen határozza meg, hogy szeretné-e a NAGYVÁLLALATI szerződés (EA) előfizetési kedvezményt figyelembe venni a költségbecslés során. 
    - A **Azure Hybrid Benefit** területen válassza ki, hogy már rendelkezik-e Windows Server-licenccel. Ha így tesz, és a Windows Server-előfizetések aktív frissítési garanciával rendelkezik, akkor az Azure-ba való licencek esetén a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) is alkalmazható.

1. Ha módosítja a módosításokat, kattintson a **Save (Mentés** ) gombra.

    ![Kiértékelés tulajdonságai](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. A **kiszolgálók értékelése** > kattintson a **tovább** gombra.

1. A **kiszolgálók kiválasztása az**  >  **értékelés nevének** kiértékeléséhez > adja meg az értékelés nevét. 

1. A **válasszon ki vagy hozzon létre egy csoportot** > válassza az **új létrehozása** elemet, és adja meg a csoport nevét. 
    
1. Válassza ki a készüléket, és válassza ki a csoportba felvenni kívánt virtuális gépeket. Ezután kattintson a **Tovább** gombra.

1. A **felülvizsgálat** és Értékelés létrehozása lapon tekintse át az értékelés részleteit, majd kattintson az **Értékelés létrehozása** elemre a csoport létrehozásához és az értékelés futtatásához.

1. Az értékelés létrehozása után tekintse meg a **kiszolgálók**  >  **Azure Migrate: felderítési és értékelési**  >  **értékelések**.

1. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.
    > [!NOTE]
    > A teljesítmény-alapú felmérések esetében javasoljuk, hogy várjon legalább egy napot a felderítés megkezdése után a felmérés létrehozása előtt. Ezzel a teljesítménnyel kapcsolatos adatok nagyobb megbízhatósággal gyűjthetők. Ideális esetben a felderítés megkezdése után várjon a megadott teljesítménybeli időtartamra (nap/hét/hónap) a magas megbízhatóságú minősítéshez.

## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: azt, hogy a virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: A lemezes tárolás becsült költségei a migrálás után.

Értékelés megtekintése:

1. **Windows, Linux és SQL Server**  >  **Azure Migrate esetén: felderítés és értékelés**, kattintson az **értékelések** melletti számra.
2. Az **Értékelések** területen válasszon ki egy kiértékelést a megnyitáshoz. Példa (csak becslések és költségek például): 

    ![Kiértékelés összegzése](./media/tutorial-assess-aws/assessment-summary.png)

3. Tekintse át az értékelés összegzését. Szerkesztheti az értékelési tulajdonságokat is, vagy újraszámíthatja az értékelést.
 
 
### <a name="review-readiness"></a>Készültség áttekintése

1. Kattintson az **Azure-készültség** elemre.
2. Az **Azure készültségi** területén tekintse át a virtuális gép állapotát:
    - **Készen áll az Azure-ra**: használatban van, ha a Azure MIGRATE a virtuális gépek méretét és a költséghatékonyságot javasolja az értékelésben szereplő virtuális gépek esetében.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a** problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha az adatelérhetőségi problémák miatt Azure Migrate nem tudja felmérni a készültséget.

3. Válasszon ki egy **Azure-kompatibilitási** állapotot. Megtekintheti a VM-készültség részleteit. A virtuális gép részleteit is megtekintheti, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-estimates"></a>Költségbecslések áttekintése

Az értékelés összegzése az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit mutatja. 

1. Tekintse át a havi teljes költséget. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban.

    - A költségbecslés a kiszolgálók, a lemezek és a hozzá tartozó tulajdonságok méretére vonatkozó javaslatok alapján történik.
    - A becsült havi számítási és tárolási költségek jelennek meg.
    - A költségbecslés a helyszíni virtuális gépek Azure-beli virtuális gépeken való futtatására szolgál. A becslés nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Tekintse át a havi tárolási költségeket. A nézet a kiértékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolási lemezek felosztásával. 
3. A részletezést lenyomva megtekintheti az adott virtuális gépek részletes költségeit.

### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Azure Migrate megbízhatósági minősítést rendel a teljesítmény-alapú értékelésekhez. Az értékelés az egyik csillagból (legalacsonyabb) és öt csillagra (a legmagasabbra) mutat.

![Megbízhatósági minősítés](./media/tutorial-assess-aws/confidence-rating.png)

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

- Függőségi [leképezést](concepts-dependency-visualization.md)használó kiszolgálói függőségek keresése.
- [Ügynök-alapú](how-to-create-group-machine-dependencies.md) függőség leképezésének beállítása.

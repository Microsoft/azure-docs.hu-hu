---
title: AVS-Értékelés létrehozása a Azure Migratekal | Microsoft Docs
description: Leírja, hogyan lehet AVS-értékelést létrehozni Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786600"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Azure VMware-megoldás (AVS) értékelésének létrehozása

Ez a cikk bemutatja, hogyan hozhat létre Azure VMware-megoldási (AVS-) értékelést a VMware-környezetben található helyszíni kiszolgálókhoz a Azure Migrate: Discovery and Assessment használatával.

[Azure Migrate](migrate-services-overview.md) segítségével áttelepítheti az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártói (ISV) ajánlatokhoz.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](./create-manage-projects.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Discovery and Assessment eszközt.
- Értékelés létrehozásához létre kell hoznia egy Azure Migrate készüléket a [VMware](how-to-set-up-appliance-vmware.md)-hez, amely felderíti a helyszíni kiszolgálókat, és metaadatokat és teljesítményadatokat küld a Azure Migrate: felderítés és Értékelés. [További információ](migrate-appliance.md).
- [A kiszolgálói metaadatokat](./tutorial-discover-import.md) vesszővel tagolt (CSV) formátumban is importálhatja.


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Az Azure VMware-megoldás (AVS) értékelésének áttekintése

Háromféle értékelést hozhat létre Azure Migrate használatával: felderítés és Értékelés.

***Értékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. A helyszíni kiszolgálókat felhasználhatja [VMware](how-to-set-up-appliance-vmware.md) -és [Hyper-V](how-to-set-up-appliance-hyper-v.md) környezetben, valamint [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) az Azure-beli virtuális gépekre való áttelepítéshez ezzel az értékelési típussal.
**Azure SQL** | A helyszíni SQL Server-kiszolgálók VMware-környezetből Azure SQL Database vagy az Azure SQL felügyelt példányba való átköltöztetésének értékelése.
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. A helyszíni kiszolgálókat a [VMware-környezetben](how-to-set-up-appliance-vmware.md) is értékelheti, ha ezt az értékelési típust használó Azure VMware-megoldásra (AVS) kíván áttelepíteni. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Az Azure VMware-megoldás (AVS) értékelését csak VMware-környezetben található kiszolgálókhoz lehet létrehozni.


Az Azure VMware Solution (AVS) felmérések létrehozásához két típusú méretezési feltétel használható:

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A helyszíni kiszolgálók összegyűjtött teljesítmény-adatain alapuló értékelések. | **Ajánlott csomópont mérete**: a CPU és a memória kihasználtsági adatai, valamint a csomópont típusa, a tárolási típus és a pénzügyi tranzakciós beállítás alapján, amelyet az értékeléshez választott.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott csomópont mérete**: a helyszíni kiszolgáló mérete alapján, valamint a csomópont típusát, a tárolási típust és a tranzakciós beállítást, amelyet az értékeléshez választott ki.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Azure VMware-megoldás (AVS) értékelésének futtatása

1.  Az **Áttekintés** oldalon > **Windows, Linux és SQL Server**, kattintson a **kiszolgálók értékelése és migrálása** elemre.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="A Azure Migrate áttekintő lapja":::

1. **Azure Migrate: felderítés és értékelés**, kattintson az **értékelés** elemre.

   ![Értékelés helye gomb](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. A **kiszolgálók**  >  **felmérési típusának** értékelése területen válassza az **Azure VMware-megoldás (AVS)** lehetőséget.

1. A **felderítés forrása**:

    - Ha a berendezést használó kiszolgálókat észlelt, válassza ki **a Azure Migrate készülékről felderített kiszolgálókat**.
    - Ha egy importált CSV-fájllal rendelkező kiszolgálókat észlelt, válassza az **importált kiszolgálók** elemet. 
    
1. Kattintson a **Szerkesztés** elemre az értékelési tulajdonságok áttekintéséhez.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Az értékelési beállítások kiválasztására szolgáló lap":::
 

1. Az **értékelés tulajdonságai**  >  **cél tulajdonságai**:

    - A **célhely** mezőben válassza ki azt az Azure-régiót, amelyre az áttelepítést szeretné végezni.
       - A méretre és a díjakra vonatkozó javaslatok a megadott helyen alapulnak.
   - A **tárolási típus** alapértelmezett értéke **vSAN**. Ez az AVS Private Cloud alapértelmezett tárolási típusa.
   - A **fenntartott példányok** jelenleg nem támogatottak az AVS-csomópontok esetében.
1. **Virtuális gép mérete**:
    - A **csomópont típusa** alapértelmezés szerint **AV36**. Azure Migrate a kiszolgálók AVS-re való átirányításához szükséges csomópontok csomópontját javasolja.
    - A **tranzakciós beállítások, RAID szint** területen válassza ki a nem tolerálható és a RAID-kombinációt.  A kiválasztott TRANZAKCIÓs lehetőség a helyszíni kiszolgáló lemezének követelményével együtt meghatározza az AVS-ben szükséges teljes vSAN-tárolót.
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
    - Az **ajánlatban** megjelenik a regisztrált [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/) . Az értékelés az ajánlat költségeit becsüli.
    - A **Pénznem** területen válassza ki a fiókja számlázási pénznemét.
    - A **kedvezmény (%)** területen adja meg az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%.

1. Ha módosítja a módosításokat, kattintson a **Save (Mentés** ) gombra.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Kiértékelés tulajdonságai":::

1. A **kiszolgálók értékelése** területen kattintson a **tovább** gombra.

1. A **kiszolgálók kiválasztása az**  >  **értékelés nevének** kiértékeléséhez > adja meg az értékelés nevét. 
 
1. A **válasszon ki vagy hozzon létre egy csoportot** > válassza az **új létrehozása** elemet, és adja meg a csoport nevét. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Kiszolgálók hozzáadása egy csoporthoz":::
 
1. Válassza ki a készüléket, és válassza ki a csoportba felvenni kívánt kiszolgálókat. Ezután kattintson a **Tovább** gombra.

1. A **felülvizsgálat** és Értékelés létrehozása lapon tekintse át az értékelés részleteit, majd kattintson az **Értékelés létrehozása** elemre a csoport létrehozásához és az értékelés futtatásához.

    > [!NOTE]
    > A teljesítmény-alapú felmérések esetében javasoljuk, hogy várjon legalább egy napot a felderítés megkezdése után a felmérés létrehozása előtt. Ezzel a teljesítménnyel kapcsolatos adatok nagyobb megbízhatósággal gyűjthetők. Ideális esetben a felderítés megkezdése után várjon a megadott teljesítménybeli időtartamra (nap/hét/hónap) a magas megbízhatóságú minősítéshez.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Azure-beli VMware-megoldás (AVS) értékelésének áttekintése

Az Azure VMware-megoldás (AVS) értékelése a következőket ismerteti:

- **Azure VMware-megoldás (AVS) készültsége**: azt határozza meg, hogy a helyszíni kiszolgálók alkalmasak-e az Azure VMware-megoldásba (AVS) való áttelepítésre.
- **AVS-csomópontok száma**: a kiszolgálók futtatásához szükséges AVS-csomópontok becsült száma.
- **Használat az AVS-csomópontok között**: a processzor, a memória és a tárhely kihasználtsága az összes csomóponton keresztül.
    - A kihasználtság a következő fürtszolgáltatási (például a vCenter Server, a NSX Manager (nagy), az NSX Edge és a HCX üzembe helyezése esetén is magában foglalja az első tényezőket, ha a HCX-kezelő és a IX-es berendezés is a ~ 44vCPU (11 CPU), a RAM és a 75GB 722GB a tömörítés és a deduplikálás előtt.
- **Havi költségbecslés**: a helyszíni virtuális gépeket futtató összes Azure VMware Solution (AVS) csomópont becsült havi költségei.


### <a name="view-an-assessment"></a>Kiértékelés megtekintése

1. **Windows, Linux és SQL Server**  >  **Azure Migrate: felderítés és értékelés**, kattintson a * * Azure VMware megoldás * * elem melletti számra.

1. Az **Értékelések** területen válasszon ki egy kiértékelést a megnyitáshoz. Példa (csak becslések és költségek például): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS Assessment – összefoglalás":::

1. Tekintse át az értékelés összegzését. Szerkesztheti az értékelési tulajdonságokat is, vagy újraszámíthatja az értékelést.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Az Azure VMware-megoldás (AVS) készültségének áttekintése

1. Az **Azure készültségi** területén ellenőrizze, hogy a kiszolgálók készen állnak-e az AVS-re való áttelepítésre.

2. Tekintse át a kiszolgáló állapotát:
    - **AVS-re kész**: a kiszolgáló áttelepíthető az Azure-ba (AVS) módosítás nélkül. A teljes AVS-támogatással indul az AVS-ben.
    - Felmerülő **feltételek**: a kiszolgáló kompatibilitási problémákba ütközhet a jelenlegi vSphere-verzióval, valamint az esetlegesen vmware-eszközök és egyéb beállítások megkövetelésével, mielőtt a kiszolgáló teljes funkcionalitása elérhető legyen az AVS-ben.
    - **Nem áll készen az AVS-re**: a kiszolgáló nem fog elindulni az AVS-ben. Ha például a helyszíni kiszolgáló külső eszközt csatlakoztat, például CD-ROM-ot, a VMotion művelet sikertelen lesz (ha a VMware VMotiont használja).
    - **Felkészültség ismeretlen**: Azure Migrate nem tudta meghatározni a kiszolgáló készültségét, mert nem áll rendelkezésre elegendő, a helyszíni környezetből gyűjtött metaadatok.

3. Tekintse át a javasolt eszközt:
    - **VMware HCX vagy Enterprise**: VMware-kiszolgálókhoz a VMware Hybrid Cloud Extension (HCX) megoldás a javasolt áttelepítési eszköz, amellyel áttelepítheti a helyszíni számítási feladatokat az Azure VMware-megoldás (AVS) privát felhőbe. [További információk](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Ismeretlen**: a CSV-fájlon keresztül importált kiszolgálók esetében az alapértelmezett áttelepítési eszköz ismeretlen. A VMware-kiszolgálók esetében azonban javasolt a VMware Hybrid Cloud Extension (HCX) megoldás használata. 

4. Kattintson egy **AVS-készültségi** állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>Költségadatok áttekintése

Ez a nézet a kiszolgálók Azure VMware-megoldásban (AVS) való futtatásának becsült költségeit jeleníti meg.

1. Tekintse át a havi teljes költséget. A kiértékelt csoportban lévő kiszolgálók költségei összesítve jelennek meg. 

    - A költségbecslés a teljes összes kiszolgáló erőforrás-követelményeit figyelembe véve szükséges AVS-csomópontok számától függ.
    - Mivel az Azure VMware-megoldás (AVS) díjszabása csomópontos, a teljes költség nem rendelkezik számítási költséggel és a tárolási költség eloszlásával.
    - A költségbecslés a helyszíni kiszolgálók AVS-ben való futtatására szolgál. Az AVS Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.
    
2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.

3. Megtekintheti az egyes kiszolgálók részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Ha teljesítmény-alapú értékeléseket futtat, a rendszer megbízhatósági minősítést rendel az értékeléshez.

![Megbízhatósági minősítés](./media/how-to-create-assessment/confidence-rating.png)

- A rendszer kinyeri az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést.
- A megbízhatósági minősítés segít megbecsülni az értékelés által biztosított méretre vonatkozó ajánlások megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.
- A teljesítmény-alapú méretezés érdekében az AVS-értékeléseknek a processzor és a kiszolgáló memóriájának kihasználtsági adataira van szükségük. A rendszer a következő teljesítményadatokat gyűjti, de nem használja az AVS-értékelések méretezési javaslataiban:
  - A lemez IOPS és adatátviteli adatok a kiszolgálóhoz csatolt minden lemezhez.
  - A hálózati I/O-műveletek a kiszolgálóhoz csatlakoztatott összes hálózati adapter teljesítmény-alapú méretezésének kezeléséhez.

Az értékelés megbízhatósági minősítése a következő.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További](concepts-azure-vmware-solution-assessment-calculation.md) információ a teljesítményadatok szolgáltatásról 


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan hozhat létre nagy megbízhatóságú csoportokat a [függőségi leképezés](how-to-create-group-machine-dependencies.md) használatával.
- [További](concepts-azure-vmware-solution-assessment-calculation.md) információ az AVS-értékelések kiszámításáról.
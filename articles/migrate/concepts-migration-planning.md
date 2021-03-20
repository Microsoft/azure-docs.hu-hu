---
title: Áttelepítési terv készítése Azure Migrate
description: Útmutatást nyújt az áttelepítési terv létrehozásához Azure Migrate használatával.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ef916e0e8b32c96382a731d4a307e2b2a98ba1ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753858"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Migrálási terv létrehozása az Azure Migrate-tel

Ezt a cikket követve felépítheti az Azure-ba történő áttelepítési tervet [Azure Migrate](migrate-services-overview.md)használatával. 

## <a name="define-cloud-migration-goals"></a>Felhőalapú áttelepítési célok meghatározása

Mielőtt elkezdené, megértette és kiértékelheti a felhőre való áttérés [motivációját](/azure/cloud-adoption-framework/strategy/motivations) , és hozzájárulhat a sikeres üzleti eredményekhez. Ahogy azt a [Cloud bevezetési keretrendszere](/azure/cloud-adoption-framework)is ismerteti, számos eseményindító és eredmény szerepel.   

**Üzleti esemény** | **Áttelepítési eredmény**
--- | ---
Adatközpont bezárása | Költségek 
Egyesítés, beszerzés vagy elidegenítés | A szállítói/technikai komplexitás csökkentése
A tőke költségeinek csökkentése | Belső műveletek optimalizálása
A kritikus fontosságú technológiák támogatásának vége | Növekedés az üzleti agilitásban
Válasz a szabályozás megfelelőségi módosításaira | Új technikai képességek előkészítése
Új adatszuverenitási követelmények | Méretezés a piaci igények kielégítése érdekében
A fennakadások csökkentése és az informatikai stabilitás fejlesztése | Méretezés a földrajzi igények kielégítése érdekében

A motiváció azonosítása segít a stratégiai áttelepítési célok rögzítésében. A következő lépés a számítási feladatokhoz igazított áttelepítési útvonal azonosítása és megtervezése. A [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszköz segíti a helyszíni számítási feladatok értékelését, valamint útmutatást és eszközöket biztosít a Migrálás megkönnyítésére.

## <a name="understand-your-digital-estate"></a>A digitális ingatlan megismerése

Először azonosítsa a helyszíni infrastruktúrát, az alkalmazásokat és a függőségeket. Ez segítséget nyújt az Azure-ba való Migrálás számítási feladatainak azonosításához, valamint az optimalizált díjak összegyűjtéséhez. A kiszolgáló-Assessment eszköz segítségével azonosíthatja a használatban lévő munkaterheléseket, a munkaterhelések közötti függőségeket és a számítási feladatok optimalizálását.

### <a name="workloads-in-use"></a>Használatban lévő számítási feladatok

A Azure Migrate egy könnyű Azure Migrate berendezéssel végzi a helyszíni VMware virtuális gépek, a Hyper-V virtuális gépek, a többi virtualizált gép és a fizikai kiszolgálók ügynök nélküli felderítését. A folyamatos felderítés a gép konfigurációs adatait, a teljesítménnyel kapcsolatos metaadatokat, valamint az alkalmazásadatok adatait gyűjti. A készülék a következő adatokat gyűjti össze a helyszíni gépekről: 

- A gép, a lemez és a NIC metaadatai.

- Telepített alkalmazások, szerepkörök és szolgáltatások.

- Teljesítményadatokat, beleértve a processzor-és memóriahasználat, a lemez IOPS és az átviteli sebességet.

Az adatok összegyűjtése után exportálhatja az alkalmazás-leltári listát az alkalmazások kereséséhez és a gépen futó példányok SQL Serverához. A SQL Server készültségének megismeréséhez használja a Azure Migrate: Database Assessment eszközt.

 ![Alkalmazás leltározása a portálon](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Alkalmazás-leltár exportálása](./media/concepts-migration-planning/application-inventory-export.png)

A kiszolgáló-értékelési eszközzel felderített információkkal együtt a Configuration Management-adatbázis (CMDB) adatai segítségével megtekintheti a kiszolgálóját és az adatbázis-hagyatékot, valamint megismerheti, hogyan oszlanak meg a kiszolgálók a különböző üzleti egységeken, alkalmazás-tulajdonosokon, földrajzi területeken stb. Ez segít eldönteni, hogy mely számítási feladatok rangsorolják az áttelepítést. 

### <a name="dependencies-between-workloads"></a>A munkaterhelések közötti függőségek

A kiszolgáló felderítése után [elemezheti a függőségeket](concepts-dependency-visualization.md), megjelenítheti és azonosíthatja a kiszolgálók közötti függőségeket, valamint optimalizálhatja a egymástól függő kiszolgálók Azure-ba való áthelyezésének optimalizálási stratégiáit. A vizualizáció segítségével megtudhatja, hogy vannak-e használatban bizonyos gépek, vagy leszerelhetők az áttelepítés helyett.  A függőségek elemzésével biztosítható, hogy a rendszer ne maradjon hátra, és az áttelepítés során meglepetések legyenek az kimaradások. Az alkalmazás leltározása és a függőségi elemzés elkészült, így a kiszolgálók nagy megbízhatóságú csoportokat hozhat létre, és megkezdheti az értékelését.

 ![Függőségek leképezése](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Optimalizálás és méretezés

Az Azure rugalmasságot biztosít a felhő kapacitásának időbeli átméretezéséhez, és a Migrálás lehetőséget biztosít a kiszolgálók számára lefoglalt CPU-és memória-erőforrások optimalizálására. Az identitással rendelkező kiszolgálók értékelésének létrehozásával megismerheti a munkaterhelés teljesítményének előzményeit. Ez létfontosságú az Azure-beli virtuális gépek és a lemezes javaslatok Azure-ban való megfelelő méretezéséhez.

## <a name="assess-migration-readiness"></a>Migrálás készültségének felmérése


### <a name="readinesssuitability-analysis"></a>Készültség/megfelelőség elemzése

Exportálhatja az értékelési jelentést, és szűrheti ezeket a kategóriákat az Azure-készültség megismeréséhez:

- **Készen áll az Azure-ra**: a gépeket az Azure-ba is át lehet telepíteni, bármilyen módosítás nélkül. 
- **Feltételesen készen áll az Azure-ra**: a gépek áttelepíthetők az Azure-ba, de kisebb módosításokra van szükség az értékelésben megadott szervizelési útmutatónak megfelelően.
- **Nem áll készen az Azure-ra**: a gépek nem telepíthetők át az Azure-ba. A problémákat az áttelepítés előtt a Szervizelési útmutatásnak megfelelően kell megállapítani. 
- **Készültség ismeretlen**: Azure Migrate nem tudja meghatározni a gép készültségét, mert nincs elég metaadat.

Az adatbázis-értékelések használatával kiértékelheti SQL Server adathagyatékának készültségét Azure SQL Database vagy Azure SQL felügyelt példányok áttelepítéséhez. Az értékelés az egyes SQL Server-példányok esetében az áttelepítési készültségi állapot százalékos arányát jeleníti meg. Emellett az egyes példányok esetében láthatja az ajánlott célt az Azure-ban, a potenciális áttelepítési blokkolók, a megszakított változások száma, az Azure SQL DB vagy az Azure SQL-alapú virtuális gép készültségi szintje, valamint a kompatibilitási szint. Mélyebben is megtudhatja, hogy milyen hatással van az áttelepítési blokkolók, és hogy milyen javaslatokat kell kijavítani.

 ![Adatbázis-értékelések](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Méretezési javaslatok

Miután egy gép az Azure-ban készként van megjelölve, a kiszolgáló értékelése során a rendszer a gépekhez tartozó Azure VM SKU és lemez típusát azonosító méretezési javaslatokat készít. A teljesítmény előzményei alapján (az áttelepített erőforrások optimalizálásához), vagy a helyszíni számítógép-beállítások alapján, a teljesítmény előzményeinek megfelelően lehet méretezési javaslatokat beolvasni. Az adatbázis-értékelésben láthatja az adatbázis SKU, a díjszabási szint és a számítási szint javaslatait.  

### <a name="get-compute-costs"></a>Számítási költségek beolvasása

A Azure Migrate értékelések teljesítmény-alapú méretezési beállítása segít a virtuális gépek megfelelő méretének növelésében, és ajánlott eljárásként használható a számítási feladatok Azure-ban való optimalizálásához. A jobb méretezés mellett néhány további lehetőség is rendelkezésre áll az Azure-költségek megtakarításának megkönnyítésére: 

- **Fenntartott példányok**: [fenntartott példányokkal (ri)](https://azure.microsoft.com/pricing/reserved-vm-instances/)az utólagos elszámolású [díjszabáshoz](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)képest jelentősen csökkentheti a költségeket.
- **Azure Hybrid Benefit**: a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)lehetővé teszi, hogy a helyszíni Windows Server-licenceket aktív frissítési garanciával vagy Linux-előfizetéssel, az Azure-ba, valamint a fenntartott példányokkal kombinálva egyesítse.
- **Nagyvállalati szerződés**: az Azure [Enterprise-szerződések (EA)](../cost-management-billing/manage/ea-portal-agreements.md) megtakarítást biztosíthatnak az Azure-előfizetések és-szolgáltatások számára.
- **Ajánlatok**: több Azure- [ajánlat](https://azure.microsoft.com/support/legal/offer-details/)is rendelkezésre áll. Például [Pay-as-you-go dev/test](https://azure.microsoft.com/pricing/dev-test/)vagy [Enterprise dev/test ajánlat](https://azure.microsoft.com/offers/ms-azr-0148p/), amely a fejlesztési/tesztelési virtuális gépek alacsonyabb díjszabását biztosítja
- **Virtuális gép üzemidő**: az Azure-beli virtuális gépek által futtatott napi havi és órányi napokat is megtekintheti. Ha a gépek nincsenek használatban, csökkentheti a költségeket (a RIs esetében nem alkalmazható).
- **Célcsoport**: felméréseket hozhat létre különböző régiókban, hogy kiderítse, hogy egy adott régióba való Migrálás költséghatékonyabb lehet-e. 

### <a name="visualize-data"></a>Adatok vizualizációja

A portálon megtekintheti a kiszolgáló-értékelési jelentéseket (az Azure készültségi adataival és a havi költség-elosztással). Emellett exportálhatja az értékelést, és gazdagíthatja az áttelepítési tervet további vizualizációkkal. Több értékelést is létrehozhat, amelyek különböző tulajdonságokkal rendelkeznek, és kiválaszthatja a vállalat számára legmegfelelőbb tulajdonságok készletét.  

 ![Felmérések áttekintése](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Rések/blokkolók kiértékelése

Az áttelepíteni kívánt alkalmazások és munkaterhelések azonosításához azonosítsa az állásidőre vonatkozó korlátozásokat, és keresse meg az alkalmazások és a mögöttes infrastruktúra közötti működési függőségeket. Ez az elemzés segítséget nyújt a helyreállítási időre vonatkozó célkitűzésnek (RTO) megfelelő áttelepítések megtervezéséhez, és minimálisan nulla adatvesztést biztosít. A Migrálás előtt javasoljuk, hogy tekintse át és csökkentse a kompatibilitási problémákat, illetve a nem támogatott szolgáltatásokat, amelyek letilthatják a kiszolgáló/SQL-adatbázis áttelepítését. A Azure Migrate Server Assessment-jelentés és a Azure Migrate adatbázis-felmérés segítséget nyújt. 

### <a name="prioritize-workloads"></a>Számítási feladatok rangsorolása

Miután összegyűjtötte a leltárával kapcsolatos információkat, azonosíthatja, hogy mely alkalmazásokat és munkaterheléseket kell áttelepítenie. Az alkalmazások rendszeres és ellenőrizhető módon történő áttelepítésére szolgáló "alkalmazás és tanulás" megközelítés fejlesztése, hogy a teljes körű Migrálás megkezdése előtt bármilyen hibát ki lehessen használni.

Az áttelepítési sorrend rangsorolása érdekében olyan stratégiai tényezőket használhat, mint például az összetettség, az áttelepítési idő, az üzleti sürgősség, a termelési/nem termelési szempontok, a megfelelőség, a biztonsági követelmények, az alkalmazások ismerete stb. 

Néhány javaslat:

- **Gyors WINS rangsorolása**: az értékelési jelentések segítségével azonosíthatja az alacsony terhelésű gyümölcsöket, beleértve a teljes mértékben kész kiszolgálókat és adatbázisokat, és minimális erőfeszítést kell tennie az Azure-ba való Migrálás során. A táblázat néhány módszert foglal össze.

    **Állapot** | **Művelet**
    --- | ---
    **Azure-beli használatra kész virtuális gépek** | Exportálja az értékelési jelentést, és szűrje az Azure- *ra kész* állapotú gépeket. Ez lehet az első olyan csoport, amely az Azure-ba való [átállást, az Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszközt használja.
    **Támogatási operációs rendszerek** | Exportálja az értékelési jelentést, és szűrje a Windows Server 2008 R2/Windows Server 2008 operációs rendszert futtató összes gépet. Ezek az operációs rendszerek a támogatás végén találhatók, és az Azure-ba való Migrálás során csak az Azure nyújt három éves biztonsági frissítést. Ha Azure Hybrid Benefit kombinálja, és a RIs-t használja, a megtakarítás sokkal magasabb lehet.
    **Áttelepítés SQL Server** | Az adatbázis-értékelési javaslatok segítségével áttelepítheti az Azure SQL Database számára kész adatbázisokat a Azure Migrate: adatbázis-áttelepítési eszköz használatával. Telepítse át az Azure SQL VM-re kész adatbázisokat a Azure Migrate: Server áttelepítési eszköz használatával.
    **Támogatási szoftver** | Exportálja az alkalmazás leltárát, és szűrje azokat a szoftvereket és bővítményeket, amelyek támogatják a támogatás befejezését. Rangsorolja ezeket az alkalmazásokat az áttelepítéshez.
    **Kiépített gépek** | Az értékelési jelentés exportálása és az alacsony CPU-kihasználtságú gépek szűrése (%) és memória kihasználtsága (%).  Telepítse át a megfelelő méretű Azure-beli virtuális gépre, és mentse a költségeket a kihasználatlan erőforrásokhoz.
    **Több mint kiosztott gép** | A nagy CPU-kihasználtsággal rendelkező gépek értékelési jelentésének és szűrésének exportálása (%) és memória kihasználtsága (%).  Oldja meg a kapacitási korlátozásokat, akadályozza meg a gépek megszakítását, és növelje a teljesítményt a gépek Azure-ba való áttelepítésével. Az Azure-ban használja az automatikus skálázási képességeket az igények kielégítése érdekében.<br/><br/> Az értékelési jelentések elemzése a tárolási kényszerek vizsgálatához. Elemezheti a lemez IOPS és átviteli sebességét, valamint az ajánlott lemez típusát.

- **Indítsa el a kis méretű, majd a Big**: Start parancsot a minimális kockázatot és összetettséget jelentő alkalmazások és munkaterhelések áthelyezésével, hogy magabiztos legyen az áttelepítési stratégiában. Elemezze Azure Migrate értékeléssel kapcsolatos javaslatokat a CMDB-tárházával együtt, így megkeresheti és áttelepítheti azokat a fejlesztési és tesztelési feladatokat, amelyek kísérleti áttelepítéshez lehetnek jelöltek. A kísérleti Migrálás visszajelzései és tapasztalatai hasznosak lehetnek az éles számítási feladatok áttelepítésének megkezdése során.  
- **Betartása**: az Azure a legnagyobb megfelelőségi portfóliót tartja az iparágban, az ajánlatok szélessége és mélysége tekintetében. A megfelelőségi követelmények használatával rangsorolhatja az áttelepítést, így az alkalmazások és a munkaterhelések megfelelnek a nemzeti, regionális és iparági szabványoknak és törvényeknek. Ez különösen igaz az üzleti szempontból kritikus fontosságú folyamatokkal foglalkozó szervezetekre, bizalmas adatok tárolására vagy nagy mértékben szabályozott iparágakban. Ezekben a típusú szervezeteknél, szabványokban és szabályozásokban bővelkednek, és gyakran változhatnak.  

## <a name="finalize-the-migration-plan"></a>Az áttelepítési terv véglegesítése

Az áttelepítési terv véglegesítése előtt győződjön meg arról, hogy figyelembe veszi és enyhítse a többi potenciális blokkoló, a következőképpen: 

- **Hálózati követelmények**: értékelje a hálózati sávszélességet és a késési korlátozásokat, ami váratlan késéseket okozhat, és megszakad az áttelepítés replikációs sebessége.
- **Tesztelési/áttelepítés utáni csípések**: az áttelepített alkalmazások teljesítményének és felhasználói elfogadásának teszteléséhez, illetve az alkalmazások az áttelepítés utáni konfigurálásához/finomhangolásához, például az adatbázis-kapcsolati karakterláncok frissítéséhez, a webkiszolgálók konfigurálásához, a kivágások és a tisztítások végrehajtásához stb.
- **Engedélyek**: Tekintse át az ajánlott Azure-engedélyeket, valamint az áttelepítéshez szükséges kiszolgálói/adatbázis-hozzáférési szerepköröket és engedélyeket.
- **Képzés**: a szervezet előkészítése a digitális átalakításra. A sikeres szervezeti változások szempontjából elengedhetetlen a Solid Training Foundation. Tekintse meg a [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)ingyenes képzését, beleértve az Azure alapjaival, megoldási architektúrákkal és biztonsággal kapcsolatos tanfolyamokat. Ösztönözze csapatát az [Azure-minősítések](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)megismerésére.  
- **Implementációs támogatás**: Ha szüksége van rá, támogatást kaphat a megvalósításhoz. Számos szervezet úgy dönt, hogy külső segítséget nyújt a felhőbe való Migrálás támogatásához. Ha az Azure-ba gyorsan és magabiztosan szeretne áttérni személyre szabott segítséggel, vegye fontolóra egy [Azure-szakértő által felügyelt](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)szolgáltatót vagy [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Hozzon létre egy hatékony felhőalapú áttelepítési tervet, amely részletes információkat tartalmaz az áttelepíteni kívánt alkalmazásokról, az alkalmazások/adatbázisok rendelkezésre állásáról, az állásidőre vonatkozó korlátozásokról és az áttelepítési mérföldkövek. A terv megállapítja, hogy mennyi ideig tart az Adatmásolás, és tartalmaz egy reális puffert az áttelepítés utáni teszteléshez és a feldarabolt tevékenységekhez. 

Az áttelepítés utáni tesztelési tervnek tartalmaznia kell a működés, az integráció, a biztonság és a teljesítmény tesztelését és a használati eseteket, hogy az áttelepített alkalmazások a várt módon működjenek, és hogy az összes adatbázis-objektum és adatkapcsolat sikeresen átkerüljön a felhőbe.  

Hozzon létre egy áttelepítési ütemtervet, és deklaráljon egy karbantartási időszakot az alkalmazások és adatbázisok áttelepítéséhez minimálisan nulla állásidővel, és korlátozza a lehetséges működési és üzleti hatásokat az áttelepítés során.  

## <a name="migrate"></a>Migrate

Azt javasoljuk, hogy a teljes körű áttelepítés megkezdése előtt futtasson Azure Migrate tesztelési tesztet. A tesztelési folyamat segítségével megbecsülheti a szóban forgó időt, és megcsípheti az áttelepítési tervet. Lehetőséget biztosít az esetleges problémák felderítésére, és a teljes áttelepítés előtt kijavítani azokat.

Ha készen áll az áttelepítésre, használja a Azure Migrate: Server áttelepítési eszközt és az Azure adatáttelepítési szolgáltatást (DMS) a zökkenőmentes és integrált áttelepítési élményhez teljes körű nyomon követéssel.

- A kiszolgáló áttelepítési eszköze segítségével áttelepítheti a helyszíni virtuális gépeket és kiszolgálókat, illetve más magán-vagy nyilvános felhőben (beleértve az AWS-t, a GCP-t) található virtuális gépeket, a nulla állásidővel.
- Az Azure DMS egy teljes körűen felügyelt szolgáltatást biztosít, amely lehetővé teszi, hogy a zökkenőmentes áttelepítést több adatbázisból az Azure-adatplatformokra, minimális állásidővel.  

## <a name="next-steps"></a>Következő lépések

- Vizsgálja meg a [felhőalapú migrációs utat](/azure/architecture/cloud-adoption/getting-started/migrate)   Az Azure Cloud bevezetési keretrendszerében.
- [Gyors áttekintést](migrate-services-overview.md) kaphat a Azure Migrateről, és megtekintheti az [első lépéseket bemutató videót](https://youtu.be/wFfq3YPxYHE).
- További információ a virtuális gépek Azure-beli [virtuális gépekre](concepts-assessment-calculation.md)való áttelepítésének értékeléséről.

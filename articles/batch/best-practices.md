---
title: Ajánlott eljárások
description: Ismerje meg az ajánlott eljárásokat és hasznos tippeket a Azure Batch-megoldások fejlesztéséhez.
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: 697ac5d213bbe2e52134cad519f69c233f1cd593
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583275"
---
# <a name="azure-batch-best-practices"></a>Azure Batch ajánlott eljárások

Ez a cikk az ajánlott eljárások és hasznos tippek gyűjteményét tárgyalja a Azure Batch szolgáltatás hatékony használatához, a Batch használatával kapcsolatos valós tapasztalatok alapján. Ezek a tippek segíthetnek a teljesítmény növelésében és a Azure Batch-megoldásokban felhasználható buktatók elkerülésében.

> [!TIP]
> A Azure Batch biztonságával kapcsolatos útmutatásért lásd: [Batch biztonsági és megfelelőségi ajánlott eljárások](security-best-practices.md).

## <a name="pools"></a>Készletek

A [készletek](nodes-and-pools.md#pools) a Batch szolgáltatásban a feladatok végrehajtásához szükséges számítási erőforrások. A következő szakaszokban a Batch-készletek használatáról szóló ajánlásokat talál.

### <a name="pool-configuration-and-naming"></a>Készlet konfigurációja és elnevezése

- **Készlet lefoglalási módja:** Batch-fiók létrehozásakor választhat két készlet kiosztási módja közül: **Batch szolgáltatás** vagy **felhasználói előfizetés**. A legtöbb esetben az alapértelmezett batch szolgáltatás módot kell használnia, amelyben a készletek a Batch által felügyelt előfizetésekben a színfalak mögött vannak lefoglalva. A szintén választható „Felhasználói előfizetés” mód esetében a Batch virtuális gépei és egyéb erőforrásai közvetlenül az előfizetésben jönnek létre egy készlet létrehozásakor. A felhasználói előfizetési fiókok elsődlegesen a fontos, de a forgatókönyvek kis részhalmazának engedélyezésére használatosak. A felhasználói előfizetési móddal kapcsolatos további információkért tekintse meg a [felhasználói előfizetés üzemmódjának további konfigurációját](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **"virtualMachineConfiguration" vagy "cloudServiceConfiguration":** Habár a készleteket jelenleg a konfigurációval is létrehozhatja, az új készleteket a "virtualMachineConfiguration" és a "cloudServiceConfiguration" beállítással kell konfigurálni. A virtuálisgép-konfigurációs készletek minden aktuális és új batch-funkciót támogatni fognak. Cloud Services konfigurációs készletek nem támogatják az összes funkciót, és nem terveznek új képességeket. Az új "cloudServiceConfiguration" készleteket nem lehet létrehozni, vagy új csomópontokat hozzáadni a meglévő készletekhez a [2024. február 29. után](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). További információ: batch- [készlet konfigurációjának Áttelepítése Cloud Servicesról virtuális gépre](batch-pool-cloud-service-to-virtual-machine-configuration.md).

- **Vegye figyelembe a feladat és a feladat futási idejét a feladat készlet-hozzárendelésének meghatározásakor:** Ha a feladatok elsősorban rövid ideig futó tevékenységekből állnak, és a tevékenységek várható összesített száma kicsi, így a feladat teljes várható futási ideje nem hosszú, ne foglaljon le új készletet az egyes feladatokhoz. A csomópontok lefoglalási ideje csökkenti a feladatok futási idejét.

- **A készleteknek több számítási csomóponttal kell rendelkezniük:** Az egyes csomópontok nem garantáltak, hogy mindig elérhetők legyenek. Habár a nem gyakori, a hardverhiba, az operációsrendszer-frissítések és más problémák egy állomása az egyes csomópontok offline állapotba helyezését eredményezheti. Ha a Batch-munkafolyamathoz determinisztikus szükséges, a garantált előrehaladást kell biztosítania, több csomóponttal rendelkező készleteket kell kiosztania.

- **Erőforrás-nevek újrafelhasználásának** mellőzése: A Batch-erőforrások (feladatok, készletek stb.) gyakran jönnek, és idővel eltérhetnek. Például hétfőn is létrehozhat egy készletet, törölheti azt keddre, majd csütörtökön létrehoz egy másik készletet. Minden létrehozott új erőforráshoz egyedi nevet kell adni, amelyet korábban még nem használt. Ezt egy GUID használatával végezheti el (akár a teljes erőforrás neve, akár a részeként), vagy beágyazhatja az erőforrásnak az erőforrás nevében történő létrehozását. A Batch támogatja a [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname)paramétert, amely egy adott erőforrás számára elérhetővé teheti az erőforrásokat, még akkor is, ha a tényleges erőforrás-azonosító olyan dolog, amely nem az emberi barát. Az egyedi nevek használata megkönnyíti az Ön számára, hogy megkülönböztesse az adott erőforrást a naplókban és a mérőszámokban. Emellett a kétértelműség is törlődik, ha az erőforráshoz nem kell támogatási esetet benyújtani.

- **Folytonosság a készlet karbantartása és a sikertelenség során:** A legjobb megoldás, ha a feladatok dinamikusan használják a készleteket. Ha a feladatok minden esetben ugyanazt a készletet használják, akkor a feladatok nem fognak futni, ha valami nem stimmel a készlettel. Ez különösen fontos az időérzékeny számítási feladatokhoz. Ennek kijavításához válasszon ki vagy hozzon létre egy készletet dinamikusan, amikor az egyes feladatokat ütemezi, vagy ha úgy szeretné felülbírálni a készlet nevét, hogy el tudja kerülni a nem megfelelő állapotú készletet.

- **Üzletmenet-folytonosság a készlet karbantartása és a sikertelenség során:** Számos oka lehet annak, hogy a készlet miért nem nő a kívánt mérettel, például belső hibákkal, a kapacitás korlátozásával stb. Emiatt készen kell állnia arra, hogy a feladatokat egy másik készletben (lehetőleg egy másik virtuálisgép-mérettel) lehessen átcélozni, ha szükséges, a Batch ezt a [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)-on keresztül támogatja. Kerülje a statikus készlet AZONOSÍTÓjának használatát azzal a várakozással, hogy soha nem lesz törölve, és soha nem változik.

### <a name="pool-lifetime-and-billing"></a>Készlet élettartama és számlázása

A készlet élettartama eltérő lehet a készlet-konfigurációra alkalmazott foglalási és beállítási módtól függően. A készletek tetszőleges időtartammal rendelkezhetnek, és a készletben lévő számítási csomópontok száma bármikor megadható. Az Ön felelőssége, hogy a készletben lévő számítási csomópontokat explicit módon, vagy a szolgáltatás által biztosított szolgáltatások (az[autoscale](nodes-and-pools.md#automatic-scaling-policy) vagy az [autopool](nodes-and-pools.md#autopools)) segítségével kezelje.

- **Készletek frissen tartása:** Méretezze át a készleteket néhány havonta nulla értékre, hogy meggyőződjön arról, hogy a [csomópont-ügynök legújabb frissítései és](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)hibajavításai megtalálhatók. A készlet nem kapja meg a csomópont-ügynök frissítéseit, kivéve, ha újból létrehozták, vagy 0 számítási csomópontra méretezi át őket. A készlet újbóli létrehozása vagy átméretezése előtt javasoljuk, hogy [a csomópontok szakaszban](#nodes) ismertetett módon letöltse a csomóponti ügynökök naplóit a hibakereséshez.

- **Készlet ismételt létrehozása:** Hasonló megjegyzés esetén nem ajánlott napi rendszerességgel törölni és újból létrehozni a készleteket. Ehelyett hozzon létre egy új készletet, és frissítse a meglévő feladatokat, hogy az új készletre mutasson. Miután az összes feladatot áthelyezte az új készletbe, törölje a régi készletet.

- **Készlet hatékonysága és számlázása:** A Batch saját maga nem számít fel külön díjat, de a felhasznált számítási erőforrásokért díjat számítunk fel. A készletben lévő összes számítási csomópont után számítunk fel díjat, függetlenül attól, hogy milyen állapotban vannak. Ez magában foglalja a csomópont futtatásához szükséges díjakat, például a tárolási és hálózati költségeket. Az ajánlott eljárások megismeréséhez tekintse meg [Azure Batch a Cost Analysis és a költségkeretet](budget.md).

### <a name="pool-allocation-failures"></a>A készlet lefoglalási hibái

A készlet-foglalási hibák az első kiosztáskor vagy az azt követő átméretezések során bármikor megtörténhetnek. Ennek oka az lehet, hogy egy adott régióban vagy más Azure-szolgáltatásokban a Batch által kiváltott átmeneti kapacitás kimerítése lehetséges. Az alapvető kvóta nem garancia, hanem korlát.

### <a name="unplanned-downtime"></a>Nem tervezett leállás

Lehetséges, hogy a Batch-készletek az Azure-ban leállási eseményeket tapasztalnak. Ne feledje, hogy a Batch-forgatókönyvek vagy munkafolyamatok tervezésekor és fejlesztésekor vegye figyelembe a problémát.

Abban az esetben, ha egy csomópont meghibásodik, a Batch automatikusan megkísérli helyreállítani ezeket a számítási csomópontokat az Ön nevében. Ez a helyreállított csomóponton futó feladatok újraütemezését eredményezheti. A megszakított feladatokkal kapcsolatos további tudnivalókért tekintse meg [az újrapróbálkozások tervezése](#design-for-retries-and-re-execution) című témakört.

### <a name="custom-image-pools"></a>Egyéni rendszerkép-készletek

Ha Azure Batch-készletet hoz létre a virtuális gép konfigurációjával, meg kell adnia egy virtuálisgép-rendszerképet, amely a készlet minden számítási csomópontja számára biztosítja az operációs rendszert. A készletet egy támogatott Azure Marketplace-lemezképpel is létrehozhatja, vagy [létrehozhat egy egyéni rendszerképet is egy megosztott képtárat](batch-sig-images.md)tartalmazó képpel. Míg a [felügyelt lemezkép](batch-custom-images.md) használatával egyéni képkészletet is létrehozhat, javasoljuk, hogy amikor csak lehet, hozzon létre egyéni lemezképeket a megosztott képtárat használva. A megosztott képkatalógus segítségével gyorsabban kiépítheti a készleteket, méretezheti nagyobb mennyiségű virtuális gépet, és növelheti a megbízhatóságot a virtuális gépek kiépítésekor.

### <a name="third-party-images"></a>Harmadik féltől származó rendszerképek

A készleteket az Azure Marketplace-en közzétett harmadik féltől származó rendszerképek használatával hozhatja létre. A felhasználói előfizetés módú batch-fiókok esetében előfordulhat, hogy a "kiosztási hiba a piactér-vásárlási jogosultság ellenőrzése miatt" hibaüzenet jelenik meg, ha a készletet bizonyos harmadik féltől származó rendszerképekkel hozza létre. A hiba megoldásához fogadja el a rendszerkép közzétevője által beállított feltételeket. Ezt a [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) vagy az [Azure CLI](/cli/azure/vm/image/terms)használatával teheti meg.

### <a name="azure-region-dependency"></a>Azure region-függőség

Ha időérzékeny vagy éles számítási feladatra van szüksége, ne használja egyetlen Azure-régiót sem. Ritkán előfordul, hogy olyan problémák merülnek fel, amelyek befolyásolhatják a teljes régiót. Ha például a feldolgozásnak egy adott időpontban kell kezdődnie, érdemes lehet a készletet az elsődleges régióban is felmérni a *kezdési időpont előtt*. Ha a készlet skálázása meghiúsul, visszatérhet egy készlet egy biztonsági mentési régióban (vagy régiókban) való méretezésére. A különböző régiókban lévő több fiókból álló készletek egy kész, könnyen hozzáférhető biztonsági mentést biztosítanak, ha egy másik készlettel valamilyen hiba történik. További információ: [az alkalmazás megtervezése a magas rendelkezésre állás érdekében](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Feladatok

A [feladat](jobs-and-tasks.md#jobs) egy olyan tároló, amely több száz, több ezer vagy akár több millió feladatot tartalmaz. Feladatok létrehozásakor kövesse az alábbi irányelveket.

### <a name="fewer-jobs-more-tasks"></a>Kevesebb feladat, több feladat

A feladatok egyetlen feladat futtatására való használata nem hatékony. Például hatékonyabban használhatja a 1000 feladatokat tartalmazó egyetlen feladatot, és nem hoz létre olyan 100-feladatokat, amelyek mindegyike 10 feladatot tartalmaz. A 1000-es feladatok futtatása egyetlen feladattal, a legkevésbé hatékony, leglassabb és legdrágább módszer.

Ezért ügyeljen arra, hogy ne tervezzen olyan batch-megoldást, amelynek több ezer egyidejű aktív feladatra van szüksége. A feladatokhoz nem tartozik kvóta, ezért a lehető leghatékonyabban végrehajtja a feladatokat a feladatok és a feladatok [ütemezett kvótáinak](batch-quota-limit.md#resource-quotas)használatával.

### <a name="job-lifetime"></a>Feladatok élettartama

A Batch-feladatok határozatlan élettartammal rendelkeznek, amíg nem törlik a rendszerből. Az állapota azt jelzi, hogy képes-e további feladatokat fogadni az ütemezéshez, vagy sem.

A feladatok nem helyezhetők automatikusan befejezett állapotba, kivéve, ha explicit módon megszakították. Ezt a [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) tulajdonság vagy a [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints)automatikusan aktiválhatja.

Az alapértelmezett [aktív feladatok és a feladatok ütemezett kvótája](batch-quota-limit.md#resource-quotas). A Befejezett állapotú feladatok és feladatok Ütemtervei nem számítanak bele a kvótába.

## <a name="tasks"></a>Feladatok

A [feladatok](jobs-and-tasks.md#tasks) olyan egyedi Munkaegységek, amelyek feladatból állnak. A feladatokat a felhasználó küldi el és ütemezi a számítási csomópontokra. A feladatok létrehozásakor és végrehajtásakor több kialakítási szempontot is figyelembe kell venni. A következő szakaszokban ismertetjük a gyakori forgatókönyveket, valamint azt, hogy miként lehet megtervezni a problémákat és hatékonyan elvégezni a feladatokat.

### <a name="save-task-data"></a>Tevékenységadatok mentése

A számítási csomópontok természetüknél fogva elmúlóak. A Batch számos funkciója van, például az [autopool](nodes-and-pools.md#autopools) és az [autoscale](nodes-and-pools.md#automatic-scaling-policy) , amely megkönnyíti a csomópontok eltűnését. Ha a csomópontok egy készletet hagynak (átméretezés vagy készlet törlése miatt), akkor a csomópontokon lévő összes fájl is törlődik. Emiatt a tevékenységnek át kell helyeznie a kimenetét a-on futó csomópontból, és egy tartós tárolóba kell helyeznie, mielőtt befejeződik. Hasonlóképpen, ha egy feladat meghibásodik, akkor át kell helyeznie a naplókat, amelyek szükségesek a tartós tár hibáinak diagnosztizálásához.

A Batch integrált támogatást nyújt az Azure Storage-hoz az adatok [OutputFiles](batch-task-output-files.md)-n keresztüli feltöltéséhez, valamint számos megosztott fájlrendszerhez, vagy a feladatokban a feltöltéshez is.

### <a name="manage-task-lifetime"></a>Feladat élettartamának kezelése

Törölje a feladatokat, ha már nincs rájuk szükség, vagy állítson be egy [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) -feladatra vonatkozó korlátozást. Ha `retentionTime` be van állítva, a Batch automatikusan törli a feladat által a lejáratkor használt lemezterületet `retentionTime` .

A tevékenységek törlése két dolgot hajt végre. Gondoskodik arról, hogy a feladatban ne legyenek felépítve feladatok, ami megnehezíti a kívánt feladat lekérdezését vagy megtalálását (mivel a befejezett feladatokon keresztül kell szűrnie). Emellett megtisztítja a kapcsolódó tevékenységadatok a csomóponton (a megadott `retentionTime` érték még nem lett kijelölve). Ezzel biztosíthatja, hogy a csomópontok ne töltsenek fel a feladattal kapcsolatos adatokkal, és elfogyjon a szabad lemezterület.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Nagy számú feladat elküldése a gyűjteményben

A feladatokat egyedi alapon vagy gyűjteményekben lehet beküldeni. A feladatok tömeges beküldése a terhelés és a beküldési idő csökkentése érdekében akár 100 [gyűjteményben](/rest/api/batchservice/task/addcollection) is elvégezhető.

### <a name="set-max-tasks-per-node-appropriately"></a>Feladatok maximális száma a csomóponton megfelelően

A Batch támogatja a csomópontokon lévő feladatok túllépését (több feladatot futtat, mint a magok). Így biztosíthatja, hogy a feladatok "illeszkedjenek" a készlet csomópontjaihoz. Előfordulhat például, hogy csökkenhet a teljesítmény, ha nyolc olyan feladatot próbál ütemezni, amelynél a CPU-használat 25%-ra van felhasználva egy csomópontra (egy készletben a-ben `taskSlotsPerNode = 8` ).

### <a name="design-for-retries-and-re-execution"></a>Újrapróbálkozások és újbóli végrehajtás tervezése

A Batch automatikusan újrapróbálkozik a feladatokat. Az újrapróbálkozások két típusa létezik: a felhasználó által vezérelt és a belső. A felhasználó által vezérelt újrapróbálkozásokat a tevékenység [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)adja meg. Ha a feladatban megadott program nem nulla kilépési kóddal kilép, a feladat újra próbálkozik a értékével `maxTaskRetryCount` .

Bár ritka, a feladat a számítási csomópont meghibásodása miatt újra próbálkozhat, például nem lehet frissíteni a belső állapotot vagy a csomópont meghibásodását a feladat futása közben. A feladat újra próbálkozik ugyanazon a számítási csomóponton, ha lehetséges, akár egy belső korláttal, mielőtt felveszi a feladatot, és elhalasztja, hogy a Batch által átütemezett feladatot, esetleg egy másik számítási csomóponton.

A feladatok dedikált vagy alacsony prioritású csomópontokon történő végrehajtásakor nincsenek tervezési különbségek. Azt jelzi, hogy egy feladat előzik alacsony prioritású csomóponton fut-e, vagy egy dedikált csomópont meghibásodása miatt megszakadt, mindkét szituációt enyhíteni kell, ha a feladat megtervezésével ellenáll a meghibásodásnak.

### <a name="build-durable-tasks"></a>Tartós feladatok készítése

A feladatokat úgy kell kialakítani, hogy elbírja a meghibásodást, és az újrapróbálkozást. Ez különösen fontos a hosszú ideig futó feladatok esetében. Ehhez győződjön meg arról, hogy a feladatok ugyanazt az eredményt használják, még akkor is, ha egynél többször futnak. Ennek az az egyik módja, hogy a feladatok "cél keresése". A másik lehetőség, hogy ellenőrizze, hogy a feladatok idempotens-e (a tevékenységek a futtatásuk időpontját is megegyeznek).

Gyakori példa a fájlok számítási csomópontba másolására szolgáló feladat. Az egyszerű megközelítés egy olyan feladat, amely minden egyes futtatásakor átmásolja az összes megadott fájlt, ami nem hatékony, és nem áll készen a sikertelen működésre. Ehelyett hozzon létre egy feladatot, amely biztosítja, hogy a fájlok a számítási csomóponton legyenek. olyan feladat, amely nem másolja újra a már meglévő fájlokat. Így a feladat felveszi a feladatot, ha megszakadt.

### <a name="avoid-short-execution-time"></a>A rövid végrehajtási idő elkerülése

Azok a feladatok, amelyek csak egy-két másodpercre futnak, nem ideálisak. Próbáljon meg jelentős mennyiségű munkát végezni egy adott feladatban (10 másodperc minimum, akár óra vagy nap). Ha az egyes feladatok egy percen (vagy több) vannak végrehajtva, akkor az ütemezési terhelés a teljes számítási idő töredékének kis hányada.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Készlet hatókörének használata a Windows-csomópontok rövid feladataihoz

A feladatok batch-csomópontokon történő ütemezésekor kiválaszthatja, hogy a feladat hatókörével vagy a készlet hatókörével kívánja-e futtatni. Ha a feladat csak rövid ideig fut, akkor a feladat hatóköre nem lehet hatékony, mert az adott feladat automatikus felhasználói fiókjának létrehozásához szükséges erőforrások. A nagyobb hatékonyság érdekében érdemes lehet ezeket a feladatokat a készlet hatókörére beállítani. További információ: [feladat futtatása automatikus felhasználóként készlet hatókörrel](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Csomópontok

A [számítási csomópont](nodes-and-pools.md#nodes) egy Azure-beli virtuális gép (VM) vagy Cloud Service VM, amely az alkalmazás munkaterhelésének egy részének feldolgozására van kijelölve. A csomópontok használatakor kövesse az alábbi irányelveket.

### <a name="idempotent-start-tasks"></a>Idempotens-indítási feladatok

Csakúgy, mint más feladatokhoz, a csomópont [indítási tevékenységének](jobs-and-tasks.md#start-task) idempotens kell lennie, mivel a csomópont minden indításakor újra futni fog. Egy idempotens feladat egyszerűen egy, amely konzisztens eredményt állít elő többszöri futtatásakor.

### <a name="isolated-nodes"></a>Elkülönített csomópontok

Használjon elkülönített virtuálisgép-méreteket a megfelelőségi és szabályozási követelményekkel rendelkező munkaterhelésekhez. A virtuális gép konfigurációs módjában támogatott elkülönített méretek a következők:,,, `Standard_E80ids_v4` `Standard_M128ms` `Standard_F72s_v2` `Standard_G5` `Standard_GS5` és `Standard_E64i_v3` . Az elkülönített virtuálisgép-méretekről további információt a [virtuális gépek elkülönítése az Azure-ban](../virtual-machines/isolation.md)című témakörben talál.

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>A hosszan futó szolgáltatások kezelése az operációs rendszer szolgáltatásainak felületén keresztül

Időnként szükség van egy másik ügynök futtatására a csomóponton található batch-ügynök mellett. Előfordulhat például, hogy adatokat szeretne gyűjteni a csomópontról, és jelentenie kell azt. Javasoljuk, hogy ezeket az ügynököket operációs rendszerként, például Windows-szolgáltatásként vagy Linux-szolgáltatásként telepítse `systemd` .

Ezeknek a szolgáltatásoknak a futtatásakor a rendszer nem tudja zárolni a fájl zárolásait a csomóponton található batch által felügyelt könyvtárakban, mert máskülönben a Batch nem fogja tudni törölni ezeket a címtárakat a fájlok zárolása miatt. Ha például Windows-szolgáltatást telepít egy indítási feladatba, ahelyett, hogy közvetlenül az indítási tevékenység munkakönyvtárból indítja el a szolgáltatást, másolja máshová a fájlokat (vagy ha a fájlok már csak kihagyják a másolatot). Ezután telepítse a szolgáltatást az adott helyről. Ha a Batch újrakezdi a kezdési feladatot, az törli a feladat indítása munkakönyvtárat, és újra létrehozza azt. Ez azért működik, mert a szolgáltatás zárolja a fájl zárolásait a másik címtárban, nem az indítási tevékenység munkakönyvtárát.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Ne hozzon létre címtár-csomópontokat a Windowsban

A címtár-összekapcsolásokat, más néven a címtárbeli rögzített hivatkozásokat, nehéz kezelni a feladatok és a feladatok karbantartása során. A rögzített hivatkozások helyett használjon szimbolikus hivatkozásokat (Soft-Links).

### <a name="collect-the-batch-agent-logs"></a>A Batch-ügynök naplóinak összegyűjtése

Ha egy csomóponton futó csomópont vagy feladatok viselkedésével kapcsolatos problémát tapasztal, gyűjtsön a Batch-ügynök naplóit a szóban forgó csomópontok felszabadítása előtt. A Batch-ügynök naplóit a Batch szolgáltatás naplófájljainak API-jával töltheti össze. Ezek a naplók a Microsoft támogatási jegyének részeként is megadhatók, és segítenek a hibaelhárításban és a megoldásban.

### <a name="manage-os-upgrades"></a>Operációs rendszer frissítéseinek kezelése

A felhasználói előfizetés módú batch-fiókok esetében az automatikus operációsrendszer-frissítések megszakítják a feladat előrehaladását, különösen akkor, ha a feladatok hosszú ideig futnak. A [idempotens feladatok kiépítése](#build-durable-tasks) segíthet a megszakítások által okozott hibák csökkentésében. Javasoljuk továbbá [, hogy az operációs rendszer rendszerképének frissítéseit olyan időpontokra ütemezze, amikor a feladatok nem várhatóan futnak](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

A Windows-készletek esetében alapértelmezés szerint a érték `enableAutomaticUpdates` van beállítva `true` . Az automatikus frissítések engedélyezése ajánlott, de ezt az értéket megadhatja, `false` Ha biztosítania kell, hogy az operációs rendszer frissítése váratlanul megtörténjen.

## <a name="isolation-security"></a>Elkülönítés biztonsága

Az elkülönítés érdekében, ha a forgatókönyv megköveteli a feladatok egymástól való elkülönítését, tegye azokat külön készletekben. A készlet a Batch biztonsági elkülönítési határa, és alapértelmezés szerint a két készlet nem látható, vagy nem tud kommunikálni egymással. Kerülje a különálló batch-fiókok elkülönítésének módját.

## <a name="moving-batch-accounts-across-regions"></a>Batch-fiókok áthelyezése régiók között

Vannak olyan helyzetek, amikor hasznos lehet egy meglévő batch-fiók áthelyezése az egyik régióból a másikba. Előfordulhat például, hogy egy másik régióba szeretne áttérni a vész-helyreállítási tervezés részeként.

Azure Batch fiókok nem helyezhetők át közvetlenül egyik régióból a másikba. A Batch-fiók meglévő konfigurációjának exportálásához azonban egy Azure Resource Manager sablont is használhat. Ezután egy másik régióban is elvégezheti az erőforrást, ha a Batch-fiókot egy sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban.

Miután feltöltötte a sablont az új régióba, újra létre kell hoznia a tanúsítványokat, a feladatok ütemterveit és az alkalmazáscsomag-csomagokat. A módosítások elvégzéséhez és a Batch-fiók áthelyezésének befejezéséhez ne felejtse el törölni az eredeti batch-fiókot vagy erőforráscsoportot.

A Resource Managerrel és a sablonokkal kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure Resource Manager sablonok létrehozása és telepítése a Azure Portal használatával](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)című témakört.

## <a name="connectivity"></a>Kapcsolat

Tekintse át a Batch-megoldások kapcsolódásával kapcsolatos alábbi útmutatást.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Hálózati biztonsági csoportok (NSG) és felhasználó által megadott útvonalak (UDR)

A [Batch-készletek virtuális hálózatban való](batch-virtual-network.md)kiépítés során ügyeljen arra, hogy a `BatchNodeManagement` szolgáltatási címke, a portok, a protokollok és a szabály irányának használatára vonatkozó irányelveket szorosan kövesse. A szolgáltatás címkéjének használata kifejezetten ajánlott, nem az alapul szolgáló batch szolgáltatás IP-címeinek használata. Ennek az az oka, hogy az IP-címek idővel változhatnak. A Batch szolgáltatás IP-címeinek közvetlen használata instabilitást, megszakítást vagy kimaradást okozhat a Batch-készletekben.

A felhasználó által megadott útvonalak (UDR-EK) esetében ellenőrizze, hogy van-e olyan folyamat, amely rendszeresen frissíti a Batch szolgáltatás IP-címeit az útválasztási táblázatban, mivel ezek a címek idővel változnak. A Batch szolgáltatás IP-címei listájának beszerzésével kapcsolatos további információkért lásd: [szolgáltatási címkék a helyszínen](../virtual-network/service-tags-overview.md). A Batch szolgáltatás IP-címei társítva lesznek a `BatchNodeManagement` szolgáltatás címkéjével (vagy a Batch-fiók régiójának megfelelő regionális változattal).

### <a name="honoring-dns"></a>A DNS tiszteletben tartása

Győződjön meg arról, hogy a rendszerek a Batch-fiók szolgáltatás URL-címéhez tartozó DNS-élettartam (TTL) tiszteletben tartásával rendelkeznek. Továbbá győződjön meg arról, hogy a Batch szolgáltatás ügyfelei és a Batch szolgáltatás egyéb csatlakozási mechanizmusai nem az IP-címekre támaszkodnak (vagy az alább leírtak szerint [hozzon létre egy statikus nyilvános IP-címmel rendelkező készletet](create-pool-public-ip.md) ).

Ha a kérések 5xx HTTP-válaszokat kapnak, és a válaszban szerepel a "kapcsolódás: Bezárás" fejléc, a Batch szolgáltatás ügyfelének be kell tartania az ajánlást a meglévő kapcsolatok lezárásával, a DNS újbóli feloldásával a Batch-fiók szolgáltatás URL-címéhez, és egy új kapcsolatban próbálja meg a következő kérelmeket.

### <a name="retry-requests-automatically"></a>Újrapróbálkozási kérelmek automatikusan

Győződjön meg arról, hogy a Batch szolgáltatás ügyfelei megfelelő újrapróbálkozási szabályzatokkal rendelkeznek a kérések automatikus újrapróbálkozásához, még a normál működés során, és nem kizárólag a szolgáltatás karbantartási időszakai alatt. Az újrapróbálkozási szabályzatoknak legalább 5 perces intervallumot kell kiterjedniük. Az automatikus újrapróbálkozási képességek különböző batch SDK-k, például a [.net RetryPolicyProvider osztály](/dotnet/api/microsoft.azure.batch.retrypolicyprovider)számára érhetők el.

### <a name="static-public-ip-addresses"></a>Statikus nyilvános IP-címek

A Batch-készletekben lévő virtuális gépek jellemzően nyilvános IP-címeken keresztül érhetők el, amelyek megváltoztathatják a készlet élettartamát. Ez megnehezítheti az adatbázisok vagy más, bizonyos IP-címekhez való hozzáférést korlátozó külső szolgáltatás kezelését. Annak biztosítása érdekében, hogy a készletben lévő nyilvános IP-címek ne változzon meg váratlanul, létrehozhat egy készletet, amely az Ön által vezérelt statikus nyilvános IP-címek készletét használja. További információ: Azure Batch- [készlet létrehozása a megadott nyilvános IP-címekkel](create-pool-public-ip.md).

### <a name="testing-connectivity-with-cloud-services-configuration"></a>Kapcsolat tesztelése Cloud Services konfigurációval

Nem használhatja a szokásos "ping"/ICMP protokollt a Cloud Services szolgáltatással, mert az ICMP protokoll használata nem engedélyezett az Azure Load balancerben. További információ: [kapcsolat és hálózatkezelés az Azure Cloud Serviceshoz](../cloud-services/cloud-services-connectivity-and-networking-faq.md#can-i-ping-a-cloud-service).

## <a name="batch-node-underlying-dependencies"></a>A Batch-csomópont mögöttes függőségei

A Batch-megoldások tervezésekor vegye figyelembe a következő függőségeket és korlátozásokat.

### <a name="system-created-resources"></a>Rendszerek által létrehozott erőforrások

Azure Batch létrehozza és kezeli a virtuális gépen lévő felhasználókat és csoportokat, amelyek nem módosíthatók. Ezek a következők:

Windows:

- Egy **PoolNonAdmin** nevű felhasználó
- **WATaskCommon** nevű felhasználói csoport

Linux:

- Egy **_azbatch** nevű felhasználó

### <a name="file-cleanup"></a>Fájl karbantartása

A Batch aktívan megpróbálja törölni azt a munkakönyvtárat, amelyen a feladatok futnak, miután a megőrzési idő lejár. A címtáron kívül írt fájlok [a saját felelőssége,](#manage-task-lifetime) hogy elkerülje a lemezterület kitöltését.

A munkakönyvtár automatikus tisztítása le lesz tiltva, ha a Windows rendszerű szolgáltatást futtat a startTask Working Directory szolgáltatásból, mert a mappa még használatban van. Ennek hatására csökken a teljesítmény. Ennek a megoldásnak a kijavításához módosítsa a szolgáltatás könyvtárát egy különálló, a Batch által nem kezelt könyvtárba.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Ismerje meg az [alapértelmezett Azure batch kvótákat, korlátozásokat és korlátozásokat, valamint a kvóta növelésének módját](batch-quota-limit.md).
- Ismerje meg, hogyan lehet [észlelni és elkerülni a készletek és csomópontok háttérben végzett műveleteinek hibáit ](batch-pool-node-error-checking.md).
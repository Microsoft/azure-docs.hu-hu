---
title: Csomópontok és készletek a Azure Batchban
description: Ismerje meg a számítási csomópontokat és készleteket, valamint azt, hogyan használják őket egy Azure Batch munkafolyamatban fejlesztési szempontból.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 7d4c2d45849deb011498efe4c8a1ae91724b9acd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563895"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Csomópontok és készletek a Azure Batchban

Egy Azure Batch munkafolyamatban egy *számítási csomópont* (vagy *csomópont*) egy olyan virtuális gép, amely az alkalmazás munkaterhelésének egy részét dolgozza fel. A *készlet* ezen csomópontok gyűjteménye, amelyeken az alkalmazás fut. Ez a cikk további tudnivalókat tartalmaz a csomópontokról és a készletekről, valamint a Azure Batch munkafolyamatokban való létrehozásával és használatával kapcsolatos szempontokat.

## <a name="nodes"></a>Csomópontok

A csomópont egy Azure-beli virtuális gép (VM) vagy Cloud Service VM, amely az alkalmazás számítási feladatainak egy részének feldolgozására van kijelölve. A csomópont mérete határozza meg a CPU-magok számát, a memóriakapacitást és a csomóponthoz lefoglalt helyi fájlrendszeri méretet.

Windows- és Linux-csomópontokból az Azure Cloud Services, az [Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)-ről származó rendszerképek vagy az Ön által előkészített egyéni rendszerképek segítségével hozhat létre készleteket.

A csomópontok minden olyan végrehajtható fájlt vagy parancsprogramot képesek futtatni, amelyet a csomópont operációsrendszer-környezete támogat. Végrehajtható fájlok vagy parancsfájlok: \* . exe, \* . cmd, \* . bat és PowerShell-parancsfájlok (Windows rendszeren) és bináris fájlok, rendszerhéj és Python-parancsfájlok (Linux esetén).

A Batch szolgáltatásban működő számítási csomópontok emellett a következőket is tartalmazzák:

- Szabványos [mappastruktúra](files-and-directories.md), valamint az ehhez tartozó [környezeti változók](jobs-and-tasks.md), amelyekre a tevékenységek hivatkozni tudnak.
- A hozzáférés vezérléséhez konfigurált **tűzfalbeállítások**.
- [Távelérés](error-handling.md#connect-to-compute-nodes) a Windows (RDP protokoll (RDP) és a Linux (Secure Shell)-csomópontokhoz (kivéve, ha [a készletet a távoli hozzáférés letiltásával hozza létre](pool-endpoint-configuration.md)).

Alapértelmezés szerint a csomópontok képesek kommunikálni egymással, de nem tudnak kommunikálni olyan virtuális gépekkel, amelyek nem azonos készlet részét képezik. Ahhoz, hogy a csomópontok biztonságosan kommunikáljanak más virtuális gépekkel vagy helyszíni hálózattal, a készletet egy [Azure-beli virtuális hálózat (VNet) alhálózatában](batch-virtual-network.md)is kiépítheti. Ha így tesz, a csomópontok nyilvános IP-címeken keresztül is elérhetők. Ezeket a nyilvános IP-címeket a Batch hozza létre, és a készlet élettartamára változhat. Létrehozhat egy Ön által vezérelt [statikus nyilvános IP-címmel rendelkező készletet](create-pool-public-ip.md) is, amely biztosítja, hogy a rendszer váratlanul ne változzon meg.

## <a name="pools"></a>Készletek

A készlet az alkalmazás által futtatott csomópontok gyűjteménye.

Az Azure Batch-fiókok az Azure számítási platformjára épülnek. Nagy léptékű kiosztást, alkalmazások telepítését, adatelosztást, állapot-figyelést és rugalmas beállítást ([skálázást](#automatic-scaling-policy)) biztosítanak a készletben lévő számítási csomópontok számának megadásához.

A készlethez adott minden csomóponthoz egyedi név és IP-cím van rendelve. Amikor eltávolít egy csomópontot a készletből, elveszíti az operációs rendszeren vagy a fájlokon végzett módosításokat, és a rendszer kiadja a csomópont nevét és IP-címét további használatra. Amikor egy csomópont kikerül egy készletből, vége van az élettartamának.

A készleteket kizárólag az a Batch-fiók használhatja, amelyben létrehozták őket. A Batch-fiókok több készletet is létrehozhatnak, hogy megfeleljenek a futtatni kívánt alkalmazások erőforrás-követelményeinek.

A készletet manuálisan vagy automatikusan is létrehozhatja [a Batch szolgáltatás](#autopools) , ha megadja a elvégzendő munkát. Készlet létrehozásakor a következő attribútumokat adhatja meg:

- [Csomópont operációs rendszere és verziója](#operating-system-and-version)
- [Csomópont típusa és a csomópontok célként megadott száma](#node-type-and-target)
- [Csomópont mérete](#node-size)
- [Automatikus skálázási szabályzat](#automatic-scaling-policy)
- [Tevékenységütemezési szabályzat](#task-scheduling-policy)
- [Kommunikációs állapot](#communication-status)
- [Tevékenységek indítása](#start-tasks)
- [Alkalmazáscsomagok](#application-packages)
- [A virtuális hálózat (VNet) és a tűzfal konfigurálása](#virtual-network-vnet-and-firewall-configuration)
- [Élettartama](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> A Batch-fiókhoz tartozik egy alapértelmezett kvóta, amely korlátozza a magok számát a Batch-fiókokban. A magok száma a számítási csomópontok számának felel meg. Az alapértelmezett kvótákat és a [kvóták növelésével](batch-quota-limit.md#increase-a-quota) kapcsolatos útmutatásokat [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Az Azure Batch szolgáltatás kvótái és korlátai) című témakörben találja. A magkvóta lehet az oka, hogy a készlet esetleg nem éri el a csomópontok kitűzött számát.

## <a name="operating-system-and-version"></a>Operációs rendszer és verzió

Batch-készlet létrehozásakor meg kell adnia az Azure-beli virtuális gép konfigurációját, valamint a készlet egyes számítási csomópontjain futtatni kívánt operációs rendszer típusát.

## <a name="configurations"></a>Konfigurációk

A Batch-ben két típusú készlet-konfiguráció érhető el.

> [!IMPORTANT]
> Habár a készleteket jelenleg a konfigurációval is létrehozhatja, az új készleteket a virtuális gép konfigurációjának használatával kell konfigurálni, és nem Cloud Services konfigurációt. A virtuálisgép-konfigurációs készletek minden aktuális és új batch-funkciót támogatni fognak. Cloud Services konfigurációs készletek nem támogatják az összes funkciót, és nem terveznek új képességeket. Az új "CloudServiceConfiguration" készleteket nem lehet létrehozni, vagy új csomópontokat hozzáadni a meglévő készletekhez a [2024. február 29. után](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

### <a name="virtual-machine-configuration"></a>Virtuális gép konfigurációja

A **virtuális gép konfigurációja** megadja, hogy a készlet Azure-beli virtuális gépekből áll. Ezek a virtuális gépek Linux- vagy Windows-rendszerképből is létrehozhatók.

A [Batch-csomóponti ügynök](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) egy olyan program, amely a készlet minden egyes csomópontján fut, és a parancs-és vezérlési felületet biztosítja a csomópont és a Batch szolgáltatás között. A csomópont-ügynök különböző implementációkat mutat be különböző operációs rendszerekhez. Ha a virtuálisgép-konfiguráción alapuló készletet hoz létre, a csomópontok mérete és a létrehozásukhoz használt rendszerképek forrása mellett a **virtuális gép képhivatkozását** és a csomópontokra telepítendő **Batch-csomóponti ügynök SKU-ját** is meg kell adnia. A készlet e tulajdonságainak megadásával kapcsolatos további információk: [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Linuxos számítási csomópontok kiépítése Azure Batch-készletekben). Csatolhat egy vagy több üres adatlemezt a Marketplace-ről származó lemezképekből létrehozott virtuális gépek készletté alakításához, vagy adatlemezeket foglalhat az egyéni rendszerképekbe a virtuális gépek létrehozásához. Az adatlemezek belefoglalásakor a lemezeket csatlakoztatnia kell a virtuális gépről, és formázni kell őket a használatuk során.

### <a name="cloud-services-configuration"></a>Cloud Services konfiguráció

> [!WARNING]
> Cloud Services konfigurációs készletek [elavultak](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Használja helyette a virtuális gépek konfigurációs készleteit. További információ: batch- [készlet konfigurációjának Áttelepítése Cloud Servicesról virtuális gépre](batch-pool-cloud-service-to-virtual-machine-configuration.md).

A **Cloud Services konfiguráció** azt adja meg, hogy a készlet az Azure Cloud Services-csomópontokból tevődik össze. Cloud Services csak Windows számítási csomópontokat biztosít.

A Cloud Services konfigurációs készletekhez elérhető operációs rendszerek az [Azure vendég operációs rendszer kiadásainak és az SDK-kompatibilitási mátrixnak](../cloud-services/cloud-services-guestos-update-matrix.md)vannak felsorolva, és az elérhető számítási csomópontok méretei a [Cloud Services méretében](../cloud-services/cloud-services-sizes-specs.md)vannak felsorolva. Cloud Services csomópontokat tartalmazó készlet létrehozásakor meg kell adnia a csomópont méretét és az operációsrendszer- *családját* (amely meghatározza, hogy a .net mely verzióit telepíti az operációs rendszer). Cloud Services a Windows rendszerű virtuális gépeknél gyorsabban üzembe helyezhető az Azure-ban. Ha windowsos számítási csomópontok készleteire van szükség, előfordulhat, hogy a Cloud Services üzembe helyezési idő szempontjából teljesítményelőnyt nyújt.

A Cloud Serviceson belüli feldolgozói szerepkörökhöz hasonlóan megadhat egy *operációsrendszer-verziót* is. Azt javasoljuk, hogy az `Latest (*)` *operációs rendszer verziójának* megadásával a csomópontok automatikusan frissüljenek, és nem szükséges dolgozni az újonnan kiadott verziókhoz. Azért tanácsos megadni a konkrét operációsrendszer-verziót, mert így garantálható az alkalmazások kompatibilitása, hogy így a korábbi verziókkal való kompatibilitási tesztet lehessen végezni a verziófrissítés engedélyezése előtt. Az ellenőrzés után frissítheti a készlet *operációsrendszer-verzióját* , és telepítheti az új operációsrendszer-rendszerképet is. A rendszer megszakítja a futó feladatokat, és újból várólistára helyezi őket.

### <a name="node-agent-skus"></a>Csomóponti ügynök SKU-i

Készletek létrehozásakor ki kell választania a megfelelő **nodeAgentSkuId** értéket a virtuális merevlemez alapképének operációs rendszerétől függően. Az elérhető csomópont-ügynök SKU-azonosítóinak leképezése az operációsrendszer-rendszerkép hivatkozásaira a [lista támogatott csomópont-ügynök SKU](/rest/api/batchservice/list-supported-node-agent-skus) -jának meghívásával kérhető le.

### <a name="custom-images-for-virtual-machine-pools"></a>Egyéni rendszerképek virtuálisgép-készletekhez

Ha szeretné megtudni, hogyan hozhat létre egyéni rendszerképekkel rendelkező készletet, tekintse meg [a közös rendszerkép-katalógus használata egyéni készlet létrehozásához](batch-sig-images.md)című témakört.

Azt is megteheti, hogy a virtuális gépek egyéni készletét [felügyelt rendszerkép](batch-custom-images.md) -erőforrás használatával hozza létre. További tudnivalókat az Azure-beli virtuális gépekről származó egyéni Linux-rendszerképek előkészítéséről a [rendszerkép virtuális gépről vagy virtuális merevlemezről történő készítését](../virtual-machines/linux/capture-image.md) ismertető cikkben találhat. További tudnivalókat az Azure-beli virtuális gépekről származó egyéni Windows-rendszerképek előkészítéséről a [felügyelt rendszerkép egy Azure-beli általánosított virtuális gépről történő létrehozását](../virtual-machines/windows/capture-image-resource.md) ismertető cikkben találhat.

### <a name="container-support-in-virtual-machine-pools"></a>Tárolótámogatás a virtuálisgép-készletekben

Amikor a Batch API-kkal hoz létre virtuálisgép-konfigurációs készletet, beállíthatja a készletet, hogy Docker-tárolókban futtasson feladatokat. Jelenleg a Docker-tárolókat támogató rendszerképpel kell létrehoznia a készletet. Használja a Windows Server 2016 Datacentert az Azure Marketplace-ről származó tárolói rendszerképpel, vagy adjon meg olyan egyéni virtuálisgép-rendszerképet, amely tartalmazza a Docker Community Edition vagy Enterprise kiadását és az összes szükséges illesztőt. A készlet beállításainak tartalmazniuk kell egy [tárolókonfigurációt](/rest/api/batchservice/pool/add), amely tárolórendszerképeket másol a virtuális gépekre a készlet létrehozásakor. A készleten futó feladatok ezután hivatkozhatnak a tárolórendszerképre és a tároló által futtatott beállításokra.

További információt a [Docker-tárolóalkalmazásoknak az Azure Batch-ben történő futtatásával](batch-docker-container-workloads.md) foglalkozó cikkben tekinthet meg.

## <a name="node-type-and-target"></a>Csomópont típusa és célja

Készlet létrehozásakor megadhatja, hogy milyen típusú csomópontokat kíván használni. A csomópontok két típusa:

- **Dedikált csomópontok.** A dedikált számítási csomópontok az adott feladatra vannak fenntartva. Költségesebbek az alacsony prioritású csomópontoknál, de biztosan nem szorulnak háttérbe.
- **Alacsony prioritású csomópontok.** Az alacsony prioritású csomópontok az Azure többletkapacitását használják ki a Batch-feladatok futtatásához. Az alacsony prioritású csomópontok olcsóbbak a dedikált csomópontok esetében, és lehetővé teszik a jelentős számítási teljesítményt igénylő munkaterhelések használatát. További információ: [Alacsony prioritású virtuális gépek használata a Batch szolgáltatással](batch-low-pri-vms.md).

Az alacsony prioritású csomópontok akkor is előzik, ha az Azure-ban nincs elég felesleges kapacitás. Amennyiben egy csomópont feladatok futása közben szorul háttérbe, akkor a feladatok visszakerülnek a várakozási sorba, és újra futnak, amikor a számítási csomópont ismét elérhetővé válik. Az alacsony prioritású csomópontokat akkor érdemes választani, ha a feladat végrehajtási ideje rugalmas, és a munka sok csomóponton oszlik meg. Mielőtt az alacsony prioritású csomópontok használata mellett döntene, győződjön meg arról, hogy az előzetes lefoglalás miatt elveszett munka minimális és könnyen újra létrehozható lesz-e.

Ugyanabban a készletben alacsony prioritású és dedikált csomópontok is lehetnek. A csomópontok minden típusa saját célértéket tartalmaz, amelynél megadhatja a csomópontok kívánt számát.

A számítási csomópontok számát azért nevezzük *cél*-értéknek, mert előfordulhat, hogy a készletben nem lehet a kívánt számú csomópontot alkalmazni. Egy készlet például nem érheti el a célértéket, ha először a Batch-fiók [magkvótáját](batch-quota-limit.md) éri el. Vagy előfordulhat, hogy a készlet nem éri el a célt, ha egy automatikus skálázási képletet alkalmazott a készlethez, amely korlátozza a csomópontok maximális számát.

Az alacsony prioritású és a dedikált csomópontok díjszabásáról a [Batch díjszabása](https://azure.microsoft.com/pricing/details/batch/)című témakörben olvashat.

## <a name="node-size"></a>Csomópont mérete

Azure Batch-készlet létrehozásakor az Azure-ban elérhető szinte összes virtuálisgép-család és -méret közül választhat. Az Azure számos különböző virtuálisgép-méretet nyújt a különböző számítási feladatokhoz, beleértve a speciális [HPC-](../virtual-machines/sizes-hpc.md) vagy [GPU-kompatibilis](../virtual-machines/sizes-gpu.md) virtuálisgép-méreteket. Vegye figyelembe, hogy a csomópontok méretei csak a készlet létrehozásakor választhatók ki. Más szóval a készlet létrehozása után a csomópont mérete nem módosítható.

További információ: [Számítási csomópontok virtuálisgép-méretének kiválasztása Azure Batch-készletben](batch-pool-vm-sizes.md).

## <a name="automatic-scaling-policy"></a>Automatikus skálázási szabályzat

A dinamikus számítási feladatokhoz automatikus skálázási szabályzatot alkalmazhat a készletre. A Batch szolgáltatás rendszeres időközönként kiértékeli a képletet, és dinamikusan módosítja a készletben lévő csomópontok számát a számítási forgatókönyv aktuális munkaterhelése és erőforrás-használata alapján. Ez lehetővé teszi az alkalmazásfuttatás teljes költségeinek csökkentését, mivel csak a szükséges erőforrásokat használja, és felszabadítja a szükségteleneket.

Az automatikus skálázáshoz írnia kell egy [automatikus skálázási képletet](batch-automatic-scaling.md#autoscale-formulas), amelyet aztán társítania kell a készlethez. A Batch szolgáltatás ezzel a képlettel határozza meg a készletben működő csomópontok célszámát a következő skálázási intervallumhoz (amelyet Ön állíthat be). A készlet automatikus skálázását a készlet létrehozásakor és később egyaránt bekapcsolhatja. A skálázási beállításokat a skálázás bekapcsolása után is módosíthatja.

Előfordulhat például, hogy egy feladathoz nagy mennyiségű feladatot kell elküldenie. Ekkor skálázási képletet rendelhet a készlethez, amely a sorban álló tevékenységek aktuális száma és a feladathoz tartozó tevékenységek befejezettségi állapota alapján határozza meg a készletben működő csomópontok számát. A Batch szolgáltatás időnként kiértékeli a képletet és átméretezi a készletet a számítási feladatok és a képlet egyéb beállításai alapján. A szolgáltatás szükség szerint csomópontokat ad hozzá, amikor nagy számú tevékenység van a várakozási sorban, és eltávolít csomópontokat, amikor nincs futó tevékenység a várakozási sorban.

A méretezési képletek a következő mérőszámokon alapulhatnak:

- Az **időmérőszámok** a megadott számú órák során öt percenként gyűjtött statisztikák alapján kaphatók meg.
- Az **erőforrás-mérőszámok** a CPU-használat, a sávszélesség-használat, a memóriahasználat és a csomópontok száma alapján számíthatók ki.
- A **tevékenységmetrikák** alapját a tevékenységállapotok, például *Aktív* (sorban áll), *Fut* vagy *Befejezve* képezik.

Amikor az automatikus skálázás csökkenti a készletben működő csomópontok számát, érdemes megfontolni, hogy mi történjen a csökkentési művelet idején még futó tevékenységekkel. Ennek elvégzéséhez a Batch olyan [*csomópont-felszabadítási lehetőséget*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) biztosít, amelyet felvehet a képletbe. Megadhatja például, hogy a rendszer azonnal leállítsa a futó tevékenységeket, majd egy másik csomóponton sorba állítsa a tevékenységeket végrehajtás céljából, vagy hagyja őket lefutni, és csak ezután távolítsa el a csomópontot a készletből. Vegye figyelembe, hogy a csomópont-felszabadítási beállítás a vagy a érték megadásával `taskcompletion` `retaineddata` megakadályozza a készlet átméretezési műveleteit, amíg az összes tevékenység be nem fejeződik, vagy az összes tevékenység megőrzési időszaka lejárt.

Az alkalmazások automatikus méretezésével kapcsolatos további információért lásd: [Számítási csomópontok automatikus méretezése egy Azure Batch-készletben](batch-automatic-scaling.md).

> [!TIP]
> A számítási erőforrások kihasználtságának maximalizálása érdekében állítsa nullára a csomópontok célszámát a feladatok végén, de engedélyezze a futó tevékenységek befejeződését.

## <a name="task-scheduling-policy"></a>Tevékenységütemezési szabályzat

A [csomópontonkénti maximális tevékenység](batch-parallel-node-tasks.md) konfigurációs lehetőség határozza meg a készleten belüli egyes számítási csomópontokon egyidejűleg futtatható tevékenységek maximális számát.

Az alapértelmezett konfiguráció meghatározza, hogy egyszerre egy tevékenység fut egy csomóponton, de bizonyos esetekben előnyös lehet, ha kettő vagy több feladat is végrehajtható egyszerre egy csomóponton. Nézze meg a [csomópontokon végzett egyidejű tevékenységekről szóló cikkben](batch-parallel-node-tasks.md) szereplő [mintaforgatókönyvet](batch-parallel-node-tasks.md#example-scenario), ha kíváncsi rá, hogy milyen esetekben lehet hasznos, ha több tevékenység fut az egyes csomópontokon.

Megadhat egy *kitöltési típust* is, amely meghatározza, hogy a Batch egyenletesen osztja-e el a tevékenységeket a készlet összes csomópontján, vagy az egyes csomópontokat a maximális számú tevékenységgel, mielőtt hozzárendeli a tevékenységeket egy másik csomóponthoz.

## <a name="communication-status"></a>Kommunikációs állapot

A legtöbb esetben a tevékenységek egymástól függetlenül működnek, nincs szükség közöttük kommunikációra. Vannak azonban olyan alkalmazások (például [MPI-megoldások](batch-mpi.md)), ahol a tevékenységeknek kommunikálniuk kell egymással.

A készletet konfigurálhatja úgy, hogy engedélyezze a **csomópontok közötti kommunikációt** , hogy a készletben lévő csomópontok is kommunikálhatnak a futtatókörnyezetben. Ha engedélyezte a csomópontok közötti kommunikációt, a Cloud Services-konfigurációt használó készletekben működő csomópontok az 1100-nál magasabb portszámú portokon, a virtuálisgép-konfigurációs készletek csomópontjai pedig bármely porton képesek lesznek kommunikálni egymással.

A csomópontok közötti kommunikáció engedélyezése is hatással van a csomópontok elhelyezésére a fürtökön belül, és korlátozhatja a készletben lévő csomópontok maximális számát a telepítési korlátozások miatt. Ha az alkalmazáshoz nincs szükség a csomópontok közötti kommunikációra, a Batch szolgáltatás nagy számú csomópont lefoglalására képes a készlethez számos különböző fürtből és adatközpontból, hogy lehetővé tegye a nagyobb párhuzamos feldolgozási teljesítményt.

## <a name="start-tasks"></a>Tevékenységek indítása

Ha kívánja, hozzáadhat egy [indítási tevékenységet](jobs-and-tasks.md#start-task) , amely minden egyes csomóponton végrehajtja a készletet, és minden alkalommal, amikor a csomópont újraindul vagy alaphelyzetbe áll. Az indítási tevékenység különösen hasznos a számítási csomópontok tevékenységek végrehajtására való előkészítéséhez, például a tevékenységek által a számítási csomópontokon futtatott alkalmazások telepítéséhez.

## <a name="application-packages"></a>Alkalmazáscsomagok

A készlethez tartozó számítási csomópontokra üzembe helyezni kívánt alkalmazáscsomagokat is meghatározhatja. Az alkalmazáscsomagok leegyszerűsítik a tevékenységek által futtatott alkalmazások üzembe helyezését és verziókezelését. A készlethez beállított alkalmazáscsomagokat a rendszer a készlethez csatlakozó összes csomópontra telepíti, illetve minden alkalommal telepíti őket, amikor egy csomópontot újraindítanak vagy rendszerképét alaphelyzetbe állítják.

További információkat az alkalmazások a Batch-csomópontokon alkalmazáscsomagok használatával történő központi telepítéséről a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>A virtuális hálózat (VNet) és a tűzfal konfigurálása

Amikor számítási csomópontok készletét hozza létre a Batchben, hozzárendelheti a készletet egy Azure-beli [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md) alhálózatához. Egy Azure-beli virtuális hálózat használatához a Batch-ügyfél API-jának Azure Active Directory- (AD-) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört.

### <a name="vnet-requirements"></a>A virtuális hálózat követelményei

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

A Batch-készletek virtuális hálózatban való beállítására vonatkozó további információért lásd: [Virtuális gépek készletének létrehozása a virtuális hálózattal](batch-virtual-network.md).

> [!TIP]
> Annak érdekében, hogy a csomópontokhoz való hozzáféréshez használt nyilvános IP-címek ne változzon meg, létrehozhat [egy olyan készletet, amely a megadott nyilvános IP-címekkel](create-pool-public-ip.md)rendelkezik.

## <a name="pool-and-compute-node-lifetime"></a>Készlet és számítási csomópont élettartama

A Azure Batch megoldás tervezésekor meg kell adnia a készletek létrehozásának módját és idejét, valamint azt, hogy mennyi ideig legyenek elérhetőek a készletekben lévő számítási csomópontok.

Az egyik szélsőség, ha azt állítja be, hogy a rendszer hozza létre a készletet az Ön által elküldött egyes feladatokhoz, és azonnal törölje a készletet, amint az egyes feladatok tevékenységeinek végrehajtása befejeződött. Ez maximalizálja a kihasználtságot, mivel a csomópontok csak szükség esetén vannak lefoglalva, és az üresjáratuk után le vannak állítva. Habár ez azt jelenti, hogy a feladatnak meg kell várnia a csomópontok lefoglalását, fontos megjegyezni, hogy a feladatok ütemezve lettek a végrehajtásra, amint a csomópontok egyedileg vannak lefoglalva, és az indítási tevékenység befejeződött. A Batch *nem* vár addig a tevékenységek csomópontokhoz rendelésével, amíg a készletben lévő összes csomópont elérhetővé válik. Így garantálható az összes elérhető csomópont maximális kihasználtsága.

A másik végletet akkor érdemes választani, ha a feladatok azonnali indítása a legfontosabb: ilyenkor a feladatok elküldése előtt hozza létre a készletet, és tegye elérhetővé a csomópontokat. Ebben a megoldásban a tevékenységek azonnal indulhatnak, de a csomópontok tétlenek lehetnek a tevékenységek hozzárendeléséig.

A változó, de folyamatos terhelések kezeléséhez általában egy kombinált módszert használunk. Rendelkezhet olyan készlettel, amelyben több feladat is elküldhető, és a feladatok terhelésének megfelelően méretezheti a csomópontok számát. Ez reaktív módon, az aktuális terhelés alapján is elvégezhető, de proaktív módszert is használhat, ha a terhelés előrejelezhető. További információ: [automatikus skálázási szabályzat](#automatic-scaling-policy).

## <a name="autopools"></a>Alapkészletek

Az [autopool](/rest/api/batchservice/job/add#autopoolspecification) olyan készlet, amelyet a Batch szolgáltatás a feladat elküldésekor hoz létre, nem pedig a készletben futtatandó feladatok előtt. A Batch szolgáltatás az Ön által megadott jellemzők szerint fogja kezelni az autopool élettartamát. A legtöbb esetben ezek a készletek a feladatok befejezését követően automatikusan törlésre is vannak beállítva.

## <a name="security-with-certificates"></a>Biztonság tanúsítványokkal

Általában tanúsítványokat kell használnia tevékenységek bizalmas információinak, például az [Azure Storage-fiókok](accounts.md#azure-storage-accounts) kulcsának titkosításakor vagy visszafejtésekor. Ehhez tanúsítványokat telepíthet a csomópontokra. A titkosított titkos kulcsok parancssori paraméterek segítségével vagy valamelyik tevékenység-erőforrásba ágyazva jutnak el a tevékenységekhez, és a telepített tanúsítványokkal fejthetők vissza.

A [Tanúsítvány hozzáadása](/rest/api/batchservice/certificate/add) művelettel (Batch REST) vagy a [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) metódussal (Batch .NET) adhat tanúsítványt Batch-fiókhoz. Ezután a tanúsítványt új vagy meglévő készlethez társíthatja.

Amikor egy tanúsítvány egy készlethez van társítva, a Batch szolgáltatás telepíti a tanúsítványt a készlet minden csomópontján. A Batch szolgáltatás telepíti a megfelelő tanúsítványokat a csomópont indításakor a feladatok elindítása előtt (beleértve az [indítási feladatot](jobs-and-tasks.md#start-task) és a Feladatkezelő [feladatot](jobs-and-tasks.md#job-manager-task)).

Ha meglévő készlethez ad hozzá tanúsítványt, újra kell indítania a számítási csomópontokat ahhoz, hogy a tanúsítványt alkalmazni lehessen a csomópontokra.

## <a name="next-steps"></a>Következő lépések

- A [feladatok és a feladatok](jobs-and-tasks.md)megismerése.
- Ismerje meg, hogyan lehet [észlelni és elkerülni a készletek és csomópontok háttérben végzett műveleteinek hibáit ](batch-pool-node-error-checking.md).

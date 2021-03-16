---
title: Készlet-és csomópont-hibák keresése
description: Ez a cikk az esetlegesen előforduló háttér-műveleteket, valamint a készletek és csomópontok létrehozásakor fellépő hibákat és azok elkerülését ismerteti.
author: mscurrell
ms.author: markscu
ms.date: 03/15/2021
ms.topic: how-to
ms.openlocfilehash: 4a0d3e017f36f580024b77fbd23145d7447f336d
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564405"
---
# <a name="check-for-pool-and-node-errors"></a>Készlet-és csomópont-hibák keresése

Azure Batch készletek létrehozásakor és kezelésekor előfordulhat, hogy bizonyos műveletek azonnal megtörténnek. A hibák észlelése általában egyszerű, mivel azokat azonnal az API, a CLI vagy a felhasználói felület adja vissza. Bizonyos műveletek azonban aszinkron módon futnak a háttérben, és több percet is igénybe vehetnek.

Ellenőrizze, hogy beállította-e az alkalmazások átfogó hibaellenőrzés megvalósítását, különösen aszinkron műveletekhez. Ez segítséget nyújt a problémák gyors azonosításához és diagnosztizálásához.

Ez a cikk a készletek és a készlet csomópontjain előforduló háttérben végzett műveletek észlelésének és elkerülésének módjait ismerteti.

## <a name="pool-errors"></a>Készlet hibái

### <a name="resize-timeout-or-failure"></a>Időtúllépés vagy hiba átméretezése

Új készlet létrehozásakor vagy egy meglévő készlet átméretezése esetén meg kell adnia a csomópontok célként megadott számát. A létrehozási vagy átméretezési művelet azonnal befejeződik, de az új csomópontok tényleges kiosztása vagy a meglévő csomópontok eltávolítása több percet is igénybe vehet. Megadhatja az átméretezési időtúllépést a [create](/rest/api/batchservice/pool/add) vagy az [lakik](/rest/api/batchservice/pool/resize) API-ban. Ha a Batch nem tudja megszerezni a csomópontok megcélzott számát az átméretezési időkorlát időtartama alatt, a készlet stabil állapotba kerül, és átméretezési hibákat jelez.

A legutóbbi értékelés [ResizeError](/rest/api/batchservice/pool/get#resizeerror) tulajdonsága felsorolja az észlelt hibákat.

Az átméretezési hibák gyakori okai a következők:

- Az átméretezés időtúllépése túl rövid
  - A legtöbb esetben az alapértelmezett 15 perces időkorlát elég hosszú ahhoz, hogy a készlet csomópontjai le legyenek foglalva vagy el lesznek távolítva.
  - Ha nagy számú csomópontot foglal le, javasoljuk, hogy az átméretezési időtúllépést 30 percre állítsa be. Például, ha több mint 1 000 csomópontra végez átméretezést egy Azure Marketplace-rendszerképből vagy egy egyéni virtuálisgép-rendszerképből származó több mint 300 csomópontra.
- Nincs elég alapvető kvóta
  - A Batch-fiók az összes készletben lefoglalható magok számával van korlátozva. A Batch leállítja a csomópontok lefoglalását, miután elérte a kvótát. [Növelheti](./batch-quota-limit.md) az alapszintű kvótát, hogy a Batch több csomópontot foglaljon le.
- Nem elegendő alhálózat IP-címe, ha a [készlet virtuális hálózaton van](./batch-virtual-network.md)
  - A virtuális hálózat alhálózatának elegendő, nem hozzárendelt IP-címmel kell rendelkeznie az összes kért készlet-csomóponthoz való lefoglaláshoz. Ellenkező esetben a csomópontok nem hozhatók létre.
- Nincs elegendő erőforrás, ha a [készlet virtuális hálózaton van](./batch-virtual-network.md)
  - A Batch-fiókkal megegyező előfizetésben létrehozhat olyan erőforrásokat, mint a terheléselosztó, a nyilvános IP-címek és a hálózati biztonsági csoportok. Győződjön meg arról, hogy az előfizetési kvóták elégségesek ezekhez az erőforrásokhoz.
- Egyéni virtuálisgép-rendszerképekkel rendelkező nagyméretű készletek
  - Az egyéni virtuálisgép-rendszerképeket használó nagyméretű készletek hosszabb időt vehetnek igénybe az időtúllépések lefoglalása és átméretezése esetén.  A korlátokkal és a konfigurációval kapcsolatos javaslatokért tekintse meg a [készlet létrehozása a megosztott rendszerkép](batch-sig-images.md) -katalógussal című témakört.

### <a name="automatic-scaling-failures"></a>Automatikus skálázási hibák

A Azure Batch beállítható úgy, hogy a készletben lévő csomópontok számát automatikusan méretezni lehessen. Meghatározhatja a [készlet automatikus skálázási képletének](./batch-automatic-scaling.md)paramétereit. A Batch szolgáltatás ezután a képlettel rendszeres időközönként kiértékeli a készletben lévő csomópontok számát, és beállítja az új célként megadott számot.

Az automatikus skálázás használatakor a következő típusú problémák léphetnek fel:

- Az automatikus skálázás kiértékelése sikertelen.
- Az eredményül kapott átméretezési művelet meghiúsul, és időtúllépést jelez.
- Az automatikus skálázási képlettel kapcsolatos probléma helytelen csomópont-célértékek elérését eredményezi. Az átméretezés akár működik, akár időtúllépést is mutat.

Az utolsó automatikus skálázási kiértékeléssel kapcsolatos információkért használja a [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun) tulajdonságot. Ez a tulajdonság a kiértékelési időt, az értékeket és az eredményt, valamint a teljesítménnyel kapcsolatos hibákat jelenti.

A [készlet átméretezése kész esemény](./batch-pool-resize-complete-event.md) rögzíti az összes értékelés információit.

### <a name="pool-deletion-failures"></a>Készlet törlési hibái

Csomópontokat tartalmazó készlet törlésekor az első köteg törli a csomópontokat. Ez több percig is eltarthat. Ezt követően a Batch maga törli a készlet objektumot.

A Batch beállítja a [készlet állapotát](/rest/api/batchservice/pool/get#poolstate) **a törlési folyamat során.** A hívó alkalmazás képes megállapítani, hogy a készlet törlése túl hosszú időt vesz igénybe az **állapot** és a **stateTransitionTime** tulajdonság használatával.

Ha a készlet a vártnál hosszabb időt vesz igénybe, a köteg a készlet sikeres törlése után rendszeresen újrapróbálkozik. Bizonyos esetekben a késést egy Azure-szolgáltatás kimaradása vagy más ideiglenes probléma okozza. A készlet sikeres törlését megakadályozó egyéb tényezők szükségessé tehetik a probléma megoldásához szükséges műveletek elvégzését. Ezek a tényezők többek között a következők:

- Az erőforrás-zárolások a Batch által létrehozott erőforrásokra, illetve a Batch által használt hálózati erőforrásokra vannak helyezve.
- A létrehozott erőforrások függőségi viszonyban vannak egy batch által létrehozott erőforrással. Ha például [létrehoz egy készletet egy virtuális hálózatban](batch-virtual-network.md), a Batch létrehoz egy hálózati biztonsági CSOPORTOT (NSG), egy nyilvános IP-címet és egy terheléselosztó-t. Ha ezeket az erőforrásokat a készleten kívül használja, a készlet nem törölhető, amíg el nem távolítja a függőséget.
- A Microsoft.BatCH erőforrás-szolgáltató regisztrációja törölve lett a készletet tartalmazó előfizetésből.
- A (z) "Microsoft Azure Batch" már nem rendelkezik [közreműködői vagy tulajdonosi szerepkörrel](batch-account-create-portal.md#allow-azure-batch-to-access-the-subscription-one-time-operation) a készletet tartalmazó előfizetéshez (felhasználói előfizetés mód batch-fiókok esetén).

## <a name="node-errors"></a>Csomóponti hibák

Ha a Batch sikeresen foglal le csomópontokat egy készletben, a különböző problémák miatt előfordulhat, hogy a csomópontok némelyike nem kifogástalan állapotú, és nem tudja futtatni a feladatokat. Ezek a csomópontok továbbra is díjkötelesek, ezért fontos, hogy észlelje a nem használható csomópontok kifizetésének elkerülésével kapcsolatos problémákat. Az általános csomópont-hibák mellett a jelenlegi [feladatok állapotának](/rest/api/batchservice/job/get#jobstate) ismerete is hasznos lehet a hibaelhárításhoz.

### <a name="start-task-failures"></a>Indítási feladat hibái

Előfordulhat, hogy egy készlethez nem kötelező [indítási feladatot](/rest/api/batchservice/pool/add#starttask) szeretne megadni. A feladatokhoz hasonlóan a parancssorból és az erőforrás-fájlokból is letölthetők a tárterületről. Az indítási tevékenység minden egyes csomóponton az elindítása után fut. A **waitForSuccess** tulajdonság azt határozza meg, hogy a Batch várakozik-e, amíg az indítási tevékenység sikeresen be nem fejeződik, mielőtt a tevékenységeket egy csomópontra ütemezni.

Mi a teendő, ha úgy konfigurálta a csomópontot, hogy várjon a sikeres indítási tevékenység befejezésére, de az indítási feladat meghiúsul? Ebben az esetben a csomópont nem lesz használható, de továbbra is díjköteles.

Az indítási feladat hibáit a legfelső szintű [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) Node tulajdonság [eredmény](/rest/api/batchservice/computenode/get#taskexecutionresult) -és [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) tulajdonságainak használatával észlelheti.

A sikertelen indítási feladat azt is okozhatja, hogy a Batch beállítsa a csomópont [állapotát](/rest/api/batchservice/computenode/get#computenodestate) , hogy **starttaskfailed** , ha a  **waitForSuccess** értéke **true (igaz**).

A feladatok elvégzéséhez hasonlóan számos oka lehet az indítási feladat meghibásodása. A hibák megoldásához keresse meg az stdout, a stderr, valamint a feladatra vonatkozó további naplófájlokat.

Az indítási tevékenységeket újra kell adni, mivel lehetséges, hogy az indítási tevékenység többször is fut ugyanazon a csomóponton; az indítási tevékenység akkor fut le, amikor a csomópontot rendszerképbe állítja vagy újraindította. Ritka esetekben egy indítási tevékenység akkor fut le, ha egy esemény egy csomópont újraindítását okozta, ahol a másik operációs rendszer vagy ideiglenes lemez újra lett indítva, míg a másik nem volt. Mivel a Batch indítási feladatai (például az összes batch-feladat) az ideiglenes lemezről futnak, ez általában nem probléma, de bizonyos esetekben, amikor az indítási tevékenység telepíti az alkalmazást az operációsrendszer-lemezre, és más adatokkal látja el az ideiglenes lemezeket, ez problémákat okozhat, mert a dolgok nincsenek szinkronban. Ha mindkét lemezt használja, az alkalmazás védelme ennek megfelelően történik.

### <a name="application-package-download-failure"></a>Alkalmazáscsomag letöltése sikertelen

Egy készlethez egy vagy több alkalmazáscsomag is megadható. A Batch letölti a megadott csomagokat az egyes csomópontokra, és kibontja a fájlokat a csomópont elindítása után, de a feladatok ütemezése előtt. Gyakori, hogy az alkalmazás csomagjaival együtt a Start Task parancssort használják. Például a fájlok másik helyre való másolásához vagy a telepítő futtatásához.

A csomópont [hibái](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság az alkalmazáscsomag letöltésére és eltávolítására vonatkozó hibát jelez; a csomópont állapota **használhatatlanra** van állítva.

### <a name="container-download-failure"></a>Tároló letöltése sikertelen

Egy készlethez egy vagy több tároló-hivatkozást is megadhat. A Batch letölti a megadott tárolókat az egyes csomópontokra. A Node [errors](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság azt jelzi, hogy a tároló letöltése nem sikerült, és a csomópont állapotát nem **használható** értékre állítja.

### <a name="node-os-updates"></a>Csomópont operációs rendszerének frissítései

A Windows-készletek esetében alapértelmezés szerint a érték `enableAutomaticUpdates` van beállítva `true` . Az automatikus frissítések engedélyezése ajánlott, de a feladat előrehaladását is megszakíthatja, különösen akkor, ha a feladatok hosszú ideig futnak. Megadhatja ezt az értéket, `false` Ha biztosítania kell, hogy az operációs rendszer frissítése váratlanul megtörténjen.

### <a name="node-in-unusable-state"></a>A csomópont használhatatlan állapotban van

Azure Batch lehet, hogy a [csomópont állapota](/rest/api/batchservice/computenode/get#computenodestate) számos okból **használhatatlanná válik** . Ha a csomópont állapota **használhatatlan** értékre van állítva, a feladatok nem ütemezhetők a csomópontra, de a szolgáltatás továbbra is díjköteles.

A csomópontok **használhatatlan** állapotban vannak, de a [hibák](/rest/api/batchservice/computenode/get#computenodeerror) hiánya azt jelenti, hogy a Batch nem tud kommunikálni a virtuális géppel. Ebben az esetben a Batch mindig megkísérli helyreállítani a virtuális gépet. A Batch nem kísérli meg automatikusan azon virtuális gépek helyreállítását, amelyek nem tudták telepíteni az alkalmazáscsomag vagy a tárolókat, még ha az állapotuk nem **használható**.

Ha a Batch meghatározhatja az okot, a Node [errors](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság jelentést küld.

További példák a **használhatatlan** csomópontok okaira:

- Egy egyéni virtuálisgép-rendszerkép érvénytelen. Például egy nem megfelelően előkészített rendszerkép.
- Egy virtuális gép az infrastruktúra meghibásodása vagy egy alacsony szintű frissítés miatt kerül áthelyezésre. A Batch helyreállítja a csomópontot.
- A virtuálisgép-lemezképek olyan hardveren lettek telepítve, amely nem támogatja azt. Ha például egy CentOS HPC-rendszerképet szeretne futtatni egy [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) virtuális gépen.
- A virtuális gépek egy Azure-beli [virtuális hálózatban](batch-virtual-network.md)találhatók, és a forgalom le lett tiltva a legfontosabb portok számára.
- A virtuális gépek virtuális hálózaton vannak, de az Azure Storage-ba irányuló kimenő forgalom le van tiltva.
- A virtuális gépek egy ügyfél-DNS-konfigurációval rendelkező virtuális hálózaton vannak, és a DNS-kiszolgáló nem tudja feloldani az Azure Storage-t.

### <a name="node-agent-log-files"></a>Csomópont-ügynök naplófájljai

A Batch-ügynök minden egyes csomóponton futó folyamata olyan naplófájlokat biztosíthat, amelyek hasznosak lehetnek, ha kapcsolatba kell lépnie a készlet csomópontjaival kapcsolatos támogatással. A csomópontok naplófájljai a Azure Portalon, Batch Exploreron vagy egy [API](/rest/api/batchservice/computenode/uploadbatchservicelogs)-n keresztül tölthetők fel. A naplófájlok feltöltése és mentése hasznos. Ezután törölheti a csomópontot vagy a készletet a futó csomópontok díjainak mentéséhez.

### <a name="node-disk-full"></a>A csomópont lemeze megtelt

A készlethez tartozó virtuális gép ideiglenes meghajtóját a Batch a feladatok fájljai, a feladatok fájljai és a megosztott fájlok esetében használja, például a következőket:

- Alkalmazás-csomagok fájljai
- Feladat-erőforrás fájljai
- A Batch-mappák egyikére letöltött alkalmazás-specifikus fájlok
- Stdout-és stderr-fájlok az egyes feladatok alkalmazás-végrehajtásához
- Alkalmazásspecifikus kimeneti fájlok

A fájlok némelyike csak egyszer írható elő, amikor a készlet csomópontjai jönnek létre, például a készlet alkalmazáscsomag vagy a készlet indítási tevékenységének erőforrás-fájljai. Akkor is, ha a csomópont létrehozásakor csak egyszer íródik, ha ezek a fájlok túl nagyok, az ideiglenes meghajtót is kitölthetik.

A rendszer minden olyan feladatra kiírja a többi fájlt, amely egy csomóponton fut, például az stdout és a stderr. Ha nagy számú tevékenység fut ugyanazon a csomóponton, és/vagy a feladatok fájljai túl nagyok, akkor kitölthetik az ideiglenes meghajtót.

Az ideiglenes meghajtó mérete a virtuális gép méretétől függ. A virtuális gép méretének kiválasztásakor az egyik szempont, hogy az ideiglenes meghajtó elegendő lemezterülettel rendelkezzen.

- A készlet hozzáadásakor a Azure Portal a virtuálisgép-méretek teljes listáját jelenítheti meg, és az erőforrás lemez mérete oszlop is megjelenik.
- Az összes virtuálisgép-méretet ismertető cikk a "Temp Storage" oszlopot tartalmazó táblázatokat tartalmaz. például a [számítási optimalizált VM-méretek](../virtual-machines/sizes-compute.md)

Az egyes feladatok által írt fájlok esetében megadható az egyes feladatokhoz tartozó megőrzési idő, amely meghatározza, hogy a rendszer mennyi ideig tárolja a feladatokat, mielőtt automatikusan kitakarítja a fájlokat. A megőrzési idő csökkentheti a tárolási követelmények csökkentését.

Ha az ideiglenes lemez kifogy (vagy nagyon közel van a helyhez), a csomópont [használhatatlan](/rest/api/batchservice/computenode/get#computenodestate) állapotba kerül, és a rendszer azt jelzi, hogy a lemez megtelt.

Ha nem biztos abban, hogy mi a terület a csomóponton, próbálja meg a távelérést a csomóponton, és vizsgálja meg, hogy hol történt a terület. A [Batch Files API](/rest/api/batchservice/file/listfromcomputenode) -t is használhatja a Batch által felügyelt mappákban található fájlok vizsgálatához (például feladat kimenetei). Vegye figyelembe, hogy ez az API csak a Batch által felügyelt címtárakban található fájlokat sorolja fel. Ha a feladatait máshol hozta létre, azok nem jelennek meg.

Győződjön meg arról, hogy az összes szükséges adat le lett kérve a csomópontról, vagy feltöltve lett egy tartós tárolóba, majd a lemezterület felszabadításához szükség szerint törölje az adatokból.

Törölheti a régi befejezett feladatokat vagy a régi befejezett feladatokat, amelyek tevékenységi adatai még mindig a csomópontokon vannak. Tekintse meg a csomópont [RecentTasks-gyűjteményét](/rest/api/batchservice/computenode/get#taskinformation) , vagy a [csomóponton található fájlokban](/rest/api/batchservice/file/listfromcomputenode). Egy feladat törlésekor a feladat összes feladatát törli a rendszer. Ha törli a feladat feladatait, a rendszer törli a csomóponton lévő feladatok könyvtáraiban lévő adatokból a törölni kívánt helyet, így szabadít fel lemezterületet. Ha elegendő lemezterületet szabadít fel, indítsa újra a csomópontot, és a "használhatatlan" állapotból és a "tétlen" állapotúra kell lépnie.

A [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) -készletek használhatatlan csomópontjának helyreállításához eltávolíthatja a csomópontot a készletből a [csomópontok eltávolítása API](/rest/api/batchservice/pool/removenodes)használatával. Ezután ismét növelheti a készletet, hogy a rossz csomópontot egy frissre cserélje. [CloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) -készletek esetén a csomópontot a [Batch rendszerkép API](/rest/api/batchservice/computenode/reimage)-n keresztül újra fel lehet lemezképre állítani. Ezzel megtisztítja a teljes lemezt. A [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) -készletek jelenleg nem támogatják az újralemezképet.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [feladat-és feladatokkal kapcsolatos hibák ellenőrzéséről](batch-job-task-error-checking.md).
- Ismerje meg az Azure Batch használatának [ajánlott eljárásait](best-practices.md) .

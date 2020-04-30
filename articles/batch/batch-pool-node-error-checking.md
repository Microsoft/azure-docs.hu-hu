---
title: Készlet-és csomópont-hibák keresése
description: Ez a cikk az esetlegesen előforduló háttér-műveleteket, valamint a készletek és csomópontok létrehozásakor fellépő hibákat és azok elkerülését ismerteti.
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: 5051b9c536ded50e77fb75515c16daba884d5d24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115907"
---
# <a name="check-for-pool-and-node-errors"></a>Készlet-és csomópont-hibák keresése

Azure Batch készletek létrehozásakor és kezelésekor előfordulhat, hogy bizonyos műveletek azonnal megtörténnek. Bizonyos műveletek azonban aszinkron módon futnak a háttérben, és több percet is igénybe vehetnek.

A azonnal elvégezhető műveletek észlelése egyszerű, mert az API, a CLI vagy a felhasználói felület azonnal visszaadja a hibákat.

Ez a cikk a készletek és a készlet csomópontjain előforduló háttérben futó műveleteket ismerteti. Ez határozza meg, hogyan lehet észlelni és elkerülni a hibákat.

## <a name="pool-errors"></a>Készlet hibái

### <a name="resize-timeout-or-failure"></a>Időtúllépés vagy hiba átméretezése

Új készlet létrehozásakor vagy egy meglévő készlet átméretezése esetén meg kell adnia a csomópontok célként megadott számát.  A létrehozási vagy átméretezési művelet azonnal befejeződik, de az új csomópontok tényleges kiosztása vagy a meglévő csomópontok eltávolítása több percet is igénybe vehet.  Az átméretezési időtúllépést a [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) vagy az [átméretezés](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API-ban adhatja meg. Ha a Batch nem tudja megszerezni a csomópontok megcélzott számát az átméretezési időtúllépés időtartama alatt, a készlet stabil állapotba kerül, és a jelentések átméretezési hibákat jelez.

A legutóbbi értékelés [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) tulajdonsága felsorolja az észlelt hibákat.

Az átméretezési hibák gyakori okai a következők:

- Az átméretezés időtúllépése túl rövid
  - A legtöbb esetben az alapértelmezett 15 perces időkorlát elég hosszú ahhoz, hogy a készlet csomópontjai le legyenek foglalva vagy el lesznek távolítva.
  - Ha nagy számú csomópontot foglal le, javasoljuk, hogy az átméretezési időtúllépést 30 percre állítsa be. Például, ha több mint 1 000 csomópontra végez átméretezést egy Azure Marketplace-rendszerképből vagy egy egyéni virtuálisgép-rendszerképből származó több mint 300 csomópontra.
- Nincs elég alapvető kvóta
  - A Batch-fiók az összes készletben lefoglalható magok számával van korlátozva. A Batch leállítja a csomópontok lefoglalását, miután elérte a kvótát. [Növelheti](https://docs.microsoft.com/azure/batch/batch-quota-limit) az alapszintű kvótát, hogy a Batch több csomópontot foglaljon le.
- Nem elegendő alhálózat IP-címe, ha a [készlet virtuális hálózaton van](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - A virtuális hálózat alhálózatának elegendő, nem hozzárendelt IP-címmel kell rendelkeznie az összes kért készlet-csomóponthoz való lefoglaláshoz. Ellenkező esetben a csomópontok nem hozhatók létre.
- Nincs elegendő erőforrás, ha a [készlet virtuális hálózaton van](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - A Batch-fiókkal megegyező előfizetésben létrehozhat olyan erőforrásokat, mint a terheléselosztó, a nyilvános IP-címek és a hálózati biztonsági csoportok. Győződjön meg arról, hogy az előfizetési kvóták elégségesek ezekhez az erőforrásokhoz.
- Egyéni virtuálisgép-rendszerképekkel rendelkező nagyméretű készletek
  - Az egyéni virtuálisgép-rendszerképeket használó nagyméretű készletek hosszabb időt vehetnek igénybe az időtúllépések lefoglalása és átméretezése esetén.  A korlátokkal és a konfigurációval kapcsolatos javaslatokért tekintse meg a [készlet létrehozása a megosztott rendszerkép](batch-sig-images.md) -katalógussal című témakört.

### <a name="automatic-scaling-failures"></a>Automatikus skálázási hibák

Azure Batch beállítható úgy is, hogy a készletben lévő csomópontok számát automatikusan méretezni lehessen. Meghatározhatja a [készlet automatikus skálázási képletének](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)paramétereit. A Batch szolgáltatás a képlettel rendszeres időközönként kiértékeli a készletben lévő csomópontok számát, és új célértéket állít be. A következő típusú problémák léphetnek fel:

- Az automatikus skálázás kiértékelése sikertelen.
- Az eredményül kapott átméretezési művelet meghiúsul, és időtúllépést jelez.
- Az automatikus skálázási képlettel kapcsolatos probléma helytelen csomópont-célértékek elérését eredményezi. Az átméretezés akár működik, akár időtúllépést is mutat.

Az utolsó automatikus skálázási kiértékeléssel kapcsolatos információk a [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) tulajdonság használatával szerezhetők be. Ez a tulajdonság a kiértékelési időt, az értékeket és az eredményt, valamint a teljesítménnyel kapcsolatos hibákat jelenti.

A [készlet átméretezése kész esemény](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) rögzíti az összes értékelés információit.

### <a name="delete"></a>Törlés

Csomópontokat tartalmazó készlet törlésekor az első köteg törli a csomópontokat. Ezután maga törli a készlet objektumot. Néhány percet is igénybe vehet, amíg a készlet csomópontjai törölve lesznek.

A Batch beállítja a [készlet állapotát](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) **a törlési folyamat során.** A hívó alkalmazás képes megállapítani, hogy a készlet törlése túl hosszú időt vesz igénybe az **állapot** és a **stateTransitionTime** tulajdonság használatával.

## <a name="pool-compute-node-errors"></a>A készlet számítási csomópontjaival kapcsolatos hibák

Ha a Batch sikeresen foglal le csomópontokat egy készletben, a különböző problémák miatt előfordulhat, hogy a csomópontok némelyike nem kifogástalan állapotú, és nem tudja futtatni a feladatokat. Ezek a csomópontok továbbra is díjkötelesek, ezért fontos, hogy észlelje a nem használható csomópontok kifizetésének elkerülésével kapcsolatos problémákat. Az általános csomópont-hibák mellett a jelenlegi [feladatok állapotának](/rest/api/batchservice/job/get#jobstate) ismerete is hasznos lehet a hibaelhárításhoz.

### <a name="start-task-failures"></a>Indítási feladat hibái

Előfordulhat, hogy egy készlethez nem kötelező [indítási feladatot](/rest/api/batchservice/pool/add#starttask) szeretne megadni. A feladatokhoz hasonlóan a parancssorból és az erőforrás-fájlokból is letölthetők a tárterületről. Az indítási tevékenység minden egyes csomóponton az elindítása után fut. A **waitForSuccess** tulajdonság azt határozza meg, hogy a Batch várakozik-e, amíg az indítási tevékenység sikeresen be nem fejeződik, mielőtt a tevékenységeket egy csomópontra ütemezni.

Mi a teendő, ha úgy konfigurálta a csomópontot, hogy várjon a sikeres indítási tevékenység befejezésére, de az indítási feladat meghiúsul? Ebben az esetben a csomópont nem lesz használható, de továbbra is díjköteles.

Az indítási feladat hibáit a legfelső szintű [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) Node tulajdonság [eredmény](/rest/api/batchservice/computenode/get#taskexecutionresult) -és [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) tulajdonságainak használatával észlelheti.

A sikertelen indítási feladat azt is okozhatja, hogy a Batch beállítsa a csomópont [állapotát](/rest/api/batchservice/computenode/get#computenodestate) , hogy **starttaskfailed** , ha a **waitForSuccess** értéke **true (igaz**).

A feladatok elvégzéséhez hasonlóan számos oka lehet az indítási feladat végrehajtása.  A hibák megoldásához keresse meg az stdout, a stderr, valamint a feladatra vonatkozó további naplófájlokat.

Az indítási tevékenységeket újra kell adni, mivel lehetséges, hogy az indítási tevékenység többször is fut ugyanazon a csomóponton; az indítási tevékenység akkor fut le, amikor a csomópontot rendszerképbe állítja vagy újraindította. Ritka esetekben egy indítási tevékenység akkor fut le, ha egy esemény egy csomópont újraindítását okozta, ahol a másik operációs rendszer vagy ideiglenes lemez újra lett indítva, míg a másik nem volt. Mivel a Batch indítási feladatai (például az összes batch-feladat) az ideiglenes lemezről futnak, ez általában nem probléma, de bizonyos esetekben, amikor az indítási tevékenység telepíti az alkalmazást az operációsrendszer-lemezre, és más adatokkal látja el az ideiglenes lemezeket, ez problémákat okozhat, mert a dolgok nincsenek szinkronban. Ha mindkét lemezt használja, az alkalmazás védelme ennek megfelelően történik.

### <a name="application-package-download-failure"></a>Alkalmazáscsomag letöltése sikertelen

Egy készlethez egy vagy több alkalmazáscsomag is megadható. A Batch letölti a megadott csomagokat az egyes csomópontokra, és kibontja a fájlokat a csomópont elindítása után, de a feladatok ütemezése előtt. Gyakori, hogy az alkalmazás csomagjaival együtt a Start Task parancssort használják. Például a fájlok másik helyre való másolásához vagy a telepítő futtatásához.

A csomópont [hibái](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság az alkalmazáscsomag letöltésére és eltávolítására vonatkozó hibát jelez; a csomópont állapota **használhatatlanra**van állítva.

### <a name="container-download-failure"></a>Tároló letöltése sikertelen

Egy készlethez egy vagy több tároló-hivatkozást is megadhat. A Batch letölti a megadott tárolókat az egyes csomópontokra. A Node [errors](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság azt jelzi, hogy a tároló letöltése nem sikerült, és a csomópont állapotát nem **használható**értékre állítja.

### <a name="node-in-unusable-state"></a>A csomópont használhatatlan állapotban van

Azure Batch lehet, hogy a [csomópont állapota](/rest/api/batchservice/computenode/get#computenodestate) számos okból **használhatatlanná válik** . Ha a csomópont állapota **használhatatlan**értékre van állítva, a feladatok nem ütemezhetők a csomópontra, de a szolgáltatás továbbra is díjköteles.

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

A készlethez tartozó virtuális gép ideiglenes meghajtóját a Batch a feladatok fájljai, a feladatok fájljai és a megosztott fájlok számára használja.

- Alkalmazás-csomagok fájljai
- Feladat-erőforrás fájljai
- A Batch-mappák egyikére letöltött alkalmazás-specifikus fájlok
- Stdout-és stderr-fájlok az egyes feladatok alkalmazás-végrehajtásához
- Alkalmazásspecifikus kimeneti fájlok

A fájlok némelyike csak egyszer írható elő, amikor a készlet csomópontjai jönnek létre, például a készlet alkalmazáscsomag vagy a készlet indítási tevékenységének erőforrás-fájljai. Akkor is, ha a csomópont létrehozásakor csak egyszer íródik, ha ezek a fájlok túl nagyok, az ideiglenes meghajtót is kitölthetik.

A rendszer minden olyan feladatra kiírja a többi fájlt, amely egy csomóponton fut, például az stdout és a stderr. Ha nagy számú tevékenység fut ugyanazon a csomóponton, és/vagy a feladatok fájljai túl nagyok, akkor kitölthetik az ideiglenes meghajtót.

Az ideiglenes meghajtó mérete a virtuális gép méretétől függ. A virtuális gép méretének kiválasztásakor az egyik szempont, hogy az ideiglenes meghajtó elegendő lemezterülettel rendelkezzen.

- A készlet hozzáadásakor a Azure Portal a virtuálisgép-méretek teljes listáját jelenítheti meg, és az erőforrás lemez mérete oszlop is megjelenik.
- Az összes virtuálisgép-méretet ismertető cikk a "Temp Storage" oszlopot tartalmazó táblázatokat tartalmaz. például a [számítási optimalizált VM-méretek](/azure/virtual-machines/windows/sizes-compute)

Az egyes feladatok által írt fájlok esetében megadható az egyes feladatokhoz tartozó megőrzési idő, amely meghatározza, hogy a rendszer mennyi ideig tárolja a feladatokat, mielőtt automatikusan kitakarítja a fájlokat. A megőrzési idő csökkentheti a tárolási követelmények csökkentését.


Ha az ideiglenes lemez kifogy (vagy nagyon közel van a helyhez), a csomópont [használhatatlan](/rest/api/batchservice/computenode/get#computenodestate) állapotba kerül, és a rendszer azt jelzi, hogy a lemez megtelt.

### <a name="what-to-do-when-a-disk-is-full"></a>Mi a teendő, ha egy lemez megtelt

Annak meghatározása, hogy a lemez miért megtelt: Ha nem biztos benne, hogy mi a terület a csomóponton, akkor azt javasoljuk, hogy távolról a csomópontra kerüljön, és vizsgálja meg, hogy a terület Hová lett-e mentve. A [Batch Files API](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) -t is használhatja a Batch által felügyelt mappákban található fájlok vizsgálatához (például feladat kimenetei). Vegye figyelembe, hogy ez az API csak a Batch által felügyelt címtárakban található fájlokat sorolja fel, és ha a feladatok máshol nem láthatók, akkor azok nem jelennek meg.

Győződjön meg arról, hogy minden szükséges adat le lett kérve a csomópontból, vagy feltöltve lett egy tartós tárolóba. A lemez teljes hibájának enyhítése magában foglalja az adattárolás lemezterület felszabadításához szükséges adattörlést.

### <a name="recovering-the-node"></a>A csomópont helyreállítása

1. Ha a készlet egy [C. loudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) -készlet, akkor a csomópontot a [Batch relemezkép API](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage)használatával újra elvégezheti. Ezzel megtisztítja a teljes lemezt. A [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) -készletek jelenleg nem támogatják az újralemezképet.

2. Ha a készlet [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration), a csomópontok [eltávolítása API](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes)használatával eltávolíthatja a csomópontot a készletből. Ezután ismét növelheti a készletet, hogy a rossz csomópontot egy frissre cserélje.

3.  Törölheti a régi befejezett feladatokat vagy a régi befejezett feladatokat, amelyek tevékenységi adatai még mindig a csomópontokon vannak. Arra vonatkozóan, hogy milyen feladatok/feladatok adatai vannak azon csomópontokon, amelyeket a csomóponton található [RecentTasks-gyűjteményben](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) vagy a [csomóponton lévő fájlokban](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode)kereshet. A feladat törlésével törli a feladat összes feladatát, és törli a feladat feladatait, hogy a csomóponton lévő feladatok könyvtáraiban található adatok törlődnek, így felszabadítja a helyet. Ha elegendő lemezterületet szabadít fel, indítsa újra a csomópontot, és a "használhatatlan" állapotból és a "tétlen" állapotúra kell lépnie.

## <a name="next-steps"></a>További lépések

Győződjön meg arról, hogy az alkalmazás teljes körű hibaellenőrzés megvalósítására van beállítva, különösen aszinkron műveletekhez. Kritikus fontosságú lehet a problémák azonnali észlelése és diagnosztizálása.

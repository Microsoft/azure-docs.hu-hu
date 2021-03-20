---
title: Számítási feladatok futtatása költséghatékony, alacsony prioritású virtuális gépeken
description: Ismerje meg, hogy miként lehet alacsony prioritású virtuális gépeket kiépíteni Azure Batch munkaterhelések díjainak csökkentése érdekében.
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: cafc7216e8112640f823ecee1aea055ab78b3fc6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098468"
---
# <a name="use-low-priority-vms-with-batch"></a>Alacsony prioritású virtuális gépek használata a Batch szolgáltatással

A Azure Batch alacsony prioritású virtuális gépeket (VM) biztosít a Batch-munkaterhelések díjainak csökkentése érdekében. Az alacsony prioritású virtuális gépek új típusú batch-munkaterheléseket tesznek lehetővé azáltal, hogy nagy mennyiségű számítási teljesítményre van szükség, amely nagyon alacsony költségeket jelent.

Az alacsony prioritású virtuális gépek kihasználhatják a felesleges kapacitást az Azure-ban. Ha alacsony prioritású virtuális gépeket ad meg a készletekben, Azure Batch használhatja ezt a többletet, ha elérhető.

Az alacsony prioritású virtuális gépek használatának kompromisszuma az, hogy ezek a virtuális gépek nem mindig lesznek elérhetők a lefoglaláshoz, vagy a rendelkezésre álló kapacitástól függően bármikor előzik. Emiatt az alacsony prioritású virtuális gépek leginkább alkalmasak a Batch-és aszinkron feldolgozási munkaterhelések esetében, ahol a feladat befejezési ideje rugalmas, és a munka több virtuális gépen van elosztva.

Az alacsony prioritású virtuális gépeket a dedikált virtuális gépekhez képest jelentősen csökkentett áron kínáljuk. A díjszabás részleteiért lásd: [Batch-díjszabás](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> Az [egypéldányos virtuális gépekhez](../virtual-machines/spot-vms.md) és a virtuálisgép- [méretezési csoportokhoz](../virtual-machine-scale-sets/use-spot.md)mostantól elérhetők a [helyszíni virtuális gépek](https://azure.microsoft.com/pricing/spot/) . A helyszíni virtuális gépek az alacsony prioritású virtuális gépek fejlődése, azonban a díjszabásban különböznek, és a helyszíni virtuális gépek kiosztásakor a választható maximális árat is megadhatja.
>
>Azure Batch készletek a jövőben megkezdik a helyszíni virtuális gépek támogatását a [Batch API-k és eszközök](./batch-apis-tools.md)új verzióival. A helyszíni virtuálisgép-támogatás elérhetővé tétele után az alacsony prioritású virtuális gépek elavultak lesznek – a jelenlegi API-k és az eszközök verziószáma legalább 12 hónapig érvényes lesz, hogy elegendő idő álljon rendelkezésre a helyszíni virtuális gépekre való áttelepítéshez.
>
> A helyszíni virtuális gépek csak a virtuálisgép-konfigurációs készletek esetén lesznek támogatottak. A helyszíni virtuális gépek használatához a Felhőbeli szolgáltatások konfigurációs készleteit át kell [telepíteni a virtuálisgép-konfigurációs készletekbe](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="batch-support-for-low-priority-vms"></a>Batch-támogatás alacsony prioritású virtuális gépekhez

A Azure Batch számos olyan képességet biztosít, amely megkönnyíti az alacsony prioritású virtuális gépek használatát és előnyeit:

- A Batch-készletek a dedikált virtuális gépeket és az alacsony prioritású virtuális gépeket is tartalmazhatják. Az egyes virtuálisgép-típusok száma megadható a készlet létrehozásakor, illetve egy meglévő készlet esetében, az explicit átméretezési művelet vagy az automatikus méretezés használatával. A feladat és a feladat elküldése változatlan marad, a készletben lévő virtuálisgép-típusoktól függetlenül. A készletet úgy is konfigurálhatja, hogy az alacsony prioritású virtuális gépeket a lehető legolcsóbban fusson, de a dedikált virtuális gépeket akkor is elindítsa, ha a kapacitás a minimális küszöbérték alá esik, így a feladatok folyamatosan futnak.
- A Batch-készletek automatikusan megkeresik az alacsony prioritású virtuális gépek céljának számát. Ha a virtuális gépek előzik vagy nem érhetők el, a Batch megkísérli lecserélni az elveszett kapacitást, és visszatérni a célhoz.
- A feladatok megszakításakor a Batch észleli és automatikusan újravárólistába helyezi a feladatokat.
- Az alacsony prioritású virtuális gépek különálló vCPU-kvótával rendelkeznek, amely eltér a dedikált virtuális gépektől. Az alacsony prioritású virtuális gépekre vonatkozó kvóta magasabb a dedikált virtuális gépekre vonatkozó kvótánál, mert az alacsony prioritású virtuális gépek olcsóbbak. További információ: [Batch szolgáltatás kvótái és korlátai](batch-quota-limit.md#resource-quotas).

> [!NOTE]
> Az alacsony prioritású virtuális gépek jelenleg nem támogatottak a [felhasználói előfizetési módban](accounts.md)létrehozott batch-fiókok esetében.

## <a name="considerations-and-use-cases"></a>Szempontok és használati esetek

Az alacsony prioritású virtuális gépek sok batch-munkaterheléssel rendelkeznek. Érdemes lehet azokat használni, amikor a feladatok sok párhuzamos feladatra vannak bontva, vagy ha sok olyan feladattal rendelkezik, amely több virtuális gépre kiterjedő és elosztott.

Néhány példa a Batch-feldolgozási használati esetekre, amelyek alacsony prioritású virtuális gépek használatára alkalmasak:

- **Fejlesztés és tesztelés**: különösen nagy léptékű megoldások fejlesztése esetén jelentős megtakarítás valósítható meg. Az összes típusú tesztelés kihasználható, de a nagy léptékű terheléses tesztelés és a regressziós tesztelés nagyszerűen használható.
- **Az igény szerinti kapacitás kiegészítése**: az alacsony prioritású virtuális gépeket a normál dedikált virtuális gépek kiegészítéseként lehet használni. Ha elérhető, a feladatok méretezhetők, így az alacsonyabb költségeket is meggyorsíthatja; Ha nem érhető el, a dedikált virtuális gépek alapterve továbbra is elérhető marad.
- **Rugalmas feladatok végrehajtásának ideje**: Ha az időfeladatok esetében rugalmasságra van lehetőség, akkor a kapacitás potenciális elhagyása tolerálható; Ugyanakkor az alacsony prioritású virtuális gépekkel kapcsolatos feladatok gyakran gyorsabban futnak, és alacsonyabb költségeket jelentenek.

A Batch-készleteket konfigurálhatja úgy, hogy az alacsony prioritású virtuális gépeket több módon lehessen használni:

- Egy készlet csak alacsony prioritású virtuális gépeket használhat. Ebben az esetben a Batch minden előzik kapacitást helyreállít, ha elérhető. Ez a konfiguráció a feladatok végrehajtásának legolcsóbb módja.
- Az alacsony prioritású virtuális gépeket a dedikált virtuális gépek rögzített alaptervével együtt lehet használni. A dedikált virtuális gépek rögzített száma biztosítja, hogy mindig legyen elegendő kapacitás a feladatok végrehajtásához.
- A készletek a dedikált és alacsony prioritású virtuális gépek dinamikus kombinációját használhatják, így az alacsony prioritású virtuális gépeket csak akkor használják, ha elérhetők, de a teljes árú dedikált virtuális gépek igény szerint méretezhetők. Ez a konfiguráció a feladatok előrehaladásának megtartása érdekében a lehető legkevesebb kapacitást tartja elérhetővé.

Az alacsony prioritású virtuális gépek használatának megtervezésekor vegye figyelembe a következőket:

- Az Azure-ban a felesleges kapacitások maximalizálása érdekében a megfelelő feladatok felskálázásra képesek.
- Előfordulhat, hogy esetenként a virtuális gépek nem állnak rendelkezésre vagy előzik, ami csökkenti a feladatok mennyiségét, és a feladatok megszakítását és ismétlését eredményezheti.
- A rövidebb végrehajtási idővel rendelkező feladatok általában alacsony prioritású virtuális gépekkel működnek a legjobban. A hosszabb feladatokkal rendelkező feladatok továbbra is befolyásolhatják a megszakítást. Ha a hosszan futó feladatok végrehajtják az ellenőrzőpontokat a végrehajtásuk folyamatának elvégzéséhez, ez a hatás csökkenhet. 
- A több virtuális gépet használó, hosszú ideig futó MPI-feladatok nem alkalmasak az alacsony prioritású virtuális gépek használatára, mivel az egyik előzik virtuális gép a teljes feladat futtatásához vezethet.
- Az alacsony prioritású csomópontok nem használhatók, ha a [hálózati biztonsági csoport (NSG) szabályai](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) helytelenül vannak konfigurálva.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Alacsony prioritású virtuális gépekkel rendelkező készletek létrehozása és kezelése

A Batch-készletek A dedikált és alacsony prioritású virtuális gépeket (más néven számítási csomópontokat) is tartalmazhatják. Megadhatja a számítási csomópontok megcélzott számát a dedikált és alacsony prioritású virtuális gépek esetében is. A csomópontok célként megadott száma határozza meg a készletben használni kívánt virtuális gépek számát.

Ha például egy készletet szeretne létrehozni az Azure Virtual Machines használatával (ebben az esetben a linuxos virtuális gépeken), amelynek célja 5 dedikált virtuális gép és 20 alacsony prioritású virtuális gép:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

A dedikált és alacsony prioritású virtuális gépekhez tartozó csomópontok aktuális számát lekérheti:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

A készlet csomópontjai rendelkeznek egy tulajdonsággal, amely jelzi, hogy a csomópont dedikált vagy alacsony prioritású virtuális gép-e:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Esetenként előfordulhat, hogy a virtuális gépek előzik. Ha ez történik, a előzik csomóponton futó virtuális gépeken futó feladatok újravárólistára kerülnek, és újra futnak.

A virtuális gépek konfigurációs készletei esetében a Batch a következő műveleteket is elvégzi:

- A előzik virtuális gépek állapota frissítve a **előzik**. 
- A virtuális gép gyakorlatilag törölve lett, ami a virtuális gépen helyileg tárolt összes adat elvesztését eredményezi.
- A készletben lévő lista csomópontjainak művelete továbbra is a előzik-csomópontokat fogja visszaadni.
- A készlet folyamatosan megkísérli elérni a rendelkezésre álló alacsony prioritású csomópontok számát. Ha a rendszer kicseréli a kapacitást, a csomópontok megőrzik az azonosítókat, de újrainicializálják őket, és az állapotok **létrehozása** és **elindítása** előtt a feladatütemezés számára elérhetővé válnak.
- A megelőlegezése a Azure Portal metrikaként érhetők el.

## <a name="scale-pools-containing-low-priority-vms"></a>Alacsony prioritású virtuális gépeket tartalmazó készletek méretezése

Csakúgy, mint a kizárólag dedikált virtuális gépekből álló készletek esetében, az átméretezési módszer meghívásával vagy az automatikus skálázás használatával lehetséges az alacsony prioritású virtuális gépeket tartalmazó készlet méretezése.

A készlet átméretezési művelete egy második opcionális paramétert is végrehajt, amely frissíti a **targetLowPriorityNodes** értékét:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A készlet autoskálázási képlete az alacsony prioritású virtuális gépeket az alábbiak szerint támogatja:

- Lekérheti vagy beállíthatja a szolgáltatás által definiált változó értékét **$TargetLowPriorityNodes**.
- Lekérheti a szolgáltatás által definiált változó értékét **$CurrentLowPriorityNodes**.
- Lekérheti a szolgáltatás által definiált változó értékét **$PreemptedNodeCount**. Ez a változó visszaadja a csomópontok számát a előzik állapotban, és lehetővé teszi a dedikált csomópontok számának vertikális fel-vagy leskálázását a nem elérhető előzik-csomópontok számától függően.

## <a name="configure-jobs-and-tasks"></a>Feladatok és feladatok konfigurálása

A feladatokhoz és a feladatokhoz kevés további konfiguráció szükséges az alacsony prioritású csomópontok számára. Fontos szem előtt tartani a következőket:

- A feladatok JobManagerTask tulajdonsága **AllowLowPriorityNode** tulajdonsággal rendelkezik. Ha a tulajdonság értéke igaz, a Feladatkezelő feladat ütemezhető egy dedikált vagy alacsony prioritású csomóponton is. Ha hamis, a Feladatkezelő feladat csak dedikált csomópontra van ütemezve.
- A AZ_BATCH_NODE_IS_DEDICATED [környezeti változó](batch-compute-node-environment-variables.md) elérhető egy feladathoz, hogy képes legyen megállapítani, hogy alacsony prioritású vagy dedikált csomóponton fut-e.

## <a name="view-metrics-for-low-priority-vms"></a>Alacsony prioritású virtuális gépek metrikáinak megtekintése

Az alacsony prioritású csomópontok [Azure Portal](https://portal.azure.com) az új metrikák érhetők el. Ezek a metrikák a következők:

- Low-Priority csomópontok száma
- Low-Priority mag száma
- Előzik-csomópontok száma

A metrikák megtekintése a Azure Portal

1. Az Azure portálon lépjen Batch-fiókjára.
2. A **figyelés** szakaszban válassza a **metrikák** lehetőséget.
3. Válassza ki a kívánt mérőszámokat a **metrika** listából.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
- Először tervezze meg az alacsony prioritású virtuális gépekről a virtuális gépekre való áttérést. Ha alacsony prioritású virtuális gépeket használ a **Cloud Service-konfigurációs** készletekkel, tervezze meg a [ **virtuális gépek konfigurációs** készletére](nodes-and-pools.md#configurations) való áttelepítést.

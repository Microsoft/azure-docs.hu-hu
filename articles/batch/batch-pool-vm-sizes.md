---
title: Válassza ki a virtuális gépek méretét és lemezképeit a készletekhez
description: Az elérhető virtuálisgép-méretek és operációsrendszer-verziók kiválasztása Azure Batch-készletekben lévő számítási csomópontokhoz
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 2c3b90d6188dc6660233ae659fb4280dc1d4f2a5
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027380"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Virtuális gép méretének és rendszerképének kiválasztása Azure Batch készlet számítási csomópontjaihoz

Ha kijelöl egy Azure Batch készlethez tartozó csomópont-méretet, az Azure-ban elérhető szinte minden virtuálisgép-méret közül választhat. Az Azure különféle számítási feladatokhoz biztosít különböző méretű Linux és Windows rendszerű virtuális gépeket.

## <a name="supported-vm-series-and-sizes"></a>Támogatott VM-sorozatok és-méretek

### <a name="pools-in-virtual-machine-configuration"></a>Készletek a virtuális gép konfigurációjában

A virtuális gép konfigurációjában a Batch-készletek szinte minden virtuálisgép- [méretet](../virtual-machines/sizes.md)támogatnak. A támogatott méretekkel és korlátozásokkal kapcsolatos további információkért tekintse meg a következő táblázatot.

| Virtuálisgép-sorozatok  | Támogatott méretek |
|------------|---------|
| Alapszintű A | Minden méret, *kivéve* a Basic_A0 (a0) |
| A | Minden méret Standard_A0, Standard_A8, Standard_A9, Standard_A10, *kivéve* Standard_A11 |
| Av2 | Minden méret |
| B | Nem támogatott |
| DCsv2 | Minden méret |
| Dv2, DSv2 | Minden méret |
| Dv3, Dsv3 | Minden méret |
| Dav4, Dasv4 | Minden méret |
| Ddv4, Ddsv4 |  Minden méret |
| DV4, Dsv4 | Nem támogatott |
| Ev3, Esv3 | Minden méret, kivéve a E64is_v3 |
| Eav4, Easv4 | Minden méret |
| Edv4, Edsv4 |  Minden méret |
| Ev4, Esv4 | Nem támogatott |
| F, FS | Minden méret |
| Fsv2 | Minden méret |
| G, GS | Minden méret |
| H | Minden méret |
| HB | Minden méret |
| HBv2 | Minden méret |
| HBv3 | Standard_HB120rs_v3 (a többi méret még nem érhető el) |
| HIBRID kapcsolat | Minden méret |
| Ls | Minden méret |
| Lsv2 | Minden méret |
| M | Minden méret |
| <sup>1</sup> . Mv2 | Minden méret |
| NC | Minden méret |
| NCv2 | Minden méret |
| NCv3 | Minden méret |
| NCasT4_v3 | Minden méret |
| ND | Minden méret |
| NDv2 | Nincs – még nem érhető el |
| NV | Minden méret |
| NVv3 | Minden méret |
| NVv4 | Minden méret |
| SAP HANA | Nem támogatott |

<sup>1</sup> ezek a virtuálisgép-sorozatok csak a 2. generációs virtuálisgép-rendszerképekkel használhatók.

### <a name="using-generation-2-vm-images"></a>A 2. generációs VM-rendszerképek használata

Egyes virtuálisgép-sorozatok (például a [Mv2](../virtual-machines/mv2-series.md)) csak 2. [generációs virtuálisgép-rendszerképekkel](../virtual-machines/generation-2.md)használhatók. A 2. generációs virtuálisgép-lemezképek a ["imageReference"](/rest/api/batchservice/pool/add#imagereference) konfigurációjának "SKU" tulajdonságával vannak megadva, mint bármely virtuálisgép-rendszerkép. az "SKU" karakterlánchoz utótag tartozik, például "-G2" vagy "-Gen2". A Batch által támogatott virtuálisgép-rendszerképek listájának lekéréséhez, beleértve a 2. generációs képeket, használja a ["támogatott lemezképek listázása"](/rest/api/batchservice/account/listsupportedimages) API-t, a [PowerShellt](/powershell/module/az.batch/get-azbatchsupportedimage)vagy az [Azure CLI](/cli/azure/batch/pool/supported-images)-t.

### <a name="pools-in-cloud-service-configuration"></a>Készletek a Cloud Service-konfigurációban

A Cloud Service-konfigurációban a Batch-készletek támogatják a Cloud Services összes virtuálisgép- [méretét](../cloud-services/cloud-services-sizes-specs.md) , a következők **kivételével** :

| Virtuálisgép-sorozatok  | Nem támogatott méretek |
|------------|-------------------|
| A-sorozat   | Extra kicsi       |
| Av2-sorozat | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Mérettel kapcsolatos szempontok

- **Alkalmazásokra vonatkozó követelmények** – vegye figyelembe a csomópontokon futtatandó alkalmazás jellemzőit és követelményeit. Az olyan szempontok, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthetnek meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. A többpéldányos [MPI-munkaterhelések](batch-mpi.md) vagy a CUDA-alkalmazások esetében érdemes lehet speciális [HPC](../virtual-machines/sizes-hpc.md) [-vagy GPU-kompatibilis virtuálisgép-](../virtual-machines/sizes-gpu.md) méreteket használni. További információ: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](batch-pool-compute-intensive-sizes.md).

- **Feladatok/csomópont** – tipikusan a csomópontok méretének kiválasztását feltételezve, hogy egyszerre egy tevékenység fut egy csomóponton. Azonban érdemes lehet több feladatot (és így több alkalmazás-példányt) [párhuzamosan futtatni](batch-parallel-node-tasks.md) a számítási csomópontokon a feladat végrehajtása során. Ebben az esetben gyakori, hogy egy többprocesszoros csomópontot válasszon a párhuzamos feladat-végrehajtás megnövekedett igényének kielégítéséhez.

- **Különböző feladatok betöltési szintjei** – a készlet összes csomópontja azonos méretű. Ha eltérő rendszerigényű és/vagy terhelési szintű alkalmazásokat szándékozik futtatni, javasoljuk, hogy használjon különálló készleteket.

- **Régió rendelkezésre állása** – előfordulhat, hogy a virtuálisgép-sorozat vagy-méret nem érhető el azokban a régiókban, ahol létrehozza a Batch-fiókokat. Ha szeretné megtekinteni, hogy elérhető-e a méret, tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/regions/services/).

- **Kvóták** – a Batch-fiókban lévő [magok kvótái](batch-quota-limit.md#resource-quotas) korlátozhatják a Batch-készletbe felvehető adott méretű csomópontok számát. Ha szükséges, [a kvóta növelését is kérheti](batch-quota-limit.md#increase-a-quota).

- **Készlet konfigurációja** – általánosságban több virtuálisgép-méretezési lehetőség is rendelkezésre áll, ha a virtuális gép konfigurációjában hoz létre készletet, a Cloud Service-konfigurációhoz képest.

## <a name="supported-vm-images"></a>Támogatott VM-rendszerképek

A következő API-k egyikével visszaállíthatja a Batch által jelenleg támogatott Windows-és Linux-alapú virtuálisgép-rendszerképek listáját, beleértve az egyes lemezképek csomópont-ügynök SKU-azonosítóit:

- Batch szolgáltatás REST API: [támogatott lemezképek listázása](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [az batch Pool által támogatott – lemezképek](/cli/azure/batch/pool/supported-images)

Javasoljuk, hogy kerülje a képeket a közelgő batch-támogatás (EOL) befejezési dátumaival. Ezeket a dátumokat az [ `ListSupportedImages` API](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages), a [POWERSHELL](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/batch/pool/supported-images)segítségével derítheti fel. A Batch-készlet virtuálisgép-rendszerképének kiválasztásával kapcsolatos további információkért tekintse meg a [Batch ajánlott eljárásait ismertető útmutatót](best-practices.md) .

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- További információ a számítási igényű virtuálisgép-méretek használatáról: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata batch-készletekben](batch-pool-compute-intensive-sizes.md).

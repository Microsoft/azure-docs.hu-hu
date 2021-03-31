---
title: A virtuális gépek mérete
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző méreteket.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: d9377ba22f1461762e53b1004dfe5f06c2d7b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89420217"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Virtuális gépek méretei az Azure-ban

Ez a cikk az alkalmazások és munkaterhelések futtatásához használható Azure-beli virtuális gépekhez elérhető méreteket és lehetőségeket ismerteti. Emellett üzembe helyezési megfontolásokat is biztosít, hogy a rendszer mikor tervezi ezeket az erőforrásokat. 

| Típus | Méretek | Leírás |
|------|-------|-------------|
| [Általános célú](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, DV4, Dsv4, Ddv4, Ddsv4  | Kiegyensúlyozott processzor-memória arány. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. |
| [Számításoptimalizált](sizes-compute.md) | F, FS, Fsv2 | Magas processzor-memória arány. Jó a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazáskiszolgáló számára. |
| [Memóriaoptimalizált](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Magas memória-processzor arány. Ideális választások a relációs adatbázisok kiszolgálóihoz, a közepes és nagy gyorsítótárakhoz, valamint a memóriában végzett elemzésekhez.                 |
| [Tároptimalizált](sizes-storage.md) | Lsv2 | A nagy adatátviteli sebesség és az i/o ideális megoldás a Big Database, az SQL, a NoSQL adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok számára.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3 (előzetes verzió), ND, NDv2 (előzetes verzió), NV, NVv3, NVv4 | A speciális virtuális gépek nagy grafikai renderelést és videoszerkesztőt céloznak meg, valamint a képzési és következtetési (ND) modellek mély tanulással. Elérhető egy vagy több GPU-val. |
| [Nagy teljesítményű számítás](sizes-hpc.md) | HB, HBv2, HC, H | A leggyorsabb és leghatékonyabb CPU-alapú virtuális gépek, amelyek opcionális nagy átviteli sebességű hálózati adapterrel (RDMA) rendelkeznek. |

- A különböző méretek díjszabásával kapcsolatos információkért tekintse meg a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) vagy a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)díjszabási oldalait.
- Az Azure-régiókban található virtuálisgép-méretek elérhetőségét lásd: [régiók által elérhető termékek](https://azure.microsoft.com/regions/services/).
- Az Azure-beli virtuális gépekre vonatkozó általános korlátokat lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
- További információ arról, hogy az Azure Hogyan nevezi el a virtuális gépeket: az [Azure virtuálisgép-méretek elnevezési konvenciói](./vm-naming-conventions.md).

## <a name="rest-api"></a>REST API

A virtuális gépek méretének lekérdezéséhez REST API használatáról a következő témakörben tájékozódhat:

- [Az átméretezéshez rendelkezésre álló virtuálisgép-méretek listázása](/rest/api/compute/virtualmachines/listavailablesizes)
- [Az előfizetéshez elérhető virtuálisgép-méretek listázása](/rest/api/compute/resourceskus/list)
- [Rendelkezésre álló virtuálisgép-méretek listázása rendelkezésre állási csoportokban](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

További információ a Linux rendszerű virtuális gépek számítási teljesítményéről a [Megjegyzések összehasonlítási pontjainak](./linux/compute-benchmark-scores.md)használatával.

További információ a Windows rendszerű virtuális gépek számítási teljesítményéről a [SPECInt teljesítményteszt-pontszámok](./windows/compute-benchmark-scores.md)használatával.

## <a name="manage-costs"></a>Költségek kezelése

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Következő lépések

További információ a rendelkezésre álló különböző virtuálisgép-méretekről:

- [Általános célú](sizes-general.md)
- [Számításoptimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- A standard, a Dv1 (D1-4 és D11-14 v1), valamint az A8-A11 sorozat [előző generációs](sizes-previous-gen.md) oldalának keresése

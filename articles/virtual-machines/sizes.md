---
title: A virtuális gépek mérete
description: Az Azure-beli virtuális gépekhez elérhető különböző méreteket sorolja fel.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: c3eecb0b9628ae889ac22bfe6d621266f06abc83
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598924"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek méretei

Ez a cikk az alkalmazások és számítási feladatok futtatásához használható Azure-beli virtuális gépek elérhető méretét és beállításait ismerteti. Emellett az üzembe helyezéssel kapcsolatos szempontokat is figyelembe kell vennie, amikor ezeket az erőforrásokat tervezi használni. 

| Típus | Méretek | Leírás |
|------|-------|-------------|
| [Általános célú](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Kiegyensúlyozott processzor-memória arány. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. |
| [Számításoptimalizált](sizes-compute.md) | F, Fs, Fsv2 | Magas processzor-memória arány. Megfelelő a közepes forgalmú webkiszolgálókhoz, hálózati berendezésekhez, kötegelt folyamatokhoz és alkalmazáskiszolgálókhoz. |
| [Memóriaoptimalizált](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Magas memória-processzor arány. Ideális választások a relációs adatbázisok kiszolgálóihoz, a közepes és nagy gyorsítótárakhoz, valamint a memóriában végzett elemzésekhez.                 |
| [Tároptimalizált](sizes-storage.md) | Lsv2 | A nagy lemez-átviteli sebesség és az IO ideális big data-, SQL-, NoSQL-, adatraktározási és nagy tranzakciós adatbázisokhoz.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3, ND, NDv2, NV, NVv3, NVv4 | Specializált virtuális gépek nagy terhelést képző grafikus rendereléshez és videószerkesztéshez, valamint modellek betanítása és következtetés (ND) mély tanulással. Elérhető egy vagy több GPU-val. |
| [Nagy teljesítményű számítás](sizes-hpc.md) | HB, HBv2, HC, H | A leggyorsabb és leghatékonyabb PROCESSZORral futó virtuális gépeink választható nagy átviteli sebességű hálózati adapterekkel (RDMA). |

- A különböző méretek díjszabására vonatkozó információkért tekintse meg a Linux vagy [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) díjszabási [lapját.](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)
- A virtuálisgép-méretek Azure-régiókban való elérhetőségét lásd: [Régiónként elérhető termékek.](https://azure.microsoft.com/regions/services/)
- Az Azure-beli virtuális gépekre vonatkozó általános korlátokat lásd: Azure-előfizetések és -szolgáltatások [korlátai, kvótái és megkötései.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- További információ arról, hogyan nevezi el az Azure a virtuális gépeket: Azure-beli virtuális gépek [méretei – elnevezési konvenciók.](./vm-naming-conventions.md)

## <a name="rest-api"></a>REST API

A virtuális gépek méretének lekérdezésére REST API információkért tekintse meg a következőt:

- [Az átméretezéshez elérhető virtuálisgép-méretek felsorolása](/rest/api/compute/virtualmachines/listavailablesizes)
- [Az előfizetéshez elérhető virtuálisgép-méretek felsorolása](/rest/api/compute/resourceskus/list)
- [Rendelkezésre állási készletben elérhető virtuálisgép-méretek felsorolása](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

További információ arról, hogyan hasonlíthatja össze az Azure-beli SKU-k számítási teljesítményét az Azure Számítási egységek [(ACU)](acu.md) segítségével.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

További információ a Linux rendszerű virtuális gépek számítási teljesítményéről a [CoreMark teljesítményteszt pontszámai alapján.](./linux/compute-benchmark-scores.md)

További információ a Windows rendszerű virtuális gépek számítási teljesítményéről a [SPECInt teljesítményteszt pontszámai alapján.](./windows/compute-benchmark-scores.md)

## <a name="manage-costs"></a>Költségek kezelése

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Következő lépések

További információ a különböző elérhető virtuálisgép-méretekről:

- [Általános célú](sizes-general.md)
- [Számításoptimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- Az A [Standard,](sizes-previous-gen.md) Dv1 (D1-4 és D11-14 v1) és A8-A11 sorozat előző generációját

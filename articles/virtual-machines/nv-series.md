---
title: NV-sorozat – Azure Virtual Machines
description: Az NV-sorozatú virtuális gépek specifikációi.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: e7fdc232d698e3eb2038bb6ec7a4c47a9c49944a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84726583"
---
# <a name="nv-series"></a>NV sorozat

Az NV-sorozatú virtuális gépeket az [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és az NVIDIA Grid Technology működteti az asztali gyorsított alkalmazások és virtuális asztalok számára, amelyekben az ügyfelek képesek megjeleníteni az adatvagy szimulációkat. A felhasználók képesek megjeleníteni az NV-példányokon elérhető grafikai igényű munkafolyamatokat, így kiváló grafikai képességeket szerezhetnek, és emellett egyetlen pontosságú számítási feladatot is futtathatnak, például a kódolást és a renderelést. Az NV sorozatú virtuális gépeket az Intel Xeon E5-2690 v3 (Haswell) processzorok is működtetik.

Az NV-példányokban minden GPU egy RÁCSos licenccel rendelkezik. Ez a licenc lehetővé teszi, hogy az NV-példányokat virtuális munkaállomásként használja egyetlen felhasználó számára, vagy 25 egyidejű felhasználó csatlakozhat a virtuális GÉPHEZ egy virtuális alkalmazási forgatókönyv esetén.

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Élő áttelepítés: nem támogatott

Memória-megőrzési frissítések: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = fél M60 kártya.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat.

Az [NVIDIA GPU illesztőprogram-bővítmény](./extensions/hpccompute-gpu-windows.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](./extensions/hpccompute-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](./extensions/overview.md).

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, tekintse meg a következőt: [n-sorozat GPU-illesztőprogram beállítása Windows](./windows/n-series-driver-setup.md) vagy [N sorozatú GPU-illesztőhöz Linux](./linux/n-series-driver-setup.md) rendszeren támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

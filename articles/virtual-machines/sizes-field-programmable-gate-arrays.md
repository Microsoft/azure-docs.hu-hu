---
title: Azure-beli virtuálisgép-méretek a mezőhöz – programozható Gate-tömbök (FPGA)
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző FPGA optimalizált méreteket. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557675"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA optimalizált virtuálisgép-méretek

A FPGA optimalizált virtuálisgép-méretek olyan speciális virtuális gépek, amelyek egy vagy több FPGA érhetők el. Ezek a méretek nagy számítási igényű munkaterhelésekhez készültek. Ez a cikk a FPGA, a vCPU, az adatlemezek és a hálózati adapterek számáról és típusáról nyújt információt. A rendszer a tároló átviteli sebességét és a hálózati sávszélességet is tartalmazza a csoportosítás minden egyes méretéhez.

- Az [NP-sorozat](np-series.md) mérete olyan számítási feladatokhoz van optimalizálva, mint például a gépi tanulási következtetés, a videó átkódolása és az adatbázis-Keresés & Analitika. Az NP sorozat a xilinx U250-gyorsító használatával van ellátja.


## <a name="deployment-considerations"></a>Telepítési szempontok

- Az N sorozatú virtuális gépek elérhetőségét lásd: [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/).

- Az N sorozatú virtuális gépek csak a Resource Manager-alapú üzemi modellben helyezhetők üzembe.

- Ha több N sorozatú virtuális gépet szeretne üzembe helyezni, vegye fontolóra az utólagos elszámolású előfizetést vagy más vásárlási lehetőséget. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- Előfordulhat, hogy az Azure-előfizetésében meg kell emelnie a magok kvótáját (régiónként), és meg kell emelnie az NP magok külön kvótáját. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](../azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításoptimalizált](sizes-compute.md)
- [GPU-gyorsított számítás](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

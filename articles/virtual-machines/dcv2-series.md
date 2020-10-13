---
title: DC sorozat – Azure Virtual Machines
description: A DC sorozatú virtuális gépek specifikációi.
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: fccdaaab121f315f1cb98b2b6ee3bffcb9ac2b3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653009"
---
# <a name="dcsv2-series"></a>DCsv2 sorozat


A DCsv2 sorozat a nyilvános felhőben feldolgozott adatok és kódok titkosságának és integritásának védelmét is lehetővé teszi. Ezeket a gépeket a SGX ENKLÁVÉHOZ technológiával rendelkező Intel XEON E-2288G processzor legújabb generációja támogatja. Az Intel Turbo Boost technológiával ezek a gépek akár 5,0 GHz-re is felmehetnek. A DCsv2 sorozat példányai lehetővé teszik, hogy az ügyfelek biztonságos enklávé-alapú alkalmazásokat hozzanak létre a kódok és az adataik használat közbeni védelméhez.

Példa a használati esetekre: a bizalmas többrésztvevős adatmegosztás, a csalások észlelése, a pénzmosás elleni védelem, a blockchain, a bizalmas használati elemzés, az intelligencia elemzése és a bizalmas gépi tanulás.

[Premium Storage](premium-storage-performance.md): támogatott *<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>

* A Standard_DC8_v2 kivételével

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Hálózati adapterek maximális száma/várt hálózati sávszélesség (MBps) | EPC-memória (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- A DCsv2 sorozatú virtuális gépek [2. generációs virtuális gépek](./linux/generation-2.md#creating-a-generation-2-vm) , és csak a `Gen2` lemezképeket támogatják.
- Jelenleg az [itt](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)felsorolt régiókban érhető el.
- A bizalmas számítási virtuális gépek előző generációja: [DC sorozat](sizes-previous-gen.md#preview-dc-series)
- DCsv2 virtuális gépek létrehozása a [Azure Portal](./linux/quick-create-portal.md) vagy az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) használatával



## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

Árképzési kalkulátor: [árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/)

További információ a lemezek típusairól: [lemezek típusai](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
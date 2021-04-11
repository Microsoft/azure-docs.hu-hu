---
title: Az Azure Cloud Services számára elérhető méretek (bővített támogatás)
description: Az Azure Cloud Services (kiterjesztett támogatás) üzemelő példányok számára elérhető méretek
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 1c1faf14eb101da41679f9f0596e1e750a3c6a51
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166273"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Az Azure Cloud Services számára elérhető méretek (bővített támogatás)

Ez a cikk a Cloud Services (kiterjesztett támogatás) példányok számára elérhető virtuálisgép-méreteket ismerteti.   

| Termékváltozat-család |  ACU/mag | 
|---|---|
| [A5 – 7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 – 190 * |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 – 190 * |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230 – 260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230 – 260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 – 190 * |
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt. A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Méretek konfigurálása Cloud Serviceshoz (bővített támogatás)

A szolgáltatási modell részeként megadhatja egy szerepkör-példány virtuálisgép-méretét a szolgáltatás definíciós fájljában. A szerepkör mérete határozza meg a CPU-magok számát, a memória kapacitását és a helyi fájlrendszer méretét.

Például a webes szerepkörhöz tartozó példány méretének beállítása a következőre `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Meglévő szerepkör méretének módosítása

Egy meglévő szerepkör méretének módosításához módosítsa a virtuálisgép-méretet a szolgáltatás definíciós fájljába (csdef), csomagolja ki újra a Cloud Service-t, majd telepítse újra. 

## <a name="get-a-list-of-available-sizes"></a>Az elérhető méretek listájának beolvasása 

Az elérhető méretek listájának lekéréséhez tekintse meg az [erőforrás-SKU-lista](/rest/api/compute/resourceskus/list) lehetőséget, és alkalmazza a következő szűrőket:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).

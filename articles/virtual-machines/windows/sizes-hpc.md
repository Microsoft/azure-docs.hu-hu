---
title: Az Azure Windows VM-méretek – HPC |} A Microsoft Docs
description: A nagy teljesítményű Windows virtuális gépek az Azure-ban elérhető különböző méretű sorolja fel. Tartalmazza a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség-sorozat méretei száma.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: 5fc5b5a287a421f93d3184ded3e429c5cff8fa3c
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566300"
---
# <a name="high-performance-compute-vm-sizes"></a>Nagy teljesítményű számítási Virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Operációs rendszer** – Windows Server 2016-ban lévő összes a fenti HPC sorozatú virtuális gépek. A Windows Server 2012 R2, Windows Server 2012 is támogatottak a nem az SR-IOV engedélyezett virtuális gépeken (ezért HB és a hibrid kapcsolat kivételével).

* **MPI** – az SR-IOV engedélyezett Virtuálisgép-méretek az Azure-ban (HB, HC) lehetővé teszi a Mellanox OFED használandó MPI szinte bármilyen íz.
Nem SR-IOV engedélyezve van a virtuális gépek, a támogatott MPI-megvalósításokban az a Microsoft hálózati közvetlen (ND) felületen példányok közötti kommunikáció során. Ezért csak a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb verzió és az Intel MPI 5.x verziók támogatottak. Újabb verzió (2017-es, 2018.) az Intel MPI-futtatókörnyezetet könyvtár feltétlenül nem kompatibilis az rdma-t az Azure-illesztőprogramokat.

* **InfiniBandDriverWindows Virtuálisgép-bővítmény** – az RDMA-kompatibilis virtuális gépek esetében adja hozzá az InfiniBand engedélyezéséhez InfiniBandDriverWindows bővítményt. A Windows VM-bővítmény telepítése a Windows hálózati közvetlen illesztőprogramok (virtuális gépeken nem SR-IOV) vagy a Mellanox OFED illesztőprogramjait RDMA-kapcsolattal (virtuális gépeken az SR-IOV).
Az egyes központi telepítések a8-as és a9-es példányok a HpcVmDrivers bővítmény hozzáadása automatikusan. Vegye figyelembe, hogy hamarosan elavulttá válik a HpcVmDrivers Virtuálisgép-bővítmény; Ez nem fog frissülni. A Virtuálisgép-bővítmény hozzáadása egy virtuális Géphez, használható [Azure PowerShell-lel](/powershell/azure/overview) parancsmagok. 

  A következő parancsot a legújabb 1.0-s verzió InfiniBandDriverWindows bővítmény telepítését egy RDMA-kompatibilis nevű meglévő virtuális Géphez a *myVM* nevű erőforráscsoportban üzembe helyezett *myResourceGroup* a a *USA nyugati RÉGIÓJA* régió:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Azt is megteheti a Virtuálisgép-bővítmények hozzáadhatók az Azure Resource Manager-sablonokkal könnyedén üzembe helyezéshez, a következő JSON-elem:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  A következő parancsot a legújabb 1.0-s verzió InfiniBandDriverWindows bővítmény telepítését pedig egy meglévő Virtuálisgép-méretezési csoportban nevű RDMA-kompatibilis virtuális gépek *myVMSS* nevű erőforráscsoportban üzembe helyezett *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  További információkért lásd: [virtuális gépi bővítmények és szolgáltatások](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Telepített virtuális gépek is dolgozhat bővítmények a [klasszikus üzemi modell](classic/manage-extensions.md).

* **RDMA hálózati címteret** – az Azure-ban az RDMA hálózati fenntartja a cím terület 172.16.0.0/16. MPI-alkalmazások egy Azure virtuális hálózaton üzembe helyezett példányokon futnak, győződjön meg róla, hogy a virtuális hálózat címtere nem fedi át az RDMA-hálózatot.


### <a name="cluster-configuration-options"></a>Fürt konfigurációs lehetőségek

Azure Windows HPC virtuális gép, amely az RDMA hálózati használatával kommunikálhatnak fürtöket számos lehetőséget kínál többek között: 

* **Virtuális gépek** – az RDMA-kompatibilis HPC virtuális gépek ugyanazon rendelkezésre állási csoportjának (Ha használja az Azure Resource Manager üzemi modell) üzembe helyezése. Ha a klasszikus üzemi modellt használja, az ugyanazon a felhőszolgáltatáson virtuális gépek üzembe helyezése. 

* **A Virtual machine scale sets** – a virtuálisgép-méretezési csoportot beállítani, győződjön meg arról, hogy korlátozza az üzembe helyezés az elhelyezési csoportból. Például egy Resource Manager-sablon beállítása az `singlePlacementGroup` tulajdonságot `true`. 

* **Virtuális gépek között MPI** – Ha MPI-kommunikáció a virtuális gépek (VM-EK) között szükség esetén győződjön meg arról, hogy a virtuális gépeket egyazon rendelkezésre állási vagy vannak beállítva a virtuális gépet azonos méretezési csoportot.

* **Az Azure CycleCloud** – a HPC-fürt létrehozása [Azure CycleCloud](/azure/cyclecloud/) MPI-feladatok futtatásához a Windows-csomópontokon.

* **Az Azure Batch** – hozzon létre egy [Azure Batch](/azure/batch/) MPI számítási feladatok futtatásához a Windows Server a készlet számítási csomópontjain. További információkért lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készletek](../../batch/batch-pool-compute-intensive-sizes.md). További tájékoztatás a [Batch hajógyárnak](https://github.com/Azure/batch-shipyard) tárolóalapú számítási feladatok futtatásához a Batch-projektben.

* **A Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) MS-MPI RDMA-kompatibilis Windows virtuális gépek üzembe helyezésekor az Azure RDMA-hálózatot használó futtatókörnyezetének tartalmazza. Például a telepítések esetén lásd: [a HPC Pack segítségével Windows RDMA-fürt beállítása MPI-alkalmazások futtatására](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>További méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)
- [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Előző generációs szoftvereknél jobban](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- Ellenőrzőlista a nagy számítási igényű példányok használata a HPC Pack a Windows Server, lásd: [a HPC Pack segítségével Windows RDMA-fürt beállítása MPI-alkalmazások futtatására](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- MPI-alkalmazások futtatása az Azure Batch számításigényes példányok használatához lásd: [többpéldányos tevékenységek használata Message Passing Interface (MPI) alkalmazások futtatásához az Azure Batchben](../../batch/batch-mpi.md).

- Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.

---
title: Nagy számítási igényű Azure-beli virtuális gépek használata a Batch használatával
description: A HPC és a GPU virtuálisgép-méretek kihasználása Azure Batch készletekben. Ismerje meg az operációs rendszer függőségeit, és tekintse meg a példákat.
ms.topic: how-to
ms.date: 12/17/2018
ms.openlocfilehash: 016da7669c9e6a6586a53d379f9665c9ea048b64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147340"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>RDMA-vagy GPU-példányok használata a Batch-készletekben

Bizonyos batch-feladatok futtatásához kihasználhatja a nagyméretű számításokhoz tervezett Azure-beli virtuálisgép-méretek előnyeit. Például:

* A többpéldányos [MPI-munkaterhelések](batch-mpi.md)futtatásához válassza a H-sorozatot vagy más olyan méreteket, amelyek hálózati adapterrel rendelkeznek a távoli közvetlen memória-hozzáféréshez (RDMA). Ezek a méretek egy InfiniBand-hálózathoz csatlakoznak a csomópontok közötti kommunikációhoz, ami felgyorsíthatja az MPI-alkalmazásokat. 

* A CUDA-alkalmazások esetében válassza az N-sorozatú, NVIDIA Tesla Graphics Processing Unit (GPU) kártyákat tartalmazó méretet.

Ez a cikk útmutatást és példákat tartalmaz a Batch-készletekben található egyes Azure-beli speciális méretek használatára. A specifikációk és a háttér esetében lásd:

* Nagy teljesítményű számítási VM-méretek ([Linux](../virtual-machines/sizes-hpc.md), [Windows](../virtual-machines/sizes-hpc.md)) 

* GPU-kompatibilis VM-méretek ([Linux](../virtual-machines/sizes-gpu.md), [Windows](../virtual-machines/sizes-gpu.md)) 

> [!NOTE]
> Előfordulhat, hogy bizonyos virtuálisgép-méretek nem érhetők el azokban a régiókban, ahol létrehozza a Batch-fiókokat. Annak ellenőrzéséhez, hogy elérhető-e a méret, tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/regions/services/) , és válasszon virtuálisgép- [méretet a Batch-készlethez](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Függőségek

A Batch-ben a számítási igényű méretek RDMA vagy GPU-képességei csak bizonyos operációs rendszerekben támogatottak. (A támogatott operációs rendszerek listája az ezekben a méretekben létrehozott virtuális gépekhez támogatott részhalmaz.) A Batch-készlet létrehozási módjától függően előfordulhat, hogy további illesztőprogramokat vagy más szoftvereket kell telepítenie vagy konfigurálnia a csomópontokon. A következő táblázatok összefoglalják ezeket a függőségeket. További részletekért tekintse meg a csatolt cikkeket. A Batch-készletek konfigurálásának lehetőségeiről a cikk későbbi részében olvashat.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-készletek – virtuális gép konfigurációja

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16,04 LTS vagy<br/>CentOS-alapú HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA-illesztőprogramok | Csomópontok közötti kommunikáció engedélyezése, egyidejű feladatok végrehajtásának letiltása |
| [NC, NCv2, NCv3, NDv2 sorozat](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (változó sorozat) | Ubuntu 16,04 LTS vagy<br/>CentOS 7,3 vagy 7,4<br/>(Azure Marketplace) | NVIDIA CUDA vagy CUDA Toolkit-illesztőprogramok | N/A | 
| [NV, NVv2 sorozat](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16,04 LTS vagy<br/>CentOS 7,3<br/>(Azure Marketplace) | NVIDIA GRID-illesztőprogramok | N/A |

<sup>*</sup>Az RDMA-kompatibilis N-sorozatú méretek NVIDIA Tesla GPU-ket is tartalmazhatnak

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-készletek – virtuális gép konfigurációja

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 vagy<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 vagy újabb, vagy<br/> Intel MPI 5<br/><br/>Windows RDMA-illesztőprogramok | Csomópontok közötti kommunikáció engedélyezése, egyidejű feladatok végrehajtásának letiltása |
| [NC, NCv2, NCv3, ND, NDv2 sorozat](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (változó sorozat) | Windows Server 2016 vagy <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA vagy CUDA Toolkit-illesztőprogramok| N/A | 
| [NV, NVv2 sorozat](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 vagy<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID-illesztőprogramok | N/A |

<sup>*</sup>Az RDMA-kompatibilis N-sorozatú méretek NVIDIA Tesla GPU-ket is tartalmazhatnak

### <a name="windows-pools---cloud-services-configuration"></a>Windows-készletek – Cloud Services-konfiguráció

> [!NOTE]
> Az N sorozatú méretek nem támogatottak a Cloud Service-konfigurációval rendelkező batch-készletekben.
>

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012 vagy<br/>2008 R2 (vendég operációsrendszer-család) | Microsoft MPI 2012 R2 vagy újabb, vagy<br/>Intel MPI 5<br/><br/>Windows RDMA-illesztőprogramok | Csomópontok közötti kommunikáció engedélyezése<br/> egyidejű feladat végrehajtásának letiltása |

## <a name="pool-configuration-options"></a>Készlet konfigurációs beállításai

A Batch-készlet speciális virtuálisgép-méretének konfigurálásához több lehetőség közül választhat a szükséges szoftverek vagy illesztőprogramok telepítéséhez:

* A virtuális gépek konfigurációjában található készletek esetében válasszon egy előre konfigurált [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) -beli virtuálisgép-rendszerképet, amelyen előre telepített illesztőprogramok és szoftverek találhatók. Angol nyelvű Példák: 

  * [CentOS-alapú 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) – tartalmazza a RDMA-illesztőprogramokat és az Intel MPI 5,1-et

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) Linux vagy Windows rendszerhez – NVIDIA CUDA-illesztőprogramokat tartalmaz

  * Linux-rendszerképek a Batch-tárolók számítási feladataihoz, beleértve a GPU-t és a RDMA illesztőprogramokat is:

    * [CentOS (GPU-val és RDMA-illesztőprogramokkal) Azure Batch Container poolokhoz](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu-kiszolgáló (GPU-val és RDMA-illesztőprogramokkal) Azure Batch Container poolokhoz](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Hozzon létre egy [Egyéni Windows vagy Linux rendszerű virtuálisgép-rendszerképet](batch-sig-images.md) , amelyen telepítette az illesztőprogramokat, a szoftvereket vagy a virtuális gép méretéhez szükséges egyéb beállításokat. 

* Hozzon létre egy batch- [alkalmazáscsomag](batch-application-packages.md) egy tömörített illesztőprogramból vagy egy alkalmazás-telepítőből, és konfigurálja a Batch-t a csomag a készlet csomópontjaira történő telepítéséhez és az egyes csomópontok létrehozásakor történő telepítéséhez. Ha például az alkalmazáscsomag egy telepítő, hozzon létre egy [indítási tevékenység](jobs-and-tasks.md#start-task) parancssort az alkalmazás csendes telepítéséhez az összes készlet-csomóponton. Ha a munkaterhelés egy adott illesztőprogram-verziótól függ, érdemes lehet egy alkalmazáscsomag és egy készlet indítási feladatát használni.

  > [!NOTE] 
  > Az indítási tevékenységnek emelt szintű (rendszergazdai) engedélyekkel kell futnia, és meg kell várnia a sikerességet. A hosszú ideig futó feladatok növelik a Batch-készlet kiépítésének idejét.
  >

* A [Batch-hajógyár](https://github.com/Azure/batch-shipyard) automatikusan KONFIGURÁLJA a GPU-t és a RDMA-illesztőprogramokat a Azure batch tárolóban lévő számítási feladatokkal történő transzparens működéséhez. A Batch-hajógyár teljes mértékben a konfigurációs fájlokkal vezérelt. A GPU-k és a RDMA számítási feladatok, például a [CNTK GPU-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) lehetővé teszik, hogy a GPU-illesztőprogramokat az N sorozatú virtuális gépeken előre konfigurálja, és betölti a Microsoft Cognitive Toolkit szoftvert Docker-rendszerképként.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Példa: NVIDIA GPU-illesztőprogramok a Windows NC VM-készleten

Ha a CUDA-alkalmazásokat Windows NC-csomópontok készletén szeretné futtatni, telepítenie kell a NVDIA GPU-illesztőprogramokat. A következő példa egy alkalmazáscsomag használatával telepíti az NVIDIA GPU-illesztőprogramokat. Ezt a beállítást akkor érdemes választania, ha a számítási feladat egy adott GPU-illesztőprogram-verziótól függ.

1. Töltsön le egy telepítőcsomagot a GPU-illesztőprogramok számára a Windows Server 2016-es [verziójáról](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe)az [NVIDIA webhelyről](https://www.nvidia.com/Download/index.aspx) – például: 411,82. Mentse a fájlt helyileg egy rövid névvel, például az *GPUDriverSetup.exe*.
2. Hozzon létre egy zip-fájlt a csomagból.
3. Töltse fel a csomagot a Batch-fiókjába. A lépéseket lásd az [alkalmazáscsomag](batch-application-packages.md) útmutatójában. Olyan alkalmazás-azonosítót kell megadnia, mint a *GPUDriver*, valamint egy olyan verzió, mint például a *411,82*.
1. A Batch API-k vagy a Azure Portal használatával hozzon létre egy készletet a virtuális gép konfigurációjában a kívánt számú csomópont és skála alapján. Az alábbi táblázat az NVIDIA GPU-illesztőprogramok csendes üzembe helyezését mutatja be az indítási tevékenység használatával:

| Beállítás | Érték |
| ---- | ----- | 
| **Rendszerkép típusa** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Ajánlat** | WindowsServer |
| **SKU** | 2016 – Datacenter |
| **Csomópont mérete** | NC6 standard |
| **Alkalmazáscsomag-referenciák** | GPUDriver, 411,82-es verzió |
| **Indítási feladat engedélyezve** | Igaz<br>**Parancssor** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Felhasználói identitás** -készlet autouser, rendszergazda<br/>**Várakozás a sikerre** – igaz

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Példa: NVIDIA GPU-illesztőprogramok Linux NC virtuálisgép-készleten

Ha a CUDA-alkalmazásokat Linux NC-csomópontok készletén szeretné futtatni, telepítenie kell a szükséges NVIDIA Tesla GPU-illesztőprogramokat a CUDA eszközkészletből. Az alábbi példa egy egyéni Ubuntu 16,04 LTS-lemezkép létrehozását és üzembe helyezését hajtja végre a GPU-illesztőprogramokkal:

1. Az Ubuntu 16,04 LTS rendszert futtató Azure NC sorozatú virtuális gép üzembe helyezése. Hozza létre például a virtuális gépet az USA déli középső régiójában. 
2. Adja hozzá az [NVIDIA GPU-illesztőprogramok bővítményt](../virtual-machines/extensions/hpccompute-gpu-linux.md
) a virtuális géphez a Azure Portal, az Azure-előfizetéshez csatlakozó ügyfélszámítógép vagy Azure Cloud Shell használatával. Azt is megteheti, hogy a virtuális géphez való kapcsolódáshoz és a [CUDA-illesztőprogramok manuális telepítéséhez](../virtual-machines/linux/n-series-driver-setup.md) kövesse a lépéseket.
3. A Batch szolgáltatáshoz tartozó megosztott képkatalógus [rendszerképének](batch-sig-images.md) létrehozásához kövesse az alábbi lépéseket.
4. Hozzon létre egy batch-fiókot egy olyan régióban, amely támogatja az NC virtuális gépeket.
5. A Batch API-k vagy Azure Portal használatával hozzon létre egy készletet [az egyéni rendszerkép](batch-sig-images.md) és a kívánt számú csomópont és skála alapján. A következő táblázat a rendszerkép minta-készletének beállításait mutatja be:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Egyéni rendszerkép |
| **Egyéni rendszerkép** | *A rendszerkép neve* |
| **Csomópont-ügynök SKU** | Batch. Node. Ubuntu 16,04 |
| **Csomópont mérete** | NC6 standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Példa: Microsoft MPI Windows H16r virtuálisgép-készleten

Ha Windows MPI-alkalmazásokat szeretne futtatni az Azure H16r virtuálisgép-csomópontok készletén, konfigurálnia kell a HpcVmDrivers-bővítményt, és telepítenie kell a [Microsoft MPI](/message-passing-interface/microsoft-mpi)-t. Íme néhány példa a Windows Server 2016-lemezkép telepítésére a szükséges illesztőprogramokkal és szoftverekkel:

1. Helyezzen üzembe egy Windows Server 2016 rendszerű Azure H16r virtuális gépet. Hozza létre például a virtuális gépet az USA nyugati régiójában. 
2. Adja hozzá a HpcVmDrivers-bővítményt a virtuális géphez egy [Azure PowerShell parancs futtatásával](../virtual-machines/sizes-hpc.md) egy olyan ügyfélszámítógépről, amely csatlakozik az Azure-előfizetéséhez, vagy használja a Azure Cloud shell. 
1. Hozzon Távoli asztal kapcsolódni a virtuális géphez.
1. Töltse le a Microsoft MPI legújabb verziójához készült telepítőcsomagot (MSMpiSetup.exe), és [telepítse a Microsoft](https://www.microsoft.com/download/details.aspx?id=57467) MPI-t.
1. A Batch szolgáltatáshoz tartozó megosztott képkatalógus [rendszerképének](batch-sig-images.md) létrehozásához kövesse az alábbi lépéseket.
1. A Batch API-k vagy Azure Portal használatával hozzon létre egy készletet [a megosztott rendszerkép](batch-sig-images.md) -katalógus és a kívánt számú csomópont és skála alapján. A következő táblázat a rendszerkép minta-készletének beállításait mutatja be:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Egyéni rendszerkép |
| **Egyéni rendszerkép** | *A rendszerkép neve* |
| **Csomópont-ügynök SKU** | Batch. Node. Windows amd64 |
| **Csomópont mérete** | H16r standard |
| **Csomópontok közötti kommunikáció engedélyezve** | Igaz |
| **Feladatok maximális száma/csomópont** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Példa: Intel MPI Linux H16r virtuálisgép-készleten

Ha MPI-alkalmazásokat szeretne futtatni a Linux H sorozatú csomópontok készletén, az egyik lehetőség az Azure Marketplace-ről származó [CentOS-alapú 7,4 HPC-](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) rendszerkép használata. A Linux RDMA-illesztőprogramok és az Intel MPI-meghajtók előre telepítve vannak. Ez a rendszerkép a Docker-tároló munkaterheléseit is támogatja.

A Batch API-k vagy Azure Portal használatával hozzon létre egy készletet a rendszerkép és a kívánt számú csomópont és skála alapján. A következő táblázat a minta készlet beállításait mutatja be:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Ajánlat** | CentOS – HPC |
| **SKU** | 7,4 |
| **Csomópont mérete** | H16r standard |
| **Csomópontok közötti kommunikáció engedélyezve** | Igaz |
| **Feladatok maximális száma/csomópont** | 1 |

## <a name="next-steps"></a>Következő lépések

* Az MPI-feladatok Azure Batch készleten való futtatásához tekintse meg a Windows vagy [Linux](/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch) [rendszerű](batch-mpi.md) példákat.

* A Batch-beli GPU-munkaterhelésekre vonatkozó példákat a [Batch hajógyári](https://github.com/Azure/batch-shipyard/) receptek című témakörben talál.

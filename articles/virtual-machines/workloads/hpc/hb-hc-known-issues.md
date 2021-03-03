---
title: A HPC-és GPU-alapú virtuális gépek ismert problémáinak elhárítása – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure-beli HPC-és GPU-alapú virtuálisgép-méretek ismert problémáinak elhárítását.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 1/19/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 83f9778da91cebb651d98e2e85748cda7435230a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674670"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>A H- és N-sorozatú virtuális gépek ismert problémái

Ez a cikk a [H-sorozat](../../sizes-hpc.md) és az [N-sorozat](../../sizes-gpu.md) HPC-és GPU-alapú virtuális gépek használatának leggyakoribb problémáit és megoldásait ismerteti.

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Gyorsított hálózatkezelés a HB, a HC, a HBv2 és a NDv2

Az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) mostantól elérhető az RDMA és InfiniBand-kompatibilis, valamint az SR-IOV-kompatibilis VM-méretek [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md) és [NDv2](../../ndv2-series.md). Ez a funkció mostantól lehetővé teszi az Azure Ethernet-hálózaton keresztüli (akár 30 GB/s) és késleltetési szintű bővítést. Bár ez elkülönül a RDMA képességeitől a InfiniBand-hálózaton keresztül, bizonyos platform-változások hatással lehetnek bizonyos MPI-implementációk viselkedésére, amikor a InfiniBand-en keresztül felveszik a feladatokat. Az egyes virtuális gépek InfiniBand felülete némileg eltérő névvel rendelkezhet (mlx5_1 a korábbi mlx5_0hoz képest), és ez szükségessé teheti az MPI-parancssorok csípését, különösen a UCX felület (általában OpenMPI és HPC-X) használata esetén.
Erről a [blogbejegyzésről](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) további részleteket a megfigyelt problémák megoldását ismertető cikkben talál.

## <a name="infiniband-driver-installation-on-n-series-vms"></a>InfiniBand-illesztőprogram telepítése N sorozatú virtuális gépeken

NC24r_v3 és ND40r_v2 az SR-IOV engedélyezve van, miközben a NC24r és a NC24r_v2 nem engedélyezett az SR-IOV. Néhány részlet a bifurkációs [itt](../../sizes-hpc.md#rdma-capable-instances).
A InfiniBand (IB) konfigurálható az SR-IOV-kompatibilis virtuálisgép-méretekben a OFED-illesztőprogramokkal, míg a nem SR-IOV virtuálisgép-méretekhez ND-illesztőprogramok szükségesek. Ez az IB-támogatás megfelelően elérhető a [CentOS-HPC-VMIs](configure.md). Az Ubuntu esetében tekintse meg a OFED és az ND-illesztőprogramok telepítésének [utasításait](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) a [docs](enable-infiniband.md#vm-images-with-infiniband-drivers)részben leírtak szerint.

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplikált MAC a Cloud-init Ubuntu használatával H-sorozatú és N sorozatú virtuális gépeken

Az Ubuntu VM-rendszerképeken a Cloud-init ismert hibája az IB-interfész üzembe helyezése. Ez a virtuális gép újraindításakor vagy az általánosítás utáni virtuálisgép-rendszerkép létrehozásakor fordulhat elő. A virtuális gép rendszerindítási naplói a következőhöz hasonló hibát jeleznek: "hálózati szolgáltatás indítása... RuntimeError: duplikált Mac rendszer található! a "eth1" és a "ib0" is Mac ".

Ez egy ismert probléma a "duplikált MAC with Cloud-init on Ubuntu" néven. A megkerülő megoldás:
1) Az (Ubuntu 18,04) Piactéri virtuálisgép-rendszerkép üzembe helyezése
2) Telepítse a szükséges csomagokat az IB engedélyezéséhez ([itt az utasítás](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Módosítsa az waagent. conf fájlt a EnableRDMA = y módosításához
4) Hálózatkezelés letiltása a felhőben – init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) A Cloud-init által generált netplan hálózati konfigurációs fájljának szerkesztése a MAC eltávolításához
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series"></a>DRAM on HB sorozat

A HB sorozatú virtuális gépek jelenleg csak 228 GB RAM-ot tudnak kiszolgálni a vendég virtuális gépeknek. Ennek az az oka, hogy az Azure hypervisor ismert korlátozása miatt a lapok nem rendelhetők hozzá a vendég virtuális géphez fenntartott AMD CCX (NUMA-tartományok) helyi DRAM-hoz.

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Az Azure-alapú gyorsított hálózatkezelés az IB-kompatibilis HPC-és GPU-alapú virtuális gépeken jelenleg nincs engedélyezve. Ha ezt a funkciót támogatja, értesítjük az ügyfeleket.

## <a name="qp0-access-restriction"></a>qp0 hozzáférési korlátozás

Ha meg szeretné akadályozni, hogy az alacsony szintű hardveres hozzáférés biztonsági réseket eredményezhet, a várólista-párok 0 nem érhetőek el a vendég virtuális gépek számára. Ez csak a ConnectX-5 NIC felügyeletéhez kapcsolódó műveleteket befolyásolja, és bizonyos InfiniBand-diagnosztika, például a ibdiagnet, de nem végfelhasználói alkalmazások futtatására is hatással van.

## <a name="gss-proxy"></a>GSS proxy

A GSS proxy egy ismert hibával rendelkezik a CentOS/RHEL 7,5-ben, amely az NFS-sel való használat során jelentős teljesítmény-és reagálási szankcióként is megnyilvánulhat. Ezt a következővel lehet enyhíteni:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Gyorsítótár tisztítása

A HPC-rendszereken gyakran hasznos a memória megtisztítása a feladatok befejeződése után, mielőtt a következő felhasználó hozzá lett rendelve ugyanahhoz a csomóponthoz. A Linuxon futó alkalmazások futtatása után előfordulhat, hogy a rendelkezésre álló memória csökkenti a puffer memóriájának növekedését, és nem futtat alkalmazásokat.

![Képernyőkép a parancssorból a tisztítás előtt](./media/known-issues/cache-cleaning-1.png)

`numactl -H`A (z) használatával megtudhatja, hogy a memória mely NUMAnode (ek) ba van pufferelt (valószínűleg az összes). A Linux rendszerben a felhasználók háromféleképpen tudják megtisztítani a gyorsítótárat, hogy a pufferelt vagy gyorsítótárazott memóriát "ingyenes" értékre állítsa vissza. A root vagy a sudo engedélyekkel kell rendelkeznie.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Képernyőkép a parancssorból a tisztítás után](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-figyelmeztetések

A következő kernel figyelmeztető üzenetek figyelmen kívül hagyhatók egy HB sorozatú virtuális gép Linux rendszerű indításakor. Ennek az az oka, hogy az Azure hypervisor egy ismert korlátozása, amely az idő múlásával lesz kezelve.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Következő lépések

- Tekintse át a [HB-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat áttekintését](hc-series-overview.md) , amelyből megismerheti a számítási feladatok optimális konfigurálását a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti nézetét lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.

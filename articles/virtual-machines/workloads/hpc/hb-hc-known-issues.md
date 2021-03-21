---
title: A HPC-és GPU-alapú virtuális gépek ismert problémáinak elhárítása – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure-beli HPC-és GPU-alapú virtuálisgép-méretek ismert problémáinak elhárítását.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: e8d191dfed5b33116dadaf34b17d5f6525060e13
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721206"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>A H- és N-sorozatú virtuális gépek ismert problémái

Ez a cikk a [H-sorozat](../../sizes-hpc.md) és az [N-sorozat](../../sizes-gpu.md) HPC-és GPU-alapú virtuális gépek használatakor a legutóbbi gyakori problémákat és azok megoldásait próbálja meglistázni.

## <a name="mofed-installation-on-ubuntu"></a>MOFED-telepítés Ubuntu rendszeren
Az Ubuntu-18,04-es verzióban a 5.4.0-1041-Azure kernel verziója nem kompatibilis a MOFED 5.2-2 és 5.2-1.0.4.0 verziójával. Javasoljuk, hogy visszagörgetje a kernel 5.4.0-1040-Azure-ra vagy egy régebbi kernelt használó piactér-rendszerképet, és ne frissítse a kernelt. Ezt a problémát várhatóan egy újabb MOFED kell feloldani.

## <a name="known-issues-on-hbv3"></a>A HBv3 ismert problémái
- A InfiniBand jelenleg csak az 120-Core virtuális gépen (Standard_HB120rs_v3) támogatott.
- Az Azure gyorsított hálózatkezelés jelenleg minden régióban nem támogatott a HBv3 sorozatokban.

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Gyorsított hálózatkezelés a HB, a HC, a HBv2 és a NDv2

Az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) mostantól elérhető a RDMA és InfiniBand-kompatibilis, valamint az SR-IOV-kompatibilis VM-méretek [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md)és [NDv2](../../ndv2-series.md). Ez a funkció mostantól lehetővé teszi az Azure Ethernet-hálózaton keresztüli (akár 30 GB/s) és késleltetési szintű bővítést. Bár ez elkülönül a RDMA képességeitől a InfiniBand-hálózaton keresztül, bizonyos platform-változások hatással lehetnek bizonyos MPI-implementációk viselkedésére, amikor a feladatok a InfiniBand-en keresztül futnak. Az egyes virtuális gépek InfiniBand felülete némileg eltérő névvel rendelkezhet (mlx5_1 a korábbi mlx5_0hoz képest), és ez szükségessé teheti az MPI-parancssorok csípését, különösen a UCX felület (általában OpenMPI és HPC-X) használata esetén. A legegyszerűbb megoldás jelenleg a legújabb HPC-X használata a CentOS-HPC virtuálisgép-rendszerképeken, vagy ha nem szükséges, tiltsa le a gyorsított hálózatkezelést.
Erről a TechCommunity a jelen cikk további, a megfigyelt problémák megoldására vonatkozó utasításokat ismertető [cikkben](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) talál további információt.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>InfiniBand-illesztőprogram telepítése nem SR-IOV virtuális gépeken

Jelenleg a H16r, a H16mr és a NC24r nem engedélyezett az SR-IOV. A InfiniBand stack bifurkációs néhány részlete [itt](../../sizes-hpc.md#rdma-capable-instances)található.
A InfiniBand konfigurálható az SR-IOV-kompatibilis virtuálisgép-méretekben a OFED-illesztőprogramokkal, míg a nem SR-IOV virtuálisgép-méretekhez ND-illesztőprogramok szükségesek. Ez az IB-támogatás megfelelő a [CentOS, a RHEL és az Ubuntu](configure.md)számára.

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplikált MAC a Cloud-init Ubuntu használatával H-sorozatú és N sorozatú virtuális gépeken

Az Ubuntu VM-rendszerképeken a Cloud-init ismert hibája az IB-interfész üzembe helyezése. Ez a virtuális gép újraindításakor vagy az általánosítás utáni virtuálisgép-rendszerkép létrehozásakor fordulhat elő. A virtuális gép rendszerindítási naplói a következőhöz hasonló hibát jeleznek:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Ez egy ismert probléma a "duplikált MAC with Cloud-init on Ubuntu" néven. Ez újabb kernelekben lesz feloldva. Ha a probléma előfordul, a megoldás a következő:
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

## <a name="qp0-access-restriction"></a>qp0 hozzáférési korlátozás

Ha meg szeretné akadályozni, hogy az alacsony szintű hardveres hozzáférés biztonsági réseket eredményezhet, a várólista-párok 0 nem érhetőek el a vendég virtuális gépek számára. Ez csak a ConnectX-5 NIC felügyeletéhez kapcsolódó műveleteket befolyásolja, és bizonyos InfiniBand-diagnosztika, például a ibdiagnet, de nem végfelhasználói alkalmazások futtatására is hatással van.

## <a name="dram-on-hb-series-vms"></a>DRAM a HB sorozatú virtuális gépeken

A HB sorozatú virtuális gépek jelenleg csak 228 GB RAM-ot tudnak kiszolgálni a vendég virtuális gépeknek. Hasonlóképpen, 458 GB HBv2 és 448 GB HBv3 virtuális gépeken. Ennek az az oka, hogy az Azure hypervisor ismert korlátozása miatt a lapok nem rendelhetők hozzá a vendég virtuális géphez fenntartott AMD CCX (NUMA-tartományok) helyi DRAM-hoz.

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
- Olvassa el a legújabb bejelentéseket, a HPC számítási feladatait és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti nézetét lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.

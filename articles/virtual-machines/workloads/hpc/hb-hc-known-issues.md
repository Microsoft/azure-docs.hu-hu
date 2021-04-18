---
title: HPC- és GPU-alapú virtuális gépek ismert hibáinak elhárítása – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg a HPC- és GPU-alapú virtuálisgép-méretekkel kapcsolatos ismert problémák elhárítását az Azure-ban.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f5bdae17126048da153f70bf27609bcc4b92fe21
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599587"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>A H- és N-sorozatú virtuális gépek ismert problémái

Ez a cikk a H- és [N-sorozatú](../../sizes-hpc.md) HPC- és [GPU-alapú](../../sizes-gpu.md) virtuális gépek használata során gyakran használt problémákat és azok megoldásait próbálja meg listába sorolni.

## <a name="qp0-access-restriction"></a>qp0 – Hozzáférés korlátozása

A 0. üzenetsorpár a vendég virtuális gépek számára nem érhető el, hogy megakadályozza az alacsony szintű hardverelérést, amely biztonsági réseket eredményezhet. Ez csak a ConnectX InfiniBand hálózati adapter felügyeletével és bizonyos InfiniBand-diagnosztikák, például az ibdiagnet futtatásával kapcsolatos műveleteket érinti, a végfelhasználói alkalmazásokat azonban nem.

## <a name="mofed-installation-on-ubuntu"></a>MOFED-telepítés Ubuntun
Ubuntu-18.04-alapú Marketplace virtuálisgép-rendszerképeken, amelyek kernelverzióval és újabb verzióval vannak, néhány régebbi Mellanox OFED nem kompatibilis, ami bizonyos esetekben akár 30 percig is megnöveli a virtuális gépek rendszerindítási `5.4.0-1039-azure #42` idejét. Ez a Mellanox OFED 5.2-1.0.4.0-s és 5.2-2.2.0.0-s verziójára is jelentve. A probléma a Mellanox OFED 5.3-1.0.0.1-es kiadásával oldható meg.
Ha az OFED inkompatibilis használata szükséges, a megoldás a **Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** marketplace virtuálisgép-rendszerkép vagy régebbi használata, és nem a kernel frissítése.

## <a name="mpi-qp-creation-errors"></a>MPI QP-létrehozási hibák
Ha bármilyen MPI számítási feladat futtatása közben infiniBand QP-létrehozási hibákat ad vissza, például az alábbiakban látható, javasoljuk, hogy indítsa újra a virtuális gépet, és próbálja újra a számítási feladatot. Ez a probléma a jövőben megoldva lesz.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

A probléma észlelése esetén ellenőrizheti az üzenetsorpárok maximális számának értékeit az alábbiak szerint.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Gyorsított hálózatépítés HB- , HC-, HBv2- és NDv2-n

[Az Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) mostantól elérhető az RDMA- és InfiniBand-kompatibilis, valamint az SR-IOV-kompatibilis [HB,](../../hb-series.md) [HC,](../../hc-series.md) [HBv2](../../hbv2-series.md)és [NDv2](../../ndv2-series.md)virtuális gépeken. Ez a képesség mostantól lehetővé teszi az Azure Ethernet-hálózaton keresztüli teljes (akár 30 Gb/s- és késési) sebességet. Bár ez elkülönül az InfiniBand-hálózaton található RDMA-képességektől, a képesség egyes platformváltozásai hatással lehetnek bizonyos MPI-implementációk viselkedésére, amikor InfiniBand-kapcsolaton keresztül futtat feladatokat. Egyes virtuális gépek InfiniBand-felülete kissé eltérő névvel is rendelkezik (mlx5_1 a korábbi mlx5_0-hez képest), és ehhez szükség lehet az MPI-parancssorok finomhangolására, különösen az UCX-felület (általában OpenMPI-vel és HPC-X-szel) használata esetén. A legegyszerűbb megoldás jelenleg az lehet, hogy a legújabb HPC-X-et használja a CentOS-HPC VIRTUÁLISgép-rendszerképeken, vagy letiltja a gyorsított hálózathasználatot, ha nem szükséges.
További részleteket ebben a [TechCo rendelkezésre](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) álló cikkben talál, és útmutatást ad a megfigyelt problémák megoldásához.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>InfiniBand-illesztőprogram telepítése nem SR-IOV virtuális gépeken

Jelenleg a H16r, a H16mr és az NC24r nincs engedélyezve az SR-IOV számára. Az InfiniBand-verem kétértelmének néhány részlete itt [van.](../../sizes-hpc.md#rdma-capable-instances)
Az InfiniBand az SR-IOV-kompatibilis virtuálisgép-méretekkel konfigurálható az OFED-illesztőprogramokkal, míg a nem SR-IOV virtuálisgép-méretek ND-illesztőprogramokat igényelnek. Ez az IB-támogatás megfelelően érhető el [CentOS, RHEL és Ubuntu esetén.](configure.md)

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>MAC duplikálta a cloud-initet az Ubuntuval H- és N-sorozatú virtuális gépeken

Az Ubuntu VM-rendszerképeken a cloud-init egy ismert probléma miatt próbálja meg elhozni az IB-felületet. Ez történhet a virtuális gép újraindításakor, vagy amikor az általánosítást követően virtuálisgép-rendszerképet próbál létrehozni. A virtuális gép rendszerindítási naplói a következő hibaüzenetet jelenhetnek meg:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Ez a "duplikált MAC-cím a cloud-init on Ubuntu rendszeren" ismert probléma. Ezt az újabb kernelek oldják fel. Ha a probléma felmerült, megkerülő megoldásként a következőt kell megkerülni:
1) Az (Ubuntu 18.04) Marketplace virtuálisgép-rendszerkép üzembe helyezése
2) Telepítse a szükséges szoftvercsomagokat az IB engedélyezéséhez ([itt útmutatást)](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)
3) Szerkessze a waagent.conf-t az EnableRDMA=y módosítása érdekében
4) Hálózat letiltása a cloud-initben
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Szerkessze a netplan cloud-init által létrehozott hálózati konfigurációs fájlját a MAC eltávolításához
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM HB-sorozatú virtuális gépeken

A HB-sorozat virtuális gépei jelenleg csak 228 GB RAM-ot tudnak elérhetővé téve a vendég virtuális gépek számára. Hasonlóképpen, 458 GB a HBv2 virtuális gépeken és 448 GB a HBv3 virtuális gépeken. Ennek oka az Azure-hipervizor ismert korlátozása, amely megakadályozza, hogy a vendég virtuális gép számára fenntartott AMD CCX-tartományok (NUMA-tartományok) helyi DRAM-jába oldalakat rendelnek hozzá.

## <a name="gss-proxy"></a>GSS-proxy

A GSS-proxy ismert hiba a CentOS/RHEL 7.5-ben, amely az NFS-sel való használata esetén jelentős teljesítmény- és válaszképességi büntetésként jelentkezik. Ezt a következővel lehet enyhíteni:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Gyorsítótár-tisztítás

HPC rendszereken gyakran hasznos a memória megtisztítása a feladat befejezése után, mielőtt a következő felhasználóhoz ugyanazt a csomópontot rendelik. Az alkalmazások Linuxon való futtatása után előfordulhat, hogy a rendelkezésre álló memória csökken, miközben a puffermemória növekszik, annak ellenére, hogy nem futtat alkalmazásokat.

![Képernyőkép a parancssorról a tisztítás előtt](./media/known-issues/cache-cleaning-1.png)

A `numactl -H` használatával megmutatja, hogy mely NUMAnode(k) pufferelte a memóriát (valószínűleg az összes). Linux rendszeren a felhasználók háromféleképpen tisztíthatja meg a gyorsítótárat, hogy pufferelt vagy gyorsítótárazott memóriát adjanak vissza "ingyenesnek". Gyökér szintűnek kell lennie, vagy sudo engedélyekkel kell rendelkeznie.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Képernyőkép a parancssorról a tisztítás után](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-figyelmeztetések

A HB sorozatú virtuális gépek Linuxon való rendszerindításakor figyelmen kívül hagyhatja a következő kernel-figyelmeztető üzeneteket. Ennek oka az Azure hipervizor ismert korlátozása, amely idővel foglalkozik.

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

- Tekintse át [a HB-sorozatok áttekintését](hb-series-overview.md) és a [HC-sorozatok](hc-series-overview.md) áttekintését, amelyekből megtudhatja, hogyan konfigurálja optimálisan a számítási feladatokat a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb közleményeket, HPC számítási feladatok példáit és a teljesítményeredményeket a [Azure Compute Tech Community Blogon.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- A HPC számítási feladatok futtatásának magasabb szintű architekturális nézetét lásd: Nagy teljesítményű [számítástechnika (HPC) az Azure-ban.](/azure/architecture/topics/high-performance-computing/)

---
title: Azure VMware-megoldás CloudSimple használatával – a számítási feladatok virtuális gépei áttelepíthetők a privát felhőbe
description: Ismerteti, hogyan telepíthet át virtuális gépeket a helyszíni vCenter a CloudSimple saját Felhőbeli vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899320"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>A számítási feladatok virtuális gépei migrálása a helyszíni vCenter a saját Felhőbeli vCenter-környezetbe

Ha a virtuális gépeket a helyszíni adatközpontból a saját CloudSimple-felhőbe szeretné áttelepíteni, több lehetőség is rendelkezésre áll.  A privát felhő natív hozzáférést biztosít a VMware vCenter, a VMware által támogatott eszközöket pedig a munkaterhelések áttelepítéséhez használhatja. Ez a cikk a vCenter áttelepítési lehetőségeinek némelyikét ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A virtuális gépek és az adatok helyszíni adatközpontból történő áttelepítéséhez az adatközponttól a saját felhőalapú környezetéhez való hálózati kapcsolat szükséges.  A hálózati kapcsolat létrehozásához használja a következő módszerek egyikét:

* [Helyek közötti VPN-kapcsolat](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni környezet és a saját felhő között.
* ExpressRoute Global Reach kapcsolatot a helyszíni ExpressRoute-áramkör és egy CloudSimple ExpressRoute-áramkör között.

A helyszíni vCenter-környezetből a privát felhőbe való hálózati elérési útnak elérhetőnek kell lennie a virtuális gépek vMotion használatával történő áttelepítéséhez.  A helyszíni vCenter lévő vMotion-hálózatnak útválasztási képességekkel kell rendelkeznie.  Ellenőrizze, hogy a tűzfal engedélyezi-e az összes vMotion-forgalmat a helyszíni vCenter és a saját felhőalapú vCenter között. (A privát felhőben a vMotion-hálózat útválasztása alapértelmezés szerint konfigurálva van.)

## <a name="migrate-isos-and-templates"></a>ISO-és sablonok migrálása

Ha új virtuális gépeket szeretne létrehozni a saját felhőben, használja az ISO-ket és a virtuálisgép-sablonokat.  Az ISO-és sablonok saját Felhőbeli vCenter való feltöltéséhez és elérhetővé tételéhez használja a következő módszert.

1. Töltse fel az ISO-t a Private Cloud vCenter a vCenter felhasználói felületéről.
2. [Hozzon közzé egy Content Library](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) -t a saját felhőalapú vCenter:

    1. Tegye közzé a helyszíni tartalom-függvénytárat.
    2. Hozzon létre egy új tartalmat a saját felhő vCenter.
    3. Fizessen elő a közzétett helyszíni tartalom-függvénytárra.
    4. Szinkronizálja a tartalmi könyvtárat az előfizetett tartalmak eléréséhez.

## <a name="migrate-vms-using-powercli"></a>Virtuális gépek migrálása a PowerCLI használatával

Ha virtuális gépeket szeretne áttelepíteni a helyszíni vCenter a saját Felhőbeli vCenter, használja a VMware PowerCLI vagy a VMware Labs-ból elérhető Cross vCenter munkaterhelés-áttelepítési segédprogramot.  A következő minta parancsfájl a PowerCLI áttelepítési parancsokat mutatja be.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> A cél vCenter-kiszolgáló és ESXi-gazdagépek nevének használatához konfigurálja a DNS-továbbítást a helyszínről a privát felhőbe.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Virtuális gépek migrálása a NSX 2. rétegbeli VPN használatával

Ez a beállítás lehetővé teszi a számítási feladatok élő áttelepítését a helyszíni VMware-környezetből az Azure-beli privát felhőbe.  Ezzel a kibővített 2. rétegbeli hálózattal a helyszíni alhálózat elérhető lesz a privát felhőben.  Az áttelepítés után az új IP-cím hozzárendelése nem szükséges a virtuális gépekhez.

A számítási [feladatok migrálása a 2. rétegbeli hálózatokkal](migration-layer-2-vpn.md) című témakör leírja, hogyan használható 2. RÉTEGbeli VPN a 2. rétegbeli hálózat kiépítéséhez a helyi környezetből a saját felhőbe.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Virtuális gépek migrálása biztonsági mentési és vész-helyreállítási eszközök használatával

A virtuális gépeknek a privát felhőbe történő áttelepítése a biztonsági mentési/visszaállítási eszközök és a vész-helyreállítási eszközök használatával végezhető el.  A privát felhőt célként használhatja a harmadik féltől származó eszköz használatával létrehozott biztonsági másolatok visszaállításához.  A privát felhő a VMware SRM vagy egy harmadik féltől származó eszköz használatával vész-helyreállítási célpontként is használható.

Ezen eszközök használatával kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Munkaterhelési virtuális gépek biztonsági mentése a CloudSimple privát felhőben a Veeam B&R használatával](backup-workloads-veeam.md)
* [A CloudSimple saját felhő beállítása vész-helyreállítási helyként a helyszíni VMware-alapú számítási feladatokhoz](disaster-recovery-zerto.md)

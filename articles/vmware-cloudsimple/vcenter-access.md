---
title: Azure VMware-megoldás CloudSimple – hozzáférés vSphere-ügyfélhez
description: Ismerteti, hogyan érheti el a vCenter a saját felhőben.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899116"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Hozzáférés a saját felhőalapú vCenter-portálhoz

A saját felhőalapú vCenter-portált Azure Portal vagy CloudSimple portálról indíthatja el.  a vCenter-portál lehetővé teszi a VMware-infrastruktúra kezelését a saját felhőben.

## <a name="before-you-begin"></a>Előkészületek

Meg kell teremteni a hálózati kapcsolatokat, és engedélyezni kell a DNS-névfeloldást a vCenter-portálhoz való hozzáféréshez.  Az alábbi lehetőségek bármelyikével létrehozhat hálózati kapcsolatot a saját felhőhöz.

* [Kapcsolódás a helyszínről a CloudSimple a ExpressRoute használatával](on-premises-connection.md)
* [VPN-kapcsolat konfigurálása saját CloudSimple-felhőhöz](set-up-vpn.md)

A saját felhőalapú VMware-infrastruktúra összetevőinek DNS-névfeloldásának beállításához lásd: DNS-névfeloldás beállítása a [saját felhőalapú vCenter való hozzáféréshez a helyszíni munkaállomásokról](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-vcenter-from-azure-portal"></a>VCenter elérése a Azure Portalból

A vCenter-portált elindíthatja saját felhőből Azure Portalról.

1. Válassza az **Összes szolgáltatás** elemet.

2. Keressen rá a **CloudSimple Services** kifejezésre.

3. Válassza ki azt a CloudSimple-szolgáltatást, amelyhez csatlakozni szeretne.

4. Az **Áttekintés** lapon kattintson a **VMware Private-felhők megtekintése** elemre.

    ![A CloudSimple szolgáltatás áttekintése](media/cloudsimple-service-overview.png)

5. Válassza ki a privát felhőt a privát felhők listájából, és kattintson az **vSphere-ügyfél indítása** lehetőségre.

    ![VSphere-ügyfél elindítása](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>VCenter elérése a CloudSimple-portálról

A privát felhő vCenter-portálját a CloudSimple portálról indíthatja el.

1. Hozzáférés a [CloudSimple-portálhoz](access-cloudsimple-portal.md).

2. Az **erőforrások** közül válassza ki a privát felhőt, amelyet szeretne elérni, és kattintson a **vSphere-ügyfél indítása** lehetőségre.

    ![VSphere-ügyfél elindítása – erőforrások](media/cloudsimple-portal-resources-launch-vcenter.png)

3. A vCenter-portált a privát felhő összegző képernyőjéről is elindíthatja.

    ![VSphere-ügyfél indítása – összefoglalás](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Következő lépések

* [VLAN-ok/alhálózatok létrehozása és kezelése saját felhők számára](create-vlan-subnet.md)
* [A VMware vCenter CloudSimple saját felhőalapú engedélyezési modellje](learn-private-cloud-permissions.md)
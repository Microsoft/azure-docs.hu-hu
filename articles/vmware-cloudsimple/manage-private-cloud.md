---
title: Azure VMware-megoldás kezelése CloudSimple privát felhővel
description: Ismerteti a CloudSimple saját Felhőbeli erőforrásainak és tevékenységének kezeléséhez rendelkezésre álló képességeket
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895206"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Saját Felhőbeli erőforrások és tevékenységek kezelése

A privát felhők kezelése a CloudSimple-portálról történik.  A CloudSimple-portálon keresse meg az állapotot, a rendelkezésre álló erőforrásokat, a saját Felhőbeli tevékenységeket és egyéb beállításokat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Privát felhők listájának megtekintése

Az **erőforrások** lap **privát felhők** lapja felsorolja az előfizetésben található összes privát felhőt. Az adatok tartalmazzák a nevet, a vSphere-fürtök számát, a helyet, a saját felhő aktuális állapotát és az erőforrás-információkat.

![Saját felhő lapja](media/manage-private-cloud.png)

További információért és műveletekhez válasszon ki egy privát felhőt.

## <a name="private-cloud-summary"></a>Saját felhő összefoglalása

A kiválasztott privát felhő átfogó összefoglalásának megtekintése.  Az összefoglalás lapon a privát felhőben üzembe helyezett DNS-kiszolgálók szerepelnek.  A DNS-továbbítást beállíthatja a helyszíni DNS-kiszolgálókról a saját felhőalapú DNS-kiszolgálóira.  A DNS-továbbítással kapcsolatos további információkért lásd: [a DNS konfigurálása névfeloldáshoz a saját felhőalapú vCenter](./on-premises-dns-setup.md)a helyszíni környezetből.

![Saját felhő összefoglalása](media/private-cloud-summary.png)

### <a name="available-actions"></a>Elérhető műveletek

* [Indítsa el a vSphere-ügyfelet](./vcenter-access.md). A vCenter elérése ehhez a privát felhőhöz.
* [Megvásárlási csomópontok](create-nodes.md). Csomópontok hozzáadása ehhez a privát felhőhöz.
* [Bontsa ki](expand-private-cloud.md)a elemet. Csomópontok hozzáadása ehhez a privát felhőhöz.
* **Frissítés**. Az oldalon található információk frissítése.
* **Delete**. A privát felhőt bármikor törölheti. **A törlés előtt győződjön meg róla, hogy minden rendszerről és adattal készített biztonsági másolatot.** A privát felhő törlése törli az összes virtuális gépet, a vCenter-konfigurációt és az összes adathalmazt. Kattintson a **Törlés** lehetőségre a kiválasztott privát felhő Összegzés szakaszában. A törlést követően az összes magánjellegű Felhőbeli adat törlődik egy biztonságos, szigorúan kompatibilis törlési folyamatba.
* [VSphere-jogosultságok módosítása](escalate-private-cloud-privileges.md)  A jogosultságokat a privát felhőben is kiterjesztheti.

## <a name="private-cloud-vlanssubnets"></a>Privát felhő VLAN-ok/alhálózatok

Megtekintheti a kijelölt VLAN-ok/alhálózatok listáját a kiválasztott privát felhőhöz.  A lista tartalmazza a privát felhő létrehozásakor létrehozott felügyeleti VLAN-okat/alhálózatokat.

![Privát felhő – VLAN-ok/alhálózatok](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Elérhető műveletek

* [VLAN-ok/alhálózatok hozzáadása](./create-vlan-subnet.md). Adjon hozzá egy VLAN/részhalmazt ehhez a privát felhőhöz.

Válasszon VLAN-t/alhálózatot a következő műveletekhez
* [Tűzfalszabály csatolása](./firewall.md). Csatoljon egy tűzfalat ehhez a privát felhőhöz.
* **Szerkesztés**
* **Törlés** (csak a felhasználó által definiált VLAN-ok/alhálózatok)

## <a name="private-cloud-activity"></a>Saját Felhőbeli tevékenység

Tekintse meg a következő információkat a kiválasztott privát felhőhöz.  A tevékenység adatai a kiválasztott privát felhő összes tevékenységének szűrt listája.  Ezen az oldalon legfeljebb 25 legutóbbi tevékenység látható.

* Legutóbbi riasztások
* Legutóbbi események
* Legutóbbi feladatok
* Legutóbbi naplózás

![Saját felhő – tevékenység](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Felhőalapú állványok

A felhőalapú állványok a saját felhő építőelemei. Mindegyik rack kapacitási egységet biztosít. A CloudSimple automatikusan konfigurálja a Felhőbeli állványokat a privát felhő létrehozásakor vagy bővítésekor megadott beállítások alapján.  Megtekintheti a felhőalapú állványok teljes listáját, beleértve az egyesekhez rendelt saját felhőt is.

![Privát felhő – felhőalapú állványok](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere-felügyeleti hálózat

A saját felhőben jelenleg konfigurált VMware felügyeleti erőforrások és virtuális gépek listája. Az információk közé tartozik a szoftver verziója, a teljes tartománynév (FQDN) és az erőforrások IP-címe.

![Saját felhőalapú vSphere felügyeleti hálózat](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Következő lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)

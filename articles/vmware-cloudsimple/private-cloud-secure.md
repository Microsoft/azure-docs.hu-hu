---
title: Azure VMware-megoldások CloudSimple – biztonságos privát felhő
description: Ismerteti, hogyan védheti meg az Azure VMware-megoldásokat a CloudSimple Private Cloud használatával
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e70745cd6e2f6a2a13581052f65e014bd0d0481
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899167"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Saját felhőalapú környezet biztonságossá tétele

Szerepköralapú hozzáférés-vezérlés (RBAC) definiálása a CloudSimple szolgáltatáshoz, a CloudSimple-portálhoz és az Azure-beli privát felhőhöz.  A vCenter eléréséhez szükséges felhasználók, csoportok és szerepkörök a VMware SSO használatával vannak megadva.  

## <a name="azure-rbac-for-cloudsimple-service"></a>Azure-RBAC a CloudSimple szolgáltatáshoz

A CloudSimple szolgáltatás létrehozásához **tulajdonosi** vagy **közreműködői** szerepkörre van szükség az Azure-előfizetésben.  Alapértelmezés szerint minden tulajdonos és közreműködő létrehoz egy CloudSimple szolgáltatást, és hozzáfér a CloudSimple-portálhoz a privát felhők létrehozásához és kezeléséhez.  Régiónként csak egy CloudSimple szolgáltatás hozható létre.  Az adott rendszergazdák hozzáférésének korlátozásához kövesse az alábbi eljárást.

1. CloudSimple-szolgáltatás létrehozása új **erőforráscsoport** Azure Portal
2. Itt adhatja meg az erőforráscsoport Azure-RBAC.
3. Csomópontok megvásárlása és ugyanazt az erőforráscsoportot használja, mint a CloudSimple szolgáltatás

Csak azok a felhasználók láthatják a CloudSimple szolgáltatást, akik **tulajdonosi** vagy **közreműködői** jogosultsággal rendelkeznek az erőforráscsoport számára, és elindítják a CloudSimple-portált.

További információ: [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC a saját felhőalapú vCenter

A rendszer létrehoz egy alapértelmezett felhasználót `CloudOwner@cloudsimple.local` a VCENTER SSO-tartományban a saját felhő létrehozásakor.  A CloudOwner-felhasználó jogosultságokkal rendelkezik a vCenter kezeléséhez. A rendszer további identitási forrásokat ad hozzá a vCenter SSO-hoz a különböző felhasználókhoz való hozzáférés biztosítása érdekében.  Az előre definiált szerepkörök és csoportok a vCenter vannak beállítva, amelyek további felhasználók hozzáadására használhatók.

### <a name="add-new-users-to-vcenter"></a>Új felhasználók hozzáadása a vCenter

1. A **CloudOwner \@ cloudsimple. local** felhasználóhoz tartozó [jogosultságok kiterjesztését](escalate-private-cloud-privileges.md) a privát felhőben.
2. Jelentkezzen be a vCenter **CloudOwner \@ cloudsimple. local** használatával
3. [VCenter egyetlen Sign-On felhasználó hozzáadása](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Felhasználók hozzáadása az [egyszeri bejelentkezési csoportok vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Az előre definiált szerepkörökkel és csoportokkal kapcsolatos további információkért lásd: [CloudSimple Private Cloud Permission Model of VMware vCenter](learn-private-cloud-permissions.md) cikk.

### <a name="add-new-identity-sources"></a>Új Identity sources hozzáadása

További identitás-szolgáltatókat is hozzáadhat a privát felhő vCenter SSO-tartományához.  Az identitás-szolgáltatók hitelesítési és vCenter SSO-csoportokat biztosítanak a felhasználók számára.

* A [Active Directory identitás-szolgáltatóként használhatja](set-vcenter-identity.md) a saját Felhőbeli vCenter.
* Az [Azure ad használata identitás-szolgáltatóként](azure-ad.md) a saját felhőalapú vCenter

1. A **CloudOwner \@ cloudsimple. local** felhasználóhoz tartozó [jogosultságok kiterjesztését](escalate-private-cloud-privileges.md) a privát felhőben.
2. Jelentkezzen be a vCenter **CloudOwner \@ cloudsimple. local** használatával
3. Adja hozzá a felhasználókat az identitás-szolgáltatótól az [egyszeri bejelentkezési csoportok vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Biztonságos hálózat a saját felhőalapú környezetében

A privát felhőalapú környezet hálózati biztonságát a hálózati hozzáférés biztonságossá tétele és az erőforrások közötti hálózati forgalom szabályozása vezérli.

### <a name="access-to-private-cloud-resources"></a>Hozzáférés a saját felhőalapú erőforrásokhoz

A saját felhőalapú vCenter és erőforrásaihoz való hozzáférés biztonságos hálózati kapcsolaton keresztül történik:

* **[ExpressRoute-kapcsolatok](on-premises-connection.md)**. A ExpressRoute biztonságos, nagy sávszélességű, kis késleltetésű kapcsolattal rendelkezik a helyszíni környezettől.  A kapcsolat lehetővé teszi, hogy a helyszíni szolgáltatások, hálózatok és felhasználók hozzáférjenek a saját Felhőbeli vCenter.
* **[Helyek közötti VPN-átjáró](vpn-gateway.md)**. A helyek közötti VPN a biztonságos alagúton keresztül hozzáférést biztosít a saját felhőalapú erőforrásaihoz a helyszínen.  Megadhatja, hogy mely helyszíni hálózatok küldhetnek és fogadhatnak hálózati forgalmat a saját felhőbe.
* **[Pont – hely típusú VPN-átjáró](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Pont – hely VPN-kapcsolat használata a saját felhőalapú vCenter való gyors távoli hozzáféréshez.

### <a name="control-network-traffic-in-private-cloud"></a>A hálózati forgalom vezérlése a privát felhőben

A tűzfal táblái és szabályai vezérlik a hálózati forgalmat a privát felhőben.  A tűzfal tábla lehetővé teszi a hálózati forgalom vezérlését a forrásoldali hálózat vagy az IP-cím, valamint a célként megadott hálózat vagy IP-cím között a táblázatban meghatározott szabályok kombinációja alapján.

1. Hozzon létre egy [Tűzfalszabály-táblázatot](firewall.md#add-a-new-firewall-table).
2. [Szabályok hozzáadása](firewall.md#create-a-firewall-rule) a tűzfal táblához.
3. [Tűzfal-tábla csatolása VLAN-hoz vagy alhálózathoz](firewall.md#attach-vlans-subnet).

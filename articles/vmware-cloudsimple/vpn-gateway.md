---
title: Azure VMware-megoldás CloudSimple használatával – VPN-átjáró beállítása
description: Útmutató pont – hely típusú VPN-átjáró és helyek közötti VPN-átjáró beállításához, valamint kapcsolatok létrehozása a helyszíni hálózat és a CloudSimple privát felhője között
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 78f78bfbf885cbae7708a75cb54ce2d41e7b526c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899099"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>VPN-átjárók beállítása a CloudSimple hálózaton

A VPN-átjárók lehetővé teszik, hogy a helyszíni hálózatról és egy ügyfélszámítógépről távolról kapcsolódjon a CloudSimple hálózathoz. A helyszíni hálózat és a CloudSimple közötti VPN-kapcsolat hozzáférést biztosít a vCenter és a munkaterhelésekhez a saját felhőben. A CloudSimple a helyek közötti VPN-és pont – hely VPN-átjárókat is támogatja.

## <a name="vpn-gateway-types"></a>VPN-átjárók típusai

* A **helyek közötti VPN-** kapcsolat lehetővé teszi a saját Felhőbeli számítási feladatok beállítását a helyszíni szolgáltatások eléréséhez. A helyszíni Active Directory identitás forrásaként is használhatja a saját felhőalapú vCenter való hitelesítéshez.  Jelenleg csak a **házirend-alapú VPN-** típusok támogatottak.
* A **pont – hely VPN** -kapcsolat a legegyszerűbb módja a saját felhőhöz való csatlakozásnak a számítógépről. A pont – hely VPN-kapcsolat használatával távolról csatlakozhat a privát felhőhöz. Az ügyfelek pont – hely típusú VPN-kapcsolatra való telepítésével kapcsolatos információkért lásd: [VPN-kapcsolat konfigurálása a saját felhőhöz](set-up-vpn.md).

Egy régióban létrehozhat egy pont – hely típusú VPN-átjárót és egy helyek közötti VPN-átjárót.

## <a name="automatic-addition-of-vlansubnets"></a>VLAN/alhálózatok automatikus hozzáadása

A CloudSimple VPN-átjárók a VLAN/alhálózatok VPN-átjárók számára történő hozzáadására vonatkozó házirendeket biztosítanak.  A szabályzatok lehetővé teszik különböző szabályok megadását a felügyeleti VLAN/alhálózatok és a felhasználó által megadott VLAN/alhálózatok számára.  A felügyeleti VLAN/alhálózatokra vonatkozó szabályok minden újonnan létrehozott privát felhőre érvényesek.  A felhasználó által definiált VLAN-ok vagy alhálózatok szabályai lehetővé teszik, hogy új VLAN-okat/alhálózatokat hozzon létre a meglévő vagy új privát felhők számára egy helyek közötti VPN-átjáróhoz, minden kapcsolathoz meg kell határoznia a szabályzatot.

A VLAN-ok/alhálózatok VPN-átjárók számára való hozzáadására vonatkozó házirendek a helyek közötti VPN-és pont – hely típusú VPN-átjárók esetében is érvényesek.

## <a name="automatic-addition-of-users"></a>Felhasználók automatikus hozzáadása

Egy pont – hely típusú VPN-átjáró lehetővé teszi az új felhasználók automatikus kiegészítési szabályzatának definiálását. Alapértelmezés szerint az előfizetés összes tulajdonosa és közreműködői hozzáférhetnek a CloudSimple-portálhoz.  A felhasználók csak akkor jönnek létre, ha az CloudSimple-portált első alkalommal indította el.  A szabályok **automatikus hozzáadása** lehetőség kiválasztásával bármely új felhasználó hozzáférhet a CloudSimple hálózathoz pont – hely típusú VPN-kapcsolat használatával.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Helyek közötti VPN-átjáró beállítása

1. [Nyissa meg a CloudSimple-portált](access-cloudsimple-portal.md) , és válassza a **hálózat** lehetőséget.
2. Válassza a **VPN Gateway** lehetőséget.
3. Kattintson az **új VPN Gateway** elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **átjáró konfigurálása** lapon írja be a következő beállításokat, majd kattintson a **tovább** gombra.

    * Válassza a **helyek közötti VPN** lehetőséget átjáró típusaként.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol a CloudSimple szolgáltatás telepítve van.
    * Opcionálisan engedélyezze a magas rendelkezésre állást.

    ![Helyek közötti VPN-átjáró létrehozása](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > A magas rendelkezésre állás engedélyezéséhez a helyszíni VPN-eszköznek két IP-címhez való csatlakozást kell támogatnia. Ez a beállítás nem tiltható le, ha a VPN-átjáró telepítve van.

5. Hozza létre az első csatlakozni a helyszíni hálózatból, és kattintson a **tovább** gombra.

    * Adjon meg egy nevet a kapcsolódás azonosításához.
    * A társ IP esetében adja meg a helyszíni VPN-átjáró nyilvános IP-címét.
    * Adja meg a helyszíni VPN-átjáró társ-azonosítóját.  A társ-azonosító általában a helyszíni VPN-átjáró nyilvános IP-címe.  Ha konfigurált egy adott azonosítót az átjárón, adja meg az azonosítót.
    * Másolja a helyi VPN-átjáróval létesített kapcsolathoz használni kívánt megosztott kulcsot.  Ha módosítani szeretné az alapértelmezett megosztott kulcsot, és megad egy újat, kattintson a Szerkesztés ikonra.
    * Helyszíni **előtagok** esetén adja meg a helyszíni CIDR előtagokat, amelyek a CloudSimple-hálózathoz fognak hozzáférni.  A kapcsolat létrehozásakor több CIDR-előtagot is hozzáadhat.

    ![Helyek közötti VPN Gateway-kapcsolat létrehozása](media/create-vpn-gateway-s2s-connection.png)

6. Engedélyezze a saját felhőalapú hálózatán a helyszíni hálózatról elérhető VLAN/alhálózatokat, majd kattintson a **tovább** gombra.

    * Felügyeleti VLAN/alhálózat hozzáadásához engedélyezze a **privát felhők felügyeleti VLAN-ok/alhálózatok hozzáadását**.  A vMotion és vSAN alhálózatok esetében felügyeleti alhálózatra van szükség.
    * VMotion alhálózatok hozzáadásához engedélyezze a **vMotion-hálózat hozzáadása lehetőséget a privát felhőknél**.
    * VSAN-alhálózatok hozzáadásához engedélyezze **a privát felhők hozzáadására vonatkozó vSAN-alhálózatot**.
    * Válassza ki vagy törölje a megadott VLAN-okat.

    ![Kapcsolat létrehozása](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Tekintse át a beállításokat, majd kattintson a **Küldés** gombra.

    ![Helyek közötti VPN-átjáró áttekintése és létrehozása](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Pont – hely típusú VPN-átjáró létrehozása

1. [Nyissa meg a CloudSimple-portált](access-cloudsimple-portal.md) , és válassza a **hálózat** lehetőséget.
2. Válassza a **VPN Gateway** lehetőséget.
3. Kattintson az **új VPN Gateway** elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **átjáró konfigurálása** lapon írja be a következő beállításokat, majd kattintson a **tovább** gombra.

    * Válassza a **pont – hely típusú VPN** lehetőséget átjáró típusaként.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol a CloudSimple szolgáltatás telepítve van.
    * A pont – hely átjáróhoz tartozó ügyféloldali alhálózat megadása.  A DHCP-címeket a rendszer a csatlakozáskor az ügyfél alhálózatában adja meg.

5. A **kapcsolatok/felhasználók** területen válassza a következő beállításokat, majd kattintson a **tovább** gombra.

    * Ha automatikusan engedélyezni szeretné az összes jelenlegi és jövőbeli felhasználó számára a privát felhő elérését a pont – hely átjárón keresztül, válassza az **összes felhasználó automatikus hozzáadása** lehetőséget. Ha bejelöli a beállítást, a rendszer a felhasználói listán szereplő összes felhasználót automatikusan kijelöli. Az automatikus beállítást felülbírálhatja az egyes felhasználók törlésével a listában.
    * Az egyes felhasználók kiválasztásához kattintson a felhasználók listájában található jelölőnégyzetekre.

6. A VLAN-ok/alhálózatok szakaszban megadhatja az átjáróhoz és a kapcsolatokhoz tartozó felügyeleti és felhasználói VLAN-okat/alhálózatokat.

    * Az **automatikus hozzáadási** beállítások az átjáróhoz tartozó globális házirendet határozzák meg. A beállítások az aktuális átjáróra érvényesek. A beállítások felülbírálása a **Select** területen lehetséges.
    * Válassza a **felügyeleti VLAN-ok vagy a privát felhők alhálózatok hozzáadása** lehetőséget. 
    * A felhasználó által definiált VLAN-ok/alhálózatok hozzáadásához kattintson a  **felhasználó által definiált VLAN-ok/alhálózatok hozzáadása** lehetőségre.
    * A beállítások **kiválasztása** felülbírálja a globális beállításokat az **automatikus Hozzáadás** területen.

7. A beállítások áttekintéséhez kattintson a **tovább** gombra. A módosítások elvégzéséhez kattintson a Szerkesztés ikonra.
8. A VPN-átjáró létrehozásához kattintson a **Létrehozás** gombra.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Az ügyfél alhálózata és a pont – hely típusú VPN-átjáró protokolljai

A pont – hely VPN-átjáró lehetővé teszi a TCP-és UDP-kapcsolatokat.  A TCP-vagy UDP-konfiguráció kiválasztásával válassza ki a számítógépről való csatlakozáskor használandó protokollt.

A konfigurált ügyfél-alhálózat TCP-és UDP-ügyfelek esetén is használatos.  A CIDR előtag két alhálózatra van osztva, egyet a TCP és egy UDP-ügyfél számára. Válassza ki az előtag maszkját azon VPN-felhasználók száma alapján, akik egyidejűleg csatlakoznak.  

A következő táblázat az előtag-maszk egyidejű ügyfélkapcsolatok számát sorolja fel.

| Előtag maszkja | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Egyidejű TCP-kapcsolatok száma | 124 | 60 | 28 | 12 | 4 |
| Egyidejű UDP-kapcsolatok száma | 124 | 60 | 28 | 12 | 4 |

Ha pont – hely típusú VPN-kapcsolattal szeretne csatlakozni, tekintse meg a [Csatlakozás a CloudSimple pont – hely típusú VPN használatával](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)című témakört.

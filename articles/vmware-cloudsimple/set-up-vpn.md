---
title: Azure VMware-megoldás CloudSimple – VPN konfigurálása a helyszíni és a privát felhő között
description: Útmutató helyek közötti vagy pont – hely típusú VPN-kapcsolat konfigurálásához a helyszíni hálózat és a CloudSimple privát felhője között
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b7a119b538ee07b961011bfa98d748f92cc76f0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899218"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>VPN-kapcsolat konfigurálása saját CloudSimple-felhőhöz

A VPN-átjárók lehetővé teszik, hogy a helyszíni hálózatról és egy ügyfélszámítógépről távolról kapcsolódjon a CloudSimple hálózathoz.  Ebből a cikkből megtudhatja, hogyan állíthatja be a VPN-átjárókat a CloudSimple-portálról.  A helyszíni hálózat és a CloudSimple közötti VPN-kapcsolat hozzáférést biztosít a vCenter és a munkaterhelésekhez a saját felhőben. A CloudSimple a pont – hely VPN-t és a helyek közötti VPN-átjárókat is támogatja.

## <a name="vpn-gateway-types"></a>VPN-átjárók típusai

* A **pont – hely VPN** -kapcsolat a legegyszerűbb módja a saját felhőhöz való csatlakozásnak a számítógépről. A pont – hely VPN-kapcsolat használatával távolról csatlakozhat a privát felhőhöz.
* A **helyek közötti VPN-** kapcsolat lehetővé teszi a saját Felhőbeli számítási feladatok beállítását a helyszíni szolgáltatások eléréséhez. A helyszíni Active Directory identitás forrásaként is használhatja a saját felhőalapú vCenter való hitelesítéshez.  Jelenleg a **házirend-alapú VPN-** típus támogatott.

Egy régióban létrehozhat egy helyek közötti VPN-átjárót és egy pont – hely típusú VPN-átjárót.

## <a name="point-to-site-vpn"></a>Pont – hely típusú VPN

Pont – hely típusú VPN-átjáró létrehozásával kapcsolatban lásd: [pont – hely típusú VPN-átjáró létrehozása](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Csatlakozás a CloudSimple pont – hely típusú VPN használatával

A CloudSimple a számítógépről való csatlakozáshoz VPN-ügyfél szükséges.  Töltse le a Windowshoz készült [OpenVPN-ügyfelet](https://openvpn.net/community-downloads/) , illetve a MacOS és az OS X [viszkozitását](https://www.sparklabs.com/viscosity/download/) .

1. Indítsa el a CloudSimple portált, és válassza a **hálózat** lehetőséget.
2. Válassza a **VPN Gateway** lehetőséget.
3. A VPN-átjárók listájában kattintson a pont – hely VPN-átjáróra.
4. Válassza a **Felhasználók** lehetőséget.
5. Kattintson a **saját VPN-konfiguráció letöltése** elemre.

    ![VPN-konfiguráció letöltése](media/download-p2s-vpn-configuration.png)

6. A konfiguráció importálása a VPN-ügyfélen

    * Útmutató a [konfiguráció importálásához a Windows-ügyfélen](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Útmutató a [konfiguráció importálásához MacOS vagy OS X rendszeren](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Csatlakozás a CloudSimple VPN-átjáróhoz.

Az alábbi példa azt mutatja be, hogyan importálhatók a kapcsolatok a **viszkozitási ügyféllel**.

#### <a name="import-connection-on-viscosity-client"></a>Importálási kapcsolatok a viszkozitási ügyfélen

1. Bontsa ki a VPN-konfiguráció tartalmát a letöltött. zip fájlból.

2. Nyissa meg a viszkozitást a számítógépén.

3. Kattintson az **+** ikonra, és válassza a **kapcsolatok importálása**  >  **fájlból** lehetőséget.

    ![VPN-konfiguráció importálása fájlból](media/import-p2s-vpn-config.png)

4. Válassza ki a használni kívánt protokollhoz tartozó OpenVPN konfigurációs fájlt (. ovpn), majd kattintson a **Megnyitás** gombra.

    ![Képernyőkép a kiválasztható OpenVPN konfigurációs fájlokra.](media/import-p2s-vpn-config-choose-ovpn.png)

A kapcsolatok most a viszkozitás menüben jelennek meg.

#### <a name="connect-to-the-vpn"></a>Kapcsolódás a VPN-hez

Ha a VPN-kapcsolathoz a viszkozitási OpenVPN-ügyféllel szeretne csatlakozni, válassza ki a kapcsolatot a menüből. A menü ikonja azt jelzi, hogy a kapcsolódás létrejött.

![A CloudSimple VPN-kapcsolat állapotát bemutató képernyőkép.](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Csatlakozás több privát Felhőkhöz

A pont – hely típusú VPN-kapcsolat feloldja a létrehozott első saját felhő DNS-nevét. Ha más privát Felhőkhöz szeretne hozzáférni, frissítenie kell a DNS-kiszolgálót a VPN-ügyfélen.

1. Indítsa el a [CloudSimple portált](access-cloudsimple-portal.md).

2. Navigáljon az **erőforrások**  >  **privát felhők** elemre, és válassza ki azt a privát felhőt, amelyhez csatlakozni szeretne.

3. A privát felhő **Összegzés** lapján másolja a saját FELHŐBELI DNS-kiszolgáló IP-címét az **alapszintű adatok** területen.

    ![Privát Felhőbeli DNS-kiszolgálók](media/private-cloud-dns-server.png)

4. Kattintson a jobb gombbal a számítógép rendszertálcán található viszkozitás ikonra, és válassza a **Beállítások** lehetőséget.

    ![VPN](media/vis00.png)

5. Válassza ki a CloudSimple VPN-kapcsolat lehetőséget.

    ![VPN-kapcsolat](media/viscosity-client.png)

6. A kapcsolódási tulajdonságok módosításához kattintson a **Szerkesztés** gombra.

    ![VPN-kapcsolat szerkesztése](media/viscosity-edit-connection.png)

7. Kattintson a **hálózatkezelés** lapra, és adja meg a saját FELHŐbeli DNS-kiszolgáló IP-címét vesszővel vagy szóközzel elválasztva, valamint a tartományt ```cloudsimple.io``` .  Válassza **a VPN-kiszolgáló által eljuttatott DNS-beállítások mellőzése** lehetőséget.

    ![VPN-hálózatkezelés](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Az első saját felhőhöz való csatlakozáshoz távolítsa el ezeket a beállításokat, és kapcsolódjon a VPN-kiszolgálóhoz.

## <a name="site-to-site-vpn"></a>Helyek közötti VPN

Helyek közötti VPN-átjáró létrehozásához tekintse meg a [helyek közötti VPN-átjáró létrehozása](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)című témakört.  A helyszíni hálózat és a privát felhő közötti helyek közötti VPN-kapcsolat biztosítja ezeket az előnyöket.  

* A saját felhőalapú vCenter kisegítő lehetőségei a helyszíni hálózat bármely munkaállomásáról
* Helyszíni Active Directory használata vCenter-identitás forrásaként
* A virtuálisgép-sablonok, az ISO-eszközök és más fájlok kényelmes átvitele a helyszíni erőforrásokból a saját Felhőbeli vCenter
* A helyszíni hálózatról a saját felhőben futó munkaterhelések hozzáférhetősége

Ha magas rendelkezésre állású módban szeretné beállítani a helyszíni VPN-átjárót, tekintse meg a [magas rendelkezésre állású VPN-kapcsolat konfigurálása](high-availability-vpn-connection.md)című témakört.

> [!IMPORTANT]
>    1. Állítsa be a TCP MSS-befogást a 1200-es méretre a VPN-eszközön. Ha a VPN-eszközök nem támogatják a MSS-befogást, akkor a bújtatási felületen lévő MTU-t 1240 bájtra is állíthatja.
> 2. Miután beállította a helyek közötti VPN-t, továbbítsa a *. cloudsimple.io DNS-kérelmeit a privát Felhőbeli DNS-kiszolgálókra.  Kövesse a helyszíni [DNS-telepítő](on-premises-dns-setup.md)utasításait.

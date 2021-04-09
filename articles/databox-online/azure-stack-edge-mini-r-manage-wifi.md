---
title: Azure Stack Edge mini R Wi-Fi Management
description: Ismerteti, hogyan kezelheti a Wi-Fi az Azure Stack Edge-Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043767"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>A helyi webes felhasználói felület használata a vezeték nélküli kapcsolat kezeléséhez az Azure Stack Edge mini R-vel

Ez a cikk azt ismerteti, hogyan kezelhetők a vezeték nélküli hálózati kapcsolatok a Azure Stack Edge mini R-eszközön. Wi-Fi profilok hozzáadásához, csatlakoztatásához és törléséhez a helyi webes felhasználói felületet használhatja a Azure Stack Edge mini R-eszközön.

## <a name="about-wi-fi"></a>Tudnivalók Wi-Fi

Az Azure Stack Edge mini R-eszköz a hálózaton vagy vezeték nélküli hálózaton keresztül is működhet. Az eszközön van egy Wi-Fi port, amelyet engedélyezni kell, hogy az eszköz vezeték nélküli hálózathoz kapcsolódjon. 

Az eszköz öt portot, 1. PORTOT és egy ötödik Wi-Fi portot tartalmaz. Itt látható a mini R-eszköz hátsó síkja egy vezeték nélküli hálózathoz való csatlakozáskor.

![Wi-Fi kábelezése](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Hozzáadás, kapcsolódás Wi-Fi profilhoz

Az eszköz helyi felhasználói felületén hajtsa végre az alábbi lépéseket Wi-Fi profil hozzáadásához és a hozzá való kapcsolódáshoz.

1. Az eszköz helyi webes FELÜLETén nyissa meg az **első lépések** lapot. A **hálózat** csempén válassza a **Konfigurálás** lehetőséget.  
    
    A fizikai eszközön öt hálózati adapter található. A PORT 1 és PORT 2 jelzésű portok 1 Gbps hálózati adapterek. A 3. és a 4. port mind a 10 GB/s hálózati adapter. Az ötödik port a Wi-Fi port. 

    [![Helyi webes felhasználói felület "hálózati beállítások" 1. oldal](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Válassza ki a Wi-Fi portot, és konfigurálja a portbeállítások beállításait. 
    
    > [!IMPORTANT]
    > Javasoljuk, hogy statikus IP-címet állítson be a Wi-Fi porthoz.  

    ![Helyi webes felhasználói felület "hálózati beállítások" 2. lapja](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    A **hálózati** oldal a Wi-Fi portbeállítások alkalmazása után frissül.

    ![Helyi webes felhasználói felület "hálózati beállítások" 3. lapja](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Válassza a **Wi-Fi profil hozzáadása** lehetőséget, és töltse fel Wi-Fi-profilját. 

    ![Helyi webes felhasználói felület "Port WiFi hálózati beállításai" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    A vezeték nélküli hálózati profil a vezeték nélküli hálózathoz való kapcsolódáshoz szükséges SSID-t (hálózati nevet), jelszót és biztonsági információt tartalmazza. A környezet Wi-Fi profilját a hálózati rendszergazdától kérheti le.

    További információ a Wi-Fi profilok előkészítéséről: [Wi-Fi profilok használata Azure stack Edge mini R-eszközökkel](azure-stack-edge-mini-r-use-wifi-profiles.md).

    ![Helyi webes felhasználói felület "Port WiFi hálózati beállításai" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    A profil hozzáadása után az Wi-Fi profilok listája frissül, hogy tükrözze az új profilt. A profilnak le kell mutatnia a **kapcsolat állapotát** **leválasztva**. 

    ![Helyi webes felhasználói felület "Port WiFi hálózati beállításai" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. A vezeték nélküli hálózati profil sikeres betöltését követően kapcsolódjon ehhez a profilhoz. Válassza **a kapcsolódás Wi-Fi profilhoz** lehetőséget. 

    ![Helyi webes felhasználói felület "Port Wi-Fi hálózati beállítások" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Válassza ki az előző lépésben hozzáadott Wi-Fi profilt, majd válassza az **alkalmaz** lehetőséget. 

    ![Helyi webes felhasználói felület "Port Wi-Fi hálózati beállítások" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    A **kapcsolat állapotának** frissítenie kell a **Connected** értékre. A jel erőssége a jel minőségének jelzésére frissül. 

    ![Helyi webes felhasználói felület "Port Wi-Fi hálózati beállítások" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Nagy mennyiségű adatok átviteléhez ajánlott vezetékes kapcsolat használata a vezeték nélküli hálózat helyett. 


## <a name="download-wi-fi-profile"></a>Wi-Fi-profil letöltése

A vezeték nélküli hálózati kapcsolathoz használt Wi-Fi-profilt letöltheti.

1. Az eszköz helyi webes FELÜLETén lépjen a **Configuration > Network** elemre. 

2. A Wi-Fi profil beállításai területen válassza a **profil letöltése** lehetőséget. Ehhez le kell töltenie a jelenleg használt Wi-Fi profilt.


## <a name="delete-wi-fi-profile"></a>Wi-Fi profil törlése

A vezeték nélküli hálózati kapcsolathoz használt Wi-Fi-profilt törölheti.


1. Az eszköz helyi webes FELÜLETén lépjen a **Configuration > Network** elemre. 

2. A Wi-Fi profil beállításai területen válassza a **Wi-Fi profil törlése** lehetőséget.

3. A **Wi-Fi-profil törlése** panelen válassza ki a törölni kívánt profilt. Kattintson az **Alkalmaz** gombra.


## <a name="configure-cisco-wi-fi-profile"></a>Cisco Wi-Fi-profil konfigurálása

Íme néhány útmutató a Cisco vezeték nélküli vezérlő és hozzáférési pont eszközön való kezeléséhez és konfigurálásához. 

### <a name="dhcp-bridging-mode"></a>DHCP-áthidaló mód

Ha Cisco vezeték nélküli vezérlőt szeretne használni az eszközhöz, engedélyeznie kell a Dynamic Host Configuration Protocol (DHCP) áthidaló üzemmódját a vezeték nélküli LAN-vezérlőn (WLC).

További információ: [DHCP-áthidaló mód](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>A konfiguráció áthidaló példája

A DHCP-áthidaló funkciónak a vezérlőn való engedélyezéséhez le kell tiltania a DHCP-proxy szolgáltatást a vezérlőn. A DHCP-áthidaló engedélyezése a parancssor használatával:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Ha a DHCP-kiszolgáló nem létezik ugyanazon a 2. rétegbeli (L2) hálózaton, mint az ügyfél, akkor a szórást az ügyfél átjárójának DHCP-kiszolgálójára kell továbbítani az IP-segítő használatával. Ez a konfiguráció mintája:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

A DHCP-áthidaló szolgáltatás globális beállítás, ezért a vezérlőn belüli összes DHCP-tranzakcióra hatással van. A vezetékes infrastruktúrában IP-segítő utasításokat kell hozzáadnia a vezérlő összes szükséges virtuális helyi hálózatához (VLAN).

### <a name="enable-the-passive-client-for-wlan"></a>A passzív ügyfél engedélyezése a WLAN-hoz

A passzív ügyféloldali szolgáltatás engedélyezése a vezeték nélküli helyi hálózathoz (WLAN) a Cisco vezeték nélküli vezérlőn:

* A WLAN-hoz társított felületnek rendelkeznie kell egy VLAN-címkézéssel.
* A csoportos küldési VLAN-nak engedélyezve kell lennie a WLAN-hoz.
* A GARP-továbbítást engedélyezni kell a WLC.

További információ: [csoportos küldéssel kapcsolatos VLAN-információk a csoportos küldés optimalizálásával kapcsolatban](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html).

### <a name="troubleshoot"></a>Hibaelhárítás

Ha az IP-címek kiosztásával kapcsolatos problémákat tapasztal Azure Stack Edge mini R-eszközön futó virtuális gépeken, a hálózati környezet fenti konfigurációs beállításait érvényesíteni kell.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [helyezheti üzembe Azure stack Edge mini R-eszközét](azure-stack-edge-mini-r-deploy-prep.md).

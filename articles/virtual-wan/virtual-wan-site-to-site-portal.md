---
title: 'Oktatóanyag: az Azure Virtual WAN használata helyek közötti kapcsolatok létrehozásához'
description: Az oktatóanyag az Azure-ba irányuló helyek közötti VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 5628c5df7ff7176896731ff8a24b4b0c990720ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430668"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Oktatóanyag: Helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával

Az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv1 és IKEv2) VPN-kapcsolaton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Elosztó létrehozása
> * Hely létrehozása
> * Hely összekötése egy hubhoz
> * VPN-hely összekötése egy hubhoz
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * Konfigurációs fájl letöltése
> * A VPN-átjáró konfigurálása

> [!NOTE]
> Ha több hellyel rendelkezik, általában egy [Virtual WAN-partner](https://aka.ms/virtualwan) segítségével hozza létre ezt a konfigurációt. A konfigurációt azonban maga is létrehozhatja, ha elboldogul a hálózatkezeléssel, és tudja, hogyan kell konfigurálnia saját VPN-eszközeit.
>

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan.png)

## <a name="prerequisites"></a>Előfeltételek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtuális WAN létrehozása

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Elosztó létrehozása

A hub olyan virtuális hálózat, amely átjárókat tartalmazhat a helyek közötti, a ExpressRoute vagy a pont – hely funkciókhoz. A központ létrehozását követően a szolgáltatás akkor is díjköteles, ha nem csatol hozzá egyetlen webhelyet sem. 30 percet vesz igénybe, hogy létrehozza a két hálózat közötti pont-pont típusú VPN-átjárót a virtuális központban.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Hely létrehozása

Ebben a szakaszban a helyet hozza létre. A helyek megfelelnek a fizikai helyeknek. Tetszőleges számú helyet hozhat létre. Például ha New Yorkban, Londonban és Los Angelesben van egy-egy helyi irodája, három külön helyet hozzon létre. Ezek a helyek tartalmazzák a helyszíni VPN-eszközök végpontjait. Virtuális WAN-ban akár 1000-es helyet hozhat létre virtuális hubhoz. Ha több hubhoz is rendelkezett, minden egyes hubhoz létrehozhat 1000-et. Ha virtuális WAN-partner CPE-eszközzel rendelkezik, egyeztessen velük az Azure-ral való automatizálásáról. Az Automation általában egyszerű kattintásos élményt nyújt a nagyméretű fiókirodai információk Azure-ba való exportálásához, valamint a CPE és az Azure virtuális WAN VPN Gateway közötti kapcsolat beállításához. További információ: Automation- [útmutató az Azure-ról a CPE-partnerekre](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>A VPN-hely összekötése a hubhoz

Ebben a lépésben a VPN-helyet csatlakoztatja a hubhoz.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>A VNet összekötése a hubhoz

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>VPN-konfiguráció letöltése

A VPN-eszköz konfigurációjának használatával konfigurálhatja a helyszíni VPN-eszközöket.

1. A virtuális WAN lapján kattintson az **Áttekintés** elemre.
2. A **hub->VPNSite** lap tetején kattintson a **VPN-konfiguráció letöltése** elemre. Az Azure létrehoz egy Storage-fiókot a "Microsoft-Network-[Location]" erőforráscsoporthoz, ahol a hely a WAN helye. Miután a konfigurációt alkalmazta a VPN-eszközökre, törölheti ezt a tárfiókot.
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. Alkalmazza a konfigurációt a helyszíni VPN-eszközre.

### <a name="about-the-vpn-device-configuration-file"></a>Tudnivalók a VPN-eszköz konfigurációs fájljáról

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections –** Ez a szakasz a következő beállításokkal kapcsolatos információkat tartalmazza:

    * A virtuális központ (ok) VNet **címe** .<br>Példa:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **A hubhoz** csatlakozó virtuális hálózatok.<br>Példa:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * A virtuális hálózat VPN-átjárójának **IP-címei** Mivel a átjáróban minden egyes csatlakoztatása két alagútból áll az aktív-aktív konfigurációban, a fájlban felsorolt IP-címeket is látni fogja. Ebben a példában mindegyik helyhez az „Instance0” és az „Instance1” van feltüntetve.<br>Példa:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Átjáróban-kapcsolat konfigurációs részletei** , például BGP, előmegosztott kulcs stb. A PSK a automatikusan létrehozott előmegosztott kulcs. A kapcsolatot az egyéni PSK Áttekintés lapján bármikor szerkesztheti.
  
### <a name="example-device-configuration-file"></a>Eszközkonfigurációs példafájl

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>VPN-eszköz konfigurálása

>[!NOTE]
> Amennyiben Virtual WAN-partnermegoldást használ, a VPN-eszköz konfigurációja automatikusan végbemegy. Az eszközvezérlő megkapja a konfigurációs fájlt az Azure-ból, és alkalmazza azt az eszközre az Azure-kapcsolat beállításához. Ez azt jelenti, hogy a VPN-eszköz manuális konfigurálásának menetét nem kell ismernie.
>

Amennyiben útmutatásra van szüksége az eszköz konfigurálásához, használhatja a [VPN-eszközkonfigurációs szkriptek lapon](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) található utasításokat az alábbi kikötésekkel:

* A VPN-eszközök lapon található utasítások nem a Virtual WAN-hoz íródtak, a Virtual WAN a konfigurációs fájlban lévő értékeivel manuálisan konfigurálhatja a VPN-eszközt. 
* A VPN Gatewayre vonatkozó letölthető eszközkonfigurációs szkriptek a Virtual WAN esetében nem működnek, mivel a konfiguráció eltér.
* Az új virtuális WAN A IKEv1 és a IKEv2 is támogatja.
* A Virtual WAN a házirend-alapú és az útválasztó-alapú VPN-eszközöket és az eszközökre vonatkozó utasításokat is használhatja.

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>A VPN-átjáró konfigurálása

A VPN-átjáró beállításait bármikor megtekintheti és konfigurálhatja a **Megtekintés/konfigurálás** lehetőség kiválasztásával.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="Képernyőfelvétel: a &quot;VPN (helyek közötti)&quot; oldal, amely a &quot;View/configure&quot; műveletre mutató nyilat mutat." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

A **VPN Gateway szerkesztése** lapon a következő beállításokat tekintheti meg:

* VPN Gateway nyilvános IP-cím (az Azure által hozzárendelt)
* VPN Gateway magánhálózati IP-cím (az Azure által hozzárendelt)
* Alapértelmezett BGP IP-cím VPN Gateway (az Azure által hozzárendelt)
* Egyéni BGP IP-cím konfigurációs beállítása: Ez a mező az APIPA (automatikus magánhálózati IP-címzés) számára van fenntartva. Az Azure támogatja a BGP IP-címet a 169.254.21. * és a 169.254.22. * tartományokban. Az Azure elfogadja a BGP-kapcsolatokat ezekben a tartományokban, de az alapértelmezett BGP-IP-címmel fogja tárcsázni a kapcsolatot.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="Konfiguráció megtekintése" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, törölje őket. A virtuális WAN-erőforrások némelyikét bizonyos sorrendben törölni kell a függőségek miatt. A törlés körülbelül 30 percet vesz igénybe.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

A virtuális WAN-ról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> * [Virtuális WAN – GYIK](virtual-wan-faq.md)

---
title: 'Azure VPN Gateway: kapcsolatok VPN-eszközeinek ismertetése'
description: Ez a cikk a létesítmények közötti S2S VPN Gateway-kapcsolatokhoz használt VPN-eszközöket és IPsec paramétereket ismerteti. A konfigurációs utasítások és minták a megfelelő hivatkozásokra kattintva érhetők el.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 4c6bd62e96d85305036626a8672c39ff1b9f6b26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201093"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Információk a helyek közötti VPN Gateway-kapcsolatok VPN-eszközeinek IPsec/IKE-paramétereiről

Létesítmények közötti, VPN-átjárót használó S2S VPN-kapcsolat konfigurálásához VPN-eszközre van szükség. A helyek közötti kapcsolat segítségével hibrid megoldást hozhat létre, illetve biztonságossá teheti a kapcsolatot a helyszíni és a virtuális hálózatok között. Ez a cikk ellenőrzött VPN-eszközök listáját, valamint a VPN-átjárók IPsec-/IKE-paramétereinek listáját tartalmazza.

> [!IMPORTANT]
> Ha problémákat tapasztal a helyszíni VPN-eszközök és a VPN-átjárók közötti kapcsolatban, tekintse meg az [ismert eszközkompatibilitási problémákkal kapcsolatos szakaszt](#known).
>

### <a name="items-to-note-when-viewing-the-tables"></a>A táblák megtekintésekor figyelembe veendő elemek:

* Az Azure VPN Gateway esetében terminológiai változás történt. Csak a nevek változtak. A funkció nem változik.
  * Statikus útválasztás = Házirendalapú
  * Dinamikus útválasztás = Útvonalalapú
* A nagy teljesítményű (HighPerformance) és az útvonalalapú (RouteBased) VPN-átjárók specifikációi azonosak, hacsak a szöveg másként nem jelzi. Például az útvonalalapú VPN-átjárókkal kompatibilis, ellenőrzött VPN-eszközök a nagy teljesítményű VPN-átjárókkal is kompatibilisek lesznek.

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>Ellenőrzött VPN-eszközök és eszközkonfigurációs útmutatók

Eszközszállítói partnereinkkel különböző standard VPN-eszközöket ellenőriztünk. Az alábbi listában szereplő eszközcsaládokban megtalálható összes eszköz kompatibilis a VPN-átjárókkal. A konfigurálni kívánt VPN Gateway-megoldáshoz használt VPN-típusok (házirendalapú vagy útvonalalapú) megismeréséhez lásd: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#vpntype).

A VPN-eszköz konfigurálásának megkönnyítéséhez tekintse meg a megfelelő eszköz termékcsaládhoz tartozó hivatkozásokat. A konfigurációs utasításokra mutató hivatkozásokat képességeinkhez mérten biztosítjuk. A VPN-eszközök támogatásával kapcsolatban lépjen kapcsolatba az eszköze gyártójával.

|**Szállító**          |**Eszközcsalád**     |**Operációs rendszer minimális verziója** |**Házirendalapú konfigurációs utasítások** |**Útválasztó-alapú konfigurációs utasítások** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Nem kompatibilis  |[Konfigurációs útmutató](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |AR sorozatú VPN-útválasztók |AR-Series 5.4.7 +               | [Konfigurációs útmutató](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[Konfigurációs útmutató](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Arista | CloudEOS-útválasztó | vEOS 4.24.0 FX | (nincs tesztelve) | [Konfigurációs útmutató](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Barracuda CloudGen tűzfal |Házirendalapú: 5.4.3<br>Útvonalalapú: 6.2.0 |[Konfigurációs útmutató](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[Konfigurációs útmutató](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Ellenőrzőpont |Biztonsági átjáró |R-80.10 |[Konfigurációs útmutató](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Konfigurációs útmutató](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Támogatott |[Konfigurációs útmutató *](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |Házirendalapú: IOS 15.1<br>Útvonalalapú: IOS 15.2 |Támogatott |Támogatott |
| Cisco | CSR | Útvonalalapú: IOS-XE 16,10 | (nincs tesztelve) | [Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |Házirendalapú: IOS 15.0<br>Útvonalalapú*: IOS 15.1 |Támogatott |Támogatott |
| Cisco |Meraki (MX) | MX v 15.12 |Nem kompatibilis | [Konfigurációs útmutató](https://documentation.meraki.com/MX/Site-to-site_VPN/Configuring_Site_to_Site_VPN_tunnels_to_Azure_VPN_Gateway) |
| Cisco | vEdge (Viptela operációs rendszer) | 18.4.0 (aktív/passzív mód)<br><br>19,2 (aktív/aktív mód) | Nem kompatibilis |  [Manuális konfiguráció (aktív/passzív)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Felhőbeli Onramp konfigurációja (aktív/aktív)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX, SDX, VPX |10.1-es vagy újabb verzió |[Konfigurációs útmutató](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Nem kompatibilis |
| F5 |BIG-IP sorozat |12.0 |[Konfigurációs útmutató](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Konfigurációs útmutató](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (nincs tesztelve) |[Konfigurációs útmutató](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone hálózatok | Next-Gen tűzfalak (NGFW) | 5.5 R7  | (nincs tesztelve) | [Konfigurációs útmutató](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |SEIL sorozat |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Konfigurációs útmutató](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Nem kompatibilis |
| Juniper |SRX |Házirendalapú: JunOS 10.2<br>Útvonalalapú: JunOS 11.4 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J sorozat |Házirendalapú: JunOS 10.4r9<br>Útvonalalapú: JunOS 11.4 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOs 12. x|Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Útválasztás és távelérés szolgáltatás |Windows Server 2012 |Nem kompatibilis |Támogatott |
| Open Systems AG |Mission Control biztonsági átjáró |N/A |[Konfigurációs útmutató](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |Nem kompatibilis |
| Palo Alto Networks |Az összes PAN-OS rendszert futtató eszköz |PAN-OS<br>Házirendalapú: 6.1.5 vagy újabb<br>Útvonalalapú: 7.1.4 |Támogatott |[Konfigurációs útmutató](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (fejlesztői) | VyOS | VyOS 1.2.2 | (nincs tesztelve) | [Konfigurációs útmutató ](https://docs.vyos.io/en/latest/configexamples/azure-vpn-bgp.html)|
| ShareTech | Következő generációs UTM (NU sorozat) | 9.0.1.3 | Nem kompatibilis | [Konfigurációs útmutató](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ sorozat, NSA sorozat<br>SuperMassive sorozat<br>E-Class NSA sorozat |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Nem kompatibilis |[Konfigurációs útmutató](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG Next Gen tűzfal | XG v17 | (nincs tesztelve) | [Konfigurációs útmutató](https://community.sophos.com/kb/127546)<br><br>[Konfigurációs útmutató – több SAs](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM 1.1.5/VpnPlusServer – 1.2.0 | (nincs tesztelve) | [Konfigurációs útmutató](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v 1.10 | (nincs tesztelve) | [BGP over IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI IKEv2/IPsec protokollon keresztül](https://help.ubnt.com/hc/en-us/articles/115012305347) |
| Ultra | 3E – 636L3 | 5.2.0. T3 Build-13  | (nincs tesztelve) | Konfigurációs útmutató |
| WatchGuard |Mind |Fireware XTM<br> Házirendalapú: v11.11.x<br>Útvonalalapú: v11.12.x |[Konfigurációs útmutató](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Konfigurációs útmutató](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| ZyXEL |ZyWALL ÁLLAMIGAZGATÁSBAN sorozat<br>ZyWALL ATP-sorozat<br>ZyWALL VPN-sorozat | ZLD v 4.32 + | (nincs tesztelve) | [VTI IKEv2/IPsec protokollon keresztül](https://businessforum.zyxel.com/discussion/2648/)<br><br>[BGP over IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) A CISCO ASA 8.4 és újabb verziói biztosítják az IKEv2-támogatást, valamint képesek csatlakozni egy Azure VPN-átjáróhoz egyéni IPsec/Internetes kulcscsere-házirend és a „UsePolicyBasedTrafficSelectors” beállítás használatával. További információkat [ebben a részletes útmutatóban](vpn-gateway-connect-multiple-policybased-rm-ps.md) olvashat.
>
> (**) Az ISR 7200 sorozatba tartozó útválasztók csak a házirendalapú VPN-eket támogatják.

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>VPN-eszközök konfigurációs parancsfájljainak letöltése az Azure-ból

Bizonyos eszközökhöz közvetlenül az Azure-ból tölthetők le a konfigurációs parancsfájlok. További információt és letöltési útmutatót a [VPN-eszközök konfigurációs parancsfájljainak letöltése](vpn-gateway-download-vpndevicescript.md)című témakörben talál.

### <a name="devices-with-available-configuration-scripts"></a>Elérhető konfigurációs parancsfájlokkal rendelkező eszközök

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>Nem ellenőrzött VPN-eszközök

Ha nem látja az eszközt a fenti Ellenőrzött VPN-eszközök táblázatban, attól még az eszköz képes lehet helyek közötti kapcsolat létesítésére. További támogatásért és konfigurációs útmutatásért lépjen kapcsolatba az eszköze gyártójával.

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>Az eszköz konfigurációs mintáinak szerkesztése

A megadott VPN-eszközkonfigurációs minta letöltését követően egyes értékeket a környezeti beállításoknak megfelelően le kell cserélni.

### <a name="to-edit-a-sample"></a>A minta szerkesztéséhez tegye a következőket:

1. Nyissa meg a mintát a Jegyzettömb alkalmazásban.
2. Keresse meg és cserélje le az összes &lt;*szöveges*&gt; sztringet a környezeti beállításokhoz tartozó értékekre. Győződjön meg arról, hogy szerepelnek benne < és > karakterek. A név megadásakor a kiválasztott névnek egyedinek kell lennie. Ha egy parancs nem működik, tekintse meg az eszköz gyártói dokumentációját.

| **Szövegminta** | **Módosítsa a következőre:** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Az objektum választott neve. Például: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |Az objektum választott neve. Például: myAzureNetwork |
| &lt;RP_AccessList&gt; |Az objektum választott neve. Például: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |Az objektum választott neve. Például: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |Az objektum választott neve. Például: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Adja meg a tartományt. Példa: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Adja meg az alhálózati maszkot. Példa: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Adja meg a helyszíni tartományt. Példa: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Adja meg a helyszíni alhálózati maszkot. Példa: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Ez az információ kifejezetten az Ön virtuális hálózatára vonatkozik, és a felügyeleti portálon az **átjáró IP-címe** név alatt található meg. |
| &lt;SP_PresharedKey&gt; |Ez az információ kifejezetten az Ön virtuális hálózatára vonatkozik, és a felügyeleti portálon a Kulcskezelés cím alatt található meg. |

## <a name="default-ipsecike-parameters"></a><a name="ipsec"></a>Alapértelmezett IPsec/IKE-paraméterek

Az alábbi táblázatok az alapértelmezett konfigurációban használt algoritmusok és paraméterek kombinációit tartalmazzák (**alapértelmezett házirendek**). Az Azure Resource Management üzembehelyezési modelljének használatával létrehozott, útvonalalapú VPN-átjárók esetében minden egyes kapcsolathoz külön egyéni szabályzatok adhatók meg. Részletes útmutatás: [IPsec/Internetes kulcscsere házirendje](vpn-gateway-ipsecikepolicy-rm-powershell.md).

Emellett a TCP **MSS** -t a **1350**-es időpontban kell megfognia. Ha a VPN-eszköze nem támogatja az MSS korlátozását, akkor ehelyett beállíthatja az alagútkapcsolaton az **MTU**-t **1400** bájtra.

A következő táblázatokban:

* SA = Biztonsági társítás
* Az IKE 1. fázis másik elnevezése: „Main Mode” (Elsődleges mód)
* Az IKE 2. fázis másik elnevezése: „Quick Mode” (Gyors mód)

### <a name="ike-phase-1-main-mode-parameters"></a>Az IKE 1. fázis (Elsődleges mód) paraméterei

| **Tulajdonság**          |**Házirendalapú**    | **Útvonalalapú**    |
| ---                   | ---               | ---               |
| IKE verziószám           |IKEv1              |IKEv1 és IKEv2    |
| Diffie-Hellman Group  |2. csoport (1024 bites) |2. csoport (1024 bites) |
| Hitelesítési módszer |Előre megosztott kulcs     |Előre megosztott kulcs     |
| Titkosító és kivonatoló algoritmus |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| SA élettartama           |28 800 másodperc     |28 800 másodperc     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Az IKE 2. fázis (Gyors mód) paraméterei

| **Tulajdonság**                  |**Házirendalapú**| **Útvonalalapú**                              |
| ---                           | ---           | ---                                         |
| IKE verziószám                   |IKEv1          |IKEv1 és IKEv2                              |
| Titkosító és kivonatoló algoritmus |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Útvonalalapú QM SA ajánlatok](#RouteBasedOffers) |
| SA élettartama (Idő)            |3 600 másodperc  |27 000 másodperc                               |
| SA élettartama (bájt)           |102 400 000 kB |102 400 000 kB                               |
| Sérülés utáni titkosságvédelem (PFS) |No             |[Útvonalalapú QM SA ajánlatok](#RouteBasedOffers) |
| Kapcsolat megszakadásának észlelése (DPD)     |Nem támogatott  |Támogatott                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>Útvonalalapú VPN IPsec biztonsági társítás (IKE – gyors mód SA) ajánlatai

Az alábbi táblázat felsorolja az IPsec SA (IKE – gyors mód) ajánlatait. Az ajánlatok prioritási sorrendben vannak felsorolva a választáshoz.

#### <a name="azure-gateway-as-initiator"></a>Azure-átjáró, mint kezdeményező

|-  |**Titkosítás**|**Hitelesítés**|**PFS-csoport**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nincsenek         |
| 2 |AES256        |SHA1              |Nincsenek         |
| 3 |3DES          |SHA1              |Nincsenek         |
| 4 |AES256        |SHA256            |Nincsenek         |
| 5 |AES128        |SHA1              |Nincsenek         |
| 6 |3DES          |SHA256            |Nincsenek         |

#### <a name="azure-gateway-as-responder"></a>Azure-átjáró, mint válaszadó

|-  |**Titkosítás**|**Hitelesítés**|**PFS-csoport**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nincsenek         |
| 2 |AES256        |SHA1              |Nincsenek         |
| 3 |3DES          |SHA1              |Nincsenek         |
| 4 |AES256        |SHA256            |Nincsenek         |
| 5 |AES128        |SHA1              |Nincsenek         |
| 6 |3DES          |SHA256            |Nincsenek         |
| 7 |DES           |SHA1              |Nincsenek         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Nincsenek         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Az IPsec ESP NULL titkosítás útvonalalapú és nagy teljesítményű VPN-átjárók segítségével adható meg. A nullalapú titkosítás nem biztosít védelmet az adatok számára az átvitel során, ezért használata csak abban az esetben indokolt, ha maximális átviteli sebességre és minimális késleltetésre van szükség. Az ügyfelek virtuális hálózatok közötti kapcsolatoknál dönthetnek ennek használata mellett, vagy ha más helyen a rendszer titkosítást alkalmaz.
* A létesítmények közötti internetes kapcsolat esetében az alapértelmezett Azure VPN-átjáróbeállításokat a fenti táblákban található titkosítási és kivonatolási algoritmusokkal használja a kritikus fontosságú kommunikáció biztonságának megteremtéséhez.

## <a name="known-device-compatibility-issues"></a><a name="known"></a>Ismert eszköz kompatibilitási problémák

> [!IMPORTANT]
> Ezek az ismert kompatibilitási problémák a külső gyártótól származó VPN-eszközök és az Azure VPN-átjárók között merülhetnek fel. Az Azure csapata folyamatosan együttműködik a szállítókkal az itt felsorolt problémák megoldása érdekében. A hibák kijavítása után ez az oldal frissül a legfrissebb információkkal. Kérjük, időnként látogasson vissza ide.
>
>

### <a name="feb-16-2017"></a>2017. február 16.

**A 7.1.4-esnél korábbi verziójú rendszert futtató Palo Alto Networks-eszközök** Azure útvonalalapú VPN-hez: Ha a Palo Alto Networkstől származó VPN-eszközöket használ a 7.1.4-esnél korábbi PAN-OS verzióval, és problémákat tapasztal, amikor az Azure útvonalalapú VPN-átjárókhoz csatlakozik, hajtsa végre a következő lépéseket:

1. Ellenőrizze a Palo Alto Networks-eszköz belső vezérlőprogramjának verzióját. Ha a PAN-OS verziója a 7.1.4-esnél régebbi, frissítsen a 7.1.4-es verzióra.
2. A Palo Alto Networks-eszközön módosítsa a 2. fázisú biztonsági társítás (Gyorsmódú biztonsági társítás) élettartamát 28 800 másodpercre (8 órára) az Azure-beli VPN-átjáróhoz való csatlakozáskor.
3. Ha továbbra is csatlakozási problémákat tapasztal, hozzon létre egy támogatási kérést az Azure Portalon.

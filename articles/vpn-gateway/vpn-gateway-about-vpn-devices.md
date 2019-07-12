---
title: Tudnivalók a létesítmények közötti Azure-kapcsolatokhoz használható VPN-eszközökről | Microsoft Docs
description: Ez a cikk a létesítmények közötti S2S VPN Gateway-kapcsolatokhoz használt VPN-eszközöket és IPsec paramétereket ismerteti. A konfigurációs utasítások és minták a megfelelő hivatkozásokra kattintva érhetők el.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/05/2019
ms.author: yushwang
ms.openlocfilehash: 8301594f63efaa5c6484a4dfd640aafa96cf15a0
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666262"
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

## <a name="devicetable"></a>Ellenőrzött VPN-eszközök és eszközkonfigurációs útmutatók

Eszközszállítói partnereinkkel különböző standard VPN-eszközöket ellenőriztünk. Az alábbi listában szereplő eszközcsaládokban megtalálható összes eszköz kompatibilis a VPN-átjárókkal. A konfigurálni kívánt VPN Gateway-megoldáshoz használt VPN-típusok (házirendalapú vagy útvonalalapú) megismeréséhez lásd: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#vpntype).

A VPN-eszköz konfigurálásához tekintse meg a megfelelő eszközcsaládhoz tartozó hivatkozásokat. A konfigurációs utasításokra mutató hivatkozásokat képességeinkhez mérten biztosítjuk. A VPN-eszközök támogatásával kapcsolatban lépjen kapcsolatba az eszköze gyártójával.

|**Szállító**          |**Eszközcsalád**     |**Operációs rendszer minimális verziója** |**Házirendalapú konfigurációs utasítások** |**Útválasztó-alapú konfigurációs utasítások** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Nem kompatibilis  |[Konfigurációs útmutató](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |AR sorozatú VPN-útválasztók |AR sorozatú 5.4.7+               |Hamarosan     |[Konfigurációs útmutató](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Barracuda Networks, Inc. |Barracuda NextGen tűzfal, F sorozat |Házirendalapú: 5.4.3<br>Útvonalalapú: 6.2.0 |[Konfigurációs útmutató](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Konfigurációs útmutató](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen tűzfal, X sorozat |Barracuda tűzfal, 6.5-ös verzió |[Konfigurációs útmutató](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Nem kompatibilis |
| Ellenőrzőpont |Biztonsági átjáró |R80.10 |[Konfigurációs útmutató](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Konfigurációs útmutató](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Támogatott |[Konfigurációs útmutató*](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |Házirendalapú: IOS 15.1<br>Útvonalalapú: IOS 15.2 |Támogatott |Támogatott |
| Cisco | CSR | Útvonalalapú: IOS-XE 16.10 | | [Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |Házirendalapú: IOS 15.0<br>Útvonalalapú *: IOS 15.1 |Támogatott |Támogatott |
| Cisco |Meraki |– |Nem kompatibilis |Nem kompatibilis |
| Citrix |NetScaler MPX, SDX, VPX |10.1-es vagy újabb verzió |[Konfigurációs útmutató](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Nem kompatibilis |
| F5 |BIG-IP sorozat |12.0 |[Konfigurációs útmutató](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Konfigurációs útmutató](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  |[Konfigurációs útmutató](https://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-56/) |
| Internet Initiative Japan (IIJ) |SEIL sorozat |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Konfigurációs útmutató](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Nem kompatibilis |
| Juniper |SRX |Házirendalapú: JunOS 10.2<br>Útvonalalapú: JunOS 11.4 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J sorozat |Házirendalapú: JunOS 10.4r9<br>Útvonalalapú: JunOS 11.4 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|Támogatott |[Konfigurációs parancsfájl](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Útválasztás és távelérés szolgáltatás |Windows Server 2012 |Nem kompatibilis |Támogatott |
| Open Systems AG |Mission Control biztonsági átjáró |– |[Konfigurációs útmutató](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Nem kompatibilis |
| Palo Alto Networks |Az összes PAN-OS rendszert futtató eszköz |PAN-OS<br>Házirendalapú: 6.1.5 vagy újabb<br>Útvonalalapú: 7.1.4 |[Konfigurációs útmutató](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Konfigurációs útmutató](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| ShareTech | Következő generációs UTM (NU sorozat) | 9.0.1.3 | Nem kompatibilis | [Konfigurációs útmutató](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ sorozat, NSA sorozat<br>SuperMassive sorozat<br>E-Class NSA sorozat |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Nem kompatibilis |[Konfigurációs útmutató](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG Next Gen tűzfal | XG v17 | | [Konfigurációs útmutató](https://community.sophos.com/kb/127546)<br><br>[Konfigurációs útmutató – több SAs](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM1.1.5/VpnPlusServer-1.2.0 |  | [Konfigurációs útmutató](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 |  | [Az IPSec vagy IKEv2 BGP](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[Az IPSec vagy IKEv2 VTI](https://help.ubnt.com/hc/en-us/articles/115012305347)
| WatchGuard |Összes |Fireware XTM<br> Házirendalapú: v11.11.x<br>Útvonalalapú: v11.12.x |[Konfigurációs útmutató](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Konfigurációs útmutató](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |ZyWALL Államigazgatásban sorozat<br>ZyWALL ATP sorozat<br>ZyWALL VPN-sorozat | ZLD v4.32+ | | [Az IPSec vagy IKEv2 VTI](https://businessforum.zyxel.com/discussion/2648/)<br>[Az IPSec vagy IKEv2 BGP](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) A CISCO ASA 8.4 és újabb verziói biztosítják az IKEv2-támogatást, valamint képesek csatlakozni egy Azure VPN-átjáróhoz egyéni IPsec/Internetes kulcscsere-házirend és a „UsePolicyBasedTrafficSelectors” beállítás használatával. További információkat [ebben a részletes útmutatóban](vpn-gateway-connect-multiple-policybased-rm-ps.md) olvashat.
>
> (**) Az ISR 7200 sorozatba tartozó útválasztók csak a házirendalapú VPN-eket támogatják.

## <a name="configscripts"></a>Az Azure VPN-eszközök konfigurációs szkriptjeinek letöltése

Az egyes eszközöket, akkor is konfigurációs szkriptjeinek letöltése közvetlenül az Azure-ból. További információkat és a letöltési útmutatás: [letöltése VPN-eszközök konfigurációs szkriptjeinek](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Parancsfájlok elérhető konfigurációs eszközök

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="additionaldevices"></a>Nem ellenőrzött VPN-eszközök

Ha nem látja az eszközt a fenti Ellenőrzött VPN-eszközök táblázatban, attól még az eszköz képes lehet helyek közötti kapcsolat létesítésére. További támogatásért és konfigurációs útmutatásért lépjen kapcsolatba az eszköze gyártójával.

## <a name="editing"></a>Az eszköz konfigurációs mintáinak szerkesztése

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

## <a name="ipsec"></a>IPsec/IKE-paraméterek

> [!IMPORTANT]
> 1. Az alábbi táblázat tartalmazza azon algoritmusok és paraméterek kombinációit, amelyeket az Azure VPN-átjárók alapértelmezés szerint használnak. Az Azure Resource Management üzembehelyezési modelljének használatával létrehozott, útvonalalapú VPN-átjárók esetében minden egyes kapcsolathoz külön egyéni szabályzatok adhatók meg. Részletes útmutatás: [IPsec/Internetes kulcscsere házirendje](vpn-gateway-ipsecikepolicy-rm-powershell.md).
>
> 2. Ezenfelül a TCP **MSS** korlátozását **1350-re** kell állítani. Ha a VPN-eszköze nem támogatja az MSS korlátozását, akkor ehelyett beállíthatja az alagútkapcsolaton az **MTU**-t **1400** bájtra.
>

A következő táblázatokban:

* SA = Biztonsági társítás
* Az IKE 1. fázis másik elnevezése: „Main Mode” (Elsődleges mód)
* Az IKE 2. fázis másik elnevezése: „Quick Mode” (Gyors mód)

### <a name="ike-phase-1-main-mode-parameters"></a>Az IKE 1. fázis (Elsődleges mód) paraméterei

| **Tulajdonság**          |**Házirendalapú**    | **Útvonalalapú**    |
| ---                   | ---               | ---               |
| IKE verziószám           |IKEv1              |IKEv2              |
| Diffie-Hellman Group  |2\. csoport (1024 bites) |2\. csoport (1024 bites) |
| Hitelesítési módszer |Előre megosztott kulcs     |Előre megosztott kulcs     |
| Titkosító és kivonatoló algoritmus |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| SA élettartama           |28 800 másodperc     |28 800 másodperc     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Az IKE 2. fázis (Gyors mód) paraméterei

| **Tulajdonság**                  |**Házirendalapú**| **Útvonalalapú**                              |
| ---                           | ---           | ---                                         |
| IKE verziószám                   |IKEv1          |IKEv2                                        |
| Titkosító és kivonatoló algoritmus |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Útvonalalapú QM SA ajánlatok](#RouteBasedOffers) |
| SA élettartama (Idő)            |3 600 másodperc  |27 000 másodperc                                |
| SA élettartama (bájt)           |102 400 000 kB | -                                           |
| Sérülés utáni titkosságvédelem (PFS) |Nem             |[Útvonalalapú QM SA ajánlatok](#RouteBasedOffers) |
| Kapcsolat megszakadásának észlelése (DPD)     |Nem támogatott  |Támogatott                                    |


### <a name ="RouteBasedOffers"></a>Útvonalalapú VPN IPsec biztonsági társítás (IKE – gyors mód SA) ajánlatai

Az alábbi táblázat felsorolja az IPsec SA (IKE – gyors mód) ajánlatait. Az ajánlatok prioritási sorrendben vannak felsorolva a választáshoz.

#### <a name="azure-gateway-as-initiator"></a>Azure-átjáró, mint kezdeményező

|-  |**Titkosítás**|**Hitelesítés**|**PFS-csoport**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nincsenek         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |Nincsenek         |
| 4 |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |Nincsenek         |
| 6 |3DES          |SHA256            |Nincsenek         |

#### <a name="azure-gateway-as-responder"></a>Azure-átjáró, mint válaszadó

|-  |**Titkosítás**|**Hitelesítés**|**PFS-csoport**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nincsenek         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |Nincsenek         |
| 4 |AES256        |SHA256            |Nincsenek         |
| 5 |AES128        |SHA1              |Nincsenek         |
| 6 |3DES          |SHA256            |None         |
| 7 |DES           |SHA1              |None         |
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

## <a name="known"></a>Ismert eszközkompatibilitási problémák

> [!IMPORTANT]
> Ezek az ismert kompatibilitási problémák a külső gyártótól származó VPN-eszközök és az Azure VPN-átjárók között merülhetnek fel. Az Azure csapata folyamatosan együttműködik a szállítókkal az itt felsorolt problémák megoldása érdekében. A hibák kijavítása után ez az oldal frissül a legfrissebb információkkal. Kérjük, időnként látogasson vissza ide.
>
>

### <a name="feb-16-2017"></a>2017. február 16.

**Palo Alto Networks-eszközök verziója a 7.1.4-esnél** Azure útvonalalapú VPN-hez: Ha a PAN-OS verziója a 7.1.4-esnél Palo Alto Networks származó VPN-eszközöket használ, és az Azure útvonalalapú VPN-átjárókhoz kapcsolódási problémákat tapasztal, hajtsa végre az alábbi lépéseket:

1. Ellenőrizze a Palo Alto Networks-eszköz belső vezérlőprogramjának verzióját. Ha a PAN-OS verziója a 7.1.4-esnél régebbi, frissítsen a 7.1.4-es verzióra.
2. A Palo Alto Networks-eszközön módosítsa a 2. fázisú biztonsági társítás (Gyorsmódú biztonsági társítás) élettartamát 28 800 másodpercre (8 órára) az Azure-beli VPN-átjáróhoz való csatlakozáskor.
3. Ha továbbra is csatlakozási problémákat tapasztal, hozzon létre egy támogatási kérést az Azure Portalon.

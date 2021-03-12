---
title: A VPN konfigurálása a Azure Stack Edge Pro R-eszközön a Azure PowerShell használatával
description: Ismerteti, hogyan lehet VPN-t konfigurálni a Azure Stack Edge Pro R-eszközön egy Azure PowerShell parancsfájl használatával Azure-erőforrások létrehozásához.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 66edd4cad5b2f38696ef1df2030687bf4c7d9956
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634175"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Konfigurálja a VPN-t az Azure Stack Edge Pro R-eszközön a Azure PowerShell használatával

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

A VPN-beállítás egy második titkosítási réteget biztosít a *TLS* -alapú adatmozgáshoz a Azure stack Edge Pro R-eszközről az Azure-ba. A VPN-t a Azure Stack Edge Pro R-eszközön a Azure Portal vagy a Azure PowerShellon keresztül állíthatja be.

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek ahhoz, hogy a VPN-t a Azure Stack Edge Pro R-eszközön konfigurálja a konfiguráció Felhőbeli létrehozásához Azure PowerShell használatával.

## <a name="about-vpn-setup"></a>A VPN beállítása

A létesítmények közötti VPN-kapcsolat egy Azure-beli VPN-átjáróból, egy helyszíni VPN-eszközből és egy, a kettőt összekötő IPsec S2S VPN-alagútból áll. A tipikus munkahelyi folyamat a következő lépéseket tartalmazza:

- Előfeltételek konfigurálása.
- Állítsa be a szükséges erőforrásokat az Azure-ban.
    - Azure VPN Gateway (virtuális hálózati átjáró) létrehozása és konfigurálása.
    - Hozzon létre és konfiguráljon egy olyan Azure-beli helyi hálózati átjárót, amely a helyszíni hálózati és VPN-eszközt jelöli.
    - Hozzon létre és konfiguráljon egy Azure VPN-kapcsolatot az Azure VPN Gateway és a helyi hálózati átjáró között.
    - Azure Firewall beállítása és hálózati és alkalmazási szabályok hozzáadása.
    - Hozzon létre Azure útválasztási táblázatot, és vegyen fel útvonalakat.
- Állítsa be a VPN-t az eszköz helyi webes FELÜLETén. A helyi hálózati átjáró által jelölt helyszíni VPN-eszközt úgy konfigurálja, hogy létrehozza a tényleges S2S VPN-alagutat az Azure VPN Gateway használatával.

A részletes lépések a következő fejezetekben találhatók.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása

1. Az [Azure stack Edge Pro r-eszköz telepítése](azure-stack-edge-pro-r-deploy-install.md)című részben leírtak szerint telepített Azure stack Edge Pro r-eszközhöz kell hozzáféréssel rendelkeznie. Ez az eszköz a helyszíni VPN-eszközként szolgál majd a VPN-kapcsolat létrehozásához az Azure-ban. 

2. A VPN-eszköznek statikus nyilvános IP-címmel (External) kell rendelkeznie. Ez a címnek nem lehet NAT.

3. Rendelkeznie kell egy érvényes Azure-előfizetéssel, amely engedélyezve van az Azure-beli Azure Stack Edge szolgáltatáshoz. Ezzel az előfizetéssel létrehozhat egy megfelelő erőforrást az Azure-ban az Azure Stack Edge Pro R-eszköz kezeléséhez.  

4. Hozzáférése van egy Windows-ügyfélhez, amelyet a Azure Stack Edge Pro R-eszközének eléréséhez fog használni. Ezt az ügyfelet használva programozott módon hozza létre a konfigurációt a felhőben.

    1. Ha telepíteni szeretné a PowerShell szükséges verzióját a Windows-ügyfélen, futtassa a következő parancsokat:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Az Azure-fiókhoz és-előfizetéshez való kapcsolódáshoz futtassa a következő parancsokat:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Adja meg az Azure-előfizetés nevét, amelyet a Azure Stack Edge Pro R-eszközzel használ a VPN konfigurálásához.

    3. [Töltse le a](https://aka.ms/ase-vpn-deployment) felhőben a konfiguráció létrehozásához szükséges parancsfájlt. A szkript a következőket hajtja végre:
        
        - Hozzon létre egy Azure-beli virtuális hálózatot és a következő alhálózatokat: *GatewaySubnet* és *AzureFirewallSubnet*.
        - Hozzon létre és konfiguráljon egy Azure-beli VPN-átjárót.
        - Hozzon létre és konfiguráljon egy Azure-beli helyi hálózati átjárót.
        - Hozzon létre és konfiguráljon egy Azure VPN-kapcsolatot az Azure VPN Gateway és a helyi hálózati átjáró között.
        - Hozzon létre egy Azure Firewall, és adja hozzá a hálózati szabályokat, az alkalmazás szabályait.
        - Hozzon létre egy Azure útválasztási táblázatot, és adjon hozzá útvonalakat.


## <a name="use-the-script"></a>A szkript használata

Először módosítsa a `parameters.json` fájlt a paraméterek beviteléhez. Ezután futtassa a parancsfájlt a módosított JSON-fájllal.

Ezeket a lépéseket a következő szakaszokban tárgyaljuk.

### <a name="download-service-tags-file"></a>Szolgáltatás-címkék letöltése fájl

Lehet, hogy már van egy `ServiceTags.json` fájlja abban a mappában, ahova letöltötte a parancsfájlt. Ha nem, akkor letöltheti a szolgáltatás címkéi fájlját.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Paraméterek módosítása fájl

Az első lépés a `parameters.json` fájl módosítása és a módosítások mentése. 


A létrehozott Azure-erőforrások esetében a következő neveket kell megadnia:

|Paraméter neve  |Leírás  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure Virtual Network neve        |
|azureFirewalls_firewall_name     | Azure Firewall neve        |
|routeTables_routetable_name     | Azure-útválasztási táblázat neve        |
|publicIPAddresses_VNGW_public_ip_name     | A virtuális hálózati átjáró nyilvános IP-címének neve       |
|virtualNetworkGateways_VNGW_name    | Azure VPN Gateway (virtuális hálózati átjáró) neve        |
|publicIPAddresses_firewall_public_ip_name     | A Azure Firewallhoz tartozó nyilvános IP-cím neve         |
|localNetworkGateways_LNGW_name    |Azure-beli helyi hálózati átjáró neve          |
|connections_vngw_lngw_name    | Azure VPN-kapcsolat neve. Ez a virtuális hálózati átjáró és a helyi hálózati átjáró közötti kapcsolat.       |
|location     |Ez az a régió, amelyben létre szeretné hozni a virtuális hálózatot. Válassza ki ugyanazt a régiót, mint amelyet az eszközhöz társít.         |

A következő IP-címek és címek a virtuális hálózattal és a társított alhálózatokkal (alapértelmezett, tűzfal, GatewaySubnet) létrehozott Azure-erőforrásokra vonatkoznak.

|Paraméter neve  |Leírás  |
|---------|---------|
|VnetIPv4AddressSpace    | Ez a virtuális hálózathoz hozzárendelt címterület.       |
|DefaultSubnetIPv4AddressSpace    |Ez a `Default` virtuális hálózat alhálózatához társított címterület.         |
|FirewallSubnetIPv4AddressSpace    |Ez a `Firewall` virtuális hálózat alhálózatához társított címterület.          |
|GatewaySubnetIPv4AddressSpace    |Ez a `GatewaySubnet` virtuális hálózathoz kapcsolódó címtartomány.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Ez a BGP-kommunikáció számára fenntartott IP-cím, amely a virtuális hálózathoz kapcsolódó címtartomány alapján van kiválasztva `GatewaySubnet` .          |

A következő IP-címek és címterületek a helyszíni hálózatra vonatkoznak (ahol az Azure Stack Edge Pro R-eszköz telepítve van).

|Paraméter neve  |Leírás  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Ez a magánhálózati IP-cím címterület.       |
|CustomerPublicNetworkAddressSpace    |  Ez a nyilvános IP-cím címterület.       |
|DbeIOTNetworkAddressSpace    |Ezt az IP-címet a IoT szolgáltatás foglalta le. Ne módosítsa ezt a paramétert.        |
|AzureVPNsharedKey    |Ezt a megosztott kulcsot az Azure VPN-kapcsolati erőforrás létrehozása során használja a rendszer. Ezt a kulcsot a VPN közös titka is megadja a helyi webes felhasználói felület VPN-konfigurációja során.         |
|DBE-Gateway-IP-cím   | A Azure Stack Edge Pro R-eszköz nyilvános IP-címe. Lehetséges, hogy ez nem ismert, és a szkriptet helyőrző IP-címmel futtathatja. Szerkessze később a helyi hálózati átjárót a tényleges IP-címmel.     |

#### <a name="caveats-to-keep-in-mind"></a>A következő figyelmeztetéseket kell szem előtt tartani:

- Nem lesz az Azure Stack Edge Pro R-eszköz IP-címe. A helyőrző IP-cím használatával létrehozhatja az erőforrást, majd később módosíthatja az Azure helyi hálózati átjárót, hogy hozzárendelje a tényleges eszköz IP-címét és az eszköz helyi hálózatának címterület-címét.
- Az Internet Assigned Numbers Authority (IANA) szolgáltatásban az IETF alapján a 172.16. x. y bármely alhálózata használható a 172.24. z.a. Fenntartjuk az Azure-hálózat 172,24 IPv4-címtartományt.

### <a name="run-the-script"></a>A szkript futtatása

Az alábbi lépéseket követve használhatja a módosított `parameters.json` és futtatott parancsfájlt Azure-erőforrások létrehozásához.

1. Futtassa a PowerShellt rendszergazdaként.

2. Váltson arra a könyvtárra, ahol a parancsfájl található.

3. Futtassa a szkriptet.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Az alábbiakban egy példa látható a kimenetre.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    A szkript futása körülbelül 90 percet vesz igénybe. A parancsfájl befejeződése után a rendszer egy telepítési naplót hoz létre ugyanabban a mappában, ahol a parancsfájl található.


## <a name="verify-the-azure-resources"></a>Az Azure-erőforrások ellenőrzése

A szkript sikeres futtatása után ellenőrizze, hogy az összes erőforrás létrejött-e az Azure-ban.

Ezután konfigurálja a VPN-t az eszköz helyi webes FELÜLETén.


## <a name="vpn-configuration-on-the-device"></a>VPN-konfiguráció az eszközön

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>VPN ellenőrzése

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>A VPN-en keresztüli adatátvitel ellenőrzése

A VPN működésének megerősítéséhez másolja az adatmásolt SMB-megosztásba. Kövesse a [megosztás hozzáadása](azure-stack-edge-gpu-manage-shares.md#add-a-share) az Azure stack Edge Pro R-eszközön című témakör lépéseit. 

1. Másoljon egy fájlt, például \data\pictures\waterfall.jpg az ügyfél rendszerére csatlakoztatott SMB-megosztásra. 
2. Ellenőrizze, hogy a fájl megjelenik-e a Storage-fiókban a felhőben.

Annak ellenőrzése, hogy az adatátvitel a VPN-en keresztül zajlik-e:

1. Nyissa meg az erőforráscsoport-beli kapcsolatok erőforrását. 

2. Tekintse át a és az adatkimeneti értéket.
 
3. Nyissa meg az Virtual Network átjárót az erőforráscsoporthoz. Megtekintheti a **teljes bújtatási bejövő** forgalom és az **alagút teljes kimenő** forgalmának diagramját.
 
## <a name="debug-issues"></a>Hibakeresési problémák

Az esetleges problémák hibakereséséhez használja a következő parancsokat:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

A minta kimenete alább látható:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Következő lépések

[A VPN konfigurálása helyi felhasználói felületen keresztül az Azure Stack Edge Pro R-eszközön](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)

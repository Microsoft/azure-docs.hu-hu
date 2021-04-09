---
title: A VPN konfigurálása a Azure Stack Edge mini R-eszközön a Azure PowerShell használatával
description: Ismerteti, hogyan lehet VPN-t konfigurálni a Azure Stack Edge mini R-eszközön egy Azure PowerShell parancsfájl használatával Azure-erőforrások létrehozásához.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630962"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Konfigurálja a VPN-t az Azure Stack Edge mini R-eszközön a Azure PowerShell használatával

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

A VPN-beállítás egy második titkosítási réteget biztosít a *TLS* -alapú adatmozgáshoz a Azure stack Edge mini R-ből vagy Azure stack Edge Pro r-eszközről az Azure-ba. A VPN-t a Azure Stack Edge mini R-eszközön a Azure Portal vagy a Azure PowerShell használatával konfigurálhatja. 

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a pont – hely (P2S) VPN konfigurálásához a Azure Stack Edge mini R-eszközön egy Azure PowerShell parancsfájl használatával a konfiguráció létrehozásához a felhőben. Az Azure Stack Edge-eszköz konfigurációja a helyi felhasználói felületen keresztül történik.

## <a name="about-vpn-setup"></a>A VPN beállítása

A P2S VPN Gateway-kapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről vagy a Azure Stack Edge mini R-eszközről. Az P2S-kapcsolatokat az ügyfélszámítógépről vagy az eszközről indítja el. A P2S-kapcsolat ebben az esetben a IKEv2 VPN-t használja, amely egy szabvány-alapú IPsec VPN-megoldás.

A tipikus munkahelyi folyamat a következő lépéseket tartalmazza:

1. Előfeltételek konfigurálása.
2. Állítsa be a szükséges erőforrásokat az Azure-ban.
    1. Hozzon létre és konfiguráljon egy virtuális hálózatot és egy szükséges alhálózatot. 
    2. Azure VPN Gateway (virtuális hálózati átjáró) létrehozása és konfigurálása.
    3. Azure Firewall beállítása és hálózati és alkalmazási szabályok hozzáadása.
    4. Hozzon létre Azure útválasztási táblákat, és vegyen fel útvonalakat.
    5. Engedélyezze a pont – hely lehetőséget a VPN-átjárón.
        1. Adja hozzá az ügyfél címkészlet-készletét.
        2. Adja meg az alagút típusát.
        3. Adja meg a hitelesítési típust.
        4. Tanúsítvány létrehozása.
        5. Tanúsítvány feltöltése.
    6. Telefonkönyv letöltése.
3. Állítsa be a VPN-t az eszköz helyi webes FELÜLETén. 
    1. Adja meg a telefonkönyvet.
    2. Adja meg a szolgáltatás címkéi (JSON) fájlját.


A részletes lépések a következő fejezetekben találhatók.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása

- Az [Azure stack Edge mini r-eszköz telepítése](azure-stack-edge-mini-r-deploy-install.md)című részben leírtak szerint telepített Azure stack Edge mini r-eszközhöz hozzáféréssel kell rendelkeznie. Ez az eszköz P2S-kapcsolatokat hoz létre az Azure-ban. 

- Rendelkeznie kell egy érvényes Azure-előfizetéssel, amely engedélyezve van az Azure-beli Azure Stack Edge szolgáltatáshoz. Ezzel az előfizetéssel létrehozhat egy megfelelő erőforrást az Azure-ban az Azure Stack Edge mini R-eszköz kezeléséhez.  

- Hozzáférése van egy Windows-ügyfélhez, amelyet a Azure Stack Edge mini R-eszközének eléréséhez fog használni. Ezt az ügyfelet használva programozott módon hozza létre a konfigurációt a felhőben.

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
        Adja meg az Azure-előfizetés nevét, amelyet az Azure Stack Edge mini R eszközzel használ a VPN konfigurálásához.

    3. [Töltse le a](https://aka.ms/ase-vpn-deployment) felhőben a konfiguráció létrehozásához szükséges parancsfájlt. A szkript a következőket hajtja végre:
        
        - Hozzon létre egy Azure-beli virtuális hálózatot és a következő alhálózatokat: *GatewaySubnet* és *AzureFirewallSubnet*.
        - Hozzon létre és konfiguráljon egy Azure-beli VPN-átjárót.
        - Hozzon létre és konfiguráljon egy Azure-beli helyi hálózati átjárót.
        - Hozzon létre és konfiguráljon egy Azure VPN-kapcsolatot az Azure VPN Gateway és a helyi hálózati átjáró között.
        - Hozzon létre egy Azure Firewall, és adja hozzá a hálózati szabályokat, az alkalmazás szabályait.
        - Hozzon létre egy Azure útválasztási táblázatot, és adjon hozzá útvonalakat.

    4. Hozza létre az erőforráscsoportot abban a Azure Portalban, amelyben létre szeretné hozni az Azure-erőforrásokat. Nyissa meg Azure Portal szolgáltatások listáját, válassza az **erőforráscsoport** elemet, majd válassza a **+ Hozzáadás** lehetőséget. Adja meg az előfizetési adatokat és az erőforráscsoport nevét, majd válassza a **Létrehozás** lehetőséget. Ha ezt az erőforráscsoportot használja, akkor nem rendelkezhet ebben az esetben szükséges erőforrásokkal.

        ![Azure-erőforráscsoport](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. A `.cer` Azure stack Edge mini R-eszközének formátumában rendelkeznie kell egy alap 64-kódolású tanúsítvánnyal. Ezt a tanúsítványt titkos kulccsal kell feltölteni az Azure Stack Edge-eszközre `pfx` . Ezt a tanúsítványt a P2S-kapcsolatot létesíteni próbáló ügyfélen lévő tároló megbízható gyökerében is telepíteni kell.

## <a name="use-the-script"></a>A szkript használata

Először módosítsa a `parameters-p2s.json` fájlt a paraméterek beviteléhez. Ezután futtassa a parancsfájlt a módosított JSON-fájllal.

Ezeket a lépéseket a következő szakaszokban tárgyaljuk.

### <a name="download-service-tags-file"></a>Szolgáltatás-címkék letöltése fájl

Lehet, hogy már van egy `ServiceTags.json` fájlja abban a mappában, ahova letöltötte a parancsfájlt. Ha nem, akkor letöltheti a szolgáltatás címkéi fájlját.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Paraméterek módosítása fájl

Az első lépés a `parameters-p2s.json` fájl módosítása és a módosítások mentése. 

A létrehozott Azure-erőforrások esetében a következő neveket kell megadnia:

|Paraméter neve  |Leírás  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure Virtual Network neve        |
|azureFirewalls_firewall_name     | Azure Firewall neve        |
|routeTables_routetable_name     | Azure-útválasztási táblázat neve        |
|publicIPAddresses_VNGW_public_ip_name     | A virtuális hálózati átjáró nyilvános IP-címének neve       |
|virtualNetworkGateways_VNGW_name    | Azure VPN Gateway (virtuális hálózati átjáró) neve        |
|publicIPAddresses_firewall_public_ip_name     | A Azure Firewallhoz tartozó nyilvános IP-cím neve         |
|location     |Ez az a régió, amelyben létre szeretné hozni a virtuális hálózatot. Válassza ki ugyanazt a régiót, mint amelyet az eszközhöz társít.         |
|RouteTables_routetable_onprem_name| Ez a további útválasztási táblázat neve, amellyel a tűzfal átirányíthatja a csomagokat Azure Stack peremhálózati eszközre. A parancsfájl két további útvonalat hoz létre, és az *alapértelmezett* és *FirewallSubnet* társítja az útválasztási táblázathoz.|

Adja meg a következő IP-címeket és címeket a létrehozott Azure-erőforrások számára, beleértve a virtuális hálózatot és a társított alhálózatokat (*alapértelmezett*, *tűzfal*, *GatewaySubnet*).

|Paraméter neve  |Leírás  |
|---------|---------|
|VnetIPv4AddressSpace    | Ez a virtuális hálózathoz hozzárendelt címterület. Adja meg a vnet IP-címtartományt magánhálózati IP-címtartományként ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Ez a `Default` virtuális hálózat alhálózatához társított címterület.         |
|FirewallSubnetIPv4AddressSpace    |Ez a `Firewall` virtuális hálózat alhálózatához társított címterület.          |
|GatewaySubnetIPv4AddressSpace    |Ez a `GatewaySubnet` virtuális hálózathoz kapcsolódó címtartomány.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Ez a BGP-kommunikáció számára fenntartott IP-cím, amely a virtuális hálózathoz kapcsolódó címtartomány alapján van kiválasztva `GatewaySubnet` .          |
|ClientAddressPool    | Ezt az IP-címet használja a rendszer a P2S-konfigurációban található címkészlet Azure Portal.         |
|PublicCertData     | A VPN Gateway a nyilvános tanúsítványokat a hozzá csatlakozó P2S-ügyfelek hitelesítésére használja. A tanúsítvány beszerzéséhez telepítse a főtanúsítványt. Győződjön meg arról, hogy a tanúsítvány Base-64. cer kiterjesztéssel van kódolva. Nyissa meg ezt a tanúsítványt, és másolja a tanúsítványban szereplő szöveget a = = BEGIN CERTIFICATe = = és = = END CERTIFICATe = = egy folytonos sorba.     |



### <a name="run-the-script"></a>A szkript futtatása

Az alábbi lépéseket követve használhatja a módosított `parameters-p2s.json` és futtatott parancsfájlt Azure-erőforrások létrehozásához.

1. Futtassa a PowerShellt. Váltson arra a könyvtárra, ahol a parancsfájl található.

3. Futtassa a szkriptet.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > Ebben a kiadásban a parancsfájl csak az USA keleti régiójában működik.

    A parancsfájl futtatásakor a következő információkat kell megadnia:

    
    |Paraméter  |Leírás  |
    |---------|---------|
    |Hely     |Ez az a régió, amelyben létre kell hozni az Azure-erőforrásokat.         |
    |AzureAppRuleFilePath     | A fájl elérési útja `appRule.json` .       |
    |AzureIPRangesFilePath     |Ez az előző lépésben letöltött JSON-fájl.         |
    |ResourceGroupName     | Az az erőforráscsoport neve, amelyben az összes Azure-erőforrás létrejött.        |
    |AzureDeploymentName    |Ez az Azure-beli üzemelő példány neve.         |
    |NetworkRuleCollectionName            | Ez a név az összes létrehozott és a Azure Firewallhoz hozzáadandó hálózati szabály gyűjteménye számára.             |
    |Prioritás            | Ez az összes létrehozott hálózati és alkalmazási szabályhoz rendelt prioritás.              |
    |AppRuleCollectionName            |Ez a név a Azure Firewallhoz létrehozott és hozzáadott összes alkalmazási szabály gyűjteménye számára.                |


    Az alábbiakban egy példa látható a kimenetre.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - A szkript futása körülbelül 90 percet vesz igénybe. Győződjön meg arról, hogy közvetlenül a szkript elindítása előtt bejelentkezik a hálózatra.
    > - Ha bármilyen okból nem sikerült munkamenetet létrehozni a parancsfájllal, mindenképp törölje az erőforráscsoportot az alatta létrehozott összes erőforrás törléséhez.
  
    
    A parancsfájl befejeződése után a rendszer egy telepítési naplót hoz létre ugyanabban a mappában, ahol a parancsfájl található.


## <a name="verify-the-azure-resources"></a>Az Azure-erőforrások ellenőrzése

A szkript sikeres futtatása után ellenőrizze, hogy az összes erőforrás létrejött-e az Azure-ban. Nyissa meg a létrehozott erőforráscsoportot. A következő erőforrásokat kell megjelennie:

![Azure-erőforrások](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Telefonkönyv letöltése a VPN-profilhoz

Ebben a lépésben le fogja tölteni az eszköz VPN-profilját.

1. A Azure Portal nyissa meg az erőforráscsoportot, majd válassza ki az előző lépésben létrehozott virtuális hálózati átjárót.

    ![Azure-beli virtuális hálózati átjáró](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Válassza a **beállítások > pont – hely konfiguráció lehetőséget**. Válassza a **VPN-ügyfél letöltése** lehetőséget.

    ![P2S-konfiguráció engedélyezése 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Mentse a tömörített profilt, és bontsa ki a Windows-ügyfelet.

    ![2. P2S-konfiguráció engedélyezése](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Nyissa meg a *WindowsAmd64* mappát, majd bontsa ki a következőt `.exe` : *VpnClientSetupAmd64.exe*.

    ![3. P2S-konfiguráció engedélyezése](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Hozzon létre egy ideiglenes elérési utat. Például:

    `C:\NewTemp\vnet\tmp`

4. Futtassa a PowerShellt, és keresse meg azt a könyvtárat, ahol a `.exe` található. A végrehajtásához `.exe` írja be a következőt:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Az ideiglenes elérési út új fájlokkal fog rendelkezni. Itt látható egy mintakimenet:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. A *. pbk* fájl a VPN-profilhoz tartozó telefonkönyv. Ezt a helyi felhasználói felületen fogja használni.


## <a name="vpn-configuration-on-the-device"></a>VPN-konfiguráció az eszközön

Kövesse az alábbi lépéseket az Azure Stack Edge-eszköz helyi felhasználói felületén.

1. A helyi felhasználói felületen nyissa meg a **VPN** lapot. A VPN állapota területen válassza a **Konfigurálás** lehetőséget.

    ![A VPN 1 konfigurálása](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. A **VPN konfigurálása** panelen:
    
    1. A telefonkönyv feltöltése fájlban mutasson arra a. pbk fájlra, amelyet az előző lépésben hozott létre.
    2. A nyilvános IP-címek listájának konfigurációs fájljának feltöltése lapon adja meg az Azure adatközpont IP-címtartomány JSON-fájlját bemenetként. Ezt a fájlt egy korábbi lépésben töltötte le a következő helyről: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Válassza a **eastus** lehetőséget a régió területen, majd válassza az **alkalmaz** lehetőséget.

    ![A 2. VPN konfigurálása](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. Adja meg azt az IP-vnet, amelyet a **csak VPN használatával szeretne elérni** , írja be az Azure-Virtual Network kiválasztott IPv4-tartományát.

    ![A VPN 3 konfigurálása](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Ügyfélkapcsolat ellenőrzése

1. A Azure Portal nyissa meg a VPN-átjárót.
2. Válassza a **beállítások > pont – hely konfiguráció lehetőséget**. A **lefoglalt IP-címek** területen megjelenik az Azure stack Edge-eszköz IP-címe.

## <a name="validate-data-transfer-through-vpn"></a>A VPN-en keresztüli adatátvitel ellenőrzése

A VPN működésének megerősítéséhez másolja az adatmásolt SMB-megosztásba. Kövesse a [megosztás hozzáadása](azure-stack-edge-gpu-manage-shares.md#add-a-share) az Azure stack Edge-eszközön című témakör lépéseit. 

1. Másoljon egy fájlt, például \data\pictures\waterfall.jpg az ügyfél rendszerére csatlakoztatott SMB-megosztásra. 
2. Annak ellenőrzéséhez, hogy az adatátvitel a VPN-en keresztül történik-e, az Adatmásolás közben:

    1. Nyissa meg a Azure Portal a VPN-átjárót. 

    2. Ugrás a **figyelés > metrikák**.

    3. A jobb oldali ablaktáblában válassza ki a **hatókört** a VPN-átjáróként, a **metrikát** az átjáró P2S sávszélességként, és az **összesítést** AVG-ként.

    4. Az adatmásolási folyamat során a sávszélesség kihasználtsága megnő, és az Adatmásolás befejezése után a sávszélesség-használat csökkenni fog.

        ![Azure VPN-metrikák](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Ellenőrizze, hogy a fájl megjelenik-e a Storage-fiókban a felhőben.
 
## <a name="debug-issues"></a>Hibakeresési problémák

Az esetleges problémák hibakereséséhez használja a következő parancsokat:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

A minta kimenete alább látható:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[Konfigurálja a VPN-t a Azure stack Edge-eszköz helyi felhasználói felületén keresztül](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).
---
title: Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Fileshoz való használathoz | Microsoft Docs
description: Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Fileshoz való használatra
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74422318718e318a00d7bd7ebaf8e4093ef75aa6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629274"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Files-vel való használatra
A pont – hely (P2S) VPN-kapcsolattal Azure-fájlmegosztás az Azure-on kívülről is csatlakoztatható SMB-n keresztül, a 445-es port megnyitása nélkül. A pont – hely VPN-kapcsolat az Azure és az egyes ügyfelek közötti VPN-kapcsolat. Ha a P2S VPN-kapcsolatot Azure Files használatával szeretné használni, konfigurálnia kell egy P2S VPN-kapcsolatot minden olyan ügyfél számára, amelyhez csatlakozni szeretne. Ha sok ügyféllel kell csatlakoznia az Azure-fájlmegosztás számára a helyszíni hálózatból, akkor az egyes ügyfelek pont – hely kapcsolata helyett használhat helyek közötti (S2S) VPN-kapcsolatot. További információ: helyek közötti [VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md).

Javasoljuk, hogy olvassa el [Azure Files hálózatkezelési áttekintést](storage-files-networking-overview.md) , mielőtt folytatná ezt a cikket a Azure Files elérhető hálózati beállítások teljes körű megvitatására.

A cikk részletesen bemutatja, hogyan konfigurálhat egy pont – hely VPN-t Linux rendszeren az Azure-fájlmegosztás közvetlen helyszíni csatlakoztatásához. Ha VPN-en keresztül szeretné átirányítani Azure File Sync forgalmat, tekintse meg a [Azure file Sync proxy-és tűzfalbeállítások konfigurálása](storage-sync-files-firewall-and-proxy.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek
- Az Azure CLI legújabb verziója. Az Azure CLI telepítésével kapcsolatos további információkért lásd: [install the Azure POWERSHELL CLI](/cli/azure/install-azure-cli) , és válassza ki az operációs rendszert. Ha a Linux rendszeren szeretné használni a Azure PowerShell-modult, akkor az alábbi utasításokat azonban az Azure CLI-hez mutatjuk be.

- Egy Azure-fájlmegosztás, amelyet a helyszínen kíván csatlakoztatni. Az Azure-fájlmegosztás üzembe helyezése a Storage-fiókokban történik, amelyek olyan felügyeleti szerkezetek, amelyek olyan megosztott tárolót képviselnek, amelyben több fájlmegosztás, valamint más tárolási erőforrások, például blob-tárolók vagy várólisták helyezhetők üzembe. Az Azure-fájlmegosztás és a Storage-fiókok Azure- [fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című részében olvashat bővebben.

- Az Azure-fájlmegosztás a helyszínen csatlakoztatni kívánt részét tartalmazó Storage-fiókhoz tartozó magánhálózati végpont. További információ a privát végpontok létrehozásáról: [Azure Files hálózati végpontok konfigurálása](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>A szükséges szoftverek telepítése
Az Azure-beli virtuális hálózati átjáró több VPN-protokollt használó VPN-kapcsolatot is biztosít, beleértve az IPsec és az OpenVPN-t is. Ez az útmutató bemutatja, hogyan használható az IPsec, és hogyan használhatja a alapú strongswan csomagot a Linux-támogatás biztosításához. 

> Ellenőrizve Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Virtuális hálózat üzembe helyezése 
Ha egy pont – hely típusú VPN-kapcsolaton keresztül szeretné elérni az Azure-fájlmegosztást és más Azure-erőforrásokat, létre kell hoznia egy virtuális hálózatot vagy egy VNet. A P2S VPN-kapcsolat automatikusan létrejön a helyszíni Linux-gép és az Azure-beli virtuális hálózat között.

A következő szkript létrehoz egy három alhálózattal rendelkező Azure-beli virtuális hálózatot: egyet a Storage-fiók szolgáltatási végpontja számára, egyet a Storage-fiók privát végpontja számára, amely a helyszíni Storage-fiókhoz való hozzáféréshez szükséges, anélkül, hogy egyéni útválasztást kellene létrehozni a Storage-fiók nyilvános IP-címéhez, és egyet a VPN szolgáltatást biztosító virtuális hálózati átjáróhoz. 

Ne felejtse el lecserélni a `<region>` `<resource-group>` `<desired-vnet-name>` környezetet a megfelelő értékekre.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Tanúsítványok létrehozása a VPN-hitelesítéshez
Ahhoz, hogy a helyszíni linuxos gépek VPN-kapcsolatai hitelesítve legyenek a virtuális hálózathoz való hozzáféréshez, létre kell hoznia két tanúsítványt: egy főtanúsítványt, amelyet a rendszer a virtuális gép átjárója számára biztosít, valamint egy ügyféltanúsítványt, amely a főtanúsítvánnyal lesz aláírva. A következő parancsfájl létrehozza a szükséges tanúsítványokat.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Virtuális hálózati átjáró üzembe helyezése
Az Azure-beli virtuális hálózati átjáró az a szolgáltatás, amellyel a helyszíni linuxos gépek csatlakozni fognak. A szolgáltatás üzembe helyezéséhez két alapvető összetevő szükséges: egy nyilvános IP-cím, amely azonosítja az átjárót az ügyfelek számára, bárhol is legyenek a világon, és egy korábban létrehozott főtanúsítvány, amelyet az ügyfelek hitelesítéséhez fog használni.

Ne felejtse el lecserélni `<desired-vpn-name-here>` az ehhez az erőforrásokhoz hasonló nevet.

> [!Note]  
> Az Azure-beli virtuális hálózati átjáró üzembe helyezése akár 45 percet is igénybe vehet. Az erőforrás üzembe helyezése közben ez a bash parancsfájl-parancsfájl letiltja a központi telepítés befejeződését.
>
> A P2S IKEv2/OpenVPN-kapcsolatok nem támogatottak az **alapszintű** SKU-val. Ez a szkript a virtuális hálózati átjáróhoz tartozó **VpnGw1** SKU-t használja ennek megfelelően.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>A VPN-ügyfél konfigurálása
Az Azure Virtual Network Gateway egy letölthető csomagot hoz létre a helyi linuxos gépen a VPN-kapcsolat inicializálásához szükséges konfigurációs fájlokkal. A következő szkript elhelyezi a megfelelő helyen létrehozott tanúsítványokat, és a `ipsec.conf` fájl megfelelő értékeit konfigurálja a letölthető csomagban található konfigurációs fájlból.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása
Most, hogy beállította a pont – hely VPN-t, csatlakoztathatja az Azure-fájlmegosztást. A következő példa a megosztást nem állandóan csatlakoztatja. A folyamatos csatlakoztatáshoz tekintse meg az [Azure-fájlmegosztás használata Linux rendszeren](storage-how-to-use-files-linux.md)című témakört. 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Lásd még
- [Azure Files hálózatkezelés – áttekintés](storage-files-networking-overview.md)
- [Pont – hely (P2S) VPN konfigurálása Windows rendszeren a Azure Files-mel való használatra](storage-files-configure-p2s-vpn-windows.md)
- [Helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md)
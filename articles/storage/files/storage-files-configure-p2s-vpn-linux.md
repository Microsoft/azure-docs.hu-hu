---
title: Pont–hely (P2S) VPN konfigurálása Linux rendszeren az Azure Files | Microsoft Docs
description: Pont–hely (P2S) VPN konfigurálása Linux rendszeren az Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9608e3bdaab033d58796a3841e8cd92d7a8a81ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777976"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Pont–hely (P2S) VPN konfigurálása Linux rendszeren az Azure Files
Pont–hely (P2S) VPN-kapcsolattal SMB-n keresztül csatlakoztathatja az Azure-fájlmegosztásokat az Azure-n kívülről, a 445-ös port megnyitása nélkül. A pont–hely VPN-kapcsolat egy VPN-kapcsolat az Azure és egy különálló ügyfél között. Ha P2S VPN-kapcsolatot szeretne használni a Azure Files, minden csatlakoztatni kívánó ügyfélhez konfigurálni kell egy P2S VPN-kapcsolatot. Ha sok ügyfélnek kell csatlakoznia az Azure-fájlmegosztáshoz a helyszíni hálózatról, minden ügyfélhez használhat pont–hely típusú kapcsolat helyett egy S2S VPN-kapcsolatot. További információ: [Configure a Site-to-Site VPN for use with Azure Files.](storage-files-configure-s2s-vpn.md)

Határozottan javasoljuk, hogy olvassa el [Azure Files](storage-files-networking-overview.md) áttekintését, mielőtt továbblép a következő cikkre, amely a hálózati lehetőségek teljes körű áttekintését Azure Files.

A cikk részletesen beállítja a pont–hely VPN-t Linux rendszeren az Azure-fájlmegosztások közvetlen helyszíni csatlakoztatására. Ha vpn-en keresztül Azure File Sync forgalmat, tekintse meg a proxy- és Azure File Sync-beállítások [konfigurálását.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Előfeltételek
- Az Azure CLI legújabb verziója. Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Install the Azure PowerShell CLI and](/cli/azure/install-azure-cli) select your operating system (Az Azure PowerShell CLI telepítése és az operációs rendszer kiválasztása). Ha inkább a Linuxon Azure PowerShell modult szeretné használni, az alábbi utasításokat azonban az Azure CLI-hez is használhatja.

- Egy Azure-fájlmegosztás, amely a helyszínen csatlakoztatható. Az Azure-fájlmegosztások tárfiókokba vannak telepítve, amelyek olyan megosztott tárolókészletet képviselő felügyeleti szerkezetek, amelyekben több fájlmegosztást, valamint más tárolási erőforrásokat, például blobtárolókat vagy üzenetsorokat helyezhet üzembe. További információ az Azure-fájlmegosztások és -tárfiókok üzembe helyezéséről: [Azure-fájlmegosztás létrehozása.](storage-how-to-create-file-share.md)

- A helyszíni csatlakoztatáshoz csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiók privát végpontja. További információ a privát végpontok létrehozásáról: [Configuring Azure Files network endpoints](storage-files-networking-endpoints.md?tabs=azure-cli)( Hálózati végpontok konfigurálása). 

## <a name="install-required-software"></a>A szükséges szoftverek telepítése
Az Azure-beli virtuális hálózati átjáró vpn-kapcsolatokat tud biztosítani több VPN-protokoll használatával, beleértve az IPsec-et és az OpenVPN-t. Ez az útmutató bemutatja, hogyan használhatja az IPsec-et, és a strongSwan csomaggal biztosítja a támogatást Linux rendszeren. 

> Ubuntu 18.10-es rendszer ellenőrzése.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Virtuális hálózat üzembe helyezése 
Ahhoz, hogy az Azure-fájlmegosztást és más Azure-erőforrásokat a helyszínről pont–hely VPN-en keresztül el tudja elérni, létre kell hoznia egy virtuális hálózatot vagy virtuális hálózatot. Az automatikusan létrehozott P2S VPN-kapcsolat egy híd a helyszíni Linux-gép és az Azure-beli virtuális hálózat között.

A következő szkript létrehoz egy Azure-beli virtuális hálózatot három alhálózattal: egyet a tárfiók szolgáltatásvégpontjának, egyet a tárfiók privát végpontjának, amely a helyszíni tárfiók eléréséhez szükséges anélkül, hogy egyéni útválasztást hoz létre a tárfiók módosítható nyilvános IP-címéhez, és egyet a VPN-szolgáltatást szolgáltató virtuális hálózati átjáróhoz. 

Ne felejtse el lecserélni a , és értékeket a `<region>` `<resource-group>` `<desired-vnet-name>` környezetének megfelelő értékekre.

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

## <a name="create-certificates-for-vpn-authentication"></a>Tanúsítványok létrehozása VPN-hitelesítéshez
Ahhoz, hogy a helyszíni Linux rendszerű gépek VPN-kapcsolatait hitelesíteni tudja a virtuális hálózat eléréséhez, két tanúsítványt kell létrehoznia: egy főtanúsítványt, amelyet a rendszer a virtuálisgép-átjáró számára biztosít, valamint egy ügyféltanúsítványt, amelyet a főtanúsítvánnyal fog aláírni. A következő szkript létrehozza a szükséges tanúsítványokat.

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
Az Azure-beli virtuális hálózati átjáró az a szolgáltatás, amelyhez a helyszíni Linux rendszerű gépek csatlakozni fognak. A szolgáltatás telepítéséhez két alapvető összetevőre van szükség: egy nyilvános IP-címre, amely azonosítja az átjárót az ügyfelek számára, bárhol is vannak a világon, valamint egy, a korábban létrehozott főtanúsítványra, amely az ügyfelek hitelesítésére lesz használva.

Ne felejtse el `<desired-vpn-name-here>` lecserélni a helyére az erőforrásokhoz megfelelő nevet.

> [!Note]  
> Az Azure-beli virtuális hálózati átjáró üzembe helyezése akár 45 percet is igénybe vehet. Az erőforrás üzembe helyezése közben ez a Bash-szkript blokkolni fogja az üzembe helyezés befejezését.
>
> A P2S IKEv2/OpenVPN-kapcsolatok nem támogatottak az **alapszintű** termékváltozattal. Ez a szkript ennek megfelelően a **VpnGw1** termékváltozatot használja a virtuális hálózati átjáróhoz.

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
Az Azure-beli virtuális hálózati átjáró létrehoz egy letölthető csomagot a helyszíni Linux rendszerű gépen a VPN-kapcsolat inicializálásához szükséges konfigurációs fájlokkal. A következő szkript a megfelelő helyre fogja állítani a létrehozott tanúsítványokat, és a fájlt a megfelelő értékekkel konfigurálja a letölthető csomagban található `ipsec.conf` konfigurációs fájlból.

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
Most, hogy beállította a pont–hely VPN-t, csatlakoztathatja az Azure-fájlmegosztást. Az alábbi példa nem állandó módon csatlakoztatja a megosztást. Az állandó csatlakoztatással lásd: [Azure-fájlmegosztás használata Linux rendszeren.](storage-how-to-use-files-linux.md) 

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
- [Azure Files hálózatok áttekintése](storage-files-networking-overview.md)
- [Pont–hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Pont–hely (S2S) VPN konfigurálása az Azure Files](storage-files-configure-s2s-vpn.md)
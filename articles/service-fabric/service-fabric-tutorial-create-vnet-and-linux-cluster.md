---
title: Linux Service Fabric-fürt létrehozása az Azure-ban
description: Megismerheti, hogyan helyezhet üzembe egy Linux-alapú Service Fabric-fürtöt egy meglévő Azure-beli virtuális hálózatban az Azure CLI használatával.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77cc49c1b79e5c24e78a67a69493aa0b0059d565
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791071"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Linux Service Fabric-fürt üzembe helyezése Azure-beli virtuális hálózaton

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe Linux Service Fabric-fürtöt Azure-beli [virtuális hálózatban (VNET)](../virtual-network/virtual-networks-overview.md) az Azure CLI és egy sablon használatával. Amikor végzett, a felhőben futó fürttel fog rendelkezni, amelyre alkalmazásokat telepíthet. Ha a PowerShell használatával szeretne Windows-fürtöt létrehozni, lásd: [Biztonságos Windows-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A [Service FABRIC parancssori](service-fabric-cli.md) felületének telepítése
* Az [Azure CLI](/cli/azure/install-azure-cli) telepítése
* A fürtök legfontosabb fogalmait az [Azure-fürtök áttekintését ismertető témakörben](service-fabric-azure-clusters-overview.md) tekintheti meg
* [Tervezze meg és készítse elő](service-fabric-cluster-azure-deployment-preparation.md) az üzemi fürtök üzembe helyezését.

Az alábbi eljárások hét csomópontos Service Fabric fürtöt hoznak létre. A Service Fabric-fürtök Azure-ban történő futtatásával járó költségek kiszámításához használja az [Azure-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse az alábbi Resource Manager-sablonfájlokat:

Ubuntu 16,04 LTS esetén:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Ubuntu 18,04 LTS esetén:

* [AzureDeploy.json][template2]
* [AzureDeploy.Parameters.json][parameters2]

Ubuntu 18,04 LTS esetén a két sablon közötti különbség a következő: 
* a **vmImageSku** attribútum "18,04-LTS" értékre van állítva
* az egyes csomópontok **typeHandlerVersion** beállítása 1,1
* Microsoft. ServiceFabric/fürtök erőforrás
   - a **apiVersion** értéke "2019-03-01" vagy magasabb
   - a **vmImage** tulajdonság "Ubuntu18_04" értékre van állítva

Ez a sablon hét virtuális gép és három csomópont típusú biztonságos fürtöt helyez üzembe egy virtuális hálózatban.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál. Az [AzureDeploy.json][template] több erőforrást is üzembe helyez, többek között az alábbiakat.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban a rendszer üzembe helyez egy Linux-fürtöt, amelyet a következők jellemeznek:

* három csomópont típusa
* az elsődleges csomópont típusának öt csomópontja (a sablon paraméterei között állítható be), a többi csomópont egyik csomópontja
* Operációs rendszer: (Ubuntu 16,04 LTS/Ubuntu 18,04 LTS) (a sablon paramétereinek megfelelően konfigurálható)
* tanúsítványon alapuló védelem (a sablon paramétereiben konfigurálható);
* engedélyezve van a [DNS szolgáltatás](service-fabric-dnsservice.md);
* bronz szintű [tartóssági szint](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
* ezüst szintű [megbízhatósági szint](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (a sablon paramétereiben konfigurálható);
* ügyfélkapcsolati végpont: 19000 (a sablon paramétereiben konfigurálható);
* HTTP-átjáró végpontja: 19080 (a sablon paramétereiben konfigurálható).

### <a name="azure-load-balancer"></a>Azure Load Balancer

A **Microsoft.Network/loadBalancers** erőforrásban a rendszer egy terheléselosztót konfigurál, a mintavételeket és szabályokat pedig az alábbi portokra állítja be:

* ügyfélkapcsolati végpont: 19000;
* HTTP-átjáró végpontja: 19080;
* alkalmazásport: 80;
* alkalmazásport: 443;

### <a name="virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat

A virtuális hálózat és az alhálózat neve a sablon paramétereiben határozható meg.  A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg, és a **Microsoft.Network/virtualNetworks** erőforrásban van konfigurálva:

* virtuális hálózat címtere: 10.0.0.0/16
* Service Fabric-alhálózat címtere: 10.0.2.0/24

Ha további alkalmazásportokra van szükség, akkor módosítania kell a Microsoft.Network/loadBalancers erőforrást a forgalom beengedésére.

## <a name="set-template-parameters"></a>Sablon paramétereinek megadása

A **AzureDeploy. Parameters** fájl számos, a fürt és a kapcsolódó erőforrások üzembe helyezéséhez használt értéket deklarál. Néhány paraméter, amelyeket lehet, hogy módosítani kell az üzembe helyezéshez:

|Paraméter|Példaérték|Jegyzetek|
|---|---||
|adminUserName|vmadmin| Rendszergazdai felhasználónév a fürt virtuális gépeihez. |
|adminPassword|Password#1234| Rendszergazdai jelszó a fürt virtuális gépeihez.|
|clusterName|mysfcluster123| A fürt neve. |
|location|southcentralus| A fürt helye. |
|certificateThumbprint|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány SHA1 ujjlenyomatának értékét. Például: „6190390162C988701DB5676EB81083EA608DCCF3” </p>|
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. Példa: "https: \/ /mykeyvault.Vault.Azure.net:443/Secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. Az **AzureDeploy.json** Resource Manager-sablon egy virtuális hálózatot (VNET-et) hoz létre a Service Fabric számára. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal.  Éles fürtök esetén hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon fürttanúsítványként. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

A cikkben található sablon olyan fürtöt helyez üzembe, amely a tanúsítvány ujjlenyomatát használja a fürt tanúsítványának azonosításához.  Nincs két tanúsítvány ugyanazzal az ujjlenyomattal, ami nehezebbé teszi a Tanúsítványkezelőt. Ha egy telepített fürtöt a tanúsítvány ujjlenyomatai megfelelnek használ a tanúsítványok köznapi nevének használatára, a Tanúsítványkezelő sokkal egyszerűbbé válik.  Ha meg szeretné tudni, hogyan frissítheti a fürtöt a tanúsítványok köznapi neveinek használatára, olvassa el a [fürt módosítása a tanúsítvány köznapi nevének kezelése](service-fabric-cluster-change-cert-thumbprint-to-cn.md)című témakört.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Fürt létrehozása meglévő tanúsítvány használatával

Az alábbi szkript az [az sf cluster create](/cli/azure/sf/cluster) parancs és egy sablon használatával helyez üzembe egy meglévő tanúsítvánnyal védett új fürtöt. A parancs emellett egy új kulcstárolót is létrehoz az Azure-ban, és feltölti a tanúsítványt.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Fürt létrehozása új, önaláírt tanúsítvány használatával

Az alábbi szkript az [az sf cluster create](/cli/azure/sf/cluster) parancs és egy sablon használatával helyez üzembe egy új fürtöt az Azure-ban. A parancs egy új kulcstárolót is létrehoz az Azure-ban, egy új önaláírt tanúsítványt hoz létre a kulcstartóhoz, és helyileg letölti a tanúsítványfájl-fájlt.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz

Csatlakozzon a fürthöz a Service Fabric parancssori felület `sfctl cluster select` parancsát használva a kulcsával.  A **--no-verify** paramétert csak önaláírt tanúsítvány esetén használja.

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Az `sfctl cluster health` parancs futtatásával ellenőrizze, hogy csatlakozik-e, és hogy a fürt állapota kifogástalan-e.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem azonnal tér rá a következő cikkre, érdemes [törölnie a fürtöt](./service-fabric-tutorial-delete-cluster.md) a felmerülő költségek elkerülése érdekében.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [méretezheti a fürtöt](service-fabric-tutorial-scale-cluster.md).

A cikkben található sablon olyan fürtöt helyez üzembe, amely a tanúsítvány ujjlenyomatát használja a fürt tanúsítványának azonosításához.  Nincs két tanúsítvány ugyanazzal az ujjlenyomattal, ami nehezebbé teszi a Tanúsítványkezelőt. Ha egy telepített fürtöt a tanúsítvány ujjlenyomatai megfelelnek használ a tanúsítványok köznapi nevének használatára, a Tanúsítványkezelő sokkal egyszerűbbé válik.  Ha meg szeretné tudni, hogyan frissítheti a fürtöt a tanúsítványok köznapi neveinek használatára, olvassa el a [fürt módosítása a tanúsítvány köznapi nevének kezelése](service-fabric-cluster-change-cert-thumbprint-to-cn.md)című témakört.

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json

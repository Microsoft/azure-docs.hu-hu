---
title: A hálózati (ARO Azure Red Hat OpenShift forgalmának korlátozása
description: Megtudhatja, milyen portokra és címekre van szükség a bejövő forgalom Azure Red Hat OpenShift (ARO)
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368690"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>A hálózati (ARO Azure Red Hat OpenShift (előzetes verzió) forgalom szabályozása

Ez a cikk azokat a szükséges részleteket tartalmazza, amelyek lehetővé teszik a Azure Red Hat OpenShift (ARO) kimenő forgalmának biztonságát. Tartalmazza az alapszintű ARO-telepítés fürtkövetelményét, valamint az opcionális Red Hat- és külső összetevőkre vonatkozó további követelményeket. A [végén](#private-aro-cluster-setup) egy példát mutatunk be arról, hogyan konfigurálhatja ezeket a követelményeket a Azure Firewall. Ne feledje, hogy ezeket az információkat alkalmazhatja bármely Azure Firewall korlátozási módszerre vagy berendezésre.

## <a name="before-you-begin"></a>Előkészületek

A cikk feltételezi, hogy új fürtöt hoz létre. Ha alapszintű ARO-fürtre van szüksége, tekintse meg az [ARO rövid útmutatóját.](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster)

> [!IMPORTANT]
> Az ARO előzetes verziójú funkciói önkiszolgáló, feliratkozási alapon érhetők el. Az előzetes verziók "adott időben" és "elérhetőként" érhetők el, és ki vannak zárva a szolgáltatásiszint-szerződésekből és a korlátozott jótállásból. Az ARO előzetes verzióit részlegesen lefedi az ügyfélszolgálat.

## <a name="minimum-required-fqdn--application-rules"></a>Minimálisan szükséges teljes tartományn/alkalmazásszabályok

Ez a lista az OpenShift-dokumentumokban található FQDN-ek listáján alapul: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

A következő FQDN/ alkalmazásszabályok szükségesek:

| Cél FQDN | Port | Használat |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS:443** | A fürt által használt telepítéshez kötelező. Ezt a fürt használja a platform tárolólemezképének letöltéséhez. |
| **`registry.redhat.io`** | **HTTPS:443** | Az alapvető bővítményekhez kötelező. Ezt a fürt az olyan alapvető összetevők letöltésére használja, mint a fejlesztői eszközök, az operátoralapú bővítmények és a Red Hat által biztosított tárolólemezképek.
| **`mirror.openshift.com`** | **HTTPS:443** | Ez a VDI-környezetben vagy a laptopon szükséges a tükrözött telepítési tartalmak és lemezképek eléréséhez. A fürtben le kell tölteni a platform kiadási aláírását, hogy tudja, milyen rendszerképeket kell le quay.io. |
| **`api.openshift.com`** | **HTTPS:443** | A fürtnek ellenőriznie kell, hogy vannak-e elérhető frissítések a képaláírások letöltése előtt. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | Belső privát beállításjegyzék az ARO-operátorok számára.  Kötelező, ha nem engedélyezi a Microsoft.ContainerRegistry szolgáltatásvégpontokat az alhálózatán. |
| **`management.azure.com`** | **HTTPS:443** | Ezt a fürt használja az Azure API-k eléréséhez. |
| **`login.microsoftonline.com`** | **HTTPS:443** | Ezt a fürt használja az Azure-beli hitelesítéshez. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | Ez a Microsoft Geno Monitoringhoz használatos, így az ARO csapata figyelni tudja az ügyfél fürt(öke)t. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | Ez a Microsoft Geno Monitoringhoz használatos, így az ARO csapata figyelni tudja az ügyfél fürt(öke)t. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | Ez a Microsoft Geno Monitoringhoz használatos, így az ARO csapata figyelni tudja az ügyfél fürt(öke)t. |
| **`*.table.core.windows.net`** | **HTTPS:443** | Ezt a Microsoft Geno Monitoringhoz használjuk, hogy az ARO csapata figyelni tudja az ügyfél fürt(öke)t. |

> [!NOTE] 
> A *.blob, *.table és más nagy címtereket felfedő ügyfelek számos esetben potenciálisan adatszivárgással kapcsolatos problémát jelentenek. Érdemes lehet az [OpenShift egressziós](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) tűzfalat használni a fürtben üzembe helyezett alkalmazások védelmére, hogy elérjék ezeket a célhelyeket, és Azure Private Link alkalmazás igényeinek megfelelően használja a tűzfalat.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>A kötelező és nem kötelező teljes tartomány (FQDN) teljes listája

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>ELSŐ CSOPORT: CSOMAGOK ÉS ESZKÖZÖK TELEPÍTÉSE ÉS LETÖLTÉSE

- **`quay.io`**: Kötelező a fürt által használt telepítéshez. Ezt a fürt használja a platform tárolólemezképének letöltéséhez.
- **`registry.redhat.io`**: Kötelező az alapvető bővítményekhez. Ezt a fürt az olyan alapvető összetevők letöltésére használja, mint a fejlesztői eszközök, az operátoralapú bővítmények vagy a Red Hat által biztosított tárolólemezképek, például a középső szoftver, az univerzális alapként szolgáló rendszerkép...
- **`sso.redhat.com`**: Erre a VDI-környezetben vagy a laptopon van szükség a virtuális géphez való cloud.redhat.com. Itt tölthetjük le a titkos lehívási titkos jelentéseket, és használhatunk néhány, a Red Hatban elérhető SaaS-megoldást többek között az előfizetések monitorozásához, a fürtkészlethez és a költséghelyi visszacsatolási jelentéskészítéshez.
- **`openshift.org`**: Erre a VDI-környezetben vagy a laptopon van szükség a CONNECT CoreOS-rendszerképek letöltéséhez, de az Azure-ban ezeket a piactérről kell letölteni.

---

### <a name="second-group-telemetry"></a>MÁSODIK CSOPORT: TELEMETRIA

Ezt a szakaszt le lehet választani, de mielőtt meg tudjuk, hogyan, ellenőrizze, hogy mi az: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: VDI- vagy laptopkörnyezetben használja.
- **`api.access.redhat.com`**: VDI- vagy laptopkörnyezetben használja.
- **`infogw.api.openshift.com`**: VDI- vagy laptopkörnyezetben használja.
- **`https://cloud.redhat.com/api/ingress`**: A fürtben használhatja azt az insights operátort, aki integrálható az aaS Red Hat-elemzésekkel.
Az OpenShift-tárolóplatformon az ügyfelek kikapcsolhatja az állapot- és használati adatok jelentését. A csatlakoztatott fürtök azonban lehetővé teszik, hogy a Red Hat gyorsabban reagáljon a problémákra, és jobban támogassa az ügyfeleinket, és jobban megértse, hogyan frissítik a termékfrissítési fürtök. A részleteket itt ellenőrizheti: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>HARMADIK CSOPORT: FELHŐALAPÚ API-K

- **`management.azure.com`**: Ezt használja a fürt az Azure API-k eléréséhez.

---

### <a name="fourth-group-other-openshift-requirements"></a>NEGYEDIK CSOPORT: EGYÉB OPENSHIFT-KÖVETELMÉNYEK

- **`mirror.openshift.com`**: Erre a VDI-környezetben vagy a laptopon van szükség a tükrözött telepítési tartalmak és lemezképek eléréséhez, valamint a fürtön a platform kiadási aláírásának letöltéséhez, amelyet a fürt használ az alkalmazásból lekért rendszerképek quay.io.
- **`storage.googleapis.com/openshift-release`**: Alternatív webhely a platform kiadási aláírásának letöltéséhez, amelyet a fürt használ a lekért rendszerképek quay.io.
- **`*.apps.<cluster_name>.<base_domain>`** (VAGY EGYENÉRTÉKŰ ARO URL-cím): A tartományok engedélyezésekor a rendszer ezt használja a vállalati hálózaton az OpenShiftben üzembe helyezett alkalmazások eléréséhez vagy az OpenShift-konzol eléréséhez.
- **`api.openshift.com`**: A fürtnek ellenőriznie kell, hogy vannak-e elérhető frissítések a képaláírások letöltése előtt.
- **`registry.access.redhat.com`**: A VDI- vagy laptopkörnyezetben beállításjegyzék-hozzáférésre van szükség a fejlesztői lemezképek letöltéséhez az ODO CLI eszköz használata esetén. (Ez a CLI-eszköz egy alternatív CLI-eszköz olyan fejlesztők számára, akik nem ismerik a Kubernetes-t). https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>ÖTÖDIK CSOPORT: MICROSOFT & RED HAT ARO MONITORING SERVICE

- **`login.microsoftonline.com`**: Ezt a fürt használja az Azure-beli hitelesítéshez.
- **`gcs.prod.monitoring.core.windows.net`**: Ez a Microsoft Geneva Monitoringhoz használatos, így az ARO csapata figyelni tudja az ügyfél fürt(öke)t.
- **`*.blob.core.windows.net`**: Ez a Microsoft Geneva Monitoringhoz használatos, így az ARO csapata figyelni tudja az ügyfél fürt(öke)t.
- **`*.servicebus.windows.net`**: Ez a Microsoft Geneva Monitoringhoz használatos, így az ARO csapata figyelni tudja az ügyfél fürt(öke)t.
- **`*.table.core.windows.net`**: Ez a Microsoft Geneva Monitoringhoz használatos, így az ARO csapata figyelni tudja az ügyfél fürt(öke)t.

## <a name="aro-integrations"></a>ARO-integrációk

### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

A tárolókhoz kétféle Azure Monitor biztosít hozzáférést: engedélyezheti az Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  vagy a szükséges teljes tartománynév/alkalmazásszabályok hozzáférését.  Az alábbi utasításokat [követve](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) adhat hozzá Azure Monitor meglévő ARO-fürthöz.

#### <a name="required-network-rules"></a>Szükséges hálózati szabályok

A következő FQDN/ alkalmazásszabályok szükségesek:

| Célvégpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Ezzel a végponttal metrikaadatokat és naplókat küldhet a Azure Monitor Log Analyticsbe. |

#### <a name="required-fqdn--application-rules"></a>Szükséges FQDN/alkalmazásszabályok

Az alábbi FQDN/ alkalmazásszabályok szükségesek az olyan ARO-fürtökhöz, amelyeken engedélyezve van Azure Monitor tárolókhoz való teljes tartomány:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Ez a végpont metrikákhoz és telemetria monitorozáshoz használatos Azure Monitor. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Ezt a végpontot a Azure Monitor log analytics-adatokhoz használja. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Ezt a végpontot az omsagent használja, amely a Log Analytics szolgáltatás hitelesítésére használatos. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Ezzel a végponttal metrikaadatokat küldhet a Azure Monitor. |


## <a name="private-aro-cluster-setup"></a>Privát ARO-fürt beállítása
A cél az ARO-fürt biztonságossá teése a kimenő forgalom átirányítása egy Azure Firewall
### <a name="before"></a>Előtte:
![Előtte](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Utána:
![Utána](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Privát ARO-fürt létrehozása

### <a name="set-up-vars-for-your-environment"></a>VARS beállítása a környezethez
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Két üres alhálózat hozzáadása a virtuális hálózathoz
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Tiltsa le a hálózati szabályzatokat Private Link szolgáltatáshoz a virtuális hálózaton és az alhálózaton. Ez a fürt eléréséhez és kezeléséhez szükséges követelmény az ARO szolgáltatás számára.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Tűzfal alhálózatának létrehozása
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Jump-host virtuális gép létrehozása
### <a name="create-a-jump-subnet"></a>Jump-subnet létrehozása
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Jump-host virtuális gép létrehozása
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift-fürt létrehozása
### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat lekért titkos secret (nem kötelező)

A Red Hat lekért titkos elérés lehetővé teszi, hogy a fürt hozzáférjen a Red Hat tárolóregisztrálókhoz és más tartalmakhoz. Ez a lépés nem kötelező, de ajánlott.

1. **[Nyissa meg a Red Hat OpenShift-fürtkezelő portált, és](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) jelentkezzen be.**

   Be kell jelentkeznie a Red Hat-fiókjába, vagy létre kell hoznia egy új Red Hat-fiókot az üzleti e-mail-címével, és el kell fogadnia a használati feltételeket.

2. **Kattintson a Download pull secret (Titkos lekért titkos adatok letöltése) gombra.**

A mentett fájlt tartsa biztonságos helyen , mert minden fürt létrehozásakor `pull-secret.txt` használni fogja.

A parancs `az aro create` futtatásakor hivatkozhat a lekért titkos útjára a `--pull-secret @pull-secret.txt` paraméterrel. Hajtsa `az aro create` végre a fájlt abból a könyvtárból, amelyben a fájlt `pull-secret.txt` tárolta. Ellenkező esetben cserélje le a `@pull-secret.txt` helyére a `@<path-to-my-pull-secret-file` következőt: .

Ha a lekért titkos kódot másolja vagy más szkriptekbe hivatkozik, a lekért titkos kódot érvényes JSON-sztringként kell formázni.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Hozzon létre egy Azure Firewall

### <a name="create-a-public-ip-address"></a>Nyilvános IP-cím létrehozása
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Frissítés a Azure Firewall bővítményhez
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Ip Azure Firewall konfigurálása és konfigurálása
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>A Azure Firewall IP-k rögzítése későbbi használatra
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>UDR és útválasztási táblázat létrehozása a Azure Firewall
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Alkalmazásszabályok hozzáadása a Azure Firewall
Az OpenShiftre vonatkozó szabály a következő lista [alapján működik:](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Docker-rendszerképek választható szabályai:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>ARO-alhálózatok társítása az FW-hez
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>A jumpbox konfigurációjának tesztelése
Ezek a lépések csak akkor működnek, ha Docker-rendszerképekhez adott hozzá szabályokat. 
### <a name="configure-the-jumpbox"></a>A jumpbox konfigurálása
Jelentkezzen be egy jumpbox virtuális gépre, és telepítse a `azure-cli` `oc-cli` , és `jq` segédprogramokat. Az openshift-cli telepítéséhez tekintse meg a Red Hat ügyfélportálját.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Jelentkezzen be az ARO-fürtbe
Fürt hitelesítő adatainak felsorolása:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
API-kiszolgálóvégpont lekérte:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Az oc CLI letöltése a jumpboxba
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Jelentkezzen be a `oc login` használatával:
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>A CentOS futtatása a külső kapcsolatok teszteléséhez
Pod létrehozása
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Ha a pod fut, futtason bele egy exec-fájlt, és tesztelje a külső kapcsolatot.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Hozzáférés a magánfürt webkonzoljához

### <a name="set-up-ssh-forwards-commands"></a>SSH Forwards-parancsok beállítása

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Az stb. gazdagépfájl módosítása a helyi gépen
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Sshuttle használata másik lehetőségként

[SSHuttle (SSHuttle)](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```
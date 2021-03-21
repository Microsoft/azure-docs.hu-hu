---
title: A OpenShift Container platform 3,11 üzembe helyezése az Azure-ban
description: A OpenShift Container platform 3,11 üzembe helyezése az Azure-ban.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: ce3f56530a7bad6eecd4c2edd3b21debe8c75a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214150"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>A OpenShift Container platform 3,11 üzembe helyezése az Azure-ban

A OpenShift Container platform 3,11 Azure-beli üzembe helyezésének számos módszerét használhatja:

- Manuálisan is telepítheti a szükséges Azure-infrastruktúra-összetevőket, majd követheti a [OpenShift-tároló platformjának dokumentációját](https://docs.openshift.com/container-platform).
- Használhat egy meglévő [Resource Manager-sablont](https://github.com/Microsoft/openshift-container-platform/) is, amely leegyszerűsíti a OpenShift-tároló platform fürt üzembe helyezését.
- Egy másik lehetőség az [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)használata.

Az összes beállításhoz szükség van egy Red Hat-előfizetésre. Az üzembe helyezés során a Red Hat Enterprise Linux példány regisztrálva van a Red Hat-előfizetésben, és ahhoz a készlet-AZONOSÍTÓhoz van csatolva, amely tartalmazza a jogosultságokat a OpenShift-tároló platformhoz.
Győződjön meg arról, hogy rendelkezik érvényes Red Hat Subscription Manager-(RHSM-) felhasználónévvel, jelszóval és készlet-AZONOSÍTÓval. Használhatja az aktiválási kulcsot, a szervezeti azonosítót és a készlet AZONOSÍTÓját. Ezen információk ellenőrzéséhez jelentkezzen be a alkalmazásba https://access.redhat.com .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Üzembe helyezés a OpenShift Container platform Resource Manager 3,11 sablon használatával

### <a name="private-clusters"></a>Privát fürtök

A privát OpenShift-fürtök üzembe helyezéséhez többre van szükség, mint a Master Load Balancerhez (webkonzol) vagy az infra Load Balancerhez (útválasztó) tartozó nyilvános IP-címekhez való hozzáférés.  A privát fürtök általában egy egyéni DNS-kiszolgálót (nem az alapértelmezett Azure DNS), egy egyéni tartománynevet (például contoso.com) és előre definiált virtuális hálózatot (k) használnak.  A privát fürtök esetében előre be kell állítania a virtuális hálózatot az összes megfelelő alhálózattal és a DNS-kiszolgáló beállításaival.  Ezután a **existingMasterSubnetReference**, a **existingInfraSubnetReference**, a **existingCnsSubnetReference** és a **existingNodeSubnetReference** használatával adja meg a fürt által használt meglévő alhálózatot.

Ha a titkos főkiszolgáló van kiválasztva (**masterClusterType**= Private), a **masterPrivateClusterIp** statikus magánhálózati IP-címet kell megadni.  Ezt az IP-címet a Master Load Balancer elülső végéhez rendeli a rendszer.  Az IP-nek a fő alhálózat CIDR belül kell lennie, és nincs használatban.  a **masterClusterDnsType** "Custom" értékre kell beállítani, és a **masterClusterDns** számára meg kell adni a fő DNS-nevet.  A DNS-névnek a statikus magánhálózati IP-címhez kell tartoznia, és a konzol a főcsomópontokon való elérésére lesz használva.

Ha privát útválasztó van kiválasztva (**routerClusterType**= Private), meg kell adni egy statikus magánhálózati IP-címet a **routerPrivateClusterIp** számára.  Ezt az IP-címet az infra Load Balancer elülső végéhez rendeli hozzá a rendszer.  Az IP-nek az infra-alhálózat CIDR belül kell lennie, és nincs használatban.  a **routingSubDomainType** az "egyéni" értékre kell beállítani, és az Útválasztás helyettesítő karakteres DNS-nevét meg kell adni a **routingSubDomain** számára.  

Ha a Private Masters és a Private router van kiválasztva, akkor az egyéni tartománynevet is meg kell adni a **tartománynévhez** .

A sikeres üzembe helyezést követően a megerősített csomópont az egyetlen olyan nyilvános IP-címmel rendelkező csomópont, amelybe SSH-t használhat.  Még akkor is, ha a főcsomópontok nyilvános hozzáférésre vannak konfigurálva, nem teszik elérhetővé SSH-hozzáférést.

A Resource Manager-sablonnal történő üzembe helyezéshez egy Parameters-fájl segítségével adja meg a bemeneti paramétereket. Az üzembe helyezés további testreszabásához a GitHub-tárházat kell megváltoztatnia, és módosítani a megfelelő elemeket.

Néhány gyakori testreszabási lehetőség, de nem korlátozódik a következőkre:

- Megerősített VM-méret (változó azuredeploy.json)
- Elnevezési konvenciók (változók a azuredeploy.json)
- OpenShift-fürtök, a Hosts fájlon keresztül módosítva (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>A Parameters fájl konfigurálása

A [OpenShift Container platform sablon](https://github.com/Microsoft/openshift-container-platform) több ág elérhető a OpenShift Container platform különböző verzióihoz.  Igény szerint közvetlenül a tárházból is üzembe helyezhető, vagy a tárházat leválaszthatja, és egyéni módosításokat végezhet a sablonokon vagy parancsfájlokon a telepítés előtt.

Használja a `appId` paraméterhez korábban létrehozott szolgáltatásnév értékét `aadClientId` .

Az alábbi példa egy azuredeploy.parameters.jsnevű paramétereket mutat be az összes szükséges bemenettel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Cserélje le a paramétereket a megadott adatokra.

A különböző kiadások különböző paraméterekkel rendelkezhetnek, ezért ellenőrizhetik a használt ág szükséges paramétereit.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.jsfájl magyarázata

| Tulajdonság | Leírás | Érvényes beállítások | Alapértelmezett érték |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Összetevők URL-címe (JSON, szkriptek stb.) |  |  https: \/ /RAW.githubusercontent.com/Microsoft/openshift-Container-platform/Master  |
| `location` | Azure-régió az erőforrások üzembe helyezéséhez |  |  |
| `masterVmSize` | A fő virtuális gép mérete. Válasszon a azuredeploy.jsfájljában felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_E2s_v3 |
| `infraVmSize` | Az infra virtuális gép mérete. Válasszon a azuredeploy.jsfájljában felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_D4s_v3 |
| `nodeVmSize` | Az alkalmazás-csomópont virtuális gép mérete. Válasszon a azuredeploy.jsfájljában felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_D4s_v3 |
| `cnsVmSize` | A tároló natív tárolási (CNS) csomópontjának mérete (VM). Válasszon a azuredeploy.jsfájljában felsorolt engedélyezett virtuálisgép-méretek közül |  | Standard_E4s_v3 |
| `osImageType` | A használni kívánt RHEL-rendszerkép. defaultgallery: igény szerint; piactér: harmadik féltől származó rendszerkép | defaultgallery <br> piactér | defaultgallery |
| `marketplaceOsImage` | Ha `osImageType` a a piactér, adja meg a Piactéri ajánlat "kiadó", "ajánlat", "SKU", "version" megfelelő értékeit. Ez a paraméter egy objektumtípus |  |  |
| `storageKind` | A használandó tároló típusa  | felügyelt<br> felügyelt | felügyelt |
| `openshiftClusterPrefix` | Az összes csomóponthoz tartozó állomásnevek konfigurálásához használt fürt-előtag.  1 és 20 karakter között |  | mycluster |
| `minoVersion` | A OpenShift Container platform 3,11-es verziójának másodlagos verziója üzembe helyezéshez |  | 69 |
| `masterInstanceCount` | Telepítendő Masters csomópontok száma | 1, 3, 5 | 3 |
| `infraInstanceCount` | Telepítendő infra-csomópontok száma | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Telepítendő csomópontok száma | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Telepítendő CNS-csomópontok száma | 3, 4 | 3 |
| `osDiskSize` | A virtuális gép operációsrendszer-lemezének mérete (GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | A Docker-kötet csomópontjaihoz csatolni kívánt adatlemez mérete (GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | A glusterfs által használt CNS-csomópontokhoz csatolni kívánt adatlemez mérete (GB-ban) | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Rendszergazdai Felhasználónév az operációs rendszer (VM) és a kezdeti OpenShift-felhasználó számára |  | ocpadmin |
| `enableMetrics` | Metrikák engedélyezése A metrikák több erőforrást igényelnek, ezért az infra VM megfelelő méretének kiválasztása | true <br> hamis | hamis |
| `enableLogging` | Naplózás engedélyezése. a elasticsearch Pod 8 GB RAM memóriát igényel, ezért az infra VM megfelelő méretének kiválasztása | true <br> hamis | hamis |
| `enableCNS` | Tároló natív tárolójának engedélyezése | true <br> hamis | hamis |
| `rhsmUsernameOrOrgId` | Red Hat előfizetés-kezelő Felhasználónév vagy szervezet azonosítója |  |  |
| `rhsmPoolId` | A Red Hat előfizetés-kezelői készlet azonosítója, amely tartalmazza a számítási csomópontok OpenShift jogosultságait |  |  |
| `rhsmBrokerPoolId` | A Red Hat előfizetés-kezelői készlet azonosítója, amely tartalmazza a OpenShift jogosultságokat a főkiszolgálók és az infra-csomópontok számára. Ha nem rendelkezik különböző készlet-azonosítókkal, adja meg a készlet AZONOSÍTÓját "rhsmPoolId"-ként. |  |
| `sshPublicKey` | Másolja ide a nyilvános SSH-kulcsot |  |  |
| `keyVaultSubscriptionId` | A Key Vault tartalmazó előfizetés előfizetés-azonosítója |  |  |
| `keyVaultResourceGroup` | Az Key Vault tartalmazó erőforráscsoport neve |  |  |
| `keyVaultName` | A létrehozott Key Vault neve |  |  |
| `enableAzure` | Az Azure Cloud Provider engedélyezése | true <br> hamis | true |
| `aadClientId` | Azure Active Directory ügyfél-azonosító más néven az egyszerű szolgáltatásnév alkalmazás-azonosítója |  |  |
| `domainName` | A használni kívánt egyéni tartománynév neve (ha van ilyen). A "None" értékre van állítva, ha nincs teljesen privát fürt üzembe helyezése |  | Nincs |
| `masterClusterDnsType` | A OpenShift webkonzol tartományának típusa. az "default" a Master infra nyilvános IP-cím DNS-címkéjét fogja használni. az "egyéni" beállítással saját nevet adhat meg | alapértelmezett <br> egyéni | alapértelmezett |
| `masterClusterDns` | A OpenShift webkonzolhoz való hozzáféréshez használt egyéni DNS-név, ha az "egyéni" lehetőséget választotta a következőhöz: `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Ha a "nipio" értékre van állítva, a `routingSubDomain` NIP.IO-t fogja használni.  Ha az útválasztáshoz használni kívánt saját tartományt használ, használja az "egyéni" lehetőséget. | nipio <br> egyéni | nipio |
| `routingSubDomain` | Az útválasztáshoz használni kívánt helyettesítő DNS-név, ha az "egyéni" lehetőséget választotta a következőhöz: `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Válassza ki, hogy meglévő Virtual Network szeretne-e használni, vagy új Virtual Network szeretne létrehozni | meglévő <br> Új | Új |
| `virtualNetworkResourceGroupName` | Az új Virtual Network erőforráscsoport neve, ha az "új" lehetőséget választotta a következőhöz: `virtualNetworkNewOrExisting` |  | resourceGroup (). név |
| `virtualNetworkName` | A létrehozandó új Virtual Network neve, ha az "új" lehetőséget választotta a következőhöz: `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Az új virtuális hálózat címzési előtagja |  | 10.0.0.0/14 |
| `masterSubnetName` | A fő alhálózat neve |  | mastersubnet |
| `masterSubnetPrefix` | A fő alhálózathoz használt CIDR – a addressPrefix részhalmazának kell lennie. |  | 10.1.0.0/16 |
| `infraSubnetName` | Az infra alhálózat neve |  | infrasubnet |
| `infraSubnetPrefix` | Az infra alhálózathoz használt CIDR-nak a addressPrefix részhalmazának kell lennie. |  | 10.2.0.0/16 |
| `nodeSubnetName` | A csomópont alhálózatának neve |  | nodesubnet |
| `nodeSubnetPrefix` | A csomópont alhálózatához használt CIDR – a addressPrefix részhalmazának kell lennie. |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Teljes hivatkozás a főcsomópontok meglévő alhálózatára. Új vNet/alhálózat létrehozásakor nem szükséges |  |  |
| `existingInfraSubnetReference` | Teljes hivatkozás az infra-csomópontok meglévő alhálózatára. Új vNet/alhálózat létrehozásakor nem szükséges |  |  |
| `existingCnsSubnetReference` | Teljes hivatkozás a meglévő alhálózatra a CNS-csomópontok számára. Új vNet/alhálózat létrehozásakor nem szükséges |  |  |
| `existingNodeSubnetReference` | Teljes hivatkozás a meglévő alhálózatra a számítási csomópontok számára. Új vNet/alhálózat létrehozásakor nem szükséges |  |  |
| `masterClusterType` | Annak megadása, hogy a fürt magán-vagy nyilvános főkiszolgálói csomópontokat használ-e. Ha a Private lehetőséget választotta, a fő csomópontok nem lesznek elérhetők az interneten nyilvános IP-címen keresztül. Ehelyett a saját IP-címet fogja használni a `masterPrivateClusterIp` | nyilvános <br> titkos | nyilvános |
| `masterPrivateClusterIp` | Ha a titkos főcsomópontok ki vannak választva, akkor meg kell adni egy magánhálózati IP-címet a főcsomópontok belső terheléselosztó általi használatra. Ennek a statikus IP-nek a főalhálózat CIDR-blokkjában kell lennie, és még nincs használatban. Ha a nyilvános főkiszolgálói csomópontok ki vannak választva, ez az érték nem lesz használatban, de még meg kell adni. |  | 10.1.0.200 |
| `routerClusterType` | Annak megadása, hogy a fürt magán-vagy nyilvános infra-csomópontokat használ-e. Ha a Private lehetőséget választotta, az infra-csomópontok nem lesznek elérhetők az interneten nyilvános IP-címen keresztül. Ehelyett a saját IP-címet fogja használni a `routerPrivateClusterIp` | nyilvános <br> titkos | nyilvános |
| `routerPrivateClusterIp` | Ha privát infra-csomópontok vannak kiválasztva, akkor meg kell adni egy magánhálózati IP-címet, amelyet az infra-csomópontok belső terheléselosztó használ. Ennek a statikus IP-nek az infra-alhálózat CIDR-blokkjában kell lennie, és még nincs használatban. Ha a nyilvános infra-csomópontok ki vannak választva, ez az érték nem lesz használatban, de továbbra is meg kell adni. |  | 10.2.0.200 |
| `routingCertType` | Egyéni tanúsítvány használata az útválasztási tartományhoz vagy az alapértelmezett önaláírt tanúsítványhoz – kövesse az **Egyéni tanúsítványok** szakasz utasításait. | selfsigned <br> egyéni | selfsigned |
| `masterCertType` | Egyéni tanúsítvány használata a főtartományhoz vagy az alapértelmezett önaláírt tanúsítványhoz – kövesse az **Egyéni tanúsítványok** szakasz utasításait. | selfsigned <br> egyéni | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

> [!NOTE] 
> A következő parancshoz Azure CLI-2.0.8 vagy újabb verzió szükséges. A CLI-verziót a `az --version` paranccsal ellenőrizheti. A CLI verziójának frissítéséhez lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

A következő példa a OpenShift-fürtöt és az összes kapcsolódó erőforrást egy openshiftrg nevű erőforráscsoporthoz helyezi üzembe a myOpenShiftCluster központi telepítési nevével. A sablon közvetlenül a GitHub-tárházból van hivatkozva, és a azuredeploy.parameters.jsfájl nevű helyi Parameters fájl van használatban.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés legalább 60 percet vesz igénybe a telepített csomópontok száma és a konfigurált beállítások alapján. A megerősített DNS teljes tartományneve és a OpenShift-konzol URL-címe kinyomtatja a terminált a telepítés befejeződése után.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Ha nem szeretné összekapcsolni a parancssort, amíg a telepítés befejeződik, adja hozzá a `--no-wait` csoport központi telepítésének egyik beállítását. A központi telepítés kimenete az erőforráscsoport telepítési szakaszában található Azure Portalból kérhető le.

## <a name="connect-to-the-openshift-cluster"></a>Kapcsolódás a OpenShift-fürthöz

Az üzembe helyezés befejezésekor a rendszer lekéri a kapcsolódást a központi telepítés kimenet szakaszából. Kapcsolódjon a OpenShift-konzolhoz a böngészőben a **OpenShift-konzol URL-címének** használatával. Azt is megteheti, hogy SSH-t használ a megerősített gazdagépen. Az alábbi példa egy olyan példát mutat be, ahol a rendszergazdai Felhasználónév clusteradmin, a megerősített nyilvános IP-cím DNS teljes tartományneve pedig bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az az [Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot, a OpenShift-fürtöt és az összes kapcsolódó erőforrást, ha már nincs rájuk szükség.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [A OpenShift üzembe helyezésének hibája az Azure-ban](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift-tároló platform – első lépések](https://docs.openshift.com)

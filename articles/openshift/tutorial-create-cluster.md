---
title: Oktatóanyag – 4-es Azure Red Hat OpenShift létrehozása
description: Megtudhatja, hogyan hozhat létre Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: dda4fc6a80bbe07977f8d2a5ffcbea895a4e1fe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771838"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Oktatóanyag: 4 Azure Red Hat OpenShift létrehozása

Ebben az oktatóanyagban, amely a harmadik rész első része, előkészítjük a környezetet egy OpenShift 4-et futtató Azure Red Hat OpenShift-fürt létrehozására, valamint egy fürt létrehozására. A következőket fogja megtanulni:
> [!div class="checklist"]
> * Az előfeltételek beállítása 
> * A szükséges virtuális hálózat és alhálózatok létrehozása
> * Fürt üzembe helyezése

## <a name="before-you-begin"></a>Előkészületek

Ha a CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Azure Red Hat OpenShift OpenShift-fürt létrehozásához és futtatásához legalább 40 mag szükséges. Az új Azure-előfizetések alapértelmezett Azure-erőforráskvótája nem felel meg ennek a követelménynek. Az erőforráskorlát növelésének kérését lásd: [Standard kvóta: Korlátok növelése virtuálisgép-sorozatok szerint.](../azure-portal/supportability/per-vm-quota-requests.md)

Az ARO lekért titkos gombra való váltás nem módosítja az ARO OPENShift-licencének költségét.

### <a name="verify-your-permissions"></a>Engedélyek ellenőrzése

Az oktatóanyag során létre fog hozni egy erőforráscsoportot, amely a fürt virtuális hálózatát fogja tartalmazni. Közreműködői és felhasználói hozzáférés-rendszergazdai engedélyekkel, vagy tulajdonosi engedélyekkel kell rendelkeznie közvetlenül a virtuális hálózaton, vagy az azt tartalmazó erőforráscsoporton vagy előfizetésen.

Emellett elegendő engedélyre Azure Active Directory az eszközök számára ahhoz, hogy a fürt nevében létrehoz egy alkalmazást és egy szolgáltatásnévt.

### <a name="register-the-resource-providers"></a>Az erőforrás-szolgáltatók regisztrálása

1. Ha több Azure-előfizetéssel rendelkezik, adja meg a megfelelő előfizetés-azonosítót:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Regisztrálja az `Microsoft.RedHatOpenShift` erőforrás-szolgáltatót:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```
    
1. Regisztrálja az `Microsoft.Compute` erőforrás-szolgáltatót:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```
    
1. Regisztrálja az `Microsoft.Storage` erőforrás-szolgáltatót:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Regisztrálja az `Microsoft.Authorization` erőforrás-szolgáltatót:

    ```azurecli-interactive
    az provider register -n Microsoft.Authorization --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat lekért titkos tok (nem kötelező)

A Red Hat lekért titkos adatok lehetővé teszik, hogy a fürt további tartalmakkal együtt hozzáférjen a Red Hat tárolóregisztrálókhoz. Ez a lépés nem kötelező, de ajánlott.

1. [Lépjen a Red Hat OpenShift-fürtkezelő portálra, és](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) jelentkezzen be.

   Be kell jelentkeznie a Red Hat-fiókjába, vagy létre kell hoznia egy új Red Hat-fiókot az üzleti e-mail-címével, és el kell fogadnia a használati feltételeket.

1. Kattintson **a Download pull secret (Titkos lekért** titkos adatok letöltése) elemre, és töltsön le egy lekért titkos gombra az ARO-fürthöz való alkalmazáshoz.

    A mentett fájlt `pull-secret.txt` biztonságos helyen kell tartani. A fájlt a rendszer minden fürt létrehozásakor használja, ha Red Hat- vagy minősített partnerek számára mintákat vagy operátorokat tartalmazó fürtöt kell létrehoznia.

    A parancs `az aro create` futtatásakor a paraméterrel hivatkozhat a lekért titkos `--pull-secret @pull-secret.txt` útjára. Hajtsa `az aro create` végre a fájlt abban a könyvtárban, ahol a fájlt `pull-secret.txt` tárolta. Ellenkező esetben cserélje le a `@pull-secret.txt` helyére a `@/path/to/my/pull-secret.txt` következőt: .

    Ha a lekért titkos kódot másolja vagy más szkriptekbe hivatkozik, a lekért titkos kódot érvényes JSON-sztringként kell formázni.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Egyéni tartomány előkészítése a fürthöz (nem kötelező)

A parancs futtatásakor a paraméterrel megadhat egy egyéni tartományt `az aro create` a `--domain foo.example.com` fürthöz.

Ha egyéni tartományt ad meg a fürthöz, vegye figyelembe a következőket:

* A fürt létrehozása után 2 DNS A rekordot kell létrehoznia a DNS-kiszolgálón a `--domain` megadottakhoz:
    * **api** – az API-kiszolgáló IP-címére mutat
    * **\* .apps** – a bejövő forgalom IP-címére mutat
    * Ezeket az értékeket a fürt létrehozása után a következő parancs végrehajtásával lehet lekérni: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* Az OpenShift-konzol a beépített tartomány helyett egy URL-címen (például ) `https://console-openshift-console.apps.example.com` lesz `https://console-openshift-console.apps.<random>.<location>.aroapp.io` elérhető.

* Alapértelmezés szerint az OpenShift önaírt tanúsítványokat használ az egyéni tartományokon létrehozott összes `*.apps.example.com` útvonalhoz.  Ha a fürthöz való csatlakozás után egyéni DNS-t használ, az OpenShift dokumentációjában konfigurálnia kell egy egyéni hitelesítésszolgáltatót [a](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) bejövő vezérlőhöz és egy egyéni hitelesítésszolgáltatót az [API-kiszolgálóhoz.](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html)

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Két üres alhálózatot tartalmazó virtuális hálózat létrehozása

A következő lépés egy virtuális hálózat létrehozása, amely két üres alhálózatot tartalmaz. Ha már van olyan virtuális hálózata, amely megfelel az igényeinek, kihagyhatja ezt a lépést.

1. **Állítsa be a következő változókat a rendszerhéj-környezetben, amelyben a `az` parancsokat fogja végrehajtani.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Hozzon létre egy erőforráscsoportot.**

   Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ezen a helyen tárolja a rendszer az erőforráscsoport metaadatait, és az erőforrások is itt futnak az Azure-ban, ha nem ad meg másik régiót az erőforrás létrehozása során. Hozzon létre egy erőforráscsoportot [az az group create paranccsal.](/cli/azure/group#az_group_create)
    
   > [!NOTE] 
   > Azure Red Hat OpenShift nem érhető el minden olyan régióban, ahol azure-erőforráscsoportot lehet létrehozni. Az [Elérhető régiók](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) oldalon további információt Azure Red Hat OpenShift támogatott régiókról.

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   Az alábbi példakimeneten a sikeresen létrehozott erőforráscsoport látható:

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **Hozzon létre egy virtuális hálózatot.**

   Azure Red Hat OpenShift OpenShift 4-et futtató fürtökhöz két üres alhálózattal való virtuális hálózatra van szükség a fő- és munkavégző csomópontokhoz. Ehhez létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévő virtuális hálózatot.

   Hozzon létre egy új virtuális hálózatot a korábban létrehozott erőforráscsoportban:

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   Az alábbi példakimeneten a sikeresen létrehozott virtuális hálózat látható:

   ```json
   {
     "newVNet": {
       "addressSpace": {
         "addressPrefixes": [
           "10.0.0.0/22"
         ]
       },
       "dhcpOptions": {
         "dnsServers": []
       },
       "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
       "location": "eastus",
       "name": "aro-vnet",
       "provisioningState": "Succeeded",
       "resourceGroup": "aro-rg",
       "type": "Microsoft.Network/virtualNetworks"
     }
   }
   ```

3. **Adjon hozzá egy üres alhálózatot a fő csomópontokhoz.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name master-subnet \
     --address-prefixes 10.0.0.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

4. **Adjon hozzá egy üres alhálózatot a munkavégző csomópontokhoz.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name worker-subnet \
     --address-prefixes 10.0.2.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

5. **[Tiltsa le az alhálózat privát végpontjának szabályzatát](../private-link/disable-private-link-service-network-policy.md) a fő alhálózaton.** Erre azért van szükség, hogy a szolgáltatás csatlakozni tud a fürthöz, és kezelni tudja azt.

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>A fürt létrehozása

Futtassa a következő parancsot egy fürt létrehozásához. Ha az alábbi lehetőségek valamelyikét választja, ennek megfelelően módosítsa a parancsot:
* Ha szükséges, át is használhatja a Red Hat le [pull-titkos](#get-a-red-hat-pull-secret-optional) secret paraméterét, amely lehetővé teszi, hogy a fürt hozzáférjen a Red Hat-tárolóregisztrálókhoz, valamint további tartalmakat is. Adja hozzá `--pull-secret @pull-secret.txt` a argumentumot a parancshoz.
* Egyéni tartományt [is használhat.](#prepare-a-custom-domain-for-your-cluster-optional) Adja hozzá a argumentumot a parancshoz, és cserélje le `--domain foo.example.com` `foo.example.com` a et a saját egyéni tartományára.

> [!NOTE]
> Ha nem kötelező argumentumokat ad hozzá a parancshoz, zárja be a parancs előző sorában található argumentumot záró perjellel.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

A parancs végrehajtása után a fürt létrehozása általában `az aro create` körülbelül 35 percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges virtuális hálózat és alhálózatok létrehozása
> * Fürt üzembe helyezése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Csatlakozás Azure Red Hat OpenShift fürthöz](tutorial-connect-cluster.md)

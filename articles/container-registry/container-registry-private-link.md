---
title: Privát végpont beállítása privát kapcsolattal
description: Állítson be egy privát végpontot egy tároló-beállításjegyzékben, és engedélyezze a hozzáférést egy helyi virtuális hálózat privát kapcsolatán keresztül. A privát kapcsolathoz való hozzáférés a Prémium szolgáltatási szint egyik funkciója.
ms.topic: article
ms.date: 03/31/2021
ms.openlocfilehash: c47eb535163a1a584bc3892da61543bdf2b0f798
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481412"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Privát csatlakozás Azure Container Registryhez a Azure Private Link


Korlátozza a beállításjegyzékhez való hozzáférést azáltal, hogy virtuális hálózat magánhálózati IP-címeit hozzárendeli a beállításjegyzék végpontjaihoz, és a következő [Azure Private Link.](../private-link/private-link-overview.md) A virtuális hálózaton lévő ügyfelek és a beállításjegyzék privát végpontjai közötti hálózati forgalom áthalad a virtuális hálózaton és egy privát kapcsolaton a Microsoft gerinchálózatán, így kiküszöbölve a nyilvános internetről való kitettséget. Private Link privát beállításjegyzék-hozzáférést is lehetővé tesz [](../expressroute/expressroute-introduction.MD) a helyszíni adatbázisból egy Azure ExpressRoute társviszony-létesítésen vagy [EGY VPN-átjárón keresztül.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

A [beállításjegyzék privát végpontjainak DNS-beállításait](../private-link/private-endpoint-overview.md#dns-configuration) konfigurálhatja úgy, hogy a beállítások feloldják a beállításjegyzék számára lefoglalt privát IP-címet. DNS-konfigurációval a hálózatban található ügyfelek és szolgáltatások továbbra is hozzáférhetnek a beállításjegyzékhez a beállításjegyzék teljes tartománynevével, például a *myregistry.azurecr.io.* 

Ez a szolgáltatás a Prémium tároló-beállításjegyzék **szolgáltatási** szinten érhető el. Jelenleg legfeljebb 10 privát végpontot lehet beállítani egy beállításjegyzékhez. A beállításjegyzék szolgáltatásszintekkel és korlátozásokkal kapcsolatos információkért lásd a Azure Container Registry [rétegeket.](container-registry-skus.md)

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Előfeltételek

* A cikkben található Azure CLI-lépések használhatja az Azure CLI 2.6.0-s vagy újabb verzióját. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli]. Vagy futtassa a [következőt a Azure Cloud Shell:](../cloud-shell/quickstart.md).
* Ha még nem rendelkezik tároló-beállításjegyzékpel, hozzon létre egyet (prémium szintű csomag szükséges), és importálja [a](container-registry-import-images.md) nyilvános mintaképet, például a `mcr.microsoft.com/hello-world` Microsoft Container Registry. A regisztrációs adatbázis létrehozásához Azure Portal [az][quickstart-portal] [Azure CLI-t.][quickstart-cli]
* Ha a beállításjegyzékhez való hozzáférést egy másik Azure-előfizetésben található privát kapcsolaton keresztül konfigurálja, regisztrálnia kell az erőforrás-szolgáltatót az Azure Container Registry előfizetésben. Például:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

A cikkben használt Azure CLI-példák a következő környezeti változókat használják. Helyettesítse be a környezetének megfelelő értékeket. Minden példa a Bash-rendszerhéjhoz van formázva:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Privát kapcsolat beállítása – CLI

### <a name="get-network-and-subnet-names"></a>Hálózat- és alhálózatnevek lekért nevei

Ha még nem rendelkezik ilyenekkel, szüksége lesz egy virtuális hálózat és egy alhálózat nevére egy privát kapcsolat beállításához. Ebben a példában ugyanazt az alhálózatot használja a virtuális géphez és a beállításjegyzék privát végpontjának. Sok esetben azonban a végpontot egy külön alhálózatban kell beállítani. 

Virtuális gép létrehozásakor az Azure alapértelmezés szerint létrehoz egy virtuális hálózatot ugyanabban az erőforráscsoportban. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gépnek a *myDockerVM* nevet ad, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, egy *myDockerVMSubnet nevű alhálózattal.* Állítsa be ezeket az értékeket a környezeti változókban az [az network vnet list paranccsal:][az-network-vnet-list]

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Hálózati szabályzatok letiltása az alhálózatban

[Tiltsa le a hálózati házirendeket,](../private-link/disable-private-endpoint-network-policy.md) például a hálózati biztonsági csoportokat a privát végpont alhálózatában. Frissítse az alhálózat konfigurációját [az az network vnet subnet update frissítéssel:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>A privát DNS-zóna konfigurálása

Hozzon [létre egy privát Azure DNS zónát a](../dns/private-dns-privatednszone.md) privát Azure Container Registry-tartományhoz. A későbbi lépésekben DNS-rekordokat fog létrehozni a beállításjegyzék tartományához ebben a DNS-zónában. További információt a cikk későbbi, [DNS-konfigurációs beállításai](#dns-configuration-options)című cikkében talál.

Ha privát zónával szeretné felülbírálni az Azure Container Registry alapértelmezett DNS-feloldási beállítását, a zónának a következő nevet **kell privatelink.azurecr.io.** Futtassa a következő [az network private-dns zone create parancsot][az-network-private-dns-zone-create] a privát zóna létrehozásához:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Társítási hivatkozás létrehozása

Futtassa [az az network private-dns link vnet create futtatásával][az-network-private-dns-link-vnet-create] társítsa a magánzónát a virtuális hálózathoz. Ez a példa létrehoz egy *myDNSLink nevű hivatkozást.*

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Privát beállításjegyzék-végpont létrehozása

Ebben a szakaszban a regisztrációs adatbázis privát végpontját hozza létre a virtuális hálózaton. Először szerezze be a regisztrációs adatbázis erőforrás-azonosítóját:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Futtassa [az az network private-endpoint create parancsot][az-network-private-endpoint-create] a beállításjegyzék privát végpontjának létrehozásához.

Az alábbi példa a *myPrivateEndpoint* végpontot és a *myConnection szolgáltatáskapcsolatot hozza létre.* A végpont tároló-beállításjegyzék-erőforrásának megadásához adja meg a `--group-ids registry` következőt:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>Végpont IP-konfigurációjának lekért száma

A DNS-rekordok konfiguráláshoz szerezze be a privát végpont IP-konfigurációját. Ebben a példában a privát végpont hálózati adapteréhez két magánhálózati IP-cím tartozik a tároló-beállításjegyzékhez: egy a regisztrációs adatbázishoz, egy pedig a beállításjegyzék adatvégpontjának. 

Először futtassa [az az network private-endpoint show futtatását][az-network-private-endpoint-show] a hálózati adapter azonosítójának privát végpontjának lekérdezéséhez:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Az alábbi [az network nic show parancsok][az-network-nic-show] a tároló-beállításjegyzék és a tárolójegyzék adatvégpontjának magánhálózati IP-címeit kapják meg:

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

> [!NOTE]
> Ha a beállításjegyzék [georeplikált,](container-registry-geo-replication.md)akkor minden beállításjegyzék-replika további adatvégpontját lekérdezi.

### <a name="create-dns-records-in-the-private-zone"></a>DNS-rekordok létrehozása a privát zónában

Az alábbi parancsok DNS-rekordokat hoznak létre a privát zónában a beállításjegyzék végpontja és adatvégpontja számára. Ha például van egy *myregistry* nevű regisztrációs adatbázisa a *westeurope* régióban, a végpontok neve `myregistry.azurecr.io` és `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Ha a beállításjegyzék georeplikált, hozzon létre további [DNS-rekordokat](container-registry-geo-replication.md)minden replika adatvégpontjának IP-címéhez.

Először futtassa [az az network private-dns record-set a create][az-network-private-dns-record-set-a-create] egy üres A-rekordhalmaz létrehozásához a beállításjegyzék-végponthoz és az adatvégponthoz:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Futtassa [az az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] parancsot a beállításjegyzék-végpont és az adatvégpont A-rekordjainak létrehozásához:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

A privát kapcsolat konfigurálva van, és készen áll a használatra.

## <a name="set-up-private-link---portal"></a>Privát kapcsolat beállítása – portál

Privát hivatkozást állíthat be a beállításjegyzék létrehozásakor, vagy egy meglévő beállításjegyzékhez való privát kapcsolat hozzáadásakor. A következő lépések azt feltételezik, hogy már rendelkezik egy virtuális géphez beállított virtuális hálózattal és alhálózattal a teszteléshez. Létrehozhat egy új virtuális hálózatot és [alhálózatot is.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint---new-registry"></a>Privát végpont létrehozása – új beállításjegyzék

1. Amikor létrehoz egy beállításjegyzéket a portálon, az Alapvető **beállítások** lap **termékváltozatában** válassza a **Prémium lehetőséget.**
1. Válassza a **Hálózatkezelés** lapot.
1. A **Hálózati kapcsolat részen** válassza a **Privát végpont**+  >  **Hozzáadás lehetőséget.**
1. Adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Adja meg egy meglévő csoport nevét, vagy hozzon létre egy újat.|
    | Name | Adjon meg egy egyedi nevet. |
    | Alforrás |Beállításjegyzék **kiválasztása**|
    | **Hálózat** | |
    | Virtuális hálózat| Válassza ki azt a virtuális hálózatot, ahol a virtuális gép üzembe van helyezni, például *myDockerVMVNET.* |
    | Alhálózat | Válasszon ki egy alhálózatot, például *a myDockerVMSubnet* alhálózatot, ahol a virtuális gép telepítve van. |
    |**saját DNS integráció**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza *az (Új) privatelink.azurecr.io* |
    |||
1. Konfigurálja a fennmaradó beállításjegyzék-beállításokat, majd válassza **az Áttekintés + létrehozás lehetőséget.**

  ![Beállításjegyzék létrehozása privát végponttal](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Privát végpont létrehozása – meglévő beállításjegyzék

1. A portálon lépjen a tároló-beállításjegyzékhez.
1. A **Beállítások alatt** válassza a Hálózat **lehetőséget.**
1. A Privát **végpontok lapon** válassza a **+ Privát végpont lehetőséget.**
1. Az Alapvető **beállítások lapon** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Adja meg egy meglévő csoport nevét, vagy hozzon létre egy újat.|
    | **Példány adatai** |  |
    | Name | Adjon meg egy nevet. |
    |Region|Válasszon régiót.|
    |||
5. Válassza **a Tovább: Erőforrás lehetőséget.**
6. Adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza **a Csatlakozás a címtárban található Azure-erőforráshoz lehetőséget.**|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza **a Microsoft.ContainerRegistry/registries lehetőséget.** |
    | Erőforrás |Válassza ki a regisztrációs adatbázis nevét|
    |Cél-alforrás |Beállításjegyzék **kiválasztása**|
    |||
7. Válassza **a Tovább: Konfiguráció lehetőséget.**
8. Adja meg vagy válassza ki az adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózat**| |
    | Virtuális hálózat| Válassza ki azt a virtuális hálózatot, ahol a virtuális gép üzembe van helyezni, például *myDockerVMVNET.* |
    | Alhálózat | Válasszon ki egy alhálózatot, például *myDockerVMSubnet,* ahol a virtuális gép üzembe van stb. |
    |**saját DNS integráció**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza *az (Új) privatelink.azurecr.io* |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját. 
2. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

A privát végpont létrehozása után a privát zóna DNS-beállításai a portál **Privát** végpontok lapján jelennek meg:

1. A portálon lépjen a tároló-beállításjegyzékhez, és válassza a **Beállítások > lehetőséget.**
1. A Privát **végpontok lapon** válassza ki a létrehozott privát végpontot.
1. Az Áttekintés **lapon** tekintse át a hivatkozásbeállításokat és az egyéni DNS-beállításokat.

  ![Végpont DNS-beállításai](./media/container-registry-private-link/private-endpoint-overview.png)

A privát kapcsolat most már konfigurálva van, és készen áll a használatra.

## <a name="disable-public-access"></a>Nyilvános hozzáférés letiltása

Számos esetben tiltsa le a beállításjegyzék nyilvános hálózatokról való hozzáférését. Ez a konfiguráció megakadályozza, hogy a virtuális hálózaton kívüli ügyfelek elérjék a beállításjegyzék végpontját. 

### <a name="disable-public-access---cli"></a>Nyilvános hozzáférés letiltása – CLI

Ha le szeretné tiltani a nyilvános hozzáférést az Azure CLI használatával, futtassa [az az acr update-et,][az-acr-update] és állítsa a következőre: `--public-network-enabled` `false` . 

> [!NOTE]
> Az `public-network-enabled` argumentumhoz az Azure CLI 2.6.0-s vagy újabb szükséges. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Nyilvános hozzáférés letiltása – portál

1. A portálon lépjen a tároló-beállításjegyzékhez, és válassza a **Beállítások > lehetőséget.**
1. A Nyilvános **hozzáférés lap** Nyilvános hálózati hozzáférés engedélyezése **lapján válassza** a **Letiltva lehetőséget.** Kattintson a **Mentés** gombra.

## <a name="validate-private-link-connection"></a>Privát kapcsolat kapcsolatának ellenőrzése

Ellenőrizze, hogy a privát végpont alhálózatán belüli erőforrások magánhálózati IP-címen keresztül csatlakoznak-e a regisztrációs adatbázishoz, és hogy a megfelelő privát DNS-zónaintegrációval rendelkezik-e.

A privát kapcsolat kapcsolatának érvényesítéséhez SSH-kapcsolaton keresztül létesítsen kapcsolatot a virtuális hálózatban beállított virtuális géppel.

Futtason egy segédprogramot (például vagy ) a regisztrációs adatbázis IP-címének a privát kapcsolaton `nslookup` `dig` keresztüli ki- és ki keresse. Például:

```bash
dig $REGISTRY_NAME.azurecr.io
```

A példakimenet a beállításjegyzék IP-címét mutatja az alhálózat címterületében:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Hasonlítsa össze ezt az eredményt az ugyanazon regisztrációs adatbázis kimenetében megadott nyilvános IP-címmel `dig` egy nyilvános végponton:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Beállításjegyzékbeli műveletek privát kapcsolaton keresztül

Azt is ellenőrizze, hogy végrehajthat-e beállításjegyzék-műveleteket az alhálózat virtuális gépével. Létesítsen SSH-kapcsolatot a virtuális géppel, és az [az acr login futtatásával][az-acr-login] jelentkezzen be a regisztrációs adatbázisba. A virtuálisgép-konfigurációtól függően előfordulhat, hogy a következő parancsokat előtaggal kell előtagként `sudo` használnia.

```bash
az acr login --name $REGISTRY_NAME
```

Hajtson végre olyan beállításjegyzék-műveleteket, mint `docker pull` például egy minta rendszerkép lekérte a beállításjegyzékből. Cserélje le a helyére a beállításjegyzéknek megfelelő rendszerképet és címkét, előtagként a beállításjegyzék bejelentkezési kiszolgálójának nevét `hello-world:v1` (csak kisbetűk):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen leküldi a rendszerképet a virtuális gépre.

## <a name="manage-private-endpoint-connections"></a>Privát végpontkapcsolatok kezelése

Kezelheti egy beállításjegyzék privát végpontkapcsolatát a Azure Portal vagy az [az acr private-endpoint-connection][az-acr-private-endpoint-connection] parancscsoportban lévő parancsokkal. A műveletek közé tartozik a regisztrációs adatbázis privát végpontkapcsolatának jóváhagyása, törlése, listába való felsorolása, elutasítása vagy megjelenítése.

Egy regisztrációs adatbázis privát végpontkapcsolatának listához például futtassa az [az acr private-endpoint-connection list parancsot.][az-acr-private-endpoint-connection-list] Például:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Amikor a cikkben található lépésekkel privát végponti kapcsolatot hoz létre, a beállításjegyzék automatikusan fogadja a beállításjegyzékben Azure RBAC-engedélyekkel rendelkező ügyfelektől és szolgáltatásoktól származó kapcsolatokat. Beállíthatja a végpontot a kapcsolatok manuális jóváhagyásának megkövetelése érdekében. További információ a privát végponti kapcsolatok jóváhagyásáról és elutasításáról: [Privát végponti kapcsolat kezelése.](../private-link/manage-private-endpoint.md)

> [!IMPORTANT]
> Jelenleg, ha töröl egy privát végpontot egy beállításjegyzékből, előfordulhat, hogy törölnie kell a virtuális hálózat privát zónára mutató hivatkozását is. Ha a hivatkozás nem törlődik, az alábbihoz hasonló hibaüzenet jelenhet `unresolvable host` meg: .

## <a name="dns-configuration-options"></a>DNS-konfigurációs beállítások

Ebben a példában a privát végpont integrálható egy alapszintű virtuális hálózathoz társított privát DNS-zónával. Ez a beállítás közvetlenül az Azure által biztosított DNS-szolgáltatás használatával oldja fel a regisztrációs adatbázis nyilvános teljes tartománynevét a virtuális hálózat privát IP-címére. 

A privát kapcsolat további DNS-konfigurációs forgatókönyveket is támogat, amelyek a privát zónát használják, beleértve az egyéni DNS-megoldásokat is. Előfordulhat például, hogy egy egyéni DNS-megoldás van telepítve a virtuális hálózatban, vagy a helyszínen egy olyan hálózatban, amely VPN-átjáróval vagy virtuális Azure ExpressRoute. 

A beállításjegyzék nyilvános teljes tartományának magánhálózati IP-címre való feloldásához egy kiszolgálószintű továbbítónak kell konfigurálnia az Azure DNS szolgáltatáshoz (168.63.129.16). A pontos konfigurációs beállítások és lépések a meglévő hálózatoktól és a DNS-től függenek. Példákért lásd: [Az Azure privát végpont DNS-konfigurációja.](../private-link/private-endpoint-dns.md)

> [!IMPORTANT]
> Ha magas rendelkezésre állás esetén több régióban hozott létre privát végpontokat, javasoljuk, hogy minden régióban külön erőforráscsoportot használjon, és helyezze el benne a virtuális hálózatot és a társított privát DNS-zónát. Ez a konfiguráció azt is megakadályozza, hogy a DNS-feloldás kiszámíthatatlan módon, ugyanazon a privát DNS-zónán osztozik.

### <a name="manually-configure-dns-records"></a>DNS-rekordok manuális konfigurálása

Bizonyos esetekben előfordulhat, hogy az Azure által biztosított privát zóna használata helyett manuálisan kell konfigurálnia a DNS-rekordokat egy privát zónában. Mindenképpen hozzon létre rekordokat a következő végpontok mindegyikéhez: a beállításjegyzék végpontja, a beállításjegyzék adatvégpontja és az adatvégpont minden további regionális replika számára. Ha nem minden rekord van konfigurálva, előfordulhat, hogy a beállításjegyzék nem érhető el.

> [!IMPORTANT]
> Ha később új replikát ad hozzá, manuálisan kell hozzáadnia egy új DNS-rekordot az adatvégponthoz az ebben a régióban. Ha például létrehoz egy *myregistry* replikát a northeurope helyen, adjon hozzá egy rekordot a következő `myregistry.northeurope.data.azurecr.io` számára: .

A DNS-rekordok létrehozásához szükséges teljes tartományneveket és magánhálózati IP-címeket a privát végpont hálózati adaptere társítja. Ezek az információk az Azure CLI-ről vagy a portálról szerezhetőek be:

* Az Azure CLI használatával futtassa [az az network nic show][az-network-nic-show] parancsot. A parancsokról lásd a cikk [korábbi, Végpont IP-konfigurációjának](#get-endpoint-ip-configuration)lekért parancsát.

* A portálon lépjen a privát végpontra, és válassza a **DNS-konfiguráció lehetőséget.**

A DNS-rekordok létrehozása után győződjön meg arról, hogy a beállításjegyzék teljes tartománynevei megfelelően feloldják a saját magánhálózati IP-címeiket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoportban hozta létre, és már nincs rájuk szüksége, törölheti az erőforrásokat egyetlen [az group delete paranccsal:](/cli/azure/group)

```azurecli
az group delete --name $RESOURCE_GROUP
```

Az erőforrások a portálon való megtisztítása érdekében lépjen az erőforráscsoporthoz. Az erőforráscsoport betöltése után kattintson  az Erőforráscsoport törlése elemre az erőforráscsoport és az ott tárolt erőforrások eltávolításához.

## <a name="next-steps"></a>Következő lépések

* További információ a Private Link a Azure Private Link [dokumentációjában.](../private-link/private-link-overview.md)

* Ha egy ügyfél tűzfal mögötti beállításjegyzék-hozzáférési szabályokat kell beállítania, tekintse meg a következőt: Configure rules to access an Azure container registry behind a firewall (Szabályok konfigurálása egy tűzfal mögötti [Azure Container Registry eléréséhez).](container-registry-firewall-access-rules.md)

* [Az Azure privát végpont kapcsolati problémáinak hibaelhárítása](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-network-nic-show]: /cli/azure/network/nic#az-network-nic-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

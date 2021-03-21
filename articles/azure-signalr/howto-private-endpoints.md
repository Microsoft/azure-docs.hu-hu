---
title: Privát végpontok használata
titleSuffix: Azure SignalR Service
description: A privát végpontok áttekintése, amelyekkel biztonságos hozzáférést biztosíthat az Azure Signaler szolgáltatáshoz a virtuális hálózatokból.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682630"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Privát végpontok használata az Azure Signaler szolgáltatáshoz

Az Azure Signaler szolgáltatáshoz [privát végpontokat](../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik a virtuális hálózat (VNet) ügyfelei számára, hogy biztonságosan hozzáférjenek az adataihoz [privát kapcsolaton](../private-link/private-link-overview.md)keresztül. A privát végpont IP-címet használ az Azure Signaler szolgáltatás VNet. A VNet és az Azure Signaler szolgáltatás ügyfelei közötti hálózati forgalom a Microsoft gerinc hálózatán található privát kapcsolaton halad át, ami kiküszöböli a nyilvános internetről való kitettséget.

Az Azure Signaler szolgáltatáshoz tartozó privát végpontok használata lehetővé teszi a következőket:

- A hálózati hozzáférés-vezérléssel biztonságossá teheti az Azure Signaler szolgáltatást, hogy letiltsa az Azure Signaler szolgáltatás nyilvános végpontján lévő összes kapcsolatot.
- A virtuális hálózat (VNet) biztonságának növelésével letilthatja a VNet kiszűrése adatait.
- Biztonságosan csatlakozhat az Azure Signaler-szolgáltatásokhoz olyan helyszíni hálózatokból, amelyek [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [expressroute](../expressroute/expressroute-locations.md) használatával csatlakoznak a VNet.

## <a name="conceptual-overview"></a>Fogalmi áttekintés

![Az Azure Signaler szolgáltatás privát végpontjai – áttekintés](media/howto-private-endpoints/private-endpoint-overview.png)

A privát végpontok egy speciális hálózati adapterek egy Azure-szolgáltatáshoz a [Virtual Networkban](../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot az Azure Signaler szolgáltatáshoz, biztonságos kapcsolatot biztosít a VNet és a szolgáltatás ügyfelei között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és az Azure Signaler szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások zökkenőmentesen csatlakozhatnak az Azure Signaler szolgáltatáshoz a magánhálózati végponton keresztül, **ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni** fognak. A magánhálózati végpontok az Azure Signaler szolgáltatás által támogatott összes protokollal használhatók, beleértve a REST APIt is.

Amikor saját VNet hoz létre egy Azure Signaler szolgáltatáshoz tartozó magánhálózati végpontot, az Azure Signaler szolgáltatás tulajdonosának jóváhagyására vonatkozó kérés érkezik. Ha a privát végpont létrehozását kérő felhasználó az Azure Signaler szolgáltatás tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérelmet automatikusan jóváhagyja.

Az Azure Signaler szolgáltatás tulajdonosai a [Azure Portal](https://portal.azure.com)Azure Signaler szolgáltatásához tartozó *privát végpontok* lapján kezelhetik a részvételi kérelmeket és a privát végpontokat.

> [!TIP]
> Ha csak a privát végponton keresztül szeretné korlátozni az Azure Signaler szolgáltatás elérését, [konfigurálja a hálózati Access Control](howto-network-access-control.md#managing-network-access-control) a nyilvános végponton keresztüli hozzáférés megtagadásához vagy vezérléséhez.

### <a name="connecting-to-private-endpoints"></a>Csatlakozás privát végpontokhoz

A privát végpontot használó VNet lévő ügyfeleknek ugyanazt a kapcsolati karakterláncot kell használniuk az Azure Signaler szolgáltatáshoz, mint a nyilvános végponthoz csatlakozó ügyfelek. A DNS-feloldás alapján automatikusan átirányítja a kapcsolatokat a VNet és az Azure Signaler szolgáltatás között egy privát kapcsolaton keresztül.

> [!IMPORTANT]
> Ugyanazzal a kapcsolati karakterlánccal csatlakozhat az Azure Signaler szolgáltatáshoz privát végpontok használatával, ahogy azt más célra is használni szeretné. Az `privatelink` altartomány URL-címének használatával ne kapcsolódjon az Azure signaler szolgáltatáshoz.

A VNet csatolt [saját DNS-zónát](../dns/private-dns-overview.md) hozunk létre a privát végpontokhoz szükséges frissítésekkel, alapértelmezés szerint. Ha azonban a saját DNS-kiszolgálóját használja, előfordulhat, hogy további módosításokat kell végeznie a DNS-konfigurációban. Az alábbi [DNS-változások](#dns-changes-for-private-endpoints) című szakasz a privát végpontokhoz szükséges frissítéseket ismerteti.

## <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

Privát végpont létrehozásakor az Azure Signaler szolgáltatáshoz tartozó DNS CNAME-erőforrásrekord az előtaggal rendelkező altartományban található aliasra frissül `privatelink` . Alapértelmezés szerint egy [saját DNS-zónát](../dns/private-dns-overview.md)is hozunk létre, amely az `privatelink` altartományhoz tartozik, és a DNS a saját végpontokhoz tartozó erőforrásrekordokat is létrehoz.

Ha az Azure Signaler szolgáltatás tartománynevét a VNet kívülről oldja fel a privát végponttal, az az Azure Signaler szolgáltatás nyilvános végpontját oldja fel. A magánhálózati végpontot üzemeltető VNet feloldva a tartománynév a magánhálózati végpont IP-címére van feloldva.

A fenti ábrán látható példában a "foobar" Azure Signaler szolgáltatáshoz tartozó DNS-erőforrásrekordok a privát végpontot üzemeltető VNet kívülről történő feloldáskor a következők:

| Név                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

Ahogy azt korábban említettük, a hálózati hozzáférés-vezérlés használatával megtagadhatja vagy vezérelheti a VNet kívüli ügyfelek hozzáférését a nyilvános végponton keresztül.

A "foobar" DNS-erőforrásrekordok, amikor a privát végpontot üzemeltető VNet-ügyfél feloldotta a következőt:

| Név                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

Ez a megközelítés lehetővé teszi, hogy az Azure Signaler szolgáltatáshoz **ugyanazt a kapcsolati karakterláncot használja** , mint a privát végpontokat üzemeltető VNet lévő ügyfelek, valamint a VNet kívüli ügyfelek számára.

Ha a hálózaton egyéni DNS-kiszolgálót használ, az ügyfeleknek képesnek kell lenniük az Azure Signaler szolgáltatás végpontjának teljes tartománynevének feloldására a magánhálózati végpont IP-címére. A DNS-kiszolgálót úgy kell konfigurálni, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához, vagy konfigurálja a rekordokat a `foobar.privatelink.service.signalr.net` saját végpont IP-címével.

> [!TIP]
> Egyéni vagy helyszíni DNS-kiszolgáló használatakor a DNS-kiszolgálót úgy kell konfigurálni, hogy az altartományban lévő Azure Signaler szolgáltatás nevét a `privatelink` magánhálózati végpont IP-címére oldja fel. Ezt úgy teheti meg, hogy delegálja az `privatelink` altartományt a VNet magánhálózati DNS-zónájához, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja a DNS-rekordot.

Az Azure Signaler szolgáltatáshoz tartozó privát végpontok ajánlott DNS-zóna neve a következő: `privatelink.service.signalr.net` .

A saját DNS-kiszolgáló magánhálózati végpontok támogatására való konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [A magánhálózati végpontok DNS-konfigurációja](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Hozzon létre egy privát végpontot a Azure Portal új Azure Signaler szolgáltatásával együtt

1. Új Azure-jelző szolgáltatás létrehozásakor válassza a **hálózatkezelés** fület. Válassza ki a **magánhálózati végpontot** kapcsolati módszerként.

    ![Azure szignáló szolgáltatás létrehozása – hálózatkezelés lap](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Kattintson a **Hozzáadás** parancsra. Adja meg az előfizetést, az erőforráscsoportot, a helyet, valamint az új privát végpont nevét. Válassza ki a virtuális hálózatot és az alhálózatot.

    ![Azure Signaler szolgáltatás létrehozása – privát végpont hozzáadása](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Kattintson a **Felülvizsgálat + létrehozás** elemre.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Hozzon létre egy privát végpontot egy meglévő Azure Signal Service-szolgáltatáshoz a Azure Portal

1. Nyissa meg az Azure Signaler szolgáltatást.

1. Kattintson a **privát végponti kapcsolatok** nevű beállítások menüre.

1. Kattintson a felső gombra **+ privát végpontra** .

    ![Privát Endpoint Connections panel](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Adja meg az előfizetést, az erőforráscsoportot, az erőforrás nevét és a régiót az új privát végpont számára.
    
    ![Privát végpont létrehozása – alapismeretek](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Válassza ki a cél Azure Signaler szolgáltatás erőforrását.

    ![Privát végpont létrehozása – erőforrás](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Cél virtuális hálózat kiválasztása

    ![Privát végpont létrehozása – konfiguráció](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Kattintson a **Felülvizsgálat + létrehozás** elemre.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Privát végpont létrehozása az Azure CLI-vel

1. Bejelentkezés az Azure CLI-be
    ```azurecli
    az login
    ```
1. Válassza ki az Azure-előfizetését
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Új erőforráscsoport létrehozása
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. A Microsoft. SignalRService regisztrálása szolgáltatóként
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. Új Azure-jelző szolgáltatás létrehozása
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Virtuális hálózat létrehozása
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Alhálózat hozzáadása
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Virtual Network házirendek letiltása
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. saját DNS zóna hozzáadása
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. saját DNS zóna összekapcsolása Virtual Network
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Privát végpont létrehozása (automatikus jóváhagyás)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Privát végpont létrehozása (manuális kérelem jóváhagyása)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Kapcsolat állapotának megjelenítése
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Ismert problémák

Tartsa szem előtt az Azure Signaler szolgáltatáshoz tartozó privát végpontokkal kapcsolatos alábbi ismert problémákat

### <a name="free-tier"></a>Ingyenes szint

Ingyenes szintű Azure Signaler szolgáltatáshoz nem hozhat létre privát végpontot.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Hozzáférési korlátozások a virtuális hálózatok lévő ügyfelek számára privát végpontokkal

A meglévő privát végpontokkal rendelkező virtuális hálózatok-ügyfelek megkötést érnek el a többi olyan Azure Signaler szolgáltatás-példányhoz, amely privát végpontokkal rendelkezik. Tegyük fel például, hogy egy VNet N1 saját végponttal rendelkezik az Azure Signaler Service-példány S1-hez. Ha az S2-es Azure Signaler szolgáltatáshoz privát végpont tartozik egy VNet N2-ben, akkor az VNet N1-ben lévő ügyfeleknek a privát végpont használatával is el kell érniük az Azure Signaler szolgáltatás S2-et. Ha az Azure Signaler Service S2 nem rendelkezik privát végpontokkal, akkor a VNet N1-ben lévő ügyfelek privát végpont nélkül is hozzáférhetnek az Azure Signaler szolgáltatáshoz az adott fiókban.

Ez a megkötés a DNS-módosítások eredményeként történt, amikor az Azure Signaler szolgáltatás S2 létrehoz egy privát végpontot.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Hálózat biztonsági csoportok szabályai a privát végpontokkal rendelkező alhálózatok esetében

Jelenleg nem konfigurálhatja a [hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md) (NSG) szabályait és a felhasználó által megadott útvonalakat a privát végpontokhoz. A privát végpontot működtető alhálózatra alkalmazott NSG-szabályok a magánhálózati végpontra lesznek alkalmazva. A probléma korlátozott megkerülő megoldásként implementálja a privát végpontok hozzáférési szabályait a forrás alhálózatokon, bár ennél a megközelítésnél magasabb szintű felügyeleti terhelésre lehet szükség.

## <a name="next-steps"></a>Következő lépések

- [Hálózati Access Control konfigurálása](howto-network-access-control.md)
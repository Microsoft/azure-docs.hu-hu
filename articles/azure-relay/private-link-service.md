---
title: Azure Relay integrálása az Azure Private link Service szolgáltatással
description: Ismerje meg, hogyan integrálható Azure Relay az Azure Private link Service használatával
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 13644082160704ba9918e6bd6257fa314bb463a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134381"
---
# <a name="integrate-azure-relay-with-azure-private-link"></a>Azure Relay integrálása az Azure Private-hivatkozással 
Az Azure **Private link Service** lehetővé teszi az Azure-szolgáltatások (például a Azure Relay, a Azure Service Bus, az Azure Event Hubs, az Azure Storage és a Azure Cosmos db) és az Azure által üzemeltetett ügyfél-partner szolgáltatások elérését a virtuális hálózat privát végpontján keresztül. További információ: [Mi az az Azure Private link?](../private-link/private-link-overview.md)

A **privát végpont** egy olyan hálózati adapter, amely lehetővé teszi, hogy a virtuális hálózaton futó munkaterhelések privát és biztonságos módon kapcsolódjanak egy olyan szolgáltatáshoz, amelynek van **privát kapcsolati erőforrása** (például egy továbbító névtér). A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute, VPN-kapcsolatra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom áthalad a Microsoft gerinc hálózatán, és így megszűnik a nyilvános internetről való kitettség. Megadhatja a hozzáférés-vezérlés részletességi szintjét azáltal, hogy engedélyezi a kapcsolódást az adott Azure Relay névterekhez. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Privát végpont hozzáadása a Azure Portal használatával

### <a name="prerequisites"></a>Előfeltételek
Azure Relay névtér Azure Private-hivatkozással való integrálásához a következő entitásokra vagy engedélyekre lesz szüksége:

- Egy Azure Relay névtér.
- Egy Azure-beli virtuális hálózat.
- Egy alhálózat a virtuális hálózaton.
- Tulajdonosi vagy közreműködői engedélyek a virtuális hálózaton.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a portál használatával kiválaszt egy régiót a privát végponthoz, akkor az automatikusan csak az adott régióban lévő virtuális hálózatokat fogja szűrni. A névtér lehet egy másik régióban is.

A privát végpont egy magánhálózati IP-címet használ a virtuális hálózaton.

### <a name="steps"></a>Lépések
Az új Azure Relay névterek és entitások létrehozásával kapcsolatos részletes útmutatásért lásd: [Azure Relay névtér létrehozása a Azure Portal használatával](relay-create-namespace-portal.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A keresősáv mezőbe írja be a következőt: **Relays**.
3. Válassza ki a listából azt a **névteret** , amelyhez privát végpontot szeretne hozzáadni.
4. Válassza a **hálózatkezelés** fület a **Beállítások** területen.
5. Válassza ki a **privát Endpoint Connections** fület az oldal tetején.
6. A lap tetején kattintson a **+ privát végpont** gombra.

    ![Privát végpont hozzáadása gomb](./media/private-link-service/add-private-endpoint-button.png)
7. Az **alapvető beállítások** lapon kövesse az alábbi lépéseket: 
    1. Válassza ki azt az **Azure-előfizetést** , amelyben létre szeretné hozni a privát végpontot. 
    2. Válassza ki a privát végpont erőforráshoz tartozó **erőforráscsoportot** .
    3. Adja meg a magánhálózati végpont **nevét** . 
    5. Válasszon ki egy **régiót** a privát végpont számára. A privát végpontnak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, de a Azure Relay névtértől eltérő régióban lehet, amelyhez csatlakozik. 
    6. Válassza a **Tovább: erőforrás >** gombot az oldal alján.

        ![Privát végpont létrehozása – alapismeretek lap](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Az **erőforrás** oldalon hajtsa végre az alábbi lépéseket:
    1. A kapcsolódási módszer esetén, ha a **címtárban a kapcsolódás Azure-erőforráshoz** lehetőséget választja, a névtérhez tulajdonosi vagy közreműködői hozzáférése van, és a névtér a privát végponttal megegyező könyvtárban található, kövesse az alábbi lépéseket: 
        1. Válassza ki azt az **Azure-előfizetést** , amelyben a **Azure Relay névtere** létezik. 
        2. Az **erőforrástípus** mezőben válassza ki a **Microsoft. Relay/Namespaces** elemet az **erőforrás típushoz**.
        3. Az **erőforrás** mezőben válasszon ki egy Relay-névteret a legördülő listából. 
        4. Győződjön meg arról, hogy a **cél alerőforrás** a **névtérre** van beállítva.
        5. Válassza a **Tovább: konfigurációs >** gombot az oldal alján. 
        
            ![Privát végpont létrehozása – Erőforrás lap](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Ha a **Kapcsolódás Azure-erőforráshoz erőforrás-azonosító vagy alias** alapján lehetőséget választja, mert a névtér nem ugyanabban a könyvtárban található, mint a privát végpont, kövesse az alábbi lépéseket:
        1. Adja meg az **erőforrás-azonosítót** vagy az **aliast**. Ez lehet az az erőforrás-azonosító vagy alias, amelyet valaki megosztott Önnel. Az erőforrás-azonosító beszerzésének legegyszerűbb módja, ha a Azure Portal Azure Relay névterére navigál, és az URI részét másolja a-től kezdődően `/subscriptions/` . Íme egy példa: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. A **cél alerőforrásnál** adja meg a **névteret**. Ez a saját végpont által elérhető alerőforrás típusa.
        3. választható Adja meg a **kérelem üzenetét**. Az erőforrás tulajdonosa látja ezt az üzenetet a privát végponti kapcsolatok kezelése során.
        4. Ezután válassza a **Tovább: konfigurációs >** gombot az oldal alján.

            ![Privát végpont létrehozása – kapcsolat az erőforrás-AZONOSÍTÓval](./media/private-link-service/connect-resource-id.png)
9. A **konfiguráció** lapon válassza ki azt az alhálózatot egy virtuális hálózaton, ahová a magánhálózati végpontot telepíteni szeretné. 
    1. Válasszon ki egy **virtuális hálózatot**. A legördülő listában csak a jelenleg kijelölt előfizetésben és helyen lévő virtuális hálózatok jelennek meg. 
    2. Válasszon egy **alhálózatot** a kiválasztott virtuális hálózatban. 
    3. Ha privát DNS-zónával szeretné integrálni a privát végpontot, engedélyezze az **integrációt a magánhálózati DNS-zónával** . 
    
        A magánhálózati végponthoz való kapcsolódáshoz DNS-rekordra van szükség. Javasoljuk, hogy a privát végpontot egy **privát DNS-zónával** integrálja. Saját DNS-kiszolgálókat is használhat, vagy létrehozhat DNS-rekordokat a virtuális gépeken található gazdagépek használatával. További információ: [Azure Private Endpoint DNS-konfiguráció](../private-link/private-endpoint-dns.md). Ebben a példában a **saját DNS-zóna integrálása** beállítás van kiválasztva, és a rendszer létrehoz egy privát DNS-zónát. 
    3. Válassza a **Tovább: címkék >** gombot az oldal alján. 

        ![Privát végpont létrehozása – konfigurációs lap](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. A **címkék** lapon hozzon létre minden olyan címkét (nevet és értéket), amelyet hozzá szeretne rendelni a privát végponthoz és a magánhálózati DNS-zónához (ha engedélyezte a beállítást). Ezután kattintson az oldal alján található **felülvizsgálat + létrehozás** gombra. 
11. A **felülvizsgálat + létrehozás** lapon tekintse át az összes beállítást, majd kattintson a **Létrehozás** elemre a privát végpont létrehozásához.
    
    ![Privát végpont létrehozása – oldal áttekintése és létrehozása](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. A **privát végpont** lapon láthatja a magánhálózati végponti kapcsolatok állapotát. Ha Ön a továbbítási névtér tulajdonosa, vagy rendelkezik a hozzáférés kezelése lehetőséggel, és kiválasztotta az **Azure-erőforráshoz való kapcsolódást a címtárban** a **kapcsolati módszerhez**, a végponti kapcsolatot **automatikusan jóvá** kell hagyni. Ha **függő** állapotban van, tekintse meg a [privát végpontok kezelése Azure Portal szakasz használatával](#manage-private-endpoints-using-azure-portal) című részt.

    ![Privát végpont lap](./media/private-link-service/private-endpoint-page.png)
13. Lépjen vissza a névtér **hálózatkezelés** lapjára, és váltson a **privát végpont kapcsolatai** lapra. Ekkor meg kell jelennie a létrehozott privát végpontnak. 

    ![Saját végpont létrehozva](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Privát végpont hozzáadása a PowerShell használatával
Az alábbi példa azt mutatja be, hogyan használható a Azure PowerShell egy magánhálózati végponti kapcsolatok Azure Relay névtérhez való létrehozásához.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. A Azure Relay névtér lehet egy másik régióban. A privát végpont pedig egy magánhálózati IP-címet használ a virtuális hálózaton.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Privát végpontok kezelése Azure Portal használatával

Privát végpont létrehozásakor jóvá kell hagyni a kapcsolódást. Ha az erőforrás (továbbítási névtér), amelyhez privát végpontot hoz létre, akkor jóváhagyhatja a kapcsolati kérést, ha a továbbítási névtéren keresztül felügyeli a jogosultságokat. Ha olyan továbbítási névtérhez csatlakozik, amelyhez nincs hozzáférése a kezeléshez, meg kell várnia, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatásművelet | A szolgáltatás fogyasztói magánhálózati végpontjának állapota | Leírás |
|--|--|--|
| Nincs | Függőben | A kapcsolatok létrehozása manuálisan történik, és a Azure Relay névtér tulajdonosának jóváhagyása függőben van. |
| Jóváhagyás | Approved | A kapcsolódás automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A Azure Relay névtér tulajdonosa elutasította a kapcsolatokat. |
| Eltávolítás | Leválasztott | A Azure Relay névtér tulajdonosa törölte a kapcsolatokat, a magánhálózati végpont informatív lesz, és törölni kell a tisztításhoz. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása, elutasítása vagy eltávolítása

1. Jelentkezzen be az Azure Portalra.
1. A keresősáv mezőbe írja be a következőt: **Relay**.
1. Válassza ki a kezelni kívánt **névteret** .
1. Válassza a **Hálózatkezelés** lapot.
5. Nyissa meg az alábbi megfelelő szakaszt a kívánt művelet alapján: jóváhagyás, elutasítás vagy eltávolítás. 

### <a name="approve-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása

1. Ha van függőben lévő kapcsolat, a kiépítési állapotban **függőben lévő** kapcsolat jelenik meg. 
2. Válassza ki a jóváhagyni kívánt **privát végpontot**
3. Kattintson a **jóváhagyás** gombra.

    ![Privát végpont jóváhagyása](./media/private-link-service/private-endpoint-approve.png)
4. A **kapcsolatok jóváhagyása** lapon adjon meg egy nem kötelező **megjegyzést**, és válassza az **Igen** lehetőséget. Ha a **nem** lehetőséget választja, semmi nem történik. 

    ![Kapcsolatok jóváhagyása lap](./media/private-link-service/approve-connection-page.png)
5. A listában a kapcsolatok állapota **Jóváhagyottra** módosult.

### <a name="reject-a-private-endpoint-connection"></a>Privát végponti kapcsolatok elutasítása

1. Ha van olyan privát végponti kapcsolat, amelyet el szeretne utasítani, legyen az egy függőben lévő kérelem vagy korábban jóváhagyott meglévő kapcsolat, válassza ki a végponti kapcsolatot, majd kattintson az **elutasítás** gombra.

    ![Elutasítás gomb](./media/private-link-service/private-endpoint-reject.png)
2. A **kapcsolatok elutasítása** lapon adjon meg egy nem kötelező megjegyzést, és válassza az **Igen** lehetőséget. Ha a **nem** lehetőséget választja, semmi nem történik. 

    ![A kapcsolatok elutasítása lap](./media/private-link-service/reject-connection-page.png)
3. Ekkor meg kell jelennie a listán szereplő kapcsolatok állapotának **.**


### <a name="remove-a-private-endpoint-connection"></a>Privát végponti kapcsolatok eltávolítása

1. Privát végponti kapcsolatok eltávolításához jelölje ki azt a listában, majd az eszköztáron kattintson az **Eltávolítás** elemre. 

    ![Eltávolítás gomb](./media/private-link-service/remove-endpoint.png)
2. A **Kapcsolódás törlése** lapon válassza az **Igen** lehetőséget a privát végpont törlésének megerősítéséhez. Ha a **nem** lehetőséget választja, semmi nem történik. 

    ![A kapcsolatok törlése lap](./media/private-link-service/delete-connection-page.png)
3. A **kapcsolat megszakadt** állapotra módosult. Ezután megjelenik a végpont a listából. 

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a magánhálózati kapcsolat működik-e
Győződjön meg arról, hogy a magánhálózati végpont virtuális hálózatán lévő erőforrások a magánhálózati IP-címén keresztül csatlakoznak a Azure Relay névteréhez.

Ebben a tesztben hozzon létre egy virtuális gépet a [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/windows/quick-create-portal.md) című témakör lépéseit követve.

A **hálózatkezelés** lapon: 

1. Határozza meg a **virtuális hálózatot** és az **alhálózatot**. Ki kell választania azt a Virtual Network, amelyre a magánhálózati végpontot telepítette.
2. **Nyilvános IP-** erőforrást kell megadni.
3. A **NIC hálózati biztonsági csoportban** válassza a **nincs** lehetőséget.
4. A **terheléselosztáshoz** válassza a **nem** lehetőséget.

Kapcsolódjon a virtuális géphez, és nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

A következőhöz hasonló eredményt kell látnia. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Korlátozások és kialakítási szempontok

### <a name="design-considerations"></a>Kialakítási szempontok
- A díjszabással kapcsolatos információkért tekintse meg az [Azure Private link díjszabását](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Korlátozások 
- Privát végpontok maximális száma Azure Relay névtérben: 64.
- Az előfizetéshez tartozó privát végpontokkal rendelkező Azure Relay névterek maximális száma: 64.
- A hálózati biztonsági csoport (NSG) szabályai és a User-Defined útvonalak nem vonatkoznak a privát végpontokra. További információ [: Azure Private link Service: korlátozások](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](../private-link/private-link-service-overview.md)
- További információ a [Azure Relay](relay-what-is-it.md)

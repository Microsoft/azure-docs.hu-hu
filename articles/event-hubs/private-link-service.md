---
title: Az Azure Event Hubs integrálása az Azure Private link Service szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Event Hubs az Azure Private link Service használatával
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: 996779e103dae2d2d950f447d2ac72667fc9e754
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94427751"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Azure Event Hubs-névterek hozzáférésének engedélyezése privát végpontokon keresztül 
Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Event Hubs, az Azure Storage és a Azure Cosmos DB) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat **privát végpontján** keresztül.

A privát végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a virtuális hálózatról, amely hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [Mi az az Azure Private link?](../private-link/private-link-overview.md)

> [!WARNING]
> A privát végpontok engedélyezése megakadályozhatja, hogy más Azure-szolgáltatások a Event Hubs használatával kommunikálnak.  A letiltott kérések közé tartoznak a más Azure-szolgáltatások, a Azure Portal, a naplózási és a metrikai szolgáltatások, valamint így tovább. Kivételként engedélyezheti a hozzáférést bizonyos megbízható szolgáltatásoktól Event Hubs erőforrásaihoz, még akkor is, ha a magánhálózati végpontok engedélyezve vannak. A megbízható szolgáltatások listáját lásd: [megbízható szolgáltatások](#trusted-microsoft-services).

>[!NOTE]
> Ez a funkció mind a **standard** , mind a **dedikált** szint esetében támogatott. Az alapszintű **csomag** nem támogatja.

## <a name="add-a-private-endpoint-using-azure-portal"></a>Privát végpont hozzáadása a Azure Portal használatával

### <a name="prerequisites"></a>Előfeltételek

Event Hubs névtér Azure Private-hivatkozással való integrálásához a következő entitásokra vagy engedélyekre lesz szüksége:

- Egy Event Hubs névtér.
- Egy Azure-beli virtuális hálózat.
- Egy alhálózat a virtuális hálózaton. Használhatja az **alapértelmezett** alhálózatot. 
- A névtérhez és a virtuális hálózathoz tartozó tulajdonosi vagy közreműködői engedélyek.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a portál használatával kiválaszt egy régiót a privát végponthoz, akkor az automatikusan csak az adott régióban lévő virtuális hálózatokat fogja szűrni. A névtér lehet egy másik régióban is.

A privát végpont egy magánhálózati IP-címet használ a virtuális hálózaton.

### <a name="steps"></a>Lépések
Ha már rendelkezik Event Hubs névtérrel, a következő lépések végrehajtásával hozhat létre privát kapcsolati kapcsolatot:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A keresősáv mezőbe írja be az **Event hubok** kifejezést.
3. Válassza ki a listából azt a **névteret** , amelyhez privát végpontot szeretne hozzáadni.
4. A bal oldali menü **Beállítások** területén válassza a **hálózatkezelés** lehetőséget.

    > [!NOTE]
    > A **hálózatkezelés** lap csak a **standard** vagy a **dedikált** névtér esetében jelenik meg. 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Hálózatok lap – kiválasztott hálózatok lehetőség" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > Alapértelmezés szerint a **kiválasztott hálózatok** lehetőség van kiválasztva. Ha nem ad meg IP-tűzfalszabály-szabályt vagy virtuális hálózatot ad hozzá, a névtér a nyilvános interneten keresztül érhető el. 
1. Válassza ki a **privát Endpoint Connections** fület az oldal tetején. 
1. A lap tetején kattintson a **+ privát végpont** gombra.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Hálózatkezelés lap – magánhálózati végponti kapcsolatok lap – privát végponti hivatkozás hozzáadása":::
7. Az **alapvető beállítások** lapon kövesse az alábbi lépéseket: 
    1. Válassza ki azt az **Azure-előfizetést** , amelyben létre szeretné hozni a privát végpontot. 
    2. Válassza ki a privát végpont erőforráshoz tartozó **erőforráscsoportot** .
    3. Adja meg a magánhálózati végpont **nevét** . 
    5. Válasszon ki egy **régiót** a privát végpont számára. A privát végpontnak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, de a privát kapcsolati erőforrástól eltérő régióban lehet, amelyhez csatlakozik. 
    6. Válassza a **Tovább: erőforrás >** gombot az oldal alján.

        ![Privát végpont létrehozása – alapismeretek lap](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Az **erőforrás** oldalon hajtsa végre az alábbi lépéseket:
    1. A kapcsolódási módszer esetén, ha a **címtárban a kapcsolódás Azure-erőforráshoz** lehetőséget választja, kövesse az alábbi lépéseket: 
        1. Válassza ki azt az **Azure-előfizetést** , amelyben a **Event Hubs névtere** létezik. 
        2. Az **erőforrástípus** mezőben válassza ki a **Microsoft. EventHub/Namespaces** elemet az **erőforrás típushoz**.
        3. Az **erőforrás** mezőben válasszon ki egy Event Hubs névteret a legördülő listából. 
        4. Győződjön meg arról, hogy a **cél alerőforrás** a **névtérre** van beállítva.
        5. Válassza a **Tovább: konfigurációs >** gombot az oldal alján. 
        
            ![Privát végpont létrehozása – Erőforrás lap](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Ha a **Kapcsolódás Azure-erőforráshoz erőforrás-azonosító vagy alias alapján** lehetőséget választja, kövesse az alábbi lépéseket:
        1. Adja meg az **erőforrás-azonosítót** vagy az **aliast**. Ez lehet az az erőforrás-azonosító vagy alias, amelyet valaki megosztott Önnel. Az erőforrás-azonosító beszerzésének legegyszerűbb módja, ha a Azure Portal Event Hubs névterére navigál, és az URI részét másolja a-től kezdődően `/subscriptions/` . Példaként tekintse meg az alábbi ábrát. 
        2. A **cél alerőforrásnál** adja meg a **névteret**. Ez a saját végpont által elérhető alerőforrás típusa.
        3. választható Adja meg a **kérelem üzenetét**. Az erőforrás tulajdonosa látja ezt az üzenetet a privát végponti kapcsolatok kezelése során.
        4. Ezután válassza a **Tovább: konfigurációs >** gombot az oldal alján.

            ![Privát végpont létrehozása – kapcsolat az erőforrás-AZONOSÍTÓval](./media/private-link-service/connect-resource-id.png)
9. A **konfiguráció** lapon válassza ki azt az alhálózatot egy virtuális hálózaton, ahová a magánhálózati végpontot telepíteni szeretné. 
    1. Válasszon ki egy **virtuális hálózatot**. A legördülő listában csak a jelenleg kijelölt előfizetésben és helyen lévő virtuális hálózatok jelennek meg. 
    2. Válasszon egy **alhálózatot** a kiválasztott virtuális hálózatban. 
    3. Válassza a **Tovább: címkék >** gombot az oldal alján. 

        ![Privát végpont létrehozása – konfigurációs lap](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. A **címkék** lapon hozzon létre minden olyan címkét (nevet és értéket), amelyet hozzá szeretne rendelni a privát végpont-erőforráshoz. Ezután kattintson az oldal alján található **felülvizsgálat + létrehozás** gombra. 
11. A **felülvizsgálat + létrehozás** lapon tekintse át az összes beállítást, majd kattintson a **Létrehozás** elemre a privát végpont létrehozásához.
    
    ![Privát végpont létrehozása – oldal áttekintése és létrehozása](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Ellenőrizze, hogy megjelenik-e a létrehozott privát végponti kapcsolatok a végpontok listájában. Ebben a példában a privát végpontot automatikusan jóváhagyjuk, mert egy Azure-erőforráshoz kapcsolódott a címtárban, és rendelkezik a megfelelő engedélyekkel. 

    ![Saját végpont létrehozva](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

Ahhoz, hogy a megbízható szolgáltatások hozzáférjenek a névtérhez, váltson a **hálózatkezelés** lapon a **tűzfalak és a virtuális hálózatok** lapra, és válassza az **Igen** lehetőséget a **tűzfal megkerülésének engedélyezése a megbízható Microsoft-szolgáltatások** számára jelölőnégyzetet. 

## <a name="add-a-private-endpoint-using-powershell"></a>Privát végpont hozzáadása a PowerShell használatával
Az alábbi példa bemutatja, hogyan használható a Azure PowerShell egy privát végponti kapcsolatok létrehozásához. Nem hoz létre dedikált fürtöt. A [cikkben](event-hubs-dedicated-cluster-create-portal.md) ismertetett lépéseket követve hozzon létre egy dedikált Event Hubs-fürtöt. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

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

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>A magánhálózati DNS-zóna konfigurálása
Hozzon létre egy magánhálózati DNS-zónát Event Hubs tartományhoz, és hozzon létre egy társítási hivatkozást a virtuális hálózattal:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Privát végpontok kezelése Azure Portal használatával

Privát végpont létrehozásakor jóvá kell hagyni a kapcsolódást. Ha az erőforrás, amelyhez privát végpontot hoz létre, akkor jóváhagyhatja a megfelelő engedélyekkel rendelkező kapcsolatkérelem-kérést. Ha egy másik címtárban található Azure-erőforráshoz csatlakozik, meg kell várnia, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatásművelet | A szolgáltatás fogyasztói magánhálózati végpontjának állapota | Leírás |
|--|--|--|
| Nincs | Függőben | A kapcsolat manuálisan lett létrehozva, és jóváhagyásra vár a Private link erőforrás-tulajdonostól. |
| Jóváhagyás | Approved | A kapcsolódás automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A magánhálózati kapcsolat erőforrásának tulajdonosa elutasította a kapcsolatot. |
| Eltávolítás | Leválasztott | A kapcsolatot a privát kapcsolat erőforrás-tulajdonosa eltávolította, a magánhálózati végpont informatív lesz, és törölni kell a tisztításhoz. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása, elutasítása vagy eltávolítása

1. Jelentkezzen be az Azure Portalra.
2. A keresősáv mezőbe írja be az **Event hubok** kifejezést.
3. Válassza ki a kezelni kívánt **névteret** .
4. Válassza a **Hálózatkezelés** lapot.
5. Nyissa meg az alábbi megfelelő szakaszt a kívánt művelet alapján: jóváhagyás, elutasítás vagy eltávolítás.

### <a name="approve-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása
1. Ha van függőben lévő kapcsolat, a kiépítési állapotban **függőben lévő** kapcsolat jelenik meg. 
2. Válassza ki a jóváhagyni kívánt **privát végpontot**
3. Kattintson a **jóváhagyás** gombra.

    ![Privát végpont jóváhagyása](./media/private-link-service/approve-private-endpoint.png)
4. A **kapcsolatok jóváhagyása** lapon adjon hozzá egy megjegyzést (nem kötelező), majd válassza az **Igen** lehetőséget. Ha a **nem** lehetőséget választja, semmi nem történik. 
5. A privát végponti kapcsolatok állapota a listában a **jóváhagyott** értékre módosult. 

### <a name="reject-a-private-endpoint-connection"></a>Privát végponti kapcsolatok elutasítása

1. Ha van olyan privát végponti kapcsolat, amelyet el szeretne utasítani, legyen az egy függőben lévő kérelem vagy létező kapcsolat, válassza ki a kapcsolatot, és kattintson az **elutasítás** gombra.

    ![Privát végpont elutasítása](./media/private-link-service/private-endpoint-reject-button.png)
2. A **kapcsolatok elutasítása** lapon írjon be egy megjegyzést (nem kötelező), majd válassza az **Igen** lehetőséget. Ha a **nem** lehetőséget választja, semmi nem történik. 
3. A privát végponti kapcsolatok állapota a listában **visszautasította** értékre módosult. 

### <a name="remove-a-private-endpoint-connection"></a>Privát végponti kapcsolatok eltávolítása

1. Privát végponti kapcsolatok eltávolításához jelölje ki azt a listában, majd az eszköztáron kattintson az **Eltávolítás** elemre.
2. A **Kapcsolódás törlése** lapon válassza az **Igen** lehetőséget a privát végpont törlésének megerősítéséhez. Ha a **nem** lehetőséget választja, semmi nem történik.
3. A **kapcsolat megszakadt** állapotra módosult. Ezután megjelenik a végpont a listából.

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a magánhálózati kapcsolat működik-e

Győződjön meg arról, hogy a magánhálózati végpont virtuális hálózatán lévő erőforrások a magánhálózati IP-címeken keresztül csatlakoznak a Event Hubs névtérhez, és a saját DNS-zóna megfelelő integrációja van.

Először hozzon létre egy virtuális gépet a [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/windows/quick-create-portal.md) című témakör lépéseit követve.

A **hálózatkezelés** lapon: 

1. Határozza meg a **virtuális hálózatot** és az **alhálózatot**. Ki kell választania azt a Virtual Network, amelyre a magánhálózati végpontot telepítette.
2. **Nyilvános IP-** erőforrást kell megadni.
3. A **NIC hálózati biztonsági csoportban** válassza a **nincs** lehetőséget.
4. A **terheléselosztáshoz** válassza a **nem** lehetőséget.

Kapcsolódjon a virtuális géphez, nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

A következőhöz hasonló eredményt kell látnia. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Korlátozások és kialakítási szempontok

**Díjszabás**: díjszabási információkért tekintse meg az [Azure Private link díjszabását](https://azure.microsoft.com/pricing/details/private-link/).

**Korlátozások**: Ez a funkció az összes Azure-beli nyilvános régióban elérhető.

**Privát végpontok maximális száma Event Hubs névtérben**: 120.

További információ [: Azure Private link Service: korlátozások](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](../private-link/private-link-service-overview.md)
- További információ az [Azure Event Hubs](event-hubs-about.md)

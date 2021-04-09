---
title: Azure Service Bus integrálása az Azure Private link Service szolgáltatással
description: Ismerje meg, hogyan integrálható Azure Service Bus az Azure Private link Service használatával
author: spelluru
ms.author: spelluru
ms.date: 03/29/2021
ms.topic: article
ms.openlocfilehash: 833d7e9fb4d517b71aab5039ae9081407eed84cd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960537"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>Azure Service Bus névtér elérésének engedélyezése privát végpontokon keresztül
Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Service Bus, az Azure Storage és a Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-és partneri szolgáltatások elérését a virtuális hálózat **privát végpontján** keresztül.

A privát végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [Mi az az Azure Private link?](../private-link/private-link-overview.md)

## <a name="important-points"></a>Fontos pontok
- Ez a funkció a Azure Service Bus **Premium** szintjével támogatott. A prémium szintű csomaggal kapcsolatos további információkért tekintse meg a [prémium és standard szintű üzenetkezelési szintek Service Busét](service-bus-premium-messaging.md) ismertető cikket.
- A privát végpontok megvalósítása megakadályozhatja, hogy más Azure-szolgáltatások is interakciót Service Bus. Kivételként engedélyezheti a hozzáférést bizonyos **megbízható szolgáltatásoktól** Service Bus erőforrásaihoz, még akkor is, ha a magánhálózati végpontok engedélyezve vannak. A megbízható szolgáltatások listáját lásd: [megbízható szolgáltatások](#trusted-microsoft-services).

    A következő Microsoft-szolgáltatások szükségesek virtuális hálózaton
    - Azure App Service
    - Azure Functions
- **Legalább egy IP-szabályt vagy virtuális hálózati szabályt** meg kell adni a névtérhez, hogy csak a virtuális hálózat megadott IP-címeiről vagy alhálózatáról engedélyezze a forgalmat. Ha nincsenek IP-és virtuális hálózati szabályok, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulccsal). 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Privát végpont hozzáadása a Azure Portal használatával

### <a name="prerequisites"></a>Előfeltételek

Service Bus névtér Azure Private-hivatkozással való integrálásához a következő entitásokra vagy engedélyekre lesz szüksége:

- Egy Service Bus névtér.
- Egy Azure-beli virtuális hálózat.
- Egy alhálózat a virtuális hálózaton. Használhatja az **alapértelmezett** alhálózatot. 
- A Service Bus névtérhez és a virtuális hálózathoz tartozó tulajdonosi vagy közreműködői engedélyek.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a portál használatával kiválaszt egy régiót a privát végponthoz, akkor az automatikusan csak az adott régióban lévő virtuális hálózatokat fogja szűrni. A Service Bus névtér lehet egy másik régióban. A privát végpont pedig egy magánhálózati IP-címet használ a virtuális hálózaton.

### <a name="steps"></a>lépések

Ha már rendelkezik egy meglévő névtérrel, a következő lépések végrehajtásával hozhat létre privát végpontot:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A keresősáv mezőbe írja be a következőt: **Service Bus**.
3. Válassza ki a listából azt a **névteret** , amelyhez privát végpontot szeretne hozzáadni.
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget a **Beállítások** területen.     Alapértelmezés szerint a **kiválasztott hálózatok** lehetőség van kiválasztva.
 
    > [!NOTE]
    > A **hálózatkezelés** lap csak a **prémium** szintű névterek esetében jelenik meg.  
   
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Hálózatkezelés lap – alapértelmezett" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::

    > [!WARNING]
    > Ha nem ad hozzá legalább egy IP-tűzfalszabály vagy virtuális hálózat ezen a lapon, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulcs használatával).
   
    Ha a **minden hálózat** lehetőséget választja, a Service Bus névtér minden IP-címről (a hozzáférési kulccsal) fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. Ha privát végpontokon keresztül szeretné engedélyezni a névteret, válassza a **privát végponti kapcsolatok** fület az oldal tetején.
6. A lap tetején kattintson a **+ privát végpont** gombra.

    ![Privát végpont hozzáadása gomb](./media/private-link-service/private-link-service-3.png)
7. Az **alapvető beállítások** lapon kövesse az alábbi lépéseket: 
    1. Válassza ki azt az **Azure-előfizetést** , amelyben létre szeretné hozni a privát végpontot. 
    2. Válassza ki a privát végpont erőforráshoz tartozó **erőforráscsoportot** .
    3. Adja meg a magánhálózati végpont **nevét** . 
    5. Válasszon ki egy **régiót** a privát végpont számára. A privát végpontnak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, de a privát kapcsolati erőforrástól eltérő régióban lehet, amelyhez csatlakozik. 
    6. Válassza a **Tovább: erőforrás >** gombot az oldal alján.

        ![Privát végpont létrehozása – alapismeretek lap](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Az **erőforrás** oldalon hajtsa végre az alábbi lépéseket:
    1. A kapcsolódási módszer esetén, ha a **címtárban a kapcsolódás Azure-erőforráshoz** lehetőséget választja, kövesse az alábbi lépéseket:   
        1. Válassza ki azt az **Azure-előfizetést** , amelyben a **Service Bus névtere** létezik. 
        2. Az **erőforrástípus** mezőben válassza ki a **Microsoft. ServiceBus/Namespaces** elemet az **erőforrás típushoz**.
        3. Az **erőforrás** mezőben válasszon ki egy Service Bus névteret a legördülő listából. 
        4. Győződjön meg arról, hogy a **cél alerőforrás** a **névtérre** van beállítva.
        5. Válassza a **Tovább: konfigurációs >** gombot az oldal alján. 
        
            ![Privát végpont létrehozása – Erőforrás lap](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Ha a **Kapcsolódás Azure-erőforráshoz erőforrás-azonosító vagy alias alapján** lehetőséget választja, kövesse az alábbi lépéseket:
        1. Adja meg az **erőforrás-azonosítót** vagy az **aliast**. Ez lehet az az erőforrás-azonosító vagy alias, amelyet valaki megosztott Önnel. Az erőforrás-azonosító beszerzésének legegyszerűbb módja, ha a Azure Portal Service Bus névterére navigál, és az URI részét másolja a-től kezdődően `/subscriptions/` . Példaként tekintse meg az alábbi ábrát. 
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
12. Győződjön meg arról, hogy a magánhálózati végpont létrejött. Ha Ön az erőforrás tulajdonosa, és kiválasztotta a Kapcsolódás az **Azure-erőforráshoz a saját címtárban** lehetőséget a **kapcsolati módszerhez**, a végponti kapcsolatot **automatikusan jóvá** kell hagyni. Ha **függő** állapotban van, tekintse meg a [privát végpontok kezelése Azure Portal szakasz használatával](#manage-private-endpoints-using-azure-portal) című részt.

    ![Saját végpont létrehozva](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="add-a-private-endpoint-using-powershell"></a>Privát végpont hozzáadása a PowerShell használatával
Az alábbi példa azt mutatja be, hogyan használható a Azure PowerShell egy magánhálózati végponti kapcsolatok Service Bus névtérhez való létrehozásához.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. A Service Bus névtér lehet egy másik régióban. A privát végpont pedig egy magánhálózati IP-címet használ a virtuális hálózaton.

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

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
1. A keresősáv mezőbe írja be a következőt: **Service Bus**.
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

    ![A kapcsolatok állapota – jóváhagyott](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Privát végponti kapcsolatok elutasítása

1. Ha van olyan privát végponti kapcsolat, amelyet el szeretne utasítani, legyen az egy függőben lévő kérelem vagy korábban jóváhagyott meglévő kapcsolat, válassza ki a végponti kapcsolatot, majd kattintson az **elutasítás** gombra.

    ![Elutasítás gomb](./media/private-link-service/private-endpoint-reject.png)
2. A **kapcsolatok elutasítása** lapon adjon meg egy nem kötelező megjegyzést, és válassza az **Igen** lehetőséget. Ha a **nem** lehetőséget választja, semmi nem történik. 

    ![A kapcsolatok elutasítása lap](./media/private-link-service/reject-connection-page.png)
3. Ekkor meg kell jelennie a listán szereplő kapcsolatok állapotának **.** 

    ![Végpont elutasítva](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Privát végponti kapcsolatok eltávolítása

1. Privát végponti kapcsolatok eltávolításához jelölje ki azt a listában, majd az eszköztáron kattintson az **Eltávolítás** elemre. 

    ![Eltávolítás gomb](./media/private-link-service/remove-endpoint.png)
2. A **Kapcsolódás törlése** lapon válassza az **Igen** lehetőséget a privát végpont törlésének megerősítéséhez. Ha a **nem** lehetőséget választja, semmi nem történik. 

    ![A kapcsolatok törlése lap](./media/private-link-service/delete-connection-page.png)
3. A **kapcsolat megszakadt** állapotra módosult. Ezután megjelenik a végpont a listából. 

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a magánhálózati kapcsolat működik-e

Győződjön meg arról, hogy a magánhálózati végpont virtuális hálózatán lévő erőforrások a magánhálózati IP-címeken keresztül csatlakoznak a Service Bus névtérhez, és a saját DNS-zóna megfelelő integrációja van.

Először hozzon létre egy virtuális gépet a [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/windows/quick-create-portal.md) című témakör lépéseit követve.

A **hálózatkezelés** lapon: 

1. Határozza meg a **virtuális hálózatot** és az **alhálózatot**. Ki kell választania azt a Virtual Network, amelyre a magánhálózati végpontot telepítette.
2. **Nyilvános IP-** erőforrást kell megadni.
3. A **NIC hálózati biztonsági csoportban** válassza a **nincs** lehetőséget.
4. A **terheléselosztáshoz** válassza a **nem** lehetőséget.

Kapcsolódjon a virtuális géphez, nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

A következőhöz hasonló eredményt kell látnia. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Korlátozások és kialakítási szempontok

**Díjszabás**: díjszabási információkért tekintse meg az [Azure Private link díjszabását](https://azure.microsoft.com/pricing/details/private-link/).

**Korlátozások**: Ez a funkció az összes Azure-beli nyilvános régióban elérhető.

**Privát végpontok maximális száma Service Bus névtérben**: 120.

További információ [: Azure Private link Service: korlátozások](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](../private-link/private-link-service-overview.md)
- További információ a [Azure Service Bus](service-bus-messaging-overview.md)

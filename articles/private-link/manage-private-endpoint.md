---
title: Privát végponti kapcsolatok kezelése az Azure-ban
description: Ismerje meg, hogyan kezelheti a privát végponti kapcsolatokat az Azure-ban
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95531372"
---
# <a name="manage-a-private-endpoint-connection"></a>Privátvégpont-kapcsolat kezelése
Az Azure Private link egy jóváhagyási hívási folyamat modelljén működik, amelyben a privát kapcsolati szolgáltatás fogyasztója kapcsolatot igényelhet a szolgáltatóval a szolgáltatás felhasználásához. A szolgáltató eldöntheti, hogy engedélyezi-e a fogyasztó számára a kapcsolódást. Az Azure Private link lehetővé teszi, hogy a szolgáltatók felügyelni tudják a magánhálózati végponti kapcsolatot az erőforrásokon. Ez a cikk útmutatást nyújt a magánhálózati végpontok kapcsolatainak kezeléséhez.

![Privát végpontok kezelése](media/manage-private-endpoint/manage-private-endpoint.png)

Két kapcsolat-jóváhagyási módszer van, amelyet a privát kapcsolati szolgáltatás fogyasztó választhat:
- **Automatikus**: Ha a szolgáltatás felhasználója Azure RBAC engedélyekkel rendelkezik a szolgáltatói erőforráson, a fogyasztó kiválaszthatja az automatikus jóváhagyási módszert. Ebben az esetben, ha a kérelem eléri a szolgáltatói erőforrást, nincs szükség beavatkozásra a szolgáltatótól, és a rendszer automatikusan jóváhagyja a kapcsolódást. 
- **Manuális**: Ha a szolgáltatás felhasználója nem rendelkezik Azure RBAC-engedélyekkel a szolgáltatói erőforráson, a fogyasztó kiválaszthatja a manuális jóváhagyási módszert. Ebben az esetben a kapcsolatkérelem a szolgáltatási erőforrásokon **függőben** jelenik meg. A szolgáltatónak manuálisan jóvá kell hagynia a kérést a kapcsolatok létrehozása előtt. Manuális esetekben a szolgáltatás fogyasztója olyan üzenetet is megadhat, amely a kéréssel több kontextust biztosít a szolgáltató számára. A szolgáltató a következő lehetőségek közül választhat az összes privát végponti kapcsolat közül: **jóváhagyva**, **elutasítás**, **Eltávolítás**.

Az alábbi táblázatban láthatók a különböző szolgáltatói műveletek, valamint a magánhálózati végpontok számára létrejövő kapcsolatok állapota.  A szolgáltató a privát végpontok kapcsolatainak egy későbbi időpontban is megváltoztathatja a fogyasztó beavatkozása nélkül. A művelet frissíti a végpont állapotát a felhasználói oldalon. 


|Szolgáltatói művelet   |A szolgáltatás fogyasztói magánhálózati végpontjának állapota   |Leírás   |
|---------|---------|---------|
|Nincs    |    Függőben     |    A kapcsolat manuálisan jön létre, és függőben van a privát hivatkozás erőforrás-tulajdonosa általi jóváhagyásra.       |
|Jóváhagyás    |  Approved       |  A kapcsolódás automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra.     |
|Elutasítás     | Elutasítva        | A magánhálózati kapcsolat erőforrásának tulajdonosa elutasította a kapcsolatot.        |
|Eltávolítás    |  Leválasztott       | A kapcsolatot a privát kapcsolat erőforrás-tulajdonosa eltávolította, a magánhálózati végpont informatív lesz, és törölni kell a karbantartást.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Privátvégpont-kapcsolatok kezelése Azure PaaS-erőforrásokon
A portál a privát végponti kapcsolatok Azure Pásti-erőforrásokon való felügyeletének előnyben részesített módszere. Jelenleg nem érhető el a PowerShell/parancssori felület támogatása az Azure Pásti-erőforrások kapcsolatainak kezeléséhez.
1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. Navigáljon a privát kapcsolati központhoz.
3. Az **erőforrások** területen válassza ki azt az erőforrástípust, amelyet a privát végponti kapcsolatok kezeléséhez használni kíván.
4. Az egyes erőforrástípusok esetében megtekintheti a hozzá társított privát végpont-kapcsolatok számát. Igény szerint szűrheti az erőforrásokat.
5. Válassza ki a magánhálózati végpontok kapcsolatait.  A felsorolt kapcsolatok alatt válassza ki a kezelni kívánt kapcsolatot. 
6. A kapcsolatok állapotát a felül található beállítások közül választva módosíthatja.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Privát végpontok kapcsolatainak kezelése egy ügyfél/partner tulajdonában lévő Private link Service-ben

A Azure PowerShell és az Azure CLI a Microsoft partner Services vagy az ügyfél tulajdonában lévő szolgáltatások privát végponti kapcsolatainak kezeléséhez javasolt módszer. Jelenleg nincs olyan portál-támogatás, amely a kapcsolatok egy privát kapcsolaton keresztüli kezelésére szolgál.  
 
### <a name="powershell"></a>PowerShell 
  
A magánhálózati végpontok kapcsolatainak kezeléséhez használja az alábbi PowerShell-parancsokat.  
#### <a name="get-private-link-connection-states"></a>Magánhálózati kapcsolati állapotok beolvasása 
Használja a `Get-AzPrivateLinkService` parancsmagot a privát végponti kapcsolatok és azok állapotának lekéréséhez.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása 
 
A `Approve-AzPrivateEndpointConnection` parancsmag használatával hagyja jóvá a magánhálózati végponti kapcsolatokat. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Magánhálózati végponti kapcsolatok megtagadása 
 
A `Deny-AzPrivateEndpointConnection` parancsmag használatával utasítsa el a privát végponti kapcsolatokat. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Privát végponti kapcsolatok eltávolítása 
 
A `Remove-AzPrivateEndpointConnection` parancsmag használatával távolítsa el a magánhálózati végponti kapcsolatokat. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
`az network private-link-service update`A saját végpont kapcsolatainak kezelésére használható. A (z) paraméterben meg van adva a kapcsolási állapot ```azurecli connection-status``` . 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Következő lépések
- [További tudnivalók a privát végpontokról](private-endpoint-overview.md)
 

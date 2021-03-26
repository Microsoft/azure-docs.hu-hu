---
title: Felügyeleti címek
description: A App Service Environment vezérléséhez használt felügyeleti címek megkeresése. Egy útválasztási táblában konfigurálta az aszimmetrikus útválasztási problémák elkerüléséhez.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 25bdfb7a0301af472baa89d3d9e73aacf8cff139
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954615"
---
# <a name="app-service-environment-management-addresses"></a>Felügyeleti címek App Service Environment

A App Service Environment (bevezetési) az Azure Virtual Network-ben (VNet) futó Azure App Service egyetlen bérlős telepítése.  Amíg a bevezetési szolgáltatás a VNet fut, továbbra is elérhetőnek kell lennie számos dedikált IP-címről, amelyeket a Azure App Service a szolgáltatás felügyeletéhez használ.  A bevezetés esetén a felügyeleti forgalom áthalad a felhasználó által vezérelt hálózaton. Ha a forgalom blokkolva van, vagy a rendszer nem irányítja át, a rendszer felfüggeszti a bevezetőt. A betekintő hálózati függőségekkel kapcsolatos részletekért olvassa el a [hálózatkezelési megfontolásokat és a app Service Environment][networking]. A kiegészítő szolgáltatással kapcsolatos általános információkért Kezdje a [app Service Environment bevezetésével][intro].

Minden ASE van egy nyilvános virtuális IP-címe, amelybe a felügyeleti forgalom bekerül. Az ezekről a címekről érkező bejövő felügyeleti forgalom a-től a 454-es és a 455-as portra kerül a központjának nyilvános VIP-címén Ez a dokumentum felsorolja a közApp Servicei felügyeleti forgalom felé irányuló felügyeleti forgalmat. Ezek a címek a AppServiceManagement nevű IP-szolgáltatás címkéjén is szerepelnek.

Az alábbi címek egy útválasztási táblában konfigurálhatók, így elkerülhetők a felügyeleti forgalom aszimmetrikus útválasztási problémái. Az útvonalak az IP-szinten lévő forgalomra hatnak, és nem ismerik a forgalom irányát, vagy a forgalom a TCP-válaszüzenet része. Ha a TCP-kérelem válaszának címe eltér a kapott címtől, akkor aszimmetrikus útválasztási probléma van. Ha nem szeretné, hogy az aszimmetrikus útválasztási problémák elkerülhetők legyenek a beadási felügyeleti forgalommal, győződjön meg arról, hogy a válaszokat a rendszer elküldi az adott címről. Ha szeretné megtudni, hogyan konfigurálhatja a bevezetőt úgy, hogy olyan környezetben működjön, amelyben a kimenő forgalmat a helyszínen küldik, olvassa el a bevezető [beállítása kényszerített bújtatással][forcedtunnel] című témakört.

## <a name="list-of-management-addresses"></a>Felügyeleti címek listája ##

| Region | Címek |
|--------|-----------|
| Összes nyilvános régió | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure China | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Hálózati biztonsági csoport konfigurálása

A hálózati biztonsági csoportokkal nem kell aggódnia az egyes címekkel vagy a saját konfiguráció fenntartásával. Létezik egy AppServiceManagement nevű IP-szolgáltatás címkéje, amely az összes címmel naprakész állapotban van. Ha ezt az IP-szolgáltatási címkét szeretné használni a NSG, lépjen a portálra, nyissa meg a hálózati biztonsági csoportok felhasználói felületét, és válassza a bejövő biztonsági szabályok lehetőséget. Ha már létezik meglévő szabály a bejövő felügyeleti forgalomhoz, szerkessze. Ha ez a NSG nem a saját **beadásával** lett létrehozva, vagy ha az összes új, válassza a Hozzáadás lehetőséget. A forrás legördülő menüben válassza ki a **szolgáltatás címkéje** elemet.  A forrás szolgáltatás címkéje területen válassza a **AppServiceManagement** lehetőséget. A forrásport tartományának beállítása \* , cél: **bármely**, célport tartománya **454-455**, protokoll – **TCP**, és művelet, amely **lehetővé teszi**. Ha a szabályt állítja be, akkor meg kell adnia a prioritást. 

![NSG létrehozása a szolgáltatás címkéjével][1]

## <a name="configuring-a-route-table"></a>Útválasztási táblázat konfigurálása

A felügyeleti címek az Internet következő ugrását tartalmazó útválasztási táblázatba helyezhetők, így biztosítva, hogy az összes bejövő felügyeleti forgalom vissza tudjon térni ugyanazon az elérési úton. Ezek az útvonalak a kényszerített bújtatás konfigurálásakor szükségesek. Az útválasztási táblázat létrehozásához használhatja a portált, a PowerShellt vagy az Azure CLI-t.  Az útválasztási tábla Azure CLI használatával PowerShell-parancssorból történő létrehozásához szükséges parancsok alább láthatók. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "20.37.74.128", "23.96.195.3", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.107.58.192", "51.107.154.192", "51.116.58.192", "51.116.155.0", "51.120.99.0", "51.120.219.0", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "65.52.250.128", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.50.128", "191.233.203.64", "191.236.154.88"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Az útválasztási táblázat létrehozása után be kell állítania azt a bevezető alhálózaton.  

## <a name="get-your-management-addresses-from-api"></a>Felügyeleti címek beszerzése az API-ból ##

A következő API-hívással listázhatja a szolgáltatásnak megfelelő felügyeleti címeket.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

Az API egy JSON-dokumentumot ad vissza, amely tartalmazza az összes bejövő címet a bemenő adatszolgáltatáshoz. A címek listája tartalmazza a felügyeleti címeket, a beadási szolgáltató által használt virtuális IP-címet és az alhálózati cím tartományát.  

Az API-nak a [armclient](https://github.com/projectkudu/ARMClient) való meghívásához használja az alábbi parancsokat, de az előfizetés-azonosító, az erőforráscsoport és a központjának neve helyett helyettesítse be.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md

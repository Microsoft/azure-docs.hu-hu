---
title: Felügyeleti címek
description: Keresse meg a felügyeleti címeket, amelyek a App Service Environment. Ezeket egy útvonaltáblában konfigurálta az aszimmetrikus útválasztási problémák elkerülése érdekében.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions, devx-track-azurecli
ms.openlocfilehash: 796ee38140e72a56f1f22b0594dd904a43ac53c0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865224"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment címek

A App Service Environment (ASE) az Azure Azure App Service (VNet) szolgáltatásban futó virtuális Virtual Network üzemelő példánya.  Bár az ASE fut a virtuális hálózaton, továbbra is elérhetőnek kell lennie számos dedikált IP-címről, amelyet a Azure App Service a szolgáltatás kezeléséhez.  ASE esetén a felügyeleti forgalom áthalad a felhasználó által vezérelt hálózaton. Ha ez a forgalom blokkolva van vagy helytelenül van átirányítva, az ASE fel lesz függesztve. Az ASE hálózattal kapcsolatos függőségekkel kapcsolatos részletekért olvassa el a [Hálózati][networking]szempontok és a App Service Environment. Az ASE-ről a Bevezetés az ASE-be [App Service Environment.][intro]

Minden ABA-nak van egy nyilvános VIP-ed, amelybe a felügyeleti forgalom érkezik. Az ezekről a címekről bejövő felügyeleti forgalom az ASE nyilvános VIP-címének 454-es és 455-ös portjairól érkezik. Ez a dokumentum felsorolja App Service ASE felügyeleti forgalmának forráscímeit. Ezek a címek szintén az AppServiceManagement nevű IP-szolgáltatáscímkében vannak.

Az alább feljegyzett címek egy útvonaltáblában konfigurálhatóak a felügyeleti forgalom aszimmetrikus útválasztási problémáinak elkerülése érdekében. Az útvonalak az IP-szinten reagálnak a forgalomra, és nem figyelmét a forgalom irányára, illetve arra, hogy a forgalom egy TCP-válaszüzenet része. Ha egy TCP-kérés válaszcíme eltér a címtől, amelybe azt küldték, akkor aszimmetrikus útválasztási problémája van. Az ASE felügyeleti forgalom aszimmetrikus útválasztási problémáinak elkerülése érdekében gondoskodnia kell arról, hogy a válaszok ugyanatról a címről kerüljenek vissza, amelybe a rendszer elküldte őket. Az ASE olyan környezetben való működésre való konfigurálásával kapcsolatos részletekért, ahol a kimenő forgalom a helyszínen van elküldve, olvassa el a következőt: Az ASE konfigurálása kényszerített [bújtatással][forcedtunnel]

## <a name="list-of-management-addresses"></a>Felügyeleti címek listája ##

| Region | Címek |
|--------|-----------|
| Minden nyilvános régió | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure China | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Hálózati biztonsági csoport konfigurálása

A hálózati biztonsági csoportok esetében nem kell aggódnia az egyes címek vagy a saját konfiguráció fenntartása miatt. Van egy AppServiceManagement nevű IP-szolgáltatáscímke, amely az összes címmel naprakész marad. Ha ezt az IP-szolgáltatáscímkét használnia kell az NSG-ben, nyissa meg a portált, nyissa meg a hálózati biztonsági csoportok felhasználói felületét, és válassza a Bejövő biztonsági szabályok lehetőséget. Ha a bejövő felügyeleti forgalomhoz már van meglévő szabálya, szerkessze azt. Ha ez az NSG nem az ASE-val lett létrehozva, vagy ha az egész új, válassza a **Hozzáadás lehetőséget.** A Source (Forrás) legördülő menüben válassza a **Service Tag (Szolgáltatáscímke) lehetőséget.**  A Forrás szolgáltatáscímke alatt válassza az **AppServiceManagement lehetőséget.** A forrásporttartományokat állítsa a következőre: , Cél: \* **Bármely,** Célporttartományok: **454–455,** **Protokoll: TCP,** Művelet: **.** A szabály létrehozása esetén be kell állítania a Prioritást. 

![NSG létrehozása a szolgáltatáscímkével][1]

## <a name="configuring-a-route-table"></a>Útválasztási táblázat konfigurálása

A felügyeleti címek elhelyezhetőek egy útvonaltáblában az internet következő ugrásával, így biztosítva, hogy az összes bejövő felügyeleti forgalom visszamehet ugyanazon az útvonalon. Ezekre az útvonalakra a kényszerített bújtatás konfigurálásakor van szükség. Az útvonaltábla létrehozásához használhatja a portált, a PowerShellt vagy az Azure CLI-t.  Az útvonaltábla PowerShell-parancssorból az Azure CLI használatával való létrehozására vonatkozó parancsok alább olvashatók. 

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

Az útvonaltábla létrehozása után be kell állítania azt az ASE-alhálózaton.  

## <a name="get-your-management-addresses-from-api"></a>Felügyeleti címek lekérte az API-ból ##

Az alábbi API-hívással listázhatja az ASE-nek megfelelő felügyeleti címeket.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

Az API egy JSON-dokumentumot ad vissza, amely tartalmazza az ASE összes bejövő címét. A címek listája tartalmazza a felügyeleti címeket, az ASE által használt virtuális IP-címet és magát az ASE alhálózati címtartományt.  

Az API [armclienttel](https://github.com/projectkudu/ARMClient) való hívásához használja a következő parancsokat, de helyettesítse be az előfizetés azonosítóját, az erőforráscsoportot és az ASE nevét.  

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

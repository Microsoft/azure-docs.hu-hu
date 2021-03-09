---
title: A hálózati konfiguráció diagnosztika bemutatása az Azure Network Watcherban | Microsoft Docs
description: Ez az oldal áttekintést nyújt a Network Watcher hálózati konfigurációs diagnosztika szolgáltatásról
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 5feef79a08789ad381b0c93cb938abd9effdfcc8
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502008"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Az Azure-beli hálózati konfigurációs diagnosztika bemutatása Network Watcher

A hálózati konfiguráció diagnosztikai eszköze segít az ügyfeleknek megérteni, hogy az Azure-Virtual Network mely adatforgalom engedélyezett vagy tagadható meg, valamint részletes információkkal a hibakereséshez. Segítheti a megértését, ha helyesen vannak konfigurálva a NSG-szabályok. 

## <a name="pre-requisites"></a>Előfeltételek
A hálózati konfigurációs diagnosztika használatához engedélyezni kell Network Watcher az előfizetésében. Lásd: [Azure Network Watcher-példány létrehozása](./network-watcher-create.md) az engedélyezéshez.

## <a name="background"></a>Háttér

- Az Azure-beli erőforrásai virtuális hálózatokon (virtuális hálózatok) és alhálózatokon keresztül kapcsolódnak egymáshoz. Ezeknek a virtuális hálózatok és alhálózatoknak a biztonsága felügyelhető hálózati biztonsági csoport (NSG) használatával.
- A NSG olyan biztonsági szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják a hálózati forgalmat a csatlakoztatott erőforrásokhoz. A NSG alhálózatokhoz, egyéni virtuális gépekhez vagy virtuális gépekhez csatolt egyedi hálózati adapterekhez (NIC) is társíthatók. 
- A hálózatban lévő összes adatforgalom kiértékelése a vonatkozó NSG található szabályok alapján történik.
- A szabályokat a rendszer a legalacsonyabb és a legmagasabb prioritási szám alapján értékeli ki 

## <a name="how-does-network-configuration-diagnostic-work"></a>Hogyan működik a hálózati konfiguráció diagnosztika? 

Egy adott folyamat esetében a NCD eszköz futtatja a folyamat szimulációját, és visszaadja, hogy a folyamat engedélyezett-e (vagy megtagadva), valamint részletes információkat a folyamatokat engedélyező vagy megtagadó szabályokról.  Az ügyfeleknek meg kell adniuk a folyamat részleteit, például a forrást, a célhelyet, a protokollt stb. Az eszköz visszaadja, hogy a forgalom engedélyezett vagy megtagadott-e, a megadott folyamatra kiértékelt NSG-szabályok és az összes szabály kiértékelési eredményei.

## <a name="next-steps"></a>Következő lépések

Hálózati konfigurációs diagnosztika használata más felületeken keresztül
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic)
 - [Azure CLI](/cli/azure/network/watcher#az_network_watcher_run_configuration_diagnostic)
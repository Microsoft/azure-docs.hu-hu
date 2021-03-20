---
title: A képességek használatához szükséges Azure RBAC-engedélyek
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogy mely Azure szerepköralapú hozzáférés-vezérlési engedélyekre van szükség a Network Watcher képességekkel való együttműködéshez.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 47418b9c5235255ff7dbf4a1a151e51e4c9aba58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019669"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Az Azure szerepköralapú hozzáférés-vezérlési engedélyei a Network Watcher képességek használatához

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi, hogy csak a szervezet azon tagjai számára rendeljen hozzá konkrét műveleteket, amelyekre a hozzárendelt feladatok elvégzéséhez szükségük van. Network Watcher képességek használatához az Azure-ba bejelentkezett fiókot hozzá kell rendelni a [tulajdonoshoz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), a [közreműködőhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)vagy a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) beépített szerepköreihez, vagy hozzá kell rendelni egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , amely az alábbi szakaszokban felsorolt, az egyes Network Watcher képességekhez tartozó műveletekhez van rendelve. Ha többet szeretne megtudni a Network Watcher képességeiről, tekintse meg a [Mi az a Network Watcher?](network-watcher-monitoring-overview.md)című témakört.

## <a name="network-watcher"></a>Network Watcher

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/READ                              | Network Watcher beszerzése                                          |
| Microsoft. Network/networkWatchers/Write                             | Network Watcher létrehozása vagy frissítése                             |
| Microsoft. Network/networkWatchers/delete                            | Hálózati figyelő törlése                                       |

## <a name="nsg-flow-logs"></a>NSG-folyamat-naplók

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/configureFlowLog/Action           | Folyamat naplójának konfigurálása                                           |
| Microsoft. Network/networkWatchers/queryFlowLogStatus/Action         | Folyamat naplójának lekérdezési állapota                                    |

## <a name="connection-troubleshoot"></a>Kapcsolati hibaelhárító

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectivityCheck/Action          | A kapcsolatok hibakeresési tesztelésének kezdeményezése
| Microsoft. Network/networkWatchers/queryTroubleshootResult/Action    | Kapcsolatok hibakeresési tesztelési eredményeinek lekérdezése                |
| Microsoft. Network/networkWatchers/hibakeresés/művelet               | Kapcsolatok hibakeresési tesztelésének futtatása                             |

## <a name="connection-monitor"></a>Csatlakozáskezelő

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectionMonitors/Start/művelet   | Csatlakozáskezelő indítása                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/leállítás/művelet    | Csatlakozáskezelő leállítása                                      |
| Microsoft. Network/networkWatchers/connectionMonitors/Query/Action   | Csatlakozáskezelő lekérdezése                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/READ           | Csatlakozáskezelő beszerzése                                       |
| Microsoft. Network/networkWatchers/connectionMonitors/Write          | Kapcsolatfigyelő létrehozása                                    |
| Microsoft. Network/networkWatchers/connectionMonitors/delete         | Csatlakozáskezelő törlése                                    |

## <a name="packet-capture"></a>Csomagrögzítő

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/packetCaptures/queryStatus/művelet | A csomagok rögzítési állapotának lekérdezése                           |
| Microsoft. Network/networkWatchers/packetCaptures/leállítás/művelet        | Csomagok rögzítésének leállítása                                          |
| Microsoft. Network/networkWatchers/packetCaptures/READ               | Csomag rögzítésének beolvasása                                           |
| Microsoft. Network/networkWatchers/packetCaptures/Write              | Csomag rögzítésének létrehozása                                        |
| Microsoft. Network/networkWatchers/packetCaptures/delete             | Csomagok rögzítésének törlése                                        |

## <a name="ip-flow-verify"></a>IP-forgalomellenőrző

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/ipFlowVerify/Action               | IP-forgalom ellenőrzése                                              |

## <a name="next-hop"></a>Következő ugrás

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/nextHop/Action                    | Virtuális gép következő ugrásának beolvasása                                     |

## <a name="network-security-group-view"></a>Hálózati biztonsági csoport nézet

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/securityGroupView/Action          | Biztonsági csoportok megtekintése                                           |

## <a name="topology"></a>Topológia

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/topológia/művelet                   | Topológia beolvasása                                                   |
| Microsoft. Network/networkWatchers/topológia/olvasás                     | Lásd fent                                                  |

## <a name="reachability-report"></a>Elérhetőségi jelentés

| Művelet                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/azureReachabilityReport/Action    | Azure-beli elérhetőségi jelentés beszerzése                               |


## <a name="additional-actions"></a>További műveletek

Network Watcher képességekhez a következő műveletek is szükségesek:

| Művelet (ek)                                                           | Description                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/ \* /READ                                     | Azure-beli szerepkör-hozzárendelések és szabályzat-definíciók beolvasásához használatos          |
| Microsoft. Resources/Subscriptions/resourceGroups/READ               | Egy előfizetésben lévő összes erőforráscsoport enumerálására használatos    |
| Microsoft. Storage/storageAccounts/olvasás                              | A megadott Storage-fiók tulajdonságainak beolvasására szolgál   |
| Microsoft. Storage/storageAccounts/listServiceSas/művelet, </br> Microsoft. Storage/storageAccounts/listAccountSas/művelet, <br> Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet| A [Storage-fiók biztonságos elérését](../storage/common/storage-sas-overview.md) és a Storage-fiókba való írást lehetővé tévő közös hozzáférésű aláírások (SAS) beolvasására szolgál. |
| Microsoft. számítás/virtualMachines/olvasás, </br> Microsoft. számítás/virtualMachines/írás| A virtuális gépre való bejelentkezéshez használatos, a csomagok rögzítése és feltöltése a Storage-fiókba|
| Microsoft. számítás/virtualMachines/bővítmények/olvasás </br> Microsoft. számítás/virtualMachines/bővítmények/írás| Annak vizsgálatára használatos, hogy Network Watcher-bővítmény megtalálható-e, és szükség esetén telepítse a telepítést |
| Microsoft. számítás/virtualMachineScaleSets/olvasás, </br> Microsoft. számítás/virtualMachineScaleSets/írás| A virtuálisgép-méretezési csoportok elérésére használatos, a csomagok rögzítése és a Storage-fiókba való feltöltése|
| Microsoft. számítási/virtualMachineScaleSets/bővítmények/olvasás, </br> Microsoft. számítás/virtualMachineScaleSets/bővítmények/írás| Annak vizsgálatára használatos, hogy Network Watcher-bővítmény megtalálható-e, és szükség esetén telepítse a telepítést |
| Microsoft. bepillantások/alertRules/*                                     | Metrikai riasztások beállításához használatos                                     |
| Microsoft. support/*                                                 | Támogatási jegyek létrehozásához és frissítéséhez használható Network Watcher |
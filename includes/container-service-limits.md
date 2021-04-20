---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 15e91e6f275c3a6ebe44690441404a38e8f61394
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732641"
---
| Erőforrás                                                                                                           | Korlát                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Fürtök maximális száma előfizetésenként                                                                                  | 1000                                                                                                                                                                                                        |
| Fürtönkénti csomópontok maximális száma virtuálisgép-rendelkezésre állási csoportokkal és alapszintű Load Balancer termékváltozattal                       | 100                                                                                                                                                                                                         |
| Fürtönkénti csomópontok maximális száma Virtual Machine Scale Sets [termékváltozattal standard Load Balancer termékváltozattal][standard-load-balancer] | 1000 (az összes [csomópontkészletben)][node-pool]                                            |
| Csomópontkészletek maximális száma fürtönként                                                                                     | 100                                                                                  |
| Podok maximális száma csomópontonként: [Alapszintű hálózat a][basic-networking] Kubenetben                                           | Maximum: 250 <br /> Azure CLI alapértelmezett: 110 <br /> Azure Resource Manager sablon alapértelmezett beállítása: 110 <br /> Azure Portal alapértelmezett beállítás: 30          |
| Podok maximális száma csomópontonként: [Speciális hálózat Azure Container Networking Interface][advanced-networking]        | Maximum: 250 <br /> Alapértelmezett: 30                                                      |
| Előzetes verziójú AKS-bővítmény megnyitása a Service Meshben (OSM)                                                                          | Kubernetes-fürt verziója: 1.19+<sup>1</sup><br />OSM-vezérlők fürtönként: 1<sup>1</sup><br />Podok OSM-vezérlőnként: 500<sup>1</sup><br />Az OSM által felügyelt Kubernetes-szolgáltatásfiókok: 50<sup>1</sup> |

<sup>1</sup> Az OSM AKS-bővítménye előzetes verziójú állapotban van, és további fejlesztéseken megy keresztül az általánosan elérhető (GA) verzió előtt. Az előzetes verzió fázisában javasoljuk, hogy ne lépje túl a megjelenő korlátokat.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

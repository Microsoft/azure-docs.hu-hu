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
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799935"
---
| Erőforrás                                                                                                           | Korlát                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Fürtök maximális száma előfizetésenként                                                                                  | 5000                                                                                                                                                                                                        |
| Csomópontok maximális száma fürtönként virtuálisgép-rendelkezésre állási csoportokkal és alapszintű Load Balancer termékváltozattal                       | 100                                                                                                                                                                                                         |
| Csomópontok maximális száma fürtönként Virtual Machine Scale Sets és [standard Load Balancer termékváltozattal][standard-load-balancer] | 1000 (az összes [csomópontkészletben)][node-pool]                                            |
| Csomópontkészletek maximális száma fürtönként                                                                                     | 100                                                                                  |
| Podok maximális száma csomópontonként: [Alapszintű hálózat a][basic-networking] Kubenetben                                           | Maximum: 250 <br /> Az Azure CLI alapértelmezett beállítása: 110 <br /> Azure Resource Manager alapértelmezett beállítás: 110 <br /> Azure Portal alapértelmezett beállítás: 30          |
| Podok maximális száma csomópontonként: [Speciális hálózat Azure Container Networking Interface][advanced-networking]        | Maximum: 250 <br /> Alapértelmezett érték: 30                                                      |
| A Service Mesh (OSM) AKS-bővítmény előzetes verziója                                                                          | Kubernetes-fürt verziója: 1.19+<sup>1</sup><br />OSM-vezérlők fürtönként: 1<sup>1</sup><br />Podok OSM-vezérlőnként: 500<sup>1</sup><br />Az OSM által felügyelt Kubernetes-szolgáltatásfiókok: 50<sup>1</sup> |

<sup>1</sup> Az AKS-hez elérhető OSM-bővítmény előzetes verziójú állapotban van, és további fejlesztéseken megy keresztül az általánosan elérhető (GA) előtt. Az előzetes verzió fázisában javasoljuk, hogy ne lépje túl a megjelenő korlátokat.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

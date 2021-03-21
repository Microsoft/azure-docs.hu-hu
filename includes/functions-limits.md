---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: aa75d1d57f44bb1a4d6513823ac97ac9917b260f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934465"
---
| Erőforrás |[Használatalapú csomag](../articles/azure-functions/consumption-plan.md)|[Prémium szintű csomag](../articles/azure-functions/functions-premium-plan.md)|[Dedikált terv](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Alapértelmezett [időkorlát időtartama](../articles/azure-functions/functions-scale.md#timeout) (perc) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|[Időtúllépés maximális időtartama](../articles/azure-functions/functions-scale.md#timeout) (perc) |10 | nem kötött<sup>7</sup> | nem kötött<sup>2</sup> | unbounded | unbounded |
| Kimenő kapcsolatok maximális száma (/példány) | 600 aktív (1200 összesen) | unbounded | unbounded | unbounded | unbounded |
| Kérelem maximális mérete (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | A fürttől függ |
| Lekérdezési karakterlánc maximális hossza<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | A fürttől függ |
| Kérelem maximális URL-címének maximális hossza<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | A fürttől függ |
|[ACU](../articles/virtual-machines/acu.md) -példányok száma | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [AK-díjszabás](https://azure.microsoft.com/pricing/details/container-service/) |
| Maximális memória (GB/példány) | 1.5 | 3,5 – 14 | 1,75 – 14 | 3,5 – 14 | Bármely csomópont támogatott |
| Function Apps/csomag |100 |100 |nem kötött<sup>4</sup> | unbounded | unbounded |
| [App Service-csomagok](../articles/app-service/overview-hosting-plans.md) | 100 [régiónként](https://azure.microsoft.com/global-infrastructure/regions/) |100/erőforráscsoport |100/erőforráscsoport | - | - |
| <sup>5</sup> . tárterület |5 TB |250 GB |50-1000 GB | 1 TB | n.a. |
| Egyéni tartományok/alkalmazás</a> |500<sup>6</sup> |500 |500 | 500 | n.a. |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/configure-ssl-bindings.md) |nem kötött SNI SSL-kapcsolatok belefoglalva | nem kötött SNI SSL és 1 IP SSL kapcsolat is |nem kötött SNI SSL és 1 IP SSL kapcsolat is | nem kötött SNI SSL és 1 IP SSL kapcsolat is | n.a. |

<sup>1</sup> alapértelmezés szerint a app Service csomag 1. x futtatókörnyezetének időtúllépése nem kötött.  
<sup>2</sup> az App Service-csomagot [mindig](../articles/azure-functions/dedicated-plan.md#always-on)be kell állítani. A standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/)szerint kell fizetni.  
<sup>3</sup> ezek a korlátok [a gazdagépen vannak beállítva](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> a gazdagépek tényleges száma az alkalmazások tevékenységéből, a gépi példányok méretétől és a megfelelő erőforrás-felhasználástól függ.  
<sup>5</sup> a tárolási korlát a teljes tartalom mérete az ideiglenes tárolóban, az összes alkalmazás között, ugyanabban a app Service csomagban. A felhasználási terv az ideiglenes tároláshoz Azure Files használ.  
<sup>6</sup> ha a Function alkalmazás egy [felhasználási](../articles/azure-functions/consumption-plan.md)csomagban található, csak a CNAME beállítás támogatott. A [prémium](../articles/azure-functions/functions-premium-plan.md) szintű csomagban vagy [app Service](../articles/azure-functions/dedicated-plan.md)csomagban található Function apps esetében egy egyéni tartományt egy CNAME vagy egy rekord használatával képezhető le.  
<sup>7</sup> 60 percig garantált.  
<sup>8</sup> a feldolgozók az ügyfelek által üzemeltetett szerepkörök. A feldolgozók három rögzített méretben érhetők el: egy vCPU/3,5 GB RAM; Két vCPU/7 GB RAM; Négy vCPU/14 GB RAM.
---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179493"
---
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Webes vagy feldolgozói szerepkörök száma üzemelő példányonként](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Példány bemeneti végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) száma üzemelő példányonként |25 |25 |
| [Bemeneti végpontok](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) száma üzemelő példányonként |25 |25 |
| [Belső végpont](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) száma üzemelő példányonként |25 |25 |
| [Üzemeltetett szolgáltatási tanúsítványok](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) száma üzemelő példányonként |199 |199 |

<sup>1</sup>minden Azure Cloud Service, webes és feldolgozói szerepkörökhöz lehet két üzembe helyezés, egyet az éles és átmeneti. Ez a korlát számára a különböző szerepkörök, vagyis a configuration vonatkozik. Ezt a korlátot nem hivatkozik szerepkörönként, példányok, skálázás.


---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179048"
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum jobban bemutatják, hogyan hozhat létre az udr-EK, használja az alábbi forgatókönyvet:

![KÉPLEÍRÁS](./media/virtual-network-create-udr-scenario-include/figure1.png)

Ebben a forgatókönyvben hoz létre egy UDR a a *előtérbeli alhálózatot* és a egy másik UDR a *háttérbeli alhálózatot*, az alábbiak szerint: 

* **UDR-FrontEnd**. Az előtér-UDR alkalmazott a *előtérbeli* alhálózathoz, és a egy útvonalat tartalmaznak:    
  * **RouteToBackend**. Ez az útvonal küld minden forgalmat a háttérbeli alhálózat a **FW1** virtuális gépet.
* **UDR-háttérrendszer**. A háttér-UDR alkalmazott a *háttérrendszer* alhálózathoz, és a egy útvonalat tartalmaznak:    
  * **RouteToFrontend**. Ez az útvonal minden forgalmat küld, az előtérbeli alhálózat felé a **FW1** virtuális gépet.

Ezeket az útvonalakat együttes használata biztosítja, hogy annak biztosítására, hogy az egyik alhálózatból egy másikba felé irányuló forgalom a **FW1** virtuális géphez, amely egy virtuális készülékként használatban van. Kapcsolja be az IP-továbbítást a kell a **FW1** megkaphatja a többi virtuális gép felé irányuló forgalom biztosításához a virtuális gép.


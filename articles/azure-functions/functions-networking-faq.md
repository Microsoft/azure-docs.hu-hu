---
title: Gyakori kérdések a hálózatkezeléssel kapcsolatban Azure Functions
description: Válaszok a Azure Functions-vel való hálózatkezeléssel kapcsolatos leggyakoribb kérdésekre és forgatókönyvekre.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 7d3493cc76dfb05cca3c89114b279b99ab49bac2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081644"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Gyakori kérdések a hálózatkezeléssel kapcsolatban Azure Functions

Ez a cikk a hálózatkezeléssel kapcsolatos gyakori kérdéseket ismerteti Azure Functionsban. Az átfogóbb áttekintést lásd: [functions Networking Options](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hogyan statikus IP-címet beállítani a functions szolgáltatásban?

Egy függvény üzembe helyezése egy App Service Environmentban jelenleg az egyetlen módszer a függvény statikus bejövő és kimenő IP-címének kiosztására. App Service Environment használatáról a [belső terheléselosztó létrehozása és használata app Service Environment](../app-service/environment/create-ilb-ase.md)használatával című cikk nyújt tájékoztatást.

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hogyan korlátozza az internet-hozzáférést a függvényhez?

Az Internet-hozzáférés több módon is korlátozható:

* [IP-korlátozások](../app-service/app-service-ip-restrictions.md): az IP-címtartomány szerint korlátozza a Function alkalmazás bejövő forgalmát.
    * Az IP-korlátozások területen olyan [szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)is konfigurálhat, amelyek korlátozzák a függvényt, hogy csak egy adott virtuális hálózatról fogadják el a bejövő forgalmat.
* Az összes HTTP-eseményindító eltávolítása. Egyes alkalmazások esetében elég egyszerűen elkerülni a HTTP-eseményindítókat, és más eseményforrás használatával aktiválhatja a függvényt.

Ne feledje, hogy a Azure Portal-szerkesztőnek közvetlen hozzáférésre van szüksége a futó függvényhez. A Azure Portalon végrehajtott bármely kód módosítása megköveteli, hogy az Ön által használt eszköz megkeresse a portálon, hogy az IP-címe engedélyezve legyen. De továbbra is használhatja a platform szolgáltatásai lapot a helyi hálózati korlátozásokkal.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hogyan korlátozza a saját Function alkalmazást egy virtuális hálózatra?

A [szolgáltatási végpontok](./functions-networking-options.md#private-site-access)használatával korlátozhatja a functions-alkalmazások **bejövő** forgalmát egy virtuális hálózatra. Ez a konfiguráció továbbra is lehetővé teszi, hogy a Function alkalmazás kimenő hívásokat hajtson végre az interneten.

Az egyetlen mód arra, hogy teljes mértékben korlátozza a függvényeket úgy, hogy a virtuális hálózaton keresztül lebonyolított összes forgalom belsőleg elosztott terhelésű App Service Environment használjon. Ez a beállítás a virtuális hálózaton belüli dedikált infrastruktúrán helyezi üzembe a helyet, és az összes eseményindítót és forgalmat a virtuális hálózaton keresztül küldi el. 

App Service Environment használatáról a [belső terheléselosztó létrehozása és használata app Service Environment](../app-service/environment/create-ilb-ase.md)használatával című cikk nyújt tájékoztatást.

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hogyan lehet hozzáférni egy virtuális hálózat erőforrásaihoz egy Function alkalmazásból?

A virtuális hálózatok erőforrásai egy futó függvényből érhetők el virtuális hálózati integráció használatával. További információ: [Virtual Network Integration (virtuális hálózat integrációja](functions-networking-options.md#virtual-network-integration)).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hogyan a szolgáltatási végpontok által védett erőforrások elérését?

A Virtual Network Integration használatával elérheti a szolgáltatás-végpont által védett erőforrásokat egy futó függvényből. További információ: [Virtual Network Integration (virtuális hálózat integrációja](functions-networking-options.md#virtual-network-integration)).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hogyan válthatok ki egy függvényt egy erőforrásból egy virtuális hálózaton?

Lehetővé teszi, hogy a HTTP-eseményindítók a [szolgáltatási végpontok](./functions-networking-options.md#private-site-access)használatával legyenek meghívva egy virtuális hálózatból. 

A függvényt a virtuális hálózat minden más erőforrásáról is aktiválhatja, ha a Function alkalmazást egy prémium csomagra, App Service csomagra vagy App Service Environmentra telepíti. További információ: [nem http virtuális hálózati eseményindítók](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hogyan helyezhetem üzembe a Function alkalmazást egy virtuális hálózaton?

A App Service Environment való üzembe helyezés az egyetlen módszer egy olyan Function-alkalmazás létrehozására, amely teljes egészében egy virtuális hálózaton belül van. A belső terheléselosztó App Service Environment használatával történő használatáról a [belső terheléselosztó létrehozása és használata app Service Environmentkal](../app-service/environment/create-ilb-ase.md)című cikkből tájékozódhat.

Olyan helyzetekben, ahol csak egyirányú hozzáférésre van szükség a virtuális hálózati erőforrásokhoz vagy kevésbé átfogó hálózati elkülönítéshez, tekintse meg a [függvények hálózatkezelésének áttekintése](functions-networking-options.md)című témakört.

## <a name="next-steps"></a>További lépések

További információ a hálózatkezelésről és a függvényekről: 

* [Kövesse a virtuális hálózatok integrálásának első lépéseit ismertető oktatóanyagot](./functions-create-vnet.md)
* [További információ a Azure Functions hálózatkezelési lehetőségeiről](./functions-networking-options.md)
* [További információ a App Service és a functions Virtual Network Integration szolgáltatásáról](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatokról](../virtual-network/virtual-networks-overview.md)
* [További hálózatkezelési funkciók és vezérlés App Service környezetekkel](../app-service/environment/intro.md)

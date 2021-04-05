---
title: Azure Functions dedikált üzemeltetés
description: Ismerje meg, hogy milyen előnyökkel jár a Azure Functions futtatása dedikált App Service üzemeltetési tervben.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: a9f976eda4a551c302ba7df92fbdbbf7a4fce1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704565"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Dedikált üzemeltetési csomagok Azure Functions

Ez a cikk azt ismerteti, hogyan üzemeltetheti a Function alkalmazást egy App Service csomagban, például egy App Service Environmentban. Más üzemeltetési lehetőségekért tekintse meg a [üzemeltetési tervről szóló cikket](functions-scale.md).

Az App Service-csomag számítási erőforrásokat határoz meg az alkalmazás futtatásához. Ezek a számítási erőforrások a hagyományos üzemeltetésű [_kiszolgálófarm_](https://wikipedia.org/wiki/Server_farm) esetében hasonlóak. Egy vagy több Function-alkalmazás konfigurálható úgy, hogy ugyanazon számítástechnikai erőforrásokon (App Service csomagon) fusson, mint más App Service alkalmazások, például a Web Apps. Ezek a csomagok alapszintű, standard, prémium és elkülönített SKU-ket tartalmaznak. Az App Service-csomag működésével kapcsolatos részletekért tekintse [meg a Azure app Service tervek részletes áttekintését](../app-service/overview-hosting-plans.md).

A következő helyzetekben vegye fontolóra App Service tervet:

* Vannak olyan meglévő, nem használt virtuális gépek, amelyek már futtatnak más App Service-példányokat.
* Egyéni rendszerképet szeretne megadni a függvények futtatásához.

## <a name="billing"></a>Számlázás

Ha más App Service erőforrásokra lenne szüksége, akkor egy App Service csomagban kell fizetnie. Ez eltér a Azure Functions-használati [tervtől](consumption-plan.md) vagy a [prémium](functions-premium-plan.md) szintű üzemeltetési csomagtól, amely a fogyasztáson alapuló Cost-összetevőket tartalmaz. Csak a csomagért számítunk fel díjat, függetlenül attól, hogy hány Function apps vagy Web Apps fut a csomagban. További információkért tekintse meg a [app Service díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="always-on"></a><a name="always-on"></a> Always on

Ha App Service csomagot futtat, engedélyezze a **mindig** beállítást, hogy a Function alkalmazás megfelelően fusson. Egy App Service-csomag esetében a függvények futtatókörnyezete néhány perc inaktivitás után üresjáratba lép, így csak a HTTP-eseményindítók fognak felébredni a függvényekből. Az **Always On** beállítás csak app Service csomagon érhető el. A platform egy használati tervben automatikusan aktiválja a Function Apps szolgáltatást.

Még ha a mindig engedélyezve van, az egyes függvények végrehajtási időtúllépését a `functionTimeout` Project fájl [host.jsjának](functions-host-json.md#functiontimeout) beállítása vezérli.

## <a name="scaling"></a>Méretezés

App Service-csomag használata esetén a további virtuálisgép-példányok hozzáadásával manuálisan is felskálázást hajthat végre. Emellett engedélyezheti az autoscale méretezést is, bár a prémium szintű csomag rugalmas skálázása lassabban történik. További információ: a [Példányszám manuális vagy automatikus skálázása](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Egy másik App Service terv kiválasztásával is méretezhető. További információ: alkalmazás vertikális [Felskálázása az Azure-ban](../app-service/manage-scale-up.md). 

> [!NOTE] 
> Ha a JavaScript (Node.js) függvényeket egy App Service-csomagon futtatja, olyan csomagot válasszon, amely kevesebb vCPU rendelkezik. További információ: [Choose Single-core app Service Plans](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>App Service-környezetek

A App Service Environment (bevezetéssel) való futtatása lehetővé teszi a függvények teljes elkülönítését és a App Service csomagnál nagyobb számú példány kihasználását. Első lépésként tekintse [meg a app Service környezetek bemutatása](../app-service/environment/intro.md)című témakört.

Ha csak egy virtuális hálózatban szeretné futtatni a Function alkalmazást, ezt a [Prémium csomag](functions-premium-plan.md)használatával teheti meg. További információ: [Azure functions Private site Access létrehozása](functions-create-private-site-access.md). 

## <a name="next-steps"></a>Következő lépések

+ [Üzemeltetési lehetőségek Azure Functions](functions-scale.md)
+ [Az Azure App Service-csomagok áttekintése](../app-service/overview-hosting-plans.md)

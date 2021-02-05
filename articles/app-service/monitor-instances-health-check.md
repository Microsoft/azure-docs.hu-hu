---
title: App Service példányok állapotának figyelése
description: Megtudhatja, hogyan figyelheti App Service példányok állapotát az állapot-ellenőrzés használatával.
keywords: Azure app Service, webalkalmazás, állapot-ellenőrzés, útvonal forgalma, kifogástalan példányok, elérési út, figyelés,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 8892723ec1a53c59e3e6183b5d53c2e61db4e5d0
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575228"
---
# <a name="monitor-app-service-instances-using-health-check"></a>App Service példányok figyelése állapot-ellenőrzés használatával

![Állapot-ellenőrzési hiba][2]

Ez a cikk az App Service példányok figyelésére használja a Azure Portal állapot-ellenőrzését. Az állapot-ellenőrzés a nem kifogástalan állapotú példányok eltávolításával növeli az alkalmazás rendelkezésre állását. Az [app Service-tervet](/azure/app-service/overview-hosting-plans) két vagy több példányra kell méretezni az állapot-ellenőrzési szolgáltatás használatához. Az állapot-ellenőrzési útvonalnak ellenőriznie kell az alkalmazás kritikus összetevőit. Ha például az alkalmazás egy adatbázistól és egy üzenetkezelő rendszertől függ, az állapot-ellenőrzési végpontnak csatlakoznia kell ezekhez az összetevőkhöz. Ha az alkalmazás nem tud csatlakozni egy kritikus összetevőhöz, az elérési útnak egy 500 szintű választ kell visszaadnia, amely azt jelzi, hogy az alkalmazás nem kifogástalan állapotú.

## <a name="what-app-service-does-with-health-checks"></a>App Service az állapot-ellenőrzésekkel

- Ha egy elérési utat adott meg az alkalmazáson, az állapot-ellenőrzési szolgáltatás az App Service alkalmazás összes példányán 1 percenként Pingeli az elérési utat.
- Ha egy példány kettő vagy több kérelem után nem válaszol 200-299 (beleértve), vagy nem válaszol a pingelésre, a rendszer megállapítja, hogy az nem kifogástalan állapotú, és eltávolítja azt.
- Az eltávolítást követően az állapot-ellenőrzési folyamat továbbra is Pingeli a nem kifogástalan állapotú példányt. Ha a művelet továbbra is sikertelenül válaszol, App Service újraindítja az alapul szolgáló virtuális gépet annak érdekében, hogy a példányt visszaadja a kifogástalan állapotnak.
- Ha egy példány nem kifogástalan állapotú egy órára, az új példánnyal lesz lecserélve.
- Emellett a fel-vagy kiskálázáskor App Service pingelése az állapot-ellenőrzési útvonalon, hogy az új példányok készen álljanak.

> [!NOTE]
> Az állapot-ellenőrzési művelet nem követi a 302-es átirányítást. Legfeljebb egy példány óránként lesz lecserélve, és App Service-csomag esetében legfeljebb három példány naponta.
>

## <a name="enable-health-check"></a>Állapot-ellenőrzési engedélyezése

![Állapot-ellenőrzési navigálás az Azure Portalon][3]

- Az állapot-ellenőrzési engedélyezéséhez keresse meg a Azure Portal, és válassza ki a App Service alkalmazást.
- A **figyelés** területen válassza az **állapot-ellenőrzés** elemet.
- Válassza az **Engedélyezés** lehetőséget, és adjon meg egy érvényes URL-útvonalat az alkalmazáson, például: `/health` vagy `/api/health` .
- Kattintson a **Mentés** gombra.

> [!CAUTION]
> Állapot-ellenőrzési konfigurációs változások újraindítása az alkalmazást. Az üzemi alkalmazások hatásának csökkentése érdekében javasoljuk az [átmeneti tárolóhelyek konfigurálását](deploy-staging-slots.md) és az éles környezetbe való cserét.
>

### <a name="configuration"></a>Konfiguráció

Az állapot-ellenőrzési beállítások konfigurálása mellett a következő [Alkalmazásbeállítások](configure-common.md)is megadhatók:

| Alkalmazás-beállítás neve | Megengedett értékek | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | A pingelési hibák maximális száma. Ha például a értékre van állítva `2` , a rendszer eltávolítja a példányokat a `2` sikertelen pingelések után. Emellett, ha a vertikális felskálázást végzi, App Service Pingeli az állapot-ellenőrzési útvonalat, hogy az új példányok készen álljanak. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Ha el szeretné kerülni, hogy az egészséges példányok ne legyenek túlterhelve, a példányok több mint fele ki lesz zárva. Ha például egy App Service csomag négy példányra van méretezve, és a három nem kifogástalan állapotú, akkor a legtöbb kettő kizárja. A másik két példány (egy kifogástalan és egy sérült) továbbra is fogadja a kérelmeket. Abban a legrosszabb esetben, ha az összes példány állapota nem kifogástalan, a rendszer nem zárja ki az egyiket sem. A viselkedés felülbírálásához állítsa a és a közötti értéket `0` `100` . A magasabb érték azt jelenti, hogy A rendszer eltávolítja a nem megfelelő állapotú példányokat (az alapértelmezett érték 50). |

#### <a name="authentication-and-security"></a>Hitelesítés és biztonság

Az állapot-ellenőrzési funkció a App Service hitelesítési és engedélyezési funkcióival integrálódik. Ha ezek a biztonsági funkciók engedélyezve vannak, nincs szükség további beállításokra. Ha azonban saját hitelesítési rendszerét használja, az állapot-ellenőrzési útvonalnak engedélyeznie kell a névtelen hozzáférést. Ha a hely csak HTTP **-alapú**, akkor a rendszer az állapot-ellenőrzési kérelmet a http-**s** protokollon keresztül küldi el.

A nagyméretű nagyvállalati fejlesztési csapatoknak gyakran kell megfelelniük a feltehetően elérhető API-k biztonsági követelményeinek. Az állapot-ellenőrzési végpont biztonságossá tételéhez először olyan szolgáltatásokat kell használnia, mint például az [IP-korlátozások](app-service-ip-restrictions.md#set-an-ip-address-based-rule), az [ügyféltanúsítványok](app-service-ip-restrictions.md#set-an-ip-address-based-rule)vagy a Virtual Network az alkalmazások hozzáférésének korlátozásához. Az állapot-ellenőrzési végpontot a `User-Agent` bejövő kérelem egyezésének megkövetelésével biztonságossá teheti `ReadyForRequest/1.0` . A User-Agent nem lehet meghamisítani, mert a kérést a korábbi biztonsági funkciók már biztosítják.

## <a name="monitoring"></a>Figyelés

Az alkalmazás állapot-ellenőrzési útvonalának megadása után Azure Monitor használatával figyelheti a webhely állapotát. A portál **állapot-ellenőrzési** paneljén kattintson a felső eszköztár **metrikái** elemére. Ekkor megnyílik egy új panel, amelyen megtekintheti a hely korábbi állapotának állapotát, és létrehozhat egy új riasztási szabályt. A helyek figyelésével kapcsolatos további információkért [tekintse meg a következő útmutatót: Azure monitor](web-sites-monitor.md).

## <a name="next-steps"></a>Következő lépések
- [Műveletnapló-riasztás létrehozása az összes autoskálázási motor műveleteinek figyeléséhez az előfizetésen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Műveletnapló-riasztás létrehozása az összes sikertelen, az előfizetésen kívüli méretezési és kibővítő művelet figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png

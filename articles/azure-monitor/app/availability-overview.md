---
title: Application Insights rendelkezésre állás áttekintése
description: Ismétlődő webes teszteket állíthat be az alkalmazás vagy webhely rendelkezésre állásának és válaszkészségének figyelése érdekében.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600794"
---
# <a name="availability-tests-overview"></a>Rendelkezésre állási tesztek áttekintése

A webalkalmazás/webhely üzembe helyezése után ismétlődő teszteket állíthat be a rendelkezésre állás és a válaszkészség figyelése érdekében. [Application Insights](./app-insights-overview.md) webes kéréseket küld rendszeres időközönként az alkalmazásnak a világ minden tájáról. Riasztást küld, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

Rendelkezésre állási teszteket állíthat be bármely olyan HTTP- vagy HTTPS-végponthoz, amely a nyilvános internetről érhető el. A tesztelt webhelyen nem kell módosításokat változtatnia. Valójában nem is kell saját webhelynek lennie. Tesztelheti egy olyan REST API, amelytől a szolgáltatás függ.

## <a name="types-of-availability-tests"></a>Rendelkezésre állási tesztek típusai

A rendelkezésre állási teszteknek három típusa van:

* [URL-ping teszt:](monitor-web-app-availability.md)Ez a kategória két egyszerű tesztet tartalmaz, amelyek a portálon keresztül hozhatók létre.
* [Többlépéses webes](availability-multistep.md)teszt: Webes kérések sorozatának rögzítése, amely visszajátszható összetettebb forgatókönyvek teszteléséhez. A többlépéses webes tesztek az Enterprise Visual Studio, és fel vannak töltve a portálra végrehajtásra.
* [Egyéni rendelkezésre állási tesztek](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)nyomon követése: Ha úgy dönt, hogy egyéni alkalmazást hoz létre a rendelkezésre állási tesztek futtatásához, a metódussal elküldheti az eredményeket a `TrackAvailability()` Application Insights.

> [!IMPORTANT]
> Mind az [URL-pingelési teszt,](monitor-web-app-availability.md) mind a többlépéses webes teszt a nyilvános internetes [DNS-infrastruktúrára](availability-multistep.md) támaszkodik a tesztelt végpontok tartománynevének feloldásához. Ez azt jelenti, hogy saját DNS használata esetén biztosítania kell, hogy a teszt minden tartományneve feloldható legyen a nyilvános tartománynév-kiszolgálók [](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) által, vagy ha ez nem lehetséges, egyéni nyomon követhető rendelkezésre állási teszteket is használhat.

**Erőforrásonként legfeljebb 100 rendelkezésre állási tesztet Application Insights létrehozhat.**

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárítási cikk.](troubleshoot-availability.md)

## <a name="next-step"></a>Következő lépés

* [Rendelkezésre állási riasztások](availability-alerts.md)
* [Többlépéses webes tesztek](availability-multistep.md)
* [URL-tesztek](monitor-web-app-availability.md)
* [Egyéni rendelkezésre állási tesztek létrehozása és futtatása Azure Functions.](availability-azure-functions.md)
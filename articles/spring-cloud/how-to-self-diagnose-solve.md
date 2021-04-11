---
title: Az Azure Spring Cloud szolgáltatásban felmerülő problémák önálló diagnosztizálása és megoldása
description: Ismerje meg, hogyan diagnosztizálhatja és megoldhatja a problémákat az Azure Spring Cloud-ban.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: 03b29e6ddd16731073b33fef14d5aafb54df2abe
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227857"
---
# <a name="self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Problémák önálló diagnosztizálása és megoldása az Azure Spring Cloud-ban

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az Azure Spring Cloud Diagnostics egy interaktív megoldás, amely a konfiguráció nélkül elhárítja az alkalmazást. Az Azure Spring Cloud Diagnostics észleli a problémákat, és útmutatást nyújt a problémák elhárításához és megoldásához.

## <a name="prerequisites"></a>Előfeltételek
A exericise elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy üzembe helyezett Azure Spring Cloud Service-példány. Az első lépésekhez kövesse [az alkalmazások Azure CLI-n keresztüli üzembe helyezését ismertető](spring-cloud-quickstart.md) útmutatót.
* Legalább egy alkalmazás már létre van hozva a szolgáltatási példányban.

## <a name="navigate-to-the-diagnostics-page"></a>Navigáljon a diagnosztika lapra
1. Jelentkezzen be az Azure Portalra.
2. Nyissa meg az Azure Spring Cloud **Áttekintés** oldalát.
3. A bal oldali navigációs panelen válassza a **diagnosztizálás és megoldás problémák** elemet.

 ![Diagnosztizálás, problémamegoldás párbeszédpanel](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Naplózott problémák keresése
A probléma megoldásához írjon be egy kulcsszót, vagy kattintson a megoldás csoport lehetőségre a kategória összes elemének megismeréséhez.

 ![Keresési problémák](media/spring-cloud-diagnose/search-detectors.png)

A **konfigurációs kiszolgáló állapotának**, a **konfigurációs kiszolgáló** állapotának vagy a **konfigurációs kiszolgáló frissítési előzményeinek** kiválasztása különböző eredményeket fog megjeleníteni.

![Problémák beállításai](media/spring-cloud-diagnose/detectors-options.png)

Keresse meg a cél detektort, és kattintson rá a végrehajtáshoz. A diagnosztika összefoglalása az érzékelő végrehajtása után jelenik meg. A **teljes jelentés megtekintése** lehetőség kiválasztásával megtekintheti a diagnosztikai adatokat, vagy kattintson a **csempe megjelenítése** gombra a detektorok listájára való visszatéréshez.

 ![Összegző diagnosztika](media/spring-cloud-diagnose/summary-diagnostics.png)

A diagnosztikai adatok lapon módosíthatja a diagnosztikai időtartományt a jobb felső sarokban található vezérlővel. Ha további mérőszámokat vagy naplókat szeretne látni, kapcsolja be az egyes diagnosztikai adatokat. A metrikák és naplók 15 perces késéssel is rendelkezhetnek.

 ![Diagnosztika részletei](media/spring-cloud-diagnose/diagnostics-details.png)

Egyes eredmények kapcsolódó dokumentációt tartalmaznak.

 ![Kapcsolódó részletek](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Következő lépések
* [Spring Cloud-erőforrások monitorozása riasztások és műveletcsoportok használatával](spring-cloud-tutorial-alerts-action-groups.md)
* [Az Azure Spring Cloud Service biztonsági vezérlői](spring-cloud-concept-security-controls.md)
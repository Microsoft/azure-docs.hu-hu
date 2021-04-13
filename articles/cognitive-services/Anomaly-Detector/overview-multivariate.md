---
title: Mi az az anomália-detektor többváltozós API?
titleSuffix: Azure Cognitive Services
description: Az új anomália-detektor nyilvános előzetese többváltozós API-k áttekintése.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomáliák észlelése, gépi tanulás, algoritmusok
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315570"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Többváltozós idősorozat – anomáliák észlelése (nyilvános előzetes verzió)

Az Azure Cognitive Services anomália-detektor első kiadása lehetővé tette, hogy metrikák figyelésére szolgáló megoldásokat építsen ki a könnyen használható [univariate Time Series rendellenesség-érzékelő API](overview.md)-k használatával. Az idősorozatok egyenkénti elemzésének engedélyezésével a rendellenesség-érzékelő univariate egyszerűséget és méretezhetőséget biztosít.

Az új **többváltozós anomália-észlelési** API-k lehetővé teszik a fejlesztők számára, hogy könnyen integrálják a speciális AI-t a metrikák csoportjaiból származó rendellenességek észleléséhez, anélkül, hogy gépi tanulási ismeretekre vagy címkézett adatokra lenne szükség Az akár 300 különböző jelek közötti függőségek és korrelációs összefüggések mostantól kulcsfontosságú tényezőként számítanak. Ez az új funkció lehetővé teszi az összetett rendszerek, például a szoftveralkalmazások, a kiszolgálók, a gyári gépek, az űrhajók vagy akár üzleti alkalmazások proaktív védelemmel való ellátását a hibáktól.

Képzelje el, hogy egy automatikus motor 20 különböző jeleket hoz létre, például vibrációt, hőmérsékletet, üzemanyag-nyomást stb. Ezeknek a jeleknek a beolvasása nem sokat jelent a rendszerszintű problémákról, de ezek együttesen a motor állapotát jelölik. Ha a jelek interakciója a szokásos tartományon kívül esik, a többváltozós anomália-észlelési funkció a tapasztalt szakértőhöz hasonló anomália lehet. Az alapul szolgáló AI-modellek a saját adataikkal vannak betanítva és testre szabva, hogy megértse a vállalat egyedi igényeit. Az új API-k az anomáliák Detektorában a fejlesztők mostantól egyszerűen integrálják a többváltozós idősorozat-rendellenességek észlelési képességeit a prediktív karbantartási megoldásokba, a AIOps-figyelési megoldásokkal összetett vállalati szoftvereket vagy üzleti intelligencia eszközöket.

## <a name="when-to-use-multivariate-versus-univariate"></a>Mikor kell használni a **többváltozós** és a **univariate**

Univariate anomália-észlelési API-k használata, ha a cél az, hogy észlelje, hogy az egyes idősorozatok normál mintája rendellenes, a saját korábbi adatai alapján. Példák: a napi bevételi rendellenességeket a bevételi adatforrások alapján szeretné észlelni, vagy a CPU-csúcsokat kizárólag a CPU-adatforrások alapján szeretné észlelni.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Az idősorozat-grafikon egyetlen változónak az Orange-körök által azonosított rendellenességeket tartalmazó, kék vonallal rögzített értékének ingadozása](./media/anomaly_detection2.png)

Használjon többváltozós anomália-észlelési API-kat, ha a cél a rendszerszintű anomáliák észlelése idősoros adatok csoportjából. Különösen, ha az egyes idősorozatok nem sokat mondanak, és az összes jelet (az idősorozatok egy csoportját) átfogóan kell megvizsgálni a rendszerszintű probléma meghatározásához. Példa: drága fizikai eszköz, például repülőgép, olaj-fúrótorony vagy szatellit. Ezen eszközök mindegyike több mint tíz vagy több száz különböző típusú érzékelőt tartalmaz. Ezeket az idősorozat-jeleket az érzékelőktől kell megvizsgálnia, hogy eldöntse, van-e rendszerszintű probléma.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Több idősoros vonalas diagram a következő változókhoz: rezgés, hőmérséklet, nyomás, sebesség, forgási sebesség, a narancssárga színnel kiemelt anomáliák](./media/multivariate-graph.png)

## <a name="region-support"></a>Régiós támogatás

Az anomália-detektor több változójának nyilvános előzetes verziója jelenleg három régióban érhető el: Nyugat-RÉGIÓJA, Kelet-RÉGIÓJA és Nyugat-Európa.

## <a name="algorithms"></a>Algoritmusok

- [Többváltozós idősorozat – anomáliák észlelése gráf-hálózaton keresztül](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Csatlakozás az Anomáliadetektor-közösséghez

- Csatlakozás az [anomália-detektor Advisors csoportjához a Microsoft Teams](https://aka.ms/AdAdvisorsJoin) szolgáltatásban

## <a name="next-steps"></a>Következő lépések

- Gyors [útmutató.](./quickstarts/client-libraries-multivariate.md)
- Ajánlott [eljárások](./concepts/best-practices-multivariate.md): Ez a cikk a többváltozós API-kkal való használatra javasolt mintákat ismerteti.

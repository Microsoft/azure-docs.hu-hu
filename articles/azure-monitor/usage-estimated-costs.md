---
title: A használat és a becsült költségek figyelése Azure Monitor
description: A Azure Monitor használati és becsült költségek oldal használatának folyamatának áttekintése
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lagayhar
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 94bc4479f4eba195dfb377f33c5a8a44538f035b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619995"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>A használat és a becsült költségek figyelése Azure Monitor

> [!NOTE]
> Ez a cikk bemutatja, hogyan tekintheti meg a használati és becsült költségeket több Azure-figyelési szolgáltatás között. A Azure Monitor adott összetevőivel kapcsolatos kapcsolódó cikkek a következők:
> - A [használat és a költségek kezelése Azure monitor naplókkal](logs/manage-cost-storage.md) : az adatmegőrzési időszak módosításával, valamint az adatfelhasználás elemzésével és riasztásával kapcsolatos költségek szabályozása.
> - [A Application Insights használatának és költségeinek kezelése](app/pricing.md) a Application Insights adatfelhasználásának elemzését ismerteti.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor díjszabási modell

Az alapszintű Azure Monitor számlázási modell egy felhőalapú, fogyasztáson alapuló díjszabás ("utólagos elszámolású"). A fizetés használat alapján történik. A [riasztások, metrikák, értesítések](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) és [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)díjszabási adatai. 

A naplózási adatokra vonatkozó utólagos elszámolású modellen kívül Log Analytics kapacitás-foglalásokkal rendelkezik, amelyek lehetővé teszik, hogy 25%-ot takarítson meg az utólagos elszámolású díjszabáshoz képest. A kapacitás foglalásának díjszabása lehetővé teszi, hogy a foglalást 100 GB/nap után vásárolja meg. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel. [További](https://azure.microsoft.com/pricing/details/monitor/) információ a kapacitás foglalásának díjszabásáról.

Egyes ügyfelek hozzáférhetnek a [régi log Analytics díjszabási szintjeihez](logs/manage-cost-storage.md#legacy-pricing-tiers) és az [örökölt vállalati Application Insights díjszabási szintjéhez](app/pricing.md#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>A Azure Monitor költségeinek megismerése

A költségek megértéséhez két fázis van. Az első a figyelési megoldás Azure Monitorének megfontolása. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>A környezet kezelésével kapcsolatos költségek becslése

Ha még nem használ Azure Monitor naplókat, a [Azure monitor árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=monitor) használatával megbecsülheti a Azure monitor használatának költségeit. Először írja be a "Azure Monitor" kifejezést a keresőmezőbe, és kattintson az eredményül kapott Azure Monitor csempére. Görgessen le az oldalról Azure Monitorre, és válassza ki az egyik lehetőséget a típus legördülő listából:

- Metrikák lekérdezései és riasztásai  
- Log Analytics
- Application Insights

Ezek mindegyikében a díjszabási kalkulátor a várt kihasználtság alapján a várható költségek becslését fogja segíteni.

Log Analytics például megadhatja a virtuális gépek számát és az egyes virtuális gépekről begyűjteni kívánt GB-nyi adatot. Egy tipikus Azure-beli virtuális gépről általában 1 GB – 3 GB adathónapot kell betölteni. Ha már kiértékeli Azure Monitor naplókat, saját környezetében is használhatja az adatstatisztikát. A [figyelt virtuális gépek számának](logs/manage-cost-storage.md#understanding-nodes-sending-data) és a [munkaterület által betöltött adatmennyiségnek](logs/manage-cost-storage.md#understanding-ingested-data-volume)a meghatározását alább találja.

A Application Insightshoz hasonlóan, ha engedélyezi az "adatmennyiség becslése az alkalmazási tevékenység alapján" funkciót, megadhatja az alkalmazással kapcsolatos bemeneteket (havi kérések havonta, a havi oldalmegtekintések esetén, ha az ügyféloldali telemetria gyűjti), majd a Számológép a hasonló alkalmazások által összegyűjtött adatmennyiség középértékét és 90%-os százalékos értékét fogja megállapítani. Ezek az alkalmazások a Application Insights konfiguráció tartományára terjednek ki (például néhány alapértelmezett mintavételezéssel, némelyikük nem tartalmaz mintavételezést stb.), így továbbra is szabályozhatja, hogy az Ön által használt adatmennyiség csökkenthető legyen a medián szint alatt, a mintavételezés használatával. Ez azonban egy kiindulási pont, amelyből megismerheti, hogy milyen más, hasonló ügyfelek látják. [További](app/pricing.md#estimating-the-costs-to-manage-your-application) információ a Application Insights költségeinek becsléséről.

### <a name="understanding-your-usage-and-estimated-costs"></a>A használat és a becsült költségek megismerése

Fontos megérteni és nyomon követni a használatot a Azure Monitor használata után, és számos eszköz áll rendelkezésére. 

Az Azure nagyszerű hasznos funkciókat biztosít a [Azure Cost Management + számlázási](../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) központban. A **Azure Cost Management + számlázási** központ megnyitása után kattintson a **Cost Management** elemre, és válassza ki a [hatókört](../cost-management-billing/costs/understand-work-scopes.md) (a vizsgálni kívánt erőforrások készletét). 

Ezután az elmúlt 30 nap Azure Monitor költségeinek megtekintéséhez kattintson a **napi költségek** csempére, válassza az "utolsó 30 nap" lehetőséget a relatív dátumok szakaszban, és adjon hozzá egy szűrőt, amely kiválasztja a szolgáltatás nevét:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Betekintések és elemzés

Ez a következő nézetet eredményezi:

![Képernyőkép Azure Cost Management](./media/usage-estimated-costs/010.png)

Innen kihasználhatja ezt a halmozott díjszabási összefoglalót, hogy minél finomabb részleteket kapjon a "Cost by Resource" nézetben. A jelenlegi díjszabási szinten az Azure log-adatok ugyanarra a mérőórákra vannak terhelve, függetlenül attól, hogy Log Analytics vagy Application Insights származik-e. A költségek Log Analytics vagy Application Insights használatból való elkülönítéséhez hozzáadhat egy szűrőt az erőforrás- **típushoz**. Az Application Insights összes költségének megjelenítéséhez szűrje az erőforrás típusát "Microsoft. bepillantások/összetevők" értékre, és Log Analytics a költségek, az erőforrástípus szűrése "Microsoft. operationalinsights/workspaces" értékre. 

A használatról részletesebben is tájékozódhat, ha [letölti a használatot a Azure Portal](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). A letöltött számolótáblában napi Azure-erőforrás-használatot láthat. Ebben az Excel-táblázatban a Application Insights-erőforrásokkal való használat első szűréssel megtalálhatók a "Application Insights" és a "Log Analytics" megjelenítéséhez, majd hozzá kell adni egy szűrőt a "példány azonosítója" oszlophoz, amely "a Microsoft. bepillantást vagy összetevőket tartalmazza".  A legtöbb Application Insights-használat a Log Analytics mérőszám-kategóriával rendelkező fogyasztásmérőn szerepel, mivel az összes Azure Monitor összetevőhöz egyetlen naplós háttér tartozik.  A rendszer csak a régi árképzési szinteken és a többlépéses webes tesztek Application Insights erőforrásait jeleníti meg Application Insights fogyasztásmérő-kategóriája alapján.  A használat megjelenik a "felhasznált mennyiség" oszlopban, és az egyes bejegyzések egysége a "mértékegység" oszlopban látható.  További részletek is elérhetők, ha [meg szeretné ismerni Microsoft Azure-számláját](../cost-management-billing/understand/review-individual-bill.md). 

> [!NOTE]
> A **Cost Management** használata a **Azure Cost Management + számlázási** központban az előnyben részesített módszer a figyelési költségek széles körű megismerésére.  A **használat és a becsült költségek** a [Log Analytics](logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs)  és a [Application Insights](app/pricing.md#understand-your-usage-and-estimate-costs) számára egyaránt lehetővé teszik a Azure monitor egyes részeinek mélyebb elemzését.

Egy másik lehetőség a Azure Monitor használatának megtekintésére a figyelő központ **használati és becsült költségek** lapja. Ez az alapvető figyelési funkciók, például a [riasztások, a metrikák](https://azure.microsoft.com/pricing/details/monitor/), az értesítések, az [Azure log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)és az [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)használatát mutatja be. A 2018. április előtt elérhető díjszabási csomaggal rendelkező ügyfelek esetében az elemzések és az elemzési ajánlat keretében vásárolt Log Analytics használat is szerepel.

Ezen a lapon a felhasználók megtekinthetik az elmúlt 31 napra vonatkozó erőforrás-használatot, az előfizetések összesített száma alapján. `Drill-ins` a használati trendek megjelenítése a 31 napos időszakban. Ehhez a becsléshez nagy mennyiségű adatra van szükség, ezért kérjük, legyen türelemmel az oldal betöltésekor.

Ez a példa a figyelési használatot és az eredményül kapott költségek becslését mutatja be:

![A használati és becsült költségek portál képernyőképe](./media/usage-estimated-costs/001.png)

Válassza ki a havi használati oszlop hivatkozását egy olyan diagram megnyitásához, amely az elmúlt 31 napos időszakban a használati trendeket mutatja: 

![Node Bar-diagramhoz tartozó képernyőkép](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Az Operations Management Suite előfizetési jogosultságai

Azok az ügyfelek, akik megvásárolták a Microsoft Operations Management Suite E1-et és az E2-t, a [log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) és a [Application Insights](app/pricing.md)esetében a csomóponton belüli adatfeldolgozási jogosultságok A jogosultságok Log Analytics-munkaterületekhez vagy Application Insights erőforrásokhoz való fogadásához egy adott előfizetésben: 

- Log Analytics munkaterületek esetében a "node (OMS)" árképzési szintet kell használni.
- Application Insights erőforrásoknak a "vállalati" árképzési szintet kell használniuk.

A szervezet által megvásárolt csomag csomópontjainak számától függően érdemes lehet egy előfizetést áthelyezni egy utólagos elszámolású (GB-os) díjszabási rétegre, de ez gondos figyelmet igényel.

> [!WARNING]
> Ha a szervezete a jelenlegi Microsoft Operations Management Suite E1 és E2 értékkel rendelkezik, általában a "csomópontos (OMS)" árképzési rétegben és Application Insights a "vállalati" díjszabási szinten érdemes megtartani a Log Analytics-munkaterületeket. 
>


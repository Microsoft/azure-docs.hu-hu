---
title: Azure-költségek kezelésének tervezése
description: Megismerheti, hogyan tervezheti meg az Azure-költségek kezelését, illetve hogyan használhatja az Azure-fiók költségkövetési és -kezelési funkcióit.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 210f7daa9305f2fb343dfae050354264e2fd823e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898562"
---
# <a name="plan-to-manage-azure-costs"></a>Azure-költségek kezelésének tervezése

Ez a cikk segít az Azure-költségkezelés megtervezésének első lépéseiben. Az Azure-ra való regisztráció során sokat tehet azért, hogy jobban megismerje a várható költségeket:

- Lekérheti a becsült költségeket a [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/), Azure-árlistával vagy szolgáltatások az Azure Portalon való hozzáadásakor.
- Monitorozhatja a költségeket [költségvetésekkel](../costs/tutorial-acm-create-budgets.md), [riasztásokkal](../costs/cost-mgt-alerts-monitor-usage-spending.md) és [költségelemzésekkel](../costs/quick-acm-cost-analysis.md).
- Áttekintheti a számlán feltüntetett díjakat a [használati adatokat tartalmazó fájlokkal](../manage/download-azure-invoice-daily-usage-date.md) összehasonlítva.
- Integrálhatja a számlázási és költségadatokat saját jelentéskészítő rendszerébe a [számlázási](/rest/api/billing/) és [használati](/rest/api/consumption/) API-kkal.
- A Nagyvállalati Szerződéses (EA), felhőszolgáltatói (CSP-) és Azure Sponsorship-ügyfelek részére elérhető további erőforrásokat vehet igénybe.
- [12 hónapig ingyenesen használhatja a legnépszerűbb Azure-szolgáltatások némelyikét](../manage/create-free-services.md), amelyek az [ingyenes Azure-fiókhoz](https://azure.microsoft.com/free/) érhetőek el. Az alább felsorolt ajánlásokon kívül tekintse meg, [hogyan kerülheti el, hogy díjak merüljenek fel ingyenes fiókja használatakor](../manage/avoid-charges-free-account.md).

Ha le szeretné mondani Azure-előfizetését, tekintse meg az [Az Azure-előfizetés lemondása](../manage/cancel-azure-subscription.md) című témakört.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Becsült költségek lekérése az Azure-szolgáltatások hozzáadása előtt

Az alábbi eszközöket használhatja az Azure-szolgáltatások költségeinek megbecsléséhez:
- Azure-díjkalkulátor
- Azure-árlista
- Azure Portal

A következő szakaszokban található képek példa díjszabásokat jelenítenek meg amerikai dollárban kifejezve. Az itt megadott árak csupán például szolgálnak. Nem feltétlenül tükrözik a tényleges költségeket. 

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>A díjkalkulátor használatával online becsülheti meg a költségeket

Tekintse meg a [díjkalkulátort](https://azure.microsoft.com/pricing/calculator/) a hozzáadni kívánt szolgáltatás becsült havi költségeinek lekéréséhez. Módosíthatja a pénznemet, hogy a helyi pénznemben kérje le a becsült költségeket.

![Képernyőkép a díjkalkulátor menüjéről](./media/plan-manage-costs/pricing-calc.png)

Megtekintheti bármely belső Azure-szolgáltatás becsült költségeit. Az alábbi képernyőképen például az A1 Windows rendszerű virtuális gép üzemórában mért 66,96 dollár/hónapos becsült költsége látható, ha a virtuális gép az időszak során végig üzemel:

![Képernyőkép a díjkalkulátorról, amely az A1 Windows rendszerű virtuális gép becsült havi költségét mutatja](./media/plan-manage-costs/pricing-calc-vm.png)

Az itt megadott árak csupán például szolgálnak. Nem feltétlenül tükrözik a tényleges költségeket.

A díjszabással kapcsolatos további információkért tekintse meg a [díjszabással kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/pricing/faq/). Ha beszélni szeretne egy Azure-értékesítővel, hívja fel a GYIK oldal tetején található telefonszámot.

### <a name="review-prices"></a>Árak áttekintése

Ha az Azure-t Nagyvállalati Szerződéssel (EA) vagy Microsoft-ügyfélszerződéssel (MCA) használja, megtekintheti és letöltheti az árlistát az Azure-fiókjához. Az árlista egy Excel-fájl, amely tartalmazza az összes Azure-szolgáltatás árát. További információ: [Az Azure díjszabásának megtekintése és letöltése](../manage/ea-pricing.md).

Más előfizetési típusok esetén a standard kiskereskedelmi árakat az [Azure Retail Prices API-val](/rest/api/cost-management/retail-prices/azure-retail-prices) kérheti le.

### <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

Megtekintheti a becsült havi költségeket, miközben hozzáad egy szolgáltatást az Azure Portalon. Amikor például a Windows rendszerű virtuális gép méretét kiválasztja, az üzemórák becsült havi díja látható:

![Példa: az A1 Windows rendszerű virtuális gép becsült havi költségének bemutatása](./media/plan-manage-costs/vm-size-cost.png)

Az itt megadott árak csupán például szolgálnak. Nem feltétlenül tükrözik a tényleges költségeket.

## <a name="monitor-costs-when-using-azure-services"></a>Költségek monitorozása az Azure-szolgáltatások használatakor
A költségek a következő eszközökkel monitorozhatók:

- Költségvetés- és költségriasztások
- Költségelemzés

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Költségek nyomon követése költségvetésekkel és költségriasztásokkal

A költségek kezeléséhez [költségvetéseket](../costs/tutorial-acm-create-budgets.md) és [riasztásokat](../costs/cost-mgt-alerts-monitor-usage-spending.md) hozhat létre, amelyek automatikusan figyelmeztetik Önt és az érdekelt feleket a rendellenes kiadásokról és a túlköltekezésről.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Költségek feltérképezése és elemzése a költségelemzés szolgáltatás használatával

Az Azure-szolgáltatások elindítása után rendszeresen tekintse meg a költségeket az Azure-költségek nyomon követéséhez. A költségelemzés segítségével megállapíthatja, hol keletkeznek az Azure használatából eredő költségei.

Nyissa meg [az Azure Portal Költségkezelés + számlázás oldalát](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

A képernyő bal oldalán kattintson a **Költségelemzés** elemre, hogy megtekinthesse az aktuális költségeket különféle területekre bontva (például szolgáltatás, hely és előfizetés). A szolgáltatások hozzáadása vagy a vásárlás végrehajtása után várjon 24 órát az adatok megjelenéséig. Alapértelmezés szerint a költségelemzés az aktuális hatókör adatait jeleníti meg. Például az alábbi képernyőképen a Contoso számlázási fiókjának költségei jelennek meg. A Hatókör elemmel másik hatókörre válthat a költségelemzésben. A hatókörökkel kapcsolatos további információért tekintse meg [A hatókörök ismertetése és használata](../costs/understand-work-scopes.md#scopes) szakaszt

![Költségelemzés nézet képernyőképe az Azure Portalon](./media/plan-manage-costs/cost-analysis.png)

A szűrést különböző tulajdonságok, például címkék, erőforrástípus, és időtartomány alapján végezheti el. Kattintson a **Szűrő hozzáadása** elemre egy szűrő tulajdonsághoz való hozzáadásához, majd válassza ki a szűrendő értékeket. Kattintson az **Exportálás** elemre a nézet vesszővel tagolt (.csv kiterjesztésű) fájlba való exportálásához.

Emellett a diagram címkéire kattintva megtekintheti a napi kiadások előzményeit az adott címkére vonatkozóan. Például az alábbi képernyőképen látható, hogy egy virtuális gép kiválasztásakor megjelenik a virtuális gép futtatásának napi költsége.

:::image type="content" source="./media/plan-manage-costs/cost-history.png" alt-text="A költési előzmények nézet képernyőképe az Azure Portalon" lightbox="./media/plan-manage-costs/cost-history.png" :::

## <a name="optimize-and-reduce-costs"></a>Költségek optimalizálása és csökkentése

Ha nem ismeri a költségkezelés alapelveit, olvassa el [A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával](../costs/cost-mgt-best-practices.md) cikket.

Az Azure Portalon optimalizálhatja és csökkentheti is az Azure-ral kapcsolatos költségeket a virtuális gépek automatikus leállításával és az Advisor-javaslatokkal.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Vegye fontolóra a virtuális gépek automatikus leállításához hasonló költségkímélő funkciókat

A forgatókönyvtől függően a virtuális gépek automatikus leállítását is konfigurálhatja az Azure Portalon. További információ: [Virtuális gépek automatikus leállítása az Azure Resource Manager használatával](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Képernyőkép a portálon látható automatikus leállítás lehetőségről](./media/plan-manage-costs/auto-shutdown.png)

Az automatikus leállítás nem ugyanaz, mint amikor a virtuális gépet az energiagazdálkodási lehetőségekkel állítja le. Az automatikus leállítás leállítja és felszabadítja a virtuális gépeket, így nem keletkeznek további használati díjak. További információt a [Linux rendszerű virtuális gépekre](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és a [Windows rendszerű virtuális gépekre](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vonatkozó díjszabással kapcsolatos gyakori kérdések oldalon talál.

A fejlesztési és tesztelési környezetekkel kapcsolatos további költségkímélő funkciókért tekintse meg az [Azure DevTest Labst](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Kapcsolja be és tekintse át az Azure Advisor javaslatait

Az [Azure Advisor](../../advisor/advisor-overview.md) az alacsony kihasználtságú erőforrások azonosításával segítséget nyújt a költségek csökkentésében. Keresse meg az **Advisor** elemet az Azure Portalon:

![Képernyőkép az Azure Portalon látható Azure Advisor gombról](./media/plan-manage-costs/advisor-button.png)

A bal oldalon válassza a **Költség** lehetőséget. A **Költségek** lapon végrehajtható javaslatokat fog látni:

![Képernyőkép az Advisor költségekkel kapcsolatos javaslatainak egy példájáról](./media/plan-manage-costs/advisor-action.png)

Az itt megadott árak csupán például szolgálnak. Nem feltétlenül tükrözik a tényleges költségeket.

Tekintse át a [Javaslatok alapján történő költségoptimalizálás](../costs/tutorial-acm-opt-recommendations.md) oktatóanyagot a költségeket csökkentő Advisor-javaslatokról szóló irányított útmutatásért.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integrálás számlázási és használati API-kkal

Használja az Azure [számlázási](/rest/api/billing/) és [használati](/rest/api/consumption/) API-jait a számlázással és a költségekkel kapcsolatos adatok programozott módon való lekéréséhez. A RateCard API és a Usage API együttes használatával lekérhető a számlázott használat. További információkért tekintse meg az [Információk a Microsoft Azure-erőforrások igénybevételéről](../manage/consumption-api-overview.md#usage-details-api) szakaszt.

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> További forrásanyagok és különleges esetek

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP- és Sponsorship-ügyfelek
Az induláshoz lépjen kapcsolatba fiókja kezelőjével vagy Azure-partnerével.

| Ajánlat | További források |
|-------------------------------|-----------------------------------------------------------------------------------|
| Nagyvállalati Szerződés (EA) | [EA Portal](https://ea.azure.com/), [súgódokumentumok](https://ea.azure.com/helpdocs) és [Power BI-jelentések](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Felhőszolgáltató (CSP) | Beszéljen szolgáltatójával |
| Azure Sponsorship | [Sponsorship Portal](https://www.microsoftazuresponsorships.com/) |

Ha egy nagy méretű szervezet informatikai működését felügyeli, javasoljuk az [Azure Enterprise-szerkezet](/azure/architecture/cloud-adoption-guide/subscription-governance) és a [nagyvállalati informatikai tanulmány](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) elolvasását (letölthető .pdf-ként, csak angol nyelven).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Nagyvállalati Szerződések költségnézetei az Azure Portalon

A vállalati költségnézetek jelenleg nyilvános előzetes verzióban érhetők el. Megjegyzések:

- Az előfizetési költségek használaton alapulnak, és nem tartalmazzák az előre fizetett összegeket, a túllépéseket, a szolgáltatási kereteket, a helyesbítéseket és az adókat. A tényleges díjak kiszámítása a regisztráció szintjén történik.
- Az Azure Portalon megjelenített összegek eltérhetnek az Enterprise Portalon látható összegektől. Eltarthat pár percig, amíg az Enterprise Portal frissítései megjelennek az Azure Portalon.
- Ha nem látja a költségeket, az a következő okok valamelyike miatt lehet:
    - Nincsenek engedélyei az előfizetés szintjén. A vállalati költségnézetek megjelenítéséhez számlázási olvasó, olvasó, közreműködő vagy tulajdonos szereppel kell rendelkeznie előfizetési szinten.
    - Ön fióktulajdonos, és a regisztrációs adminisztrátor letiltotta a „díjtételek megtekintésének engedélyezését a fióktulajdonos számára”.  A költségekhez való hozzáférésért vegye fel a kapcsolatot a regisztrációs adminisztrátorral.
    - Ön részlegszintű tulajdonos, és a regisztrációs adminisztrátor letiltotta a **díjtételek megtekintésének engedélyezését a részlegszintű rendszergazda számára**.  Hozzáférésért vegye fel a kapcsolatot a regisztrációs adminisztrátorral.
    - Egy csatornapartneren keresztül vásárolta meg az Azure-t, és a partner nem adta meg a díjszabási információkat.  
- Ha az Enterprise Portalon frissíti a költséghozzáféréshez kapcsolódó beállításokat, néhány percig eltart, amíg a módosítások megjelennek az Azure Portalon.
- A költségkeret és a számlázási útmutató nem vonatkozik a Nagyvállalati Szerződéssel rendelkező előfizetésekre.

### <a name="check-your-subscription-and-access"></a>Az előfizetés és a hozzáférés ellenőrzése

A költségek megtekintéséhez fiók- vagy előfizetési szintű hozzáféréssel kell rendelkeznie a költségekkel vagy a számlázással kapcsolatos információkhoz. A hozzáférés a számlázási fiók típusától függően eltérő. A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókok az Azure Portalon történő megtekintését](../manage/view-all-accounts.md) ismertető cikket.

Amennyiben az Azure-t egy Microsoft Online Service Programba (MOSP) tartozó számlázási fiókon keresztül használja, tekintse meg az [Azure-beli számlázási információkhoz való hozzáférés kezelése](../manage/manage-billing-access.md) című szakaszt.

Amennyiben az Azure-t Nagyvállalati Szerződéssel (EA) rendelkező számlázási fiókkal használja, tekintse meg az [Azure Nagyvállalati Szerződés rendszergazdai szerepkörei az Azure-ban](../manage/understand-ea-roles.md) című szakaszt.

Amennyiben az Azure-t Microsoft-ügyfélszerződéssel (MCA) rendelkező számlázási fiókkal használja, tekintse meg [a Microsoft-ügyfélszerződés Azure-beli felügyeleti szerepköreinek ismertetését](../manage/understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Szolgáltatói szerződési jóváírás kérelmezése egy szolgáltatási esemény miatt

A szolgáltatási szerződés (SLA) ismerteti a Microsoft üzemidővel és hálózati elérhetőséggel kapcsolatos vállalásait. A szolgáltatási eseményeket a rendszer akkor jelenti, ha az Azure-szolgáltatások olyan problémát tapasztalnak, amely hatással van az üzemidőre vagy a kapcsolatra. Ezt gyakran *kimaradásnak* is nevezik. Ha nem érjük el és nem tartjuk fenn az egyes szolgáltatásokhoz tartozó, az SLA-ban leírt szolgáltatási szinteket, akkor Ön jogosult lehet a havi szolgáltatási díjak egy részének jóváírására.

Jóváírás kérése:

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Ha több fiókja is van, ügyeljen arra, hogy azt a fiókot használja, amelyet az Azure-állásidő érintett. Ezután hozzon létre egy új támogatási kérést.

A **problématípusokat tartalmazó** területen válassza a **Számlázás** lehetőséget, majd a **Probléma típusa** területen válassza a **Visszatérítési kérelem** lehetőséget.

A részletek megadásával egyértelműsítse, hogy SLA-jóváírást szeretne, és határozza meg a dátumot/időt/időzónát, valamint az érintett szolgáltatásokat (virtuális gépek, webhelyek stb.).

Végül ellenőrizze a kapcsolattartási adatait, majd a kérés elküldéséhez válassza a **Létrehozás** lehetőséget.

Egyes szolgáltatások esetében az SLA alkalmazása előfeltételektől is függ. A virtuális gépeknek például azonos rendelkezésreállási csoportban két vagy több példányszámban kell üzemelniük.

További információkért tekintse meg a [Szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) és az [Azure-szolgáltatások SLA-jának összegzése](https://azure.microsoft.com/support/legal/sla/summary/) dokumentációt.

## <a name="next-steps"></a>További lépések
- A túlköltekezés elkerülése érdekében ismerkedjen meg a [költségkeretekkel](../manage/spending-limit.md).
- Indítson [Azure-költségelemzést](../costs/quick-acm-cost-analysis.md).
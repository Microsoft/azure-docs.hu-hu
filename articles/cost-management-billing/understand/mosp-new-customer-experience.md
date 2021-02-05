---
title: A frissített Azure-beli számlázási fiók használatának első lépései
description: A frissített Azure-beli számlázási fiók használatának első lépései és a számlázás és a költségkezelés változásai
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: banders
ms.openlocfilehash: 887b7013eb3060020a39d2df0082768b8185bdde
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575466"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>A frissített Azure-beli számlázási fiók használatának első lépései

A költségek és a számlák kezelése a felhőbeli felhasználói élmény egyik fontos velejárója. Segítséget nyújt a felhőbeli kiadások szabályozásához és megértéséhez. Annak érdekében, hogy könnyebben kezelhesse a költségeket és a számlákat, a Microsoft frissíti Azure-beli számlázási fiókját, amely így tartalmazza a továbbfejlesztett költségkezelési és számlázási képességeket. Ez a cikk ismerteti a számlázási fiók frissítéseit, és bemutatja annak új lehetőségeit.

> [!IMPORTANT]
> A fiók akkor fog frissülni, amikor a Microsoft e-mailben értesíti a fiókja frissítéséről. Az értesítést 60 nappal a fiók frissítését megelőzően küldjük el.

## <a name="more-flexibility-with-your-new-billing-account"></a>Az új számlázási fiók nagyobb rugalmasságot nyújt

Az alábbi ábra az új és a régi számlázási fiókot hasonlítja össze:

![Az új és a régi fiók számlázási hierarchiájának összehasonlítását bemutató ábra](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Az új számlázási fiók egy vagy több számlázási profilt tartalmaz, amelyek lehetővé teszik a számlák és a fizetési módok kezelését. Az egyes számlázási profilok egy vagy több számlaszakaszt tartalmaznak, amelyekkel rendszerezhetőek a számlázási profil számláján szereplő költségek.

![Az új számlázási hierarchiát bemutató ábra](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

A számlázási fiók szerepkörei rendelkeznek a legmagasabb szintű engedélyekkel. Ezeket a szerepköröket olyan felhasználókhoz kell hozzárendelni, akiknek meg kell tekinteniük a számlákat és nyomon kell követniük a teljes fiók költségeit, tehát például egy szervezet pénzügyi vagy informatikai vezetőihez vagy egy fiókkal rendelkező személyhez. További információkért lásd [a számlázási fiók szerepköreit és azok feladatait](../manage/understand-mca-roles.md#billing-account-roles-and-tasks) ismertető cikket. A fiók frissítésekor a régi számlázási fiókban rendszergazdai szerepkörrel rendelkező felhasználó tulajdonosi szerepkört kap az új fiókhoz.

## <a name="billing-profiles"></a>Számlázási profilok

A számlákat és a fizetési módokat a számlázási profilok segítségével kezelheti. A hónap elején a fiókhoz tartozó összes számlázási profilhoz létrejön egy havi számla. A számla a számlázási profilhoz társított előfizetések előző havi díjait tartalmazza.

A fiók frissítésekor mindegyik előfizetéshez automatikusan létrejön egy számlázási profil. Az előfizetés díjait a rendszer a megfelelő számlázási profilnál számolja fel, és feltünteti a számlán.

A számlázási profilok szerepkörei rendelkeznek a számlák és a fizetési módok megtekintéséhez és kezeléséhez szükséges engedéllyel. Ezeket a szerepköröket a számlák kifizetéséért felelős felhasználókhoz kell hozzárendelni, például a cég könyvelői csapatának tagjaihoz. További információkért lásd [a számlázási profil szerepköreit és azok feladatait](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) ismertető cikket.

A fiók frissítésekor minden olyan előfizetésben, amelyben engedélyt adott mások számára a [számlák megtekintésére](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice), a tulajdonosi, közreműködői, olvasói vagy számlázási olvasói Azure-szerepkörrel rendelkező felhasználók olvasói szerepkört kapnak a megfelelő számlázási profilhoz.

## <a name="invoice-sections"></a>Számlaszakaszok

A számlaszakaszok segítségével a számlán szereplő költségeket rendszerezheti. Előfordulhat például, hogy csak egy számlára van szüksége, azonban szeretné részlegek, csapatok vagy projektek szerint rendezni a költségeket. Ebben a forgatókönyvben egyetlen számlázási profillal rendelkezik, ahol létrehozhat számlaszakaszokat az egyes részlegek, csapatok vagy projektek számára.

A fiók frissítésekor a rendszer létrehoz egy számlaszakaszt az egyes számlázási profilokhoz, és hozzárendeli a kapcsolódó előfizetést a számlaszakaszhoz. További előfizetések hozzáadásakor további szakaszokat hozhat létre, és hozzájuk rendelheti az előfizetéseket. A szakaszok megjelennek a számlázási profilhoz tartozó számlán, amely a szakaszokhoz társított előfizetések használati adatait veszi alapul.

A számlaszakasz szerepkörei jogosultak szabályozni, ki hozhat létre Azure-előfizetéseket. Ezeket a szerepköröket azokhoz a felhasználókhoz kell hozzárendelni, akik Azure-környezeteket hoznak létre a cég vagy szervezet csapatai számára, például a mérnöki vezetőkhöz és a rendszertervezőkhöz. További információkért lásd [a számlaszakasz szerepköreit és azok feladatait](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks) ismertető részt.

## <a name="enhanced-features-in-your-new-experience"></a>Az új felhasználói felület továbbfejlesztett szolgáltatásai

Az új fiók az alábbi költségkezelési és számlázási képességeket tartalmazza, amelyekkel könnyebben kezelheti a költségeket és a számlákat:

#### <a name="invoice-management"></a>Számlakezelés

**Kiszámíthatóbb havi számlázási időszak** – Az új fiókban a számlázási időszak a hónap első napján kezdődik és a hónap utolsó napján ér véget, függetlenül attól, hogy mikor regisztrált az Azure használatára. A rendszer minden hónap elején létrehoz egy számlát, amely az előző hónap minden díját tartalmazza.

**Egyetlen havi számla több előfizetéshez** – Lehetősége van külön-külön havi számlát kapni mindegyik előfizetéséhez, vagy egyetlen számlát kapni többhöz.

**Egyetlen havi számla az Azure-előfizetésekhez, a támogatási csomagokhoz és az Azure Marketplace-termékekhez** – Egyetlen havi számlát fog kapni, amely az összes költséget tartalmazza, például az Azure-előfizetések használati díját, a támogatási csomagok és az Azure Marketplace-vásárlások költségeit.

**Igények szerint csoportosított költségek a számlán** – Igényeinek megfelelően csoportosíthatja a költségeket a számlán, például részlegek, projektek vagy csapatok szerint.

**Opcionális rendelésszám megadása a számlán** – Ha társítani szeretné a számlát a belső pénzügyi rendszeréhez, ehhez megadhat egy rendelésszámot. Ezt bármikor kezelheti és frissítheti az Azure Portalon.

#### <a name="payment-management"></a>Fizetéskezelés

**Számlák azonnali kifizetése hitelkártyával** – Nem kell megvárnia, amíg automatikusan megterhelik a hitelkártyájához tartozó bankszámlát. Az esedékes vagy a lejárt számlákat bármilyen hitelkártyával kifizetheti az Azure Portalon.

**A fiókhoz tartozó fizetések nyomon követése** – Az Azure Portalon megtekintheti a fiókhoz tartozó fizetéseket, beleértve a használt fizetési módszert, a fizetett összeget és a fizetési dátumot.

#### <a name="cost-management"></a>Költségkezelés

**Használati adatok tárfiókba történő havi exportálásának ütemezése** – Automatikusan továbbíthatja a költségekre és a használatra vonatkozó adatokat egy tárfiókba, napi, heti vagy havi rendszerességgel.

#### <a name="account-and-subscription-management"></a>Fiókok és előfizetések kezelése

**Több rendszergazda hozzárendelése a számlázási műveletek elvégzéséhez** – Több felhasználóhoz is hozzárendelhet számlázási engedélyeket a fiók számlázásának kezeléséhez. Rugalmas rendszert alakíthat ki, ha olvasási, írási vagy mindkét típusú engedélyt ad másoknak.

**További előfizetések létrehozása közvetlenül az Azure Portalon** – Minden előfizetést egyetlen kattintással létrehozhat az Azure Portalon.

#### <a name="api-support"></a>API-támogatás

**Számlázási és költségkezelési műveletek elvégzése API-k, SDK és a PowerShell használatával** – A költségkezelési, számlázási és használati API-k segítségével lekérheti a számlázással és a költségekkel kapcsolatos adatokat az előnyben részesített adatelemző eszközökbe.

**Előfizetési műveletek végrehajtása API-k, SDK és a PowerShell segítségével** – Az Azure-előfizetési API-kkal automatizálhatja az Azure-előfizetések kezelését, például a létrehozást, az átnevezést és a lemondást.

## <a name="get-prepared-for-your-new-experience"></a>Felkészülés az új funkciókra

Az alábbiakat javasoljuk, hogy felkészülhessen az újdonságokra:

**Havi számlázási időszak és eltérő számladátum**

Az új eljárás szerint a számláját a hónap kilencedik napja környékén állítja ki a rendszer. A számla az előző hónap minden díját tartalmazza. Ez a dátum eltérhet attól, amely napon a régi fiókban állította ki a számlát a rendszer. Ha másokkal is megosztja a számlát, értesítse őket az új dátumról.

**Új számlázási és költségkezelési API-k**

Ha Cost Management vagy Billing API-kkal kérdezi le vagy frissíti a számlázással vagy a költségekkel kapcsolatos adatokat, akkor az új API-kat kell használnia. Az alábbi táblázat tartalmazza azokat az API-kat, amelyek nem fognak működni az új számlázási fiókkal, illetve azokat a módosításokat, amelyeket végre kell hajtania az új számlázási fiókjában.

|API | Módosítások  |
|---------|---------|
|[Billing Accounts – List](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | A Billing Accounts – List API-ban a régi számlázási fiók agreementType tulajdonsága **MicrosoftOnlineServiceProgram**, az új számlázási fiók agreementType tulajdonsága pedig **MicrosoftCustomerAgreement**. Ha függőséget szeretne felvenni az agreementType tulajdonsághoz, frissítse. |
|[Invoices – List By Billing Subscription](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Ez az API csak a fiók frissítése előtt létrehozott számlákat fogja visszaadni. Az új számlázási fiókban létrehozott számlák az [Invoices – List By Billing Subscription](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) API használatával tekinthetők meg. |

## <a name="cost-management-updates-after-account-update"></a>A Cost Management frissítése a fiók frissítése után

A Microsoft Ügyfélszerződéséhez tartozó frissített Azure-számlázási fiókkal hozzáférhet az Azure Portal új és kibővített Cost Management-felületeihez, amelyek nem szerepelnek a használatalapú fizetéses fiókjában.

### <a name="new-capabilities"></a>Új képességek

Az alábbi új képességek érhetők el az Azure-számlázási fiókban.

#### <a name="new-billing-scopes"></a>Új számlázási hatókörök

A fiók frissítésének eredményeként új hatókörök érhetők el a Költségkezelés + Számlázás területen. Ezek segítséget nyújtanak a hierarchikus szervezettel és a számlázással kapcsolatban, illetve a segítségükkel megtekinthetők a mögöttes előfizetések összesített költségei is. További információ a számlázási hatókörökkel kapcsolatban: [A Microsoft Ügyfélszerződés hatókörei](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

Emellett a Cost Management API-khoz is hozzáférhet, és megtekintheti a költségek összesített nézeteit magasabb hatókörön. Az előfizetés hatókörét használó Cost Management API-k továbbra is elérhetők a sémában, azonban tartalmaznak néhány apró módosítást. Az ezekkel az API-kkal kapcsolatos további információért tekintse meg az [Azure Cost Management API-k](/rest/api/cost-management/) és [Azure Consumption API-k](/rest/api/consumption/) című dokumentumokat.

#### <a name="cost-allocation"></a>Költséglefoglalás

A fiók frissítése után a költséglefoglalási funkciókkal feloszthatja a szervezet megosztott szolgáltatásainak költségeit. További információ a költségek lefoglalásáról: [Azure-beli költséglefoglalási szabályok létrehozása és kezelése](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

A Power BI Desktophoz készült Azure Cost Management-összekötővel egyéni vizualizációkat és jelentéseket hozhat létre az Azure-beli használatról és költségekről. A költség- és használati adatokat a frissített fiókhoz való csatlakozás után érheti el. További információ a Power BI Desktophoz készült Azure Cost Management-összekötőről: [Vizualizációk és jelentések létrehozása az Azure Cost Management-összekötővel a Power BI Desktopban](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Frissített képességek

Az alábbi frissített képességek érhetők el az Azure-számlázási fiókban.

#### <a name="cost-analysis"></a>Költségelemzés

Továbbra is megtekintheti és nyomon követheti a havi használati költségeket, és mostantól megtekintheti a foglalások és a Marketplace-vásárlások költségeit a Költségelemzésben.

A fiók frissítése után egyetlen számlát fog kapni, amely az Azure összes költségét tartalmazza. Ezenkívül mostantól egy egyszerűbb havi naptárnézet érhető el a számlázási időszakok korábbi nézete helyett.

Ha például a régi fiók számlázási időszaka november 24-től december 23-ig tartott, akkor a frissítés után az időszak megváltozik, és november 1-től november 30-ig tart, december 1-től december 31-ig tart stb.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="A régi és az új számlázási időszak összehasonlítását ábrázoló kép " lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Költségvetések

Mostantól létrehozhatja a számlázási fiók költségvetését, amely lehetővé teszi az előfizetések költségeinek nyomon követését. A költségvetésekkel naprakész lehet a vásárlások költségeivel kapcsolatban is. További információ a költségvetésekről: [Azure-költségvetések létrehozása és kezelése](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>Exportálások

Az új számlázási fiók továbbfejlesztett exportálási funkciókat biztosít. Például létrehozhat exportálásokat az aktuális költségekhez, amelyek tartalmazzák a vásárlások költségeit és az amortizált költségeket (a foglalás vásárlási költségeit a vásárlási időtartamra elosztva). A számlázási fiókhoz is létrehozhat exportálást, amelyből megtudhatja a számlázási fiókban lévő előfizetések díj- és költségadatait. További információ az exportálásokról: [Exportált adatok létrehozása és kezelése](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> A fiók frissítése előtt létrehozott, **A múlt havi költségek havi exportálása** típussal rendelkező exportálások az utolsó naptári hónap adatait exportálják, nem pedig az utolsó számlázási időszakét.

Például a december 23. és a január 22. közötti számlázási időszak esetén az exportált CSV-fájl az erre az időszakra vonatkozó költség- és használati adatokat tartalmazná. A frissítés után az exportálás a naptári hónapra vonatkozó adatokat fogja tartalmazni. Például a január 1. és január 31. közötti adatokat stb.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="A régi és az új exportálási adatok összehasonlítását ábrázoló kép" lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>További információ

A következő szakaszok további információkkal szolgálnak az újdonságokról.

**Nincs szolgáltatáskiesés** Az előfizetésében lévő Azure-szolgáltatások megszakítás nélkül futnak tovább. Csak a számlázás menete frissül. Meglévő erőforrásait, erőforráscsoportjait vagy felügyeleti csoportjait a változás nem érinti.

**Az Azure-erőforrások nem módosulnak** Az Azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával beállított Azure-erőforrások hozzáférését a frissítés nem érinti.

**A régebbi számlák elérhetők maradnak** A fiók frissítése előtt létrejött számlák továbbra is elérhetők lesznek az Azure Portalon.

**A hónap közepén frissített fiók számlái** Ha fiókja a hónap közepén frissül, a rendszer kiállít egy számlát a fiók frissítésének napjáig felgyűlt költségekről. A hónap hátrelévő részéről egy másik számlát fog kapni. Például a fiókja egy előfizetéssel rendelkezik, és szeptember 15-én frissül. Ekkor számlát fog kapni a szeptember 15-ig felgyűlt költségekről. A szeptember 15. és szeptember 30. közötti időszakról egy másik számlát állít ki a rendszer. Szeptember után havonta egy számlát fog kapni.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

A számlázási fiókkal kapcsolatos további tudnivalókért tekintse meg az alábbi cikkeket.

- [Az új számlázási fiók felügyeleti szerepkörei](../manage/understand-mca-roles.md)
- [További Azure-előfizetések létrehozása az új számlázási fiókban](../manage/create-subscription.md)
- [Szakaszok létrehozása a számlán a költségek rendszerezéséhez](../manage/mca-section-invoice.md)
---
title: A Cloud Partner Portalról a partneri központba való áttéréssel kapcsolatos gyakori kérdések – Microsoft kereskedelmi piactér
description: Válaszok az ajánlatok Cloud Partner Portalról a partneri központba való átváltásával kapcsolatos gyakori kérdésekre.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 091feacceb510038786ae487c0895f9ff1e47aba
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880338"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Gyakori kérdések az Cloud Partner Portalról a partneri központba való áttérésről

A Cloud Partner Portal a partner Centerre váltott. A partner Center egy egyszerűsített, integrált élményt nyújt az új ajánlatok közzétételéhez [Microsoft AppSource](https://appsource.microsoft.com/) vagy az [Azure Marketplace](https://azuremarketplace.microsoft.com/) -en, valamint a Cloud Partner Portalból áttelepített meglévő ajánlatok kezeléséhez. A Cloud Partner Portal összes funkciója elérhető a partner Centerben. Ez a cikk az ezzel kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Mit jelent a partner Centerre való áttérés?

Folytathatja az üzleti tevékenységet a partner Centerben:

| Terület<img src="" width=200px> | Módosítások |
| --- | --- |
| Fiók | Nem kell új partner Center-fiókot létrehoznia; a meglévő Cloud Partner Portal hitelesítő adataival bejelentkezhet a fiókpartner-központba, ahol a fiókját, a felhasználóit, az engedélyeit és a számlázást is kezelheti. A közzétételi szerződés és a vállalati profil adatai áttelepülnek az új partner Center-fiókjába, valamint a kifizetési profilokkal, a felhasználói fiókokkal és engedélyekkel, valamint az aktív ajánlatokkal. További információ: [a kereskedelmi piactér fiókjának kezelése a partner Centerben](partner-center-portal/manage-account.md). |
| Ajánlat-közzétételi és ajánlat-kezelési élmény | Áthelyezte az ajánlat adatait a Cloud Partner Portalról a partneri központba. Mostantól elérhetővé teszi az ajánlatokat a partner Centerben, amely továbbfejlesztett felhasználói élményt és intuitív felületet biztosít. Megtudhatja, hogyan [frissíthet egy meglévő ajánlatot a kereskedelmi piactéren](partner-center-portal/update-existing-offer.md). |
| Az ajánlatok elérhetősége a kereskedelmi piactéren | Nincs változás. Ha az ajánlata a kereskedelmi piacon él, továbbra is élőben fog működni. |
| Új vásárlások és központi telepítések | Nincs változás. Az ügyfelek megszakítás nélkül folytathatják az ajánlatok megvásárlását és üzembe helyezését. |
| Kifizetések | A vásárlások és az üzembe helyezések továbbra is a megszokott módon lesznek kifizetve. További információ [a kereskedelmi piactéren való fizetésről](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| API-integrációk meglévő [Cloud Partner Portal API](cloud-partner-portal-api-overview.md) -kkal | A meglévő Cloud Partner Portal API-k továbbra is támogatottak, és a meglévő integrációk továbbra is működnek. További információ: [a Cloud Partner Portal REST API-k támogatottak?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Elemzés | Továbbra is figyelheti az értékesítéseket, kiértékelheti a teljesítményt, és optimalizálhatja ajánlatait a kereskedelmi piactéren az elemzések megtekinthető a partner Centerben. Az analitikai jelentések a CPP-ben és a partner Centerben is megjelennek. Például a CPP-beli **értékesítői** elemzések olyan **Orders & használati** lapokkal rendelkeznek, amelyek a használati és a nem használatos ajánlatok adatait jelenítik meg, míg a partner Centerben a **megrendelések** oldalon külön lap található az SaaS-ajánlatok számára. További információ: [hozzáférés analitikus jelentései a kereskedelmi piactéren a partner Centerben](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Létre kell hozni egy új fiókot az ajánlatom kezeléséhez a partner Centerben?

Nem, a fiókját megőrzi a rendszer. Ez azt jelenti, hogy ha Ön már meglévő partner, használhatja a meglévő Cloud Partner Portal fiókjának hitelesítő adatait a partner Centerbe való bejelentkezéshez. Ne hozzon létre új fiókot vagy az Cloud Partner Portal-ben létrehozott és a partner Centerbe áthelyezett ajánlatokat nem társítja.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>A partner Centerben milyen lapok tartoznak a Cloud Partner Portal használt lapokhoz?

Az alábbiakban a Cloud Partner Portal leggyakrabban használt lapokhoz tartozó fiókpartner-hivatkozások találhatók. Ha a Cloud Partner Portal hivatkozásokat könyvjelzőként mentette, érdemes frissítenie.

| Cloud Partner Portal lap <img src="" width=100px>| Cloud Partner Portal oldal hivatkozása | Partner Center-oldal hivatkozása |
| --- | --- | --- |
| Minden ajánlat oldal | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Minden közzétevő oldal | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Közzétevői profil | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Felhasználók oldal | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Előzmények lap | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Az előzmények funkció még nem támogatott a partner Centerben. |
| Az áttekintések irányítópultja | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Kifizetési jelentés | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>A kifizetési jelentések eltérései

Ezek a kivont Cloud Partner Portal és az aktuális partner központ közötti kifizetési jelentés különbségei:

| Felhőpartnerportál | Partnerközpont |
| --- | --- |
| **Hivatkozás**: https://cloudpartner.azure.com/ | **Hivatkozás**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory és https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigáció**: a bejelentések kifizetésében megadott kifizetési jelentések | **Navigáció**: a partner Centerben megadott kifizetési jelentés – kifizetés ikon |
| **Hatókör**:<ul><li>A tranzakció/sorban lévő elemek láthatók a folyamatban lévő, begyűjtött és fizetős gyűjteményekhez.</li><li>Jelentéskészítés – az összes sor elemet megjeleníti a beszerzési sorrend létrehozása után, beleértve a folyamatban lévő gyűjteményt és a számlázást, valamint a gyűjtemény állapotát és a sorokra vonatkozó olyan elemeket, amelyek még nem jogosultak a kifizetésre.</li></ul> | **Hatókör**:<ul><li>Megjeleníti a sorban lévő elemeket, miután a rendszer jogosult bevételnek tekinti őket.</li><li>Az ügyfelek először fizetnek a Microsoftnak, majd az ISV-ket láthatják a kifizetési jelentés megkezdéséről.</li><li>A kifizetési jelentés nem jeleníti meg a folyamatban lévő gyűjteményt, és a számlázás folyamatban van.</li></ul> |
| A **tranzakció nem áll készen a kifizetésre**: a számlázás folyamatban van | A **tranzakció nem áll készen a kifizetésre**: következő becsült fizetés: a kifizetés állapota a feldolgozatlan állapotban van. |
| **Kifizetés állapota**: n/a | **Kifizetés állapota**:<ul><li>Feldolgozatlan: a kereset fizetésre jogosult.</li><li>Közelgő: a befizetést a következő havi kifizetés keretében küldi el a rendszer a közzétevőnek.</li><li>Elküldve: a rendszer elküldje a fizetést a banknak.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Mi a helyzet a Cloud Partner Portal közzétett ajánlatokkal?

A rendszer áthelyezte az ajánlatokat a partneri központba, és elérhetővé válik a partner központba való bejelentkezés után, a Dynamics NAV által felügyelt szolgáltatás és a Cortana Intelligence ajánlatok kivételével. Ha az ajánlata a kereskedelmi piactéren volt elérhető, továbbra is élőben fog működni, és az ügyfelek továbbra is megvásárolhatják és üzembe helyezhetik azokat megszakítások nélkül. További részletekért tekintse meg a következő kérdést, **hogy milyen ajánlatokat helyeztek át a partner Centerbe?**.

## <a name="what-offers-were-moved-to-partner-center"></a>Milyen ajánlatokat helyeztek át a partner központba?

A Cloud Partner Portal által korábban támogatott összes típusú ajánlat támogatott a partner Centerben, a Dynamics NAV által felügyelt szolgáltatás és a Cortana Intelligence ajánlatok kivételével.

A partner Centerben támogatott ajánlati típusok esetében az összes ajánlat az állapotuktól függetlenül mozgott. a draft, de a List és a Preview-only ajánlat is át lett helyezve.

| Csomag típusa <img src="" width=150px>| Áthelyezte a partneri központba? <img src="" width=100px>| További lépések |
| --- | --- | --- |
| SaaS | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ: [SaaS-ajánlat tervezése a kereskedelmi piactéren](plan-saas-offer.md). |
| Virtuális gép | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ a [virtuális gépek ajánlatának megtervezése](marketplace-virtual-machines.md)című témakörben található. |
| Azure-alkalmazás | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információt az [Azure-alkalmazás ajánlatának létrehozása](create-new-azure-apps-offer.md)című témakörben olvashat. |
| Dynamics 365 Business Central | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ: [create a Dynamics 365 Business Central ajánlat](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 a Customer engagement & PowerApps | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ: [create a Dynamics 365 for Customer Engagement & PowerApps ajánlat](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információt a [Dynamics 365 for Operations ajánlat létrehozása](partner-center-portal/create-new-operations-offer.md)című témakörben olvashat. |
| Power BI alkalmazás | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ: [Power bi alkalmazás létrehozása a AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| IoT Edge modul | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ: [IoT Edge modul létrehozása, konfigurálása és közzététele az Azure Marketplace-](partner-center-portal/azure-iot-edge-module-creation.md)en. |
| Tároló | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ: [Azure Container-ajánlat létrehozása](./create-azure-container-offer.md). |
| Tanácsadói szolgáltatás | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információ: [create a Consulting Service ajánlat](./create-consulting-service-offer.md). |
| Felügyelt szolgáltatás | Igen | Jelentkezzen be a partner Centerbe, és hozzon létre új ajánlatokat, és kezelje a Cloud Partner Portal-ben létrehozott ajánlatokat. További információt a [felügyelt szolgáltatás ajánlatának létrehozása](./plan-managed-service-offer.md)című témakörben olvashat. |
| Dynamics NAV – felügyelt szolgáltatás | Nem | A Microsoft a Dynamics [365 Business Central](/dynamics365/business-central/)platformon fejlesztette ki a Dynamics NAV által felügyelt szolgáltatást, ezért a Dynamics NAV felügyelt szolgáltatásának élő ajánlatai a AppSource-től vannak felsorolva. Ezeket az ajánlatokat az ügyfelek már nem tudják feltárni, és nem helyezték át a partner központba. Ha elérhetővé kívánja tenni ajánlatait a AppSource-ben, a Dynamics 365 Business Central-ajánlatokhoz igazíthatja őket, és beküldheti őket a [partner Centerben](https://partner.microsoft.com/). További információ: [create a Dynamics 365 Business Central ajánlat](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | Nem | A Microsoft megalakította a Cortana Intelligence termékhez tartozó országúti térképet, ezért a AppSource-től származó, Cortana Intelligence élő ajánlatokat már nem tartalmazza. Ezeket az ajánlatokat az ügyfelek már nem tudják feltárni, és nem helyezték át a partner központba. Ha elérhetővé kívánja tenni ajánlatait a kereskedelmi piactéren, az ajánlatait szolgáltatásként (SaaS) biztosíthatja, és beküldheti őket a [partner Centerben](https://partner.microsoft.com/). További információ: [SaaS-ajánlat létrehozása ellenőrzőlista a partner Centerben](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Nem találom a Cloud Partner Portal ajánlatokat a partner Centerben

A partner Centerben látható funkciók a-ban regisztrált programoktól, a hozzá tartozó fiókokból, valamint a hozzárendelt felhasználói szerepköröktől és engedélyektől függenek. Számos partner Center-program érhető el, és előfordulhat, hogy több programban is regisztrálva van. Előfordulhat, hogy több fiókhoz is hozzáférhet ugyanazzal a felhasználói hitelesítő adatokkal.

A Cloud Partner Portalban létrehozott ajánlatok a **kereskedelmi piactér** program keretében, az ajánlatok létrehozásához használt fiók alatt érhetők el a partner Centerben. Győződjön meg arról, hogy a megfelelő programot és a megfelelő fiókot tekinti meg, kövesse az alábbi lépéseket. További hibaelhárítási tippekért lásd: [a partner Center-fiók kezelése](/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>A jobb program elérése a partner Centerben

1. A Cloud Partner Portalba való bejelentkezéshez használt hitelesítő adatokkal jelentkezzen be a [partner központjába](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) . A bal oldali navigációs ablaktábla megjeleníti azokat a beállításokat, amelyek az Ön által regisztrált programokhoz vannak társítva.

    Példa: tegyük fel, hogy van hozzáférése három programhoz: az MPN programhoz, az átirányítási programhoz és a kereskedelmi piactér programhoz. Amikor bejelentkezik a partneri központba, a navigációs ablaktáblán megjelenik a három program.

2. Az ajánlatok eléréséhez válassza a **kereskedelmi piactér**  >  **áttekintését** .

    Ha nem jelenik meg a kereskedelmi piactér program a bal oldali navigációs ablaktáblán, akkor lehet, hogy a fiók nem megfelelő. A megfelelő fiók eléréséhez kövesse a következő szakaszban ismertetett lépéseket.

    [![A partner Center áttekintő menüjét bemutató képernyőkép](media/cpp-pc-faq/overview-menu.png "A partneri központ áttekintő menüjének megjelenítése")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>A megfelelő fiók elérése a partner Centerben

Ha több fiók is része, a partner Centerben a bal oldali navigációs menüben két nyíllal jelölt fiók-választó gomb jelenik meg. A fiókhoz tartozó összes fiók listájának megtekintéséhez kattintson a Fiókbeállítások gombra. Válassza ki bármelyik fiókot a listán, és váltson rá, és tekintse meg az adott fiókra vonatkozó összes programot és információt. Ha a navigációs menüben nem jelenik meg a fiók kiválasztása gomb, akkor egyetlen fiók tagja.

[![Képernyőfelvétel: a partner Center Account picker gomb.](media/cpp-pc-faq/picker-button.png "A partner Center Account picker gomb megjelenítése")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Hogyan új ajánlatokat létrehozni?

Új ajánlatok létrehozásához nyissa meg a kereskedelmi piactér programját a [partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) . Az Áttekintés lapon válassza az **+ új ajánlat** lehetőséget.

[![A képernyőfelvételen a partneri központ áttekintése menü látható.](media/cpp-pc-faq/new-offer.png "A partneri központ áttekintő menüjének megjelenítése")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Nem tudok bejelentkezni, és meg kell nyitni egy támogatási jegyet

Ha nem tud bejelentkezni a fiókjába, itt is megnyithatja a [támogatási jegyet](https://partner.microsoft.com/support/v2/?stage=1) .

## <a name="where-are-instructions-for-using-partner-center"></a>Hol találhatók a partner Center használatára vonatkozó utasítások?

Nyissa meg a [kereskedelmi piactér dokumentációját](index.yml), majd bontsa ki a **kereskedelmi piactér portált a partner Centerben**. Az ajánlatok a partner Centerben való létrehozásával kapcsolatos súgótémakörök megtekintéséhez bontsa ki az **új ajánlat létrehozása** elemet.

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Mik a közzétételi és az ajánlat-kezelési különbségek?

Íme néhány különbség a Cloud Partner Portal és a partner központ között.

### <a name="modular-publishing-capabilities"></a>Moduláris közzétételi képességek

A partner Center egy moduláris közzétételi lehetőséget biztosít, amellyel kiválaszthatja a közzétenni kívánt módosításokat, ahelyett, hogy mindig az összes frissítést közzéteszi. Az alábbi képernyőn például látható, hogy az egyetlen közzétételre kijelölt módosítások a **Tulajdonságok** és az  **ajánlati lista** módosításai. Az előnézeti lapon végrehajtott módosítások nem lesznek közzétéve.

[![A képernyőfelvételen a partner központ áttekintése és a közzététel lap látható.](media/cpp-pc-faq/review-page.png "A partneri központ felülvizsgálatának és közzétételének oldalát jeleníti meg")](media/cpp-pc-faq/review-page.png#lightbox)

A nem közzéteendő frissítések piszkozatként lesznek mentve. Továbbra is használhatja az ajánlat előzetesét, hogy ellenőrizze az ajánlatát, mielőtt a nyilvánosságra tenné.

### <a name="enhanced-preview-options"></a>Bővített előnézet beállításai

A partner Center egy [összehasonlítási funkcióval](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) bővült, amely továbbfejlesztett szűrési lehetőségekkel rendelkezik. Ez lehetővé teszi, hogy összehasonlítsa az ajánlat előzetes és élő verzióit.

[![Képernyőfelvétel: a partner Center összehasonlítása funkció.](media/cpp-pc-faq/compare.png "A partner Center összehasonlítási funkciójának megjelenítése")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Védjegyezési és navigációs változások

Megfigyelheti néhány védjegyezési változást. Az *SKU* -ket például a partner Centerben lévő *csomagként* kell kiszolgálni:

[![Képernyőfelvétel: a partner Center-csomagok lapja.](media/cpp-pc-faq/plans.png "A partner Center-csomagok oldal megjelenítése")](media/cpp-pc-faq/plans.png#lightbox)

Továbbá a **piactéren** vagy a **kirakati részletekben**  (a tanácsadási szolgáltatás, Power bi alkalmazás) Cloud Partner Portal oldalain korábban megadott információkat a rendszer mostantól a partner Center **ajánlati lista** lapján gyűjti:

[! [Képernyőfelvétel: a partner Center ajánlati listáját tartalmazó oldal.] (Media/CPP-PC-GYIK/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Előfordulhat, hogy a Cloud Partner Portal egyetlen lapján a SKU-ban korábban megadott adatok gyűjtése a partner Center több oldalán történik:

- A telepítés megtervezése lap
- A lista megtervezése lap
- Csomag rendelkezésre állása lap
- Tervezze meg a technikai konfiguráció oldalát, ahogy az itt látható:

[![Megjeleníti a partner Center technikai konfigurációs lapját.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Az ajánlat azonosítója mostantól az ajánlat bal oldali navigációs sávján látható:

![A partneri központ ajánlatának AZONOSÍTÓjának helyét jeleníti meg](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Ajánlat értékesítésének leállítása

Kérheti, hogy közvetlenül a partner Center portálról [állítsa le az ajánlat értékesítését](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) a piactéren. A lehetőség az ajánlat **Áttekintés** lapján érhető el.

[![A képernyőfelvételen a partneri központ oldalára kattintva leállíthatja az ajánlatok értékesítését.](media/cpp-pc-faq/stop-sell.png "Megjeleníti a partneri központ oldalát az ajánlat értékesítésének leállításához")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>A Cloud Partner Portal REST API-k továbbra is támogatottak?

A Cloud Partner Portal API-k integrálva vannak a partner Centerben, és továbbra is működni fognak. A partneri központba való áttérés kis változásokat mutat be. Az alábbi táblázatban ellenőrizheti, hogy a kód továbbra is működik-e a partner Centerben.

| API <img src="" width=100px>| Módosítások ismertetése | Hatás |
| --- | --- | --- |
| Közzététel utáni, GoLive, Mégse | Az áttelepített ajánlatok esetében a válasz fejlécének formátuma eltérő lesz, de továbbra is ugyanúgy működik, mint a művelet állapotának lekéréséhez szükséges relatív elérési út. | Az ajánlathoz tartozó BEJEGYZÉSi kérelmek bármelyikének küldésekor a Location (hely) fejléc az ajánlat áttelepítési állapotának függvényében két formátum valamelyikét fogja tartalmazni: <ul><li>Nem áttelepített ajánlatok: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Áttelepített ajánlatok: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Művelet beolvasása | A válaszban korábban a "Notification-mail" mezőt támogató ajánlatok esetében ez a mező elavult, és az áttelepített ajánlatok esetében már nem lesz visszaküldve. | Az áttelepített ajánlatok esetében a továbbiakban nem küldünk értesítéseket a kérelmekben megadott e-mailek listájára. Ehelyett az API szolgáltatás egyezteti az e-mailek küldését a partner Centerben az értesítő e-mail folyamattal. A műveleti folyamatokról szóló értesítéseket a rendszer a fiókpartner fiók beállításai között, az eladó kapcsolattartási adatok szakaszában megadott e-mail-címre küldi el.<br><br>Győződjön meg arról, hogy az e-mail-cím meg van határozva az eladó kapcsolattartási adatai szakaszban a fiókpartner [fiók beállításai](https://partner.microsoft.com/dashboard/account/management) között. |
|||
---
title: Gyakori kérdések a microsoftos Cloud Partner Portal Partnerközpont – Microsoft kereskedelmi piactérről
description: Válaszok az ajánlatok váltása során gyakran feltett kérdésekre a Cloud Partner Portal-Partnerközpont.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0a45e7c0479cf490ad8688230897ae89ce4d1020
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819123"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Gyakori kérdések az új rendszerről a Cloud Partner Portal-Partnerközpont

A Cloud Partner Portal az új Partnerközpont. Partnerközpont egyszerű, integrált felhasználói élményt kínál az új [](https://appsource.microsoft.com/) ajánlatok közzétételéhez [](https://azuremarketplace.microsoft.com/) a Microsoft AppSource-ban vagy Azure Marketplace-ban, valamint a meglévő, a Cloud Partner Portal. A függvények minden Cloud Partner Portal elérhetők a Partnerközpont. Ez a cikk az ezzel kapcsolatos gyakori kérdéseket foglalkozik.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Mit jelent a Partnerközpont való átállás?

Az üzleti munkát a következő Partnerközpont:

| Terület | Módosítások |
| --- | --- |
| Fiók | Nincs szükség új Partnerközpont fiók létrehozására; Meglévő hitelesítő adataival Cloud Partner Portal bejelentkezhet egy olyan Partnerközpont, ahol most a fiókját, a felhasználókat, az engedélyeket és a számlázást fogja kezelni. A közzétételi szerződés és a vállalati profil adatai az új Partnerközpont-fiókba migrálva, a kifizetési profil adataival, a felhasználói fiókokkal és engedélyekkel, valamint az aktív ajánlatokkal együtt. További információ: A kereskedelmi [piactéri fiók kezelése a Partnerközpont.](manage-account.md) |
| Ajánlat közzététele és ajánlatkezelés | Áthelyezte az ajánlat adatait a Cloud Partner Portal Partnerközpont. Most már elérheti ajánlatát a Partnerközpont, amely továbbfejlesztett felhasználói élményt és intuitív felületet biztosít. Ismerje meg, hogyan [frissítheti meglévő ajánlatát a kereskedelmi piactéren.](partner-center-portal/update-existing-offer.md) |
| Az ajánlatok rendelkezésre állása a kereskedelmi piactéren | Nincs változás. Ha az ajánlata élő a kereskedelmi piactéren, az továbbra is élő lesz. |
| Új vásárlások és üzembe helyezések | Nincs változás. Az ügyfelek továbbra is megszakítás nélkül megvásárolhatja és üzembe helyezhetik ajánlatát. |
| Kifizetések | A vásárlások és az üzembe helyezések kifizetése továbbra is a megszokott módon folytatódik. További információ: [Fizetés a kereskedelmi piactéren.](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context) |
| API-integráció a meglévő [Cloud Partner Portal API-okkal](cloud-partner-portal-api-overview.md) | A Cloud Partner Portal API-k továbbra is támogatottak, és a meglévő integrációk továbbra is működnek. További információ: [Támogatja a Cloud Partner Portal REST API-kat?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Elemzés | A kereskedelmi piactéren továbbra is figyelheti az értékesítéseket, értékelheti a teljesítményt, és optimalizálhatja ajánlatát, ha megtekinti az elemzéseket a Partnerközpont. Különbségek vannak a CPP-ben és a CPP-ben az elemzési jelentések Partnerközpont. A CPP Értékesítői elemzések ben például van egy Orders & Usage (Megrendelések **&-használat)** lap, amely a használatalapú és a  nem használatalapú ajánlatok adatait jeleníti meg, míg a Partnerközpont Orders (Megrendelések) oldalon külön lap található az SaaS-ajánlatokhoz.  További információ: [Access anlytic reports for the commercial marketplace in Partnerközpont.](partner-center-portal/analytics.md) |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Létre kell hoznom egy új fiókot az ajánlatom kezeléséhez a Partnerközpont?

Nem, a rendszer megőrzi a fiókját. Ez azt jelenti, hogy ha Ön meglévő partner, a meglévő Cloud Partner Portal hitelesítő adataival bejelentkezhet a Partnerközpont. Ne hozzon létre új fiókot vagy a Cloud Partner Portal létrehozott és a Partnerközpont-fiókba áthelyezve ne legyen társítva.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>A Partnerközpont a lapokon használt oldalaknak Cloud Partner Portal?

Az alábbiakban Partnerközpont a dokumentumban gyakran használt oldalakra mutató Cloud Partner Portal. Ha a hivatkozásokat Cloud Partner Portal könyvjelzőként mentette, akkor frissítenie kell őket.

| Cloud Partner Portal lap | Cloud Partner Portal laphivatkozás | Partnerközpont laphivatkozás |
| --- | --- | --- |
| Minden ajánlat oldal | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Minden közzétevő oldal | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Közzétevői profil | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Felhasználók oldal | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Előzmények oldal | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Az Előzmények funkció még nem támogatott a Partnerközpont. |
| Insights-irányítópult | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Kifizetési jelentés | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Kifizetési jelentés eltérései

Ezek a kifizetési jelentésekben a visszavont fizetési Cloud Partner Portal és az aktuális Partnerközpont:

| Felhőpartnerportál | Partnerközpont |
| --- | --- |
| **Hivatkozás:**https://cloudpartner.azure.com/ | **Hivatkozás:** https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory és https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigáció:** Kifizetési jelentéskészítés az Insights-kifizetésekben | **Navigáció:** Kifizetési jelentéskészítés a Partnerközpont – Kifizetés ikon |
| **Hatókör:**<ul><li>A sortételenkénti tranzakciók láthatók a folyamatban lévő, összegyűjtött és fizetős gyűjtés számára.</li><li>Jelentéskészítés – a rendelés létrehozása után az összes sorelemet megjeleníti, beleértve a folyamatban lévő és a számlázásban lévő gyűjteményt, valamint a gyűjtemény állapotát és azokat a sorelemeket, amelyek még nem jogosultak a fizetésre.</li></ul> | **Hatókör:**<ul><li>Megjeleníti a sorelemeket, miután jogosult bevételnek minősülnek.</li><li>Az ügyfelek először a Microsoftnak fizetnek, majd a szoftveres szoftveres felhasználók láthatják a kifizetési jelentés kezdőpontját.</li><li>A kifizetési jelentés nem mutatja a folyamatban lévő gyűjteményt és a folyamatban lévő számlázást.</li></ul> |
| **A tranzakció nem áll készen a kifizetésre:** Számlázás folyamatban | **A tranzakció nem áll készen a kifizetésre:** Következő becsült kifizetés: A kifizetési állapot feldolgozatlan állapotban van. |
| **Kifizetés állapota:** n/a | **Kifizetés állapota:**<ul><li>Feldolgozatlan: A bevétel jogosult kifizetésre.</li><li>Közelgő: A bevétel a következő havi kifizetésben lesz elküldve a közzétevőnek.</li><li>Elküldött: A kifizetés el lett küldve a bankjának.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Mi a helyzet a következőben közzétett ajánlatokkal Cloud Partner Portal?

Az ajánlatok átkerültek a Partnerközpont szolgáltatásba, és az Partnerközpont-be való bejelentkezés után elérhetők lesznek a Dynamics Nav managed Service és Cortana Intelligence ajánlatok kivételével. Ha az ajánlata élő volt a kereskedelmi piactéren, az továbbra is élő lesz, és az ügyfelek továbbra is megszakítás nélkül vásárolhatják meg és helyezhetik üzembe. További részleteket a What offers were moved to Partnerközpont? , (Milyen ajánlatok helyezték át a **Partnerközpont?)** kérdést.

## <a name="what-offers-were-moved-to-partner-center"></a>Milyen ajánlatok helyezték át a Partnerközpont?

A dynamics nav. Cloud Partner Portal és Cortana Intelligence-ajánlatok kivételével az Partnerközpont támogatja az összes korábban támogatott ajánlattípust.

A szolgáltatásban támogatott ajánlattípusok Partnerközpont összes ajánlat átkerült az állapotuktól függetlenül; A draft (vázlat) és a de-listed (fel nem sorolt) és a csak előzetes verzióra vonatkozó ajánlatok is átkerültek.

| Csomag típusa | Áthelyezte a Partnerközpont? | Következő lépések |
| --- | --- | --- |
| SaaS | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be Cloud Partner Portal. További [információ: SaaS-ajánlat megterve a kereskedelmi piactérhez.](plan-saas-offer.md) |
| Virtuális gép | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be Cloud Partner Portal. További információ: [Plan a virtual machine offer (Virtuálisgép-ajánlat megtervezze a virtuális gépeket).](marketplace-virtual-machines.md) |
| Azure-alkalmazás | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be Cloud Partner Portal. További információ: [Azure-alkalmazásajánlat létrehozása.](create-new-azure-apps-offer.md) |
| Dynamics 365 Business Central | Igen | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Dynamics 365 Business Central-ajánlat létrehozása.](partner-center-portal/create-new-business-central-offer.md) |
| Dynamics 365 for Customer Engagement & PowerApps | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Dynamics 365 for Customer Engagement létrehozása & PowerApps-ajánlathoz.](dynamics-365-customer-engage-offer-setup.md) |
| Dynamics 365 for Operations | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Dynamics 365 for Operations-ajánlat létrehozása.](partner-center-portal/create-new-operations-offer.md) |
| Power BI alkalmazás | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Alkalmazásalkalmazás Power BI az AppSource-hoz.](partner-center-portal/create-power-bi-app-offer.md) |
| IoT Edge modul | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Create, configure,](partner-center-portal/azure-iot-edge-module-creation.md)and publish an IoT Edge module offer in Azure Marketplace. |
| Tároló | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Azure-tárolóajánlat létrehozása.](./create-azure-container-offer.md) |
| Tanácsadói szolgáltatás | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Tanácsadási szolgáltatásajánlat létrehozása.](./create-consulting-service-offer.md) |
| Felügyelt szolgáltatás | Yes | A Partnerközpont létrehozott új ajánlatok létrehozásához és kezeléséhez jelentkezzen be a Cloud Partner Portal. További információ: [Felügyelt szolgáltatási ajánlat létrehozása.](./plan-managed-service-offer.md) |
| Dynamics Nav – felügyelt szolgáltatás | No | A Microsoft a Dynamics NAV Managed Service-t a [Dynamics 365 Business Central](/dynamics365/business-central/)verzióra fejlődte, ezért a Dynamics NAV Managed Service élő ajánlatát az AppSource-ból soroljuk fel. Ezek az ajánlatok már nem deríthetők fel az ügyfelek számára, és nem helyezték át őket Partnerközpont. Ahhoz, hogy az ajánlata elérhető legyen az AppSource-ban, adaptálja őket a Dynamics 365 Business [Central-ajánlatokhoz,](https://partner.microsoft.com/)és küldje el őket a Partnerközpont. További [információ: Dynamics 365 Business Central-ajánlat létrehozása.](partner-center-portal/create-new-business-central-offer.md) |
| Cortana Intelligence | No | A Microsoft továbbfejlődött a Cortana Intelligence termék-térképén, ezért az AppSource-ból származó élő Cortana Intelligence-ajánlatokat soroljuk fel. Ezek az ajánlatok már nem deríthetők fel az ügyfelek számára, és nem helyezték át őket Partnerközpont. Annak érdekében, hogy ajánlatai elérhetők a kereskedelmi piactéren, az ajánlatokat a szolgáltatott szoftverre (SaaS) vonatkozó ajánlatokhoz igazjanak, és küldje el [Partnerközpont.](https://partner.microsoft.com/) További információ: [SaaS-ajánlat létrehozásának ellenőrzőlistája a Partnerközpont.](./plan-saas-offer.md) |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Nem találom a Cloud Partner Portal ajánlatomat a Partnerközpont

Az, hogy mit lát a Partnerközpont attól függ, hogy milyen programokban van regisztrálva, hogy ön milyen fiókokhoz tartozik, valamint hogy milyen felhasználói szerepkörök és engedélyek vannak hozzárendelve. Számos különböző Partnerközpont érhető el, és előfordulhat, hogy több programban is regisztrálva van. Több fiókhoz is hozzáférhet ugyanazokkal a felhasználói hitelesítő adatokkal.

A Cloud Partner Portal létrehozott ajánlatok a Partnerközpont **Marketplace** program keretében és az ajánlatok létrehozásához használt fiók alatt érhetők el. Annak érdekében, hogy biztosan a megfelelő programot és a megfelelő fiókot nézi meg, kövesse az alábbi lépéseket. További hibaelhárítási tippekért lásd: Manage your Partnerközpont account (Saját fiók [Partnerközpont kezelése).](/partner-center/partner-center-account-setup)

### <a name="access-the-right-program-in-partner-center"></a>A megfelelő program elérése a Partnerközpont

1. Jelentkezzen be [a Partnerközpont](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) ugyanazokkal a hitelesítő adatokkal, amelyek a bejelentkezéshez Cloud Partner Portal. A bal oldali navigációs panelen a regisztrált programokhoz társított beállítások megjelenik.

    Példa: tegyük fel, hogy három programhoz rendelkezik hozzáféréssel: az MPN programhoz, a Ajánlói programhoz és a Kereskedelmi piactér programhoz. Amikor bejelentkezik a Partnerközpont, ez a három program látható a navigációs panelen.

2. Válassza **a Kereskedelmi**  >  **piactér – Áttekintés lehetőséget** az ajánlatok eléréséhez.

    Ha nem látja a kereskedelmi piactér programot a bal oldali navigációs panelen, akkor lehet, hogy nem a megfelelő fiókot használja. A megfelelő fiók eléréséhez kövesse a következő szakaszban található lépéseket.

    [![Az Áttekintés Partnerközpont képernyőképe](media/cpp-pc-faq/overview-menu.png "Megjeleníti a Partnerközpont Áttekintés menüjét")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>A megfelelő fiók elérése a Partnerközpont

Ha több fiókhoz is tagja, a Partnerközpontban a bal oldali navigációs menüben megjelenik egy két nyíllal jelölt fiókválasztó gomb. Válassza a fiókválasztó gombot az összes olyan fiók listájának megtekintéséhez, amelyhez Ön tartozik. Válassza ki a listából bármelyik fiókot, hogy átváltson rá, és tekintse meg az adott fiókhoz tartozó összes programot és információt. Ha nem látja a fiókválasztó gombot a navigációs menüben, akkor ön egyetlen fiók tagja.

[![A fiókválasztó Partnerközpont képernyőképe.](media/cpp-pc-faq/picker-button.png "Megjeleníti Partnerközpont fiókválasztó gombot")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Hogyan új ajánlatokat hoz létre?

Új ajánlatok létrehozásához Partnerközpont [kereskedelmi](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) piactéri programját. Az Áttekintés lapon válassza az **+ Új ajánlat lehetőséget.**

[![Az Áttekintés Partnerközpont képernyőképe.](media/cpp-pc-faq/new-offer.png "Megjeleníti a Partnerközpont Áttekintés menüjét")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Nem tudok bejelentkezni, és támogatási jegyet kell nyitnom

Ha nem tud bejelentkezni a fiókjába, itt nyithat [meg egy támogatási jegyet.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="where-are-instructions-for-using-partner-center"></a>Hol találhatóak a Partnerközpont?

A kereskedelmi piactér [dokumentációjában bontsa](index.yml)ki a **Kereskedelmi piactér portálját a Partnerközpont.** Ha segítségre van szüksége az ajánlatok létrehozásához a Partnerközpont, bontsa ki **az Új ajánlat létrehozása gombra.**

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Mik a közzétételi és ajánlatkezelési különbségek?

Íme néhány különbség a két Cloud Partner Portal és Partnerközpont.

### <a name="modular-publishing-capabilities"></a>Moduláris közzétételi képességek

Partnerközpont moduláris közzétételi lehetőséget kínál, amellyel kiválaszthatja a közzétenni kívánt módosításokat ahelyett, hogy minden frissítést egyszerre tesz közzé. Az alábbi képernyőn például az látható, hogy csak a **Tulajdonságok** és az Ajánlatlista módosításai vannak közzétéve. Az Előzetes verzió lapon végrehajtott módosítások nem lesznek közzétéve.

[![Képernyőkép a Partnerközpont áttekintési és közzétételi lapról.](media/cpp-pc-faq/review-page.png "Megjeleníti Partnerközpont Áttekintés és közzététel oldalt")](media/cpp-pc-faq/review-page.png#lightbox)

A nem közzétett frissítések piszkozatként vannak mentve. Használja tovább az ajánlat előnézetét az ajánlat ellenőrzéséhez, mielőtt nyilvánosan elérhetővé teszi.

### <a name="enhanced-preview-options"></a>Bővített előzetes verziójú beállítások

Partnerközpont továbbfejlesztett [szűrési](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) lehetőségekkel rendelkezik egy összehasonlítási funkcióval. Így összehasonlíthatja az ajánlatot az előzetes verzióval és az élő verzióval.

[![Az összehasonlítási Partnerközpont képernyőképe.](media/cpp-pc-faq/compare.png "Megjeleníti az Partnerközpont összehasonlítása funkciót")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Védjegyezés és navigáció változásai

Figyelje meg a márkajelzések változásait. A *termékkódok például* Plans (Csomagok) termékekkel *oszlnak meg* a Partnerközpont:

[![Képernyőkép a Partnerközpont csomagok lapról.](media/cpp-pc-faq/plans.png "A Partnerközpont csomagok lapját jeleníti meg")](media/cpp-pc-faq/plans.png#lightbox)

Emellett az Cloud Partner Portal Marketplace vagy  a **Storefront Details** (Consulting Service, Power BI app) oldalain korábban megadott információk  a következő dokumentum Ajánlatlista oldalán Partnerközpont:

[! [Képernyőkép a Partnerközpont-termékoldalról.] (media/cpp-pc-faq/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

A lap egy oldalán korábban megadott SKUs Cloud Partner Portal a következő oldalakon gyűjthetőek össze a Partnerközpont:

- A csomagbeállítás oldala
- Csomaglista oldal
- A rendelkezésre állási terv oldala
- Tervezze meg a technikai konfigurációt az itt látható módon:

[![Megjeleníti a Partnerközpont konfigurációs lapját.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Az ajánlat azonosítója megjelenik az ajánlat bal oldali navigációs sávján:

![Megjeleníti az Partnerközpont-azonosító helyét](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Ajánlat értékesítésének leállítása

Kérheti, hogy [a](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) piactéren ne értékesítsen tovább egy ajánlatot közvetlenül a Partnerközpont portálon. Ez a lehetőség az ajánlat **ajánlatáttekintő** oldalán érhető el.

[![Képernyőkép a Partnerközpont oldalról, hogy ne értékesítsen tovább egy ajánlatot.](media/cpp-pc-faq/stop-sell.png "Megjeleníti az Partnerközpont, hogy ne értékesítsen tovább egy ajánlatot")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Az Cloud Partner Portal REST API-k továbbra is támogatottak?

A Cloud Partner Portal API-k integrálva vannak a Partnerközpont és továbbra is működni fognak. Az átállás a Partnerközpont kisebb változásokat vezet be. Tekintse át az alábbi táblázatot, és ellenőrizze, hogy a kód továbbra is Partnerközpont.

| API | Módosítások ismertetése | Hatás |
| --- | --- | --- |
| POST Publish, GoLive, Cancel | Migrált ajánlatok esetén a válaszfejléc formátuma eltérő lesz, de továbbra is ugyanúgy fog működni, a művelet állapotának lekéréséhez szükséges relatív elérési út jelölésére. | Amikor elküld egy ajánlathoz tartozó POST-kéréseket, a Hely fejléc két formátumot fog használni az ajánlat migrálási állapotától függően: <ul><li>Nem migrált ajánlatok: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrált ajánlatok: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET művelet | A válaszban korábban "notification-email" mezőt támogató ajánlatok esetén ez a mező elavult, és a továbbiakban nem lesz visszaadva a migrált ajánlatokhoz. | Migrált ajánlatok esetén a továbbiakban nem küldünk értesítéseket a kérelmekben megadott e-mailek listájára. Ehelyett az API szolgáltatás az e-mailek küldésére vonatkozó értesítési e-Partnerközpont fog igazodni. A műveleti folyamatról szóló értesítéseket a rendszer a fiókbeállítások Értékesítői kapcsolattartási adatok szakaszában megadott e-mail-címre küldi a Partnerközpont.<br><br>Győződjön meg arról, hogy a fiókbeállítások [](https://partner.microsoft.com/dashboard/account/management) Értékesítői kapcsolattartási adatok szakaszában megadott e-mail-cím Partnerközpont megfelelő az értesítések fogadására. |
|||
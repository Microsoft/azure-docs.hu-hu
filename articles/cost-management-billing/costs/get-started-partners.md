---
title: Ismerkedés a partnerek számára készült Azure Cost Management szolgáltatással
description: Ez a cikk bemutatja, hogyan használják a partnerek az Azure Cost Management funkcióit, és hogyan teszik lehetővé a Cost Managementhez való hozzáférést az ügyfeleik számára.
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 40c683d98a13bf4810ec4575af3fba5ae64961f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946989"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Ismerkedés a partnerek számára készült Azure Cost Management szolgáltatással

Az Azure Cost Management natív módon érhető el azon közvetlen partnerek számára, akik előkészítették ügyfeleiket egy Microsoft Ügyfélszerződésre, illetve [vásároltak egy Azure-csomagot](/partner-center/purchase-azure-plan). Ez a cikk ismerteti, hogyan használják a partnerek az [Azure Cost Management](../index.yml) funkcióit az Azure-csomagba tartozó előfizetések költségeinek megtekintéséhez. Azt is bemutatja, miként engedélyezik a partnerek a Cost Managementhez való, kiskereskedelmi árú hozzáférést az ügyfeleik számára.

Közvetlen partnerek és közvetett szolgáltatók esetén a globális rendszergazda és a rendszergazdai ügynökök hozzáférhetnek a Cost Managementhez a partnerbérlőben, és a számlázott áron kezelhetik a költségeket.

A viszonteladók és az ügyfelek hozzáférhetnek Cost Management az ügyfél bérlője számára, és megtekinthetik az előfizetések használati költségeit, ahol a költségek kiszámítása és a kiskereskedelmi díjszabás alapján történik. Az előfizetéshez azonban Azure RBAC-hozzáféréssel kell rendelkezni az ügyfélbérlőben a költségek megtekintéséhez. A szolgáltatónak engedélyeznie kell a költségek láthatóságára vonatkozó szabályzatot az ügyfélbérlő számára.

Az ügyfelek a CSP-partnerük által engedélyezett Cost Management-funkciókat használhatják.

A CSP-partnerek a következő célokra használják a Cost Managementet:

- A számlázott költségek értelmezése, valamint a költségek társítása az ügyfelekhez, az előfizetésekhez, az erőforráscsoportokhoz és a szolgáltatásokhoz.
- Az Azure-költségek intuitív megtekintése [a költségelemzésben](quick-acm-cost-analysis.md) olyan képességekkel, amelyek lehetővé teszik a költségek ügyfél, előfizetés, erőforráscsoport, erőforrás, fogyasztásmérő, szolgáltatás, és számos egyéb dimenzión alapján történő elemzését.
- Azon erőforrások költségeinek megtekintése, amelyekre partneri jóváírásokat (PEC) alkalmaztak a költségelemzésben.
- Értesítések és az automatizálás beállítása programozott [költségvetésekkel](tutorial-acm-create-budgets.md) és riasztásokkal arra az esetre, ha a költségek meghaladják a költségkeretet.
- Az Azure Resource Manager-szabályzat engedélyezése, amely lehetővé teszi az ügyfelek számára a Cost Management-adatokhoz való hozzáférését. Az ügyfelek ezt követően megtekinthetik a [használatalapú fizetéses](https://azure.microsoft.com/pricing/calculator/) előfizetéseik használati költségeire vonatkozó adatokat.
- Blobtárolóba exportálhatják költség- és használati adataikat egy használatalapú fizetéses előfizetéssel.

Itt látható egy példa, amely az összes ügyfélre vonatkozóan mutatja be a költségeket.

[![Az összes ügyfél költségeit bemutató példa](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

Itt látható egy példa, amely egyetlen ügyfélre vonatkozóan mutatja be a költségeket.

[![Egyetlen ügyfél költségeit bemutató példa](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Az Azure Cost Managementben elérhető összes funkció a REST API-k esetében is rendelkezésre áll. Az API-k alkalmazásával automatizálhatók a költségkezelési feladatok.

## <a name="prerequisites"></a>Előfeltételek

Partnerek számára az Azure Cost Management natív módon csak az Azure-csomaghoz tartozó előfizetések esetében érhető el.

Az Azure Cost Management az Azure Portalon való engedélyezéséhez a Microsoft Ügyfélszerződés ügyféloldali elfogadásának megerősítése szükséges (az ügyfél nevében), továbbá az ügyfélnek át kellett váltania az Azure-csomagra. Csak az Azure-csomagra átváltott előfizetések költségei állnak rendelkezésre az Azure Cost Managementben.

Az Azure Cost Management esetében olvasási hozzáférésre van szükség az Ön számlázási fiókjához vagy előfizetéséhez.

További információ az Azure Cost Managementhez való hozzáférés engedélyezéséről és hozzárendeléséről egy számlázási fiók esetében: [Felhasználói szerepkörök és engedélyek hozzárendelése](/partner-center/permissions-overview). A **globális rendszergazda**  és a **rendszergazdai ügynök** szerepkörök kezelhetik a számlázási fiók költségeit.

Az Azure Cost Managementhez az előfizetési hatókörben történő hozzáféréshez bármely, az előfizetéshez Azure RBAC-hozzáféréssel rendelkező felhasználó megtekintheti a költségeket kiskereskedelmi (használatalapú fizetéses) díjak mellett. Azonban engedélyezni kell a [költségek láthatóságára vonatkozó szabályzatot az ügyfélbérlő számára](#enable-the-policy-to-view-azure-usage-charges). A támogatott fióktípusok teljes listáját [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md) című részben tekintheti meg.

A meglévő számlázási szerződések új partnernek való átadásakor a Cost Management képességei csak a partnerrel fennálló számlázási kapcsolat esetén érhetők el. Az új partnernek való átvitel előtt a korábbi költségek nem kerülnek át az új számlázási fiókba. A korábbi díjak azonban megmaradnak az eredeti társított számlázási fiókkal.

## <a name="how-cost-management-uses-scopes"></a>Hogyan használja a Cost Management a hatóköröket?

A hatókörökben lehet kezelni a számlázási adatokat, rendelkezhet a kifizetésekre vonatkozó konkrét szerepkörökkel, megtekinthetők a számlák, és általános fiókkezelési feladatok végezhetők el. A számlázási és fiókszerepköröket az erőforrás-kezelésre használt hatóköröktől elkülönítve kezelik, amelyek az Azure RBAC-t használják. Annak érdekében, hogy egyértelműen meg lehessen különböztetni a különálló hatókörök mögötti szándékot, beleértve a hozzáférés-vezérlésre kiterjedő eltéréseket is, ezeket számlázási hatóköröknek, illetve Azure RBAC-hatóköröknek nevezzük.

A számlázási és Azure RBAC-hatókörök, valamint a költségkezelés és a hatókörök együttes működésének megértéséhez tekintse meg [A hatókörök ismertetése és használata](understand-work-scopes.md) című részt.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Költségek kezelése a partnerbérlő számlázási hatóköreivel

Miután előkészítette az ügyfeleit a Microsoft Ügyfélszerződésre, a következő _számlázási hatókörök_ válnak elérhetővé a bérlőjében. A hatókörök használatával kezelheti a költségek a Cost Managementben.

### <a name="billing-account-scope"></a>Számlázási fiók hatóköre

A számlázási fiók hatókörével megtekintheti az összes ügyfél és számlázási profil adók levonása előtti költségeit. A számlaköltségek csak az ügyfél használatalapú termékeire vonatkozóan jelennek meg a Microsoft Ügyfélszerződés esetében. A vásárlásalapú termékekre vonatkozó számlaköltségek azonban a Microsoft Ügyfélszerződés és a CSP-ajánlat esetében is megjelennek az ügyfelek számára. A költségek a hatókörön belüli megtekintésének alapértelmezett pénzneme jelenleg az amerikai dollár. A hatókörhöz beállított költségvetések szintén amerikai dollárban jelennek meg.

A különböző számlázott pénznemektől függetlenül a partnerek a Számlázási fiók hatókör használatakor amerikai dollárban adják meg a költségvetéseket, illetve kezelik a költségeket az ügyfelek, előfizetések, erőforrások és erőforráscsoportok tekintetében.

A partnerek emellett a költségelemzés nézetben egy adott számlázási pénznemben is szűrhetik a költségeket az ügyfeleikre vonatkozóan. Válassza ki a **Tényleges költségek** listáját a költségek támogatott számlázási pénznemekben való megtekintéséhez.

![A Tényleges költségekhez választható pénznemeket szemléltető példa](./media/get-started-partners/actual-cost-selector.png)

Az [amortizált költségek nézetének](quick-acm-cost-analysis.md#customize-cost-views) a számlázási hatókörökben történő használatával megtekinthetők a fenntartott példány amortizált költségei egy foglalási időszakra vonatkozóan.

### <a name="billing-profile-scope"></a>Számlázási profil hatóköre

A számlázási profil hatókörével megtekintheti az összes ügyfél számláin szereplő összes termékére és előfizetésére vonatkozó, adók levonása előtti költségeket a számlázási pénznemben. Egy adott számla számlázási profiljában lévő költségeket az **InvoiceID** szűrő használatával szűrheti. A szűrő egy adott számla felhasználási és termékvásárlási költségeit jeleníti meg. Emellett egy adott ügyfél számlán szereplő költségeit is szűrheti az adók levonása előtti költségek megtekintéséhez.

Miután előkészítette az ügyfeleket egy Microsoft Ügyfélszerződésre, egy olyan számlát kap, amely tartalmazza az ügyfelek a Microsoft Ügyfélszerződés szerinti összes termékének (felhasználás, vásárlások és jogosultságok) díját. Ha a számlázás ugyanabban a pénznemben történik, akkor ezek a számlák tartalmazzák a jogosultságok és a megvásárolt termékek, például az SaaS vagy az Azure Marketplace díjait, illetve az olyan ügyfelek foglalásait is, akik még mindig a klasszikus CSP-ajánlatot használják, nem pedig az Azure-csomagot.

Annak érdekében, hogy megkönnyítse a díjaknak az ügyfélszámlával való összevetését, a számlázási profil hatókör lehetővé teszi az ügyfelei számára kiszámlázott összes felhalmozott költség megtekintését. A számlához hasonlóan a hatókör megjeleníti az új Microsoft Ügyfélszerződéshez tartozó minden ügyfél költségeit. A hatókörben megtekinthető továbbá azon ügyféljogosultsági termékek minden díja, amelyek továbbra is az aktuális CSP-ajánlat részei.

A számlázási profil és a számlázási fiók hatóköre az egyetlen olyan alkalmazható hatókör, amely megjeleníti a jogosultsági és vásárlásalapú termékek, például az Azure Marketplace- és foglalásvásárlások díjait.

A számlázási profilok határozzák meg azokat az előfizetéseket, amelyek szerepelnek a számlán. A számlázási profilok funkcionálisan egyenértékűek a nagyvállalati szerződéses regisztrációval. A számlázási profil az a hatókör, ahol a számlák létrejönnek.

A számlázási pénznem jelenleg az alapértelmezett pénznem a költségeknek a számlázási profil hatókörében való megtekintésekor. A számlázási profil hatókörében beállított költségvetések a számlázási pénznemben vannak megadva.

A partnerek felhasználhatják a hatókört a számlák egyeztetéséhez. Továbbá a hatókör segítségével határozhatják meg a költségvetéseket a számlázási pénznemben a következőkhöz:

- Adott szűrt számla
- Ügyfél
- Előfizetés
- Erőforráscsoport
- Erőforrás
- Azure-szolgáltatás
- Mérő
- ResellerMPNID

### <a name="customer-scope"></a>Ügyfél hatókör

A partnerek a hatókört azon ügyfelekkel kapcsolatos költségek kezelésre használják, akiket előkészítettek a Microsoft Ügyfélszerződésre. A hatókör lehetővé teszi, hogy a partnerek megtekintsék egy adott ügyfél adók levonása előtti költségeit a számlázási pénznemben. Az adók levonása előtti költségek egy adott előfizetésre, erőforráscsoportra vagy erőforrásra vonatkozóan is szűrhetők.

Az ügyfél hatókör nem foglalja magában azokat az ügyfeleket, akik a jelenlegi CSP-ajánlatba tartoznak. A hatókör csak azokat az ügyfeleket tartalmazza, akik Microsoft Ügyfélszerződéssel rendelkeznek. Az aktuális CSP-ajánlat ügyfeleinek jogosultsági költségei (és nem az Azure-használat) a számlázási fiók és a számlázási profil hatókörben érhetők el az ügyfélszűrő alkalmazásakor. Az ezen hatókörben beállított költségvetések a számlázási pénznemben szerepelnek.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partneri hozzáférés a számlázási hatókörökhöz a Cost Managementben

Csak a **globális rendszergazda** és a **rendszergazdai ügynök** szerepkörrel rendelkező felhasználók kezelhetik és tekinthetik meg a számlázási fiókokhoz kapcsolódó költségeket, a számlázási profilokat és az ügyfeleket közvetlenül a partner Azure-bérlőjében. További információ a partnerközponti szerepkörökről: [Felhasználói szerepkörök és engedélyek hozzárendelése](/partner-center/permissions-overview).

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Költségkezelés engedélyezése az ügyfélbérlő előfizetéséhez

A partnerek engedélyezhetik a Cost Managementhez való hozzáférést, miután az ügyfeleket előkészítették a Microsoft Ügyfélszerződésre. A partnerek ezután engedélyezhetik azt a szabályzatot, amely lehetővé teszi, hogy az ügyfelek megtekintsék az Azure által használt szolgáltatások kiskereskedelmi, használatalapú díjszabás alapján felszámított költségeit. A költségek az ügyfél számlázási pénznemében jelennek meg az Azure RBAC-előfizetés és az erőforráscsoportok hatókörére vonatkozó felhasználásukra vonatkozóan.

Ha a partner engedélyezi a költségek láthatóságára vonatkozó szabályzatot, az előfizetéshez Azure Resource Manager-hozzáféréssel rendelkező minden felhasználó kezelheti és elemezheti a költségeket, használatalapú díjszabás mellett. Így azok a viszonteladók és ügyfelek, akik megfelelő Azure RBAC-hozzáféréssel rendelkeznek az Azure-előfizetésekhez, megtekinthetik a költségeket.

A szabályzattól függetlenül a globális rendszergazdák és a szolgáltató rendszergazdai ügynökei megtekinthetik az előfizetési költségeket, ha hozzáféréssel rendelkeznek az előfizetéshez és az erőforráscsoporthoz.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Engedélyezze a szabályzatot az Azure használati díjainak megtekintéséhez

A szabályzat megtekintéséhez és frissítéséhez a **rendszergazdai ügynök** csoport tagjának kell lennie. Az alábbi információk használatával engedélyezheti az Azure-használat költségeinek megtekintését lehetővé tévő szabályzatot az ügyfelek számára.

Jelentkezzen be a *partnerbérlőre* az Azure Portalon, és válassza a **Költségkezelés + számlázás** lehetőséget. Válassza ki a megfelelő számlázási hatókört a Számlázási hatókör területen, majd válassza az **Ügyfelek** lehetőséget. Az ügyfelek listája a számlázási fiókhoz van társítva. *Ha tévedésből az ügyfélbérlőre jelentkezik be, nem fogja látni az **ügyfelek** listáját.*

Az ügyfelek listájában válassza ki azt az ügyfelet, akinek engedélyezni szeretné a költségek megtekintését.

[![Ügyfelek kiválasztása a Cost Managementben](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

A **Beállítások** területen válassza a **Szabályzatok** elemet.

Megjelenik az aktuális költségláthatósági szabályzat a kiválasztott ügyfél előfizetéseihez társított **Azure-használati** díjakra vonatkozóan.
![Szabályzat, amely lehetővé teszi az ügyfelek számára a használatalapú fizetés díjainak megtekintését](./media/get-started-partners/cost-management-billing-policies.png)

Ha a szabályzat **Nem** értékre van állítva, az Azure Cost Management nem érhető el az előfizetés az ügyfélhez társított felhasználói számára. Hacsak egy partner nem engedélyezi, a költségek láthatóságára vonatkozó szabályzat alapértelmezés szerint le van tiltva az előfizetés összes felhasználója számára.

Ha a szabályzat **Igen** értékre van állítva, az előfizetés az ügyfélbérlőhöz társított felhasználói megtekinthetik a felhasználási díjakat, használatalapú díjszabás mellett.

Ha a költségek láthatóságára vonatkozó szabályzat engedélyezve van, az összes, előfizetés-felhasználással rendelkező szolgáltatás a használatalapú díjszabás szerint mutatja a költségeket. A foglalások felhasználása a tényleges és az amortizált költségekre vonatkozóan nulla költséggel jelenik meg. A vásárlások és jogosultságok nem konkrét előfizetésekhez vannak társítva. Így a vásárlások nem jelennek meg az előfizetés hatókörében. Egy közvetlen partner vagy közvetett szolgáltató globális rendszergazdája/rendszergazdai ügynöke az [Update Customer API](/rest/api/billing/2019-10-01-preview/policies/updatecustomer)-t is használhatja az ügyfelek költségei láthatóságára vonatkozó szabályzatának nagy léptékű beállításához.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Az előfizetési költségek megtekintése az ügyfélbérlőben

Az előfizetés költségeinek megtekintéséhez lépjen a **Költségkezelés + számlázás** területre az ügyfél Azure-bérlőjében. Válassza a **Költségelemzés** lehetőséget a megfelelő előfizetésnél a költségek áttekintésének elkezdéséhez. Az egyes előfizetések használati költségeit külön is megtekintheti az ügyfélbérlőben.

[![Költségelemzés megtekintése ügyfélként ](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

A költségelemzés, a költségvetések és a riasztások a használatalapú fizetés díjszabása szerinti költségek mellett érhetők el az előfizetés és az erőforráscsoport Azure RBAC-hatóköreihez.

A fenntartott példányok amortizált nézetei és tényleges költségei nulla díjat mutatnak az Azure RBAC-hatókörben. A jogosultságok, például a fenntartott példányok vásárlási költségei és a Marketplace díjai csak a partner bérlőjének azon számlázási hatóköreiben jelennek meg, ahol a vásárlások történtek.

A nézetben látható költségek kiszámításához használt kiskereskedelmi árak ugyanazok, amelyeket az Azure-díjkalkulátor minden ügyfélnek megjelenít. A megjelenő költségek nem tartalmazzák az olyan kedvezményeket vagy jóváírásokat, amelyekkel a partnerek rendelkezhetnek. Ezek lehetnek például a partneri jóváírások, a szintkedvezmények vagy a globális szolgáltatási kedvezmények.

 

## <a name="analyze-costs-in-cost-analysis"></a>Költségek elemzése a költségelemzésben

Azok a partnerek, akik hozzáférnek számlázási hatókörökhöz a partnerbérlőben, felderíthetik és elemezhetik a számlázott költségeket több ügyfél költségelemzésében egy adott ügyfélre vagy számlára vonatkozóan. A [költségelemzés](quick-acm-cost-analysis.md) nézetben lehetőség van továbbá [a nézetek mentésére](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) és az adatok [CSV- és PNG-fájlokba](quick-acm-cost-analysis.md#download-usage-data) való exportálására.

Az ügyfélbérlőben lévő előfizetéshez hozzáféréssel rendelkező Azure RBAC-felhasználók emellett elemezhetik a kiskereskedelmi költségeket az ügyfélbérlőben lévő előfizetésekre vonatkozóan, menthetik a nézeteket, valamint CSV- és PNG-fájlokba exportálhatják az adatokat.

A költségelemzés szűrő és csoportosítás funkcióival több mező szerint elemezheti a költségeket. A partnerspecifikus mezők a következő szakaszban láthatók.

## <a name="data-fields"></a>Adatmezők

A következő adatmezők találhatók a használatrészletező fájlokban és a Cost Management API-kban. Ahol elérhető, a Partnerközpontnak megfelelő információk jelennek meg. A következő félkövér mezőkben a partnerek a szűrő és csoportosítás funkcióival több mező szerint elemezhetik a költségeket. A félkövér mezők csak a partnerek által támogatott Microsoft Ügyfélszerződésekre vonatkoznak.

| **Mezőnév** | **Leírás** | **Partnerközpontbeli megfelelő** |
| --- | --- | --- |
| invoiceId | Az adott tranzakcióhoz tartozó számlán szereplő számlaazonosító. | A tranzakciót megjelenítő számla száma. |
| previousInvoiceID | Hivatkozás az eredeti számlára visszatérítés (negatív költség) esetén. Csak visszatérítés esetén van kitöltve. | N/A |
| billingAccountName | A partnert képviselő számlázási fiók neve. Minden olyan ügyfél költségeit felhalmozza, akiket a Microsoft Ügyfélszerződésre előkészítettek, valamint azon CSP-ügyfelek költségeit is, akik jogosultsági vásárlásokat hajtottak végre (például SaaS, Azure Marketplace vagy foglalások). | N/A |
| billingAccountID | A partnert képviselő számlázási fiók azonosítója. | Az MCAPI-partner kereskedelmi gyökérazonosítója. Kérelmekben használatos, de nem szerepel a válaszban.|
| billingProfileID | Azon számlázási profil azonosítója, amely egyetlen számlázási pénznemben csoportosítja a számlák költségeit azon ügyfelek tekintetében, akiket előkészítettek a Microsoft Ügyfélszerződésre, illetve azon CSP-ügyfelek esetében, akik jogosultsági vásárlásokat hajtottak végre (például SaaS, Azure Marketplace vagy foglalások). | MCAPI-partner számlázásicsoport-azonosítója. Kérelmekben használatos, de nem szerepel a válaszban. |
| billingProfileName | Azon számlázási profil neve, amely egyetlen számlázási pénznemben csoportosítja a számlák költségeit azon ügyfelek tekintetében, akiket előkészítettek a Microsoft Ügyfélszerződésre, illetve azon CSP-ügyfelek esetében, akik jogosultsági vásárlásokat hajtottak végre (például SaaS, Azure Marketplace vagy foglalások). | N/A |
| invoiceSectionName | A számlán felszámított projekt neve. Nem vonatkozik a partnerek által előkészített Microsoft Ügyfélszerződésekre. | N/A |
| invoiceSectionID | A számlán felszámított projekt azonosítója. Nem vonatkozik a partnerek által előkészített Microsoft Ügyfélszerződésekre. | N/A |
| **CustomerTenantID** | Az ügyfél előfizetéséhez tartozó Azure Active Directory-bérlő azonosítója. | Az ügyfél szervezeti azonosítója – az ügyfél Azure Active Directory-bérlőazonosítója (TenantID). |
| **CustomerName** | Az ügyfél előfizetéséhez tartozó Azure Active Directory-bérlő neve. | Az ügyfél szervezetének neve, a Partnerközpontban megjelenített módon. Fontos a számla és az Ön rendszer-információinak egyeztetése szempontjából. |
| **CustomerTenantDomainName** | Az ügyfél előfizetéséhez tartozó Azure Active Directory-bérlő tartományneve. | Az ügyfél Azure Active Directory-bérlőjének tartománya. |
| **PartnerTenantID** | A partner Azure Active Directory-bérlőjéhez tartozó azonosító. | A partner Azure Active Directory-bérlőazonosítója partnerazonosítóként feltüntetve, GUID formátumban. |
| **PartnerName** | A partner Azure Active Directory-bérlőjéhez tartozó név. | A partner neve. |
| **ResellerMPNID** | Az előfizetéshez társított viszonteladó MPNID-je. | Az előfizetéshez rögzített viszonteladó MPNID-je. Az aktuális tevékenységhez nem érhető el. |
| costCenter | Az előfizetéshez társított költséghely. | N/A |
| billingPeriodStartDate | A számlázási időszak kezdődátuma, a számlán látható módon. | N/A |
| billingPeriodEndDate | A számlázási időszak záródátuma, a számlán látható módon. | N/A |
| servicePeriodStartDate | A díjszabási időszak kezdődátuma, amikor a szolgáltatáshasználatra vonatkozó díjakat megállapították. Az Azure-szolgáltatások árait a díjszabási időszakra vonatkozóan határozzák meg. | ChargeStartDate (díjszabási időszak kezdődátuma) a Partnerközpontban  A számlázási ciklus kezdődátuma, kivéve egy megelőző számlázási ciklusban korábban nem felszámított látens használati adatokhoz tartozó dátumok megjelenítésekor. Az időpont mindig a nap kezdete, 0:00. |
| servicePeriodEndDate | Az időszak záródátuma, amikor a szolgáltatáshasználatra vonatkozó díjakat megállapították. Az Azure-szolgáltatások árait a díjszabási időszak alapján határozzák meg. | N/A |
| dátum | Az Azure felhasználási adatai esetében a használat dátumát mutatja a díjszabás szerint. Fenntartott példány esetében a vásárlás dátumát mutatja. Ismétlődő és egyszeri díjak, mint például a Marketplace és a támogatás díjai esetében a vásárlás dátumát mutatja. | N/A |
| productID | Annak a terméknek az azonosítója, amelyre vonatkozóan díjak merültek fel felhasználás vagy vásárlás révén. Ez a productID és a SkuID összefűzött kulcsa, ahogy a Partnerközpontban is megjelenik. | A termék azonosítója. |
| product | Annak a terméknek a neve, amelyre vonatkozóan díjak merültek fel fogyasztás vagy vásárlás révén, a számlán szereplő módon. | A katalógusban szereplő terméknév. |
| serviceFamily | Megjeleníti a megvásárolt vagy felszámított termékhez tartozó szolgáltatási családot. Például Storage vagy Compute. | N/A |
| productOrderID | Azon adategység vagy Azure-csomag neve, amelyhez az előfizetés tartozik. Például Azure-csomag. | CommerceSubscriptionID |
| productOrderName | Azon Azure-csomag neve, amelyhez az előfizetés tartozik. Például Azure-csomag. | N/A|
| consumedService | Felhasznált szolgáltatás (örökölt taxonómia) az örökölt EA-felhasználási adatokban alkalmazott módon. | A Partnerközpontban megjelenített szolgáltatás. Például, Microsoft.Storage, Microsoft.Compute és microsoft.operationalinsights. |
| meterID | Mérési azonosító a mért felhasználáshoz. | A használt fogyasztásmérő azonosítója. |
| meterName | Azonosítja a mért felhasználáshoz tartozó fogyasztásmérő nevét. | A felhasználáshoz tartozó mérő neve. |
| meterCategory | Megadja a legfelső szintű szolgáltatást a használat esetében. | A használathoz tartozó legfelső szintű szolgáltatás. |
| meterSubCategory | Megadja azon Azure-szolgáltatás típusát vagy alkategóriáját, amely befolyásolhatja a díjszabást. | Azon Azure-szolgáltatás típusa, amely befolyásolhatja a díjszabást.|
| meterRegion | Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ. | A szolgáltatásokhoz tartozó adatközpont regionális elhelyezkedése, ha alkalmazható és ki van töltve. |
| subscription ID | Az Azure-előfizetésre vonatkozó, Microsoft által létrehozott egyedi azonosító. | EntitlementID |
| subscriptionName | Az Azure-előfizetés neve. | N/A |
| Időszak | Megjeleníti az ajánlat érvényességi időszakát. A fenntartott példányok például az adott fenntartott példány éves időszakának 12 hónapját mutatják. Egyszeri vagy ismétlődő vásárlások esetén az időszak egy hónapot jelenít meg az SaaS, az Azure Marketplace és a támogatás esetében. Nem vonatkozik az Azure-felhasználásra. | N/A |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | A közzétevő típusa, amely a közzétevőt belső félként, külső viszonteladóként vagy külső ügynökségként azonosítja. | N/A |
| partNumber | A nem használt fenntartott példányra és Azure Marketplace-szolgáltatásokra vonatkozó cikkszám. | N/A |
| publisherName | A szolgáltatás közzétevőjének neve, beleértve a Microsoftot vagy a külső közzétevőket. | A termék közzétevőjének neve.|
| reservationId | A fenntartott példány vásárlási azonosítója. | N/A |
| reservationName | A fenntartott példány neve. | N/A |
| reservationOrderId | A fenntartott példány rendelési azonosítója (OrderID). | N/A |
| frequency | Fenntartott példányhoz tartozó fizetési gyakoriság. | N/A |
| resourceGroup | Az életciklussal kapcsolatos erőforrás-kezeléshez használt Azure-erőforráscsoport neve. | Az erőforráscsoport neve. |
| InstanceId (vagy) ResourceId | Az erőforráspéldány azonosítója. | ResourceURI formájában jelenik meg, amely tartalmazza az összes erőforrás-tulajdonságot. |
| resourceLocation | Az erőforrás helyének neve. | Az erőforrás helye. |
| Hely | Az erőforrás normalizált helye. | N/A |
| effectivePrice | A szolgáltatás (a díjszabási pénznemében számított) tényleges egységára. Minden termék, szolgáltatáscsalád, mérőszám és ajánlat esetén egyedi. A számlázási fiókhoz tartozó árlistán szereplő árképzéssel együtt használatos. Szintenként meghatározott díjak vagy szolgáltatási keret esetén a felhasználásra vonatkozó kevert árat mutatja. | A módosítás utáni egységár. |
| Mennyiség | Megvásárolt vagy felhasznált mért mennyiség. A fogyasztásmérő számlázási időszak során felhasznált mennyisége. | Egységek száma. Az egyeztetés során ellenőrizze, hogy megfelel-e a számlázási rendszerében található információknak. |
| unitOfMeasure | A szolgáltatás számlázási egységét jelöli. Például GB és óra. | A szolgáltatás számlázási egységét jelöli. Például GB, óra, és 10 000 s. |
| pricingCurrency | Az egységárat meghatározó pénznem. | Az árlistán szereplő pénznem.|
| billingCurrency | A számlázott költséget meghatározó pénznem. | A számlán számlázott pénznemként meghatározott pénznem. |
| chargeType | Meghatározza a díj típusát, amelyet a költség az Azure Cost Managementben képvisel (például vásárlás vagy visszatérítés). | A díj vagy módosítás típusa. Az aktuális tevékenységhez nem érhető el. |
| costinBillingCurrency | ExtendedCost vagy adók levonása előtti kevert költség a számlázott pénznemben. | N/A |
| costinPricingCurrency | ExtendedCost vagy adók levonása előtti kevert költség az árképzési pénznemben, az árakkal való egyeztetéshez. | N/A |
| **costinUSD** | Becsült ExtendedCost vagy adók levonása előtti kevert költség amerikai dollárban. | N/A |
| **paygCostInBillingCurrency** | Megjeleníti a költségeket, ha a díjszabás kiskereskedelmi áron történik. Megjeleníti a használatalapú fizetéshez tartozó árakat a számlázási pénznemben. Csak Azure RBAC-hatókörök esetében érhető el. | N/A |
| **paygCostInUSD** | Megjeleníti a költségeket, ha a díjszabás kiskereskedelmi áron történik. Megjeleníti a használatalapú fizetéshez tartozó árakat amerikai dollárban. Csak Azure RBAC-hatókörök esetében érhető el. | N/A |
| exchangeRate | Az árképzési pénznemről a számlázási pénznemre való átváltáshoz használt árfolyam. | A Partnerközpontban PCToBCExchangeRate néven jelenik meg. Az árképzési pénznemről a számlázási pénznemre való átváltási árfolyam.|
| exchangeRateDate | Az árképzési pénznemről a számlázási pénznemre való átváltáshoz használt árfolyamra vonatkozó dátum. | A Partnerközpontban PCToBCExchangeRateDat néven jelenik meg. Az árképzési pénznemről a számlázási pénznemre való átváltási árfolyam dátuma.|
| isAzureCreditEligible | Jelzi, hogy a költség jogosult-e Azure-kreditek általi fizetésre. | N/A |
| serviceInfo1 | Örökölt mező, amelyben a nem kötelező szolgáltatásspecifikus metaadatok rögzíthetők. | Belső Azure-szolgáltatás metaadatai. |
| serviceInfo2 | Örökölt mező, amelyben a nem kötelező szolgáltatásspecifikus metaadatok rögzíthetők. | Szolgáltatás adatai. Például egy virtuális gép rendszerképének típusa vagy az ExpressRoute-hoz tartozó ISP-név.|
| additionalInfo | Szolgáltatásspecifikus metaadatok. Például egy virtuális gép rendszerképének típusa. | Bármely további információ, amelyet a többi oszlop nem tartalmaz. A szolgáltatásspecifikus metaadatok. Például egy virtuális gép rendszerképének típusa.|
| tags | A fogyasztásmérőhöz hozzárendelt címke. Címkék használatával csoportosíthatja a számlázási rekordokat. A címkék használatával például a fogyasztásmérőt használó részleg szerint oszthatók fel a költségek. | Az ügyfél által hozzáadott címkék.|
| **partnerEarnedCreditRate** | Az alkalmazott kedvezmény mértéke, ha van partneri jóváírás (PEC) a partnerrendszergazdai hivatkozáshoz való hozzáférés alapján. | A partneri jóváírás (PEC) mértéke. Például 0% vagy 15%. |
| **partnerEarnedCreditApplied** | Jelzi, hogy alkalmazták-e a partneri jóváírást. | N/A |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>A partneri jóváírás (PEC) erőforrásköltségeinek megtekintése

Az Azure Cost Managementben a partnerek a költségelemzés segítségével tekinthetik meg azokat a költségeiket, amelyekre alkalmazták a PEC előnyeit.

Jelentkezzen be a partnerbérlőbe az Azure Portalon, és válassza a **Költségkezelés + számlázás** lehetőséget. A **Költségkezelés** területen válassza a **Költségelemzés** lehetőséget.

A Költségelemzés nézet mutatja a partnerhez tartozó számlázási fiók költségeit. A számlák egyeztetéséhez szükség szerint válassza ki a **Hatókör** lehetőséget a partnerre, konkrét ügyfélre vagy számlázási profilra vonatkozóan.

A fánkdiagramon válassza ki a legördülő listát, majd a **PartnerEarnedCreditApplied** elemet a PEC-költségek részleteinek megtekintéséhez.

![A partneri jóváírás megtekintési módjára vonatkozó példa](./media/get-started-partners/cost-analysis-pec1.png)

Ha a **PartnerEarnedCreditApplied** tulajdonság értéke _Igaz_, a kapcsolódó költségre alkalmazhatók a partneri rendszergazdai hozzáférés előnyei.

Ha a **PartnerEarnedCreditApplied** tulajdonság _Hamis_, a kapcsolódó költség nem felelt meg a jóváíráshoz szükséges feltételeknek. Az is előfordulhat, hogy a megvásárolt szolgáltatás nem jogosult a partneri jóváírásra.

A szolgáltatáshasználati adatok megjelenítése a Cost Managementben 8-24 órát vesz igénybe. További információ: [Költségek és használati adatok frissítése és megőrzése](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). A PEC-jóváírások az Azure Cost Managementhez való hozzáférés idejétől számított 48 órán belül jelennek meg.


A **PartnerEarnedCreditApplied** tulajdonság szerint is végezhet csoportosítást és szűrést a **Csoportosítás szempontja** beállítás alkalmazásával. A beállítások alkalmazásával megvizsgálhatja azokat a költségeket, amelyekre érvényes, illetve nem érvényes a partneri jóváírás.

![Csoportosítás vagy szűrés partneri jóváírás alapján](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Költségadatok exportálása az Azure Storage-ba

A partnerbérlőben számlázási hatókörökhöz való hozzáféréssel rendelkező partnerek exportálhatják költség- és használati adataikat egy Azure Storage-blobba. A blobnak a partnerbérlő olyan előfizetésében kell lennie, amely nem [megosztott szolgáltatás-előfizetés](/partner-center/shared-services) vagy egy ügyfél előfizetése. A költségadatok exportálásának engedélyezéséhez javasoljuk, hogy hozzon létre egy független, használatalapú fizetéses előfizetést a partnerbérlőben, hogy helyet biztosítson az exportált költségadatoknak. Az exportálási tárfiók a használatalapú fizetéses előfizetésben üzemeltetett Azure Storage-blobon jön létre. Azon hatókör alapján, amelyben a partner létrehozza az exportálást, a vonatkozó adatok automatikusan, rendszeres időközönként lesznek exportálva a tárfiókba.

Az előfizetéshez Azure RBAC-hozzáféréssel rendelkező felhasználók a költségadatokat az ügyfélbérlő bármelyik előfizetésében lévő Azure-blobba is exportálhatják.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Exportálás létrehozása egy partnerbérlőben vagy ügyfélbérlőben

Jelentkezzen be a partnerbérlőbe vagy ügyfélbérlőbe az Azure Portalon, és válassza a **Költségkezelés + számlázás** lehetőséget. Válasszon egy megfelelő hatókört, például egy Microsoft Partnerszerződés számlázási fiókját, majd válassza a **Költségelemzés** lehetőséget. Ha az oldal betöltött, válassza az **Exportálás** lehetőséget. Válassza ki az **Összes exportálás megtekintése** lehetőséget az Ütemezett exportálás területen.

![Válassza ki az Exportálás és az Összes exportálás megtekintése lehetőséget](./media/get-started-partners/export01.png)

Ezután válassza a **Hozzáadás** lehetőséget, írja be a nevet, és válasszon egy exportálási típust. Válassza ki a **Storage** lapot, majd írja be a szükséges adatokat.

![Új exportálás hozzáadása és a Storage lap kiválasztása](./media/get-started-partners/export02.png)

Amikor létrehoz egy exportálást a partnerbérlőben, válassza a használatalapú fizetéses előfizetést a partnerbérlőben. Hozzon létre egy Azure Storage-tárfiókot a szóban forgó előfizetés használatával.

Az ügyfélbérlőben lévő Azure RBAC-felhasználók esetében válasszon ki egy előfizetést az ügyfélbérlőben. Hozzon létre egy Azure Storage-tárfiókot az előfizetés használatával.

Tekintse át a tartalmat, majd válassza a **Létrehozás** lehetőséget az exportálás ütemezéséhez.

Az exportálási listában lévő adatok ellenőrzéséhez kattintson a tárfiók nevére. A tárfiók oldalán válassza a **Tárolók** elemet, majd válassza ki a tárolót. Navigáljon a megfelelő mappába, és válassza ki a CSV-fájlt. Válassza a **Letöltés** lehetőséget a CSV-fájl letöltéséhez, majd nyissa meg. Az exportált adatok az Azure Portalról származó használati adatokhoz hasonló költségadatokra emlékeztetnek.

![Példa az exportált adatokra](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST API-k

A partnerek és az ügyfelek gyakori feladatok elvégzésére használhatják a következő részekben leírt Cost Management API-kat.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management API-k – Közvetlen és közvetett szolgáltatók

A partnerbérlőben számlázási hatókörökhöz való hozzáféréssel rendelkező partnerek a következő API-kat használhatják a számlázott költségek megtekintéséhez.

Az adott partner az előfizetések hatókörében lévő API-kat a költségszabályzattól függetlenül meghívhatja, ha hozzáféréssel rendelkezik az előfizetéshez. Az előfizetéshez hozzáférő más felhasználók, például az ügyfelek vagy viszonteladók csak akkor hívhatják meg az API-kat, ha a partner engedélyezi az ügyfélbérlő számára a költségszabályzatot.


#### <a name="to-get-a-list-of-billing-accounts"></a>A számlázási fiókok listájának lekérése

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Az ügyfelek listájának lekérése

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Az előfizetések listájának lekérése

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Egy adott időszakra vonatkozó számlák listájának lekérése

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

Az API-hívás olyan, számlákat tartalmazó tömböt ad vissza, amelyek elemei hasonlítanak a következő JSON-kódra.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Használja az előző visszaküldött azonosítómező értékét, és cserélje le a következő példában a használati adatok lekérdezési hatókörére.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

A példa az adott számlához társított használati rekordokat adja vissza.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>A szabályzat lekérése az ügyfelek számára a költségek megtekintése céljából

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>A szabályzat beállítása az ügyfelek számára a költségek megtekintése céljából

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Egy adott számlázási fiókhoz tartozó Azure-szolgáltatás használati adatainak lekérése

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Egy Azure-szolgáltatás adott ügyfélre vonatkozó használati adatainak letöltése

A következő get hívás aszinkron művelet.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

A művelet állapotának ellenőrzéséhez hívja meg a válaszban visszaadott `Location` URI-t. Ha az állapot *Befejezve*, a `downloadUrl` tulajdonság tartalmaz egy hivatkozást a létrehozott jelentés letöltéséhez.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>A felhasznált Azure-szolgáltatásokra vonatkozó árlista lekérése vagy letöltése

Először használja a következő post műveletet.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Ezután hívja meg az aszinkron művelet tulajdonságának értékét. Például:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Az előző get hívás visszaadja az árlistát tartalmazó letöltési hivatkozást.


#### <a name="to-get-aggregated-costs"></a>Az összesített költségek lekérése

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Költségvetés létrehozása egy partner számára

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Költségvetést létrehozása egy ügyfél számára

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Költségvetés törlése

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>További lépések
- [Költségelemzés indítása](quick-acm-cost-analysis.md) a Cost Managementben
- [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md) a Cost Managementben

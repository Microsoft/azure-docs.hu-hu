---
title: Az Azure Cost Management hatóköreinek ismertetése és használata
description: Ez a cikk segít megérteni az Azure-ban elérhető számlázási és erőforrás-kezelési hatóköröket, valamint azt, hogyan kell használni a hatóköröket a Cost Managementben és az API-kban.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 729444b1d1ccf55f34e54a4b59508131458c472b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054804"
---
# <a name="understand-and-work-with-scopes"></a>A hatókörök ismertetése és használata

Ez a cikk segít megérteni az Azure-ban elérhető számlázási és erőforrás-kezelési hatóköröket, valamint azt, hogyan kell használni a hatóköröket a Cost Managementben és az API-kban.

## <a name="scopes"></a>Hatókörök

A _hatókör_ az Azure-erőforrás-hierarchia egyik csomópontja, amelynek keretében az Azure AD-felhasználók elérhetik és kezelhetik a szolgáltatásokat. A legtöbb Azure-erőforrás erőforráscsoportokban jön létre és lesz üzembe helyezve, amelyek az előfizetések részét képezik. A Microsoft két, az Azure-előfizetések feletti hierarchiát is kínál, amelyek speciális szerepkörökkel rendelkeznek a számlázási adatok kezelésére:
- Számlázási adatok, mint például kifizetések és számlák
- Felhőszolgáltatások, mint például költséggel és szabályzattal kapcsolatos irányítás

A hatókörökben lehet kezelni a számlázási adatokat, rendelkezhet a kifizetésekre vonatkozó konkrét szerepkörökkel, megtekinthetők a számlák, és általános fiókkezelési feladatok végezhetők el. A számlázási és fiókszerepköröket az erőforrás-kezelésre használt szerepköröktől elkülönítve kezelik, amelyek az [Azure RBAC-t](../../role-based-access-control/overview.md) használják. Annak érdekében, hogy egyértelműen meg lehessen különböztetni a különálló hatókörök mögötti szándékot, beleértve a hozzáférés-vezérlésre kiterjedő eltéréseket is, ezeket _számlázási hatóköröknek_, illetve _Azure RBAC-hatóköröknek_ nevezzük.

A hatókörökkel kapcsolatos további információkért tekintse meg [a hierarchiák Cost Managementben történő beállításával](https://www.youtube.com/watch?v=n3TLRaYJ1NY) kapcsolatos videót. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Hogyan használja a Cost Management a hatóköröket?

A Cost Management az erőforrások feletti összes hatókörben működik, hogy a szervezeteknek a hozzáférési szintjüknek megfelelő költségkezelést tegyen elérhetővé, függetlenül attól, hogy a teljes számlázási fiókról vagy egyetlen erőforráscsoportról van-e szó. Bár a számlázási hatókörök eltérnek az Ön Microsoft-szerződése (előfizetés típusa) alapján, az Azure RBAC-hatókörök nem.

## <a name="azure-rbac-scopes"></a>Azure RBAC-hatókörök

Az Azure három hatókört támogat az erőforrás-kezeléshez. Minden hatókör támogatja a hozzáférés és irányítás kezelését, nem kizárólagosan beleértve a költségkezelést is.

- [**Felügyeleti csoportok**](../../governance/management-groups/overview.md) – Hierarchikus tárolók, legfeljebb nyolc szinttel, az Azure-előfizetések rendszerezéséhez.

    Erőforrástípus: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Előfizetések** – Elsődleges tárolók az Azure-erőforrásokhoz.

    Erőforrástípus: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Erőforráscsoportok**](../../azure-resource-manager/management/overview.md#resource-groups) – Egy Azure-megoldáshoz kapcsolódó erőforrások logikai csoportosításai, amelyek ugyanazon az életcikluson osztoznak. Például lehetnek azok az erőforrások, amelyeket együtt helyeznek üzembe és törölnek.

    Erőforrástípus: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

A felügyeleti csoportok lehetővé teszik az előfizetések hierarchiába szervezését. Például létrehozhat egy logikai szervezeti hierarchiát felügyeleti csoportok alkalmazásával. Ezután adjon a csapatoknak előfizetéseket az éles és fejlesztési/tesztelési feladatokhoz. Majd hozzon létre erőforráscsoportokat az előfizetésekben minden egyes alrendszer vagy összetevő kezelésére.

A szervezeti hierarchia létrehozása lehetővé teszi a költségeknek és szabályzatoknak való megfelelés szervezeti szintű összesítését. Ezután minden vezető megtekintheti és elemezheti az aktuális költségeit. Majd létrehozhatnak költségvetéseket a helytelen költségminták megfékezéséhez és a költségek a legalacsonyabb szintű Advisor-javaslatokkal történő optimalizálásához.

A hozzáférés biztosítása a költségek megtekintéséhez és opcionálisan a költségkonfigurációhoz (például a költségvetéshez és az exportálásokhoz) az irányítási hatókörökön keresztül történik, az Azure RBAC használatával. Az Azure RBAC használatával hozzáférést biztosíthat az Azure AD-felhasználók és -csoportok számára előre meghatározott műveletek végrehajtásához, amelyek egy adott hatókörre és az alá tartozó hatókörökre vonatkozó szerepkörben vannak meghatározva. Például egy felügyeleti csoport hatóköréhez hozzárendelt szerepkör ugyanazokat az engedélyeket biztosítja a beágyazott előfizetéseknek és erőforráscsoportoknak.

A Cost Management az alábbi beépített szerepköröket támogatja a következő hatókörökhöz:

- [**Tulajdonos**](../../role-based-access-control/built-in-roles.md#owner) – Megtekintheti a költségeket, és mindent kezelhet, a költségkonfigurációt is beleértve.
- [**Közreműködő**](../../role-based-access-control/built-in-roles.md#contributor) – Megtekintheti a költségeket, és a hozzáférés-vezérlést leszámítva mindent kezelhet, a költségkonfigurációt is beleértve.
- [**Olvasó**](../../role-based-access-control/built-in-roles.md#reader) – Mindent megtekinthet, a költségadatokat és a -konfigurációt is beleértve, de nem végezhet módosításokat.
- [**Cost Management-közreműködő**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – Megtekintheti a költségeket és az ajánlásokat, és kezelheti a költségkonfigurációkat.
- [**Cost Management-olvasó**](../../role-based-access-control/built-in-roles.md#cost-management-reader) Megtekintheti a költségadatokat, a költségkonfigurációt és az ajánlásokat.

A Cost Management-közreműködő az ajánlott, minimális jogosultsághoz tartozó szerepkör. A költségek hatékonyabb monitorozása és jelentése érdekében ez a szerepkör lehetővé teszi a költségvetések és az exportálások létrehozását és kezelését. A Cost Management-közreműködőknek további szerepkörökre is szükségük lehet az összetett forgatókönyvek támogatásához. Vegyük példaként a következő forgatókönyveket:

- **Jelentéskészítés az erőforrás-használatról** – Az Azure Cost Management megjeleníti a költségeket az Azure Portalon. Ebbe a használati adatok is beletartoznak, mivel a költségek a teljes használati mintához kötődnek. A jelentés az API-k és a letöltés díjait is tartalmazhatja, azonban a részletesebb áttekintés érdekében érdemes lehet a részletes használati metrikákat is megvizsgálni az Azure Monitorban. Vegye fontolóra [Figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) szerepkör biztosítását minden olyan hatókörön, ahol a részletes használati metrikák jelentésére is szükség van.
- **Művelet a költségvetés túllépése esetén** – A Cost Management-közreműködőknek hozzáférésre van szükségük a műveletcsoportok létrehozásához és kezeléséhez is a túllépésre való automatikus reagálás érdekében. Vegye fontolóra a [Monitorozási közreműködő](../../role-based-access-control/built-in-roles.md#monitoring-contributor) megadását egy olyan erőforráscsoportnak, amely magában foglalja a költségvetési küszöbértékek túllépésekor alkalmazandó műveletcsoportot. A konkrét műveletek automatizálása további szerepköröket igényel a felhasznált konkrét szolgáltatásokhoz, például: Automation és Azure Functions.
- **Költségadatok exportálásának ütemezése** – A Cost Management-közreműködőknek hozzáférésre van szükségük a tárfiókok kezeléséhez is, hogy beütemezhessék az adatok tárfiókba másolását célzó exportálást. Vegye fontolóra a [Tárfiók-közreműködő](../../role-based-access-control/built-in-roles.md#storage-account-contributor) megadását egy olyan erőforráscsoportnak, amely magában foglalja azt a tárfiókot, ahová a költségadatokat exportálják.
- **A költségmegtakarítási javaslatok megtekintése** – Alapértelmezés szerint a Cost Management-olvasók és a Cost Management-közreműködők számára is elérhető a költségjavaslatok *megtekintése*. Azonban a költségjavaslatokkal kapcsolatos műveletekhez való hozzáféréshez az egyes erőforrásokhoz kell hozzáféréssel rendelkezni. Vegye fontolóra egy [szolgáltatásspecifikus szerepkör](../../role-based-access-control/built-in-roles.md#all) megadását, ha költségalapú javaslattal kapcsolatos műveletet szeretne elvégezni.

A felügyeleti csoportok csak akkor támogatottak, ha Nagyvállalati Szerződés (EA), használatalapú fizetéses (PAYG) vagy belső Microsoft-előfizetéseket tartalmaznak. Azok a felügyeleti csoportok, amelyek más típusú előfizetéssel rendelkeznek, mint a Microsoft Ügyfélszerződés vagy az Azure Active Directory-előfizetések, nem tekinthetik meg a költségeket. Ha több előfizetéssel rendelkezik, helyezze át a nem támogatott előfizetéseket a felügyeleti csoportok hierarchiájának egy külön ágába, hogy engedélyezze a Cost Managementet a támogatott előfizetésekhez. Például hozzon létre két felügyeleti csoportot a gyökérszintű felügyeleti csoport alatt: **Azure AD** és **Saját szervezet**. Helyezze át Azure AD-előfizetését az **Azure AD** felügyeleti csoportba, így a költségeket a **Saját szervezet** felügyeleti csoporttal tekintheti meg és kezelheti.

## <a name="enterprise-agreement-scopes"></a>A Nagyvállalati Szerződés hatókörei

A Nagyvállalati Szerződés (EA) számlázási fiókjai (más néven a regisztrációk) a következő hatókörökkel rendelkeznek:

- [**Számlázási fiók**](../manage/view-all-accounts.md) – Egy EA-regisztrációt jelöl. Ebben a hatókörben történik a számlakiállítás. A nem használatalapú beszerzések – mint például a Marketplace és a foglalások – csak ebben a hatókörben érhetők el. Nem szerepelnek a részlegekben vagy a regisztrációs fiókokban. A foglaláshasználat és az összes többi használat az egyedi erőforrásokra vonatkozik. A használat összesítve jelenik meg a számlázási fiókban lévő előfizetések esetében. A foglalási költségek erőforrások szerinti lebontásban történő megtekintéséhez váltson az **Amortizált költség** nézetre a költségelemzésben.

    Erőforrástípus: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Részleg** – A regisztrációs fiókok opcionális csoportja.

    Erőforrástípus: `Billing/billingAccounts/departments`

- **Regisztrált fiók** – Egyetlen fióktulajdonost jelöl. Nem támogatja a hozzáférés megadását több embernek.

    Erőforrástípus: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Bár az irányítási hatókörök egyetlen címtárhoz vannak kötve, az EA számlázási hatókörei nem. Az EA számlázási fiók előfizetésekkel rendelkezhet bármilyen számú Azure AD-címtárra vonatkozóan.

Az EA számlázási hatókörei a következő szerepköröket támogatják:

- **Vállalati rendszergazda** – Kezelheti a számlázási fiókok beállításait és a hozzáférést, megtekintheti az összes költséget és kezelheti a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Gyakorlatilag az EA számlázási hatóköre ugyanaz, mint a [Cost Management-közreműködő Azure-szerepkör](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Vállalati írásvédett felhasználó** – Megtekintheti a számlázási fiókok beállításait, a költségadatokat és a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Gyakorlatilag az EA számlázási hatóköre ugyanaz, mint a [Cost Management-olvasó Azure-szerepkör](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Részlegszintű rendszergazda** – Kezelheti a részlegbeállításokat, például a költséghelyet, valamint elérheti és megtekintheti az összes költséget, és kezelheti a költségkonfigurációt. Például a költségvetéseket és az exportálásokat.  A **Díjtételek megtekintése a részlegszintű rendszergazda számára** számlázási fiókbeállítást engedélyezni kell a részlegszintű rendszergazdák és a csak olvasási jogosultsággal rendelkező felhasználók számára a költségek megtekintéséhez. Ha a **Díjtételek megtekintése a részlegszintű rendszergazda számára** lehetőség le van tiltva, a részleg felhasználói semmilyen szinten nem láthatják a költségeket, még akkor sem, ha egy fiók vagy előfizetés tulajdonosai.
- **Részlegszintű írásvédett felhasználó** – Megtekintheti a részlegszintű beállításokat, a költségadatokat és a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Ha a **Díjtételek megtekintése a részlegszintű rendszergazda számára** lehetőség le van tiltva, a részleg felhasználói semmilyen szinten nem láthatják a költségeket, még akkor sem, ha egy fiók vagy előfizetés tulajdonosai.
- **Fiók tulajdonosa** – Kezelheti a regisztrációs fiók beállításait (mint például a költséghelyeket), megtekintheti az összes költséget, és kezelheti a regisztrációs fiókhoz tartozó költségkonfigurációt (például a költségvetést és az exportálásokat). A **Díjtételek megtekintése a fióktulajdonos számára** számlázási fiókbeállítást engedélyezni kell a fióktulajdonosok és az Azure RBAC-felhasználók számára a költségek megtekintéséhez.

Az EA számlázási fiókfelhasználói nem rendelkeznek közvetlen hozzáféréssel a számlákhoz. A számlák a külső mennyiségi licencelési rendszerből érhetők el.

Az Azure-előfizetések a regisztrációs fiókok alá vannak beágyazva. A számlázási felhasználók hozzáféréssel rendelkeznek a költségadatokhoz azon előfizetések és erőforráscsoportok esetén, amelyek a megfelelő hatókörök alatt helyezkednek el. De nem rendelkeznek hozzáféréssel az erőforrások megtekintéséhez vagy kezeléséhez az Azure Portalon. A felhasználók megtekinthetik a költségeket az Azure Portal szolgáltatáslistájának **Költségkezelés + számlázás** elemére navigálva. Ezután rászűrhetnek azon előfizetések és erőforráscsoportok költségeire, amelyekről jelentést kell készíteniük.

A számlázási felhasználók nem férhetnek hozzá a felügyeleti csoportokhoz, mert nem tartoznak kifejezetten egy adott számlázási fiókhoz. Kifejezett hozzáférést kell biztosítani a felügyeleti csoportoknak. A felügyeleti csoportok összesítik a költségeket az összes beágyazott előfizetésből. Azonban csak a használaton alapuló beszerzéseket tartalmazzák. Nem tartoznak ide az olyan beszerzések, mint a foglalások és a harmadik féltől származó Marketplace-ajánlatok. E költségek megtekintéséhez az EA számlázási fiókját használhatja.

## <a name="individual-agreement-scopes"></a>Az egyes szerződések hatókörei

Az egyéni ajánlatokból, mint például a használatalapú fizetésből és a hozzá kapcsolódó típusokból, például az ingyenes próbaidőszakból és a fejlesztési/tesztelési ajánlatokból létrehozott Azure-előfizetések nem rendelkeznek kifejezett hatókörrel a számlázási fiókra vonatkozóan. Ehelyett minden előfizetéshez tartozik egy fióktulajdonos vagy fiókrendszergazda, például az EA-fiók tulajdonosa.

- [**Számlázási fiók**](../manage/view-all-accounts.md) – Egyetlen fióktulajdonost jelöl egy vagy több Azure-előfizetésre vonatkozóan. Jelenleg nem támogatja több személy hozzáférését, vagy az összesített költségnézetekhez való hozzáférést.

    Erőforrástípus: Nem alkalmazható

Az egyes Azure-előfizetési fiókok rendszergazdái megtekinthetik és kezelhetik a számlázási adatait, például a számlákat és a kifizetéseket a [Azure Portal](https://portal.azure.com)-  >  **előfizetésből** > kiválaszthatják az

Az EA-tól eltérően az Azure előfizetési fiókjainak rendszergazdái megtekinthetik a számláikat az Azure Portalon. Ne feledje, hogy a Cost Management-olvasó és a Cost Management-közreműködő szerepkör nem biztosít hozzáférést a számlákhoz. A további tudnivalókért lásd [a számlákhoz való hozzáférés biztosítását](../manage/manage-billing-access.md#give-read-only-access-to-billing) ismertető szakaszt.

## <a name="microsoft-customer-agreement-scopes"></a>A Microsoft-ügyfélszerződés hatókörei

A Microsoft-ügyfélszerződés számlázási fiókjai a következő hatókörökkel rendelkeznek:

- **Számlázási fiók** – Egy több Microsoft-terméket és -szolgáltatást magában foglaló ügyfélszerződést jelöl. Az ügyfélszerződések számlázási fiókjai működés szempontjából nem azonosak az EA-regisztrációkkal. Az EA-regisztrációk szorosabban illeszkednek a számlázási profilokhoz.

    Erőforrástípus: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Számlázási profil** – Meghatározza a számlán szereplő előfizetéseket. A számlázási profilok az EA-regisztráció gyakorlati megfelelői, mivel ez az a hatókör, amelyen a számlákat létrehozzák. Hasonlóképpen, a nem használatalapú beszerzések – például a Marketplace vagy a foglalások – csak ebben a hatókörben érhetők el. Nem szerepelnek a számlaszakaszokban.

    Erőforrástípus: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Számlaszakasz** – Előfizetések egy csoportját jelzi egy számlán vagy számlázási profilon. A számlázási szakaszok olyanok, mint a részlegek – több ember férhet hozzá egy számlaszakaszhoz.

    Erőforrástípus: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Ügyfél** – Olyan előfizetések csoportját jelöli, amelyek egy olyan adott ügyfélhez vannak társítva, amely a partner által egy Microsoft-ügyfélszerződés keretében lett előkészítve. Ez a hatókör kifejezetten a felhőszolgáltatókra (CSP) vonatkozik.

Az EA számlázási hatóköreitől eltérően az ügyfélszerződések számlázási fiókjai _egyetlen_ könyvtárhoz kötődnek, és nem rendelkezhetnek több Azure AD-címtár előfizetéseivel.

Az ügyfélszerződés számlázási hatókörei nem vonatkoznak a partnerekre. A partneri szerepkörök és engedélyek dokumentációja a [felhasználói szerepkörök és engedélyek hozzárendelését](/partner-center/permissions-overview) ismertető szakaszban található.

Az ügyfélszerződés számlázási hatókörei a következő szerepköröket támogatják:

- **Tulajdonos** – Kezelheti a számlázási beállításokat és a hozzáférést, megtekintheti az összes költséget és kezelheti a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Gyakorlatilag ez az ügyfélszerződéses számlázási hatókör ugyanaz, mint a [Cost Management-közreműködő Azure-szerepkör](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Közreműködő** – Kezelheti a számlázási beállításokat a hozzáférés kivételével, megtekintheti az összes költséget és kezelheti a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Gyakorlatilag ez az ügyfélszerződéses számlázási hatókör ugyanaz, mint a [Cost Management-közreműködő Azure-szerepkör](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Olvasó** – Megtekintheti a számlázási beállításokat, a költségadatokat és a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Gyakorlatilag ez az ügyfélszerződéses számlázási hatókör ugyanaz, mint a [Cost Management-olvasó Azure szerepkör](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Számlakezelő** – Megtekintheti és kifizetheti a számlákat, valamint megtekintheti a költségadatokat és a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Gyakorlatilag ez az ügyfélszerződéses számlázási hatókör ugyanaz, mint a [Cost Management-olvasó Azure szerepkör](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-előfizetés létrehozója** – Létrehozhat Azure-előfizetéseket, megtekintheti a költségeket és kezelheti a költségkonfigurációt. Például a költségvetéseket és az exportálásokat. Gyakorlatilag ez az ügyfélszerződéses számlázási hatókör ugyanaz, mint az EA-regisztrációs fiók tulajdonosi szerepköre.

Az Azure-előfizetések a számlaszakaszokba vannak beágyazva, az EA-regisztrációs fiókok alatti elhelyezkedésüknek megfelelően. A számlázási felhasználók hozzáféréssel rendelkeznek a költségadatokhoz azon előfizetések és erőforráscsoportok esetén, amelyek a megfelelő hatókörök alatt helyezkednek el. De nem rendelkeznek hozzáféréssel az erőforrások megtekintéséhez vagy kezeléséhez az Azure Portalon. A számlázási felhasználók megtekinthetik a költségeket az Azure Portal szolgáltatáslistájának **Költségkezelés + számlázás** elemére navigálva. Ezután rászűrhetnek azon előfizetések és erőforráscsoportok költségeire, amelyekről jelentést kell készíteniük.

A számlázási felhasználók nem férhetnek hozzá a felügyeleti csoportokhoz, mert nem tartoznak kifejezetten a számlázási fiókhoz. Ha azonban a felügyeleti csoportok engedélyezve vannak a szervezetnél, az összes előfizetés költsége összegezve van a számlázási fiókra és a gyökérszintű felügyeleti csoportra vonatkozóan, mivel mindkettő egyetlen címtárra korlátozódik. A felügyeleti csoportok csak a felhasználáson alapuló beszerzéseket tartalmazzák. Az olyan beszerzések, mint például a foglalások és a harmadik féltől származó Marketplace-ajánlatok nem szerepelnek a felügyeleti csoportokban. Így a számlázási fiók és a gyökérszintű felügyeleti csoport különböző összegeket jeleníthet meg. E költségek megtekintéséhez a számlázási fiókot vagy a megfelelő számlázási profilt használhatja.

## <a name="aws-scopes"></a>AWS-hatókörök

Az AWS-integráció befejezését követően lásd az [AWS-integráció beállítását és konfigurálását](aws-integration-set-up-configure.md) ismertető részt. Az alábbi hatókörök állnak rendelkezésre:

- **Külső számlázási fiók** – Egy külső beszállítóval létrejött ügyfélszerződést jelöl. Hasonlít az EA számlázási fiókjához.

    Erőforrástípus: `Microsoft.CostManagement/externalBillingAccounts`

- **Külső előfizetés** – Egy külső beszállítóval létrejött operatív ügyfélfiókot jelöl. Hasonlít egy Azure-előfizetéshez.

    Erőforrástípus: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Felhőszolgáltató (CSP) hatókörei

A Microsoft-ügyfélszerződést használó ügyfelek számára a következő hatókörök támogatottak CSP-k esetén:

- **Számlázási fiók** – Egy több Microsoft-terméket és -szolgáltatást magában foglaló ügyfélszerződést jelöl. Az ügyfélszerződések számlázási fiókjai működés szempontjából nem azonosak az EA-regisztrációkkal. Az EA-regisztrációk szorosabban illeszkednek a számlázási profilokhoz.

    Erőforrástípus: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Számlázási profil** – Meghatározza a számlán szereplő előfizetéseket. A számlázási profilok az EA-regisztráció gyakorlati megfelelői, mivel ez az a hatókör, amelyen a számlákat létrehozzák. Hasonlóképpen, a nem használatalapú beszerzések – például a Marketplace vagy a foglalások – csak ebben a hatókörben érhetők el.

    Erőforrástípus: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Ügyfél** – Olyan előfizetések csoportját jelöli, amelyek egy olyan adott ügyfélhez vannak társítva, amely a partner által egy Microsoft-ügyfélszerződés keretében lett előkészítve.

Csak a *globális rendszergazda* és a *rendszergazdai ügynök* szerepkörrel rendelkező felhasználók kezelhetik és tekinthetik meg a számlázási fiókokhoz kapcsolódó költségeket, a számlázási profilokat és az ügyfeleket közvetlenül a partner Azure-bérlőjében. További információ a partnerközponti szerepkörökről: [Felhasználói szerepkörök és engedélyek hozzárendelése](/partner-center/permissions-overview).

Az Azure Cost Management csak akkor támogatja a CSP-partnerügyfeleket, ha Microsoft-ügyfélszerződéssel rendelkeznek. A Microsoft-ügyfélszerződéssel még nem rendelkező támogatott CSP-ügyfelek esetén lásd a [Partnerközpontot](/azure/cloud-solution-provider/overview/partner-center-overview).

A Cost Management nem támogatja a CSP-hatókörökben lévő felügyeleti csoportokat. Ha van CSP-előfizetése, és a költségelemzésben a hatókört egy felügyeleti csoportra állítja be, a következőhöz hasonló hibaüzenet jelenik meg:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Váltás a hatókörök között a Cost Managementben

Az Azure Portal összes Cost Management-nézete magában foglalja a **Hatókör** kiválasztó elemet a nézet bal felső részén. A hatókör gyors megváltoztatásához használható. Válassza a **Hatókör** elemet a hatókörválasztó megnyitásához. Megjeleníti a számlázási fiókokat, a gyökérszintű felügyeleti csoportot, valamint azokat az előfizetéseket, amelyek nincsenek beágyazva a gyökérszintű felügyeleti csoportba. Egy adott hatókör kiválasztásához válassza ki a hátteret a kiemeléséhez, majd válassza alul a **Kiválasztás** lehetőséget. A beágyazott hatókörök, például az előfizetéshez tartozó erőforráscsoportok részletezéséhez válassza ki a hatókör nevét tartalmazó hivatkozást. Bármelyik beágyazott szinten a fölérendelt hatókör kiválasztásához válassza ki a **Válassza ki ezt a &lt;hatókört&gt;** elemet a hatókörválasztó felső részén.

## <a name="identify-the-resource-id-for-a-scope"></a>Egy hatókör erőforrás-azonosítójának beazonosítása

A Cost Management API-kkal végzett munka esetén a hatókör ismerete kritikus fontosságú. A következő információ felhasználásával hozhatja létre a megfelelő hatókör-URI-t a Cost Management API-khoz.

### <a name="billing-accounts"></a>Számlázási fiókok

1. Nyissa meg az Azure Portalt, és navigáljon a **Költségkezelés + számlázás** elemre a szolgáltatások listájában.
2. Válassza ki a **Tulajdonságok** elemet a számlázási fiók menüben.
3. Másolja át a számlázási fiók azonosítóját.
4. Az Ön hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Számlázási profilok

1. Nyissa meg az Azure Portalt, és navigáljon a **Költségkezelés + számlázás** elemre a szolgáltatások listájában.
2. Válassza ki a **Számlázási profilok** elemet a számlázási fiók menüben.
3. Válassza ki a számlázási profil nevét.
4. Válassza ki a **Tulajdonságok** elemet a számlázási profil menüben.
5. Másolja át a számlázási fiók és a számlázási profil azonosítóját.
6. Az Ön hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Számlaszakaszok

1. Nyissa meg az Azure Portalt, és navigáljon a **Költségkezelés + számlázás** elemre a szolgáltatások listájában.
2. Válassza ki a **Számlaszakaszok** elemet a számlázási fiók menüben.
3. Válassza ki a számlaszakasz nevét.
4. Válassza ki a **Tulajdonságok** elemet a számlaszakasz menüben.
5. Másolja át a számlázási fiók és a számlaszakasz azonosítóját.
6. Az Ön hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-részlegek

1. Nyissa meg az Azure Portalt, és navigáljon a **Költségkezelés + számlázás** elemre a szolgáltatások listájában.
2. Válassza ki a **Részlegek** elemet a számlázási fiók menüben.
3. Válassza ki a részleg nevét.
4. Válassza ki a **Tulajdonságok** elemet a részleggel kapcsolatos menüben.
5. Másolja át a számlázási fiók és a részleg azonosítóját.
6. Az Ön hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA-regisztrációs fiók

1. Nyissa meg az Azure Portalt, és navigáljon a **Költségkezelés + számlázás** elemre a szolgáltatások listájában.
2. Válassza ki a **Regisztrációs fiókok** elemet a számlázási fiók menüben.
3. Válassza ki a regisztrációs fiók nevét.
4. Válassza ki a **Tulajdonságok** elemet a regisztrációs fiók menüben.
5. Másolja át a számlázási fiók és a regisztrációs fiók azonosítóját.
6. Az Ön hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Felügyeleti csoport

1. Nyissa meg az Azure Portalt, és navigáljon a **Felügyeleti csoportok** elemre a szolgáltatások listájában.
2. Lépjen a felügyeleti csoporthoz.
3. Másolja át a felügyeleti csoport azonosítóját a táblázatból.
4. Az Ön hatóköre: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Előfizetés

1. Nyissa meg az Azure Portalt, és navigáljon az **Előfizetések** elemre a szolgáltatások listájában.
2. Másolja át az előfizetés azonosítóját a táblázatból.
3. Az Ön hatóköre: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Erőforráscsoportok

1. Nyissa meg az Azure Portalt, és navigáljon az **Erőforráscsoportok** elemre a szolgáltatások listájában.
2. Válassza ki az erőforráscsoport nevét.
3. Válassza ki a **Tulajdonságok** elemet az erőforráscsoport menüben.
4. Másolja át az erőforrás-azonosító mező értékét.
5. Az Ön hatóköre: `"/subscriptions/{id}/resourceGroups/{name}"`

A Cost Management használata jelenleg az [Azure Global](https://management.azure.com) és az [Azure Government](https://management.usgovcloudapi.net) alatt támogatott. Az Azure Governmenttel kapcsolatos további információkért lásd az [Azure Global és Government API végpontjait](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping) ismertető szakaszt.

## <a name="next-steps"></a>További lépések

- Ha még nem végezte el a Cost Management első lépéseit, itt megtekintheti a [költségelemzés elkezdésének](quick-acm-cost-analysis.md) lépéseit.
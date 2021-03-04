---
title: Privileged Identity Management (PIM) üzembe helyezése – Azure AD | Microsoft Docs
description: Útmutatás Azure AD Privileged Identity Management (PIM) üzembe helyezésének megtervezéséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036826"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM) üzembe helyezése

Ez a cikk egy lépésenkénti útmutató, amely leírja, hogyan tervezhető meg a Privileged Identity Management (PIM) üzembe helyezése a Azure Active Directory (Azure AD) szervezetében. Ha lehetséges, újra hozzá kell rendelnie a felhasználókat a magas jogosultsági szintű szerepkörökhöz, hogy a lehető leghatékonyabban beépített vagy egyéni szerepkörök legyenek, és tervezze meg az igény szerinti szerepkör-hozzárendeléseket a legtöbb Kiemelt szerepkörhöz. Ebben a cikkben javaslatot teszünk az üzembe helyezés megtervezésére és megvalósítására is.

> [!TIP]
> Ebben a cikkben a következőként megjelölt elemeket fogja látni:
>
> : heavy_check_mark: a **Microsoft javasolja**
>
> Ezek általános javaslatok, amelyeket csak akkor kell megvalósítani, ha az adott vállalati igényekre vonatkoznak.

## <a name="licensing-requirements"></a>Licenckövetelmények

Privileged Identity Management használatához a címtárnak a következő fizetős vagy próbaverziós licencek egyikével kell rendelkeznie. További információ: [a Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 oktatási célokra A5
- Microsoft 365 Nagyvállalati verzió E5

## <a name="how-pim-works"></a>Hogyan működik a PIM?

Ez a szakasz áttekintést nyújt a Privileged Identity Management folyamat megfelelő részeinek tervezési céljáról. További információ: [Mi az Azure ad Privileged Identity Management?](pim-configure.md)

1. Privileged Identity Management használatának megkezdése, hogy a felhasználók jogosultak legyenek a Kiemelt szerepkörökre.
1. Ha egy jogosult felhasználónak a Kiemelt szerepkört kell használnia, Privileged Identity Management használatával aktiválja a szerepkört.
1. A felhasználónak a következő beállításokban kell megadnia a beállításokat:

    - Multi-Factor Authentication használata
    - Aktiválás kérésének jóváhagyása
    - Az aktiválás üzleti okának megadása

1. Miután a felhasználó sikeresen aktiválta a szerepkört, egy meghatározott időtartamra jogosultak lesznek a szerepkör engedélyei.
1. A rendszergazdák megtekinthetik az összes Privileged Identity Management tevékenység előzményeit a naplóban. Emellett az Azure AD-szervezeteket továbbra is biztonságossá tehetik, és Privileged Identity Management funkciók, például hozzáférési felülvizsgálatok és riasztások használatával képesek megfelelni a megfelelőségnek.

## <a name="roles-that-can-be-managed-by-pim"></a>A PIM által felügyelhető szerepkörök

Az **Azure ad-szerepkörök** mindegyike Azure Active Directory (például globális rendszergazda, Exchange-rendszergazda és biztonsági rendszergazda). A szerepkörökről és azok funkcióival kapcsolatos további információkért tekintse meg [Azure Active Directory a rendszergazdai szerepkör engedélyei](../roles/permissions-reference.md)című részt. A rendszergazdák által hozzárendelt szerepkörök meghatározásával kapcsolatos segítségért tekintse meg a [legkevésbé Kiemelt szerepkörök feladat szerint feladatot](../roles/delegate-by-task.md).

Az **Azure-szerepkörök** olyan szerepkörök, amelyek egy Azure-erőforráshoz, erőforráscsoporthoz, előfizetéshez vagy felügyeleti csoportjához vannak társítva. A PIM használatával igény szerinti hozzáférést biztosíthat a beépített Azure-szerepkörökhöz, például a tulajdonoshoz, a felhasználói hozzáférés-adminisztrátorhoz és a Közreműködőhöz, valamint az [egyéni szerepkörökhöz](../../role-based-access-control/custom-roles.md)is. Az Azure-szerepkörökkel kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md).

További információ: [Privileged Identity Managementban nem felügyelhető szerepkörök](pim-roles.md).

## <a name="deployment-plan"></a>Telepítési terv

Mielőtt üzembe helyezi a Privileged Identity Management a szervezetében, kövesse az utasításokat, és ismerkedjen meg az ebben a szakaszban szereplő fogalmakkal, hogy segítsen a szervezete privilegizált identitási követelményeihez igazított terv létrehozásában.

### <a name="identify-your-stakeholders"></a>Az érintettek azonosítása

A következő szakasz segítségével azonosíthatja a projektben érintett összes érintettet, és ki kell jelentkeznie, ellenőriznie vagy tájékoztatnia kell. Külön táblákat tartalmaz a PIM Azure AD-szerepkörökhöz és a PIM Azure-szerepkörökhöz való telepítéséhez. Adja hozzá az érintetteket az alábbi táblázathoz a szervezete számára megfelelő módon.

- TEHÁT = kijelentkezés ezen a projekten
- R = a projekt áttekintése és bemenet megadása
- I = tájékozott a projektről

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Érintett felek: Privileged Identity Management Azure AD-szerepkörökhöz

| Name | Szerepkör | Művelet |
| --- | --- | --- |
| Név és e-mail | **Identity Architect vagy Azure globális rendszergazda**<br/>Az Identity Management csapat képviselője, amely meghatározza, hogyan igazíthatja ezt a változást a szervezeten belüli alapvető Identity Management-infrastruktúrával. | SO/R/I |
| Név és e-mail | **Szolgáltatás tulajdonosa/soros kezelő**<br/>Egy szolgáltatás vagy szolgáltatási csoport informatikai tulajdonosai. Ezek kulcsfontosságúak a döntések meghozatalában és a csapatuk Privileged Identity Managementának bevezetésében. | SO/R/I |
| Név és e-mail | **Biztonsági tulajdonos**<br/>A biztonsági csapat képviselője, aki kijelentkezhet, hogy a csomag megfelel-e a szervezete biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatás kezelője/ügyfélszolgálata**<br/>Az informatikai támogatási szervezet egyik képviselője, aki visszajelzést adhat a változás támogatásáról egy segélyszolgálat szemszögéből. | R/I |
| A kísérleti felhasználók neve és e-mail-címe | **Kiemelt szerepkörű felhasználók**<br/>Azon felhasználók csoportja, amelyeken a Privileged Identity Management implementálva van. Tudnia kell, hogyan kell aktiválni a szerepköröket Privileged Identity Management megvalósítása után. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Érdekelt felek: Privileged Identity Management Azure-szerepkörökhöz

| Name | Szerepkör | Művelet |
| --- | --- | --- |
| Név és e-mail | **Előfizetés/erőforrás tulajdonosa**<br/>Az egyes előfizetések vagy erőforrások tulajdonosának a Privileged Identity Management központilag telepíteni kívánó képviselője | SO/R/I |
| Név és e-mail | **Biztonsági tulajdonos**<br/>A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek. | SO/R |
| Név és e-mail | **INFORMATIKAI támogatás kezelője/ügyfélszolgálata**<br/>Az informatikai támogatási szervezet egyik képviselője, aki visszajelzést adhat a változás támogatásáról egy segélyszolgálat szemszögéből. | R/I |
| A kísérleti felhasználók neve és e-mail-címe | **Azure-szerepkör felhasználói**<br/>Azon felhasználók csoportja, amelyeken a Privileged Identity Management implementálva van. Tudnia kell, hogyan kell aktiválni a szerepköröket Privileged Identity Management megvalósítása után. | I |

### <a name="start-using-privileged-identity-management"></a>A Privileged Identity Management használatának első lépései

A tervezési folyamat részeként elő kell készítenie Privileged Identity Managementt a Privileged Identity Management cikk [használatának megkezdése](pim-getting-started.md) után. A Privileged Identity Management hozzáférést biztosít néhány olyan szolgáltatáshoz, amelyet az üzembe helyezésének segítésére terveztek.

Ha a cél az Azure-erőforrásokhoz való Privileged Identity Management üzembe helyezése, kövesse az [Azure-erőforrások felderítése Privileged Identity Management cikkben való felügyeletét](pim-resource-roles-discover-resources.md) ismertető cikket. Csak az előfizetések és a felügyeleti csoportok tulajdonosai vehetik igénybe ezeket az erőforrásokat Privileged Identity Management felügyelete alá. A felügyelet alatt a PIM funkció minden szinten elérhető a tulajdonosok számára, beleértve a felügyeleti csoportot, az előfizetést, az erőforráscsoportot és az erőforrást. Ha Ön egy globális rendszergazda, aki az Azure-erőforrások Privileged Identity Management telepítését kísérli meg, az [összes Azure-előfizetés kezeléséhez megemelheti a hozzáférést](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , hogy az összes Azure-erőforráshoz hozzáférhessen a felderítéshez. Azt javasoljuk azonban, hogy az egyes előfizetésekhez tartozó tulajdonosok jóváhagyása előtt az erőforrásaikat Privileged Identity Management-mel kezelhesse.

### <a name="enforce-principle-of-least-privilege"></a>A legalacsonyabb jogosultsági szint betartatásának elve

Fontos, hogy az Azure AD-hez és az Azure-szerepkörökhöz egyaránt érvényesítse a legalacsonyabb jogosultsági szint elvét a szervezetben.

#### <a name="plan-least-privilege-delegation"></a>A minimális jogosultságok delegálásának megtervezése

Az Azure AD-szerepkörök esetében gyakori, hogy a szervezetek számos rendszergazdához rendelik a globális rendszergazdai szerepkört, ha a legtöbb rendszergazdának csak egy vagy két konkrét és kevésbé hatékony rendszergazdai szerepkörre van szüksége. Nagy számú globális rendszergazda vagy más magas jogosultsági szintű szerepkör esetén nehéz megfelelően nyomon követni a Kiemelt szerepkör-hozzárendeléseket.

Az alábbi lépéseket követve implementálhatja az Azure AD-szerepkörökhöz tartozó legalacsonyabb jogosultsági szint elvét.

1. A szerepkörök részletességének megismeréséhez olvassa el és ismerkedjen meg az elérhető [Azure ad beépített szerepköreivel](../roles/permissions-reference.md). Az Azure AD-ben Ön és csapata is hivatkozhat a [rendszergazdai szerepkörökre az Azure ad-ben](../roles/delegate-by-task.md), amely ismerteti a legkevésbé Kiemelt szerepkört az adott feladatokhoz.

1. A szervezete Kiemelt szerepkörrel rendelkező szerepköreinek listázása. A Privileged Identity Management [felderítés és az észlelés (előzetes verzió)](pim-security-wizard.md) segítségével csökkentheti a kitettséget.

    ![Felderítési és észlelési (előzetes) lap, amellyel csökkentheti az expozíciót a Kiemelt szerepkörök használatával](./media/pim-deployment-plan/new-preview-page.png)

1. A szervezet összes globális rendszergazdája számára Ismerje meg, hogy miért van szükségük a szerepkörre. Ezután távolítsa el őket a globális rendszergazdai szerepkörből, és rendeljen hozzájuk olyan beépített szerepköröket vagy egyéni szerepköröket, amelyek alacsonyabb jogosultsággal rendelkeznek Azure Active Directoryon belül. FYI, a Microsoft jelenleg csak a globális rendszergazdai szerepkörrel rendelkező 10 rendszergazdával rendelkezik. További információ a [Microsoft által Privileged Identity Management használatáról](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. Az összes többi Azure AD-szerepkör esetében tekintse át a hozzárendelések listáját, azonosítsa azokat a rendszergazdákat, akiknek már nincs szüksége a szerepkörre, majd távolítsa el őket a hozzárendeléseiről.

Az utolsó két lépés automatizálásához használhatja a Privileged Identity Management hozzáférési felülvizsgálatait. Az [Privileged Identity Management Azure ad-szerepkörökhöz való hozzáférési felülvizsgálat indítása](pim-how-to-start-security-review.md)című rész lépéseit követve hozzáférési felülvizsgálatot állíthat be minden olyan Azure ad-szerepkörhöz, amely egy vagy több taggal rendelkezik.

![Hozzáférési felülvizsgálati ablaktábla létrehozása Azure AD-szerepkörökhöz](./media/pim-deployment-plan/create-access-review.png)

A felülvizsgálók beállítása a **tagoknak (saját)**. A szerepkör összes felhasználója egy e-mailt fog kapni, amely arra kéri, hogy erősítse meg, hogy szükségük van a hozzáférésre. Emellett a speciális beállításokban be kell kapcsolni a **jóváhagyás szükséges okát** , hogy a felhasználóknak meg kell tenniük, hogy miért van szükségük a szerepkörre. Ezen információk alapján eltávolíthatja a felhasználókat a szükségtelen szerepkörökből, vagy delegálhatja azokat részletesebb rendszergazdai szerepkörökre.

A hozzáférési felülvizsgálatok az e-mailekre támaszkodva értesítik a felhasználókat, hogy vizsgálják át a szerepkörük hozzáférését. Ha olyan Kiemelt jogosultságú fiókkal rendelkezik, amelyekhez nem tartozik e-mailek társítva, akkor ügyeljen arra, hogy a másodlagos e-mail-mezőket feltöltse a fiókoknál. További információ: [proxyAddresses attribútum az Azure ad-ben](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Azure erőforrás-szerepkör delegálásának megtervezése

Az Azure-előfizetések és-erőforrások esetében beállíthat egy hasonló hozzáférési felülvizsgálati folyamatot, amellyel áttekintheti a szerepköröket az egyes előfizetésekben vagy erőforrásokban. Ennek a folyamatnak a célja az egyes előfizetésekhez vagy erőforrásokhoz csatolt tulajdonosi és felhasználói hozzáférési rendszergazdai hozzárendelések csökkentése, valamint a szükségtelen hozzárendelések eltávolítása. A szervezetek azonban gyakran delegálják ezeket a feladatokat az egyes előfizetések vagy erőforrások tulajdonosának, mert jobban megértették az adott szerepköröket (különösen az egyéni szerepköröket).

Ha a globális rendszergazdai szerepkörben szeretné telepíteni a PIM-t az Azure-szerepkörökhöz a szervezetében, a [hozzáférést az összes Azure-előfizetés kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) elérheti, hogy hozzáférjen az egyes előfizetésekhez. Ezután megkeresheti az egyes előfizetések tulajdonosait, és dolgozhat velük a szükségtelen hozzárendelések eltávolításához és a tulajdonosi szerepkör-hozzárendelés minimalizálásához.

Az Azure-előfizetésekhez tartozó tulajdonosi szerepkörrel rendelkező felhasználók az Azure- [erőforrások hozzáférési felülvizsgálatait](pim-resource-roles-start-access-review.md) is használhatják az Azure ad-szerepkörökhöz korábban ismertetett eljáráshoz hasonló, szükségtelen szerepkör-hozzárendelések naplózására és eltávolítására.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Döntse el, hogy mely szerepkör-hozzárendeléseket szeretné védeni Privileged Identity Management

Miután feltöltötte a Kiemelt szerepkör-hozzárendeléseket a szervezetében, el kell döntenie, hogy mely szerepköröket kell védelemmel ellátni Privileged Identity Management.

Ha Privileged Identity Management védi a szerepkört, akkor a hozzá tartozó jogosult felhasználóknak a szerepkör által biztosított jogosultságokat kell használniuk. A jogosultságszint-emelési folyamat magában foglalhatja a jóváhagyás megszerzését, a többtényezős hitelesítés használatát, és az aktiválásuk okát is. Privileged Identity Management az értesítéseken és a Privileged Identity Management és az Azure AD naplózási eseménynaplókban is nyomon követheti a jogosultságszint-emeléseket.

Ha kijelöli, hogy mely szerepköröket szeretné a védelemmel ellátni Privileged Identity Management, akkor az egyes szervezetek számára is bonyolult lehet. Ez a szakasz az Azure AD-vel és az Azure-szerepkörökkel kapcsolatos ajánlott eljárásokat ismerteti.

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Fontos, hogy rangsorolja a legtöbb jogosultságokkal rendelkező Azure AD-szerepkörök védelmét. Az összes Privileged Identity Management ügyfél használati mintái alapján a Privileged Identity Management által kezelt első 10 Azure AD-szerepkör a következő:

1. Globális rendszergazda
1. Biztonsági rendszergazda
1. Felhasználói rendszergazda
1. Exchange-rendszergazda
1. SharePoint-rendszergazda
1. Intune-rendszergazda
1. Biztonsági olvasó
1. Szolgáltatás-rendszergazda
1. Számlázási adminisztrátor
1. Skype Vállalati verzió-rendszergazda

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes globális rendszergazdát és biztonsági rendszergazdát a Privileged Identity Management első lépéseként kezelje, mivel azok a felhasználók, akik feltörték a legkárt.

Fontos figyelembe venni, hogy milyen adatok és engedélyek vannak a szervezete számára leginkább kényesek. Előfordulhat például, hogy egyes szervezetek a Privileged Identity Management használatával szeretnék védelemmel ellátni Power BI rendszergazdai szerepkört vagy a csapatuk rendszergazdai szerepkörét, mert az adataik eléréséhez és a fő munkafolyamatok módosításához is hozzáférnek.

Ha vannak olyan szerepkörök, amelyekhez vendég felhasználó van hozzárendelve, akkor sebezhetőek a támadásokkal szemben.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az Privileged Identity Management segítségével minden szerepkört kezelje a vendég felhasználókkal, hogy csökkentse a feltört vendég felhasználói fiókokkal kapcsolatos kockázatokat.

Az olvasói szerepköröket, például a címtár-olvasót, az üzenetsor-olvasót és a biztonsági olvasót esetenként kevésbé fontosnak tekintik, mint a többi szerepkört, mivel nem rendelkeznek írási engedéllyel. Vannak azonban olyan ügyfeleink, akik szintén védik ezeket a szerepköröket, mert az ilyen fiókokhoz hozzáférő támadók képesek lehetnek bizalmas adatok, például személyes adatok olvasására. Vegye figyelembe ezt a kockázatot annak eldöntése során, hogy szeretné-e az olvasói szerepköröket a szervezetben felügyelni Privileged Identity Management használatával.

#### <a name="azure-roles"></a>Azure-szerepkörök

Annak eldöntéséhez, hogy mely szerepkör-hozzárendeléseket kell felügyelni az Azure Resource Privileged Identity Management használatával, először azonosítania kell a szervezete számára leglényegesebb előfizetéseket/erőforrásokat. Ilyen előfizetések/erőforrások például a következők:

- A legérzékenyebb adatokat üzemeltető erőforrások
- Az alapszintű, az ügyfelek felé irányuló alkalmazások a következőktől függenek:

Ha Ön globális rendszergazda, aki nem tudja, hogy mely előfizetések és erőforrások fontosak, vegye fel a kapcsolatot az előfizetés-tulajdonosokkal a szervezetében az egyes előfizetések által kezelt erőforrások listájának összegyűjtéséhez. Ezt követően az előfizetés-tulajdonosokkal együttműködve csoportosíthatja az erőforrásokat a súlyossági szint alapján abban az esetben, ha azok biztonsága megsérül (alacsony, közepes és magas). Erőforrások kezelése a Privileged Identity Management a súlyossági szint alapján.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy a kritikus szolgáltatások előfizetésének/erőforrás-tulajdonosának használatával Privileged Identity Management munkafolyamatot hozzon létre a bizalmas előfizetéseken/erőforrásokon belüli összes szerepkörhöz.

Az Azure-erőforrások Privileged Identity Managementa támogatja az időhöz kötött szolgáltatásfiókok használatát. A szolgáltatási fiókokat ugyanúgy kell kezelnie, mint a normál felhasználói fiókok kezelését.

A nem kritikus fontosságú előfizetések/erőforrások esetében nem szükséges Privileged Identity Management beállítani az összes szerepkörhöz. A tulajdonosi és a felhasználói hozzáférés rendszergazdai szerepköreit azonban továbbra is meg kell Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes előfizetés/erőforrás tulajdonosi szerepköreit és felhasználói hozzáférés-felügyeleti szerepköreit Privileged Identity Management használatával kezelje.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Döntse el, hogy szeretne-e csoportot használni a szerepkörök hozzárendeléséhez

Azt határozza meg, hogy egy szerepkört az egyes felhasználók helyett egy csoporthoz rendeljen-e stratégiai döntés. A tervezés során érdemes lehet szerepkört hozzárendelni egy csoporthoz a szerepkör-hozzárendelések kezeléséhez a következő esetekben:

- Számos felhasználó van hozzárendelve egy szerepkörhöz
- Delegálni kívánja a szerepkör hozzárendelését

#### <a name="many-users-are-assigned-to-a-role"></a>Számos felhasználó van hozzárendelve egy szerepkörhöz

Nyomon követheti, hogy ki rendel hozzá egy szerepkörhöz, és hogyan kezelheti a hozzárendeléseket attól függően, hogy mire van szükségük, amikor manuálisan végzi a munkát. Egy csoport szerepkörhöz való hozzárendeléséhez először [hozzon létre egy szerepkörhöz hozzárendelhető csoportot](../roles/groups-create-eligible.md) , majd rendelje hozzá a csoportot egy szerepkörhöz. Ez a művelet a csoportba tartozó mindenki számára ugyanazokat az aktiválási folyamatot, mint az egyes felhasználók számára, akik jogosultak a szerepkörre való jogosultságszint-emelésre. A csoporttagok a Privileged Identity Management aktiválási kérelem és a jóváhagyási folyamat segítségével egyénileg aktiválják a hozzárendeléseket a csoportnak. A csoport nincs aktiválva, csak a felhasználó csoporttagság.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Delegálni kívánja a szerepkör hozzárendelését

A csoport tulajdonosai kezelhetik a csoportok tagságát. Az Azure AD szerepkörhöz hozzárendelhető csoportok esetében csak az emelt szintű szerepkörű rendszergazda, a globális rendszergazda és a csoport tulajdonosai kezelhetik a csoporttagság kezelését. Ha új tagokat vesz fel a csoportba, a tag hozzáfér azokhoz a szerepkörökhöz, amelyekhez a csoport hozzá van rendelve, hogy a hozzárendelés jogosult vagy aktív-e. A csoport tulajdonosai segítségével delegálhatja a csoporttagság kezelését egy hozzárendelt szerepkörhöz, hogy csökkentse a szükséges jogosultságok szélességét. A tulajdonosnak a csoport létrehozásakor való hozzárendelésével kapcsolatos további információkért lásd: [szerepkörhöz hozzárendelhető csoport létrehozása az Azure ad-ben](../roles/groups-create-eligible.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az Azure ad szerepkörrel rendelkező csoportokat Privileged Identity Management alapján a felügyelet alá vonja. Ha egy szerepkörhöz hozzárendelhető csoportot a PIM felügyelete alá hoztak, akkor ezt Kiemelt jogosultságú hozzáférési csoportnak nevezzük. A PIM használatával megkövetelheti, hogy a csoport tulajdonosai a tulajdonos szerepkör-hozzárendelést a csoporttagság kezelése előtt aktiválja. További információ a csoportok PIM-felügyelet alatti létrehozásáról: emelt [szintű hozzáférési csoportok (előzetes verzió) Privileged Identity Managementba való beszerzése](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Döntse el, hogy melyik szerepkör-hozzárendelés legyen állandó vagy jogosult

Ha úgy döntött, hogy Privileged Identity Management által felügyelni kívánt szerepkörök listáját választotta, el kell döntenie, hogy mely felhasználók kapják meg a jogosult szerepkört és a véglegesen aktív szerepkört. A tartósan aktív szerepkörök a Azure Active Directory és az Azure-erőforrások által hozzárendelt normál szerepkörök, míg a jogosult szerepkörök csak Privileged Identity Management társíthatók.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az Azure ad-szerepkörökhöz és az Azure-szerepkörökhöz egyaránt nulla végleges aktív hozzárendelések legyenek, amelyek nem az ajánlott két, az egész világra kiterjedő, globális rendszergazdai szerepkörrel rendelkező [break-Glass sürgősségi hozzáférési fiók](../roles/security-emergency-access.md)

Habár a nulla állandó rendszergazda használatát javasoljuk, időnként nehéz lehet a szervezetek számára ezt azonnal elérni. Az alábbi szempontokat érdemes figyelembe venni a döntés végrehajtásakor:

- Jogosultságszint-emelés gyakorisága – ha a felhasználónak csak egyszer kell kiemelni a Kiemelt jogosultságú hozzárendelést, nem rendelkezhet állandó hozzárendeléssel. Ha azonban a felhasználónak szüksége van a napi feladatra, és a Privileged Identity Management használata jelentősen csökkenti a termelékenységet, akkor az állandó szerepkörnek tekinthetők.
- A szervezetre jellemző esetek – ha az a személy, aki a jogosult szerepkört egy távoli csapatból vagy egy magas prioritású ügyvezetőből áll, a jogosultságszint-emelési folyamattal folytatott kommunikációt és érvényesítést nehéz megállapítani, a végleges szerepkörnek tekinthetők.

> [!TIP]
> : heavy_check_mark: a **Microsoft javasolja** , hogy állítson be ismétlődő hozzáférési felülvizsgálatokat a állandó szerepkör-hozzárendelésekkel rendelkező felhasználók számára (ha van ilyen). További információ az ismétlődő hozzáférési felülvizsgálatról a telepítési terv utolsó szakaszában

### <a name="draft-your-privileged-identity-management-settings"></a>A Privileged Identity Management beállításainak tervezete

A Privileged Identity Management-megoldás implementálása előtt érdemes a szervezet által használt összes Kiemelt szerepkörhöz tartozó Privileged Identity Management-beállításokat előkészíteni. Ez a szakasz néhány példát mutat be az egyes szerepkörök Privileged Identity Management beállításaira (csak referenciák, és a szervezete számára eltérőek lehetnek). Ezeket a beállításokat részletesen ismertetjük a Microsoft javaslataival a táblázatok után.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD-szerepkörök Privileged Identity Management beállításainak megadása

| Szerepkör | MFA megkövetelése | Értesítés | Incidens jegy | Jóváhagyás megkövetelése | Jóváhagyó | Aktiválás időtartama | Állandó rendszergazda |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Globális rendszergazda | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Egyéb globális rendszergazdák | 1 óra | Vészhelyzeti hozzáférési fiókok |
| Exchange-rendszergazda | :heavy_check_mark: | :heavy_check_mark: | x | x | Nincs | 2 óra | Nincs |
| Segélyszolgálat rendszergazdája | x | x | :heavy_check_mark: | x | Nincs | 8 óra | Nincs |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Azure-szerepkörök Privileged Identity Management beállításainak megadása

| Szerepkör | MFA megkövetelése | Értesítés | Jóváhagyás megkövetelése | Jóváhagyó | Aktiválás időtartama | Aktív rendszergazda | Aktív lejárat | Jogosult lejárat |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Kritikus előfizetések tulajdonosa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Az előfizetés egyéb tulajdonosai | 1 óra | Nincs | n.a. | 3 hónap |
| Kevésbé kritikus előfizetések felhasználói hozzáférésének rendszergazdája | :heavy_check_mark: | :heavy_check_mark: | x | Nincs | 1 óra | Nincs | n.a. | 3 hónap |
| Virtuális gépek közreműködője | x | :heavy_check_mark: | x | Nincs | 3 óra | Nincs | n.a. | 6 hónap |

Az alábbi táblázat az egyes beállításokat ismerteti.

| Beállítás | Leírás |
| --- | --- |
| Szerepkör | Annak a szerepkörnek a neve, amelynek a beállításait definiálja. |
| MFA megkövetelése | Azt határozza meg, hogy a jogosult felhasználónak az MFA-t kell-e elvégeznie a szerepkör aktiválása előtt.<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy minden rendszergazdai szerepkör esetében érvényesítse az MFA-t, különösen akkor, ha a szerepkörök vendég felhasználókkal rendelkeznek. |
| Értesítés | Ha igaz értékre van állítva, a globális rendszergazda, a Kiemelt szerepkörű rendszergazda és a szervezet biztonsági rendszergazdája e-mailben értesítést fog kapni, ha egy jogosult felhasználó aktiválja a szerepkört.<br/><br/>**Megjegyzés:** Egyes szervezetek nem rendelkeznek e-mail-címmel, amely a rendszergazdai fiókjához van kötve, az e-mail-értesítések beszerzéséhez be kell állítania egy alternatív e-mail címet, hogy a rendszergazdák megkapják ezeket az e-maileket. |
| Incidens jegy | Azt határozza meg, hogy a jogosult felhasználónak rögzítenie kell-e az incidens jegy számát a szerepkör aktiválása során. Ez a beállítás segít a szervezetnek az egyes aktiválások belső incidensek számával történő azonosításában a nemkívánatos aktiválások enyhítése érdekében.<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az incidensek jegyei száma alapján a belső rendszerébe kösse a Privileged Identity Management. Ez a módszer olyan jóváhagyók esetében lehet hasznos, akiknek az aktiváláshoz környezetre van szükségük. |
| Jóváhagyás megkövetelése | Azt határozza meg, hogy a jogosult felhasználónak meg kell-e kapnia a szerepkör aktiválásához szükséges jóváhagyást.<br/><br/> : heavy_check_mark: a **Microsoft javasolja** , hogy állítson be jóváhagyást a legtöbb engedéllyel rendelkező szerepkörökhöz. Az összes Privileged Identity Management ügyfél, a globális rendszergazda, a felhasználói rendszergazda, az Exchange-rendszergazda, a biztonsági rendszergazda és a jelszó-rendszergazda használati mintái alapján a jóváhagyás beállításával leggyakrabban a leggyakoribb szerepkörök. |
| Jóváhagyó | Ha a jogosult szerepkör aktiválásához jóváhagyásra van szükség, sorolja fel azokat a személyeket, akiknek jóvá kell hagyniuk a kérést. Alapértelmezés szerint a Privileged Identity Management úgy állítja be a jóváhagyót, hogy az összes olyan felhasználó legyen, aki Kiemelt szerepkörű rendszergazda, függetlenül attól, hogy azok állandóak vagy jogosultak-e.<br/><br/>**Megjegyzés:** Ha a felhasználó is jogosult egy Azure AD-szerepkörre és a szerepkör jóváhagyójának használatára, nem lesznek képesek jóváhagyni magukat.<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy a jóváhagyók olyan felhasználók legyenek, akik a leghatékonyabban ismerik a szerepkört és a gyakran használt felhasználókat a globális rendszergazda helyett. |
| Aktiválás időtartama | Az az időtartam, ameddig egy felhasználó a szerepkör lejárta előtt aktiválva lesz. |
| Állandó rendszergazda | Azoknak a felhasználóknak a listája, akik a szerepkör állandó rendszergazdája lesznek (soha nem kell aktiválni).<br/><br/> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes szerepkörhöz nulla állandó rendszergazdai jogosultságot biztosítson, kivéve a globális rendszergazdákat. További információt a WHO-ban jogosultnak kell lennie, és ki kell a terv végleges aktív szakaszát. |
| Aktív rendszergazda | Az Azure-erőforrások esetében az aktív rendszergazda azoknak a felhasználóknak a listája, akik számára soha nem kell aktiválni a szerepkört. Ez a lista nem olyan állandó rendszergazda, mint az Azure AD-szerepkörök esetében, mert lejárati időt állíthat be, amikor a felhasználó elveszíti ezt a szerepkört. |
| Aktív lejárat | Az Azure-szerepkörökhöz tartozó aktív szerepkör-hozzárendelések érvényessége a beállított időtartam után lejár. 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy tartósan aktív lehetőség közül választhat. |
| Jogosult lejárat | Az Azure-szerepkörökre vonatkozó jogosult szerepkör-hozzárendelések ezen időtartam után lejárnak. 15 nap, 1 hónap, 3 hónap, 6 hónap, 1 év vagy tartósan jogosult lehetőség közül választhat. |

## <a name="implementation-plan"></a>Megvalósítási terv

A megfelelő tervezés alapja az, hogy az alkalmazás sikeresen telepíthető Azure Active Directory használatával.  Intelligens biztonságot és integrációt biztosít, amely leegyszerűsíti a bevezetést, miközben csökkenti a sikeres üzembe helyezések idejét.  Ez a kombináció biztosítja, hogy az alkalmazás könnyen integrálható legyen, miközben csökkenti a végfelhasználók idejét.

### <a name="identify-test-users"></a>Tesztelési felhasználók azonosítása

Ez a szakasz a felhasználók és a felhasználók csoportjainak azonosítására használható a megvalósítás ellenőrzéséhez. A tervezés szakaszban kiválasztott beállítások alapján azonosítsa azokat a felhasználókat, akiket szeretne tesztelni az egyes szerepkörökhöz.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az egyes Azure ad-szerepkörök szolgáltatás-tulajdonosai legyenek a tesztek felhasználóinak, hogy megismerjék a folyamatot, és belső tanácsadóként váljon a kivezetéshez.

A táblázatban azonosíthatja azokat a felhasználókat, akik ellenőrizni fogják, hogy a szerepkörök beállításai működnek-e.

| Szerepkörnév | Felhasználók tesztelése |
| --- | --- |
| &lt;Szerepkörnév&gt; | &lt;A szerepkör tesztelésére szolgáló felhasználók&gt; |
| &lt;Szerepkörnév&gt; | &lt;A szerepkör tesztelésére szolgáló felhasználók&gt; |

### <a name="test-implementation"></a>Tesztelés megvalósítása

Most, hogy azonosította a felhasználók tesztelését, ezzel a lépéssel konfigurálhatja a Privileged Identity Management a tesztelési felhasználók számára. Ha a szervezete Privileged Identity Management munkafolyamatot kíván beépíteni a saját belső alkalmazásba ahelyett, hogy a Azure Portal Privileged Identity Management kellene használnia, a [Graph API](/graph/api/resources/privilegedidentitymanagement-root)-ban a Privileged Identity Management összes művelete is támogatott.

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Privileged Identity Management konfigurálása Azure AD-szerepkörökhöz

1. [Konfigurálja az Azure ad szerepkör beállításait](pim-how-to-change-default-settings.md) a tervezett beállítások alapján.

1. Navigáljon az **Azure ad-szerepkörökhöz**, válassza a **szerepkörök** lehetőséget, majd válassza ki a konfigurált szerepkört.

1. Ha a tesztelési felhasználók csoportjának már állandó rendszergazdája van, akkor a megfelelő módon megkeresheti őket, és az állandóról jogosultra konvertálhatja őket a sorban lévő három pont kiválasztásával. Ha még nem rendelkeznek szerepkör-hozzárendelésekkel, létrehozhat [egy új jogosult hozzárendelést](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Ismételje meg a 1-3. lépést a tesztelni kívánt összes szerepkörhöz.

1. Miután beállította a felhasználók tesztelését, el kell küldenie az [Azure ad-szerepkör aktiválásához](pim-how-to-activate-role.md)szükséges hivatkozást.

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Privileged Identity Management konfigurálása Azure-szerepkörökhöz

1. [Konfigurálja az Azure erőforrás-szerepkör beállításait](pim-resource-roles-configure-role-settings.md) egy olyan szerepkörhöz, amely egy adott előfizetésben vagy erőforrásban szeretne tesztelni.

1. Navigáljon az előfizetéshez tartozó **Azure-erőforrásokhoz** , és válassza a **szerepkörök** lehetőséget, majd válassza ki a konfigurált szerepkört.

1. Ha a tesztelési felhasználók csoportjának már aktív rendszergazdája van, akkor rákereshet rájuk, és [frissítheti a szerepkör-hozzárendelését](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Ha még nem rendelkeznek szerepkörrel, [új szerepkört rendelhet hozzá](pim-resource-roles-assign-roles.md#assign-a-role).

1. Ismételje meg a 1-3. lépést a tesztelni kívánt összes szerepkörhöz.

1. Miután beállította a felhasználók tesztelését, el kell küldenie az [Azure-erőforrás szerepkörének aktiválásához](pim-resource-roles-activate-your-roles.md)szükséges hivatkozást.

Ezt a szakaszt kell használnia annak ellenőrzéséhez, hogy a szerepkörökhöz beállított összes konfiguráció megfelelően működik-e. A tesztek dokumentálása a következő táblázattal végezhető el. Az érintett felhasználókkal folytatott kommunikáció optimalizálásához ezt a szakaszt is érdemes használni.

| Szerepkör | Az aktiválás során várt viselkedés | Tényleges eredmények |
| --- | --- | --- |
| Globális rendszergazda | (1) MFA megkövetelése<br/>(2) jóváhagyás szükséges<br/>(3) a jóváhagyó értesítést kap, és jóváhagyhatja<br/>(4) a szerepkör az előre beállított idő után lejár |  |
| Az előfizetés *X* tulajdonosa | (1) MFA megkövetelése<br/>(2) a jogosult hozzárendelés a beállított időszak után lejár |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Privileged Identity Management kommunikál az érintett felekkel

A Privileged Identity Management üzembe helyezése további lépéseket fog bevezetni a Kiemelt szerepkörök felhasználói számára. Bár a Privileged Identity Management nagy mértékben csökkenti a Kiemelt identitásokkal kapcsolatos biztonsági problémákat, a változást a szervezet teljes körű üzembe helyezése előtt hatékonyan kell közölni. A befolyásolt rendszergazdák számától függően a szervezetek gyakran belső dokumentumot, videót vagy e-mailt hoznak létre a változásról. A közleményekben gyakran szerepelnek a következők:

- Mi az a PIM?
- Milyen előnyökkel jár a szervezet számára
- Kik lesznek érintettek
- A PIM kidobásakor
- Milyen további lépésekre lesz szükség a felhasználók számára a szerepkör aktiválásához
    - A dokumentációra mutató hivatkozásokat kell elküldenie:
    - [Azure AD-szerepkörök aktiválása](pim-how-to-activate-role.md)
    - [Azure-szerepkörök aktiválása](pim-resource-roles-activate-your-roles.md)
- A PIM-vel kapcsolatos esetleges problémákért forduljon a kapcsolattartási adatokhoz vagy az ügyfélszolgálathoz

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az ügyfélszolgálati/támogatási csapatával időt állítsanak be a Privileged Identity Management munkafolyamaton keresztül (ha a szervezet belső informatikai támogatási csapattal rendelkezik). Adja meg a megfelelő dokumentációt, valamint a kapcsolattartási adatait.

### <a name="move-to-production"></a>Átállás éles üzemre

A tesztelés befejezése és sikeres végrehajtása után helyezze át Privileged Identity Management az éles környezetbe úgy, hogy a tesztelési fázisok összes lépését megismétli a Privileged Identity Management-konfigurációban meghatározott szerepkörök összes felhasználója számára. Az Azure AD-szerepkörökhöz Privileged Identity Management a szervezetek gyakran tesztelik és kivezetik Privileged Identity Management a globális rendszergazdák számára, mielőtt tesztelik és kivezetik a többi szerepkörhöz Privileged Identity Management. Az Azure-erőforrások mellett a szervezetek általában egy Azure-előfizetést Privileged Identity Management egyszerre tesztelnek és állítanak elő.

### <a name="if-a-rollback-is-needed"></a>Ha visszaállításra van szükség

Ha Privileged Identity Management az éles környezetben nem tudott megfelelően dolgozni, a következő visszaállítási lépések segítséget nyújthatnak a megfelelő állapot visszaállításához a Privileged Identity Management beállítása előtt:

#### <a name="azure-ad-roles"></a>Azure AD-szerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Válassza az **Azure ad-szerepkörök** lehetőséget, majd válassza a **szerepkörök** lehetőséget.
1. Minden konfigurált szerepkörhöz válassza a három pontot (**..**.) minden olyan felhasználó számára, aki jogosult hozzárendeléssel rendelkezik.
1. Válassza az **állandó** beállítás lehetőséget a szerepkör-hozzárendelés véglegesvé tételéhez.

#### <a name="azure-roles"></a>Azure-szerepkörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Válassza ki az **Azure-erőforrások** elemet, majd válassza ki azt az előfizetést vagy erőforrást, amelyet vissza szeretne állítani.
1. Válassza a **szerepkörök** lehetőséget.
1. Minden konfigurált szerepkörhöz válassza a három pontot (**..**.) minden olyan felhasználó számára, aki jogosult hozzárendeléssel rendelkezik.
1. Válassza az **állandó** beállítás lehetőséget a szerepkör-hozzárendelés véglegesvé tételéhez.

## <a name="next-steps-after-deploying"></a>A telepítés utáni következő lépések

A Privileged Identity Management éles környezetben történő üzembe helyezése jelentős előrelépést jelent a szervezet emelt szintű identitásának védelme szempontjából. A Privileged Identity Management üzembe helyezése további Privileged Identity Management funkciókat is tartalmaz, amelyeket a biztonság és a megfelelőség érdekében érdemes használni.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Az emelt szintű hozzáférés védelme Privileged Identity Management riasztások használatával

További információ a Privileged Identity Management beépített riasztási funkcióinak használatáról a szervezet védelme érdekében: [biztonsági riasztások](pim-how-to-configure-security-alerts.md#security-alerts). Ezek a riasztások a következők: a rendszergazdák nem Kiemelt szerepköröket használnak, a szerepköröket a Privileged Identity Managementon kívül kell kiosztani, a szerepköröket túl gyakran aktiválják. A szervezet teljes körű védelem érdekében rendszeresen menjen végig a riasztások listáján, és javítsa ki a problémákat. A riasztásokat a következő módon tekintheti meg és javíthatja:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg **Azure ad Privileged Identity Management**.
1. Válassza az **Azure ad-szerepkörök** lehetőséget, majd válassza a **riasztások** lehetőséget.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az összes riasztást azonnal feltüntetve. Közepes és alacsony súlyosságú riasztások esetén tájékoztatni kell, és módosítania kell a módosításokat, ha úgy gondolja, hogy biztonsági fenyegetés van.

Ha az adott riasztások bármelyike nem hasznos, vagy nem vonatkozik a szervezetére, a riasztást bármikor elvetheti a riasztások lapon. Később bármikor visszaállíthatja ezt az elbocsátást az Azure AD beállítások lapján.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Ismétlődő hozzáférési felülvizsgálatok beállítása a szervezet emelt szintű identitásának rendszeres auditálásához

A hozzáférési felülvizsgálatok lehetővé teszik a legjobb megoldást arra, hogy a Kiemelt szerepkörökkel vagy konkrét felülvizsgálók által hozzárendelt felhasználókat Kérdezzen rá, hogy az egyes felhasználóknak szükségük van-e a privilegizált identitásra. A hozzáférési felülvizsgálatok nagyszerűek, ha csökkenteni szeretné a támadási felületet, és megfelelőnek kell lennie. További információ a hozzáférési felülvizsgálat elindításáról: [Azure ad-szerepkörök hozzáférési](pim-how-to-start-security-review.md) felülvizsgálatok és az [Azure-szerepkörök hozzáférési](pim-resource-roles-start-access-review.md)felülvizsgálatai. Egyes szervezetek esetében az időszakos hozzáférési felülvizsgálat elvégzése szükséges a törvények és szabályozások betartásához, míg mások esetében a hozzáférési felülvizsgálat a legjobb módszer a legalacsonyabb jogosultsági szint érvényesítésére a szervezeten belül.

> [!TIP]
> : heavy_check_mark: a **Microsoft javasolja** , hogy negyedévente hozzáférési felülvizsgálatokat állítson be az összes Azure ad-és Azure-szerepkörhöz.

A legtöbb esetben az Azure ad-szerepkörök felülvizsgáló maga a felhasználó, miközben az Azure-szerepkörök véleményezője az előfizetés tulajdonosa, amely a szerepkör. Gyakran előfordul azonban, hogy a vállalatok olyan Kiemelt jogosultságú fiókkal rendelkeznek, amelyek nem kapcsolódnak egy adott személy e-mail-címéhez. Ezekben az esetekben senki nem olvas és nem tekinti át a hozzáférést.

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy adjon hozzá egy másodlagos e-mail-címet minden olyan fiókhoz, amelynek Kiemelt szerepkörű hozzárendelései vannak, amelyek nem kapcsolódnak rendszeresen ellenőrzött e-mail-címekhez.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>A biztonság és a megfelelőség javítása érdekében hozza ki a legtöbbet a naplóból

A napló az a hely, ahol naprakész maradhat, és megfelel a szabályozásoknak. A Privileged Identity Management jelenleg a szervezeti előzmények 30 napos előzményeit tárolja a naplójában, beleértve a következőket:

- A jogosult szerepkörök aktiválása/inaktiválása
- Szerepkör-hozzárendelési tevékenységek Privileged Identity Managementon belül és kívül
- A szerepkör beállításaiban bekövetkezett változások
- A szerepkör-aktiválásra vonatkozó kérés/jóváhagyás/megtagadás tevékenységek jóváhagyási beállítással
- Frissítés riasztásokra

A naplókat akkor érheti el, ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda. További információ: Azure [ad-szerepkörök naplózási előzményei](pim-how-to-use-audit-log.md) és [Naplózási előzmények az Azure-szerepkörökhöz](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy legalább egy rendszergazdát beolvasson az összes naplózási eseményről hetente, és havi rendszerességgel exportálja a naplózási eseményeket.

Ha hosszabb ideig szeretné automatikusan tárolni a naplózási eseményeket, Privileged Identity Management naplóját a rendszer automatikusan szinkronizálja az [Azure ad-naplókba](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft azt javasolja** , hogy az [Azure log-figyelés](../reports-monitoring/concept-activity-logs-azure-monitor.md) beállításával archiválja az Azure Storage-fiókban található naplózási eseményeket a nagyobb biztonság és megfelelőség érdekében.

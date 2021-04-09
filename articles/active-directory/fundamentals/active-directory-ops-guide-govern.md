---
title: Azure Active Directory irányítási műveletek útmutatója
description: Ez az üzemeltetési útmutató ismerteti az irányítási felügyelet biztonságossá tételéhez szükséges ellenőrzéseket és műveleteket.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: ba3ca140abe36a31ffa03422420ea537bbe4f39a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935750"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory irányítási műveletek útmutatója

Az [Azure ad üzemeltetési útmutatójának](active-directory-ops-guide-intro.md) jelen szakasza azokat az ellenőrzéseket és műveleteket ismerteti, amelyeket el kell végeznie, hogy értékelje és igazolja a hozzáférést a nem Kiemelt jogosultságú és emelt szintű identitások, a naplózás és a környezet változásainak ellenőrzéséhez.

> [!NOTE]
> Ezek a javaslatok a közzététel dátumától kezdve érvényesek, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell irányítási eljárásaikat, mivel a Microsoft termékei és szolgáltatásai idővel fejlődnek.

## <a name="key-operational-processes"></a>Fő működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok kiosztása a legfontosabb feladatokhoz

A Azure Active Directory kezelése a legfontosabb működési feladatok és folyamatok folyamatos végrehajtásához szükséges, ami esetleg nem része a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Feladat | Tulajdonos |
| :- | :- |
| Azure AD-naplók archiválása SIEM-rendszeren | InfoSec-műveleti csapat |
| A megfelelőséggel felügyelt alkalmazások felderítése | IAM Operations csapat |
| Az alkalmazásokhoz való hozzáférés rendszeres áttekintése | InfoSec-architektúra csapata |
| A külső identitásokhoz való hozzáférés rendszeres áttekintése | InfoSec-architektúra csapata |
| Rendszeresen tekintse át a Kiemelt szerepköröket | InfoSec-architektúra csapata |
| Biztonsági kapuk definiálása a Kiemelt szerepkörök aktiválásához | InfoSec-architektúra csapata |
| A hozzájárulási támogatások rendszeres felülvizsgálata | InfoSec-architektúra csapata |
| Katalógusok és hozzáférési csomagok tervezése a szervezet alkalmazottai által használt alkalmazásokhoz és erőforrásokhoz | Alkalmazás tulajdonosai |
| Biztonsági házirendek meghatározása a felhasználók hozzáférési csomagokhoz való hozzárendeléséhez | InfoSec-csapat + alkalmazás tulajdonosai |
| Ha a szabályzatok jóváhagyási munkafolyamatokat tartalmaznak, rendszeresen tekintse át a munkafolyamat-jóváhagyásokat | Alkalmazás tulajdonosai |
| Tekintse át a biztonsági házirendekben, például a feltételes hozzáférési házirendekben szereplő kivételeket a hozzáférési felülvizsgálatok használatával | InfoSec-műveleti csapat |

A lista áttekintése során előfordulhat, hogy tulajdonost kell rendelnie olyan feladatokhoz, amelyek nem rendelkeznek tulajdonossal, vagy nem módosítanak tulajdonjogot olyan feladatokhoz, amelyek nem illeszkednek a fenti javaslatokhoz.

#### <a name="owner-recommended-reading"></a>Tulajdonos által ajánlott olvasás

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](../roles/permissions-reference.md)
- [Irányítás az Azure-ban](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>Konfiguráció módosításainak tesztelése

Vannak olyan változtatások, amelyek különleges szempontokat igényelnek a tesztelés során, olyan egyszerű technikáktól, mint például a felhasználók célzott részhalmazának bevezetése egy párhuzamos tesztelési bérlő módosításának üzembe helyezéséhez. Ha még nem alkalmazott tesztelési stratégiát, az alábbi táblázatban szereplő irányelvek alapján meg kell határoznia a tesztelési módszert:

| Eset| Ajánlás |
|-|-|
|A hitelesítési típus módosítása összevontról PHS/PTA-re vagy fordítva| A [szakaszos](../hybrid/how-to-connect-staged-rollout.md) bevezetéssel tesztelheti a hitelesítési típus módosításának hatását.|
|Új feltételes hozzáférési (CA-) házirend vagy Identity Protection-házirend bevezetése|Hozzon létre egy új feltételes hozzáférési szabályzatot, és rendelje hozzá a felhasználók teszteléséhez.|
|Egy alkalmazás tesztelési környezetének bevezetése|Vegye fel az alkalmazást éles környezetbe, rejtse el a MyApps panelen, és rendelje hozzá a felhasználók teszteléséhez a minőségbiztosítási (QA) fázisban.|
|Szinkronizálási szabályok módosítása|Hajtsa végre a tesztekben Azure AD Connect módosításokat ugyanazzal a konfigurációval, amely jelenleg éles környezetben van, más néven átmeneti mód, és elemezze a CSExport eredményeit. Ha teljesülnek, az éles környezetbe kerül, ha készen áll.|
|A branding módosítása|Tesztelés külön tesztelési bérlőn.|
|Új funkció bevezetése|Ha a szolgáltatás támogatja a felhasználók célcsoportjának kiépítését, a kísérleti felhasználók azonosítását és kiépítését. Például az önkiszolgáló jelszó-visszaállítás és a többtényezős hitelesítés meghatározott felhasználókat vagy csoportokat célozhat meg.|
|Átváltás egy helyszíni Identitáskezelő (identitásszolgáltató) alkalmazásból, például Active Directoryból az Azure AD-be|Ha az alkalmazás több identitásszolgáltató-konfigurációt is támogat, például a Salesforce-t, a módosítási időszakban konfigurálja az Azure AD-t és a tesztelést (ha az alkalmazás bevezeti a HRD lapot). Ha az alkalmazás nem támogatja több IDP használatát, ütemezze a tesztet a változás-ellenőrzési időszak során, és állítsa be a program leállását.|
|Dinamikus csoport szabályainak frissítése|Hozzon létre egy párhuzamos dinamikus csoportot az új szabállyal. Hasonlítsa össze a kiszámított eredményt, például futtassa a PowerShellt ugyanazzal a feltétellel.<br>Ha a test pass (tesztelés), a régi csoportot használó helyeket cseréli le (ha lehetséges).|
|A licencek áttelepíthetők|Tekintse meg a [licencek egyetlen felhasználóhoz való módosítását egy licenccel rendelkező csoportban Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md).|
|AD FS szabályok módosítása, például engedélyezés, kiadás, MFA|A csoportjogcím használata a felhasználók részhalmazának megcélzására.|
|AD FS hitelesítési élmény vagy hasonló, az egész farmra vonatkozó változtatások módosítása|Hozzon létre egy párhuzamos farmot ugyanazzal az állomásnévvel, adja meg a konfiguráció módosításait, tesztelje az ügyfeleket Hosts fájl, NLB útválasztási szabályok vagy hasonló útválasztás használatával.<br>Ha a célként megadott platform nem támogatja a GAZDAGÉPek fájljait (például a mobileszközök esetében), a vezérlés módosítása.|

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

### <a name="access-reviews-to-applications"></a>Alkalmazások hozzáférési felülvizsgálatai

Idővel a felhasználók felhasználhatják az erőforrásokhoz való hozzáférést, mivel azok a különböző csapatok és pozíciók között mozognak. Fontos, hogy az erőforrás-tulajdonosok rendszeresen vizsgálják felül az alkalmazásokhoz való hozzáférést, és azokat a jogosultságokat, amelyekre már nincs szükség a felhasználók életciklusa során. Az Azure AD [hozzáférési felülvizsgálatok](../governance/access-reviews-overview.md) lehetővé teszik a szervezetek számára a csoporttagságok hatékony kezelését, a vállalati alkalmazások elérését és a szerepkör-hozzárendeléseket. Az erőforrás-tulajdonosoknak rendszeres időközönként át kell tekinteniük a felhasználók hozzáférését, hogy csak a megfelelő személyek férhessenek hozzájuk. Ideális esetben érdemes megfontolni az Azure AD hozzáférési felülvizsgálatok használatát ehhez a feladathoz.

![Hozzáférési felülvizsgálatok kezdőlapja](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Minden, a hozzáférési felülvizsgálattal kommunikáló felhasználónak fizetős prémium szintű Azure AD P2 licenccel kell rendelkeznie.

### <a name="access-reviews-to-external-identities"></a>Külső identitások hozzáférési felülvizsgálatai

Elengedhetetlen, hogy a külső identitásokhoz való hozzáférést csak a szükséges erőforrásokhoz korlátozza, a szükséges idő alatt. Az Azure AD [hozzáférési](../governance/access-reviews-overview.md)felülvizsgálatokat használó összes külső identitáshoz és alkalmazáshoz való hozzáféréshez hozzon létre rendszeres, automatizált hozzáférési felülvizsgálati folyamatot. Ha egy folyamat már létezik a helyszínen, érdemes lehet használni az Azure AD hozzáférési felülvizsgálatait. Az alkalmazás kivonása vagy már nem használatba vételéhez távolítsa el az összes olyan külső identitást, amely hozzáféréssel rendelkezik az alkalmazáshoz.

> [!NOTE]
> Minden, a hozzáférési felülvizsgálattal kommunikáló felhasználónak fizetős prémium szintű Azure AD P2 licenccel kell rendelkeznie.

## <a name="privileged-account-management"></a>Kiemelt jogosultságú fiókok felügyelete

### <a name="privileged-account-usage"></a>Kiemelt fiók használata

A hackerek gyakran a rendszergazdai fiókokat és a privilegizált hozzáférés más elemeit célozzák meg, hogy gyorsan hozzáférjenek a bizalmas adatokhoz és rendszerekhez. Mivel a Kiemelt szerepkörökkel rendelkező felhasználók egyre több időt vesznek igénybe, fontos a rendszergazdai hozzáférés rendszeres felülvizsgálata és kezelése, valamint az Azure AD-hez és az Azure-erőforrásokhoz való, igény szerinti jogosultságok biztosítása.

Ha a szervezet nem rendelkezik olyan folyamattal, amely a Kiemelt fiókokat felügyeli, vagy jelenleg olyan rendszergazdákat használ, akik a szokásos felhasználói fiókjaikat használják a szolgáltatások és az erőforrások kezeléséhez, azonnal érdemes külön fiókokat használni, például az egyiket a napi napi tevékenységekhez. a másik a privilegizált hozzáféréshez és az MFA konfigurálásához. Még jobb, ha a szervezete prémium szintű Azure AD P2 előfizetéssel rendelkezik, akkor azonnal telepítenie kell a [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements) (PIM) programot. Ugyanebben a jogkivonatban tekintse át ezeket a jogosultsági szintű fiókokat, és ha szükséges, [alacsonyabb jogosultsági szintű szerepköröket is rendeljen hozzá](../roles/security-planning.md) .

A Kiemelt jogosultságokkal rendelkező fiókok felügyeletének egy másik aspektusa, amely a fiókok [hozzáférési felülvizsgálatának](../governance/access-reviews-overview.md) meghatározására szolgál, manuálisan vagy [automatizáltan a PIM használatával](../privileged-identity-management/pim-how-to-perform-security-review.md).

#### <a name="privileged-account-management-recommended-reading"></a>Kiemelt jogosultságú fiókok kezelése ajánlott olvasás

- [Szerepkörök a Azure AD Privileged Identity Management](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>Vészhelyzeti hozzáférési fiókok

A szervezeteknek [vészhelyzeti fiókokat](../roles/security-emergency-access.md) kell létrehozniuk az Azure ad kezeléséhez olyan esetekben, mint például a hitelesítési kimaradások:

- A hitelesítési infrastruktúra kimaradási összetevői (AD FS, helyszíni AD, MFA szolgáltatás)
- Felügyeleti munkatársak forgalma

Annak megakadályozása érdekében, hogy véletlenül kizárja a bérlőt, mert nem tud bejelentkezni vagy aktiválni egy meglévő egyéni felhasználói fiókot rendszergazdaként, hozzon létre két vagy több vészhelyzeti fiókot, és gondoskodjon arról, hogy azok implementálva legyenek, és összhangba kerüljenek a [Microsoft ajánlott eljárásaival](../roles/security-planning.md) és az [üveg-eljárások megszakításával](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Emelt szintű hozzáférés az Azure EA portálhoz

Az [azure nagyvállalati szerződés (Azure EA) portál](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) lehetővé teszi, hogy Azure-előfizetéseket hozzon létre egy fő nagyvállalati szerződés, amely egy hatékony szerepkör a vállalaton belül. Gyakori, hogy a portál létrehozása előtt még az Azure AD-t is beolvassa, ezért az Azure AD-identitások használatával le kell zárnia, el kell távolítania a személyes fiókokat a portálról, meg kell győződnie arról, hogy a megfelelő delegálás van érvényben, és csökkenti a zárolás kockázatát.

Ha az EA portál engedélyezési szintjének beállítása jelenleg "vegyes mód", a Microsoft-fiókokat el kell távolítania minden, a [nagyvállalati](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) szerződéssel rendelkező Azure-portálról, és úgy kell konfigurálnia az EA portált, hogy csak az Azure ad-fiókokat használja. Ha az EA-portál delegált szerepkörei nincsenek konfigurálva, a részlegek és a fiókok delegált szerepköreinek megkeresésére és megvalósítására is lehetőség van.

#### <a name="privileged-access-recommended-reading"></a>Kiemelt jogosultságú hozzáférés ajánlott olvasmánya

- [Rendszergazdai szerepkörök engedélyezése az Azure Active Directoryban](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>Jogosultságkezelés

A [jogosultsági felügyelet (em)](../governance/entitlement-management-overview.md) lehetővé teszi, hogy az alkalmazások tulajdonosai erőforrásokat rendeljenek le, és a szervezeten belül meghatározott personák rendelje őket (belső és külső). Az EM lehetővé teszi az önkiszolgáló regisztrációt és a vállalkozások tulajdonosainak való delegálását, miközben az irányítási házirendeket a hozzáférés biztosításához, a hozzáférési időtartamok beállításához és a jóváhagyási munkafolyamatok engedélyezéséhez tartja. 

> [!NOTE]
> Az Azure AD-jogosultságok kezeléséhez prémium szintű Azure AD P2 licencekre van szükség.

## <a name="summary"></a>Összefoglalás

A biztonságos identitás irányításának nyolc aspektusa van. Ez a lista segítséget nyújt azoknak a műveleteknek a meghatározásához, amelyeket el kell végeznie a nem Kiemelt jogosultságú és privilegizált identitásokhoz, naplózáshoz és a környezet változásainak ellenőrzéséhez.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- Tesztelési stratégia implementálása.
- Az Azure AD hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket.
- Hozzon létre egy rendszeres, automatizált hozzáférési felülvizsgálati folyamatot a külső identitások és az alkalmazás-hozzáférés összes típusához.
- Hozzon létre egy hozzáférési felülvizsgálati folyamatot a rendszergazdai hozzáférés rendszeres áttekintéséhez és kezeléséhez, valamint az Azure AD-hez és az Azure-erőforrásokhoz való, igény szerinti jogosultságú hozzáférés biztosításához.
- Hozzon létre vészhelyzeti fiókokat az Azure AD-t nem várt kimaradások kezeléséhez.
- Zárja be az Azure EA portál elérését.
- A jogosultságok felügyeletének megvalósítása a szabályozott hozzáférés biztosításához egy erőforrás-gyűjteményhez.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az [Azure ad operatív ellenőrzésekkel és műveletekkel](active-directory-ops-guide-ops.md).
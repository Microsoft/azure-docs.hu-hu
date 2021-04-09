---
title: Az Azure AD-felhő szabályozza a helyszíni számítási feladatok felügyeletét – Azure
description: Ez a témakör ismerteti a felhőben szabályozott felügyeletet a helyszíni számítási feladatokhoz.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b4d1041b9d330227fadf31f6afc1804174ea2ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96340849"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Hogyan biztosítja az Azure AD a felhőben szabályozott felügyeletet a helyszíni számítási feladatokhoz

Azure Active Directory (Azure AD) egy átfogó, IDaaS-megoldás, amely az identitás, a hozzáférés-kezelés és a biztonság valamennyi aspektusára kiterjedő, több millió szervezetet használ. Az Azure AD több mint egy milliárd felhasználói identitást tart fenn, és segít a felhasználóknak a bejelentkezésben és a biztonságos hozzáférésben:

* Külső erőforrások, mint például a Microsoft 365, a Azure Portal és több ezer más szolgáltatott szoftver (SaaS) alkalmazás.
* Belső erőforrások, például a szervezet vállalati hálózatán és intranetén lévő alkalmazások, valamint az adott szervezet által fejlesztett felhőalapú alkalmazások.

A szervezetek használhatják az Azure AD-t, ha "tiszta felhő" vagy "hibrid" üzemelő példányok, ha helyszíni számítási feladatokkal rendelkeznek. Az Azure AD hibrid üzembe helyezése egy olyan stratégia részét képezheti a szervezet számára, amely áttelepíti az IT-eszközeiket a felhőbe, vagy hogy továbbra is integrálja a meglévő helyszíni infrastruktúrát az új felhőalapú szolgáltatások mellett.

A "hibrid" szervezetek történelmileg a meglévő helyszíni infrastruktúrájuk kiterjesztéseként látták az Azure AD-t. Ezekben az üzembe helyezésekben a helyszíni identitás-irányítási felügyelet, a Windows Server Active Directory vagy más belső címtárszolgáltatás, a vezérlési pontok, a felhasználók és csoportok pedig szinkronizálva lesznek a rendszerből egy Felhőbeli címtárba, például az Azure AD-ba. Ha ezek az identitások a felhőben vannak, elérhetővé tehetők a Microsoft 365, az Azure és más alkalmazások számára is.

![Identitás életciklusa](media/cloud-governed-management-for-on-premises//image1.png)

Mivel a szervezetek több informatikai infrastruktúrát helyeznek át az alkalmazásaikat a felhőbe, a többit a szolgáltatásként nyújtott Identitáskezelés jobb biztonsági és egyszerűsített felügyeleti képességeire keresik. A felhőalapú IDaaS funkciók az Azure AD-ben felgyorsítják a felhőbe irányított felügyeletre való áttérést azáltal, hogy olyan megoldásokat és képességeket biztosítanak, amelyek lehetővé teszik, hogy a szervezetek gyorsan fogadjanak el és helyezzenek el személyazonosságuk kezelését a hagyományos helyi rendszerekről az Azure AD-be, miközben továbbra is támogatják a meglévő és az új alkalmazásokat.

Ez a tanulmány a Microsoft hibrid IDaaS vonatkozó stratégiáját ismerteti, és leírja, hogy a szervezetek hogyan használhatják az Azure AD-t meglévő alkalmazásaikban.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Az Azure AD megközelítése a felhőben szabályozott Identitáskezelés kezeléséhez

A szervezeteknek a felhőbe való áttéréshez olyan biztosítékokra van szükségük, amelyek a teljes környezetük felett vannak, és nagyobb biztonságot és nagyobb láthatóságot biztosítanak a tevékenységek, az automatizálás és az proaktív betekintések révén. "A **felhőben szabályozott felügyelet**" azt ismerteti, hogyan kezelik és szabályozzák a szervezetek a felhasználókat, az alkalmazásokat, a csoportokat és az eszközöket a felhőből.

Ebben a modern világban a szervezeteknek az SaaS-alkalmazások elterjedése, valamint az együttműködés és a külső identitások egyre növekvő szerepe miatt nagy léptékű hatékony felügyeletet kell biztosítaniuk. A felhő új kockázati környezete azt jelenti, hogy a szervezetnek rugalmasabbnak kell lennie – egy rosszindulatú személy, aki feltört egy Felhőbeli felhasználót, hatással lehet a Felhőbeli és a helyszíni alkalmazásokra.

Különösen a hibrid szervezeteknek képesnek kell lenniük a feladatok delegálására és automatizálására, amely történelmileg manuálisan történt. A feladatok automatizálásához olyan API-kra és folyamatokra van szükségük, amelyek a különböző identitásokkal kapcsolatos erőforrások (felhasználók, csoportok, alkalmazások, eszközök) életciklusát hangolják össze, így az erőforrások napi felügyeletét több személy számára is delegálhatja az informatikai személyzeten kívül. Az Azure AD ezeket a követelményeket a felhasználói fiókok felügyelete és a felhasználók natív hitelesítése révén kezeli, anélkül, hogy a helyszíni identitás-infrastruktúrát kellene használnia. A helyszíni infrastruktúra kiépítése nem biztosít olyan szervezeteket, amelyek olyan új közösségekkel rendelkeznek, mint például az üzleti partnerek, amelyek nem a helyszíni címtárból származnak, de a hozzáférés-kezelés kulcsfontosságú az üzleti eredmények eléréséhez.

Emellett a felügyelet nem fejeződött be irányítási---nélkül, és ebben az új világban az identitásrendszer integrált része, nem pedig bővítmény. Az identitás-szabályozás lehetővé teszi a szervezetek számára az identitás és a hozzáférés életciklusának kezelését az alkalmazottak, az üzleti partnerek és a szállítók, valamint a szolgáltatások és alkalmazások között.

A személyazonosság szabályozása megkönnyíti a szervezet számára a felhőbe irányított felügyeletre való áttérést, lehetővé teszi a méretezést, a vendégek új kihívásokkal való ellátását, valamint mélyebb elemzéseket és automatizálást nyújt, mint a helyszíni infrastruktúrával rendelkező ügyfelek. Az új világ irányítása azt jelenti, hogy a szervezet számára a szervezeten belüli erőforrásokhoz való hozzáférés átláthatósága, láthatósága és megfelelő szabályozása szükséges. Az Azure AD lehetővé teszi, hogy a biztonsági műveletek és a könyvvizsgáló csapatok betekintést kapjanak arra, hogy ki---, és kinek van hozzáférése a szervezet erőforrásaihoz (az eszközökön), mit csinálnak a felhasználók az adott hozzáféréssel, valamint hogy a szervezet rendelkezik-e a megfelelő vezérlőkkel, hogy a vállalati vagy szabályozási szabályzatoknak megfelelően távolítsa el vagy korlátozza a hozzáférést.

Az új felügyeleti modell SaaS-és üzletági (LOB) alkalmazásokkal is rendelkezik a szervezetekhez, mivel azok könnyebben kezelhetik és biztonságossá tehetik a hozzáférést ezekhez az alkalmazásokhoz. Az alkalmazások az Azure AD-vel való integrálásával a szervezetek a Felhőbeli és a helyszíni identitások közötti hozzáférést is képesek használni és kezelni. Az alkalmazások életciklusának kezelése egyre hatékonyabb lesz, és az Azure AD részletes információkat nyújt az alkalmazások használatáról, amely nem volt könnyen elérhető a helyszíni Identitáskezelés során. Az Azure AD-n keresztül Microsoft 365 csoportok és csapatok önkiszolgáló szolgáltatásai révén a szervezetek könnyedén hozhatnak létre csoportokat a hozzáférés-kezeléshez és az együttműködéshez, valamint hozzáadhatnak vagy eltávolíthatnak felhasználókat a felhőben az együttműködési és hozzáférés-kezelési követelmények lehetővé tételéhez.

Ha kiválasztja a megfelelő Azure AD-funkciókat a felhőben szabályozott felügyelethez, a használni kívánt alkalmazásoktól függ, és hogy ezek az alkalmazások hogyan lesznek integrálva az Azure AD-vel. Az alábbi fejezetek az AD-integrált alkalmazások és az összevonási protokollokat (például SAML, OAuth vagy OpenID Connect) használó alkalmazások megközelítéseit ismertetik.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Felhőben szabályozott felügyelet az AD-integrált alkalmazások számára

Az Azure AD a biztonságos távoli hozzáférés és a feltételes hozzáférés révén javítja a szervezet helyi Active Directory integrált alkalmazásainak felügyeletét. Emellett az Azure AD a fiókok életciklusának kezelését és a hitelesítő adatok kezelését is biztosítja a felhasználó meglévő AD-fiókjaihoz, beleértve a következőket:

* **Biztonságos távoli hozzáférés és feltételes hozzáférés helyszíni alkalmazásokhoz**

Számos szervezet esetében a felhőből a helyszíni AD-integrált webes és távoli asztali alkalmazásokhoz való hozzáférés kezelésének első lépése a biztonságos távoli hozzáférés [biztosítása az alkalmazások](../manage-apps/application-proxy.md) előtt.

Az Azure AD-be való egyszeri bejelentkezés után a felhasználók külső URL-címen vagy egy belső alkalmazás-portálon keresztül is hozzáférhetnek a Felhőbeli és a helyszíni alkalmazásokhoz. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosít a Távoli asztalhoz, a SharePointhoz, valamint az olyan alkalmazásokhoz, mint a tabló és a Qlik, valamint az üzletági (LOB) alkalmazások. Emellett a feltételes hozzáférési szabályzatok tartalmazhatják a [használati feltételek](../conditional-access/terms-of-use.md) megjelenítését, és [biztosítják, hogy a felhasználó megállapodott bennük,](../conditional-access/require-tou.md) mielőtt hozzáfér az alkalmazáshoz.

![Alkalmazásproxy architektúrája](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory fiókok automatikus életciklus-kezelése**

Az identitások szabályozása révén a szervezetek a *hatékonyságot* ---, hogy milyen gyorsan férhetnek hozzá a felhasználók a szükséges erőforrásokhoz, például ha csatlakoznak a szervezethez? ---és *biztonsági* ---Hogyan változnak a hozzáférésük az idő múlásával, például ha az adott személy foglalkoztatási állapota megváltozik? Az identitás-életciklus kezelése az identitások irányításának alapja, és a hatékony irányítás érdekében az alkalmazásokhoz szükséges az identitás-életciklus-kezelési infrastruktúra korszerűsítése.

Számos szervezet esetében az alkalmazottak identitás-életciklusa az adott felhasználó emberi tőkét kezelő (HCM) rendszerbeli ábrázolásához van kötve. A munkanapokat az HCM-rendszerként használó szervezetek számára az Azure AD lehetővé teszi a felhasználói fiókok automatikus üzembe helyezését és megszüntetését a [munkanapokon](../saas-apps/workday-inbound-tutorial.md). Így a felhasználók termelékenységének növelése a veleszületett jogainak automatizálásával és a kockázatok kezelésével biztosítható, hogy az alkalmazások hozzáférésének automatikus frissítése automatikusan megtörténjen, amikor a felhasználó megváltoztatja a szerepköröket, vagy elhagyja a szervezetet. A munkahelyen alapuló [üzembe helyezési telepítési terv](https://aka.ms/WorkdayDeploymentPlan) egy lépésenkénti útmutató, amellyel a szervezetek a munkanapok gyakorlati megvalósításával Active Directory a felhasználó kiépítési megoldásához egy öt lépésből álló folyamat során.

A prémium szintű Azure AD Microsoft Identity Manager is tartalmaz, amely más helyszíni HCM-rendszerekből származó rekordokat is importálhat, beleértve az SAP-t, az Oracle-t és az Oracle PeopleSoft-t is.

A vállalatok közötti együttműködés egyre inkább a szervezeten kívüli személyek hozzáférésének biztosítását igényli. Az [Azure ad B2B](/azure/active-directory/b2b/) -együttműködés lehetővé teszi, hogy a szervezetek biztonságosan osszák meg alkalmazásaikat és szolgáltatásaikat a vendég felhasználókkal és a külső partnerekkel, miközben a saját céges adataikat szabályozzák.

Az Azure AD lehetővé teszi, hogy a felhasználók igény szerint [automatikusan hozzon létre fiókokat az ad-ben](../external-identities/hybrid-cloud-to-on-premises.md) , így az üzleti vendégek hozzáférhetnek a helyszíni ad-integrált alkalmazásokhoz anélkül, hogy más jelszót kellene megadniuk. A szervezetek beállíthatja [a többtényezős hitelesítési (MFA) házirendeket a vendég felhasználói számára](../external-identities/conditional-access.md), így az MFA-ellenőrzéseket az alkalmazásproxy-hitelesítés során végezheti el. A felhőalapú B2B-felhasználók által végzett [hozzáférési felülvizsgálatok](../governance/manage-guest-access-with-access-reviews.md) a helyszíni felhasználókra is érvényesek. Ha például a Felhőbeli felhasználót az életciklus-kezelési szabályzatok segítségével törli, a helyszíni felhasználó is törlődik.

**Active Directory-fiókok hitelesítő adatainak kezelése** Az Azure AD önkiszolgáló jelszó-visszaállítási szolgáltatása lehetővé teszi, hogy a jelszavak újbóli hitelesítése és a jelszavak alaphelyzetbe állítása érdekében a [rendszer a helyi Active Directoryba írt](../authentication/concept-sspr-writeback.md)módosított jelszavakat használja. A jelszó-visszaállítási folyamat a helyszíni Active Directory jelszóházirend használatát is lehetővé teszi: amikor egy felhasználó visszaállítja a jelszavát, ellenőrzi, hogy az megfelel-e a helyszíni Active Directory házirendnek, mielőtt véglegesíti azt az adott könyvtárba. Az önkiszolgáló jelszó-visszaállítási [telepítési terv](../authentication/howto-sspr-deployment.md) az ajánlott eljárásokat ismerteti az önkiszolgáló jelszó-visszaállítást a felhasználóknak webes és Windows-integrált felületen keresztül.

![Azure AD-SSPR architektúrája](media/cloud-governed-management-for-on-premises/image3.png)

Végül azon szervezetek számára, amelyek lehetővé teszik a felhasználók számára, hogy módosítsák a jelszavukat az AD-ben, az AD konfigurálható úgy, hogy ugyanazt a jelszóházirend használatát használja, mint a szervezet az Azure AD [jelszavas védelem szolgáltatásán](../authentication/concept-password-ban-bad-on-premises.md)keresztül, amely jelenleg nyilvános előzetes verzióban érhető el.

Ha egy szervezet készen áll egy AD-integrált alkalmazás felhőbe való áthelyezésére, az alkalmazást az Azure-ba futtató operációs rendszer áthelyezésével [Azure ad Domain Services](../../active-directory-domain-services/overview.md) ad-kompatibilis tartományi szolgáltatásokat biztosít (például tartományhoz való csatlakozás, csoportházirend, LDAP és KERBEROS/NTLM hitelesítés). Azure AD Domain Services integrálódik a szervezet meglévő Azure AD-Bérlővel, így a felhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be. A meglévő csoportok és felhasználói fiókok emellett az erőforrásokhoz való hozzáférés biztosítására is használhatók, így biztosítva a helyszíni erőforrások zökkenőmentes "átemelését" az Azure infrastrukturális szolgáltatásokhoz.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Felhőbe irányított felügyelet a helyszíni összevonási alkalmazások esetében

Egy olyan szervezet számára, amely már használ helyszíni identitás-szolgáltatót, az alkalmazások Azure AD-be való áthelyezésével biztonságosabb és könnyebben kezelhetővé válik az összevonási felügyelet. Az Azure AD lehetővé teszi, hogy az Azure ad feltételes hozzáférés használatával többalkalmazásos hozzáférés-vezérlést, többek között az Azure AD Multi-Factor Authentication konfigurálja. Az Azure AD több funkciót is támogat, többek között az alkalmazásspecifikus jogkivonat-aláíró tanúsítványokat és a konfigurálható tanúsítvány lejárati dátumát. Ezek a képességek, eszközök és útmutatók lehetővé teszik a szervezetek számára, hogy kivonják a helyszíni identitás-szolgáltatókat. A Microsoft saját informatikai, egy példája miatt 17 987 alkalmazást helyezett át a Microsoft belső Active Directory összevonási szolgáltatások (AD FS) (AD FS) az Azure AD-be.

![Az Azure AD evolúciója](media/cloud-governed-management-for-on-premises/image5.png)

Az összevont alkalmazások identitás-szolgáltatóként való áttelepítésének megkezdéséhez tekintse meg a következő https://aka.ms/migrateapps hivatkozásokat is:

* Az [alkalmazások áttelepítése a Azure Active Directoryre](https://aka.ms/migrateapps/whitepaper), amely az áttelepítés előnyeit mutatja be, és leírja, hogyan tervezze meg az áttelepítést négy jól tagolt fázisban: felderítés, besorolás, áttelepítés és folyamatos felügyelet. Útmutatást kaphat arról, hogyan gondolhatja át a folyamatot, és hogyan bontják le a projektet könnyen felhasználható darabokra. A dokumentum teljes egészében olyan fontos erőforrásokra mutató hivatkozásokat talál, amelyek segítenek az Ön számára.

* A megoldási útmutató az [alkalmazás hitelesítésének Active Directory összevonási szolgáltatások (AD FS)ról Azure Active Directory való áttelepítéséről](../manage-apps/migrate-adfs-apps-to-azure.md) részletesebben ismerteti az alkalmazás-áttelepítési projekt tervezésének és végrehajtásának négy fázisát. Ebből az útmutatóból megtudhatja, hogyan alkalmazhatja ezeket a fázisokat az alkalmazások Active Directory összevonási szolgáltatások (AD FS) (AD FS) Azure AD-be való áthelyezésének konkrét céljára.

* A [Active Directory összevonási szolgáltatások (AD FS) Migrálás készültségi parancsfájlja](https://aka.ms/migrateapps/adfstools) futtatható a meglévő helyszíni Active Directory összevonási szolgáltatások (AD FS) (AD FS) kiszolgálókon az Azure ad-ba való áttelepítéshez használható alkalmazások készültségének meghatározásához.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Folyamatos hozzáférés-kezelés a Felhőbeli és a helyszíni alkalmazások között

A szervezeteknek olyan folyamatra van szükségük, amely a méretezhető hozzáférés kezelésére szolgál. A felhasználók továbbra is felhalmozják a hozzáférési jogosultságokat, és a végén meghaladják azt, amit eredetileg kiosztottak. Emellett a vállalati szervezeteknek hatékonyan kell méretezniük a hozzáférési házirend és az ellenőrzések folyamatos fejlesztését és betartatását.

Ez általában a hozzáférés-jóváhagyási döntéseket delegálja az üzleti döntéshozóknak. Emellett maguk is bejárhatják a felhasználókat. A vállalati marketing alkalmazásban található bizalmas ügyféladatokat használó felhasználóknak például ismerniük kell a vállalat szabályzatait. Emellett előfordulhat, hogy a vendég felhasználói nem tudnak az olyan szervezeten belüli adatkezelési követelményekkel kapcsolatban, amelyeknek meghívást kaptak.

A szervezetek az olyan technológiákon keresztül automatizálják a hozzáférési életciklus folyamatát, mint a [dinamikus csoportok](../enterprise-users/groups-dynamic-membership.md), a felhasználó által az [SaaS-alkalmazásokhoz](../saas-apps/tutorial-list.md)való kiépítés, illetve [a rendszer használatával a tartományok közötti Identitáskezelés (scim](../app-provisioning/use-scim-to-provision-users-and-groups.md)) szabványhoz integrált alkalmazások. A szervezetek azt is szabályozhatják [, hogy mely vendég felhasználók férhetnek hozzá a helyszíni alkalmazásokhoz](../external-identities/hybrid-cloud-to-on-premises.md). Ezek a hozzáférési jogosultságok rendszeresen áttekinthetők az ismétlődő [Azure ad hozzáférési felülvizsgálatok](../governance/access-reviews-overview.md)használatával.

## <a name="future-directions"></a>Jövőbeli irányok

A hibrid környezetekben a Microsoft stratégiája az olyan üzemelő példányok engedélyezése, amelyekben a **felhő az identitás vezérlő síkja**, valamint a helyszíni címtárak és más Identity rendszerek, például a Active Directory és más helyszíni alkalmazások, amelyek célja a felhasználók hozzáférésének kiépítése. Ez a stratégia továbbra is biztosítja a jogosultságokat, az identitásokat és a hozzáférést a rájuk támaszkodó alkalmazásokban és munkaterhelésekben. Ezen a helyen a szervezetek teljes egészében képesek lesznek a végfelhasználók termelékenységét a felhőből elvinni.

![Azure AD-architektúra](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Következő lépések

További információ az utazás megkezdéséről: az Azure AD üzembehelyezési csomagjai, amely a következő címen található: <https://aka.ms/deploymentplans> . Teljes körű útmutatást nyújtanak Azure Active Directory (Azure AD) képességeinek üzembe helyezéséhez. Az egyes tervek ismertetik az Azure AD-képességek sikeres kiépítéséhez szükséges üzleti értékeket, tervezési szempontokat, kialakítási és üzemeltetési eljárásokat. A Microsoft folyamatosan frissíti az üzembe helyezési terveket az ügyfelek üzembe helyezésével és más visszajelzésekkel kapcsolatos ajánlott eljárásokkal, amikor új képességeket adunk a felhőből az Azure AD-vel való felügyelethez.
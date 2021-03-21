---
title: Általános üzemeltetési útmutató Azure Active Directory
description: Ez az üzemeltetési útmutató ismerteti az általános műveletek biztonságossá tételéhez szükséges ellenőrzéseket és műveleteket.
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
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836835"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Általános üzemeltetési útmutató Azure Active Directory

Az [Azure ad üzemeltetési útmutatójának](active-directory-ops-guide-intro.md) jelen szakasza azokat az ellenőrzéseket és műveleteket ismerteti, amelyeket a Azure Active Directory (Azure ad) általános műveleteinek optimalizálása érdekében el kell végeznie.

> [!NOTE]
> Ezek a javaslatok a közzététel dátumától kezdve érvényesek, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell működési eljárásaikat, mivel a Microsoft termékei és szolgáltatásai idővel fejlődnek.

## <a name="key-operational-processes"></a>Fő működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok kiosztása a legfontosabb feladatokhoz

A Azure Active Directory kezelése a legfontosabb működési feladatok és folyamatok folyamatos végrehajtásához szükséges, ami esetleg nem része a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Feladat | Tulajdonos |
| :- | :- |
| Az identitások biztonságos pontszámának fejlesztése | InfoSec-műveleti csapat |
| Azure AD Connect-kiszolgálók karbantartása | IAM Operations csapat |
| IdFix-jelentések rendszeres végrehajtása és osztályozása | IAM Operations csapat |
| A szinkronizálás és a AD FS osztályozása Azure AD Connect Health-riasztások | IAM Operations csapat |
| Ha nem használja a Azure AD Connect Healtht, akkor az ügyfél az egyéni infrastruktúra figyeléséhez megfelelő folyamattal és eszközökkel rendelkezik | IAM Operations csapat |
| Ha nem használja a AD FSt, akkor az ügyfél az egyéni infrastruktúra figyeléséhez megfelelő folyamattal és eszközökkel rendelkezik | IAM Operations csapat |
| Hibrid naplók monitorozása: Azure AD alkalmazás proxy-összekötők | IAM Operations csapat |
| Hibrid naplók monitorozása: továbbító hitelesítési ügynökök | IAM Operations csapat |
| Hibrid naplók figyelése: Password visszaírási Service | IAM Operations csapat |
| Hibrid naplók figyelése: helyszíni jelszavas védelmi átjáró | IAM Operations csapat |
| Hibrid naplók figyelése: Azure AD MFA NPS-bővítmény (ha alkalmazható) | IAM Operations csapat |

A lista áttekintése során előfordulhat, hogy tulajdonost kell rendelnie olyan feladatokhoz, amelyek nem rendelkeznek tulajdonossal, vagy nem módosítanak tulajdonjogot olyan feladatokhoz, amelyek nem illeszkednek a fenti javaslatokhoz.

#### <a name="owners-recommended-reading"></a>Ajánlott olvasás a tulajdonosoknak

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](../roles/permissions-reference.md)
- [Irányítás az Azure-ban](../../governance/index.yml)

## <a name="hybrid-management"></a>Hibrid felügyelet

### <a name="recent-versions-of-on-premises-components"></a>A helyszíni összetevők legújabb verziói

A helyszíni összetevők legfrissebb verzióival az ügyfél a legújabb biztonsági frissítéseket, a teljesítménnyel kapcsolatos fejlesztéseket és a környezet további egyszerűsítését segítő funkciókat biztosít. A legtöbb összetevő automatikus frissítési beállítással rendelkezik, amely automatizálja a frissítési folyamatot.

Ezek az összetevők a következők:

- Azure AD Connect
- Azure AD Application Proxy-összekötők
- Azure AD átmenő hitelesítési ügynökök
- Azure AD Connect Health ügynökök

Ha az egyiket nem hozták létre, meg kell határoznia az összetevők frissítésének folyamatát, és ha lehetséges, az automatikus frissítési funkcióra kell támaszkodnia. Ha hat vagy több hónapot tartalmazó összetevőket talál, akkor a lehető leghamarabb frissítenie kell.

#### <a name="hybrid-management-recommended-reading"></a>Hibrid kezelés – ajánlott olvasás

- [Azure AD Connect: automatikus frissítés](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Az Azure AD Application Proxy-összekötők ismertetése | Automatikus frissítések](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health riasztás alapterve

A szervezeteknek [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) kell központilag telepíteniük Azure AD Connect és AD FS monitorozásához és jelentéskészítéséhez. A Azure AD Connect és AD FS olyan kritikus fontosságú összetevők, amelyek képesek az életciklus-kezelés és a hitelesítés megszakítására, és ezért kimaradások elvégzéséhez vezethetnek. Azure AD Connect Health segít megfigyelni és betekintést nyerni a helyszíni identitás-infrastruktúrába, így biztosítva a környezet megbízhatóságát.

![Azure AD Connect Heath-architektúra](./media/active-directory-ops-guide/active-directory-ops-img16.png)

A környezet állapotának figyelése során azonnal meg kell felelnie a nagy súlyosságú riasztásoknak, majd az alacsonyabb súlyossági riasztásoknak.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health ajánlott olvasás

- [Az Azure AD Connect Health-ügynök telepítése](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Helyszíni ügynökök naplói

Néhány identitás-és hozzáférés-kezelési szolgáltatás a helyszíni ügynököket igényli a hibrid forgatókönyvek engedélyezéséhez. Ilyenek például a jelszó alaphelyzetbe állítása, az átmenő hitelesítés (PTA ESP), az Azure AD Application Proxy és az Azure AD MFA NPS-bővítmény. Fontos, hogy az operatív csapat alapkonfigurációja és az összetevők állapotának figyelése az összetevő-ügynök naplófájljainak archiválásával és elemzésével, például System Center Operations Manager vagy SIEM használatával. Ugyanilyen fontos, hogy az INFOSEC-műveleti csapat vagy az ügyfélszolgálat Ismerje meg a hibák elhárításának módját.

#### <a name="on-premises-agents-logs-recommended-reading"></a>A helyszíni ügynökök a javasolt olvasást naplózzák

- [Alkalmazásproxyval kapcsolatos hibaelhárítás](../manage-apps/application-proxy-troubleshoot.md)
- [Önkiszolgáló jelszó-visszaállítás hibaelhárítása – Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Az Azure AD Application Proxy-összekötők ismertetése](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: átmenő hitelesítés – problémamegoldás](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Az Azure AD MFA NPS-bővítményhez tartozó hibakódok megoldása](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Helyszíni ügynökök kezelése

Az ajánlott eljárások bevezetése segíthet a helyszíni ügynökök optimális működésében. Tekintse át az alábbi ajánlott eljárásokat:

- Több Azure AD alkalmazásproxy-összekötőt ajánlunk a zökkenőmentes terheléselosztás és a magas rendelkezésre állás érdekében azáltal, hogy a proxy alkalmazásokhoz való hozzáférés során elkerülhetők a meghibásodási pontok. Ha jelenleg csak egy összekötő található az éles üzemben lévő alkalmazásokat kezelő összekötő csoportban, legalább két összekötőt kell üzembe helyeznie a redundancia érdekében.
- Az alkalmazás-proxy összekötő csoport hibakeresési célokra való létrehozása és használata hasznos lehet a forgatókönyvek és az új helyszíni alkalmazások előkészítése során. Javasoljuk továbbá, hogy a hálózati eszközöket, például az üzenetsor-elemzőt és a hegedűst is telepítse az összekötő-gépeken.
- Több átmenő hitelesítési ügynök használata ajánlott a zökkenőmentes terheléselosztás és a magas rendelkezésre állás biztosításához azáltal, hogy a hitelesítési folyamat során nem kerül egyetlen meghibásodási pont. Ügyeljen arra, hogy legalább két átmenő hitelesítési ügynököt helyezzen üzembe a redundancia érdekében.

#### <a name="on-premises-agents-management-recommended-reading"></a>Helyszíni ügynökök felügyeletének ajánlott olvasata

- [Az Azure AD Application Proxy-összekötők ismertetése](../manage-apps/application-proxy-connectors.md)
- [Azure AD átmenő hitelesítés – gyors útmutató](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Felügyelet nagy léptékben

### <a name="identity-secure-score"></a>Identitásbiztonság pontszám

Az [identitás biztonságos pontszáma](./identity-secure-score.md) a szervezet biztonsági helyzetének számszerűsíthető mértékét biztosítja. Fontos, hogy folyamatosan áttekintse és foglalkozzon a jelentett eredményekkel, és törekedjen a lehető legmagasabb pontszámot. A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

![Biztonsági pontszám](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Ha a szervezete jelenleg nem rendelkezik olyan programmal, amely figyeli a változásokat az identitások biztonságos Pontszámában, javasoljuk, hogy hozzon létre egy csomagot, és rendeljen tulajdonosokat a figyeléshez és a javító műveletekhez. A szervezeteknek a lehető leghamarabb ki kell javítaniuk a fejlesztési műveleteket a 30-nál nagyobb pontszámot érintő hatásokkal.

### <a name="notifications"></a>Értesítések

A Microsoft e-mailes kommunikációt küld a rendszergazdáknak a szolgáltatás különböző változásainak, a szükséges konfigurációs frissítéseknek és a rendszergazdai beavatkozást igénylő hibáknak a bejelentéséhez. Fontos, hogy az ügyfelek az értesítő e-mail-címeket úgy állítsa be, hogy az értesítéseket a megfelelő csapattagok kapják meg, akik elfogadják és felhasználhatják az összes értesítést. Javasoljuk, hogy adjon hozzá több címzettet az [üzenetközpont](/office365/admin/manage/message-center) számára, és kérje meg, hogy a rendszer elküldje az értesítéseket (beleértve Azure ad Connect Health értesítéseket) egy terjesztési listához vagy egy megosztott postaládához. Ha egy e-mail-címmel rendelkező globális rendszergazdai fiókkal rendelkezik, mindenképpen legalább két, e-mail-kompatibilis fiókot kell konfigurálnia.

Az Azure AD két "feladó" címet használ: az <o365mc@email2.microsoft.com> üzenetsor-értesítéseket küldi, és a következőhöz <azure-noreply@microsoft.com> kapcsolódó értesítéseket küld:

- [Azure AD-hozzáférési felülvizsgálatok](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Vállalati alkalmazás lejárati tanúsítványának értesítései](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Vállalati alkalmazások kiépítési szolgáltatásával kapcsolatos értesítések

Az alábbi táblázatból megtudhatja, hogy milyen típusú értesítéseket küld, és hogy hol keresse meg őket:

| Értesítés forrása | Az Elküldések | Hol érdemes megnézni |
|:-|:-|:-|
| Technikai kapcsolattartó | Szinkronizálási hibák | Azure Portal – Tulajdonságok panel |
| Üzenetközpont | Az Identity Services és a Microsoft 365 háttérbeli szolgáltatások incidens-és romlási megjegyzései | Office-portál |
| Identity Protection heti kivonata | Identity Protection-kivonatoló | Azure AD Identity Protection panel |
| Azure AD Connect Health | Riasztási értesítések | Azure Portal – Azure AD Connect Health panel |
| Vállalati alkalmazások értesítései | Értesítések, amikor a tanúsítványok lejárnak és kiépítési hibák | Azure Portal – vállalati alkalmazás panel (minden alkalmazás saját e-mail-cím beállítással rendelkezik) |

#### <a name="notifications-recommended-reading"></a>Ajánlott olvasási értesítések

- [Megváltoztathatja a munkahely címeit, a technikai kapcsolattartást és egyebeket](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Működési felületi terület

### <a name="ad-fs-lockdown"></a>Zárolás AD FS

Szervezetek, amelyek az [Azure ad Smart zárolási](../authentication/concept-sspr-howitworks.md)szolgáltatásból közvetlenül az Azure ad-be való hitelesítéshez konfigurálják az alkalmazásokat. Ha AD FSt használ a Windows Server 2012 R2 rendszerben, implementálja AD FS [extranet zárolási védelmét](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Ha AD FSt használ a Windows Server 2016-es vagy újabb verziójára, implementálja az [extranetes intelligens zárolást](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Legalább azt javasoljuk, hogy engedélyezze az extranet zárolását a helyszíni Active Directory elleni találgatásos támadásokkal szemben. Ha azonban AD FS a Windows 2016-es vagy újabb verziójával, akkor engedélyeznie kell az extranetes intelligens zárolást is, amely segít enyhíteni a [jelszó-szórásos](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) támadásokat.

Ha AD FS csak az Azure AD-összevonás esetében használatos, néhány végpontot ki lehet kapcsolni a támadási felület minimalizálásához. Ha például AD FSt csak az Azure AD-hez használja, akkor a **usernamemixed** és a **windowstransport** számára engedélyezett végpontokon kívül le kell tiltania WS-Trust végpontokat.

### <a name="access-to-machines-with-on-premises-identity-components"></a>A helyszíni identitás-összetevőket tartalmazó gépekhez való hozzáférés

A szervezeteknek a helyszíni hibrid összetevőkkel megegyező módon kell lezárniuk a gépek hozzáférését a helyszíni tartományhoz. Például a biztonságimásolat-felelős vagy a Hyper-V-rendszergazda nem tud bejelentkezni a Azure AD Connect kiszolgálóra a szabályok módosításához.

A Active Directory felügyeleti réteg modellje úgy lett kialakítva, hogy a rendszer a környezet teljes vezérlése (0. réteg) és a támadók által gyakran feltört magas kockázatú munkaállomás-eszközök között puffer zónák használatával megvédje az identitási rendszereket. ![A többrétegű modell három rétegét bemutató ábra](./media/active-directory-ops-guide/active-directory-ops-img18.png)

A [réteg modell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) három szintből áll, és csak rendszergazdai fiókokat tartalmaz, nem általános jogú felhasználói fiókokat.

- **0. réteg** – A vállalati identitások közvetlen ellenőrzése a környezetben. A 0. réteg olyan fiókokat, csoportokat és más elemeket tartalmaz, amelyek közvetlen vagy közvetett ellenőrzéssel rendelkeznek az Active Directory-erdő, -tartomány vagy -tartományvezérlő, valamint a bennük található valamennyi erőforrás felett. A 0. rétegbeli erőforrások biztonsági érzékenysége egyenértékű, ugyanis gyakorlatilag egymás ellenőrzése alatt állnak.
- **1. réteg** – A vállalati kiszolgálók és alkalmazások ellenőrzése. Az 1. réteg erőforrásai közé a kiszolgálói operációs rendszerek, a felhőszolgáltatások és a vállalati alkalmazások tartoznak. Az 1. rétegbeli rendszergazdai fiókok az ezeken az erőforrásokon található jelentős mennyiségű üzleti érték felett gyakorolnak ellenőrzést. Általános példaként említhetjük erre a kiszolgálói rendszergazdák szerepkörét, akik ezeket az operációs rendszereket karbantartják, és az összes vállalati szolgáltatásra hatással lehetnek.
- **2. réteg** – A felhasználói munkaállomások és eszközök ellenőrzése. A 2. rétegbeli rendszergazdai fiókok a munkaállomásokon és eszközökön található jelentős mennyiségű üzleti érték felett gyakorolnak ellenőrzést. Példaként említhetők erre az ügyfélszolgálati és számítógép-támogatási rendszergazdák, hiszen ők szinte bármilyen felhasználói adat épségére hatással lehetnek.

A tartományvezérlők esetében ugyanúgy zárja be a helyszíni identitás-összetevők, például a Azure AD Connect, a AD FS és az SQL-szolgáltatások elérését.

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúrának hét aspektusa van. Ez a lista segít megtalálni azokat a műveleteket, amelyeket el kell végeznie a Azure Active Directory (Azure AD) műveleteinek optimalizálása érdekében.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- A helyszíni hibrid összetevők frissítési folyamatának automatizálása.
- Azure AD Connect Health üzembe helyezése Azure AD Connect és AD FS monitorozásához és jelentéskészítéséhez.
- A helyszíni hibrid összetevők állapotának monitorozása az összetevő-ügynök naplófájljainak archiválásával és elemzésével System Center Operations Manager vagy egy SIEM megoldás használatával.
- A biztonsági helyzeteket a személyazonosság biztonságos pontszámának mérésével valósíthatja meg.
- AD FS zárolása.
- A helyszíni identitás-összetevőkkel rendelkező gépekhez való hozzáférés zárolása.

## <a name="next-steps"></a>Következő lépések

Tekintse át az [Azure ad telepítési terveit](active-directory-deployment-plans.md) a nem telepített képességek megvalósítására vonatkozó részletekért.
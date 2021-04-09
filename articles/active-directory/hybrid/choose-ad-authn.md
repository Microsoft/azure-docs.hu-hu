---
title: Hitelesítés az Azure AD Hybrid Identity Solutions-megoldásokhoz
titleSuffix: Active Directory
description: Ez az útmutató segítséget nyújt az ügyvezetők, a informatikai igazgatók, a CISOs, a vezető identitás-építészek, a vállalati építészek és a biztonsági és informatikai döntéshozók számára, hogy az Azure AD Hybrid Identity megoldáshoz használt hitelesítési módszert használják közepes és nagy szervezetekben.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 15d62f40b50617fd1f6e543cb404a0d38361d3bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836495"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Válassza ki a megfelelő hitelesítési módszert a Azure Active Directory Hybrid Identity megoldáshoz

A megfelelő hitelesítési módszer kiválasztásával az alkalmazásait a felhőbe áthelyezni kívánó szervezeteknek az első szempontja. Ne tegye meg ezt a döntést a következő okok miatt:

1. Ez az első döntés egy olyan szervezet számára, amely át kíván térni a felhőbe.

2. A hitelesítési módszer a szervezet Felhőbeli jelenlétének kritikus eleme. Az összes Felhőbeli adattal és erőforrással való hozzáférést szabályozza.

3. Ez az Azure AD összes többi speciális biztonsági és felhasználói élményének alapja.

Az identitás az IT-biztonság új vezérlő síkja, így a hitelesítés egy szervezet hozzáférés-védelme az új felhő világába. A szervezeteknek olyan Identity Control-síkon kell rendelkezniük, amely megerősíti a biztonságot, és a támadók biztonságban tartja a Felhőbeli alkalmazásaikat.

> [!NOTE]
> A hitelesítési módszer megváltoztatásához tervezési, tesztelési és lehetséges állásidő szükséges. A [kétlépcsős](./how-to-connect-staged-rollout.md) bevezetéssel tesztelheti a felhasználókat az összevonásból a Felhőbeli hitelesítésbe való áttelepítéssel.

### <a name="out-of-scope"></a>Hatókörön kívül
Azok a szervezetek, amelyek nem rendelkeznek meglévő helyszíni címtár-lábnyommal, nem a jelen cikk középpontjában állnak. Ezek a vállalatok jellemzően csak a felhőben hoznak létre identitásokat, ami nem igényel hibrid identitási megoldást. A csak felhőalapú identitások kizárólag a felhőben találhatók, és nem kapcsolódnak a megfelelő helyszíni identitásokhoz.

## <a name="authentication-methods"></a>Hitelesítési módszerek
Ha az Azure AD Hybrid Identity megoldás az új vezérlő síkja, a hitelesítés a Felhőbeli hozzáférés alapja. A megfelelő hitelesítési módszer kiválasztása kulcsfontosságú első döntés az Azure AD Hybrid Identity megoldás létrehozásához. A Azure AD Connect használatával konfigurált hitelesítési módszer implementálása, amely a felhőben lévő felhasználókat is kiépíti.

A hitelesítési módszer kiválasztásához figyelembe kell vennie az időt, a meglévő infrastruktúrát, az összetettséget és a választott megvalósítás költségeit. Ezek a tényezők minden szervezet esetében eltérőek, és idővel változhatnak.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Az Azure AD a következő hitelesítési módszereket támogatja a hibrid identitási megoldásokhoz.

### <a name="cloud-authentication"></a>Felhőbeli hitelesítés
Ha ezt a hitelesítési módszert választja, az Azure AD kezeli a felhasználók bejelentkezési folyamatát. A zökkenőmentes egyszeri bejelentkezéssel (SSO) párosulva a felhasználók a hitelesítő adataik újbóli megadása nélkül is bejelentkezhetnek a Felhőbeli alkalmazásokba. A felhőalapú hitelesítés használatával két lehetőség közül választhat:

**Azure AD-jelszókivonat szinkronizálása**. A helyszíni címtár-objektumok hitelesítésének legegyszerűbb módja az Azure AD-ben. A felhasználók ugyanazt a felhasználónevet és jelszót használhatják, amelyet a helyszínen használnak anélkül, hogy további infrastruktúrát kellene üzembe helyeznie. Az Azure AD egyes prémium funkciói, például az Identity Protection és a [Azure ad Domain Services](../../active-directory-domain-services/tutorial-create-instance.md), jelszó-kivonatolási szinkronizálást igényelnek, függetlenül attól, hogy melyik hitelesítési módszert választja.

> [!NOTE]
> A jelszavak soha nem tiszta szövegben tárolódnak, vagy az Azure AD-ben egy reverzibilis algoritmussal titkosítva vannak. A jelszó-kivonatolási szinkronizálás tényleges folyamatával kapcsolatos további információkért lásd: [jelszó-kivonatolási szinkronizálás implementálása Azure ad Connect szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Az Azure ad átmenő hitelesítése**. Egyszerű jelszó-érvényesítést biztosít az Azure AD hitelesítési szolgáltatásokhoz egy vagy több helyszíni kiszolgálón futó szoftver-ügynök használatával. A kiszolgálók közvetlenül a helyszíni Active Directory érvényesítik a felhasználókat, így biztosítva, hogy a jelszó érvényesítése ne történjen meg a felhőben.

A vállalati felhasználói fiókok állapotának azonnali betartatására, a jelszóházirendek és a bejelentkezési órák használatára vonatkozó biztonsági követelményt biztosító vállalatok ezt a hitelesítési módszert használhatják. A tényleges átmenő hitelesítési folyamattal kapcsolatos további információkért lásd: [felhasználói bejelentkezés az Azure ad átmenő hitelesítéssel](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Összevont hitelesítés
Ha ezt a hitelesítési módszert választja, az Azure AD kikapcsolja a hitelesítési folyamatot egy különálló megbízható hitelesítési rendszerre, például a helyszíni Active Directory összevonási szolgáltatások (AD FS) (AD FS) a felhasználó jelszavának ellenőrzésére.

A hitelesítési rendszeren további speciális hitelesítési követelmények is megadhatók. Ilyenek például az intelligens kártya alapú hitelesítés vagy a külső féltől származó többtényezős hitelesítés. További információ: [Active Directory összevonási szolgáltatások (AD FS) üzembe helyezése](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

A következő szakasz segítségével eldöntheti, hogy melyik hitelesítési módszer a legmegfelelőbb a döntési fa használatával. Segít eldönteni, hogy az Azure AD Hybrid Identity megoldáshoz felhőalapú vagy összevont hitelesítést kíván-e üzembe helyezni.

## <a name="decision-tree"></a>Döntési fa

![Azure AD-hitelesítés döntési fája](./media/choose-ad-authn/azure-ad-authn-image1.png)

A döntéssel kapcsolatos kérdések részletei:

1. Az Azure AD a felhasználók bejelentkezését anélkül tudja kezelni, hogy helyszíni összetevőket kellene használni a jelszavak ellenőrzéséhez.
2. Az Azure AD szolgáltatással kikapcsolhatja a felhasználói bejelentkezést egy megbízható hitelesítési szolgáltatóra, például a Microsoft AD FSra.
3. Ha alkalmaznia kell, a felhasználói szintű Active Directory biztonsági házirendeket, például a fiók lejártát, a fiók lejáratát, a jelszó lejártát, a fiók zárolását, valamint a bejelentkezési órákat minden felhasználói bejelentkezéskor, az Azure AD-nek néhány helyszíni összetevőt kell használnia.
4. Az Azure AD nem támogatja natív módon a bejelentkezési funkciókat:
   * Bejelentkezés intelligens kártyák vagy tanúsítványok használatával.
   * Bejelentkezés a helyszíni MFA-kiszolgáló használatával.
   * Bejelentkezés harmadik féltől származó hitelesítési megoldás használatával.
   * Többhelyes helyszíni hitelesítési megoldás.
5. Azure AD Identity Protection jelszó-kivonatolási szinkronizálást igényel, függetlenül attól, hogy melyik bejelentkezési módszert választja ki, hogy a *felhasználók kiszivárgott hitelesítő adatokat tartalmazó* jelentést nyújtsanak. Ha az elsődleges bejelentkezési módszer meghiúsul, és a hiba esemény előtt lett konfigurálva, a szervezetek átadhatják a jelszó-kivonatolási szinkronizálást.

> [!NOTE]
> Azure AD Identity Protection [prémium szintű Azure ad P2](https://azure.microsoft.com/pricing/details/active-directory/) -licencek szükségesek.

## <a name="detailed-considerations"></a>Részletes megfontolások

### <a name="cloud-authentication-password-hash-synchronization"></a>Felhőbeli hitelesítés: jelszó-kivonat szinkronizálása

* **Erőfeszítés**. A jelszó-kivonatolási szinkronizáláshoz a legkevesebb erőfeszítést kell tenni az üzembe helyezés, a karbantartás és az infrastruktúra terén.  Ez az adatszint általában olyan szervezetekre vonatkozik, amelyeknek csak a felhasználóknak kell bejelentkezniük Microsoft 365, SaaS-alkalmazásokba és más Azure AD-alapú erőforrásokhoz. Ha be van kapcsolva, a jelszó-kivonat szinkronizálása a Azure AD Connect szinkronizálási folyamatának része, és két percenként fut.

* **Felhasználói élmény**. A felhasználók bejelentkezési élményének javításához helyezzen üzembe zökkenőmentes egyszeri bejelentkezést jelszó-kivonatolási szinkronizálással. Ha a felhasználók bejelentkeznek, a zökkenőmentes egyszeri bejelentkezés kiküszöböli a szükségtelen kéréseket.

* **Speciális forgatókönyvek**. Ha a szervezetek úgy döntenek, hogy az prémium szintű Azure AD P2-vel rendelkező Azure AD Identity Protection-jelentésekkel identitásokkal kapcsolatos bepillantásokat használhat. Ilyen például a kiszivárgott hitelesítő adatok jelentés. A Windows Hello for Business [speciális követelményeket támaszt a jelszó-kivonatolási szinkronizálás használatakor](/windows/access-protection/hello-for-business/hello-identity-verification). [Azure ad Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) jelszó-kivonatolási szinkronizálást igényel a felhasználók vállalati hitelesítő adatokkal való kiépítéséhez a felügyelt tartományban.

    Azok a szervezetek, amelyeken a többtényezős hitelesítés jelszó-kivonatolási szinkronizálást igényelnek, az Azure AD Multi-Factor Authentication vagy a [feltételes hozzáférés egyéni vezérlőit](../../active-directory/conditional-access/controls.md#custom-controls-preview)kell használniuk Ezek a szervezetek nem használhatnak olyan harmadik féltől származó vagy helyszíni többtényezős hitelesítési módszereket, amelyek az összevonásra támaszkodnak.

> [!NOTE]
> Az Azure AD feltételes hozzáféréséhez [prémium szintű Azure ad P1](https://azure.microsoft.com/pricing/details/active-directory/) licenc szükséges.

* Az **üzletmenet folytonossága**. A jelszó-kivonatoló szinkronizálás Felhőbeli hitelesítéssel való használata olyan felhőalapú szolgáltatásként érhető el, amely az összes Microsoft-adatközpontra méretezhető. Annak érdekében, hogy a jelszó-kivonat szinkronizálása ne menjen le hosszabb ideig, helyezzen üzembe egy második Azure AD Connect kiszolgálót átmeneti módban egy készenléti konfigurációban.

* **Megfontolások**. A jelszó-kivonatolási szinkronizálás jelenleg nem kényszeríti ki azonnal a helyi fiók állapotának módosításait. Ebben az esetben a felhasználók csak akkor férhetnek hozzá a felhőalapú alkalmazásokhoz, ha a felhasználói fiók állapota szinkronizálva lett az Azure AD-val. Előfordulhat, hogy a szervezetek egy új szinkronizálási ciklus futtatásával szeretnék legyőzni ezt a korlátozást, miután a rendszergazdák tömeges frissítést végeznek a helyszíni felhasználói fiókok állapotára. Ilyen például a fiókok letiltása.

> [!NOTE]
> A jelszó lejárt, és a fiókhoz tartozó kizárt állapotok jelenleg nem szinkronizálhatók az Azure AD-val Azure AD Connect. Amikor megváltoztatja a felhasználó jelszavát, és beállítja, hogy a *felhasználónak a következő bejelentkezési jelzőn kell megváltoztatnia a jelszót* , a jelszó-kivonat nem lesz szinkronizálva az Azure ad-val Azure ad Connect, amíg a felhasználó nem módosítja a jelszavát.

Tekintse át a [jelszó-kivonatok szinkronizálásának megvalósítása](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) című témakört.

### <a name="cloud-authentication-pass-through-authentication"></a>Felhőalapú hitelesítés: átmenő hitelesítés  

* **Erőfeszítés**. Az átmenő hitelesítéshez egy vagy több (három) egyszerűsített ügynököt kell telepíteni a meglévő kiszolgálókon. Ezeknek az ügynököknek hozzáféréssel kell rendelkezniük a helyszíni Active Directory Domain Servicesokhoz, beleértve a helyszíni AD-tartományvezérlőket is. Kimenő hozzáférésre van szükségük az internethez és a tartományvezérlőkhöz való hozzáféréshez. Emiatt nem támogatott az ügynökök üzembe helyezése peremhálózati hálózatban.

    Az átmenő hitelesítéshez a tartományvezérlők számára nem korlátozott hálózati hozzáférés szükséges. Minden hálózati forgalom titkosítva van, és a hitelesítési kérelmekre korlátozódik. A folyamattal kapcsolatos további információkért tekintse meg az átmenő hitelesítés [biztonsági](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) részletes ismertetését.

* **Felhasználói élmény**. A felhasználók bejelentkezési élményének javítása érdekében zökkenőmentes SSO-t helyezzen üzembe átmenő hitelesítéssel. Ha a felhasználók bejelentkeznek, a zökkenőmentes egyszeri bejelentkezés kiküszöböli a szükségtelen kéréseket.

* **Speciális forgatókönyvek**. Az átmenő hitelesítés a bejelentkezés időpontjában kényszeríti a helyszíni fiók házirendjét. Például a hozzáférés megtagadva, ha egy helyszíni felhasználó fiókjának állapota le van tiltva, ki van zárva, vagy a [jelszavuk lejár](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) , vagy a bejelentkezési kísérlet az órákon kívül esik, amikor a felhasználó bejelentkezhet.

    A többtényezős hitelesítést az átmenő hitelesítéssel igénylő szervezeteknek az Azure AD Multi-Factor Authentication (MFA) vagy a [feltételes hozzáférés egyéni vezérlőit](../../active-directory/conditional-access/controls.md#custom-controls-preview)kell használniuk. Ezek a szervezetek nem használhatnak olyan harmadik féltől származó vagy helyszíni többtényezős hitelesítési módszert, amely az összevonásra támaszkodik. A speciális funkciók megkövetelik, hogy a jelszó-kivonat szinkronizálása telepítve legyen, függetlenül attól, hogy az áteresztő hitelesítést választja-e. Ilyen például az Identity Protection kiszivárgott hitelesítő adatairól szóló jelentés.

* Az **üzletmenet folytonossága**. Javasoljuk, hogy két további áteresztő hitelesítési ügynököt helyezzen üzembe. Ezek az Extrák a Azure AD Connect-kiszolgáló első ügynökén kívül vannak. Ez a további üzemelő példány biztosítja a hitelesítési kérések magas rendelkezésre állását. Ha három ügynök van telepítve, az egyik ügynök továbbra is meghiúsulhat, ha egy másik ügynök karbantartás alatt áll.

    További előnyt jelent a jelszó-kivonatolási szinkronizálás üzembe helyezése az átmenő hitelesítés mellett. Biztonsági mentési hitelesítési módszerként működik, ha az elsődleges hitelesítési módszer már nem érhető el.

* **Megfontolások**. A jelszó-kivonatoló szinkronizálást biztonsági mentési hitelesítési módszerként használhatja az átmenő hitelesítéshez, ha az ügynökök nem tudják érvényesíteni a felhasználó hitelesítő adatait egy jelentős helyszíni hiba miatt. A jelszó-kivonat szinkronizálása nem történik meg automatikusan, és a Azure AD Connect használatával manuálisan kell átváltania a bejelentkezési módszert.

    Az átmenő hitelesítéssel kapcsolatos egyéb megfontolásokért, beleértve az alternatív AZONOSÍTÓk támogatását is, tekintse meg a [gyakran ismételt kérdéseket](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Tekintse át az [átmenő hitelesítés implementálása](../../active-directory/hybrid/how-to-connect-pta.md) az üzembe helyezés lépésein című témakört.

### <a name="federated-authentication"></a>Összevont hitelesítés

* **Erőfeszítés**. Az összevont hitelesítési rendszer külső megbízható rendszerre támaszkodik a felhasználók hitelesítéséhez. Néhány vállalat újra szeretné használni a meglévő összevont rendszerbefektetést az Azure AD Hybrid Identity megoldásával. Az összevont rendszerek karbantartása és kezelése az Azure AD-n kívül esik. A szervezet számára az összevont rendszer használatával gondoskodni kell arról, hogy biztonságos módon legyen üzembe helyezhető, és képes legyen kezelni a hitelesítési terhelést.

* **Felhasználói élmény**. Az összevont hitelesítés felhasználói élménye az összevonási Farm funkcióinak, topológiájának és konfigurációjának megvalósításán múlik. Egyes szervezeteknek erre a rugalmasságra van szükségük az összevonási farmhoz való hozzáféréshez és a biztonsági követelmények kielégítéséhez. Beállíthatja például, hogy a belsőleg csatlakoztatott felhasználók és eszközök automatikusan jelentkezzenek be a felhasználókba anélkül, hogy hitelesítő adatokat kelljen megadniuk. Ez a konfiguráció azért működik, mert már be van jelentkezve az eszközeibe. Ha szükséges, néhány speciális biztonsági funkció nehezebbé teszi a felhasználók bejelentkezési folyamatát.

* **Speciális forgatókönyvek**. Összevont hitelesítési megoldásra akkor van szükség, ha az ügyfeleknek olyan hitelesítési követelményük van, amelyet az Azure AD natív módon nem támogat. [A megfelelő bejelentkezési lehetőség kiválasztásához](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365)tekintse meg a részletes információkat. Vegye figyelembe a következő gyakori követelményeket:

  * Intelligens kártyák vagy tanúsítványok használatát igénylő hitelesítés.
  * A helyszíni MFA-kiszolgálók vagy az összevont identitás-szolgáltatót igénylő külső többtényezős szolgáltatók.
  * Hitelesítés harmadik féltől származó hitelesítési megoldások használatával. Tekintse meg az [Azure ad-összevonás kompatibilitási listáját](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Jelentkezzen be, amelyhez sAMAccountName szükséges, például: tartomány \ Felhasználónév, egyszerű felhasználónév (UPN) helyett például: user@domain.com .

* Az **üzletmenet folytonossága**. Az összevont rendszerek általában a kiszolgálók terheléses, farmként ismert tömbjét igénylik. Ez a farm belső hálózati és peremhálózati topológiában van konfigurálva a hitelesítési kérések magas rendelkezésre állásának biztosítása érdekében.

    Ha az elsődleges hitelesítési módszer már nem érhető el, a jelszó-kivonatolási szinkronizálást és az összevont hitelesítést is üzembe helyezheti biztonsági mentési hitelesítési módszerként. Ilyen eset például, ha a helyszíni kiszolgálók nem érhetők el. Egyes nagyméretű nagyvállalati szervezetek olyan összevonási megoldást igényelnek, amely támogatja a Geo-DNS-sel konfigurált több internetes beléptetési pont használatát kis késleltetésű hitelesítési kérelmek esetén.

* **Megfontolások**. Az összevont rendszerek jellemzően nagyobb mértékű befektetést igényelnek a helyszíni infrastruktúrában. A legtöbb szervezet ezt a lehetőséget választja, ha már van helyszíni összevonási beruházása. Ha pedig erős üzleti követelmény, hogy egyetlen identitású szolgáltatót használjon. Az összevonás bonyolultabb a felhőalapú hitelesítési megoldásokkal összehasonlítva a működéshez és a hibakereséshez.

Olyan nem átirányítható tartományhoz, amely nem ellenőrizhető az Azure AD-ben, további konfigurációra van szükség a felhasználói azonosító bejelentkezésének megvalósításához. Ezt a követelményt másodlagos bejelentkezési AZONOSÍTÓnak is nevezzük. Lásd: [alternatív bejelentkezési azonosító konfigurálása](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) a korlátozásokhoz és a követelményekhez. Ha úgy dönt, hogy egy külső féltől származó multi-Factor Authentication szolgáltatót használ összevonással, győződjön meg arról, hogy a szolgáltató támogatja a WS-Trust, hogy az eszközök csatlakozzanak az Azure AD-hoz.

Tekintse át az [összevonási kiszolgálók](/windows-server/identity/ad-fs/deployment/deploying-federation-servers) központi telepítésének lépéseit ismertető témakört.

> [!NOTE]
> Az Azure AD Hybrid Identity megoldás üzembe helyezésekor a Azure AD Connect támogatott topológiáinak egyikét kell megvalósítani. További információ a támogatott és nem támogatott konfigurációkról [Azure ad Connect-topológiák esetében](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Architektúra-diagramok

Az alábbi ábrák az Azure AD Hybrid Identity megoldással használható, az egyes hitelesítési módszerekhez szükséges magas szintű architektúrák összetevőit vázolják fel. Áttekintést nyújtanak a megoldások közötti különbségek összehasonlításához.

* Jelszó-kivonatoló szinkronizációs megoldás egyszerűsége:

    ![Azure AD Hybrid Identity jelszó-kivonatoló szinkronizálással](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Az átmenő hitelesítés ügynöki követelményei, két ügynököt használva a redundancia érdekében:

    ![Azure AD hibrid identitás átmenő hitelesítéssel](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Az Ön és a szervezet belső hálózata közötti összevonáshoz szükséges összetevők:

    ![Azure AD Hybrid Identity összevont hitelesítéssel](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Módszerek összehasonlítása

|Megfontolandó|Jelszó-kivonat szinkronizálása + zökkenőmentes SSO|Átmenő hitelesítés + zökkenőmentes egyszeri bejelentkezés|Összevonás az AD FS rendszerrel|
|:-----|:-----|:-----|:-----|
|Hol történik a hitelesítés?|A felhőben|A felhőben a biztonságos jelszó-ellenőrzési csere után a helyszíni hitelesítési ügynökkel|Helyszíni|
|A helyszíni kiszolgálóra vonatkozó követelmények a kiépítési rendszeren túl: Azure AD Connect?|Nincsenek|Egy kiszolgáló minden további hitelesítési ügynökhöz|Két vagy több AD FS-kiszolgáló<br><br>Két vagy több WAP-kiszolgáló a peremhálózati/DMZ-hálózaton|
|Milyen követelmények vonatkoznak a helyszíni internetre és a hálózatkezelésre a kiépítési rendszeren túl?|Nincsenek|[Kimenő internet-hozzáférés](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) a hitelesítési ügynököket futtató kiszolgálókról|[Bejövő internetes hozzáférés](/windows-server/identity/ad-fs/overview/ad-fs-requirements) a peremhálózati WAP-kiszolgálókhoz<br><br>Bejövő hálózati hozzáférés AD FS kiszolgálókhoz a peremhálózati WAP-kiszolgálókról<br><br>Hálózati terheléselosztás|
|Van TLS/SSL-tanúsítványra vonatkozó követelmény?|Nem|Nem|Igen|
|Van állapot-figyelési megoldás?|Nem szükséges|A [Azure Active Directory felügyeleti központ](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md) által megadott ügynök állapota|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|A felhasználók egyszeri bejelentkezést kapnak a felhőalapú erőforrásokhoz a vállalati hálózaton belüli tartományhoz csatlakoztatott eszközökről?|Igen, [zökkenőmentes egyszeri bejelentkezéssel](../../active-directory/hybrid/how-to-connect-sso.md)|Igen, [zökkenőmentes egyszeri bejelentkezéssel](../../active-directory/hybrid/how-to-connect-sso.md)|Yes|
|Milyen típusú bejelentkezési típusok támogatottak?|UserPrincipalName + jelszó<br><br>Windows-Integrated hitelesítés [zökkenőmentes SSO](../../active-directory/hybrid/how-to-connect-sso.md) használatával<br><br>[Másodlagos bejelentkezési azonosító](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + jelszó<br><br>Windows-Integrated hitelesítés [zökkenőmentes SSO](../../active-directory/hybrid/how-to-connect-sso.md) használatával<br><br>[Másodlagos bejelentkezési azonosító](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + jelszó<br><br>sAMAccountName + jelszó<br><br>Windows-Integrated hitelesítés<br><br>[Tanúsítvány-és intelligens kártyás hitelesítés](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Másodlagos bejelentkezési azonosító](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Támogatott a vállalati Windows Hello?|[Kulcs megbízhatósági modellje](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Kulcs megbízhatósági modellje](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*A Windows Server 2016-tartomány működési szintjét igényli*|[Kulcs megbízhatósági modellje](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Tanúsítvány megbízhatósági modellje](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Mik a többtényezős hitelesítési lehetőségek?|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Feltételes hozzáféréssel rendelkező egyéni vezérlők *](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Feltételes hozzáféréssel rendelkező egyéni vezérlők *](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Azure MFA-kiszolgáló](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[Harmadik féltől származó MFA](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Feltételes hozzáféréssel rendelkező egyéni vezérlők *](../../active-directory/conditional-access/controls.md)|
|Milyen felhasználói fiókok támogatottak?|Letiltott fiókok<br>(legfeljebb 30 perces késleltetés)|Letiltott fiókok<br><br>Fiók kizárva<br><br>A fiók lejárt<br><br>A jelszó lejárt<br><br>Bejelentkezési idő|Letiltott fiókok<br><br>Fiók kizárva<br><br>A fiók lejárt<br><br>A jelszó lejárt<br><br>Bejelentkezési idő|
|Mik a feltételes hozzáférési lehetőségek?|[Azure AD feltételes hozzáférés prémium szintű Azure AD](../../active-directory/conditional-access/overview.md)|[Azure AD feltételes hozzáférés prémium szintű Azure AD](../../active-directory/conditional-access/overview.md)|[Azure AD feltételes hozzáférés prémium szintű Azure AD](../../active-directory/conditional-access/overview.md)<br><br>[AD FS jogcím szabályai](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|A örökölt protokollok blokkolása támogatott?|[Igen](../../active-directory/conditional-access/overview.md)|[Igen](../../active-directory/conditional-access/overview.md)|[Igen](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Testre szabhatja az emblémát, a képet és a leírást a bejelentkezési lapokon?|[Igen, prémium szintű Azure AD](../../active-directory/fundamentals/customize-branding.md)|[Igen, prémium szintű Azure AD](../../active-directory/fundamentals/customize-branding.md)|[Igen](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Milyen speciális forgatókönyvek támogatottak?|[Intelligens jelszó zárolása](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Kiszivárgott hitelesítő adatok jelentései prémium szintű Azure AD P2-vel](../identity-protection/overview-identity-protection.md)|[Intelligens jelszó zárolása](../../active-directory/authentication/howto-password-smart-lockout.md)|Többhelyes, kis késleltetésű hitelesítési rendszerek<br><br>[Extranetes zárolás AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integráció harmadik féltől származó személyazonossági rendszerekkel](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Az Azure AD feltételes hozzáférésben az egyéni vezérlők jelenleg nem támogatják az eszközök regisztrálását.

## <a name="recommendations"></a>Javaslatok
Az Identity rendszer biztosítja, hogy a felhasználók hozzáférjenek a felhőalapú alkalmazásokhoz, valamint az áttelepített és a felhőben elérhetővé tenni kívánt üzletági alkalmazásokhoz. Annak érdekében, hogy a jogosult felhasználók a szervezet bizalmas adataiból származó, produktív és rosszul működő felhasználókat is megőrizzen, a hitelesítés szabályozza az alkalmazásokhoz való hozzáférést.

A jelszó-kivonatolási szinkronizálást a választott hitelesítési módszerhez használhatja vagy engedélyezheti a következő okok miatt:

1. **Magas rendelkezésre állás és vész-helyreállítás**. Az átmenő hitelesítés és az összevonás a helyszíni infrastruktúrára támaszkodik. Az átmenő hitelesítéshez a helyszíni lábnyom magában foglalja a kiszolgálói hardvert és az átmenő hitelesítési ügynökök hálózatának megkövetelését. Az összevonás esetében a helyszíni lábnyom még nagyobb. A peremhálózaton lévő kiszolgálókat a proxy hitelesítési kéréseinek és a belső összevonási kiszolgálóknak kell megadniuk.

    Az egyes meghibásodási pontok elkerülése érdekében helyezzen üzembe redundáns kiszolgálókat. A hitelesítési kérelmeket a rendszer mindig szervizeli, ha valamelyik összetevő meghibásodik. Az átmenő hitelesítés és az összevonás is arra támaszkodik, hogy a tartományvezérlők válaszolnak a hitelesítési kérelmekre, ami szintén sikertelen lehet. Ezeknek az összetevőknek sok karbantartásra van szükségük, hogy egészségesek maradjanak. Az kimaradások nagyobb valószínűséggel működnek, ha a karbantartás nem tervezett és nem megfelelően van megvalósítva. A jelszó-kivonatoló szinkronizálás használatával elkerülhetők a leállások, mivel a Microsoft Azure AD felhőalapú hitelesítési szolgáltatás globálisan méretezhető, és mindig elérhető.

2. **A helyszíni leállás túlélése**.  Egy, a Cyber-támadás vagy a katasztrófa miatti helyszíni kimaradás következményei jelentősek lehetnek, a megbénult szervezettől kezdve a támadók nem tudják kezelni a támadásokat. A közelmúltban számos szervezet volt a kártevők elleni támadás, beleértve a megtalált ransomware is, ami miatt a helyszíni kiszolgálók leállnak. Ha a Microsoft segítséget nyújt az ügyfeleknek az ilyen típusú támadások kezelésében, két szervezetet lát:

   * Azok a szervezetek, amelyek korábban is bekapcsolták az összevont vagy átmenő hitelesítéshez használt jelszó-kivonatolási szinkronizálást, megváltoztatták elsődleges hitelesítési módszereiket, hogy a jelszó-kivonatolási szinkronizálást használják. Egy órán belül ismét online állapotba kerültek. A Microsoft 365 használatával történő e-mailekhez való hozzáférés révén a problémák megoldásához és más felhőalapú számítási feladatokhoz való hozzáféréshez is kapcsolódott.

   * Azok a szervezetek, amelyeknek korábban nem engedélyezték a jelszó-kivonatolási szinkronizálást, nem megbízható külső fogyasztói e-mail-rendszerekre kellett volna rendelkezniük a problémák megoldásához. Ezekben az esetekben hetekig tartották a helyszíni identitás-infrastruktúrát, mielőtt a felhasználók újra bejelentkeznek a felhőalapú alkalmazásokba.

3. **Identity Protection**. A Felhőbeli felhasználók védelmének egyik legjobb módja a prémium szintű Azure AD P2 Azure AD Identity Protection. A Microsoft folyamatosan ellenőrzi az interneten a felhasználók és a jelszavak listáját, hogy a rossz szereplőkkel árulnak és elérhetővé teszik a Dark web-t. Az Azure AD ezeket az információkat használva ellenőrizheti, hogy a szervezetben lévő felhasználónevek és jelszavak biztonsága sérült-e. Ezért fontos a jelszó-kivonat szinkronizálásának engedélyezése, függetlenül attól, hogy melyik hitelesítési módszert használja, legyen az összevont vagy átmenő hitelesítés. A kiszivárgott hitelesítő adatok jelentésként jelennek meg. Ezekkel az adatokkal blokkolhatja vagy kényszerítheti a felhasználókat a jelszavuk módosítására, amikor megpróbálnak bejelentkezni a kiszivárgott jelszavakkal.

## <a name="conclusion"></a>Összegzés

Ez a cikk a felhőalapú alkalmazásokhoz való hozzáférés támogatásához a szervezetek által konfigurálható és telepíthető különböző hitelesítési lehetőségeket ismerteti. A különböző üzleti, biztonsági és technikai követelmények teljesítése érdekében a szervezetek választhatnak a jelszó-kivonat szinkronizálása, az átmenő hitelesítés és az összevonás között.

Vegye figyelembe az egyes hitelesítési módszereket. A megoldás üzembe helyezésének erőfeszítése, valamint a bejelentkezési folyamat felhasználói tapasztalata az üzleti igények kielégítése érdekében? Értékelje ki, hogy a szervezetnek szüksége van-e az egyes hitelesítési módszerek speciális forgatókönyvekre és üzletmenet-folytonossági szolgáltatásaira. Végezetül értékelje ki az egyes hitelesítési módszerek szempontjait. Ezek közül bármelyiket meggátolja az Ön döntésének megvalósításában?

## <a name="next-steps"></a>Következő lépések

Napjaink világában a fenyegetések napi 24 órában jelennek meg, és bárhonnan érkeznek. Implementálja a megfelelő hitelesítési módszert, és csökkenti a biztonsági kockázatokat, és gondoskodik az identitások védelméről.

[Ismerkedjen](../fundamentals/active-directory-whatis.md) meg az Azure ad-vel, és telepítse a megfelelő hitelesítési megoldást a szervezet számára.

Ha úgy gondolja, hogy összevontról felhőbe történő áttelepítést végez, további információ [a bejelentkezési módszer módosításáról](../../active-directory/hybrid/plan-connect-user-signin.md). Az áttelepítés megtervezéséhez és megvalósításához használja ezeket a projekt-telepítési terveket, vagy vegye fontolóra az új [szakaszos](../../active-directory/hybrid/how-to-connect-staged-rollout.md) [bevezetési](../fundamentals/active-directory-deployment-plans.md) funkciót, amellyel áttelepítheti az összevont felhasználókat a felhőalapú hitelesítés használatával egy szakaszos megközelítésben.
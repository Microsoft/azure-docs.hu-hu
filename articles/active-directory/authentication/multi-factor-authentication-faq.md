---
title: Azure AD Multi-Factor Authentication GYIK – Azure Active Directory
description: Az Azure AD Multi-Factor Authentication kapcsolatos gyakori kérdések és válaszok.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58fbf874339bf287ce1af62e1edb39ec6227d99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257102"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Gyakran ismételt kérdések az Azure AD-vel kapcsolatban Multi-Factor Authentication

Ez a gyakori kérdések az Azure AD Multi-Factor Authentication és az Multi-Factor Authentication szolgáltatás használatával kapcsolatos gyakori kérdésekre adnak választ. A szolgáltatással kapcsolatos kérdésekre általánosságban, számlázási modellekkel, felhasználói élményekkel és hibaelhárítással foglalkozunk.

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. Azok a felhasználók, akik a többtényezős hitelesítést szeretnék megkövetelni a felhasználóknak, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk. Azok a meglévő ügyfelek, akik aktiválták az MFA-kiszolgálót a július 1. előtt, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.
>
> Az alábbi, az Azure Multi-Factor Authentication-kiszolgálót érintő információ csak azokra a felhasználókra vonatkozik, akik már futtatták az MFA-kiszolgálót.
>
> A fogyasztáson alapuló licencelés az új ügyfelek számára már nem érhető el, 2018. szeptember 1-től érvényes.
> Az új hitelesítési szolgáltatók már nem hozhatók létre. szeptember 1., 2018. A meglévő hitelesítési szolgáltatók továbbra is használhatók és frissíthetők. A többtényezős hitelesítés továbbra is elérhető lesz prémium szintű Azure AD licencekben.

## <a name="general"></a>Általános kérdések

* [Hogyan kezeli az Azure Multi-Factor Authentication-kiszolgáló a felhasználói adatkezelést?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Milyen SMS rövid kódokat használ SMS-üzenetek küldésére a felhasználóknak?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Hogyan kezeli az Azure Multi-Factor Authentication-kiszolgáló a felhasználói adatkezelést?

A Multi-Factor Authentication-kiszolgáló a felhasználói adatszolgáltatások csak a helyszíni kiszolgálókon tárolódnak. A felhőben nincsenek állandó felhasználói adatok. Ha a felhasználó kétlépéses ellenőrzést hajt végre, Multi-Factor Authentication-kiszolgáló adatokat küld az Azure AD Multi-Factor Authentication Cloud Service-be a hitelesítéshez. A Multi-Factor Authentication-kiszolgáló és a Multi-Factor Authentication Cloud Service közötti kommunikáció SSL (SSL) vagy Transport Layer Security (TLS) protokollt használ a kimenő 443-as porton keresztül.

Ha hitelesítési kérelmeket küld a Cloud Service-nek, a rendszer adatokat gyűjt a hitelesítési és használati jelentésekhez. A kétlépéses ellenőrzési naplók a következő adatmezőket tartalmazzák:

* **Egyedi azonosító** (vagy Felhasználónév vagy helyszíni multi-Factor Authentication-kiszolgáló azonosító)
* Keresztnév **és vezetéknév** (nem kötelező)
* **E-mail-cím** (nem kötelező)
* **Telefonszám** (hanghívás vagy SMS-hitelesítés használatakor)
* **Eszköz jogkivonata** (a Mobile App Authentication használatakor)
* **Hitelesítési mód**
* **Hitelesítés eredménye**
* **Multi-Factor Authentication-kiszolgáló neve**
* **Multi-Factor Authentication-kiszolgáló IP-cím**
* **Ügyfél IP-címe** (ha elérhető)

A nem kötelező mezők konfigurálhatók Multi-Factor Authentication-kiszolgálóban.

Az ellenőrzés eredményét (sikeres vagy elutasítás) és a Megtagadás okát a hitelesítő adatként tárolja a rendszer. Ezek az adatok a hitelesítési és használati jelentésekben érhetők el.

További információ: az [adattárolási és ügyféladatok az Azure AD multi-Factor Authentication](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Milyen SMS rövid kódokat használ SMS-üzenetek küldésére a felhasználóknak?

A Egyesült Államok a következő SMS rövid kódokat használjuk:

* *97671*
* *69829*
* *51789*
* *99399*

Kanadában a következő SMS rövid kódokat használjuk:

* *759731*
* *673801*

A konzisztens SMS-és hangalapú Multi-Factor Authentication-küldési kérések nem garantálják ugyanazt a számot. A felhasználók érdekében bármikor hozzáadhatjuk vagy eltávolíthatjuk a rövid kódokat, ahogy az útvonal-kiigazításokat az SMS-kézbesítés javítására is felhasználjuk.

A Egyesült Államok és Kanadában kívüli országokban és régiókban nem támogatjuk a rövid kódokat.

## <a name="billing"></a>Számlázás

A legtöbb számlázási kérdés megválaszolható a [multi-Factor Authentication díjszabási oldalára](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) vagy az [Azure ad multi-Factor Authentication-verziókra és-fogyasztási csomagokra](concept-mfa-licensing.md)vonatkozó dokumentációra hivatkozva.

* [Felszámítanak a szervezetem a hitelesítéshez használt telefonhívások és SMS-üzenetek küldésére?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Díjköteles a felhasználónkénti számlázási modell az összes engedélyezett felhasználónál, vagy csak a kétlépéses ellenőrzést végzők?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Hogyan működik Multi-Factor Authentication számlázás?](#how-does-multi-factor-authentication-billing-work)
* [Létezik az Azure AD Multi-Factor Authentication ingyenes verziója?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [A szervezetem bármikor válthat a felhasználónkénti és a felhasználónkénti számlázási modellek között?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Válthatok-e a szervezetem a fogyasztáson alapuló számlázási és előfizetések (a licenc alapú modell) között bármikor?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [A szervezetem az identitások használatát és szinkronizálását kell használnia az Azure AD Multi-Factor Authentication használatához?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Felszámítanak a szervezetem a hitelesítéshez használt telefonhívások és SMS-üzenetek küldésére?

Nem, nem számítunk fel díjat a felhasználóknak az Azure AD-Multi-Factor Authenticationon keresztül küldött vagy szöveges üzenetek küldésére. Ha hitelesítési MFA-szolgáltatót használ, minden egyes hitelesítés után a használt módszerhez nem kell fizetnie.

A felhasználók a személyes telefonos szolgáltatásuk alapján felmerülhetnek a telefonhívások vagy a kapott szöveges üzenetekért.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Díjköteles a felhasználónkénti számlázási modell az összes engedélyezett felhasználónál, vagy csak a kétlépéses ellenőrzést végzők?

A számlázás a Multi-Factor Authentication használatára konfigurált felhasználók számától függ, függetlenül attól, hogy az adott hónapban kétlépéses ellenőrzést hajtottak végre.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Hogyan működik Multi-Factor Authentication számlázás?

Felhasználónkénti vagy hitelesítési MFA-szolgáltató létrehozásakor a szervezet Azure-előfizetését havi használat alapján számítjuk fel. Ez a számlázási modell hasonló ahhoz, hogy az Azure hogyan számlázza a virtuális gépek és a Web Apps használatát.

Ha Azure AD-Multi-Factor Authentication-előfizetést vásárol, a szervezet csak az egyes felhasználókra vonatkozó éves licenc díját fizeti. Az MFA-licencek és Microsoft 365, prémium szintű Azure AD vagy Enterprise Mobility + Security csomagok számlázása így történik.

További információ: [Az Azure ad-multi-Factor Authentication beszerzése](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Létezik az Azure AD Multi-Factor Authentication ingyenes verziója?

A biztonsági beállítások a ingyenes Azure AD szinten engedélyezhetők. A biztonsági beállításokkal minden felhasználó számára engedélyezett a többtényezős hitelesítés a Microsoft Authenticator alkalmazás használatával. A szöveges üzenet vagy a telefonos ellenőrzés nem használható biztonsági alapértékekkel, csak a Microsoft Authenticator alkalmazással.

További információ: [Mi a biztonsági alapértékek?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>A szervezetem bármikor válthat a felhasználónkénti és a felhasználónkénti számlázási modellek között?

Ha a szervezete az MFA-t önálló szolgáltatásként vásárolja meg a fogyasztáson alapuló számlázást, akkor az MFA-szolgáltató létrehozásakor kiválaszthat egy számlázási modellt. Az MFA-szolgáltató létrehozása után nem módosítható a számlázási modell. 

Ha az MFA-szolgáltató *nincs Azure* ad-bérlőhöz csatolva, vagy az új MFA-szolgáltatót egy másik Azure ad-bérlőhöz, a felhasználói beállításokhoz és a konfigurációs beállításokhoz kapcsolja. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

További információ az MFA-szolgáltatókról az [Azure multi-Factor Auth-szolgáltató első lépéseiben](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Válthatok-e a szervezetem a fogyasztáson alapuló számlázási és előfizetések (a licenc alapú modell) között bármikor?

Bizonyos esetekben igen.

Ha a címtár *felhasználónkénti* Azure multi-Factor Authentication szolgáltatóval rendelkezik, MFA-licenceket adhat hozzá. A licenccel rendelkező felhasználók nem számítanak bele a felhasználónkénti felhasználás szerinti Számlázásba. A licenccel nem rendelkező felhasználók továbbra is engedélyezhetők az MFA-szolgáltatón keresztül. Ha a Multi-Factor Authentication használatára konfigurált összes felhasználóhoz vásárol és rendel hozzá licenceket, akkor törölheti az Azure Multi-Factor Authentication-szolgáltatót. Ha a jövőben több felhasználóval rendelkezik, akkor mindig létrehozhat egy másik felhasználónkénti MFA-szolgáltatót.

Ha a címtárban van egy hitelesítési Azure Multi-Factor Authentication *-* szolgáltató, akkor mindig minden hitelesítés után kell fizetnie, ha az MFA-szolgáltató az előfizetéshez van társítva. Az MFA-licenceket hozzárendelheti a felhasználókhoz, de minden kétlépéses ellenőrzési kérelem esetében továbbra is díjat számítunk fel, függetlenül attól, hogy a hozzárendelt MFA-licenccel rendelkező valakitől származik-e.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>A szervezetem az identitások használatát és szinkronizálását kell használnia az Azure AD Multi-Factor Authentication használatához?

Ha a szervezet egy fogyasztáson alapuló számlázási modellt használ, Azure Active Directory megadása nem kötelező, de nem kötelező. Ha az MFA-szolgáltató nincs Azure AD-bérlőhöz társítva, akkor csak a helyszíni Azure Multi-Factor Authentication-kiszolgáló telepíthető.

Azure Active Directory szükséges a licencelési modellhez, mert a licenceket az Azure AD-bérlőhöz adja hozzá, amikor megvásárolja és hozzárendeli a címtárban lévő felhasználókhoz.

## <a name="manage-and-support-user-accounts"></a>Felhasználói fiókok kezelése és támogatása

* [Mit kell mondanom a felhasználóknak, ha nem kapnak választ a telefonjára?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Mi a teendő, ha az egyik felhasználó nem tud bejutni a fiókjába?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Mi a teendő, ha az egyik felhasználó elveszti az alkalmazás jelszavait használó telefont?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Mi a teendő, ha a felhasználó nem tud bejelentkezni a böngészőn kívüli alkalmazásokba?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [A felhasználók azt mondják, hogy néha nem kapják meg a szöveges üzenetet, vagy a hitelesítés időtúllépést mutat.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Megváltoztathatom azt az időtartamot, ameddig a felhasználóknak meg kell adniuk az ellenőrző kódot egy szöveges üzenetből a rendszer időtúllépése előtt?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Használhatok hardveres jogkivonatokat az Azure Multi-Factor Authentication-kiszolgáló?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Használhatom az Azure Multi-Factor Authentication-kiszolgálót a Terminálszolgáltatások biztonságossá tételéhez?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Konfiguráltam a hívót az MFA-kiszolgálón, de a felhasználók továbbra is Multi-Factor Authentication hívásokat fogadnak egy névtelen hívótól.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Miért kérik a felhasználókat, hogy regisztrálják a biztonsági adataikat?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Mit kell mondanom a felhasználóknak, ha nem kapnak választ a telefonjára?

Ha a felhasználók telefonhívást vagy SMS-t próbálnak meg használni a hitelesítéshez, 5 percen belül ötször próbálkoznak. A Microsoft több szolgáltatót használ a hívások és SMS-üzenetek kézbesítéséhez. Ha ez a megközelítés nem működik, nyisson meg egy támogatási esetet a további hibák megoldásához.

A harmadik féltől származó biztonsági alkalmazások is letilthatják az ellenőrző kód szöveges üzenetét vagy telefonhívását. Ha harmadik féltől származó biztonsági alkalmazást használ, próbálja meg letiltani a védelmet, majd kérjen egy másik MFA-ellenőrző kódot.

Ha a fenti lépések nem működnek, ellenőrizze, hogy a felhasználók több ellenőrzési módszerhez vannak-e konfigurálva. Próbálkozzon újra a bejelentkezéssel, de válasszon másik ellenőrzési módszert a bejelentkezési oldalon.

További információkért lásd a [végfelhasználói hibaelhárítási útmutatót](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Mi a teendő, ha az egyik felhasználó nem tud bejutni a fiókjába?

A felhasználó fiókját alaphelyzetbe állíthatja, ha újra el szeretné indítani a regisztrációs folyamatot. További információ a [felhasználók és eszközök beállításainak a Felhőbeli Azure ad-multi-Factor Authentication történő kezeléséről](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Mi a teendő, ha az egyik felhasználó elveszti az alkalmazás jelszavait használó telefont?

A jogosulatlan hozzáférés megakadályozása érdekében törölje az összes felhasználó alkalmazásának jelszavát. Miután a felhasználó rendelkezik egy helyettesítő eszközzel, újra létrehozhatja a jelszavakat. További információ a [felhasználók és eszközök beállításainak a Felhőbeli Azure ad-multi-Factor Authentication történő kezeléséről](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Mi a teendő, ha a felhasználó nem tud bejelentkezni a böngészőn kívüli alkalmazásokba?

Ha a szervezet továbbra is örökölt ügyfeleket használ, és [engedélyezte az alkalmazások jelszavainak használatát](howto-mfa-app-passwords.md), a felhasználók nem jelentkezhetnek be az örökölt ügyfelek felhasználónevével és jelszavával. Ehelyett az [alkalmazás jelszavait kell beállítania](../user-help/multi-factor-authentication-end-user-app-passwords.md). A felhasználóknak törölnie kell a bejelentkezési adataikat, újra kell indítaniuk az alkalmazást, majd be kell jelentkezniük a felhasználónevével és az *alkalmazás jelszavával* a normál jelszó helyett.

Ha a szervezet nem rendelkezik örökölt ügyfelekkel, ne engedélyezze a felhasználóknak az alkalmazás jelszavának létrehozását.

> [!NOTE]
> **Modern hitelesítés az Office 2013-ügyfelek számára**
>
> Az alkalmazás jelszavai csak a modern hitelesítést nem támogató alkalmazásokhoz szükségesek. Az Office 2013-ügyfelek támogatják a modern hitelesítési protokollokat, de konfigurálni kell őket. A modern hitelesítés minden olyan ügyfél számára elérhető, amely az Office 2013 2015 márciusi vagy újabb frissítését futtatja. További információkért tekintse meg a [frissített Office 365 modern hitelesítés](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)című blogbejegyzést.

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>A felhasználók azt mondják, hogy néha nem kapják meg a szöveges üzenetet, vagy a hitelesítés időtúllépést mutat.

Az SMS-üzenetek kézbesítése nem garantált, mert olyan nem ellenőrizhető tényezők vannak, amelyek befolyásolhatják a szolgáltatás megbízhatóságát. Ezek a tényezők közé tartoznak a rendeltetési ország vagy régió, a mobiltelefon-szolgáltató és a jelerősség.

A harmadik féltől származó biztonsági alkalmazások is letilthatják az ellenőrző kód szöveges üzenetét vagy telefonhívását. Ha harmadik féltől származó biztonsági alkalmazást használ, próbálja meg letiltani a védelmet, majd kérjen egy másik MFA-ellenőrző kódot.

Ha a felhasználók gyakran problémákba ütköznek a szöveges üzenetek megbízható fogadásával, akkor a Microsoft Authenticator alkalmazás vagy telefonhívás módszer használatát kell kérnie. A Microsoft Authenticator a mobil-és Wi-Fi-kapcsolatokon keresztül is fogadhat értesítéseket. Emellett a Mobile alkalmazás akkor is létrehozhat ellenőrző kódokat, ha az eszközön nincs jel. A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) és [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) rendszereken érhető el.

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Megváltoztathatom azt az időtartamot, ameddig a felhasználóknak meg kell adniuk az ellenőrző kódot egy szöveges üzenetből a rendszer időtúllépése előtt?

Bizonyos esetekben igen.

Az Azure MFA Server 7.0-s vagy újabb verziójával rendelkező egyirányú SMS-ben a beállításkulcs beállításával állíthatja be az időtúllépési beállítást. Miután az MFA Cloud Service elküldi a szöveges üzenetet, a rendszer visszaadja az ellenőrző kódot (vagy egyszer használatos jelszót) az MFA-kiszolgálónak. Az MFA-kiszolgáló alapértelmezés szerint 300 másodpercig tárolja a kódot a memóriában. Ha a felhasználó nem adja meg a kódot a 300 másodperc eltelte előtt, a hitelesítés megtagadva. Az alapértelmezett időtúllépési beállítás módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a következőt: `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Hozzon létre egy *pfsvc_pendingSmsTimeoutSeconds* nevű **DWORD** beállításkulcsot, és állítsa be azt az időpontot másodpercben, ameddig az Azure MFA-kiszolgálónak egyszer használatos PIN-kódot kell tárolnia.

>[!TIP]
>
> Ha több MFA-kiszolgálóval rendelkezik, csak az eredeti hitelesítési kérelem feldolgozását végző felhasználó ismeri a felhasználónak továbbított ellenőrző kódot. Ha a felhasználó megadja a kódot, az érvényesítéséhez szükséges hitelesítési kérést ugyanarra a kiszolgálóra kell elküldeni. Ha a kód érvényesítését egy másik kiszolgálóra küldik, a rendszer megtagadja a hitelesítést.

Ha a felhasználók nem válaszolnak az SMS-re a megadott időtúllépési időtartamon belül, a hitelesítés megtagadva.

Egyirányú SMS-ben az Azure AD MFA a felhőben (beleértve a AD FS adaptert vagy a hálózati házirend-kiszolgáló bővítményt) nem konfigurálhatja az időtúllépési beállítást. Az Azure AD 180 másodpercig tárolja az ellenőrző kódot.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Használhatok hardveres jogkivonatokat az Azure Multi-Factor Authentication-kiszolgáló?

Ha az Azure Multi-Factor Authentication-kiszolgáló-t használja, akkor importálhat harmadik féltől származó nyílt hitelesítési (eskü) időalapú, egyszeri jelszavas (TOTP) jogkivonatokat, majd használhatja őket a kétlépéses ellenőrzéshez.

Ha a titkos kulcsot egy CSV-fájlba helyezi, és az Azure Multi-Factor Authentication-kiszolgálóba importálja, használhatja a ActiveIdentity-tokeneket, amelyek ESKÜt TOTP jogkivonatokat használnak. A Active Directory összevonási szolgáltatások (AD FS) (ADFS), az Internet Information Server (IIS) űrlapalapú hitelesítés és a Remote Authentication Dial-In User Service (RADIUS) használatával is használhat eskü-tokeneket, feltéve, hogy az ügyfélrendszer el tudja fogadni a felhasználói adatokat.

A harmadik féltől származó eskü TOTP jogkivonatok importálása a következő formátumokkal végezhető el:  

- Hordozható szimmetrikus kulcstároló (PSKC)
- CSV, ha a fájl sorozatszámot, titkos kulcsot tartalmaz Base 32 formátumban, és egy időintervallumot

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Használhatom az Azure Multi-Factor Authentication-kiszolgálót a Terminálszolgáltatások biztonságossá tételéhez?

Igen, de ha Windows Server 2012 R2 vagy újabb operációs rendszert használ, csak Távoli asztali átjáró (RD-átjáró) használatával biztosíthat biztonságos terminálszolgáltatásokat.

A Windows Server 2012 R2 biztonsági módosításai megváltoztak, hogyan kapcsolódik az Azure Multi-Factor Authentication-kiszolgáló a helyi biztonsági szervezet (LSA) biztonsági csomagjához a Windows Server 2012 és a korábbi verziókban. A Windows Server 2012-es vagy korábbi verziójú Terminálszolgáltatások esetében a Windows- [hitelesítéssel biztonságossá](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)teheti az alkalmazásokat. Ha a Windows Server 2012 R2 rendszert használja, RD-átjáróra van szüksége.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Konfiguráltam a hívót az MFA-kiszolgálón, de a felhasználók továbbra is Multi-Factor Authentication hívásokat fogadnak egy névtelen hívótól.

Ha Multi-Factor Authentication hívásokat a nyilvános telefonos hálózaton keresztül helyezi át, időnként olyan szolgáltatón keresztül irányítja őket, amely nem támogatja a hívó AZONOSÍTÓját. A szolgáltatói viselkedés miatt a hívó azonosítója nem garantált, még akkor is, ha a Multi-Factor Authenticationrendszer mindig elküldi azt.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Miért kérik a felhasználókat, hogy regisztrálják a biztonsági adataikat?

Számos oka lehet annak, hogy a felhasználóknak meg kell adniuk biztonsági adataik regisztrálását:

- A felhasználó engedélyezve lett az MFA számára az Azure AD-beli rendszergazdája számára, de még nem rendelkezik a fiókjához regisztrált biztonsági információkkal.
- A felhasználó az Azure AD-ben engedélyezte az önkiszolgáló jelszó-visszaállítást. A biztonsági információk segítségével a későbbiekben is visszaállíthatja a jelszavát, ha elfelejtik.
- A felhasználó olyan alkalmazást kapott, amely feltételes hozzáférési házirenddel rendelkezik az MFA megköveteléséhez, és korábban nem regisztrált az MFA-hoz.
- A felhasználó regisztrálja az eszközt az Azure AD-vel (beleértve az Azure AD Joint is), és a szervezete megköveteli az MFA használatát az eszközök regisztrálásához, de a felhasználó korábban nem regisztrált az MFA-ra.
- A felhasználó a Windows Hello for businesst generálja a Windows 10 rendszerben (amely MFA-t igényel), és korábban nem regisztrált az MFA-ra.
- A szervezet létrehozta és engedélyezte a felhasználóra alkalmazott MFA regisztrációs szabályzatot.
- A felhasználó korábban már regisztrálta az MFA-t, de olyan ellenőrzési módszert választott, amelyet a rendszergazda letiltott. A felhasználónak ezért újra kell haladnia az MFA-regisztráción, hogy új alapértelmezett ellenőrzési módszert válasszon ki.

## <a name="errors"></a>Hibák

* [Mit kell tenniük a felhasználóknak, ha "hitelesítési kérelem nem aktivált fiókhoz" hibaüzenet jelenik meg a Mobile apps értesítéseinek használatakor?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Mi a teendő, ha 0x800434D4L hibaüzenet jelenik meg a nem böngésző alkalmazásba való bejelentkezéskor?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Mit kell tenniük a felhasználóknak, ha "hitelesítési kérelem nem aktivált fiókhoz" hibaüzenet jelenik meg a Mobile apps értesítéseinek használatakor?

Kérje meg a felhasználót, hogy az alábbi eljárás végrehajtásával távolítsa el a fiókját a Microsoft Authenticator, majd adja hozzá újra:

1. Lépjen a [Azure Portal profiljához](https://account.activedirectory.windowsazure.com/profile/) , és jelentkezzen be szervezeti fiókkal.
2. Válassza a **további biztonsági ellenőrzés** lehetőséget.
3. Távolítsa el a meglévő fiókot a Microsoft Authenticator alkalmazásból.
4. Kattintson a **Konfigurálás** elemre, majd kövesse az utasításokat a Microsoft Authenticator újrakonfigurálásához.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Mi a teendő, ha 0x800434D4L hibaüzenet jelenik meg a nem böngésző alkalmazásba való bejelentkezéskor?

A *0x800434D4L* hiba akkor fordul elő, ha egy helyi számítógépen telepített, nem böngészős alkalmazásba próbál bejelentkezni, amely nem működik olyan fiókokkal, amelyek kétlépéses ellenőrzést igényelnek.

Ennek a hibának a megkerülő megoldás, ha külön felhasználói fiókkal rendelkezik a rendszergazdai és a nem rendszergazdai műveletekhez. Később a rendszergazdai fiók és a nem rendszergazdai fiók között is csatolhat postaládákat, így a nem rendszergazdai fiókkal is bejelentkezhet az Outlookba. A megoldással kapcsolatos további információkért tekintse meg, hogy a [rendszergazda hogyan nyithatja meg és tekintheti meg a felhasználó postaládájának tartalmát](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Következő lépések

Ha a kérdés itt nem válaszol, a következő támogatási lehetőségek érhetők el:

* A gyakori technikai problémák megoldásához keressen a [Microsoft ügyfélszolgálata Tudásbázisban](https://support.microsoft.com) .
* Megkeresheti és böngészheti a Közösség technikai kérdéseit és válaszait, vagy megteheti a saját kérdéseit az [Azure Active Directory Q&a](/answers/topics/azure-active-directory.html)-ben.
* Forduljon a Microsoft Professional szolgáltatáshoz az [Azure multi-Factor Authentication-kiszolgáló támogatásával](https://support.microsoft.com/oas/default.aspx?prid=14947). Amikor kapcsolatba lép velünk, hasznos lehet, ha a lehető legtöbb információt felveheti a probléma megoldására. A megadható információk közé tartozik az a lap, ahol a hiba, a megadott hibakód, a megadott munkamenet-azonosító és a hibát megtekintő felhasználó azonosítója szerepel.
* Ha Ön örökölt PhoneFactor-ügyfél, és kérdései vannak, vagy segítségre van szüksége a jelszó alaphelyzetbe állításához, akkor az [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) e-mail-cím használatával nyisson meg egy támogatási esetet.

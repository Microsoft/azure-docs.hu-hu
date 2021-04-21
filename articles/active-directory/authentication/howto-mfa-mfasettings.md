---
title: Az Azure AD Multi-Factor Authentication konfigurálása – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja az Azure AD Multi-Factor Authentication beállításait a Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: c067dba3a8af87e354019154fad8304fe9edfbbc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829655"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Az Azure AD Multi-Factor Authentication beállításainak konfigurálása

Az Azure AD Multi-Factor Authentication végfelhasználói élményének testreszabásához olyan beállításokat konfigurálhat, mint a fiókzárolási küszöbértékek, a csalással kapcsolatos riasztások és értesítések. Egyes beállítások közvetlenül a Azure Portal for Azure Active Directory (Azure AD), néhány pedig egy külön Azure AD Multi-Factor Authentication-portálon találhatóak.

A következő Azure AD Multi-Factor Authentication-beállítások érhetők el a Azure Portal:

| Szolgáltatás | Leírás |
| ------- | ----------- |
| [Fiókzárolás](#account-lockout) | Ideiglenesen zárolja a fiókokat az Azure AD Multi-Factor Authentication használatában, ha túl sok elutasított hitelesítési kísérlet van egy sorban. Ez a funkció csak azokra a felhasználókra vonatkozik, akik PIN-kódot írnak be a hitelesítéshez. (MFA-kiszolgáló) |
| [Felhasználók blokkolása/tiltásának feloldása](#block-and-unblock-users) | Bizonyos felhasználók nem kaphatnak Azure AD Multi-Factor Authentication-kéréseket. A letiltott felhasználók összes hitelesítési kísérlete automatikusan el lesz utasítva. A felhasználók a letiltás vagy a letiltás manuális feloldása után 90 napig maradnak letiltva. |
| [Csalási riasztás](#fraud-alert) | Konfigurálja a beállításokat, amelyek lehetővé teszik a felhasználók számára, hogy csaló ellenőrzési kéréseket jelentsen. |
| [Értesítések](#notifications) | Engedélyezze az MFA-kiszolgáló eseményeiről szóló értesítéseket. |
| [OATH-jogkivonatok](concept-authentication-oath-tokens.md) | Felhőalapú környezetekben Azure AD MFA felhasználók OATH-jogkivonatának kezeléséhez. |
| [Telefonhívás-beállítások](#phone-call-settings) | A felhőbeli és helyszíni környezetek telefonhívásaira és üdvözléseire vonatkozó beállítások konfigurálása. |
| Szolgáltatók | Ez az összes olyan meglévő hitelesítésszolgáltatót megmutatja, amelyek a fiókjához társítva lehetnek. Előfordulhat, hogy 2018. szeptember 1-től nem jön létre új hitelesítésszolgáltató |

![Azure Portal – Az Azure AD Multi-Factor Authentication beállításai](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Fiókzárolás

Az ismétlődő MFA-kísérletek támadások részeként való megakadályozása érdekében a fiókzárolási beállítások segítségével megadhatja, hogy hány sikertelen kísérletet engedélyezzen a rendszer, mielőtt egy ideig zárolná a fiókot. A fiókzárolás beállításait csak akkor alkalmazza a rendszer, ha pin-kódot ad meg az MFA-kéréshez.

A következő beállítások érhetők el:

* Fiókzárolást kiváltó MFA-elutasítások száma
* Percek a fiókzárolási számláló alaphelyzetbe állításáig
* Percek a fiók automatikus feloldásáig

A fiókzárolási beállítások konfiguráláskor adja meg a következő beállításokat:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) rendszergazdaként.
1. Lépjen a **Azure Active Directory**  >    >  **MFA-fiók**  >  **zárolásának gombra.**
1. Adja meg a környezethez szükséges értékeket, majd válassza a **Mentés lehetőséget.**

    ![Képernyőkép a fiókzárolás beállításairól a Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Felhasználók blokkolása és tiltásának feloldása

Ha egy felhasználó eszköze elveszett vagy ellopták, letilthatja az Azure AD Multi-Factor Authentication-kísérleteket a társított fiókhoz. A letiltott felhasználók azure AD Multi-Factor Authentication-kísérlete automatikusan le lesz tiltva. A felhasználók a letiltás időpontjától kezdve 90 napig letiltva maradnak. Közzétettünk egy videót, amely bemutatja, hogyan [tilthat le](https://www.youtube.com/watch?v=WdeE1On4S1o) és oldhat fel felhasználókat a bérlőben, hogy meg tudja ezt mutatni.

### <a name="block-a-user"></a>Felhasználó blokkolása

Felhasználó letiltáshoz kövesse az alábbi lépéseket:

1. Lépjen a **Azure Active Directory**  >  **MFA**  >  **felhasználók**  >  **blokkolásának/feloldásának megkeresésére.**
1. Kattintson **a Hozzáadás gombra** a felhasználó letiltáshoz.
1. Adja meg a letiltott felhasználó felhasználónevét a következőként: , majd adjon meg egy `username@domain.com` megjegyzést az *Ok mezőben.*
1. Ha elkészült, kattintson az **OK gombra** a felhasználó letiltáshoz.

### <a name="unblock-a-user"></a>Felhasználó tiltásának feloldása

Felhasználó letiltásának feloldásához kövesse az alábbi lépéseket:

1. Lépjen a **Azure Active Directory**  >  **MFA**  >  **felhasználók**  >  **blokkolásának/feloldásának megkeresésére.**
1. A kívánt *felhasználó melletti Művelet* oszlopban válassza a **Tiltás feloldása lehetőséget.**
1. Írjon be egy megjegyzést a *Tiltás feloldásának oka mezőbe.*
1. Ha elkészült, kattintson az **OK gombra** a felhasználó letiltásának feloldásához.

## <a name="fraud-alert"></a>Csalási riasztás

A csalás elleni riasztási funkció lehetővé teszi, hogy a felhasználók rosszindulatú kísérleteket jelentsen az erőforrásaik elérésére. Ha ismeretlen és gyanús MFA-kérés érkezik, a felhasználók jelentést kaphatnak a csalási kísérletről a Microsoft Authenticator alkalmazással vagy telefonon keresztül.

A következő csalási riasztások konfigurációs lehetőségei érhetők el:

* **Csalást** bejelentő felhasználók automatikus blokkolása: Ha egy felhasználó csalást jelent, a rendszer 90 napig vagy egészen addig letiltja a felhasználói fiókkal kapcsolatos Azure AD MFA-hitelesítési kísérleteket, amíg egy rendszergazda fel nem oldja a fiókjának zárolását. A rendszergazdák a bejelentkezési jelentéssel áttekintheti a bejelentkezéseket, és megfelelő lépéseket tudnak tenni a jövőbeli csalások megelőzése érdekében. A rendszergazda ezután [feloldhatja](#unblock-a-user) a felhasználói fiók letiltásának feloldását.
* **Csalást jelentő** kód a kezdeti üdvözléskor: Amikor a felhasználók telefonhívást kapnak a többtényezős hitelesítés végrehajtásához, általában a billentyűvel megerősítik **#** a bejelentkezést. Csalás bejelentéséhez a felhasználó a billentyű lenyomása előtt beír egy **#** kódot. Ez a kód **alapértelmezés szerint 0,** de testre szabható.

   > [!NOTE]
   > A Microsoft alapértelmezett beszédes üdvözlése arra utasítja a felhasználókat, hogy a **0#** lenyomására küldjenek el egy csalási riasztást. Ha a 0-stól más **kódot** szeretne használni, rögzítse és töltse fel saját egyéni hanga üdvözlését a megfelelő utasításokkal a felhasználók számára.

A csalási riasztások engedélyezéséhez és konfiguráláshoz kövesse az alábbi lépéseket:

1. Keresse meg **a Azure Active Directory**  >  MFA Fraud  >  **(Biztonsági MFA-csalás)**  >  **riasztást.**
1. A *Csalási riasztások beküldésének* engedélyezése a felhasználóknak beállításnál adja meg a **Be beállítást.**
1. Konfigurálja *a Csalást* vagy  Kódot jelentő felhasználók automatikus blokkolása beállítást, hogy a kezdeti üdvözlés során szükség szerint jelentsen csalást.
1. Ha elkészült, válassza a **Mentés lehetőséget.**

### <a name="view-fraud-reports"></a>Csalási jelentések megtekintése

Válassza **Azure Active Directory**  >  **Bejelentkezések hitelesítési részletei**  >  **lehetőséget.** A csalási jelentés mostantól a szabványos Azure AD bejelentkezési jelentés része, és az **"Eredmény részletei"** mezőben az MFA megtagadva, a Fraud Code Entered (Csalás beadva) jelentésként fog jelenni.
 
## <a name="notifications"></a>Értesítések

E-mailes értesítéseket konfigurálhat, amikor a felhasználók csalási riasztásokat küldnek. Ezeket az értesítéseket általában az identitás-rendszergazdáknak küldi el a rendszer, mivel a felhasználó fiók hitelesítő adatai valószínűleg sérültek. Az alábbi példa bemutatja, hogyan néz ki egy csalással kapcsolatos riasztást értesítő e-mail:

![Példa csalási riasztási értesítő e-mailre](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

A csalás elleni riasztási értesítések konfigurálására adja meg a következő beállításokat:

1. Lépjen a **Azure Active Directory**  >    >  **Multi-Factor Authentication-értesítésekhez.**  >  
1. Adja meg az e-mail-címet, amit hozzá szeretne adni a következő mezőben.
1. Meglévő e-mail-cím eltávolításához válassza a kívánt e-mail-cím melletti **...** lehetőséget, majd válassza a **Törlés lehetőséget.**
1. Ha elkészült, válassza a **Mentés lehetőséget.**

## <a name="oath-tokens"></a>OATH-jogkivonatok

Az Azure AD támogatja az OATH-TOTP SHA-1 tokenek használatát, amelyek 30 vagy 60 másodpercenként frissülnek. Az ügyfelek a választott szállítótól vásárolhatják meg ezeket a jogkivonatokat.

Az OATH TOTP hardvertokenek általában tartalmaznak egy előre programozott titkos kulcsot vagy magot. Ezeket a kulcsokat az Azure AD-be kell beírni a következő lépésekben leírtak szerint. A titkos kulcsok legfeljebb 128 karakterből állhatnak, ami nem minden jogkivonattal kompatibilis. A titkos kulcs csak az *a–z* vagy az *A–Z* karaktereket és *az 1–7* számjegyeket tartalmazhatja, és *Base32 kódolásúnak kell lennie.*

A szoftvertoken beállítási folyamatában az Azure AD-val is beállítható, programozható OATH TOTP hardvertokenek állíthatók be.

Az OATH hardvertokenek a nyilvános előzetes verzió részeként támogatottak. További információ az előzetes verziókról: Kiegészítő használati feltételek a Microsoft Azure [előzetes verziókhoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![OATH-jogkivonatok feltöltése az MFA OATH tokenek panelre](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

A jogkivonatok beszerzése után azokat egy vesszővel elválasztott értékeket (CSV) fájlformátumba kell feltölteni, beleértve az UPN-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt az alábbi példában látható módon:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ügyeljen arra, hogy a CSV-fájlban szerepeljen a fejlécsor.

Miután megfelelően formázta a CSV-fájlt, a rendszergazda bejelentkezhet a Azure Portal-be, navigálhat az **Azure Active Directory > Security > MFA > OATH tokenek** lapra, és feltöltheti az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően a feldolgozás eltarthat néhány percig. Válassza **a Frissítés gombot** az aktuális állapot lekért állapotához. Ha a fájlban hibák vannak, letölthet egy CSV-fájlt, amely felsorolja a megoldható hibákat. A letöltött CSV-fájl mezőinek nevei eltérnek a feltöltött verziótól.

A hibák elhárítása után a rendszergazda úgy aktiválhatja az egyes kulcsokat, hogy kiválasztja az **Aktiválás** et a jogkivonathoz, és beírásával a jogkivonaton megjelenő egyszeri jelszó van beírásával.

A felhasználók legfeljebb öt OATH hardvertoken vagy hitelesítő alkalmazás, például a Microsoft Authenticator alkalmazás kombinációját konfigurálják bármikor használatra.

## <a name="phone-call-settings"></a>Telefonhívás beállításai

Ha a felhasználók telefonhívásokat kapnak az MFA-kérésekért, konfigurálhatja a felhasználói élményt, például a hívóazonosítót vagy a hangavacsot, amit hallanak.

A Egyesült Államok ha még nem konfigurálta az MFA-hívóazonosítót, a Microsoft hanghívásai a következő számokból jönnek. Levélszemétszűrők használata esetén mindenképpen zárja ki ezeket a számokat:

* *+1 (866) 539 4191*
* *+1 (855) 330 8653*
* *+1 (877) 668 6536*

> [!NOTE]
> Amikor az Azure AD Multi-Factor Authentication-hívásokat a nyilvános telefonhálózaton keresztül kezdeményezik, előfordulhat, hogy a hívásokat egy olyan szolgáltatón keresztül irányítják át, amely nem támogatja a hívóazonosítót. Emiatt a hívóazonosító nem garantált, annak ellenére, hogy az Azure AD Multi-Factor Authentication mindig elküldi. Ez a hívásokra és az Azure AD Multi-Factor Authentication által biztosított szöveges üzenetekre is vonatkozik. Ha ellenőriznie kell, hogy egy szöveges üzenet az Azure AD Multi-Factor Authenticationtől érkezett-e, tekintse meg a What SMS short codes [are used for sending messages?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users) (Milyen rövid SMS-kódokat használ az üzenetek küld?

Saját hívóazonosító-szám konfiguráláshoz kövesse az alábbi lépéseket:

1. Lépjen a **Azure Active Directory**  >    >  **MFA telefonos**  >  **hívási beállításainak megkereséséhez.**
1. Állítsa be **az MFA-hívóazonosító** számát arra a számra, amit a felhasználók a telefonjukon látniuk kell. Csak az USA-alapú számok engedélyezettek.
1. Ha elkészült, válassza a **Mentés lehetőséget.**

### <a name="custom-voice-messages"></a>Egyéni hangüzenetek

Az Egyéni hangüzenetek funkcióval saját felvételeket vagy üdvözléseket is használhat az Azure AD Multi-Factor Authenticationhez. Ezek az üzenetek az alapértelmezett Microsoft-felvételek mellett vagy azok cseréjére is használhatók.

Mielőtt hozzákezd, vegye figyelembe a következő korlátozásokat:

* A támogatott fájlformátumok a *.wav és* *az .mp3.*
* A fájlméret korlátja 1 MB.
* A hitelesítési üzeneteknek 20 másodpercnél rövidebbnek kell lennie. A 20 másodpercnél hosszabb üzenetek az ellenőrzés meghiúsulhat. Előfordulhat, hogy a felhasználó nem válaszol az üzenet befejeződés előtt, és az ellenőrzés időkorrekta.

### <a name="custom-message-language-behavior"></a>Egyéni üzenetnyelv viselkedése

Ha a felhasználó egy egyéni hangüzenetet játszik le, az üzenet nyelve a következő tényezőktől függ:

* Az aktuális felhasználó nyelve.
  * A felhasználó böngészője által észlelt nyelv.
  * Más hitelesítési forgatókönyvek másképp viselkedhetnek.
* Az elérhető egyéni üzenetek nyelve.
  * Ezt a nyelvet a rendszergazda választja ki egyéni üzenet hozzáadásakor.

Ha például csak egy egyéni üzenet van, német nyelvvel:

* A német nyelven hitelesítést használó felhasználó az egyéni német üzenetet fogja hallatni.
* Az angol nyelvű hitelesítést kapó felhasználó a szokásos angol nyelvű üzenetet fogja hallatni.

### <a name="custom-voice-message-defaults"></a>Egyéni hangüzenetek alapértelmezései

Az alábbi példaszkprogramokkal létrehozhatja saját egyéni üzeneteit. Ezek az alapértelmezett kifejezések, ha nem konfigurálja a saját egyéni üzeneteit:

| Üzenet neve | Script |
| --- | --- |
| A hitelesítés sikeres | A bejelentkezés ellenőrzése sikeres volt. viszlát. |
| Bővítmény parancssora | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. A folytatáshoz nyomja le a kettőskereszt billentyűt. |
| Csalás megerősítése | Csalás elleni riasztás lett elküldve. A fiók zárolásának feloldásához forduljon a cége ügyfélszolgálatához. |
| Csalások üdvözlése (Standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja le a kettőskereszt billentyűt. Ha nem ön kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbálja elérni a fiókját. A csalásról értesítő értesítés küld akkor is, ha lenyomja a kettőskeresztet. Ez értesíti a vállalata it-csapatát, és letiltja a további ellenőrzési kísérleteket. |
| Csalás által jelentett Csalási riasztás lett elküldve. | A fiók zárolásának feloldásához forduljon a cége ügyfélszolgálatához. |
| Aktiválás | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja le a kettőskereszt billentyűt. |
| Hitelesítés megtagadva – újrapróbálkozás | Ellenőrzés megtagadva. |
| Újrapróbálkozás (Standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja le a kettőskereszt billentyűt. |
| Greeting (Standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja le a kettőskereszt billentyűt. |
| Üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez adja meg a PIN-kódot, majd a kettőskereszt kulcsot. |
| Csalások üdvözlése (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja.  Az ellenőrzés befejezéséhez adja meg a PIN-kódot, majd a kettőskereszt kulcsot. Ha nem ön kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbálja elérni a fiókját. A csalásról értesítő értesítés küld akkor is, ha lenyomja a kettőskeresztet. Ez értesíti a vállalata it-csapatát, és letiltja a további ellenőrzési kísérleteket. |
| Újrapróbálkozás (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez adja meg a PIN-kódot, majd a kettőskereszt kulcsot. |
| Kiterjesztési kérés számjegyek után | Ha már a bővítménynél van, nyomja le a kettőskereszt billentyűt a folytatáshoz. |
| Hitelesítés megtagadva | Sajnos jelenleg nem tudjuk bejelentkezni. Próbálkozzon újra később. |
| Aktiválási üdvözlés (Standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja le a kettőskereszt billentyűt. |
| Aktiválás újrapróbálása (Standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja le a kettőskereszt billentyűt. |
| Aktiválási üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez adja meg a PIN-kódot, majd a kettőskereszt kulcsot. |
| Bővítmény kérése a számjegyek előtt | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Adja át ezt a hívást a bővítménynek... |

### <a name="set-up-a-custom-message"></a>Egyéni üzenet beállítása

Ha saját egyéni üzeneteket is használnia kell, kövesse az alábbi lépéseket:

1. Lépjen a **Azure Active Directory**  >    >  **MFA telefonos**  >  **hívási beállításainak megkereséséhez.**
1. Válassza **az Üdvözlés hozzáadása lehetőséget.**
1. Válassza ki **az Üdvözlés** típusa lehetőséget, például *Greeting (standard) vagy* Authentication successful *(Sikeres hitelesítés).*
1. Válassza ki **a Nyelvet** az előző szakaszban az egyéni üzenetnyelvi [viselkedés alapján.](#custom-message-language-behavior)
1. Keresse meg és válassza ki a feltölteni kívánt *.mp3* vagy *.wav* hangfájlt.
1. Ha elkészült, válassza a **Hozzáadás,** majd a **Mentés lehetőséget.**

## <a name="mfa-service-settings"></a>MFA-szolgáltatás beállításai

Az alkalmazásjelszavak, a megbízható IP-k, az ellenőrzési lehetőségek és az Azure AD Multi-Factor Authentication többtényezős hitelesítésének megjegyzése beállítás a szolgáltatásbeállítások között található. Ez inkább egy örökölt portál, és nem része a hagyományos Azure AD-portálnak.

A szolgáltatásbeállításokat a Azure Portal a **Azure Active Directory** MFA Első lépések  >    >    >    >  **Konfigurálása**  >  **További felhőalapú MFA-beállítások között.** Megnyílik egy új ablak vagy lap, amely további *szolgáltatásbeállításokat* tartalmaz.

## <a name="trusted-ips"></a>Megbízható IP-címek

Az  Azure AD Multi-Factor Authentication Megbízható IP-címek funkciója megkerüli a többtényezős hitelesítési kéréseket a megadott IP-címtartományból bejelentkező felhasználóknál. A helyszíni környezetekhez megbízható IP-címtartományokat állíthat be, így ha a felhasználók ezen helyek valamelyikén találhatók, nincs Azure AD Multi-Factor Authentication-kérés. Az  Azure AD Multi-Factor Authentication Megbízható IP-k funkció használatához prémium szintű Azure AD P1 kiadás szükséges. 

> [!NOTE]
> A megbízható IP-címek csak MFA-kiszolgáló használata esetén tartalmazhatnak magánhálózati IP-címtartományokat. Felhőalapú Azure AD Multi-Factor Authentication esetén csak nyilvános IP-címtartományokat használhat.
>
> Az IPv6-tartományok csak a Megnevezett hely [(előzetes verzió) felületen támogatottak.](../conditional-access/location-condition.md)

Ha a szervezet azért telepíti az NPS-bővítményt, hogy MFA-t biztosítson a helyszíni alkalmazásoknak, vegye figyelembe, hogy a forrás IP-címe mindig az NPS-kiszolgáló lesz, amely felé a hitelesítési kísérlet áthalad.

| Azure AD-bérlő típusa | Megbízható IP-szolgáltatás beállításai |
|:--- |:--- |
| Felügyelt |**Ip-címek meghatározott tartománya:** A rendszergazdák olyan IP-címtartományt határoznak meg, amely megkerülheti a többtényezős hitelesítést a vállalati intranetről bejelentkező felhasználók számára. Legfeljebb 50 megbízható IP-címtartomány konfigurálható.|
| Összevont |**Minden összevont felhasználó:** A szervezeten belülről bejelentkező összes összevont felhasználó megkerülheti a többtényezős hitelesítést. A felhasználók megkerülik az ellenőrzést egy, a felhasználó által kiadott jogcím Active Directory összevonási szolgáltatások (AD FS) (AD FS).<br/>**Ip-címek meghatározott tartománya:** A rendszergazdák olyan IP-címtartományt határoznak meg, amely megkerülheti a többtényezős hitelesítést a vállalati intranetről bejelentkező felhasználók számára. |

A megbízható IP-megkerülés csak a vállalati intranetről működik. Ha a **Minden** összevont felhasználó lehetőséget választja, és egy felhasználó a vállalati intranetről jelentkezik be, a felhasználónak többtényezős hitelesítéssel kell hitelesítenie magát. A folyamat akkor is ugyanaz, ha a felhasználó bemutatja a AD FS jogcímet.

### <a name="end-user-experience-inside-of-corpnet"></a>A vállalati hálózat végfelhasználói élménye

Ha a megbízható IP-k szolgáltatás le van tiltva, a böngészőfolyamatokhoz többtényezős hitelesítés szükséges. A régebbi, gazdag ügyfélalkalmazások esetében alkalmazásjelszavakra van szükség.

Megbízható IP-k használata esetén a böngészőfolyamatokhoz nincs szükség többtényezős hitelesítésre. A régebbi, gazdag ügyfélalkalmazások esetében nincs szükség alkalmazásjelszavakra, feltéve, hogy a felhasználó nem hozott létre alkalmazásjelszót. Ha már használatban van egy alkalmazásjelszó, a jelszóra továbbra is szükség lesz.

### <a name="end-user-experience-outside-corpnet"></a>Vállalati hálózaton kívüli végfelhasználói élmény

Függetlenül attól, hogy a megbízható IP-cím meg van-e határozva, a böngészőfolyamatokhoz többtényezős hitelesítés szükséges. A régebbi, gazdag ügyfélalkalmazások esetében alkalmazásjelszavakra van szükség.

### <a name="enable-named-locations-by-using-conditional-access"></a>Elnevezett helyek engedélyezése feltételes hozzáféréssel

A feltételes hozzáférési szabályokkal elnevezett helyeket határozhat meg az alábbi lépésekben:

1. A Azure Portal keresse meg és válassza a Azure Active Directory **lehetőséget,** majd keresse meg a **Biztonsági** feltételes hozzáférés  >    >  **neves helyeket.**
1. Válassza **az Új hely lehetőséget.**
1. Adja meg a hely nevét.
1. Válassza **a Megjelölés megbízható helyként lehetőséget.**
1. Adja meg az IP-címtartományt a környezet CIDR-notation értékében, *például: 40.77.182.32/27.*
1. Válassza a **Létrehozás** lehetőséget.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>A Megbízható IP-k funkció engedélyezése feltételes hozzáféréssel

A megbízható IP-k feltételes hozzáférési szabályzatokkal való engedélyezéséhez kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg és válassza a Azure Active Directory **lehetőséget,** majd keresse meg a **Security** Conditional Access Named locations (Biztonsági feltételes hozzáférés  >     >  **neve) helyeket.**
1. Válassza **az MFA megbízható IP-k konfigurálása lehetőséget.**
1. A **Szolgáltatásbeállítások lapon** a Megbízható **IP-k alatt** válasszon az alábbi két lehetőség közül:

   * **Az intranetről** származó összevont felhasználóktól származó kérések esetén: Ha ezt a beállítást választja, jelölje be a jelölőnégyzetet. A vállalati hálózatról bejelentkező összes összevont felhasználó megkerüli a többtényezős hitelesítést egy, a vállalat által kiadott jogcím AD FS. Győződjön meg AD FS, hogy van olyan szabály, amely hozzáadja az intranetes jogcímet a megfelelő forgalomhoz. Ha a szabály nem létezik, hozza létre a következő szabályt a AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Nyilvános IP-címek** egy adott tartományának kérései esetén: Ha ezt a beállítást választja, CIDR-beállítással adja meg az IP-címeket a szövegmezőben.
      * A xxx.xxx.xxx.1 és xxx.xxx.xxx.254 tartományba eső IP-címekhez használja a következőt: **xxx.xxx.xxx.0/24.**
      * Egyetlen IP-címhez használjon például egy **xxx.xxx.xxx.xxx/32.**
      * Legfeljebb 50 IP-címtartományt adjon meg. Az ezekről az IP-címekről bejelentkező felhasználók megkerülik a többtényezős hitelesítést.

1. Kattintson a **Mentés** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>A Megbízható IP-k szolgáltatás engedélyezése szolgáltatásbeállítások használatával

Ha nem szeretne feltételes hozzáférési szabályzatokat használni a megbízható IP-k engedélyezéséhez, a következő lépésekkel konfigurálhatja az Azure AD Multi-Factor Authentication szolgáltatásbeállítását: 

1. A Azure Portal keresse meg és válassza ki a Azure Active Directory, majd válassza **a** **Felhasználók lehetőséget.**
1. Válassza a **Multi-Factor Authentication** lehetőséget.
1. A Multi-Factor Authentication alatt válassza a **szolgáltatásbeállítások lehetőséget.**
1. A **Szolgáltatásbeállítások lapon** a Megbízható **IP-k** alatt válasszon egyet (vagy mindkettőt) az alábbi két lehetőség közül:

   * **Összevont felhasználók intranetes** kérései esetén: Ha ezt a lehetőséget választja, jelölje be a jelölőnégyzetet. A vállalati hálózatról bejelentkező összes összevont felhasználó megkerüli a többtényezős hitelesítést egy, a vállalat által kiadott jogcím AD FS. Győződjön meg AD FS, hogy a megfelelő forgalomhoz adja hozzá az intranetes jogcímet a megfelelő szabály szerint. Ha a szabály nem létezik, hozza létre a következő szabályt a AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * Megadott IP-cím-alhálózati tartományokból származó kérések **esetén:** Ha ezt a beállítást választja, CIDR-beállítással írja be a szövegmezőbe az IP-címeket.
      * A xxx.xxx.xxx.1–xxx.xxx.254 tartományba esik IP-címekhez használja a következő formátumot: **xxx.xxx.xxx.0/24.**
      * Egyetlen IP-címhez használjon például a **xxx.xxx.xxx.xxx/32.**
      * Adjon meg legfeljebb 50 IP-címtartományt. Az ezekről az IP-címekről bejelentkező felhasználók megkerülik a többtényezős hitelesítést.

1. Kattintson a **Mentés** gombra.

## <a name="verification-methods"></a>Ellenőrzési módszerek

A felhasználók számára elérhető ellenőrzési módszereket a szolgáltatásbeállítások portálján választhatja ki. Amikor a felhasználók regisztrálják fiókjukat az Azure AD Multi-Factor Authentication szolgáltatásban, az Ön által engedélyezett lehetőségek közül választják az előnyben részesített ellenőrzési módszert. A felhasználói regisztrációs folyamathoz a Fiók beállítása többtényezős [hitelesítéshezcímű dokumentum nyújt útmutatást.](../user-help/multi-factor-authentication-end-user-first-time.md)

A következő ellenőrzési módszerek érhetők el:

| Metódus | Leírás |
|:--- |:--- |
| Hívás telefonra |Automatizált hanghívást végez. A felhasználó fogadja a hívást, majd a hitelesítéshez lenyomja a telefon billentyűzetén a # gombot.  A telefonszám nincs szinkronizálva a helyi Active Directory. |
| Szöveges üzenet a telefonra |Ellenőrző kódot tartalmazó szöveges üzenetet küld. A felhasználónak meg kell adnia az ellenőrző kódot a bejelentkezési felületen. Ezt a folyamatot egyútos SMS-nek nevezzük. A kétútos SMS azt jelenti, hogy a felhasználónak szöveges üzenetet kell küldenie egy adott kódról. 2018. november 14-től a kétúti SMS-ek elavultak, és nem támogatottak. A rendszergazdáknak egy másik módszert kell engedélyezniük a korábban kétútos SMS-t használó felhasználók számára.|
| Értesítés mobilalkalmazáson keresztül |Leküldéses értesítést küld telefonjára vagy regisztrált eszközére. A felhasználó megtekinti az értesítést, és az **Ellenőrzés lehetőséget választja** az ellenőrzés befejezéséhez. A Microsoft Authenticator alkalmazás a következő [alkalmazásokhoz érhető el: Windows Phone-telefon](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Mobilalkalmazásból vagy hardvertokenből származó ellenőrzőkód |A Microsoft Authenticator alkalmazás 30 másodpercenként létrehoz egy új OATH ellenőrző kódot. A felhasználó beírja az ellenőrző kódot a bejelentkezési felületen. A Microsoft Authenticator alkalmazás a következő [alkalmazásokhoz érhető el: Windows Phone-telefon](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

További információ: Milyen hitelesítési és ellenőrzési módszerek érhetők el [az Azure AD-ban?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Ellenőrzési módszerek engedélyezése és letiltása

Az ellenőrzési módszerek engedélyezéséhez vagy letiltásához kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg és válassza ki a Azure Active Directory, majd válassza **a** **Felhasználók lehetőséget.**
1. Válassza a **Multi-Factor Authentication** lehetőséget.
1. A Multi-Factor Authentication alatt válassza a **szolgáltatásbeállítások lehetőséget.**
1. A **Szolgáltatásbeállítások lapon,** az **ellenőrzési** beállítások alatt válassza ki vagy törölje a felhasználók számára biztosítani kívánt módszerek kijelölését.
1. Kattintson a **Mentés** gombra.

## <a name="remember-multi-factor-authentication"></a>Többtényezős hitelesítés ne feledje

A _többtényezős_ hitelesítés megírása funkció lehetővé teszi, hogy a felhasználók bizonyos számú napig mellőzni tudják a további ellenőrzést, miután sikeresen bejelentkeztek egy eszközre a Multi-Factor Authentication használatával. A jobb használhatóság és a felhasználók MFA-műveletek elvégzésének minimálisra csökkentése érdekében válasszon ki egy 90 napos vagy annál hosszabb időtartamú eszközt.

> [!IMPORTANT]
> Ha egy fiók vagy eszköz biztonsága sérül, a megbízható eszközök többtényezős hitelesítésének engedélyezése hatással lehet a biztonságra. Ha egy vállalati fiók biztonsága sérül, vagy egy megbízható eszköz elveszett vagy ellopták, vonja vissza [az MFA-munkameneteket.](howto-mfa-userdevicesettings.md)
>
> A visszaállítási művelet visszavonja az összes eszköz megbízható állapotát, és a felhasználónak újra el kell végeznie a többtényezős hitelesítést. Arra is utasíthatja a felhasználókat, hogy állják vissza a többtényezős hitelesítést a saját eszközeiken a Manage your settings for multi-factor authentication (A többtényezős hitelesítés beállításainak [kezelése)címűcímű oktató szerint.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>A funkció működése

A Multi-Factor Authentication szolgáltatás állandó cookie-t állít be a böngészőben, amikor a felhasználó a Ne kérdezzen **rá újra X** napra lehetőséget választja a bejelentkezésnél. A felhasználótól a rendszer nem kéri újra a Multi-Factor Authenticationt ugyanezen böngészőből, amíg a cookie le nem jár. Ha a felhasználó egy másik böngészőt nyit meg ugyanazon az eszközön, vagy törli a cookie-kat, a rendszer ismét megerősítést kér.

A **Ne kérdezzen** rá újra X napra lehetőség nem jelenik meg a böngészőn kívüli alkalmazásokban, függetlenül attól, hogy az alkalmazás támogatja-e a modern hitelesítést. Ezek az alkalmazások frissítési _jogkivonatokat használnak,_ amelyek óránként új hozzáférési jogkivonatokat biztosítanak. A frissítési jogkivonat érvényesítésekor az Azure AD ellenőrzi, hogy a legutóbbi többtényezős hitelesítés a megadott számú napon belül történt-e.

A funkció csökkenti a webalkalmazások hitelesítésének számát, ami általában minden alkalommal kéri. A funkcióval megnövelheti a modern hitelesítési ügyfelek hitelesítésének számát, amelyek általában 180 naponta kérik, ha alacsonyabb időtartam van konfigurálva. A feltételes hozzáférési szabályzatokkal kombinálva növelheti a hitelesítések számát is.

> [!IMPORTANT]
> A **többtényezős** hitelesítés nem kompatibilis az  AD FS bejelentkezve tartás funkcióval, ha a felhasználók az Azure Multi-Factor Authentication-kiszolgáló-val vagy egy külső többtényezős hitelesítési megoldással végeznek többtényezős hitelesítést az AD FS-ban.
>
> Ha a  felhasználók a Bejelentkezve vagyok az **AD FS-ban** lehetőséget választva az eszközüket is megbízhatóként jelölik meg a Többtényezős hitelesítéshez, a rendszer nem ellenőrzi automatikusan a felhasználót, miután lejárt a többtényezős hitelesítésre vonatkozó fontos információk száma. Az Azure AD friss többtényezős hitelesítést kér, de a AD FS egy tokent ad vissza az eredeti Multi-Factor Authentication-jogcím és -dátum alapján ahelyett, hogy újra végrehajtja a többtényezős hitelesítést. **Ez a reakció ellenőrzési hurkot hoz létre az Azure AD és a AD FS.**
>
> A **multi-factor authentication** funkció nem kompatibilis a B2B-felhasználókkal, és nem lesz látható a B2B-felhasználók számára, amikor bejelentkeznek a meghívott bérlőkbe.
>

### <a name="enable-remember-multi-factor-authentication"></a>Többtényezős hitelesítés engedélyezése

Ha engedélyezni szeretné és konfigurálni szeretné a felhasználók számára, hogy jegyezhjék meg az MFA-állapotukat és megkerüljék a kéréseket, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg és válassza ki a Azure Active Directory, majd válassza **a** **Felhasználók lehetőséget.**
1. Válassza a **Multi-Factor Authentication** lehetőséget.
1. A Multi-Factor Authentication alatt válassza a **szolgáltatásbeállítások lehetőséget.**
1. A **Szolgáltatásbeállítások lapon,** a Többtényezős hitelesítés  jegyezve alatt jelölje be a Többtényezős hitelesítés engedélyezése a felhasználóknak a megbízható eszközökön beállítást. 
1. Állítsa be, hogy a megbízható eszközök hány napig mellőzhetik a többtényezős hitelesítést. Az optimális felhasználói élmény érdekében hosszabbítsa meg *az időtartamot 90 vagy több* napra.
1. Kattintson a **Mentés** gombra.

### <a name="mark-a-device-as-trusted"></a>Eszköz megjelölése megbízhatóként

Miután engedélyezi a Többtényezős hitelesítés megjegyzása funkciót, a felhasználók a Ne kérdezzenek újra lehetőséget választva megbízhatóként jelölnek meg egy eszközt a **bejelentkezéskor.**

## <a name="next-steps"></a>Következő lépések

Az Azure AD Multi-Factor Authenticationben használható módszerekről további információt a What authentication and verification methods are available [in Azure Active Directory?](concept-authentication-methods.md) (Milyen hitelesítési és ellenőrzési módszerek érhetők el a Azure Active Directory?

---
title: Az Azure AD Multi-Factor Authentication konfigurálása – Azure Active Directory
description: Ismerje meg, hogyan konfigurálhatja az Azure AD Multi-Factor Authentication beállításait a Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 9d2c42a664c8d8a591d95a312cdf1c0034c4d031
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305463"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Az Azure AD Multi-Factor Authentication beállításainak konfigurálása

Az Azure AD Multi-Factor Authentication végfelhasználói élményének testreszabásához olyan beállításokat adhat meg, mint például a fiókzárolási küszöbértékek vagy a csalások elleni riasztások és értesítések. Egyes beállítások közvetlenül a Azure Active Directory (Azure AD) Azure Portal, néhány pedig egy különálló Azure AD Multi-Factor Authentication-portálon találhatók.

A Azure Portal a következő Azure AD Multi-Factor Authentication beállítások érhetők el:

| Szolgáltatás | Leírás |
| ------- | ----------- |
| [Fiókzárolási](#account-lockout) | A fiókok ideiglenes zárolása az Azure AD-Multi-Factor Authentication használatával, ha túl sok megtagadott hitelesítési kísérlet van egy sorban. Ez a funkció csak azokra a felhasználókra vonatkozik, akik hitelesítő PIN-kódot ad meg. (MFA-kiszolgáló) |
| [Felhasználók blokkolása/feloldása](#block-and-unblock-users) | Az Azure AD Multi-Factor Authentication kérések fogadásának letiltása adott felhasználók számára. A letiltott felhasználók összes hitelesítési kísérlete automatikusan el lesz utasítva. A felhasználók a blokkolt időponttól számítva 90 napig, vagy manuálisan feloldják a zárolást. |
| [Csalási riasztás](#fraud-alert) | Olyan beállítások konfigurálása, amelyek lehetővé teszik a felhasználók számára a csalárd ellenőrzési kérelmek jelentését. |
| [Értesítések](#notifications) | Az MFA-kiszolgálóról érkező események értesítéseinek engedélyezése. |
| [OATH-jogkivonatok](concept-authentication-oath-tokens.md) | Felhőalapú Azure AD MFA-környezetekben használatos a felhasználók eskü-jogkivonatának kezeléséhez. |
| [Telefonhívás beállításai](#phone-call-settings) | A Felhőbeli és a helyszíni környezetekhez kapcsolódó telefonhívások és üdvözlések beállításainak konfigurálása. |
| Szolgáltatók | Ez megjeleníti a fiókjához esetlegesen hozzárendelt meglévő hitelesítési szolgáltatókat is. Az új hitelesítési szolgáltatók nem hozhatók létre szeptember 1-től 2018-ig |

![Azure Portal – Azure AD-Multi-Factor Authentication beállításai](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Fiókzárolási

Ha meg szeretné akadályozni, hogy a támadás részeként ismétlődő MFA-kísérleteket hajtson végre, a fiókzárolási beállítások lehetővé teszik annak megadását, hogy hány sikertelen kísérletet engedélyezzen a fiók egy adott időszakra való kizárása előtt. A fiókzárolási beállításokat csak akkor alkalmazza a rendszer, ha PIN-kódot adtak meg az MFA-parancssorhoz.

A következő beállítások érhetők el:

* Fiók zárolását kiváltó MFA-elutasítások száma
* A fiókzárolás számlálójának alaphelyzetbe állítása percben
* A fiók automatikus feloldása ennyi percet vesz igénybe

A fiókzárolás beállításainak konfigurálásához hajtsa végre az alábbi beállításokat:

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **MFA**-  >  **fiók zárolását**.
1. Adja meg a környezet szükséges értékeit, majd kattintson a **Mentés** gombra.

    ![Képernyőfelvétel a Azure Portal fiókzárolási beállításairól](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Felhasználók blokkolása és feloldása

Ha a felhasználó eszközét elvesztette vagy ellopták, letilthatja az Azure AD-Multi-Factor Authentication a társított fiókra vonatkozó kísérleteket. A rendszer a blokkolt felhasználókra vonatkozó összes Azure AD-Multi-Factor Authentication automatikusan megtagadja. A felhasználók a letiltás időpontjától kezdve 90 napig letiltva maradnak. Közzétettünk egy videót arról, [Hogyan lehet letiltani és feloldani a bérlőben lévő felhasználókat](https://www.youtube.com/watch?v=WdeE1On4S1o) , hogy megmutassa a módját.

### <a name="block-a-user"></a>Felhasználó blokkolása

Egy felhasználó letiltásához hajtsa végre a következő lépéseket:

1. Keresse meg a **Azure Active Directory**  >  **biztonsági**  >  **MFA**  >  **blokkolása/letiltása felhasználókat**.
1. A felhasználó blokkolásához válassza a **Hozzáadás** lehetőséget.
1. Adja meg a letiltott felhasználó felhasználónevét `username@domain.com` , majd adjon meg egy megjegyzést az *OK* mezőben.
1. Ha elkészült, kattintson az **OK** gombra a felhasználó blokkolásához.

### <a name="unblock-a-user"></a>Felhasználó tiltásának feloldása

A felhasználó tiltásának feloldásához hajtsa végre a következő lépéseket:

1. Keresse meg a **Azure Active Directory**  >  **biztonsági**  >  **MFA**  >  **blokkolása/letiltása felhasználókat**.
1. A kívánt felhasználó melletti *művelet* oszlopban válassza a **Tiltás feloldása** lehetőséget.
1. Adjon meg egy megjegyzést a *blokkolás feloldásának okát* illetően.
1. Ha elkészült, kattintson **az OK** gombra a felhasználó tiltásának feloldásához.

## <a name="fraud-alert"></a>Csalási riasztás

A csalások riasztási funkciója lehetővé teszi, hogy a felhasználók csalárd kísérleteket jelentsenek az erőforrásaik eléréséhez. Ha egy ismeretlen és gyanús MFA-kérés érkezik, a felhasználók a Microsoft Authenticator alkalmazással vagy telefonon keresztül jelenthetik be a csalási kísérletet.

A következő csalási riasztások konfigurációs beállításai érhetők el:

* A **csalást bejelentő felhasználók automatikus letiltása**: Ha egy felhasználó csalást jelez, a felhasználói fiókhoz tartozó Azure ad MFA hitelesítési kísérletek 90 napig le vannak tiltva, vagy amíg a rendszergazda fel nem oldja a fiókját. A rendszergazdák a bejelentkezési jelentés segítségével ellenőrizhetik a bejelentkezéseket, és megtehetik a megfelelő lépéseket a jövőbeli csalások megelőzése érdekében. A rendszergazdák Ezután [letilthatják](#unblock-a-user) a felhasználó fiókját.
* **Kód a csalás bejelentéséhez a kezdeti üdvözlés során**: Ha a felhasználók telefonhívást kapnak a többtényezős hitelesítés végrehajtásához, a rendszer általában megnyomja a **#** Bejelentkezés megerősítését. A csalások jelentéséhez a felhasználónak be kell írnia egy kódot a gomb megnyomása előtt **#** . Alapértelmezés szerint ez a kód **0** , de testre is szabhatja.

   > [!NOTE]
   > A Microsoft alapértelmezett hang-üdvözlése arra utasítja a felhasználókat, hogy a **0 #** lenyomásával küldjenek csalási riasztást. Ha **nullától** eltérő kódot szeretne használni, jegyezze fel és töltse fel a saját egyéni hangfelvételeit a felhasználókra vonatkozó megfelelő utasításokkal.

A csalási riasztások engedélyezéséhez és konfigurálásához hajtsa végre a következő lépéseket:

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **MFA**-  >  **csalások elleni riasztást**.
1. Állítsa be a *csalások beküldésének engedélyezése a felhasználók* számára **beállítást.**
1. Konfigurálja a *csalást vagy kódot jelentéssel rendelkező felhasználók automatikus blokkolását* *, hogy az első üdvözlés* beállításakor a kívánt módon jelentse a csalást.
1. Ha elkészült, válassza a **Mentés** lehetőséget.

### <a name="view-fraud-reports"></a>Csalási jelentések megtekintése

Válassza ki **Azure Active Directory** a  >  **bejelentkezések**  >  **hitelesítésének részleteit**. A csalási jelentés mostantól a szabványos Azure AD-beli bejelentkezési jelentés részét képezi, és az **"eredmény részletesen"** jelenik meg, mivel az MFA megtagadva, a csalási kód beírása megtörtént.
 
## <a name="notifications"></a>Értesítések

Az e-mail-értesítések akkor konfigurálhatók, ha a felhasználók csalási riasztásokat jelentenek. Ezeket az értesítéseket általában az Identity Administrators szolgáltatásba küldi a rendszer, mivel a felhasználó fiókjának hitelesítő adatait valószínűleg feltörték. Az alábbi példa bemutatja, hogyan néz ki a csalások riasztási értesítő e-mailje:

![Példa csalás riasztási értesítő e-mail-címére](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

A csalások riasztási értesítéseinek konfigurálásához hajtsa végre az alábbi beállításokat:

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **multi-Factor Authentication**  >  **értesítéseket**.
1. Adja meg a következő mezőbe felvenni kívánt e-mail-címet.
1. Meglévő e-mail-cím eltávolításához válassza a kívánt e-mail-cím melletti **...** lehetőséget, majd válassza a **Törlés** lehetőséget.
1. Ha elkészült, válassza a **Mentés** lehetőséget.

## <a name="oath-tokens"></a>OATH-jogkivonatok

Az Azure AD az eskü-TOTP SHA-1 tokenek használatát támogatja, amelyek 30 vagy 60 másodpercenként frissítik a kódokat. Az ügyfelek megvásárolhatják ezeket a jogkivonatokat a választott gyártótól.

Az eskü TOTP-tokenek általában titkos kulccsal, vagy a tokenben előre programozott magokkal rendelkeznek. Ezeket a kulcsokat az alábbi lépésekben leírtaknak megfelelően be kell állítani az Azure AD-be. A titkos kulcsok legfeljebb 128 karakterből állhatnak, amelyek nem kompatibilisek az összes jogkivonattal. A titkos kulcs csak az *a-z* , a-z és *a-z* , valamint a *1-7* karakterből állhat, és a *Base32*-ben kell kódolni.

A feldolgozható, programozható eskü TOTP a szoftveres jogkivonat telepítési folyamatában is beállítható az Azure AD-ben.

A nyilvános előzetes verzió részeként a rendszer a hardveres jogkivonatokat is támogatja. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![ESKÜ-tokenek feltöltése az MFA-eskü tokenek paneljére](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

A jogkivonatok beszerzése után a következő példában látható módon fel kell tölteni egy vesszővel tagolt (CSV) fájlformátumot, beleértve az UPN-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ügyeljen rá, hogy a fejlécsor szerepeljen a CSV-fájlban.

Miután megfelelően formázott CSV-fájlként, a rendszergazda bejelentkezhet a Azure Portalba, és megnyithatja **Azure Active Directory > biztonsági > MFA > eskü-tokeneket**, és feltöltheti az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően a folyamat eltarthat néhány percig. Kattintson a **frissítés** gombra az aktuális állapot lekéréséhez. Ha bármilyen hiba van a fájlban, letöltheti azt a CSV-fájlt, amely felsorolja a feloldható hibákat. A letöltött CSV-fájl mezőinek neve eltér a feltöltött verziótól.

A hibák elhárítása után a rendszergazda ezután aktiválhatja az egyes kulcsokat, ha a jogkivonat **aktiválása** elemre kattint, és beírja a tokenen megjelenő OTP elemet.

Előfordulhat, hogy a felhasználók legfeljebb öt ESKÜvel rendelkező hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást) kombinálnak, amelyet bármikor használatra konfiguráltak.

## <a name="phone-call-settings"></a>Telefonhívás beállításai

Ha a felhasználók telefonhívásokat kapnak az MFA-kérésekhez, konfigurálhatja a felhasználói élményt, például a hívó AZONOSÍTÓját vagy a hang üdvözlését.

Ha a Egyesült Államok nem konfigurálta az MFA-hívó AZONOSÍTÓját, a Microsoft hanghívásai a következő számokból származnak. Ha levélszemét-szűrőket használ, ügyeljen arra, hogy kizárják ezeket a számokat:

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> Ha az Azure AD-Multi-Factor Authentication hívások a nyilvános telefonos hálózaton keresztül vannak elhelyezve, a hívások néha egy olyan szolgáltatón keresztül jutnak át, amely nem támogatja a hívó AZONOSÍTÓját. Emiatt a hívó azonosítója nem garantált, noha az Azure AD Multi-Factor Authentication mindig elküldi. Ez mind a telefonhívásokra, mind az Azure AD Multi-Factor Authentication által biztosított szöveges üzenetekre vonatkozik. Ha ellenőriznie kell, hogy egy szöveges üzenet az Azure AD-Multi-Factor Authenticationból származik-e, tekintse meg, hogy [milyen SMS-eket használnak az üzenetek küldéséhez?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

A saját hívóazonosító számának konfigurálásához hajtsa végre a következő lépéseket:

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **MFA**  >  **telefonhívási beállításait**.
1. Állítsa be az **MFA-HÍVÓ azonosítójának számát** arra a számra, amelyet a felhasználók a telefonon látni kívánnak. Csak az USA-alapú számok engedélyezettek.
1. Ha elkészült, válassza a **Mentés** lehetőséget.

### <a name="custom-voice-messages"></a>Egyéni hangüzenetek

Az Azure AD Multi-Factor Authentication saját felvételeit vagy üdvözléseit az egyéni hangüzenetek funkcióval is használhatja. Ezek az üzenetek a vagy az alapértelmezett Microsoft-felvételek helyett is használhatók.

Mielőtt elkezdené, vegye figyelembe a következő korlátozásokat:

* A támogatott fájlformátumok: *. wav* és *. mp3*.
* A fájlméret korlátja 1 MB.
* A hitelesítési üzeneteknek 20 másodpercnél rövidebbnek kell lenniük. A 20 másodpercnél hosszabb üzenetek esetében az ellenőrzés sikertelen lehet. Előfordulhat, hogy a felhasználó nem válaszol az üzenet befejeződése előtt, és a hitelesítés időtúllépést okoz.

### <a name="custom-message-language-behavior"></a>Egyéni üzenet nyelvi viselkedése

Ha egy egyéni hangüzenet jelenik meg a felhasználó számára, az üzenet nyelve az alábbi tényezőktől függ:

* Az aktuális felhasználó nyelve.
  * A felhasználó böngészőjében észlelt nyelv.
  * Más hitelesítési forgatókönyvek különbözőképpen működhetnek.
* Az elérhető egyéni üzenetek nyelve.
  * Ezt a nyelvet a rendszergazda választja ki egyéni üzenet hozzáadásakor.

Ha például csak egy egyéni üzenet van, a német nyelvvel:

* A német nyelven hitelesítő felhasználó az egyéni német üzenetet fogja hallani.
* Az angol nyelvű hitelesítéssel rendelkező felhasználó a standard angol nyelvű üzenetet fogja hallani.

### <a name="custom-voice-message-defaults"></a>Egyéni hangüzenetek alapértelmezett értékei

A következő minta-parancsfájlok használatával hozhatja létre saját egyéni üzeneteit. Ezek a kifejezések az alapértelmezett értékek, ha nem konfigurálja a saját egyéni üzeneteit:

| Üzenet neve | Script |
| --- | --- |
| A hitelesítés sikerült | A bejelentkezés ellenőrzése sikerült. viszlát. |
| Bővítmény kérése | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. A folytatáshoz nyomja le a font billentyűt. |
| Csalás megerősítése | A rendszer csalási riasztást küldött. A fiók zárolásának feloldásához forduljon a vállalat informatikai ügyfélszolgálatához. |
| Csalási üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. Ha nem kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbál hozzáférni a fiókjához. A csalási riasztás beküldéséhez nyomja le a nulla kilót. Ez értesíti a vállalat informatikai csapatát, és blokkolja a további ellenőrzési kísérleteket. |
| Csalást jelentett, csalási riasztást küldtek. | A fiók zárolásának feloldásához forduljon a vállalat informatikai ügyfélszolgálatához. |
| Aktiválás | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Hitelesítés megtagadva – újrapróbálkozás | Ellenőrzés megtagadva. |
| Újrapróbálkozás (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. |
| Visszaélési üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja.  Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. Ha nem kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbál hozzáférni a fiókjához. A csalási riasztás beküldéséhez nyomja le a nulla kilót. Ez értesíti a vállalat informatikai csapatát, és blokkolja a további ellenőrzési kísérleteket. |
| Újrapróbálkozás (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. |
| Bővítmény Rákérdezés számjegyek után | Ha már ezen a bővítményen van, a folytatáshoz nyomja le a font billentyűt. |
| Hitelesítés megtagadva | Sajnos nem lehet bejelentkezni. Próbálkozzon újra később. |
| Aktiválási üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Aktiválás újrapróbálkozás (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Aktiválási üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. |
| Kiterjesztés kérése számjegyek előtt | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Vigye át ezt a hívást a bővítménybe... |

### <a name="set-up-a-custom-message"></a>Egyéni üzenet beállítása

A saját egyéni üzeneteinek használatához hajtsa végre a következő lépéseket:

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **MFA**  >  **telefonhívási beállításait**.
1. Válassza az **üdvözlés hozzáadása** lehetőséget.
1. Válassza ki az üdvözlés **típusát** *(például üdvözlés (standard)* vagy  *sikeres hitelesítés*).
1. Válassza ki a **nyelvet**, amely az [Egyéni üzenet nyelvi viselkedésének](#custom-message-language-behavior)előző szakaszán alapul.
1. Tallózással keresse meg és válassza ki a feltölteni kívánt *. mp3* vagy *. wav* hangfájl-fájlt.
1. Ha elkészült, válassza a **Hozzáadás**, majd a **Mentés** lehetőséget.

## <a name="mfa-service-settings"></a>MFA-szolgáltatás beállításai

Az alkalmazások jelszavainak, megbízható IP-címeinek, ellenőrzési lehetőségeinek és az Azure AD Multi-Factor Authentication többtényezős hitelesítésének megjegyzései megtalálhatók a szolgáltatás beállításai között. Ez inkább egy örökölt portál, amely nem része a normál Azure AD-portálnak.

A szolgáltatás beállításai a Azure Portal érhetők el, ha megkeresi az  >    >    >    >    >  **új felhőalapú MFA-beállítások** konfigurálására szolgáló Azure Active Directory biztonsági MFA első lépéseit. Megnyílik egy új ablak vagy lap a *Szolgáltatásbeállítások* további beállításaival.

## <a name="trusted-ips"></a>Megbízható IP-címek

Az Azure AD Multi-Factor Authentication _megbízható IP_ -címei szolgáltatás megkerüli a többtényezős hitelesítési kéréseket a megadott IP-címtartományból bejelentkező felhasználók számára. A helyszíni környezetekhez beállíthatja a megbízható IP-tartományokat, így ha a felhasználók ezen helyek egyikén vannak, akkor nincs szükség Azure AD-Multi-Factor Authenticationra. Az Azure AD Multi-Factor Authentication _megbízható IP_ -címei funkciójának prémium szintű Azure ad P1 kiadásra van szüksége. 

> [!NOTE]
> A megbízható IP-címek csak az MFA-kiszolgáló használata esetén tartalmazhatnak privát IP-tartományokat. Felhőalapú Azure AD-Multi-Factor Authentication esetén csak nyilvános IP-címtartományok használhatók.
>
> Az IPv6-tartományok csak a [nevesített hely (előzetes verzió)](../conditional-access/location-condition.md) felületén támogatottak.

Ha a szervezete telepíti a hálózati házirend-kiszolgáló bővítményét, hogy az MFA-t a helyszíni alkalmazások számára biztosítsa, jegyezze fel, hogy a forrás IP-cím mindig az NPS-kiszolgáló lesz, amely a hitelesítési kísérleten átfolyik.

| Azure AD-bérlő típusa | Megbízható IP-szolgáltatás beállításai |
|:--- |:--- |
| Felügyelt |**IP-címek meghatározott tartománya**: a rendszergazdák olyan IP-címtartományt határoznak meg, amely elkerülheti a többtényezős hitelesítést a vállalati intranetről bejelentkező felhasználók számára. Legfeljebb 50 megbízható IP-tartományt lehet konfigurálni.|
| Összevont |**Minden összevont felhasználó**: a szervezeten belülről bejelentkező összes összevont felhasználó kihagyhatja a többtényezős hitelesítést. A felhasználók megkerülik az ellenőrzést Active Directory összevonási szolgáltatások (AD FS) (AD FS) által kiállított jogcímek használatával.<br/>**IP-címek meghatározott tartománya**: a rendszergazdák olyan IP-címtartományt határoznak meg, amely elkerülheti a többtényezős hitelesítést a vállalati intranetről bejelentkező felhasználók számára. |

A megbízható IP-megkerülés csak a vállalati intraneten belülről működik. Ha a **minden összevont felhasználó** lehetőséget választja, és a felhasználó a vállalati intraneten kívülről jelentkezik be, a felhasználónak a multi-Factor Authentication használatával kell hitelesítenie magát. A folyamat akkor is ugyanaz, ha a felhasználó AD FS jogcímet jelent.

### <a name="end-user-experience-inside-of-corpnet"></a>Végfelhasználói élmény a Corpnet-on belül

Ha a megbízható IP-címek funkció le van tiltva, a böngésző folyamataihoz többtényezős hitelesítés szükséges. Az alkalmazások jelszavai a régebbi, gazdag ügyfélalkalmazások esetében szükségesek.

A megbízható IP-címek használata esetén a többtényezős hitelesítés nem szükséges a böngésző folyamataihoz. Az alkalmazás jelszavai nem szükségesek a régebbi, Rich ügyfélalkalmazások számára, ha a felhasználó nem hozott létre alkalmazás-jelszót. Ha egy alkalmazás jelszava használatban van, a jelszót is meg kell adni.

### <a name="end-user-experience-outside-corpnet"></a>Végfelhasználói élmény a Corpnet-en kívül

Függetlenül attól, hogy a megbízható IP-címek meg vannak-e adva, a többtényezős hitelesítés szükséges a böngésző folyamataihoz. Az alkalmazások jelszavai a régebbi, gazdag ügyfélalkalmazások esetében szükségesek.

### <a name="enable-named-locations-by-using-conditional-access"></a>Elnevezett helyszínek engedélyezése feltételes hozzáférés használatával

A következő lépések végrehajtásával feltételes hozzáférési szabályokat használhat a nevesített helyszínek definiálásához:

1. A Azure Portal keresse meg és válassza ki a **Azure Active Directory** elemet, majd keresse meg a **biztonsági**  >  **feltételes hozzáférés**  >  **elnevezett helyeinek nevét**.
1. Válassza az **új hely** lehetőséget.
1. Adja meg a hely nevét.
1. Válassza **a megjelölés megbízható helyként** lehetőséget.
1. Adja meg az IP-címtartományt CIDR-jelöléssel a környezethez, például *40.77.182.32/27*.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>A megbízható IP-címek funkciójának engedélyezése feltételes hozzáférés használatával

A megbízható IP-címek feltételes hozzáférési házirendek használatával történő engedélyezéséhez hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki a **Azure Active Directory** elemet, majd keresse meg a **biztonsági**  >   **feltételes hozzáférés**  >  **elnevezett helyeinek nevét**.
1. Válassza az **MFA megbízható IP**-címek konfigurálása lehetőséget.
1. A **Szolgáltatásbeállítások** oldalon, a **megbízható IP**-címek területen válasszon a következő két lehetőség közül:

   * Az **intranetről származó összevont felhasználóktól érkező kérelmek esetén**: Ha ezt a beállítást szeretné választani, jelölje be a jelölőnégyzetet. Minden összevont felhasználó, aki bejelentkezik a vállalati hálózatról a többtényezős hitelesítés megkerülésével, AD FS által kiállított jogcímet használva. Győződjön meg arról, hogy AD FS rendelkezik olyan szabállyal, amely az intranetes jogcímet hozzáadja a megfelelő adatforgalomhoz. Ha a szabály nem létezik, hozza létre a következő szabályt a AD FSban:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * A **nyilvános IP-címek meghatározott tartományának kéréseire**: Ha ezt a lehetőséget választja, adja meg az IP-címeket a szövegmezőben a CIDR-jelölés használatával.
      * Az xxx. xxx. xxx. 1 és XXX. xxx. xxx. 254 tartományba tartozó IP-címek esetében használja az alábbi jelölést: **xxx. xxx. xxx. 0/24**.
      * Egyetlen IP-cím esetén használjon olyan jelölést, mint a **xxx.xxx.xxx.xxx/32**.
      * Akár 50 IP-címtartományt is megadhat. Azok a felhasználók, akik ezen IP-címekről jelentkeznek be, megkerülik a többtényezős hitelesítést.

1. Kattintson a **Mentés** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>A megbízható IP-címek funkció engedélyezése a szolgáltatás beállításai alapján

Ha nem kíván feltételes hozzáférési szabályzatokat használni a megbízható IP-címek engedélyezéséhez, az alábbi lépések végrehajtásával konfigurálhatja az Azure AD Multi-Factor Authentication *szolgáltatás beállításait* :

1. A Azure Portal keresse meg, majd válassza a **Azure Active Directory**, majd a **felhasználók** lehetőséget.
1. Válassza a **Multi-Factor Authentication** lehetőséget.
1. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai** elemet.
1. A **Szolgáltatásbeállítások** oldalon, a **megbízható IP**-címek területen válasszon egyet (vagy mindkettőt) a következő két lehetőség közül:

   * Az **intranetes összevont felhasználóktól érkező kérések esetén**: Ha ezt a beállítást szeretné választani, jelölje be a jelölőnégyzetet. Minden összevont felhasználó, aki bejelentkezik a vállalati hálózatról a többtényezős hitelesítés megkerülésével, AD FS által kiállított jogcímet használva. Győződjön meg arról, hogy AD FS rendelkezik olyan szabállyal, amely az intranetes jogcímet hozzáadja a megfelelő adatforgalomhoz. Ha a szabály nem létezik, hozza létre a következő szabályt a AD FSban:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * Az **IP-alhálózatok megadott tartományának kérései esetén**: Ha ezt a lehetőséget választja, adja meg az IP-címeket a szövegmezőben a CIDR-jelölés használatával.
      * Az xxx. xxx. xxx. 1 és XXX. xxx. xxx. 254 tartományba tartozó IP-címek esetében használja az alábbi jelölést: **xxx. xxx. xxx. 0/24**.
      * Egyetlen IP-cím esetén használjon olyan jelölést, mint a **xxx.xxx.xxx.xxx/32**.
      * Akár 50 IP-címtartományt is megadhat. Azok a felhasználók, akik ezen IP-címekről jelentkeznek be, megkerülik a többtényezős hitelesítést.

1. Kattintson a **Mentés** gombra.

## <a name="verification-methods"></a>Ellenőrzési módszerek

A Service Settings portálon megadhatja a felhasználók számára elérhető ellenőrzési módszereket. Amikor a felhasználók regisztrálják az Azure AD-Multi-Factor Authenticationhoz tartozó fiókjaikat, az Ön által engedélyezett beállítások alapján választják ki az előnyben részesített ellenőrzési módszert. A felhasználó beléptetési folyamatával kapcsolatos útmutatást a [saját fiók beállítása a multi-Factor Authentication szolgáltatáshoz](../user-help/multi-factor-authentication-end-user-first-time.md)című témakörben talál.

A következő ellenőrzési módszerek érhetők el:

| Metódus | Leírás |
|:--- |:--- |
| Telefonos hívás |Automatikus hanghívást helyez el. A felhasználó fogadja a hívást, majd a hitelesítéshez lenyomja a telefon billentyűzetén a # gombot.  A telefonszám nincs szinkronizálva a helyszíni Active Directory. |
| SMS-üzenet a telefonra |Egy ellenőrző kódot tartalmazó szöveges üzenetet küld. A rendszer felszólítja a felhasználót, hogy adja meg az ellenőrző kódot a bejelentkezési felületen. Ezt a folyamatot egy egyirányú SMS-nek nevezzük. A kétirányú SMS azt jelenti, hogy a felhasználónak egy adott kódot kell visszaadnia. A kétirányú SMS elavult, és 2018 november 14. után nem támogatott. A rendszergazdáknak engedélyezniük kell egy másik módszert azon felhasználók számára, akik korábban a kétirányú SMS-t használták.|
| Értesítés a Mobile App használatával |Leküldéses értesítést küld a telefonra vagy a regisztrált eszközre. A felhasználó megtekinti az értesítést, **és kiválasztja az ellenőrzés** befejezését. A Microsoft Authenticator alkalmazás [Windows Phone-telefon](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)rendszerhez érhető el. |
| Ellenőrző kód a Mobile App vagy a Hardware tokenből |A Microsoft Authenticator alkalmazás 30 másodpercenként létrehoz egy új eskü-ellenőrző kódot. A felhasználó beírja az ellenőrző kódot a bejelentkezési felületre. A Microsoft Authenticator alkalmazás [Windows Phone-telefon](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)rendszerhez érhető el. |

További információ: [Milyen hitelesítési és ellenőrzési módszerek érhetők el az Azure ad-ben?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Ellenőrzési módszerek engedélyezése és letiltása

Az ellenőrzési módszerek engedélyezéséhez vagy letiltásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg, majd válassza a **Azure Active Directory**, majd a **felhasználók** lehetőséget.
1. Válassza a **Multi-Factor Authentication** lehetőséget.
1. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai** elemet.
1. A **Szolgáltatásbeállítások** lapon az **ellenőrzési beállítások** területen válassza ki/törölje a felhasználók számára megadható metódusokat.
1. Kattintson a **Mentés** gombra.

## <a name="remember-multi-factor-authentication"></a>Ne feledje Multi-Factor Authentication

A _multi-Factor Authentication megjegyzése_ funkció lehetővé teszi, hogy a felhasználók a megadott számú napig is megkerüljék a további ellenőrzéseket, miután az multi-Factor Authentication használatával sikeresen bejelentkezett az eszközre. A használhatóság növeléséhez és a felhasználók azon számának minimalizálásához, amikor egy felhasználónak az MFA-t ugyanazon az eszközön kell végrehajtania, válasszon egy 90 napos vagy annál hosszabb időtartamot.

> [!IMPORTANT]
> Ha egy fiók vagy eszköz biztonsága sérül, a megbízható eszközök Multi-Factor Authenticationának megjegyzése hatással lehet a biztonságra. Ha egy vállalati fiók biztonsága sérül, vagy egy megbízható eszköz elveszett vagy ellopták, akkor [vissza kell vonnia az MFA-munkameneteket](howto-mfa-userdevicesettings.md).
>
> A visszaállítási művelet visszavonja a megbízható állapotot az összes eszközről, és a felhasználónak újra kell végeznie a többtényezős hitelesítést. Azt is megteheti, hogy a felhasználók a saját eszközökön is visszaállítják Multi-Factor Authentication a [beállítások kezelése a többtényezős hitelesítéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)című részében leírtak szerint.

### <a name="how-the-feature-works"></a>A szolgáltatás működése

A ne feledje Multi-Factor Authentication szolgáltatás állandó cookie-t állít be a böngészőben, ha a felhasználó a bejelentkezéskor a **ne Kérdezzen újra X napra** lehetőséget választja. A felhasználónak nem kell ismét megadnia Multi-Factor Authentication ugyanezen böngészőből, amíg a cookie le nem jár. Ha a felhasználó egy másik böngészőt nyit meg ugyanazon az eszközön, vagy törli a cookie-kat, a rendszer ismét kérni fogja az ellenőrzésre.

A **ne jelenjen meg többé az X nap** beállítás nem jelenik meg a böngészőn kívüli alkalmazásokon, függetlenül attól, hogy az alkalmazás támogatja-e a modern hitelesítést. Ezek az alkalmazások a _frissítési jogkivonatokat_ használják, amelyek óránként új hozzáférési jogkivonatokat biztosítanak. A frissítési jogkivonat ellenőrzésekor az Azure AD ellenőrzi, hogy a legutóbbi többtényezős hitelesítés a megadott számú napon belül történt-e.

A szolgáltatás csökkenti a webes alkalmazások hitelesítésének számát, ami általában minden alkalommal felgyorsul. A szolgáltatás növelheti a modern hitelesítési ügyfelek hitelesítésének számát, amelyek általában 90 naponta kérik le, ha alacsonyabb időtartam van beállítva. A feltételes hozzáférési szabályzatokkal kombinálva növelheti a hitelesítések számát is.

> [!IMPORTANT]
> A **Ne feledje multi-Factor Authentication** funkció nem kompatibilis a AD FS **bejelentkezett** szolgáltatásával, amikor a felhasználók többtényezős AD FS hitelesítést végeznek az Azure multi-Factor Authentication-kiszolgáló vagy egy harmadik féltől származó multi-Factor Authentication megoldáson keresztül.
>
> Ha a felhasználók a AD FS **bejelentkezve szeretnék megtartani** az eszközt, és a multi-Factor Authentication számára is megbízhatóként jelölik meg az eszközét, a felhasználó nem lesz automatikusan ellenőrizve a **multi-Factor Authentication számának megjegyzése** lejár. Az Azure AD új többtényezős hitelesítést kér, de AD FS visszaadja az eredeti Multi-Factor Authentication jogcím és dátum típusú tokent, nem pedig a multi-Factor Authentication ismételt elküldése helyett. **Ez a reakció ellenőrző hurkot állít be az Azure AD és a AD FS között.**
>
> A **megjegyzés multi-Factor Authentication** funkció nem kompatibilis a B2B-felhasználókkal, és nem lesz látható a B2B-felhasználók számára a meghívott bérlők bejelentkezésekor.
>

### <a name="enable-remember-multi-factor-authentication"></a>Megjegyzés engedélyezése Multi-Factor Authentication

A következő lépések végrehajtásával engedélyezheti és konfigurálhatja, hogy a felhasználók megjegyezzenek az MFA-állapotukat, és megkerüljék a kéréseket:

1. A Azure Portal keresse meg, majd válassza a **Azure Active Directory**, majd a **felhasználók** lehetőséget.
1. Válassza a **Multi-Factor Authentication** lehetőséget.
1. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai** elemet.
1. A **Szolgáltatásbeállítások** lapon, a **többtényezős hitelesítés megjegyzése** területen jelölje be a **többtényezős hitelesítés megadásának engedélyezése a felhasználóknak a megbízható eszközökön** jelölőnégyzetet.
1. Állítsa be, hogy a rendszer hány napig engedélyezze a megbízható eszközök számára a többtényezős hitelesítés megkerülését. Az optimális felhasználói élmény érdekében az időtartamot *90* vagy több napra kell kiterjeszteni.
1. Kattintson a **Mentés** gombra.

### <a name="mark-a-device-as-trusted"></a>Eszköz megjelölése megbízhatóként

Miután engedélyezte a Megjegyzés Multi-Factor Authentication funkciót, a felhasználók a bejelentkezéskor megbízhatóként jelölhetik meg az eszközöket, ha bejelöli a **ne Kérdezzen rá újra** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure AD-Multi-Factor Authentication használható módszerekről, tekintse meg a [Azure Active Directory-ben elérhető hitelesítési és ellenőrzési módszereket?](concept-authentication-methods.md)

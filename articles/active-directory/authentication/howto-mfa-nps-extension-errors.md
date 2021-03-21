---
title: Az Azure AD MFA NPS bővítmény hibaelhárítása – Azure Active Directory
description: Segítség kérése az Azure AD-hez készült NPS-bővítménnyel kapcsolatos problémák megoldásához Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: fa3a2366f007ff1481e7c84f049e606586392037
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742952"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-ad-multi-factor-authentication"></a>Hibák elhárítása az Azure AD-hez tartozó NPS-bővítményből Multi-Factor Authentication

Ha az Azure AD Multi-Factor Authentication hálózati házirend-bővítményével kapcsolatos hibákba ütközik, ebben a cikkben gyorsan elérheti a felbontást. A hálózati házirend-kiszolgáló bővítmény naplófájljai a Eseménynapló **egyéni nézetek**  >  **kiszolgálói szerepkörök**  >  **hálózati házirend-és elérési szolgáltatások** részében találhatók azon a kiszolgálón, amelyen a hálózati házirend-bővítmény telepítve van.

## <a name="troubleshooting-steps-for-common-errors"></a>Gyakori hibák elhárítási lépései

| Hibakód | Hibaelhárítási lépések |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Forduljon az ügyfélszolgálathoz](#contact-microsoft-support), és nevezze el a naplók összegyűjtéséhez szükséges lépések listáját. Annyi információt adjon meg, amely a hiba előtt történt, beleértve a bérlői azonosítót és az egyszerű felhasználónevet (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Előfordulhat, hogy az ügyféltanúsítvány telepítésének vagy a bérlőhöz való hozzárendelésének problémája lehet. Az ügyfél-tanúsítványokkal kapcsolatos problémák kivizsgálásához kövesse az [MFA NPS-bővítmény hibáinak elhárítása című](howto-mfa-nps-extension.md#troubleshooting) témakör utasításait. |
| **ESTS_TOKEN_ERROR** | Az ügyfél-tanúsítvány és az ADAL token problémáinak vizsgálatához kövesse az [MFA NPS-bővítmény hibáinak elhárítása](howto-mfa-nps-extension.md#troubleshooting) című témakör utasításait. |
| **HTTPS_COMMUNICATION_ERROR** | Az NPS-kiszolgáló nem tud válaszokat kapni az Azure AD MFA-ból. Győződjön meg arról, hogy a tűzfalak a és a rendszer felé irányuló forgalom kétirányú megnyitása https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | A hálózati házirend-kiszolgálót futtató kiszolgálón ellenőrizze, hogy elérhető-e a  `https://adnotifications.windowsazure.com` és a `https://login.microsoftonline.com/` . Ha ezek a helyek nem töltődnek be, akkor hárítsa el a kapcsolódást az adott kiszolgálón. |
| **NPS-bővítmény az Azure AD MFA-hoz:** <br> Az Azure AD MFA NPS-bővítménye csak a AccessAccept állapotban lévő RADIUS-kérelmek másodlagos hitelesítését hajtja végre. A válasz állapot AccessReject rendelkező felhasználói felhasználónévre vonatkozó kérelem érkezett, figyelmen kívül hagyva a kérést. | Ez a hiba általában egy hitelesítési hibát jelez az AD-ben, illetve azt, hogy az NPS-kiszolgáló nem tud válaszokat kapni az Azure AD-től. Győződjön meg arról, hogy a tűzfalak a `https://adnotifications.windowsazure.com` `https://login.microsoftonline.com` 80-es és a 443-es porton keresztüli és onnan érkező, illetve onnan érkező és onnan érkező forgalom számára vannak Azt is fontos ellenőrizni, hogy a hálózati hozzáférési engedélyek Betárcsázás lapján a beállítás "hozzáférés vezérlése az NPS-hálózati házirend alapján" értékre van-e állítva. Ez a hiba akkor is aktiválható, ha a felhasználóhoz nincs hozzárendelve licenc. |
| **REGISTRY_CONFIG_ERROR** | Hiányzik egy kulcs az alkalmazás beállításjegyzékében, ennek oka az lehet, hogy a [PowerShell-parancsfájlt](howto-mfa-nps-extension.md#install-the-nps-extension) a telepítés után nem futtatták. A hibaüzenetnek tartalmaznia kell a hiányzó kulcsot. Győződjön meg arról, hogy a kulcs a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa alatt van. |
| **REQUEST_FORMAT_ERROR** <br> A RADIUS-kérelemből hiányzik a kötelező RADIUS-userName\Identifier attribútum. A hálózati házirend-kiszolgáló RADIUS-kérelmek fogadásának ellenőrzése | Ez a hiba általában egy telepítési problémát tükröz. A hálózati házirend-kiszolgáló bővítményét telepíteni kell a RADIUS-kérelmeket fogadó NPS-kiszolgálókon. Azok a hálózati házirend-kiszolgálók, amelyek függőségként vannak telepítve a RDG és az RRAS szolgáltatáshoz, nem kapják meg a RADIUS-kéréseket. A hálózati házirend-kiszolgáló bővítmény nem működik, ha az ilyen telepítések és hibák miatt nem tudja olvasni a hitelesítési kérelem részleteit. |
| **REQUEST_MISSING_CODE** | Győződjön meg arról, hogy a hálózati házirend-kiszolgáló és a NAS kiszolgálók közötti jelszó-titkosítási protokoll támogatja a másodlagos hitelesítési módszert, amelyet használ. A **pap** az Azure ad MFA összes hitelesítési módszerét támogatja a felhőben: telefonhívás, egyirányú szöveges üzenet, Mobile App Notification és Mobile App ellenőrző kód. A **CHAPv2** és az **EAP** támogatja a telefonhívást és a Mobile apps-értesítést. |
| **USERNAME_CANONICALIZATION_ERROR** | Ellenőrizze, hogy a felhasználó szerepel-e a helyszíni Active Directory-példányban, és hogy a hálózati házirend-kiszolgáló rendelkezik-e jogosultságokkal a címtár eléréséhez. Ha erdők közötti megbízhatósági kapcsolatot használ, további segítségért [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) . |

### <a name="alternate-login-id-errors"></a>Másodlagos bejelentkezési AZONOSÍTÓval kapcsolatos hibák

| Hibakód | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: a userObjectSid keresése nem sikerült | Ellenőrizze, hogy a felhasználó létezik-e a helyszíni Active Directory-példányban. Ha erdők közötti megbízhatósági kapcsolatot használ, további segítségért [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) . |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: nem sikerült a másodlagos LoginId keresése | Ellenőrizze, hogy a LDAP_ALTERNATE_LOGINID_ATTRIBUTE [érvényes Active Directory-attribútumra](/windows/win32/adschema/attributes-all)van-e beállítva. <br><br> Ha LDAP_FORCE_GLOBAL_CATALOG értéke TRUE (igaz), vagy a LDAP_LOOKUP_FORESTS nem üres értékkel van konfigurálva, ellenőrizze, hogy a globális katalógust konfigurálta-e, és hogy a AlternateLoginId attribútum hozzá van-e adva. <br><br> Ha LDAP_LOOKUP_FORESTS nem üres értékre van konfigurálva, ellenőrizze, hogy helyes-e az érték. Ha egynél több erdő neve van, a neveket pontosvesszővel kell elválasztani, nem szóközöket. <br><br> Ha ezek a lépések nem javítják a problémát, további segítségért [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) . |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: a másodlagos LoginId értéke üres | Ellenőrizze, hogy a AlternateLoginId attribútum konfigurálva van-e a felhasználó számára. |

## <a name="errors-your-users-may-encounter"></a>A felhasználók által észlelt hibák

| Hibakód | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | A hívó bérlője nem rendelkezik hozzáférési engedélyekkel a felhasználó hitelesítéséhez | Győződjön meg arról, hogy a bérlő tartománya és az egyszerű felhasználónév (UPN) tartománya azonos-e. Győződjön meg például arról, hogy user@contoso.com a a contoso-bérlőre próbál hitelesíteni. Az UPN az Azure-beli Bérlő érvényes felhasználóját jelöli. |
| **AuthenticationMethodNotConfigured** | A megadott hitelesítési módszer nincs konfigurálva a felhasználóhoz. | A felhasználó a [beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md)című részben leírtak szerint adja hozzá vagy ellenőrizze az ellenőrzési módszereiket. |
| **AuthenticationMethodNotSupported** | A megadott hitelesítési módszer nem támogatott. | Gyűjtse össze az összes olyan naplót, amely tartalmazza ezt a hibát, és [forduljon az ügyfélszolgálathoz](#contact-microsoft-support). Ha kapcsolatba lép a támogatási szolgálattal, adja meg a hibát kiváltó felhasználónevet és másodlagos ellenőrzési módszert. |
| **BecAccessDenied** | A MSODS Bec-hívás visszautasította a hozzáférést, valószínűleg a Felhasználónév nincs definiálva a bérlőben | A felhasználó a helyszíni Active Directoryban található, de az AD-kapcsolat nem szinkronizálja az Azure AD-be. Vagy a felhasználó hiányzik a bérlőhöz. Adja hozzá a felhasználót az Azure AD-hez, és adja hozzá az ellenőrzési módszereiket a [beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md)című részben leírtaknak megfelelően. |
| **InvalidFormat** vagy **StrongAuthenticationServiceInvalidParameter** | A telefonszám nem felismerhető formátumban van | Állítsa be a felhasználó számára az ellenőrző telefonszámok helyességét. |
| **InvalidSession** | A megadott munkamenet érvénytelen vagy lejárt | A munkamenet végrehajtása több mint három percet vett igénybe. Győződjön meg arról, hogy a felhasználó beírja az ellenőrző kódot, vagy válaszol az alkalmazás értesítésére a hitelesítési kérés kezdeményezését követő három percen belül. Ha ez nem oldja meg a problémát, ellenőrizze, hogy nincsenek-e hálózati késések az ügyfél, a NAS-kiszolgáló, a hálózati házirend-kiszolgáló és az Azure AD MFA-végpont között.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Nincs beállítva alapértelmezett hitelesítési módszer a felhasználóhoz | A felhasználó a [beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md)című részben leírtak szerint adja hozzá vagy ellenőrizze az ellenőrzési módszereiket. Győződjön meg arról, hogy a felhasználó az alapértelmezett hitelesítési módszert választotta, és konfigurálta a fiókját. |
| **OathCodePinIncorrect** | Helytelen kód és PIN-kód van megadva. | Ez a hiba nem várható a hálózati házirend-kiszolgáló bővítményében. Ha a felhasználó ezt tapasztalja, [forduljon a támogatási szolgálathoz](#contact-microsoft-support) hibaelhárítási segítségért. |
| **ProofDataNotFound** | Nem lett konfigurálva a megadott hitelesítési módszerhez tartozó igazoló adatbázis. | A felhasználó egy másik ellenőrzési módszert kell kipróbálnia, vagy új ellenőrzési módszereket kell megadnia a [beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md)című részben leírtak szerint. Ha a felhasználó továbbra is ezt a hibát látja, miután meggyőződött róla, hogy az ellenőrzési módszer helyesen van beállítva, [forduljon az ügyfélszolgálathoz](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Helytelen kód és PIN-kód van megadva. (OneWaySMS) | Ez a hiba nem várható a hálózati házirend-kiszolgáló bővítményében. Ha a felhasználó ezt tapasztalja, [forduljon a támogatási szolgálathoz](#contact-microsoft-support) hibaelhárítási segítségért. |
| **TenantIsBlocked** | A bérlő blokkolva van | [Forduljon az ügyfélszolgálathoz](#contact-microsoft-support) a *bérlői azonosítóval* a Azure Portal Azure ad Tulajdonságok lapján. |
| **UserNotFound** | A megadott felhasználó nem található | A bérlő már nem látható aktívként az Azure AD-ben. Győződjön meg arról, hogy az előfizetése aktív, és rendelkezik a szükséges első féltől származó alkalmazásokkal. Győződjön meg arról is, hogy a tanúsítvány tulajdonosának bérlője a várt módon van-e, és a tanúsítvány továbbra is érvényes és regisztrálva van az egyszerű szolgáltatásban. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>A felhasználók által esetlegesen előforduló üzenetek

Időnként előfordulhat, hogy a felhasználók Multi-Factor Authentication üzeneteket kapnak, mert a hitelesítési kérelem sikertelen volt. Ezek nem hibák a konfiguráció termékében, de szándékos figyelmeztetések, amelyek elmagyarázzák a hitelesítési kérelem elutasításának okát.

| Hibakód | Hibaüzenet | Javasolt lépések |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Helytelen a kód entered\OATH kódja | A felhasználó érvénytelen kódot adott meg. Próbálkozzon újra egy új kód igénylésével vagy a bejelentkezés újbóli megkeresésével. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Elérte az újrapróbálkozások maximális megengedett kódját | A felhasználó túl sokszor nem tudta megerősíteni az ellenőrzési feladatot. A beállításoktól függően előfordulhat, hogy a rendszergazdának most fel kell oldania a tiltást.  |
| **SMSAuthFailedWrongCodeEntered** | Helytelen kód van megadva/szöveges üzenet, OTP helytelen | A felhasználó érvénytelen kódot adott meg. Próbálkozzon újra egy új kód igénylésével vagy a bejelentkezés újbóli megkeresésével. |

## <a name="errors-that-require-support"></a>Támogatásra szoruló hibák

Ha ezen hibák valamelyikével találkozik, javasoljuk, hogy forduljon az [ügyfélszolgálathoz](#contact-microsoft-support) a diagnosztikai súgóhoz. Nincs olyan szabványos lépés, amely képes kezelni ezeket a hibákat. Ha felveszi a kapcsolatot a támogatási szolgálattal, ügyeljen arra, hogy a lehető legtöbbet adja meg a hibához vezető lépéseknek és a bérlői információknak.

| Hibakód | Hibaüzenet |
| ---------- | ------------- |
| **InvalidParameter** | A kérelem nem lehet null értékű. |
| **InvalidParameter** | A ObjectId nem lehet null értékű vagy üres a ReplicationScope:{0} |
| **InvalidParameter** | A cégnév \{ 0} \ hossza hosszabb a megengedett maximális hossznál {1} |
| **InvalidParameter** | A UserPrincipalName nem lehet null értékű vagy üres. |
| **InvalidParameter** | A megadott TenantId formátuma nem megfelelő. |
| **InvalidParameter** | A munkamenet-azonosító nem lehet null értékű vagy üres. |
| **InvalidParameter** | Nem oldható fel ProofData a kérelemből vagy a Msods. A ProofData ismeretlen |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Következő lépések

### <a name="troubleshoot-user-accounts"></a>Felhasználói fiókok hibakeresése

Ha a felhasználók a [kétlépéses ellenőrzés során problémákba ütköznek](../user-help/multi-factor-authentication-end-user-troubleshoot.md), segítsen nekik öndiagnosztizálásban.

### <a name="health-check-script"></a>Állapot-ellenőrzési parancsfájl

Az [Azure ad MFA NPS bővítmény állapot-ellenőrzési parancsfájlja](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) alapszintű állapot-ellenőrzést hajt végre a hálózati házirend-kiszolgáló bővítményének hibaelhárításakor. Futtassa a szkriptet, és válassza a 3. lehetőséget.

### <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Ha további segítségre van szüksége, forduljon a támogatási szakemberekhez az [Azure multi-Factor Authentication-kiszolgáló ügyfélszolgálatán](https://support.microsoft.com/oas/default.aspx?prid=14947)keresztül. Amikor kapcsolatba lép velünk, hasznos lehet, ha a lehető legtöbb információt felveheti a probléma megoldására. A megadható információk közé tartozik az a lap, ahol a hiba, a megadott hibakód, a megadott munkamenet-azonosító, a hibát megtekintő felhasználó azonosítója és a hibakeresési naplók szerepelnek.

A támogatási diagnosztika hibakeresési naplóinak összegyűjtéséhez kövesse az alábbi lépéseket az NPS-kiszolgálón:

1. Nyissa meg a Beállításszerkesztőt, és tallózással keresse meg HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa a **VERBOSE_LOG** beállítása **igaz** értékre
2. Nyisson meg egy rendszergazdai parancssort, és futtassa a következő parancsokat:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reprodukálja a hibát

4. Állítsa le a nyomkövetést a következő parancsokkal:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Nyissa meg a Beállításszerkesztőt, és tallózással keresse meg a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** **hamis** értékre
6. Zip a C:\NPS mappa tartalmát, és csatolja a tömörített fájlt a támogatási esethez.
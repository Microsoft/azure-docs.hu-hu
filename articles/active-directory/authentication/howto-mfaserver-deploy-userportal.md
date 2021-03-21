---
title: Felhasználói portál az Azure MFA-kiszolgálóhoz – Azure Active Directory
description: Ismerkedés az Azure MFA-val és a felhasználói portállal.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61cefacc4147ab1eb105af9cb21365129dd1950e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742459"
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Felhasználói portál az Azure Multi-Factor Authentication-kiszolgálóhoz

A felhasználói portál egy IIS-webhely, ahol a felhasználók regisztrálhatnak az Azure Multi-Factor Authenticationre (MFA), és kezelhetik a fiókjaikat. A felhasználók megváltoztathatják a telefonszámukat és a PIN-kódjukat, vagy választhatják a kétlépéses ellenőrzés mellőzését a következő bejelentkezéskor.

A felhasználók a szokványos felhasználónevükkel és jelszavukkal jelentkeznek be a felhasználói portálra, majd egy kétlépéses ellenőrző hívással vagy biztonsági kérdésekre adott válaszokkal végzik el a hitelesítést. Ha a felhasználók regisztrációja engedélyezett, a felhasználók a felhasználói portálra való első bejelentkezéskor konfigurálják a telefonszámukat és PIN-kódjukat.

A felhasználói portálhoz rendszergazdák állíthatók be, akik az új felhasználók hozzáadására és a meglévő felhasználók frissítésére vonatkozó engedélyt kaphatnak.

A felhasználói portált a környezettől függően érdemes lehet az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálóra, illetve egy másik, internetkapcsolattal rendelkező kiszolgálóra telepíteni.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

![MFA-kiszolgáló felhasználói portáljának bejelentkezési lapja](./media/howto-mfaserver-deploy-userportal/portal.png)

> [!NOTE]
> A felhasználói portál csak a Multi-Factor Authentication-kiszolgálóval érhető el. Ha a Multi-Factor Authenticationt a felhőben használja, hívja fel a felhasználói figyelmét a [fiók kétlépéses ellenőrzéshez történő beállítására](../user-help/multi-factor-authentication-end-user-first-time.md) vagy a [beállítások kétlépéses ellenőrzésnél történő kezelésére](../user-help/multi-factor-authentication-end-user-manage-settings.md).

## <a name="install-the-web-service-sdk"></a>A Web Service SDK telepítése

Mindkét forgatókönyvre érvényes, hogy ha az Azure Multi-Factor Authentication Web Service SDK még **nincs** telepítve az Azure Multi-Factor Authentication- (MFA-) kiszolgálón, akkor végre kell hajtania a következő lépéseket.

1. Nyissa meg a Multi-Factor Authentication-kiszolgáló konzolját.
2. Lépjen a **Web Service SDK** részre, majd válassza a **Web Service SDK telepítése** lehetőséget.
3. Végezze el a telepítést az alapértelmezett értékekkel, hacsak nem kell valamilyen okból módosítania őket.
4. Kösse a TLS/SSL-tanúsítványt a webhelyhez az IIS-ben.

Ha kérdése van a TLS/SSL-tanúsítvány IIS-kiszolgálón való konfigurálásával kapcsolatban, tekintse meg az [SSL beállítása az IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)-ben című cikket.

A Web Service SDK-t TLS/SSL-tanúsítvánnyal kell védeni. Erre a célra megfelel egy önaláírt tanúsítvány. Importálja a tanúsítványt a felhasználói portál webkiszolgálóján lévő helyi számítógépfiók "megbízható legfelső szintű hitelesítésszolgáltatók" tárolójába, hogy az megbízhatónak minősítse ezt a tanúsítványt a TLS-kapcsolat indításakor.

![MFA-kiszolgáló konfigurálása – Web Service SDK](./media/howto-mfaserver-deploy-userportal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálón

A következő előfeltételek szükségesek ahhoz, hogy a felhasználói portált **ugyanarra a kiszolgálóra** telepítse, mint az Azure multi-Factor Authentication-kiszolgáló:

* IIS, beleértve az ASP.NET-et és az IIS 6 metabázisával való kompatibilitási funkciót (IIS 7 vagy újabb verzió esetén)
* Egy fiók rendszergazdai jogosultságokkal a számítógéphez és a tartományhoz, ha szükséges. A fióknak engedéllyel kell rendelkeznie Active Directory-biztonságicsoportok létrehozásához.
* A felhasználói portál védelme TLS/SSL-tanúsítvánnyal.
* Az Azure Multi-Factor Authentication Web Service SDK védelme TLS/SSL-tanúsítvánnyal.

A felhasználói portál üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure Multi-Factor Authentication-kiszolgáló konzolját, kattintson a bal oldali menüben lévő **Felhasználói portál** ikonra, majd kattintson a **Felhasználói portál telepítése** parancsra.
2. Végezze el a telepítést az alapértelmezett értékekkel, hacsak nem kell valamilyen okból módosítania őket.
3. TLS/SSL-tanúsítvány kötése a webhelyhez az IIS-ben

   > [!NOTE]
   > Ez a TLS/SSL-tanúsítvány általában nyilvánosan aláírt TLS/SSL-tanúsítvány.

4. Nyisson meg egy webböngészőt bármilyen számítógépről, és navigáljon arra az URL-címre, ahol a felhasználói portál telepítve van (például: `https://mfa.contoso.com/MultiFactorAuth` ). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

![MFA-kiszolgáló felhasználói portáljának telepítése](./media/howto-mfaserver-deploy-userportal/install.png)

Ha kérdése van a TLS/SSL-tanúsítvány IIS-kiszolgálón való konfigurálásával kapcsolatban, tekintse meg az [SSL beállítása az IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)-ben című cikket.

## <a name="deploy-the-user-portal-on-a-separate-server"></a>A felhasználói portál telepítése különálló kiszolgálón

Ha az Azure Multi-Factor Authentication-kiszolgáló rendszert futtató kiszolgáló nem Internetes elérésű, akkor a felhasználói portált **külön, internetkapcsolattal rendelkező kiszolgálón** kell telepíteni.

Ha a szervezete egyik ellenőrzési módszerként a Microsoft Authenticator appot használja, és telepíteni szeretné a felhasználói portált a saját kiszolgálóján, az alábbi feltételeknek kell teljesülniük:

* Az Azure Multi-Factor Authentication-kiszolgáló 6.0-s vagy újabb verzióját kell használnia.
* A felhasználói portált a Microsoft Internet Information Services (IIS) 6.x-es vagy újabb verzióját futtató internetes webkiszolgálóra kell telepítenie.
* Az IIS 6.x használatakor győződjön meg arról, hogy az ASP.NET 2.0.50727-es verziója telepítve van, regisztrálva van, és a beállítása **Engedélyezve**.
* Az IIS 7.x vagy újabb verzió használatakor IIS, beleértve az alapszintű hitelesítést, az ASP.NET-et és az IIS 6 metabázisával való kompatibilitási funkciót.
* A felhasználói portál védelme TLS/SSL-tanúsítvánnyal.
* Az Azure Multi-Factor Authentication Web Service SDK védelme TLS/SSL-tanúsítvánnyal.
* Győződjön meg arról, hogy a felhasználói portál a TLS/SSL protokollon keresztül tud csatlakozni az Azure Multi-Factor Authentication Web Service SDK-hoz.
* A felhasználói portálnak képesnek kell lennie arra, hogy a „PhoneFactor-adminisztrátorok” nevű biztonsági csoportban található szolgáltatásfiók hitelesítő adataival végezzen hitelesítést az Azure Multi-Factor Authentication Web Service SDK-ban. Ez a szolgáltatásfiók és csoport az Active Directoryban található meg, ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón fut. Ez a szolgáltatásfiók és csoport helyben található meg az Azure Multi-Factor Authentication-kiszolgálón, ha az nincs tartományhoz csatlakoztatva.

A felhasználói portál az Azure Multi-Factor Authentication-kiszolgálótól eltérő kiszolgálón való telepítésének lépései a következők:

1. **Az MFA-kiszolgálón** lépjen a telepítés helyére (például C:\Program Files\Multi-Factor Authentication Server), majd másolja a **MultiFactorAuthenticationUserPortalSetup64** fájlt egy olyan helyre, ahol hozzáférhet az interneteléréssel rendelkező kiszolgáló, amelyen telepíteni szeretné.
2. **Az interneteléréssel rendelkező webkiszolgálón** futtassa a Multi-Factor AuthenticationMobileAppWebServiceSetup telepítőfájlt rendszergazdaként, szükség esetén módosítsa a Hely beállítást, és tetszés szerint adjon meg egy rövid nevet a virtuális címtár számára.
3. Kösse a TLS/SSL-tanúsítványt a webhelyhez az IIS-ben.

   > [!NOTE]
   > Ez a TLS/SSL-tanúsítvány általában nyilvánosan aláírt TLS/SSL-tanúsítvány.

4. Lépjen a **C:\inetpub\wwwroot\MultiFactorAuth** helyre.
5. Szerkessze a Web.config fájlt a Jegyzettömbben.

    * Keresse meg a **"USE_WEB_SERVICE_SDK"** kulcsot, és módosítsa **value="false"** értékről **value="true"** értékre.
    * Keresse meg a **„WEB_SERVICE_SDK_AUTHENTICATION_USERNAME”** kulcsot, és módosítsa a **value=""** értéket **value="DOMAIN\User"** értékre, ahol a DOMAIN\User egy olyan szolgáltatásfiók, amely a „PhoneFactor-adminisztrátorok” csoport tagja.
    * Keresse meg a **„WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD”** kulcsot, és módosítsa a **value=""** értéket **value="Password"** értékre, ahol a Password az előző sorban megadott szolgáltatásfiókhoz tartozó jelszó.
    * Keresse meg a `https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx` értéket, és módosítsa ezt a helyőrző URL-címet arra a címre, amelyre a Web Service SDK-t telepítette a 2. lépésben.
    * Mentse a Web.Config fájlt, és zárja be a Jegyzettömböt.

6. Nyisson meg egy webböngészőt bármilyen számítógépről, és navigáljon arra az URL-címre, ahol a felhasználói portál telepítve van (például: `https://mfa.contoso.com/MultiFactorAuth` ). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

Ha kérdése van a TLS/SSL-tanúsítvány IIS-kiszolgálón való konfigurálásával kapcsolatban, tekintse meg az [SSL beállítása az IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)-ben című cikket.

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

Most, hogy telepítette a felhasználói portált, konfigurálnia kell az Azure Multi-Factor Authentication-kiszolgálót, hogy működjön a portállal.

1. Az Azure Multi-Factor Authentication-kiszolgáló konzoljában kattintson a **Felhasználói portál** ikonra. A Beállítások lapon írja be a felhasználói portál URL-címét a **Felhasználói portál URL-címe** szövegmezőbe. Ez az URL-cím a felhasználóknak küldött e-mailekben szerepel a felhasználók Azure Multi-Factor Authentication-kiszolgálóra való importálásakor, ha az e-mailes funkció engedélyezve van.
2. Válassza ki a felhasználói portálon használni kívánt beállításokat. Ha például a felhasználók választhatják ki a hitelesítési módszereket, győződjön meg arról, hogy a **Módszer kiválasztásának engedélyezése a felhasználóknak** jelölőnégyzet be van jelölve azokkal a módszerekkel együtt, amelyek közül választhatnak.
3. Határozza meg, hogy kik legyenek a rendszergazdák a **rendszergazdák** lapon. A Hozzáadás/szerkesztés mezőkben a jelölőnégyzetek és a legördülő lista használatával részletes rendszergazdai engedélyeket hozhat létre.

Választható konfiguráció:

- **Biztonsági kérdések** – Jóváhagyott biztonsági kérdéseket adhat meg a környezetéhez, továbbá a kérdések nyelvét is beállíthatja.
- **Elvégzett munkamenetek** – Konfigurálhatja a felhasználói portál integrációját egy űrlapalapú webhellyel az MFA használatával.
- **Megbízható IP-címek** – Engedélyezheti a felhasználóknak az MFA kihagyását, ha a hitelesítést egy olyan helyről végzik, amely szerepel a megbízható IP-címek vagy tartományok listáján.

![MFA-kiszolgáló felhasználói portáljának konfigurálása](./media/howto-mfaserver-deploy-userportal/config.png)

Az Azure Multi-Factor Authentication-kiszolgáló több lehetőséget nyújt a felhasználói portálhoz. A következő táblázat tartalmazza ezen lehetőségek listáját és annak magyarázatát, hogy mire szolgálnak.

| Felhasználói portál beállításai | Description |
|:--- |:--- |
| Felhasználói portál URL-címe | Adja meg a portál üzemeltetési helyének URL-címét. |
| Elsődleges hitelesítés | Adja meg a portálra való bejelentkezéskor használandó hitelesítési típust. Ez Windows-, Radius- vagy LDAP-hitelesítés lehet. |
| Felhasználók bejelentkezésének engedélyezése | Lehetővé teszi, hogy a felhasználók beírjanak egy felhasználónevet és jelszót a bejelentkezési oldalon a felhasználói portálhoz. Ha ez a lehetőség nincs bejelölve, a mezők szürkén jelennek meg. |
| Felhasználó beléptetésének engedélyezése | Lehetővé teszi, hogy a felhasználók regisztráljanak a Multi-Factor Authenticationben egy olyan beállítási képernyő megjelenítésével, amely további információk, például a telefonszám megadását kéri. A másodlagos telefonszámra való rákérdezéskor a felhasználók megadhatnak egy másodlagos telefonszámot. A külső OATH tokenre való rákérdezéskor a felhasználók megadhatnak egy külső OATH tokent. |
| Egyszeri megkerülés kezdeményezésének engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók egyszeri mellőzést kezdeményezzenek. Ha a felhasználó megadja ezt a beállítást, a következő bejelentkezésekor lép életbe. A mellőzés másodperceire való rákérdezéskor a felhasználók egy mezőben módosíthatják az alapértelmezett 300 másodpercet. Egyéb esetben az egyszeri mellőzés csak 300 másodpercig érvényes. |
| Módszer kiválasztásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók megadják az elsődleges kapcsolatfelvételi módot. Ez lehet telefonhívás, szöveges üzenet, mobilalkalmazás vagy OATH token. |
| Nyelv kiválasztásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználó módosítsa a telefonhíváshoz, szöveges üzenethez, mobilalkalmazáshoz vagy OATH tokenhez használt nyelvet. |
| Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók aktiválási kódot hozzanak létre a kiszolgálóval használt mobilalkalmazás-aktiválási folyamat elvégzéséhez.  Megadhatja azon eszközök 1 és 10 közötti számát is, amelyeken ezt az alkalmazást aktiválhatják. |
| Biztonsági kérdések használata tartalék megoldásként | Engedélyezi a biztonsági kérdések használatát, ha a kétlépéses ellenőrzés sikertelennek bizonyul. Megadhatja a sikeresen megválaszolandó biztonsági kérdések számát. |
| Harmadik féltől származó OATH token hozzárendelésének engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók megadhassanak egy külső OATH tokent. |
| OATH token használata tartalékként | Lehetővé teszi egy OATH token használatát, ha a kétlépéses ellenőrzés sikertelennek bizonyul. Ezenkívül a munkamenet időtúllépését is megadhatja percben. |
| Naplózás engedélyezése | Lehetővé teszi a naplózást a felhasználói portálon. A naplófájlok a következő helyen találhatók: C:\Program Files\Multi-Factor Authentication Server\Logs. |

> [!IMPORTANT]
> 2019 márciusában a telefonhívási lehetőségek nem lesznek elérhetők az MFA-kiszolgáló felhasználói számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

Ezen beállítások láthatóvá válnak a portálon a felhasználók számára, ha engedélyezettek és be vannak jelentkezve a felhasználói portálra.

![Az MFA-kiszolgáló fiókjának kezelése a felhasználói portál használatával](./media/howto-mfaserver-deploy-userportal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Önkiszolgáló felhasználói regisztráció

Ha azt szeretné, hogy a felhasználók bejelentkezzenek és regisztráljanak, be kell jelölnie a **felhasználók bejelentkezésének engedélyezése** és a **felhasználói beléptetési beállítások engedélyezése** a beállítások lapon. Ne feledje, hogy a kiválasztott beállítások hatással vannak a felhasználói bejelentkezési élményre.

Amikor például egy felhasználó először jelentkezik be a felhasználói portálra, az Azure Multi-Factor Authentication felhasználói beállítási lapjára kerül. Az Azure Multi-Factor Authentication konfigurációjától függően lehet, hogy a felhasználó kiválaszthatja a hitelesítési módszert.

Ha a felhasználók a Hanghívás ellenőrzési módszert választják vagy ha ez a módszer van előre konfigurálva, a felhasználóknak a lapon meg kell adniuk az elsődleges telefonszámukat és melléküket, ha van ilyen. Lehet, hogy másodlagos telefonszámot is megadhatnak.

![Elsődleges és másodlagos telefonszámok regisztrálása](./media/howto-mfaserver-deploy-userportal/backupphone.png)

Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód létrehozását is kéri. A telefonszám(ok) és a PIN-kód (ha van) megadása után a felhasználó a **Hívjon fel a hitelesítéshez** gombra kattint. Az Azure Multi-Factor Authentication telefonhívás-ellenőrzést hajt végre a felhasználó elsődleges telefonszámán. A felhasználónak fogadnia kell a hívást, és meg kell adnia a PIN-kódját (ha van), majd meg kell nyomnia a # gombot az önregisztrációs folyamat következő lépésére való továbblépéshez.

Ha a felhasználók a szöveges üzenetes ellenőrzési módszert választják, vagy ha ez a módszer van előre konfigurálva, a lapon meg kell adniuk a mobiltelefonszámukat. Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri.  A telefonszám és a PIN-kód (ha van) beírása után a felhasználó a **Küldjön SMS-t a hitelesítéshez** gombra kattint. Az Azure Multi-Factor Authentication SMS-ellenőrzést végez a felhasználó mobiltelefonjára. A felhasználó egyszeri jelszót (one-time-passcode, OTP) tartalmazó SMS-t kap, és válaszol az üzenetre az OTP-vel és a PIN-kódjával (ha van).

![Felhasználói portál ellenőrzése SMS-sel](./media/howto-mfaserver-deploy-userportal/text.png)

Ha a felhasználó a mobilalkalmazásos ellenőrzési módszert választja, az oldal felkéri, hogy telepítse a Microsoft Authenticator alkalmazást az eszközére, és hozzon létre egy aktiválási kódot. Az alkalmazás telepítése után a felhasználónak az Aktiváló kód előállítása gombra kell kattintania.

> [!NOTE]
> A Microsoft Authenticator alkalmazás használatához a felhasználónak engedélyeznie kell a leküldéses értesítéseket az eszközén.

A lap ezután megjelenít egy aktiválási kódot és egy URL-címet egy vonalkód képével együtt. Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri. A felhasználó beírja az aktiválási kódot és az URL-címet a Microsoft Authenticator alkalmazásba, vagy a vonalkódolvasóval beolvassa a vonalkódot, majd az Aktiválás gombra kattint.

Az aktiválás elvégzése után a felhasználó a **Hitelesítsen most** gombra kattint. Az Azure Multi-Factor Authentication ellenőrzést végez a felhasználó Mobile alkalmazásával. A felhasználónak meg kell adnia a PIN-kódját (ha van) és a mobilalkalmazásban a Hitelesítés gombra kell kattintania, hogy az önregisztrációs folyamat következő lépésére ugorjon.

Ha a rendszergazdák úgy konfigurálták az Azure Multi-Factor Authentication-kiszolgálót, hogy biztonsági kérdéseket és válaszokat gyűjtsön, a felhasználó a Biztonsági kérdések lapra kerül. A felhasználónak ki kell választania négy biztonsági kérdést, és meg kell válaszolnia azokat.

![A felhasználói portál biztonsági kérdései](./media/howto-mfaserver-deploy-userportal/secq.png)

A felhasználó önregisztrációja ezzel befejeződött, és a felhasználó be van jelentkezve a felhasználói portálra. Ezután bármikor újra bejelentkezhet a felhasználói portálra a telefonszáma, PIN-kódja, hitelesítési módszere és biztonsági kérdései módosításához, ha ezt a rendszergazdáik engedélyezték.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](howto-mfaserver-deploy-mobileapp.md)
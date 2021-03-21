---
title: 'Azure AD Connect: átmenő hitelesítés – problémamegoldás | Microsoft Docs'
description: Ez a cikk az Azure Active Directory (Azure AD) átmenő hitelesítésének hibaelhárítását ismerteti.
services: active-directory
keywords: A Azure AD Connect átmenő hitelesítés Active Directory, az Azure AD-hez, az egyszeri bejelentkezéshez szükséges összetevők és az Azure AD-hez szükséges összetevők – hibakeresés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a014bd5c8f1edbfb00019b8541cef552271d65b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762839"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Az Azure Active Directory átmenő hitelesítésének hibaelhárítása

Ez a cikk az Azure AD átmenő hitelesítéssel kapcsolatos gyakori problémákról nyújt hibaelhárítási információkat.

>[!IMPORTANT]
>Ha az átmenő hitelesítéssel kapcsolatos felhasználói bejelentkezési problémákat tapasztal, ne tiltsa le a funkciót, vagy távolítsa el az átmenő hitelesítési ügynököket anélkül, hogy csak Felhőbeli globális rendszergazdai fiókkal kellene visszaesnie. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról](../fundamentals/add-users-azure-active-directory.md). Ez a lépés kritikus fontosságú, és gondoskodik arról, hogy ne legyen kizárva a bérlőből.

## <a name="general-issues"></a>Általános problémák

### <a name="check-status-of-the-feature-and-authentication-agents"></a>A szolgáltatás és a hitelesítési ügynökök állapotának ellenõrzése

Győződjön meg arról, hogy az átmenő hitelesítés funkció továbbra is **engedélyezve** van a bérlőn, és a hitelesítési ügynökök állapota **aktív**, és nem **inaktív**. Az állapot ellenőrzéséhez nyissa meg a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) **Azure ad Connect** paneljét.

![Azure Active Directory felügyeleti központ – Azure AD Connect panel](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory felügyeleti központ – átmenő hitelesítés panel](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Felhasználó felé irányuló bejelentkezési hibaüzenetek

Ha a felhasználó nem tud bejelentkezni az átmenő hitelesítés használatával, akkor az Azure AD bejelentkezési képernyőjén a következő, felhasználó által megjelenített hibák egyike jelenhet meg: 

|Hiba|Leírás|Feloldás
| --- | --- | ---
|AADSTS80001|Nem lehet csatlakozni a Active Directory|Győződjön meg arról, hogy az ügynök-kiszolgálók ugyanahhoz az AD-erdőhöz tartoznak, mint azok a felhasználók, akiknek a jelszavát érvényesíteni kell, és képesek csatlakozni a Active Directoryhoz.  
|AADSTS8002|Időtúllépés történt a Active Directoryhoz való csatlakozáskor|Győződjön meg arról, hogy a Active Directory elérhető, és válaszol az ügynökök kéréseire.
|AADSTS80004|Az ügynöknek átadott Felhasználónév érvénytelen.|Győződjön meg arról, hogy a felhasználó a megfelelő felhasználónévvel próbál bejelentkezni.
|AADSTS80005|Az ellenőrzés kiszámíthatatlan webkivételt észlelt|Átmeneti hiba. Próbálja megismételni a kérelmet. Ha a művelet továbbra is sikertelen, forduljon a Microsoft ügyfélszolgálatához.
|AADSTS80007|Hiba történt a Active Directoryával folytatott kommunikáció során|További információkért tekintse meg az ügynök naplóit, és ellenőrizze, hogy a Active Directory a várt módon működik-e.

### <a name="users-get-invalid-usernamepassword-error"></a>A felhasználók érvénytelen felhasználónevet vagy jelszót kapnak. 

Ez akkor fordulhat elő, ha a felhasználó helyszíni UserPrincipalName (UPN) eltér a felhasználó Felhőbeli egyszerű felhasználónevétől.

Annak ellenőrzéséhez, hogy ez a probléma, először ellenőrizze, hogy az áteresztő hitelesítési ügynök megfelelően működik-e:


1. Hozzon létre egy teszt fiókot.  
2. Importálja a PowerShell-modult az ügynök gépére:

 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Futtassa a PowerShell meghívása parancsot: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. Amikor a rendszer a hitelesítő adatok megadását kéri, adja meg a bejelentkezéshez használt felhasználónevet és jelszót ( https://login.microsoftonline.com) ).

Ha ugyanazzal a Felhasználónév/jelszóval kapcsolatos hibaüzenetet kap, ez azt jelenti, hogy az áteresztő hitelesítési ügynök megfelelően működik, és a probléma az lehet, hogy a helyszíni UPN nem irányítható. További tudnivalókért tekintse meg az [alternatív bejelentkezési azonosító konfigurálását](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)ismertető témakört.

> [!IMPORTANT]
> Ha a Azure AD Connect-kiszolgáló nincs tartományhoz csatlakoztatva, [Azure ad Connect: előfeltételek](./how-to-connect-install-prerequisites.md#installation-prerequisites), Érvénytelen felhasználónév/jelszó hiba lép fel.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Bejelentkezési hibák okai a Azure Active Directory felügyeleti központban (prémium szintű licencre van szükség)

Ha a bérlőhöz prémium szintű Azure AD licenc van társítva, akkor a [bejelentkezési tevékenységről szóló jelentést](../reports-monitoring/concept-sign-ins.md) is megtekintheti a [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com/).

![Azure Active Directory felügyeleti központ – bejelentkezési jelentés](./media/tshoot-connect-pass-through-authentication/pta4.png)

**Azure Active Directory**  ->  **Jelentkezzen** be a [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com/) , és kattintson egy adott felhasználó bejelentkezési tevékenységére. Keresse meg a **bejelentkezési hibakód** mezőt. Képezze le az adott mező értékét a hiba okának és feloldásának a következő táblázat használatával:

|Bejelentkezési hibakód|Bejelentkezési hiba oka|Feloldás
| --- | --- | ---
| 50144 | A felhasználó Active Directory jelszava lejárt. | A felhasználó jelszavának alaphelyzetbe állítása a helyszíni Active Directoryban.
| 80001 | Nem érhető el hitelesítési ügynök. | Hitelesítési ügynök telepítése és regisztrálása.
| 80002 | A hitelesítési ügynök jelszó-érvényesítési kérése túllépte az időkorlátot. | Ellenőrizze, hogy a Active Directory elérhető-e a hitelesítési ügynöktől.
| 80003 | A hitelesítési ügynök érvénytelen választ kapott. | Ha a probléma következetesen reprodukálható több felhasználó között, ellenőrizze a Active Directory konfigurációját.
| 80004 | A bejelentkezési kérésben helytelen egyszerű felhasználónevet (UPN-t) használtak. | Kérje meg a felhasználót, hogy jelentkezzen be a megfelelő felhasználónévvel.
| 80005 | Hitelesítési ügynök: hiba történt. | Átmeneti hiba. Próbálkozzon újra később.
| 80007 | A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz. | Ellenőrizze, hogy a Active Directory elérhető-e a hitelesítési ügynöktől.
| 80010 | A hitelesítési ügynök nem tudta visszafejteni a jelszót. | Ha a probléma következetesen reprodukálható, telepítsen és regisztráljon egy új hitelesítési ügynököt. És távolítsa el a jelenlegit. 
| 80011 | A hitelesítési ügynök nem tudta lekérni a visszafejtési kulcsot. | Ha a probléma következetesen reprodukálható, telepítsen és regisztráljon egy új hitelesítési ügynököt. És távolítsa el a jelenlegit.
| 80014 | Az érvényesítési kérelem a maximális eltelt idő lejárta után válaszolt. | A hitelesítési ügynök időkorlátja lejárt. Nyisson meg egy támogatási jegyet a hibakódtal, a korrelációs AZONOSÍTÓval és az időbélyeggel, ha további részleteket szeretne kapni erről a hibáról

>[!IMPORTANT]
>Az átmenő hitelesítési ügynökök a [Win32 LOGONUSER API](/windows/win32/api/winbase/nf-winbase-logonusera)meghívásával hitelesítik az Azure ad-felhasználókat a felhasználónevek és a jelszavak Active Directory való ellenőrzésével. Ennek eredményeképpen, ha beállította a "Bejelentkezés a következőre" beállítást a Active Directory a munkaállomás bejelentkezési hozzáférésének korlátozásához, akkor az áteresztő hitelesítési ügynököket futtató kiszolgálókat is fel kell vennie a "Bejelentkezés" kiszolgálókra. Ha ezt nem teszi meg, azzal letiltja a felhasználók számára az Azure AD-ba való bejelentkezést.

## <a name="authentication-agent-installation-issues"></a>A hitelesítési ügynök telepítési problémái

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

[Gyűjtse össze az ügynökök naplóit](#collecting-pass-through-authentication-agent-logs) a kiszolgálóról, és vegye fel a kapcsolatot Microsoft ügyfélszolgálata a problémával.

## <a name="authentication-agent-registration-issues"></a>A hitelesítési ügynök regisztrálásával kapcsolatos problémák

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>A hitelesítési ügynök regisztrációja letiltott portok miatt nem sikerült

Győződjön meg arról, hogy a hitelesítési ügynököt tartalmazó kiszolgáló képes kommunikálni a szolgáltatás URL-címeivel és az [itt](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)felsorolt portokkal.

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>A hitelesítési ügynök regisztrációja a jogkivonat vagy a fiók engedélyezési hibája miatt meghiúsult

Győződjön meg arról, hogy csak felhőalapú globális rendszergazdai fiókot használ az összes Azure AD Connect vagy önálló hitelesítési ügynök telepítési és regisztrációs műveleteihez. Ismert probléma van az MFA-kompatibilis globális rendszergazdai fiókokkal. az MFA ideiglenes kikapcsolása (csak a műveletek végrehajtásához) megkerülő megoldásként.

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

[Gyűjtse össze az ügynökök naplóit](#collecting-pass-through-authentication-agent-logs) a kiszolgálóról, és vegye fel a kapcsolatot Microsoft ügyfélszolgálata a problémával.

## <a name="authentication-agent-uninstallation-issues"></a>A hitelesítési ügynök eltávolításával kapcsolatos problémák

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Figyelmeztető üzenet Azure AD Connect eltávolításakor

Ha az átmenő hitelesítés engedélyezve van a bérlőn, és megpróbálja eltávolítani Azure AD Connect, akkor a következő figyelmeztető üzenet jelenik meg: "a felhasználók nem fognak tudni bejelentkezni az Azure AD-be, ha más áteresztő hitelesítési ügynökök is telepítve vannak más kiszolgálókra."

A felhasználói bejelentkezés elkerülése érdekében győződjön meg arról, hogy a telepítés a Azure AD Connect eltávolítása előtt [nagyon elérhető](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) .

## <a name="issues-with-enabling-the-feature"></a>A szolgáltatás engedélyezésével kapcsolatos problémák

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>A funkció engedélyezése nem sikerült, mert nem érhetők el hitelesítési ügynökök

Rendelkeznie kell legalább egy aktív hitelesítési ügynökkel az átmenő hitelesítés engedélyezéséhez a bérlőn. A hitelesítési ügynököt telepítheti Azure AD Connect vagy önálló hitelesítési ügynök telepítésével is.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>A funkció engedélyezése letiltott portok miatt meghiúsult

Győződjön meg arról, hogy a kiszolgáló, amelyen a Azure AD Connect telepítve van, képes kommunikálni a szolgáltatás URL-címeivel és az [itt](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)felsorolt portokkal.

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>A funkció engedélyezése a jogkivonat vagy a fiók engedélyezési hibája miatt meghiúsult

A szolgáltatás engedélyezésekor ügyeljen arra, hogy csak felhőalapú globális rendszergazdai fiókot használjon. Ismert probléma történt a többtényezős hitelesítés (MFA) használatára képes globális rendszergazdai fiókok esetében. az MFA ideiglenes kikapcsolása (csak a művelet befejezéséhez) megkerülő megoldásként.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Átmenő hitelesítési ügynök naplóinak gyűjtése

Az esetlegesen felmerülő problémák típusától függően különböző helyeken kell megkeresnie az átmenő hitelesítési ügynök naplófájljait.

### <a name="azure-ad-connect-logs"></a>Naplók Azure AD Connect

A telepítéssel kapcsolatos hibákért olvassa el a Azure AD Connect naplókat a **%ProgramData%\AADConnect\trace- \* . log** címen.

### <a name="authentication-agent-event-logs"></a>Hitelesítési ügynök eseménynaplói

A hitelesítési ügynökkel kapcsolatos hibák esetén nyissa meg a Eseménynapló alkalmazást a kiszolgálón, és keresse meg az **alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

A részletes elemzéshez engedélyezze a "munkamenet" naplót (kattintson a jobb gombbal a Eseménynapló alkalmazáson belül a beállítás megkereséséhez). A normál műveletek során ne futtassa a hitelesítési ügynököt ezzel a naplóval. csak hibaelhárításhoz használható. A napló tartalma csak akkor látható, ha a napló újra le van tiltva.



### <a name="detailed-trace-logs"></a>Részletes nyomkövetési naplók

A felhasználók bejelentkezési hibáinak elhárításához keresse meg a nyomkövetési naplókat a **%PROGRAMDATA%\MICROSOFT\AZURE ad kapcsolódási hitelesítési Agent\Trace \\**. Ezek a naplók magukban foglalják, hogy egy adott felhasználói bejelentkezés miért nem sikerült az átmenő hitelesítés funkció használatával. Ezek a hibák az előző bejelentkezési hiba okainak táblázatában látható bejelentkezési hibák okaira is leképezhetők. A következő példa egy naplóbejegyzés:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Az előző példában szereplő hiba részletes leírását ("1328") a parancssor megnyitásával és a következő parancs futtatásával kérheti le (Megjegyzés: a "1328" kifejezés lecserélése a naplókban megjelenő tényleges számra):

`Net helpmsg 1328`

![Átmenő hitelesítés](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Tartományvezérlői naplók

Ha a naplózás engedélyezve van, a tartományvezérlők biztonsági naplóiban további információk is megtalálhatók. Az átmenő hitelesítési ügynökök által küldött bejelentkezési kérések lekérdezésének egyszerű módja a következő:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Teljesítményfigyelő számlálói

A hitelesítési ügynökök figyelésének másik módja az, hogy nyomon kövessék az egyes Teljesítményfigyelő számlálóit minden olyan kiszolgálón, amelyen a hitelesítési ügynök telepítve van. A következő globális számlálókat használja (**# PTA hitelesítések**, **#PTA sikertelen hitelesítések** és **#PTA sikeres hitelesítések**) és a hibák számlálói (**# PTA hitelesítési hibák**):

![Átmenő hitelesítés Teljesítményfigyelő számlálói](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Az átmenő hitelesítés több hitelesítési ügynök használatával magas rendelkezésre állást biztosít, _nem_ pedig a terheléselosztást. A konfigurációtól függően _nem_ minden hitelesítési ügynök nagyjából _azonos_ számú kérést kap. Lehetséges, hogy egy adott hitelesítési ügynök egyáltalán nem kap forgalmat.

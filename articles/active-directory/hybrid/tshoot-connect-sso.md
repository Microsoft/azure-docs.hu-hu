---
title: 'Azure Active Directory Connect: zökkenőmentes, egyetlen Sign-On hibáinak megoldása | Microsoft Docs'
description: Ez a témakör azt ismerteti, hogyan lehet hibaelhárítási Azure Active Directory zökkenőmentes egyetlen Sign-On
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef58f6e84fb3b4dec947fa3614b6ec1043ff89e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644646"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory zökkenőmentes egyetlen Sign-On hibáinak megoldása

Ez a cikk segítséget nyújt a Azure Active Directory (Azure AD) zökkenőmentes egyszeri Sign-On (zökkenőmentes SSO) kapcsolatos gyakori problémákról.

## <a name="known-issues"></a>Ismert problémák

- Néhány esetben a zökkenőmentes SSO engedélyezése akár 30 percet is igénybe vehet.
- Ha letiltja és újból engedélyezi a zökkenőmentes egyszeri bejelentkezést a bérlőn, a felhasználók nem kapják meg az egyszeri bejelentkezést, amíg a gyorsítótárazott Kerberos-jegyek (általában 10 órán át érvényesek) lejárnak.
- Ha a zökkenőmentes egyszeri bejelentkezés sikeres, a felhasználónak nincs lehetősége kiválasztani a **bejelentkezve maradni** lehetőséget. Ennek a viselkedésnek köszönhetően a [SharePoint és a OneDrive leképezési forgatókönyvek](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nem működnek.
- A 16.0.8730. xxxx és újabb verziókkal rendelkező Win32-ügyfelek (Outlook, Word, Excel és egyebek) nem interaktív folyamat használatával támogatottak. Microsoft 365 Más verziók nem támogatottak; ezeken a verziókon a felhasználók bejelentkeznek a felhasználónevek, de a jelszavak nem. A OneDrive a csendes bejelentkezési élmény érdekében aktiválni kell a [OneDrive csendes konfigurációs szolgáltatást](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) .
- A zökkenőmentes egyszeri bejelentkezés nem működik a Firefox privát böngészési módjában.
- A zökkenőmentes egyszeri bejelentkezés nem működik az Internet Explorerben, ha a fokozottan védett mód be van kapcsolva.
- A zökkenőmentes egyszeri bejelentkezés nem működik privát böngészési módban a Microsoft Edge-ben (örökölt).
- A zökkenőmentes egyszeri bejelentkezés nem működik az iOS és az Android rendszerű böngészőkben.
- Ha egy felhasználó túl sok csoporthoz tartozik a Active Directoryban, a felhasználó Kerberos-jegye valószínűleg túl nagy lesz a feldolgozáshoz, és ez a zökkenőmentes egyszeri bejelentkezést eredményezi. Az Azure AD HTTPS-kérések legfeljebb 50 KB méretű fejléceket tartalmazhatnak. A Kerberos-jegyeknek kisebbnek kell lenniük, mint amennyit az Azure AD-összetevők (általában 2-5 KB), például a cookie-k befogadására korlátoznak. Javasoljuk, hogy csökkentse a felhasználók csoporttagságait, és próbálkozzon újra.
- Ha 30 vagy több Active Directory erdőt szinkronizál, a zökkenőmentes SSO-t nem engedélyezheti Azure AD Connecton keresztül. Megkerülő megoldásként [manuálisan engedélyezheti](#manual-reset-of-the-feature) a szolgáltatást a bérlőn.
- Az Azure AD szolgáltatás URL-címének () hozzáadása a `https://autologon.microsoftazuread-sso.com` megbízható helyek zónájához a helyi intranet zóna helyett *blokkolja a felhasználók bejelentkezését*.
- A zökkenőmentes egyszeri bejelentkezés támogatja a Kerberos AES256_HMAC_SHA1, AES128_HMAC_SHA1 és RC4_HMAC_MD5 titkosítási típusait. Azt javasoljuk, hogy a AzureADSSOAcc $ fiók titkosítási típusa legyen AES256_HMAC_SHA1, vagy a további biztonság érdekében adja meg az egyik AES-típust a vs. RC4-nek. A titkosítási típust a Active Directory lévő fiók msDS-Supportedencryptiontypes attribútuma alapján attribútuma tárolja.  Ha a AzureADSSOAcc $ fiók titkosítási típusa RC4_HMAC_MD5 értékre van állítva, és módosítani szeretné az AES-titkosítási típusok egyikére, akkor ügyeljen arra, hogy az adott kérdésben a [GYIK-dokumentumban](how-to-connect-sso-faq.md) leírtak szerint először átadja a AzureADSSOAcc $ fiók Kerberos-visszafejtési kulcsát, ellenkező esetben a zökkenőmentes egyszeri bejelentkezés nem fog történni.
-  Ha több erdővel rendelkezik, és az egyszeri bejelentkezés engedélyezése az egyik erdőben, az az összes megbízható erdőben engedélyezi az egyszeri bejelentkezést. Ha olyan erdőben engedélyezi az egyszeri bejelentkezést, amelyben az SSO már engedélyezve van, hibaüzenet jelenik meg, amely azt jelzi, hogy az SSO már engedélyezve van az erdőben.

## <a name="check-status-of-feature"></a>Szolgáltatás állapotának ellenõrzése

Győződjön meg arról, hogy a zökkenőmentes egyszeri bejelentkezés funkció továbbra is **engedélyezve** van a bérlőn. Az állapot ellenőrzéséhez nyissa meg a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) **Azure ad Connect** ablaktábláját.

![Azure Active Directory felügyeleti központ: Azure AD Connect panel](./media/tshoot-connect-sso/sso10.png)

Kattintson a végig lehetőségre a zökkenőmentes egyszeri bejelentkezéshez engedélyezett összes AD-erdő megjelenítéséhez.

![Azure Active Directory felügyeleti központ: zökkenőmentes SSO-ablaktábla](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Bejelentkezési hibák okai a Azure Active Directory felügyeleti központban (prémium szintű licencre van szükség)

Ha a bérlőhöz prémium szintű Azure AD licenc van társítva, akkor a [bejelentkezési tevékenységről szóló jelentést](../reports-monitoring/concept-sign-ins.md) is megtekintheti a [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com/).

![Azure Active Directory felügyeleti központ: bejelentkezési jelentés](./media/tshoot-connect-sso/sso9.png)

Keresse meg **Azure Active Directory**  >  a **bejelentkezéseket** a [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com/), majd válasszon egy adott felhasználó bejelentkezési tevékenységét. Keresse meg a **bejelentkezési hibakód** mezőt. Képezze le az adott mező értékét a hiba okának és a megoldásnak az alábbi táblázat használatával:

|Bejelentkezési hibakód|Bejelentkezési hiba oka|Feloldás
| --- | --- | ---
| 81001 | A felhasználó Kerberos-jegye túl nagy. | Csökkentse a felhasználó csoporttagságait, majd próbálkozzon újra.
| 81002 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81003 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81004 | A Kerberos-hitelesítési kísérlet meghiúsult. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81008 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81009 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81010 | A zavartalan egyszeri bejelentkezés meghiúsult, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. | A felhasználónak be kell jelentkeznie egy tartományhoz csatlakoztatott eszközről a vállalati hálózaton belül.
| 81011 | Nem található a felhasználói objektum a felhasználó Kerberos-jegyében található információk alapján. | Azure AD Connect használatával szinkronizálhatja a felhasználó adatait az Azure AD-ben.
| 81012 | Az Azure AD-be bejelentkezni próbáló felhasználó különbözik az eszközre bejelentkezett felhasználótól. | A felhasználónak be kell jelentkeznie egy másik eszközről.
| 81013 | Nem található a felhasználói objektum a felhasználó Kerberos-jegyében található információk alapján. |Azure AD Connect használatával szinkronizálhatja a felhasználó adatait az Azure AD-ben. 

## <a name="troubleshooting-checklist"></a>Hibaelhárítási ellenőrzőlista

A következő ellenőrzőlista segítségével elháríthatja a zökkenőmentes egyszeri bejelentkezéssel kapcsolatos problémákat:

- Győződjön meg arról, hogy a zökkenőmentes egyszeri bejelentkezés funkció engedélyezve van Azure AD Connectban. Ha nem tudja engedélyezni a szolgáltatást (például egy letiltott port miatt), győződjön meg arról, hogy az összes [előfeltétel](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) teljesül.
- Ha engedélyezte az [Azure ad-csatlakozást](../devices/overview.md) és a zökkenőmentes egyszeri bejelentkezést a bérlőn, győződjön meg arról, hogy a probléma nem az Azure ad JOIN szolgáltatással érhető el. Az Azure AD JOIN szolgáltatásból származó egyszeri bejelentkezés elsőbbséget élvez a zökkenőmentes SSO esetén, ha az eszköz regisztrálva van az Azure AD-ben és a tartományhoz csatlakoztatva. Az Azure AD-csatlakozáshoz tartozó egyszeri bejelentkezéssel a felhasználó egy bejelentkezési csempét lát, amely a "Connected to Windows".
- Győződjön meg arról, hogy az Azure AD URL-címe ( `https://autologon.microsoftazuread-sso.com` ) a felhasználó intranetes zóna-beállításainak részét képezi.
- Győződjön meg arról, hogy a vállalati eszköz csatlakozik a Active Directory tartományhoz. Az eszköznek _nem_ kell az [Azure ad](../devices/overview.md) -hez CSATLAKOZNIA a zökkenőmentes egyszeri bejelentkezés működéséhez.
- Győződjön meg arról, hogy a felhasználó Active Directory tartományi fiókkal van bejelentkezve az eszközre.
- Győződjön meg arról, hogy a felhasználói fiók olyan Active Directory erdőben van, ahol be van állítva a zökkenőmentes egyszeri bejelentkezés.
- Győződjön meg arról, hogy az eszköz a vállalati hálózathoz csatlakozik.
- Győződjön meg arról, hogy az eszköz ideje szinkronizálva van a Active Directory és a tartományvezérlők időpontjával, valamint arról, hogy egymástól öt percen belül vannak.
- Győződjön meg arról, hogy a `AZUREADSSOACC` számítógépfiók megtalálható és engedélyezve van minden olyan ad-erdőben, amelyet engedélyezni szeretne a zökkenőmentes egyszeri bejelentkezéshez. Ha a számítógépfiók törölve lett vagy hiányzik, a [PowerShell-parancsmagok](#manual-reset-of-the-feature) használatával újra létrehozhatja őket.
- Az eszközön lévő meglévő Kerberos-jegyek listázása a parancs parancssorból való használatával `klist` . Győződjön meg arról, hogy a számítógépfiók számára kiállított jegyek `AZUREADSSOACC` jelen vannak. A felhasználók Kerberos-jegyei általában 10 órán át érvényesek. Előfordulhat, hogy különböző beállítások vannak a Active Directory.
- Ha letiltotta és újraengedélyezte a zökkenőmentes egyszeri bejelentkezést a bérlőn, akkor a felhasználók nem kapják meg az egyszeri bejelentkezést, amíg a gyorsítótárazott Kerberos-jegyek lejártak.
- Törölje a meglévő Kerberos-jegyeket az eszközről a `klist purge` parancs használatával, majd próbálkozzon újra.
- Annak megállapításához, hogy van-e JavaScripttel kapcsolatos probléma, tekintse át a böngésző konzoljának naplóit ( **fejlesztői eszközök**).
- Tekintse át a [tartományvezérlő naplófájljait](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Tartományvezérlői naplók

Ha engedélyezi a sikeres naplózást a tartományvezérlőn, akkor minden alkalommal, amikor egy felhasználó a zökkenőmentes SSO-n keresztül jelentkezik be, egy biztonsági bejegyzést rögzít az eseménynaplóban. Ezeket a biztonsági eseményeket a következő lekérdezéssel érheti el. (Keresse meg a **$ AzureADSSOAcc** számítógépfiók **4769** -as eseményét.)

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>A szolgáltatás manuális alaphelyzetbe állítása

Ha a hibaelhárítás nem segített, manuálisan állíthatja vissza a szolgáltatást a bérlőn. Kövesse az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen Azure AD Connect futtat.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>1. lépés: a zökkenőmentes egyszeri bejelentkezés PowerShell-moduljának importálása

1. Először töltse le és telepítse az [Azure ad PowerShellt](/powershell/azure/active-directory/overview).
2. Tallózással keresse meg a `%programfiles%\Microsoft Azure Active Directory Connect` mappát.
3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>2. lépés: azon Active Directory erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés

1. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a t `New-AzureADSSOAuthenticationContext` . Ha a rendszer kéri, adja meg a bérlő globális rendszergazdai hitelesítő adatait.
2. Hívás `Get-AzureADSSOStatus` . Ez a parancs a Active Directory erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>3. lépés: a zökkenőmentes SSO letiltása minden Active Directory erdőben, amelyen a szolgáltatást beállította

1. Hívás `$creds = Get-Credential` . Ha a rendszer kéri, adja meg a kívánt Active Directory erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazdai hitelesítő adatok felhasználónevet a SAM-fiók nevének formátumában kell megadni (contoso\johndoe vagy contoso. com\johndoe). A Felhasználónév tartomány részeként a tartományi rendszergazda tartományvezérlőjét kell megkeresni a DNS használatával.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Protected Users csoport tagja. Ha igen, a művelet sikertelen lesz.

2. Hívás `Disable-AzureADSSOForest -OnPremCredentials $creds` . Ez a parancs eltávolítja a számítógépfiókot a helyszíni `AZUREADSSOACC` tartományvezérlőről ehhez az adott Active Directory erdőhöz.
3. Ismételje meg az előző lépéseket minden olyan Active Directory erdőben, amelyben a szolgáltatást beállította.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. lépés: a zökkenőmentes SSO engedélyezése minden Active Directory erdőben

1. Hívás `Enable-AzureADSSOForest` . Ha a rendszer kéri, adja meg a kívánt Active Directory erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazdai hitelesítő adatok felhasználónevet a SAM-fiók nevének formátumában kell megadni (contoso\johndoe vagy contoso. com\johndoe). A Felhasználónév tartomány részeként a tartományi rendszergazda tartományvezérlőjét kell megkeresni a DNS használatával.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Protected Users csoport tagja. Ha igen, a művelet sikertelen lesz.

2. Ismételje meg az előző lépést minden olyan Active Directory erdőben, amelyben be szeretné állítani a szolgáltatást.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. lépés A szolgáltatás engedélyezése a bérlőn

Ha be szeretné kapcsolni a szolgáltatást a bérlőn, hívja a következőt: `Enable-AzureADSSO -Enable $true` .

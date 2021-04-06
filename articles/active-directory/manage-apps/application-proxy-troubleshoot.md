---
title: Alkalmazásproxy – problémamegoldás | Microsoft Docs
description: Az Azure AD Application Proxy hibáinak elhárítását ismerteti.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cb8fb0e194b4c43b5e247f2ea5d1e38d924591db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257963"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása

Az alkalmazásproxy hibáinak elhárítása során javasoljuk, hogy kezdje meg a hibaelhárítási folyamat áttekintését, az [alkalmazásproxy-összekötő](application-proxy-debug-connectors.md)hibáinak elhárítását, és állapítsa meg, hogy az alkalmazásproxy-összekötők megfelelően vannak-e konfigurálva. Ha továbbra is problémát tapasztal az alkalmazáshoz való csatlakozás során, kövesse az [alkalmazásproxy alkalmazásával kapcsolatos hibák](application-proxy-debug-apps.md)elhárítása című témakört.

Ha hiba lép fel egy közzétett alkalmazás elérésekor vagy az alkalmazások közzétételekor, tekintse meg a következő beállításokat, és ellenőrizze, hogy Microsoft Azure AD alkalmazásproxy megfelelően működik-e:

* Nyissa meg a Windows-szolgáltatások konzolt. Ellenőrizze, hogy a **Microsoft HRE Application proxy Connector** szolgáltatás engedélyezve van-e és fut-e. Érdemes megtekinteni az alkalmazásproxy szolgáltatás Tulajdonságok lapját is, ahogy az az alábbi képen is látható:  
  ![A Microsoft HRE Application proxy-összekötő Tulajdonságok ablak képernyőképe](./media/application-proxy-troubleshoot/connectorproperties.png)
* Nyissa meg Eseménynapló és keresse meg az alkalmazásproxy-összekötő eseményeit az **alkalmazásokban és a szolgáltatásokban naplózza** a  >  **Microsoft**  >  **AadApplicationProxy**-  >  **összekötő**  >  **rendszergazdáját**.
* Ha szükséges, részletesebb naplók érhetők el [az alkalmazásproxy-összekötő munkamenet-naplófájljainak bekapcsolásával](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>A lap nem megfelelően jelenik meg
Előfordulhat, hogy problémák léptek fel az alkalmazás megjelenítésével kapcsolatban, vagy helytelenül működnek, anélkül, hogy konkrét hibaüzeneteket kellene kapnia. Ez akkor fordulhat elő, ha közzétette a cikk elérési útját, de az alkalmazásnak az elérési úton kívül található tartalomra van szüksége.

Ha például közzéteszi az elérési utat `https://yourapp/app` , de az alkalmazás rendszerképeket hív meg `https://yourapp/media` , azok nem lesznek megjelenítve. Győződjön meg arról, hogy az alkalmazást a legmagasabb szintű elérési úttal kell közzétennie, és az összes releváns tartalmat fel kell vennie. Ebben a példában ez lenne `http://yourapp/` .

## <a name="connector-errors"></a>Összekötői hibák

Ha az összekötő varázsló telepítésekor nem sikerül a regisztráció, kétféleképpen tekintheti meg a hiba okát. Keresse meg az eseménynaplót az **alkalmazások és szolgáltatások Logs\Microsoft\AadApplicationProxy\Connector\Admin** alatt, vagy futtassa a következő Windows PowerShell-parancsot:

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

Miután megtalálta az összekötő hibáját az eseménynaplóból, a gyakori hibákkal kapcsolatos táblázat segítségével oldja meg a problémát:

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Az összekötő regisztrálása nem sikerült: Győződjön meg arról, hogy engedélyezte az alkalmazásproxy használatát az Azure felügyeleti portálban, és hogy helyesen adta meg a Active Directory felhasználónevét és jelszavát. Hiba: egy vagy több hiba történt. | Ha bezárta a regisztrációs ablakot az Azure AD-be való bejelentkezés nélkül, futtassa újra az összekötő varázslót, és regisztrálja az összekötőt. <br><br> Ha megnyílik a regisztrációs ablak, majd azonnal bezárul anélkül, hogy bejelentkezne, valószínűleg ezt a hibát fogja kapni. Ez a hiba akkor fordul elő, ha hálózati hiba van a rendszeren. Győződjön meg arról, hogy a böngészőből nyilvános webhelyre lehet csatlakozni, és hogy a portok az [alkalmazásproxy előfeltételeiben](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)megadott módon vannak megnyitva. |
| A törlési hiba a regisztrációs ablakban jelenik meg. Nem folytatható | Ha ezt a hibát látja, és az ablak bezárul, helytelen felhasználónevet vagy jelszót adott meg. Próbálkozzon újra. |
| Az összekötő regisztrálása nem sikerült: Győződjön meg arról, hogy engedélyezte az alkalmazásproxy használatát az Azure felügyeleti portálban, és hogy helyesen adta meg a Active Directory felhasználónevét és jelszavát. Hiba: "AADSTS50059: nem található bérlő által azonosított információ sem a kérelemben, sem pedig a megadott hitelesítő adatok alapján történő keresés, és a szolgáltatás egyszerű URI általi keresése nem sikerült. | Microsoft-fiókkal próbál bejelentkezni, és nem egy olyan tartományba, amely tagja annak a címtárnak, amelyhez hozzáférni próbál. Győződjön meg arról, hogy a rendszergazda a bérlő tartományának azonos tartománynevéhez tartozik, például ha az Azure AD-tartomány contoso.com, a rendszergazdának kell lennie admin@contoso.com . |
| Nem sikerült beolvasni a PowerShell-parancsfájlok futtatásának aktuális végrehajtási szabályzatát. | Ha az összekötő telepítése sikertelen, ellenőrizze, hogy nincs-e letiltva a PowerShell végrehajtási házirendje. <br><br>1. Nyissa meg a Csoportházirend szerkesztőt.<br>2. Lépjen a **Számítógép konfigurációja**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Windows PowerShell** elemre, és kattintson duplán **a parancsfájlok végrehajtásának bekapcsolása** lehetőségre.<br>3. a végrehajtási házirend beállítható úgy, hogy **nincs konfigurálva** vagy **engedélyezve**. Ha az **engedélyezve** értékre van állítva, győződjön meg arról, hogy a beállítások területen a végrehajtási házirend a **helyi parancsfájlok és a távoli aláírású parancsfájlok engedélyezése** vagy az **összes parancsfájl engedélyezése** beállítást adja meg. |
| Az összekötő nem tudta letölteni a konfigurációt. | Az összekötő ügyféltanúsítvány, amely a hitelesítéshez használatos, lejárt. Ez akkor is előfordulhat, ha az összekötőt proxy mögött telepítette. Ebben az esetben az összekötő nem fér hozzá az internethez, és nem fog tudni alkalmazásokat biztosítani a távoli felhasználók számára. A Windows PowerShell parancsmagjának használatával manuálisan újítsa meg a megbízhatósági kapcsolatot `Register-AppProxyConnector` . Ha az összekötő egy proxy mögött van, akkor internet-hozzáférést kell biztosítania a "hálózati szolgáltatások" és a "helyi rendszer" összekötő fiókjaihoz. Ezt úgy teheti meg, hogy hozzáférést biztosít számukra a proxyhoz, vagy a proxy megkerüléséhez beállítja őket. |
| Az összekötő regisztrálása nem sikerült: Győződjön meg arról, hogy a Active Directory alkalmazás-rendszergazdája az összekötő regisztrálásához. Hiba: "a regisztrációs kérelem megtagadva." | A bejelentkezni próbáló alias nem rendszergazda ezen a tartományon. Az összekötő mindig telepítve van a felhasználó tartományát birtokló könyvtárba. Győződjön meg arról, hogy a bejelentkezni próbáló rendszergazdai fiókja atleast alkalmazás-rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz. |
| Az összekötő hálózati problémák miatt nem tudott kapcsolódni a szolgáltatáshoz. Az összekötő megpróbálta elérni a következő URL-címet. | Az összekötő nem tud csatlakozni az alkalmazásproxy Cloud Service-hez. Ez akkor fordulhat elő, ha egy tűzfalszabály blokkolja a kapcsolatokat. Győződjön meg arról, hogy engedélyezett a megfelelő portok és URL-címek elérése az [alkalmazásproxy előfeltételeiben](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Kerberos-hibák

Ez a táblázat a Kerberos-telepítés és-konfiguráció leggyakoribb hibáit ismerteti, és javaslatokat tartalmaz a megoldásra.

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| Nem sikerült beolvasni a PowerShell-parancsfájlok futtatásának aktuális végrehajtási szabályzatát. | Ha az összekötő telepítése nem sikerül, győződjön meg arról, hogy a PowerShell végrehajtási házirendje nincs letiltva.<br><br>1. Nyissa meg a Csoportházirend szerkesztőt.<br>2. Lépjen a **Számítógép konfigurációja**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Windows PowerShell** elemre, és kattintson duplán **a parancsfájlok végrehajtásának bekapcsolása** lehetőségre.<br>3. a végrehajtási házirend beállítható úgy, hogy **nincs konfigurálva** vagy **engedélyezve**. Ha az **engedélyezve** értékre van állítva, győződjön meg arról, hogy a beállítások területen a végrehajtási házirend a **helyi parancsfájlok és a távoli aláírású parancsfájlok engedélyezése** vagy az **összes parancsfájl engedélyezése** beállítást adja meg. |
| 12008 – az Azure AD túllépte a maximálisan megengedett Kerberos-hitelesítési próbálkozások számát a háttér-kiszolgáló felé. | Ez a hiba helytelen konfigurációt jelezhet az Azure AD és a háttérrendszer-kiszolgáló között, illetve a mindkét gépen az idő és a dátum konfigurációjában tapasztalható problémát. A háttér-kiszolgáló elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttérbeli alkalmazáskiszolgáló megfelelően van-e konfigurálva. Győződjön meg arról, hogy az Azure AD-ben és a háttérbeli alkalmazáskiszolgáló időpontjának és dátumának konfigurációja szinkronizálva van. |
| 13016 – az Azure AD nem tud Kerberos-jegyet beolvasni a felhasználó nevében, mert nincs egyszerű felhasználónév a peremhálózati jogkivonatban vagy a hozzáférési cookie-ban. | Probléma merült fel az STS-konfigurációval kapcsolatban. Javítsa ki az UPN-jogcím konfigurációját az STS-ben. |
| 13019 – az Azure AD nem tud Kerberos-jegyet beolvasni a felhasználó nevében a következő általános API-hiba miatt. | Ez az esemény helytelen konfigurációt jelezhet az Azure AD és a tartományvezérlő kiszolgálója között, illetve a mindkét gépen az idő és a dátum konfigurációjában tapasztalható problémát. A tartományvezérlő elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttérbeli alkalmazáskiszolgáló megfelelően van-e konfigurálva, különösen az SPN-konfigurációval. Győződjön meg arról, hogy az Azure AD tartomány ugyanahhoz a tartományhoz van csatlakoztatva, mint a tartományvezérlő, így biztosítva, hogy a tartományvezérlő megbízhatóan létrehozza az Azure AD-t. Győződjön meg arról, hogy az Azure AD és a tartományvezérlő időpontjának és dátumának konfigurációja szinkronizálva van. |
| 13020 – az Azure AD nem tud Kerberos-jegyet beolvasni a felhasználó nevében, mert nincs megadva a háttér-kiszolgáló SPN-je. | Ez az esemény helytelen konfigurációt jelezhet az Azure AD és a tartományvezérlő kiszolgálója között, illetve a mindkét gépen az idő és a dátum konfigurációjában tapasztalható problémát. A tartományvezérlő elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttérbeli alkalmazáskiszolgáló megfelelően van-e konfigurálva, különösen az SPN-konfigurációval. Győződjön meg arról, hogy az Azure AD tartomány ugyanahhoz a tartományhoz van csatlakoztatva, mint a tartományvezérlő, így biztosítva, hogy a tartományvezérlő megbízhatóan létrehozza az Azure AD-t. Győződjön meg arról, hogy az Azure AD és a tartományvezérlő időpontjának és dátumának konfigurációja szinkronizálva van. |
| 13022 – az Azure AD nem tudja hitelesíteni a felhasználót, mert a háttér-kiszolgáló HTTP 401 hibával válaszol a Kerberos-hitelesítési kísérletekre. | Ez az esemény helytelen konfigurációt jelezhet az Azure AD és a háttérrendszer-kiszolgáló között, illetve a mindkét gépen az idő és a dátum konfigurációjában tapasztalható problémát. A háttér-kiszolgáló elutasította az Azure AD által létrehozott Kerberos-jegyet. Ellenőrizze, hogy az Azure AD és a háttérbeli alkalmazáskiszolgáló megfelelően van-e konfigurálva. Győződjön meg arról, hogy az Azure AD-ben és a háttérbeli alkalmazáskiszolgáló időpontjának és dátumának konfigurációja szinkronizálva van. További információ: [a Kerberos által korlátozott delegálási konfigurációk hibaelhárítása az alkalmazásproxy számára](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Végfelhasználói hibák

Ez a lista azokat a hibákat tartalmazza, amelyekkel a végfelhasználók találkozhatnak, amikor megpróbálnak hozzáférni az alkalmazáshoz, és sikertelenek lesznek. 

| Hiba | Javasolt lépések |
| ----- | ----------------- |
| A webhely nem tudja megjeleníteni a lapot. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az alkalmazás egy IWA alkalmazás. Lehet, hogy az alkalmazáshoz megadott egyszerű szolgáltatásnév helytelen. IWA alkalmazások esetén győződjön meg arról, hogy az alkalmazáshoz konfigurált egyszerű szolgáltatásnév helyes. |
| A webhely nem tudja megjeleníteni a lapot. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az alkalmazás egy OWA-alkalmazás. Ezt a következők okozhatják:<br><li>Az alkalmazáshoz megadott egyszerű szolgáltatásnév helytelen. Győződjön meg arról, hogy az alkalmazáshoz beállított egyszerű szolgáltatásnév helyes.</li><li>Az alkalmazást használó felhasználó egy Microsoft-fiókt használ a bejelentkezéshez, és nem a megfelelő vállalati fiókot használja, vagy a felhasználó vendég felhasználó. Győződjön meg arról, hogy a felhasználó a közzétett alkalmazás tartományával megegyező vállalati fiókkal jelentkezik be. A Microsoft-fiókok felhasználói és vendégei nem férhetnek hozzá a IWA-alkalmazásokhoz.</li><li>Az alkalmazáshoz hozzáférni próbáló felhasználó nincs megfelelően definiálva ehhez az alkalmazáshoz a helyszíni oldalon. Győződjön meg arról, hogy a felhasználó rendelkezik a megfelelő engedélyekkel a háttérbeli alkalmazáshoz a helyszíni gépen. |
| Ez a vállalati alkalmazás nem érhető el. Ön nem jogosult az alkalmazás elérésére. Az engedélyezés sikertelen. Győződjön meg arról, hogy a felhasználót az alkalmazáshoz való hozzáféréssel társítja. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha a vállalati fiók helyett Microsoft-fiókokat használ a bejelentkezéshez. Előfordulhat, hogy a vendég felhasználók is megkapják ezt a hibát. A Microsoft-fiókok felhasználói és a vendégek nem férhetnek hozzá a IWA-alkalmazásokhoz. Győződjön meg arról, hogy a felhasználó a közzétett alkalmazás tartományával megegyező vállalati fiókkal jelentkezik be.<br><br>Lehet, hogy nincs hozzárendelve a felhasználó ehhez az alkalmazáshoz. Lépjen az **alkalmazás** lapra, és a **felhasználók és csoportok** területen rendelje hozzá ezt a felhasználót vagy felhasználói csoportot ehhez az alkalmazáshoz. |
| Ez a vállalati alkalmazás jelenleg nem érhető el. Próbálkozzon újra később... Az összekötő időkorlátja lejárt. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha azokat nem megfelelően definiálták ehhez az alkalmazáshoz a helyszíni oldalon. Győződjön meg arról, hogy a felhasználók rendelkeznek a megfelelő engedélyekkel a háttérbeli alkalmazáshoz a helyszíni gépen. |
| Ez a vállalati alkalmazás nem érhető el. Ön nem jogosult az alkalmazás elérésére. Az engedélyezés sikertelen. Győződjön meg arról, hogy a felhasználó rendelkezik prémium szintű Azure Active Directory-licenccel. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az előfizető rendszergazdája kifejezetten nem rendelt hozzájuk prémium licenccel. Lépjen az előfizető Active Directory **licencek** lapra, és győződjön meg arról, hogy a felhasználó vagy a felhasználói csoport prémium szintű licenccel van társítva. |
| Nem található a megadott állomásnévvel rendelkező kiszolgáló. | Előfordulhat, hogy a felhasználó ezt a hibaüzenetet kapja, amikor megpróbál hozzáférni a közzétett alkalmazáshoz, ha az alkalmazás egyéni tartománya nincs megfelelően konfigurálva. Győződjön meg arról, hogy feltöltött egy tanúsítványt a tartományhoz, és megfelelően konfigurálta a DNS-rekordot az [Azure-beli egyéni tartományok használata](application-proxy-configure-custom-domain.md) című témakörben ismertetett lépéseket követve. ad Application proxy |
|Tiltott: Ez a vállalati alkalmazás nem érhető el, vagy a felhasználó nem hitelesíthető. Győződjön meg arról, hogy a felhasználó a helyszíni AD-ben van definiálva, és hogy a felhasználó hozzáfér az alkalmazáshoz a helyszíni AD-ben. | Ez az engedélyezési adatokhoz való hozzáféréssel kapcsolatos probléma lehet: [egyes alkalmazások és API-k hozzáférését igénylik a fiók objektumainak engedélyezési adataihoz]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information). Dióhéjban vegye fel az App proxy-összekötő számítógépfiókját a "Windows-engedélyezési hozzáférés csoport" beépített tartományi csoportba a megoldáshoz. |

## <a name="my-error-wasnt-listed-here"></a>A hiba nem szerepel a listán

Ha a jelen hibaelhárítási útmutatóban nem szereplő Azure AD Application Proxy hibával vagy problémával találkozik, azt szeretnénk hallani. Küldjön egy e-mailt a [visszajelzési csapatnak](mailto:aadapfeedback@microsoft.com) az észlelt hiba részleteivel.

## <a name="see-also"></a>Lásd még
* [Alkalmazásproxy engedélyezése Azure Active Directory számára](application-proxy-add-on-premises-application.md)
* [Alkalmazások közzététele az alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

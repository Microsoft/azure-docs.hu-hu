---
title: Oktatóanyag – helyszíni alkalmazás-proxy hozzáadása az Azure AD-ben
description: Azure Active Directory (Azure AD) olyan alkalmazásproxy-szolgáltatással rendelkezik, amely lehetővé teszi a felhasználók számára, hogy az Azure AD-fiókjával való bejelentkezéssel hozzáférjenek a helyszíni alkalmazásokhoz. Ez az oktatóanyag bemutatja, hogyan készítheti elő környezetét az alkalmazásproxy használatához. Ezután a Azure Portal használatával ad hozzá egy helyszíni alkalmazást az Azure AD-bérlőhöz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q3
ms.openlocfilehash: 04b3dd51aab9686d102b6b94324f89396a268e7d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260079"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Oktatóanyag: helyi alkalmazás hozzáadása a távoli eléréshez az alkalmazásproxy használatával Azure Active Directory

Azure Active Directory (Azure AD) olyan alkalmazásproxy-szolgáltatással rendelkezik, amely lehetővé teszi a felhasználók számára, hogy az Azure AD-fiókjával való bejelentkezéssel hozzáférjenek a helyszíni alkalmazásokhoz. Ez az oktatóanyag előkészíti a környezetet az alkalmazásproxy használatához. Ha a környezet elkészült, a Azure Portal használatával hozzáadhat egy helyszíni alkalmazást az Azure AD-bérlőhöz.

:::image type="content" source="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png" alt-text="Alkalmazásproxy áttekintő diagramja" lightbox="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png":::

Az összekötők az alkalmazásproxy egyik kulcsfontosságú részét képezik. További információ az összekötők használatáról: az [Azure ad Application proxy-összekötők megismerése](application-proxy-connectors.md).

Ez az oktatóanyag:

> [!div class="checklist"]
> * A kimenő forgalom portjainak megnyitása, valamint a hozzáférés engedélyezése adott URL-címekhez
> * Telepíti az összekötőt a Windows-kiszolgálóra, és regisztrálja azt az alkalmazásproxy-ban.
> * Ellenőrzi, hogy az összekötő telepítve és megfelelően van-e regisztrálva
> * Helyszíni alkalmazás hozzáadását az Azure AD-bérlőhöz
> * Ellenőrzi, hogy egy felhasználó Azure AD-fiókkal tud-e bejelentkezni az alkalmazásba

## <a name="prerequisites"></a>Előfeltételek

Helyszíni alkalmazás Azure AD-hez való hozzáadásához a következők szükségesek:

* [Microsoft Azure ad Premium-előfizetés](https://azure.microsoft.com/pricing/details/active-directory)
* Alkalmazás-rendszergazdai fiók
* A felhasználói identitásokat a helyszíni címtárból kell szinkronizálni, vagy közvetlenül az Azure AD-bérlőn belül kell létrehozni. Identitásszinkronizálás lehetővé teszi az Azure AD számára, hogy előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést adna nekik az App proxy közzétett alkalmazásaihoz, és hogy a szükséges felhasználói azonosító információkkal rendelkezzen az egyszeri bejelentkezés (SSO) végrehajtásához.

### <a name="windows-server"></a>Windows Server

Az alkalmazásproxy használatához Windows Server 2012 R2 vagy újabb rendszert futtató Windows Serverre van szükség. Telepítse az alkalmazásproxy-összekötőt a kiszolgálóra. Az összekötő-kiszolgálónak csatlakoznia kell az Azure-beli alkalmazásproxy-szolgáltatásokhoz és a közzétenni kívánt helyszíni alkalmazásokhoz.

Az éles környezetben való magas rendelkezésre állás érdekében javasoljuk, hogy egynél több Windows Servert válasszon. Ebben az oktatóanyagban az egyik Windows-kiszolgáló elegendő.

> [!IMPORTANT]
> Ha az összekötőt a Windows Server 2019-es verzióra telepíti, le kell tiltania a HTTP2 protokoll támogatását a WinHttp-összetevőben a Kerberos által korlátozott delegáláshoz, hogy megfelelően működjön. Ez alapértelmezés szerint le van tiltva a támogatott operációs rendszerek korábbi verzióiban. A következő beállításkulcs hozzáadásával és a kiszolgáló újraindításával letiltja azt a Windows Server 2019 rendszeren. Vegye figyelembe, hogy ez egy számítógép-szintű beállításkulcs.
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp]
> "EnableDefaultHTTP2"=dword:00000000
> ```
>
> A kulcs a PowerShell használatával állítható be a következő paranccsal.
> ```
> Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\' -Name EnableDefaultHTTP2 -Value 0
>

#### <a name="recommendations-for-the-connector-server"></a>Az összekötő-kiszolgáló javaslatai

1. Az összekötő és az alkalmazás közötti teljesítmény optimalizálása érdekében fizikailag keresse meg az összekötő-kiszolgálót az alkalmazáskiszolgáló közelébe. További információ: [hálózati topológia szempontjai](application-proxy-network-topology.md).
1. Az összekötő-kiszolgálónak és a webalkalmazás-kiszolgálóknak ugyanahhoz a Active Directory tartományhoz vagy span megbízó tartományhoz kell tartozniuk. Ha a kiszolgálók ugyanabban a tartományban vagy megbízható tartományban vannak, akkor az egyszeri bejelentkezés (SSO) integrált Windows-hitelesítéssel (IWA) és a Kerberos által korlátozott delegálással (KCD) való használatának követelménye. Ha az összekötő-kiszolgáló és a webalkalmazás-kiszolgálók különböző Active Directory tartományokban találhatók, az egyszeri bejelentkezéshez erőforrás-alapú delegálást kell használnia. További információ: [KCD az egyszeri bejelentkezéshez az alkalmazásproxy](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Ha telepítette az Azure AD jelszavas védelmi proxyt, ne telepítse az Azure AD Application Proxy és az Azure AD jelszavas védelmi proxyt ugyanarra a gépre. Az Azure AD Application Proxy és az Azure AD jelszavas védelmi proxy a Azure AD Connect Agent Updater szolgáltatás különböző verzióit telepíti. Ezek a verziók nem kompatibilisek egymással, ha ugyanazon a gépen vannak telepítve.

#### <a name="tls-requirements"></a>TLS-követelmények

Az alkalmazásproxy-összekötő telepítése előtt a Windows Connector-kiszolgálónak engedélyezve kell lennie a TLS 1,2-nek.

A TLS 1,2 engedélyezése:

1. Állítsa be a következő beállításkulcsokat:

   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Indítsa újra a kiszolgálót.

> [!Note]
> A Microsoft frissíti az Azure-szolgáltatásokat, hogy TLS-tanúsítványokat használjanak egy másik legfelső szintű hitelesítésszolgáltatóktól (CA). Ez a változás azért történik, mert az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok nem felelnek meg a HITELESÍTÉSSZOLGÁLTATÓ/böngésző alapkonfigurációjának egyik követelményének. További információért lásd az [Azure TLS-tanúsítvány módosításait](../../security/fundamentals/tls-certificate-changes.md) ismertető témakört.

## <a name="prepare-your-on-premises-environment"></a>A helyszíni környezet előkészítése

Először is engedélyezze az Azure-adatközpontok kommunikációját, hogy előkészítse a környezetet az Azure AD Application Proxy számára. Ha van tűzfal az elérési úton, győződjön meg róla, hogy meg van nyitva. A nyílt tűzfal lehetővé teszi, hogy az összekötő HTTPS-(TCP-) kéréseket hozzon az alkalmazásproxy számára.

> [!IMPORTANT]
> Ha a Azure Government-felhőhöz tartozó összekötőt telepíti, kövesse az [Előfeltételek](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) és a [telepítés lépéseit](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Ehhez engedélyezni kell a hozzáférést egy másik URL-készlethez, és egy további paramétert a telepítés futtatásához.

### <a name="open-ports"></a>Portok megnyitása

Nyissa meg a következő portokat a **kimenő** forgalom számára.

   | Portszám | Használat célja |
   | --- | --- |
   | 80 | A tanúsítvány-visszavonási listák (CRL-ek) letöltése a TLS/SSL-tanúsítvány ellenőrzése közben |
   | 443 | Minden kimenő kommunikáció az alkalmazásproxy szolgáltatással |

Ha a tűzfal a kezdeményező felhasználók alapján kényszeríti a forgalmat, a 80-es és a 443-es portot is nyissa meg a hálózati szolgáltatásként futó Windows-szolgáltatásokból érkező forgalomhoz.

### <a name="allow-access-to-urls"></a>URL-címek elérésének engedélyezése

A következő URL-címek elérésének engedélyezése:

| URL-cím | Port | Használat célja |
| --- | --- | --- |
| &ast;. msappproxy.net<br>&ast;. servicebus.windows.net | 443/HTTPS | Kommunikáció az összekötő és az alkalmazásproxy Cloud Service között |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP |Az összekötő ezeket az URL-címeket használja a tanúsítványok ellenőrzéséhez. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;. microsoftonline-p.com<br>&ast;. msauth.net<br>&ast;. msauthimages.net<br>&ast;. msecnd.net<br>&ast;. msftauth.net<br>&ast;. msftauthimages.net<br>&ast;. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com<br>www.microsoft.com/pkiops | 443/HTTPS |Az összekötő ezeket az URL-címeket használja a regisztrációs folyamat során. |
| ctldl.windowsupdate.com | 80/HTTP |Az összekötő ezt az URL-címet használja a regisztrációs folyamat során. |

A (z &ast; ). msappproxy.net, &ast; . servicebus.Windows.net és más URL-címekkel is engedélyezheti a kapcsolatokat, ha a tűzfal vagy a proxy lehetővé teszi a hozzáférési szabályok tartományi utótagok alapján történő konfigurálását. Ha nem, engedélyeznie kell az [Azure IP-címtartományok és a szolgáltatás-címkék nyilvános felhőhöz](https://www.microsoft.com/download/details.aspx?id=56519)való hozzáférését. Az IP-címtartományok hetente frissülnek.

### <a name="dns-name-resolution-for-azure-ad-application-proxy-endpoints"></a>DNS-névfeloldás Azure AD Application Proxy-végpontokhoz

Az Azure AD Application Proxy-végpontok nyilvános DNS-rekordjai egy rekordra mutató láncolt CNAME rekordok. Ez biztosítja a hibatűrést és a rugalmasságot. Garantáljuk, hogy az Azure AD Application Proxy-összekötő mindig a _*. msappproxy.net_ vagy _*. servicebus.Windows.net_ tartomány-utótaggal fér hozzá a gazdagépekhez. A névfeloldás során azonban előfordulhat, hogy a CNAME rekordok különböző állomásnévvel és utótaggal rendelkező DNS-rekordokat tartalmazhatnak.  Emiatt biztosítania kell, hogy az eszköz (a telepítő-összekötő kiszolgálótól, a tűzfaltól, a kimenő proxytól függően) képes legyen feloldani a láncban lévő összes rekordot, és lehetővé teszi a kapcsolódást a megoldott IP-címekhez. Mivel előfordulhat, hogy a láncban lévő DNS-rekordok időről időre módosulnak, és nem biztosítunk semmilyen List DNS-rekordot.

## <a name="install-and-register-a-connector"></a>Összekötő telepítése és regisztrálása

Az alkalmazásproxy használatához telepítsen egy összekötőt az alkalmazásproxy szolgáltatással használt összes Windows-kiszolgálón. Az összekötő egy olyan ügynök, amely a helyszíni alkalmazás-kiszolgálókról az Azure AD-ben lévő alkalmazásproxy felé irányuló kimenő kapcsolatokat kezeli. Olyan kiszolgálókra is telepíthet összekötőket, amelyeken más hitelesítési ügynökök is telepítve vannak, például Azure AD Connect.


Az összekötő telepítése:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az alkalmazásproxy-t használó címtár alkalmazás-rendszergazdájaként. Ha például a bérlő tartománya contoso.com, akkor a rendszergazdának admin@contoso.com vagy más rendszergazdai aliasnak kell lennie az adott tartományban.
1. Válassza ki a felhasználónevét a jobb felső sarokban. Ellenőrizze, hogy be van-e jelentkezve az alkalmazásproxy-t használó könyvtárba. Ha módosítania kell a címtárakat, válassza a **váltás könyvtárat** , és válasszon egy, az alkalmazásproxy-t használó könyvtárat.
1. A bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.
1. A **kezelés** területen válassza a **alkalmazásproxy** elemet.
1. Válassza az **összekötő szolgáltatás letöltése** lehetőséget.

    ![Az összekötő szolgáltatás letöltése a szolgáltatási feltételek megtekintéséhez](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Olvassa el a szolgáltatási feltételeket. Ha elkészült, válassza a **feltételek elfogadása & Letöltés** lehetőséget.
1. Az ablak alján kattintson a **Futtatás** elemre az összekötő telepítéséhez. Megnyílik egy telepítési varázsló.
1. A szolgáltatás telepítéséhez kövesse a varázsló utasításait. Amikor a rendszer arra kéri, hogy regisztrálja az összekötőt az Azure AD-bérlőhöz tartozó alkalmazásproxy használatával, adja meg az alkalmazás rendszergazdai hitelesítő adatait.
    - Az Internet Explorer (IE) esetében, ha az **IE fokozott biztonsági beállításai** be vannak kapcsolva, előfordulhat, hogy a regisztrációs képernyő nem jelenik **meg**. A hozzáférés megszerzéséhez kövesse a hibaüzenet utasításait. Győződjön meg arról, hogy az **Internet Explorer fokozott biztonsági beállításai** **ki vannak kapcsolva**.

### <a name="general-remarks"></a>Általános megjegyzések

Ha korábban már telepített egy összekötőt, telepítse újra a legújabb verzió beszerzéséhez. A korábban kiadott verziókról és a benne foglalt változásokról a következő témakörben talál további információt: [Application proxy: verzió kiadásának előzményei](application-proxy-release-version-history.md).

Ha úgy dönt, hogy egynél több Windows-kiszolgálót használ a helyi alkalmazásokhoz, telepítenie és regisztrálnia kell az összekötőt az egyes kiszolgálókon. Az összekötőket összekötő csoportokba rendezheti. További információ: [összekötő csoportok](application-proxy-connector-groups.md).

Ha a szervezet proxykiszolgálót használ az internethez való csatlakozáshoz, konfigurálnia kell őket az alkalmazásproxy számára.  További információ: a [meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md). 

További információ az összekötők, a kapacitás megtervezéséről és a naprakész információkról: az [Azure ad Application proxy-összekötők ismertetése](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Ellenőrizze, hogy az összekötő telepítve és regisztrálva van-e

A Azure Portal vagy a Windows Server használatával ellenőrizheti, hogy az új összekötő megfelelően van-e telepítve.

### <a name="verify-the-installation-through-azure-portal"></a>A telepítés ellenőrzése Azure Portal

Az összekötő ellenőrzése és megfelelő regisztrálása:

1. Jelentkezzen be a bérlői könyvtárába a [Azure Portal](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget, majd válassza az **alkalmazásproxy** elemet a **kezelés** szakaszban. Ezen az oldalon az összes összekötő és összekötő csoport is megjelenik.
1. A részletek ellenőrzéséhez tekintse meg az összekötőt. Az összekötőket alapértelmezés szerint ki kell bontani. Ha a megtekinteni kívánt összekötő nincs kibontva, bontsa ki az összekötőt a részletek megtekintéséhez. Az aktív zöld felirat azt jelzi, hogy az összekötő csatlakozni tud a szolgáltatáshoz. Bár a címke zöld színű, a hálózati probléma továbbra is blokkolhatja az összekötőt az üzenetek fogadása során.

    ![Azure AD Application Proxy-összekötők](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Az összekötők telepítésével kapcsolatos további segítségért lásd: [az alkalmazásproxy-összekötő telepítése](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>A telepítés ellenőrzése a Windows Serveren

Az összekötő ellenőrzése és megfelelő regisztrálása:

1. Nyissa meg a Windows-szolgáltatások kezelőjét a **Windows** -kulcsra kattintva, és írja be a *Services. msc parancsot*.
1. Ellenőrizze, hogy a következő két szolgáltatás állapota **fut**-e.
   - A **Microsoft HRE Application proxy-összekötő** lehetővé teszi a kapcsolódást.
   - A **Microsoft aad Application proxy Connector Updater** egy automatizált frissítési szolgáltatás. A frissítés ellenőrzi az összekötő új verzióit, és szükség szerint frissíti az összekötőt.

     ![Az alkalmazásproxy összekötőjének szolgáltatásai – képernyőfelvétel](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Ha a szolgáltatások állapota nem **fut**, kattintson a jobb gombbal az egyes szolgáltatások kiválasztásához, és válassza az **Indítás** lehetőséget.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Helyszíni alkalmazás hozzáadása az Azure AD-hez

Most, hogy előkészítette a környezetet, és telepített egy összekötőt, készen áll a helyszíni alkalmazások hozzáadására az Azure AD-ben.  

1. Jelentkezzen be rendszergazdaként a [Azure Portal](https://portal.azure.com/).
2. A bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **vállalati alkalmazások** lehetőséget, majd válassza az **új alkalmazás** lehetőséget.
4. Válassza a helyszíni **alkalmazás hozzáadása** gombot, amely a helyszíni **alkalmazások** szakaszban található oldal felé mutat. Azt is megteheti, hogy a lap tetején a **saját alkalmazás létrehozása** lehetőséget választja, majd kiválasztja az **alkalmazásproxy konfigurálása lehetőséget a helyi alkalmazásokhoz való biztonságos távoli hozzáféréshez**.
5. A **saját helyszíni alkalmazás hozzáadása** szakaszban adja meg a következő információkat az alkalmazásról:

    | Mező | Leírás |
    | :---- | :---------- |
    | **Név** | Annak az alkalmazásnak a neve, amely megjelenik a saját alkalmazások és a Azure Portalban. |
    | **Belső URL-cím** | Az alkalmazásnak a magánhálózaton belüli elérésére szolgáló URL-cím. Megadhat egyedi elérési utat a háttérkiszolgálón a közzétételhez, míg a kiszolgáló további része nem lesz közzétéve. Így különböző webhelyeket tehet közzé ugyanazon a kiszolgálón, mint a különböző alkalmazásokat, és mindegyiknek saját nevet és hozzáférési szabályokat adhat.<br><br>Ha közzétesz egy útvonalat, győződjön meg róla, hogy az tartalmaz minden szükséges lemezképet, szkriptet és stíluslapot az alkalmazásához. Ha például az alkalmazás a https:/yourapp/app címen található, \/ és a https:/yourapp/Media helyen található rendszerképeket használja \/ , akkor a https:/yourapp/útvonalat kell közzétennie \/ . A belső URL-címnek nem kell a felhasználók által megjelenített kezdőlapnak lennie. További információt a [közzétett alkalmazások egyéni kezdőlapjának beállítása](application-proxy-configure-custom-home-page.md)című témakörben talál. |
    | **Külső URL-cím** | A felhasználók számára az alkalmazásnak a hálózaton kívülről való eléréséhez használt címe. Ha nem szeretné az alapértelmezett alkalmazásproxy-tartományt használni, olvassa el az [Azure ad Application proxy egyéni tartományait](application-proxy-configure-custom-domain.md)ismertető témakört.|
    | **Előzetes hitelesítés** | Hogyan ellenőrzi az alkalmazásproxy a felhasználókat, mielőtt hozzáférést adna nekik az alkalmazáshoz.<br><br>**Azure Active Directory** – az alkalmazásproxy átirányítja a felhasználókat, hogy jelentkezzenek be az Azure ad-be, amely hitelesíti a címtár és az alkalmazás engedélyeit. Azt javasoljuk, hogy ezt a beállítást az alapértelmezett módon tartsa elérhetővé, így kihasználhatja az Azure AD biztonsági funkcióit, például a feltételes hozzáférést és a Multi-Factor Authentication. Az alkalmazás Microsoft Cloud alkalmazás-biztonsággal való figyeléséhez **Azure Active Directory** szükséges.<br><br>**Továbbító** – a felhasználóknak nem kell hitelesíteniük az Azure ad-vel az alkalmazás eléréséhez. Továbbra is beállíthatja a háttérbeli hitelesítési követelményeket. |
    | **Összekötő csoport** | Az összekötők feldolgozzák az alkalmazáshoz való távoli hozzáférést, és az összekötő csoportok segítségével régió, hálózat vagy cél szerint rendszerezheti az összekötőket és az alkalmazásokat. Ha még nincs létrehozva összekötő-csoport, az alkalmazás **alapértelmezés szerint** hozzá lesz rendelve.<br><br>Ha az alkalmazás WebSockets-t használ a csatlakozáshoz, a csoportban lévő összes összekötőnek 1.5.612.0 vagy újabb verziójúnak kell lennie.|

6. Ha szükséges, konfigurálja a **további beállításokat**. A legtöbb alkalmazás esetében ezeket a beállításokat az alapértelmezett állapotukban kell megőrizni. 

    | Mező | Leírás |
    | :---- | :---------- |
    | **Háttérbeli alkalmazások időtúllépése** | Csak akkor állítsa be **ezt az értéket** , ha az alkalmazása lassú a hitelesítéshez és a kapcsolódáshoz. Alapértelmezés szerint a háttérbeli alkalmazás időkorlátja 85 másodpercnél hosszabb. Ha hosszú értékre van állítva, a háttérbeli időtúllépés 180 másodpercre nő. |
    | **HTTP-Only cookie használata** | Állítsa ezt az értéket **Igen** értékre, ha az alkalmazásproxy-cookie-k tartalmazzák a HTTPOnly jelzőt a http-válasz fejlécében. Távoli asztali szolgáltatások használata esetén a **nem** értékre állítsa ezt az értéket.|
    | **Biztonságos cookie használata**| Állítsa **Igen** értékre a cookie-k biztonságos csatornán keresztüli továbbításához, például egy titkosított HTTPS-kéréshez.
    | **Állandó cookie használata**| Ezt az **értéket ne értékre állítsa.** Csak ezt a beállítást használja olyan alkalmazásokhoz, amelyek nem oszthatnak meg sütiket a folyamatok között. A cookie-beállításokkal kapcsolatos további információkért lásd: [cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directoryban](./application-proxy-configure-cookie-settings.md).
    | **URL-címek lefordítása a fejlécekben** | Tartsa meg ezt az értéket **Igen** , kivéve, ha az alkalmazásnak a hitelesítési kérelemben az eredeti állomásfejléc-fejlécet kellett volna megadnia. |
    | **URL-címek fordítása az alkalmazás törzsében** | Ezt az értéket ne **csak akkor tartsa meg, ha** hardcoded HTML-hivatkozásokat más helyszíni alkalmazásokhoz, és ne használjon egyéni tartományokat. További információ: [a fordítás összekapcsolása az alkalmazásproxy szolgáltatással](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ezt az értéket állítsa **Igen** értékre, ha azt tervezi, hogy Microsoft Cloud app Security (MCAS) használatával figyeli az alkalmazást. További információ: [valós idejű alkalmazás-hozzáférés figyelésének konfigurálása Microsoft Cloud app Security és Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Válassza a **Hozzáadás** elemet.

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Készen áll annak tesztelésére, hogy az alkalmazás helyesen van-e hozzáadva. A következő lépésekben hozzáadhat egy felhasználói fiókot az alkalmazáshoz, majd megpróbálhat bejelentkezni.

### <a name="add-a-user-for-testing"></a>Felhasználó hozzáadása teszteléshez

Mielőtt hozzáad egy felhasználót az alkalmazáshoz, győződjön meg arról, hogy a felhasználói fióknak már van engedélye az alkalmazás eléréséhez a vállalati hálózaton belül.

Tesztelési felhasználó hozzáadása:

1. Válassza a **vállalati alkalmazások** lehetőséget, majd válassza ki a tesztelni kívánt alkalmazást.
2. Válassza az **első lépések** lehetőséget, majd válassza **a felhasználó kiosztása teszteléshez** lehetőséget.
3. A **felhasználók és csoportok** területen válassza a **felhasználó hozzáadása** elemet.
4. A **hozzárendelés hozzáadása** területen válassza a **felhasználók és csoportok** lehetőséget. Megjelenik a **felhasználó és a csoportok** szakasz.
5. Válassza ki a hozzáadni kívánt fiókot.
6. Válassza a **kiválasztás**, majd a **hozzárendelés** lehetőséget.

### <a name="test-the-sign-on"></a>A bejelentkezés tesztelése

Az alkalmazásba való bejelentkezés tesztelése:

1. A tesztelni kívánt alkalmazásból válassza a **alkalmazásproxy** lehetőséget.
2. Az oldal tetején válassza az **alkalmazás tesztelése** lehetőséget, hogy tesztet futtasson az alkalmazáson, és ellenőrizze a konfigurációs problémákat.
3. Ügyeljen arra, hogy először indítsa el az alkalmazást az alkalmazásba való bejelentkezés teszteléséhez, majd töltse le a diagnosztikai jelentést az észlelt problémákra vonatkozó megoldási útmutató áttekintéséhez.

Hibaelhárítás: az [alkalmazásproxy problémáinak elhárítása és a hibaüzenetek](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben az oktatóanyagban létrehozott erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban előkészítette a helyszíni környezetet, hogy működjön az Application proxyval, majd telepítse és regisztrálja az alkalmazásproxy-összekötőt. Ezután hozzáadott egy alkalmazást az Azure AD-bérlőhöz. Ellenőrizte, hogy egy felhasználó Azure AD-fiókkal tud-e bejelentkezni az alkalmazásba.

A következőket hajtotta végre:
> [!div class="checklist"]
> * Nyitott portok a kimenő forgalomhoz, és engedélyezett hozzáférés adott URL-címekhez
> * Telepítette az összekötőt a Windows-kiszolgálóra, és regisztrálva van az alkalmazásproxy-ben.
> * Ellenőrizte, hogy az összekötő telepítve és megfelelően van regisztrálva
> * Helyszíni alkalmazás hozzáadva az Azure AD-bérlőhöz
> * Ellenőrizte, hogy egy felhasználó Azure AD-fiókkal tud-e bejelentkezni az alkalmazásba

Készen áll az alkalmazás konfigurálására az egyszeri bejelentkezéshez. Az alábbi hivatkozásra kattintva kiválaszthatja az egyszeri bejelentkezési módszert, és megkeresheti az egyszeri bejelentkezési oktatóanyagokat.

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](sso-options.md#choosing-a-single-sign-on-method)

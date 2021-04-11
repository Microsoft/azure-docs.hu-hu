---
title: Azure AD átmenő hitelesítés – gyors útmutató | Microsoft Docs
description: Ez a cikk az Azure Active Directory (Azure AD) átmenő hitelesítéssel való ismerkedést ismerteti.
services: active-directory
keywords: Azure AD Connect átmenő hitelesítés, telepítési Active Directory, szükséges összetevők az Azure AD-hez, egyszeri bejelentkezéshez, egyszeri bejelentkezéshez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c5e71522d53bd4e528b2a5a106a4dcc344df3ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732856"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory átmenő hitelesítés: gyors útmutató

## <a name="deploy-azure-ad-pass-through-authentication"></a>Azure AD átmenő hitelesítés üzembe helyezése

Azure Active Directory (Azure AD) átmenő hitelesítés lehetővé teszi a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be mind a helyszíni, mind a felhőalapú alkalmazásokba. Az átmenő hitelesítés a felhasználókat a következő helyen ellenőrzi: a jelszavak közvetlenül a helyszíni Active Directory.

>[!IMPORTANT]
>Ha AD FS (vagy más összevonási technológiákból) áttelepítését továbbítja a hitelesítésre, javasoljuk, hogy kövesse az [itt](https://aka.ms/adfstoPTADPDownload)közzétett részletes üzembe helyezési útmutatót.

>[!NOTE]
>Ha a Azure Government felhővel történő hitelesítéssel végzi a továbbítást, tekintse meg a [Azure Government hibrid identitással kapcsolatos szempontokat](./reference-connect-government-cloud.md).

Kövesse ezeket az utasításokat az átmenő hitelesítés telepítéséhez a bérlőn:

## <a name="step-1-check-the-prerequisites"></a>1. lépés: az Előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételek vannak érvényben.

>[!IMPORTANT]
>Biztonsági szempontból a rendszergazdáknak az PTA ESP-ügynököt futtató kiszolgálót kell kezelnie, mintha tartományvezérlővé tennék.  Az PTA-ügynök kiszolgálóit meg kell erősíteni a [tartományvezérlők támadás elleni biztonságossá](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack) tételével megegyező sorok mentén.

### <a name="in-the-azure-active-directory-admin-center"></a>A Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú globális rendszergazdai fiókot az Azure AD-bérlőn. Így kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások meghibásodnak vagy elérhetetlenné válnak. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról](../fundamentals/add-users-azure-active-directory.md). Ennek a lépésnek a befejezése kritikus fontosságú annak biztosítása érdekében, hogy ne legyen kizárva a bérlőből.
2. Adjon hozzá egy vagy több [Egyéni tartománynevet](../fundamentals/add-custom-domain.md) az Azure ad-bérlőhöz. A felhasználók a következő tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-on-premises-environment"></a>Helyszíni környezetben

1. Azure AD Connect futtatásához azonosítson egy Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálót. Ha már nincs engedélyezve, [engedélyezze a TLS 1,2-et a kiszolgálón](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Adja hozzá a kiszolgálót ugyanahhoz a Active Directory erdőhöz, mint azon felhasználók számára, akiknek a jelszavát ellenőrizni kell. Fontos megjegyezni, hogy Pass-Through hitelesítési ügynök telepítése a Windows Server Core verziókon nem támogatott. 
2. Telepítse a [Azure ad Connect legújabb verzióját](https://www.microsoft.com/download/details.aspx?id=47594) az előző lépésben azonosított kiszolgálóra. Ha már Azure AD Connect fut, győződjön meg arról, hogy a verzió 1.1.750.0 vagy újabb.

    >[!NOTE]
    >A 1.1.557.0, a 1.1.558.0, a 1.1.561.0 és a 1.1.614.0 verziók a jelszó-kivonat szinkronizálásával kapcsolatos problémával rendelkeznek. Azure AD Connect Ha _nem_ kívánja használni a jelszó-kivonatolási szinkronizálást az átmenő hitelesítéssel együtt, olvassa el a [Azure ad Connect kibocsátási megjegyzéseit](./reference-connect-version-history.md).

3. Azonosítson egy vagy több további kiszolgálót (Windows Server 2012 R2 vagy újabb rendszert futtató, TLS 1,2 engedélyezve), ahol önálló hitelesítési ügynököket futtathat. Ezek a további kiszolgálók szükségesek a bejelentkezéshez szükséges kérelmek magas rendelkezésre állásának biztosításához. Adja hozzá a kiszolgálókat ugyanahhoz a Active Directory erdőhöz, mint azok a felhasználók, akiknek a jelszavát ellenőrizni kell.

    >[!IMPORTANT]
    >Éles környezetekben javasoljuk, hogy legalább 3 hitelesítési ügynököt futtasson a bérlőn. A rendszer legfeljebb 40 hitelesítési ügynököt alkalmaz a bérlők esetében. A legjobb megoldás az, ha a hitelesítési ügynököket futtató összes kiszolgálót 0. szintű rendszerként kezeli (lásd a [hivatkozást](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Ha tűzfal található a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
   - Győződjön meg arról, hogy a hitelesítési ügynökök a következő portokon tehetik meg a *kimenő* kérelmeket az Azure ad-nek:

     | Portszám | Használat célja |
     | --- | --- |
     | **80** | Letölti a visszavont tanúsítványok listáját (CRL) a TLS/SSL-tanúsítvány ellenőrzése közben. |
     | **443** | Kezeli a szolgáltatással folytatott összes kimenő kommunikációt |
     | **8080** (nem kötelező) | A hitelesítési ügynökök tíz percenként jelentést készítenek az állapotukról az 8080-as porton keresztül, ha a 443-es port nem érhető el. Az állapot az Azure AD portálon látható. A 8080-es port _nem_ használatos a felhasználói bejelentkezésekhez. |
     
     Ha a tűzfal a felhasználók helye szerint érvényesíti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatások adatforgalma számára.
   - Ha a tűzfal vagy a proxy lehetővé teszi, hogy DNS-bejegyzéseket adjon hozzá egy engedélyezési, vegyen fel kapcsolatokat a **\* . msappproxy.net** és a **\* . servicebus.Windows.net**. Ha nem, engedélyezze a hozzáférést az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek hetente frissülnek.
   - Kerülje az Azure átadó ügynök és az Azure-végpont közötti, kimenő TLS-kommunikációra vonatkozó, beágyazott ellenőrzés és Befejezés összes formáját. 
   - Ha kimenő HTTP-proxyval rendelkezik, győződjön meg arról, hogy az URL-cím (autologon.microsoftazuread-sso.com) szerepel az engedélyezett listán. Explicit módon meg kell adnia ezt az URL-címet, mert a helyettesítő karakterek nem fogadhatók el. 
   - A hitelesítési ügynököknek hozzá kell férniük a **login.Windows.net** és a **login.microsoftonline.com** a kezdeti regisztrációhoz. Nyissa meg a tűzfalat az URL-címekhez is.
    - A tanúsítvány érvényesítéséhez oldja fel a következő URL-címeket: **crl3.Digicert.com:80**, **crl4.Digicert.com:80**, **ocsp.digicert.com:80**, **www \. d-trust.net:80**, **root-C3-Ca2-2009.OCSP.d-Trust.net:80**, **CRL.microsoft.com:80**, **oneocsp.microsoft.com:80** és **OCSP.msocsp.com:80**. Mivel ezek az URL-címek más Microsoft-termékekkel való tanúsítvány-érvényesítéshez használatosak, előfordulhat, hogy az URL-címeket feloldják.

### <a name="azure-government-cloud-prerequisite"></a>Azure Government a felhő előfeltételei
Mielőtt engedélyezné az átmenő hitelesítés engedélyezését Azure AD Connect a 2. lépéssel, töltse le az PTA ESP-ügynök legújabb kiadását a Azure Portal.  Győződjön meg arról, hogy az ügynök verziója **1.5.1742.0.** kell fordítani.  Az ügynök ellenőrzéséhez lásd: a [hitelesítési ügynökök frissítése](how-to-connect-pta-upgrade-preview-authentication-agents.md)

Az ügynök legújabb kiadásának letöltése után folytassa az alábbi utasításokat a Pass-Through hitelesítés Azure AD Connect használatával történő konfigurálásához.

## <a name="step-2-enable-the-feature"></a>2. lépés: a funkció engedélyezése

Áteresztő hitelesítés engedélyezése [Azure ad Connecton](whatis-hybrid-identity.md)keresztül.

>[!IMPORTANT]
>Az átmenő hitelesítést engedélyezheti a Azure AD Connect elsődleges vagy átmeneti kiszolgálón. Erősen ajánlott engedélyezni az elsődleges kiszolgálóról. Ha a jövőben egy Azure AD Connect átmeneti kiszolgálót állít be, továbbra is az átmenő hitelesítést **kell** választania bejelentkezési lehetőségként; egy másik lehetőség választása **letiltja** az átmenő hitelesítést a bérlőn, és felülbírálja a beállítást az elsődleges kiszolgálón.

Ha első alkalommal telepíti a Azure AD Connect, válassza az [egyéni telepítési útvonalat](how-to-connect-install-custom.md). A **felhasználói bejelentkezés** lapon válassza az **áteresztő hitelesítés** lehetőséget a **bejelentkezési módszerként**. A sikeres befejezést követően az áteresztő hitelesítési ügynök ugyanarra a kiszolgálóra van telepítve, mint a Azure AD Connect. Emellett az áteresztő hitelesítés funkció engedélyezve van a bérlőn.

![Azure AD Connect: felhasználói bejelentkezés](./media/how-to-connect-pta-quick-start/sso3.png)

Ha már telepítette Azure AD Connect az [expressz telepítés](how-to-connect-install-express.md) vagy az [egyéni telepítési](how-to-connect-install-custom.md) útvonal használatával, jelölje be a **felhasználói bejelentkezési feladat módosítása** Azure ad Connecton lehetőséget, majd kattintson a **tovább** gombra. Ezután válassza az **átmenő hitelesítés** lehetőséget a bejelentkezési módszerként. A sikeres befejezést követően az áteresztő hitelesítési ügynök ugyanarra a kiszolgálóra van telepítve, mint a Azure AD Connect, és a szolgáltatás engedélyezve van a bérlőn.

![Azure AD Connect: felhasználói bejelentkezés módosítása](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Az átmenő hitelesítés a bérlői szintű szolgáltatás. A bekapcsolás hatással van a felhasználók bejelentkezésére a bérlő _összes_ felügyelt tartományában. Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS)-ről átmenő hitelesítésre vált át, várjon legalább 12 órát az AD FS-infrastruktúra leállítása előtt. Ez a várakozási idő annak biztosítása, hogy a felhasználók a váltás során is megtarthatják a bejelentkezést az Exchange ActiveSync szolgáltatásba. Ha további segítségre van a AD FSról átmenő hitelesítésre való áttelepítéssel kapcsolatban, tekintse meg az [itt](https://aka.ms/adfstoptadpdownload)közzétett részletes üzembe helyezési tervet.

## <a name="step-3-test-the-feature"></a>3. lépés: a funkció tesztelése

Kövesse ezeket az utasításokat annak ellenőrzéséhez, hogy az áteresztő hitelesítés helyesen van-e beállítva:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **Azure ad Connect** lehetőséget.
4. Győződjön meg arról, hogy az **átmenő hitelesítés** funkció **engedélyezve** állapotú.
5. Válassza **az átmenő hitelesítés** lehetőséget. Az **átmenő hitelesítés** panel felsorolja azokat a kiszolgálókat, amelyeken a hitelesítési ügynökök telepítve vannak.

![Azure Active Directory felügyeleti központ: Azure AD Connect panel](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory felügyeleti központ: átmenő hitelesítés panel](./media/how-to-connect-pta-quick-start/pta8.png)

Ebben a szakaszban a bérlő összes felügyelt tartományának felhasználói bejelentkezhetnek az átmenő hitelesítés használatával. Az összevont tartományokból származó felhasználók azonban továbbra is bejelentkeznek AD FS vagy egy korábban konfigurált összevonási szolgáltató használatával. Ha a tartományt összevontról felügyelt értékre konvertálja, az adott tartomány összes felhasználója automatikusan megkezdi a bejelentkezést az átmenő hitelesítés használatával. Az átmenő hitelesítés funkció nem érinti a csak felhőalapú felhasználókat.

## <a name="step-4-ensure-high-availability"></a>4. lépés: a magas rendelkezésre állás biztosítása

Ha éles környezetben tervezi az átmenő hitelesítés telepítését, további önálló hitelesítési ügynököket kell telepítenie. Telepítse ezeket a hitelesítési ügynököt a kiszolgáló ( _k) ra a Azure ad Connect-t_ nem futtató kiszolgálókon. A telepítő magas rendelkezésre állást biztosít a felhasználói bejelentkezési kérésekhez.

>[!IMPORTANT]
>Éles környezetekben javasoljuk, hogy legalább 3 hitelesítési ügynököt futtasson a bérlőn. A rendszer legfeljebb 40 hitelesítési ügynököt alkalmaz a bérlők esetében. A legjobb megoldás az, ha a hitelesítési ügynököket futtató összes kiszolgálót 0. szintű rendszerként kezeli (lásd a [hivatkozást](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Több átmenő hitelesítési ügynök telepítése biztosítja a magas rendelkezésre állást, de nem determinisztikus a hitelesítési ügynökök közötti terheléselosztást. Annak megállapításához, hogy hány hitelesítési ügynökre van szüksége a bérlőhöz, vegye figyelembe a bérlőn megtekinteni kívánt bejelentkezési kérelmek maximális és átlagos terhelését. Viszonyítási alapként egyetlen hitelesítési ügynök a 300-400 hitelesítések másodpercenkénti kezelésére alkalmas standard 4 magos CPU, 16 GB RAM-kiszolgáló esetén.

A hálózati forgalom becsléséhez használja a következő méretezési útmutatót:
- Minden kérelemnek van egy adattartalom mérete (0,5 K + 1K * num_of_agents) bájt, azaz az Azure AD-ből származó adatok a hitelesítési ügynöknek. Itt a "num_of_agents" a bérlőn regisztrált hitelesítési ügynökök számát jelzi.
- Minden válaszhoz tartozik egy 1K bájtos adattartalom mérete, azaz a hitelesítési ügynöktől az Azure AD felé irányuló adat.

A legtöbb ügyfél esetében az összesen három hitelesítési ügynök elegendő a magas rendelkezésre álláshoz és a kapacitáshoz. A bejelentkezési késés javítása érdekében telepítse a hitelesítési ügynököket a tartományvezérlőhöz közel.

Ennek elkezdéséhez kövesse az alábbi utasításokat a hitelesítési ügynök szoftverének letöltéséhez:

1. A hitelesítési ügynök legújabb verziójának (1.5.193.0 vagy újabb verzió) letöltéséhez jelentkezzen be a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **Azure ad Connect** lehetőséget, válassza az **átmenő hitelesítés** lehetőséget, majd válassza az **ügynök letöltése** lehetőséget.
4. Válassza a **feltételek elfogadása & Letöltés** gombot.

![Azure Active Directory felügyeleti központ: hitelesítési ügynök letöltése gomb](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory felügyeleti központ: ügynök ablaktábla letöltése](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Emellett közvetlenül is [letöltheti a hitelesítési ügynök szoftverét](https://aka.ms/getauthagent). A telepítés _előtt_ tekintse át és fogadja el a hitelesítési ügynök szolgáltatási [feltételeit](https://aka.ms/authagenteula) .

Önálló hitelesítési ügynök üzembe helyezése kétféleképpen lehetséges:

Először a letöltött hitelesítési ügynök végrehajtható fájljának futtatásával és a bérlő globális rendszergazdai hitelesítő adatainak megadásával, ha a rendszer kéri, interaktív módon is megteheti.

Másodszor, létrehozhat és futtathat felügyelet nélküli telepítési parancsfájlt. Ez akkor lehet hasznos, ha több hitelesítési ügynököt szeretne egyszerre telepíteni, vagy olyan Windows-kiszolgálókra szeretné telepíteni a hitelesítési ügynököket, amelyeken nincs engedélyezve a felhasználói felület, vagy nem fér hozzá a Távoli asztalhoz. Az alábbi útmutató a módszer használatát ismerteti:

1. A következő parancs futtatásával telepítheti a hitelesítési ügynököt: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q` .
2. A hitelesítési ügynököt a Windows PowerShell használatával is regisztrálhatja a szolgáltatásban. Hozzon létre egy PowerShell hitelesítő adatokat `$cred` tartalmazó objektumot, amely tartalmazza a bérlőhöz tartozó globális rendszergazdai felhasználónevet és jelszót. Futtassa a következő parancsot, és cserélje le *\<username\>* *\<password\>* :

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. Nyissa meg a **C:\Program Files\Microsoft Azure ad Connect hitelesítési ügynököt** , és futtassa a következő szkriptet a `$cred` létrehozott objektum használatával:

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>Ha egy hitelesítési ügynök telepítve van egy virtuális gépen, a virtuális gép nem telepíthető másik hitelesítési ügynök beállítására. Ez a metódus nem **támogatott**.

## <a name="step-5-configure-smart-lockout-capability"></a>5. lépés: az intelligens zárolási képesség konfigurálása

Az intelligens zárolás segítséget nyújt a felhasználók jelszavának kitalálása, illetve a találgatásos kényszerített módszerek használatával történő beolvasásához. Az intelligens zárolási beállítások Azure AD-ben való konfigurálásával és/vagy a helyszíni Active Directory a megfelelő zárolási beállításokkal a támadások kiszűrhetők a Active Directory elérése előtt. [Ebből a cikkből](../authentication/howto-password-smart-lockout.md) megtudhatja, hogyan konfigurálhatja az intelligens zárolási beállításokat a bérlőn a felhasználói fiókok védetté tételéhez.

## <a name="next-steps"></a>Következő lépések
- [Migrálás ad FSról áteresztő hitelesítésre](https://aka.ms/adfstoptadp) – részletes útmutató a AD FS (vagy más összevonási technológiákból) áttelepített hitelesítéshez.
- [Intelligens zárolás](../authentication/howto-password-smart-lockout.md): megtudhatja, hogyan konfigurálhatja az intelligens zárolási funkciót a bérlőn a felhasználói fiókok védetté tételéhez.
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md): megtudhatja, hogy mely forgatókönyvek támogatottak az átmenő hitelesítéssel, és melyek nem.
- [Technikai](how-to-connect-pta-how-it-works.md)részletes információ: az átmenő hitelesítés funkciójának megismerése.
- [Gyakori](how-to-connect-pta-faq.md)kérdések: válaszok keresése a gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): megtudhatja, Hogyan oldhatók fel az áteresztő hitelesítési szolgáltatással kapcsolatos gyakori problémák.
- [Biztonsági](how-to-connect-pta-security-deep-dive.md)részletes információk: technikai információ kérése az átmenő hitelesítés funkcióról.
- [Azure ad – zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md): További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): használja a Azure Active Directory fórumot az új szolgáltatásokra vonatkozó kérelmek fájljának megjelenítéséhez.

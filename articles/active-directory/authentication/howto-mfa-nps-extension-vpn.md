---
title: VPN az Azure AD MFA-val az NPS bővítmény használatával – Azure Active Directory
description: Integrálja a VPN-infrastruktúrát az Azure AD MFA-val a Microsoft Azure hálózati házirend-kiszolgálójának bővítménnyel.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c4c5c4e632943ebbe68003f663aebbeaab9ebaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743445"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-ad-mfa-by-using-the-network-policy-server-extension-for-azure"></a>A VPN-infrastruktúra integrálása az Azure AD MFA-val az Azure-hoz készült hálózati házirend-kiszolgáló bővítménnyel

Az Azure-hoz készült hálózati házirend-kiszolgáló (NPS) bővítmény lehetővé teszi a szervezetek számára Remote Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítés védelmét a felhőalapú [Azure ad-multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md)használatával, amely kétlépéses ellenőrzést biztosít.

Ez a cikk útmutatást nyújt a hálózati házirend-kiszolgáló infrastruktúrájának MFA-nal való integrálásához az Azure NPS-bővítményének használatával. Ez a folyamat lehetővé teszi a kétlépéses hitelesítés biztonságos használatát olyan felhasználók számára, akik VPN használatával próbálnak csatlakozni a hálózathoz.

A hálózati házirend-és elérési szolgáltatások lehetővé teszi a szervezetek számára a következőket:

* Rendeljen hozzá egy központi helyet a megadható hálózati kérelmek kezeléséhez és vezérléséhez:

  * Kik csatlakozhatnak

  * A napi kapcsolatok időpontjának engedélyezése

  * A kapcsolatok időtartama

  * Az ügyfelek által a kapcsolódáshoz használt biztonsági szint

    Ahelyett, hogy a házirendeket az egyes VPN-vagy Távoli asztali átjáró-kiszolgálókon kellene megadni, ezt követően a központi helyen kell lenniük. A RADIUS protokoll központosított hitelesítés, engedélyezés és nyilvántartás (AAA) biztosítására szolgál.

* A hálózatvédelmi (NAP-) ügyfelek állapot-házirendjeinek létrehozása és kényszerítése, amelyek meghatározzák, hogy az eszközök nem korlátozott vagy korlátozott hozzáférést kapnak-e a hálózati erőforrásokhoz.

* Adja meg a 802.1 x-kompatibilis vezeték nélküli hozzáférési pontokhoz és Ethernet-kapcsolókhoz való hozzáférés hitelesítésének és engedélyezésének módját.
  További információ: [hálózati házirend-kiszolgáló](/windows-server/networking/technologies/nps/nps-top).

A biztonság növelése és a magas szintű megfelelőség biztosítása érdekében a szervezetek az NPS-t az Azure AD-Multi-Factor Authentication integrálva biztosítják, hogy a felhasználók kétlépéses ellenőrzéssel csatlakozzanak a VPN-kiszolgáló virtuális portjához. Ahhoz, hogy a felhasználók hozzáférjenek a hozzáféréshez, meg kell adniuk a felhasználónevet és a jelszót, valamint az általuk vezérelt egyéb információkat. Ennek az információnak megbízhatónak kell lennie, és nem lehet könnyen duplikált. Tartalmazhat egy mobiltelefon-számot, egy vezetékes számot vagy egy mobileszközön lévő alkalmazást is.

Az Azure-hoz készült NPS-bővítmény rendelkezésre állása előtt azoknak az ügyfeleknek, akik az integrált hálózati házirend-kiszolgáló és az MFA-környezetek kétlépéses ellenőrzését szeretnék megvalósítani, egy külön MFA-kiszolgálót kellett konfigurálni és karbantartani egy helyszíni környezetben. Ezt a hitelesítési típust a Távoli asztali átjáró és az Azure Multi-Factor Authentication-kiszolgáló a RADIUS használatával ajánlja fel.

Az Azure-hoz készült NPS-bővítmény használatával a szervezetek a helyszíni vagy a felhőalapú MFA-megoldás üzembe helyezésével biztonságossá tehetik a RADIUS-ügyfél hitelesítését.

## <a name="authentication-flow"></a>Hitelesítési folyamat

Amikor a felhasználók egy VPN-kiszolgálón lévő virtuális porthoz csatlakoznak, először különböző protokollok használatával kell hitelesíteniük magukat. A protokollok lehetővé teszik a Felhasználónév és jelszó és a tanúsítványalapú hitelesítési módszerek kombinációjának használatát.

Az identitás hitelesítése és ellenőrzése mellett a felhasználóknak a megfelelő betárcsázási engedélyekkel kell rendelkezniük. Egyszerű implementációkban a hozzáférés engedélyezését engedélyező betárcsázási engedélyek közvetlenül a Active Directory felhasználói objektumokon vannak beállítva.

![Betárcsázás lap Active Directory felhasználók és számítógépek felhasználói tulajdonságok](./media/howto-mfa-nps-extension-vpn/image1.png)

Az egyszerű implementációkban minden VPN-kiszolgáló megadja vagy megtagadja a hozzáférést az egyes helyi VPN-kiszolgálókon definiált szabályzatok alapján.

Nagyobb és skálázható implementációkban a VPN-hozzáférést megadó vagy megtagadó házirendek központosítva vannak a RADIUS-kiszolgálókon. Ezekben az esetekben a VPN-kiszolgáló olyan hozzáférési kiszolgálóként (RADIUS-ügyfélként) működik, amely a kapcsolódási kérelmeket és a fiókok üzeneteit egy RADIUS-kiszolgálóra továbbítja. A VPN-kiszolgáló virtuális portjához való csatlakozáshoz a felhasználóknak hitelesíteniük kell magukat, és meg kell felelniük a RADIUS-kiszolgálókon központilag definiált feltételeknek.

Ha az Azure-hoz készült NPS-bővítmény integrálva van a hálózati házirend-kiszolgálóval, a sikeres hitelesítési folyamat eredménye a következő:

1. A VPN-kiszolgáló hitelesítési kérést kap egy VPN-felhasználótól, amely tartalmazza az erőforráshoz való csatlakozáshoz használt felhasználónevet és jelszót, például egy Távoli asztal munkamenetet.
2. RADIUS-ügyfélként a VPN-kiszolgáló átalakítja a kérést egy RADIUS *Access-Request* üzenetre, és elküldi (titkosított jelszóval) arra a RADIUS-kiszolgálóra, ahol az NPS-bővítmény telepítve van.
3. A Felhasználónév és a jelszó kombinációja Active Directory ellenőrizve. Ha a Felhasználónév vagy a jelszó helytelen, a RADIUS-kiszolgáló egy *hozzáférés-visszautasítási* üzenetet küld.
4. Ha az NPS-csatlakozási kérelemben és a hálózati házirendekben megadott összes feltétel teljesül (például a napszak vagy a csoporttagság korlátozása), akkor a hálózati házirend-kiszolgáló bővítmény a másodlagos hitelesítésre vonatkozó kérelmet indít el az Azure AD Multi-Factor Authentication.
5. Az Azure AD Multi-Factor Authentication kommunikál a Azure Active Directoryekkel, lekéri a felhasználó adatait, és a felhasználó által konfigurált módszer használatával (mobil telefonhívás, szöveges üzenet vagy mobil alkalmazás) végzi a másodlagos hitelesítés elvégzését.
6. Ha az MFA-kihívás sikeres, az Azure AD Multi-Factor Authentication a hálózati házirend-kiszolgáló bővítményének eredményét továbbítja.
7. Miután a kapcsolódási kísérlet hitelesítése és engedélyezése is megtörtént, az a hálózati házirend-kiszolgáló, amelyen a bővítmény telepítve van, RADIUS *Access-Accept* üzenetet küld a VPN-kiszolgálónak (RADIUS-ügyfélnek).
8. A felhasználó hozzáférést kap a VPN-kiszolgáló virtuális portjához, és egy titkosított VPN-alagutat hoz létre.

## <a name="prerequisites"></a>Előfeltételek

Ez a szakasz részletesen ismerteti azokat az előfeltételeket, amelyeket el kell végezni ahhoz, hogy integrálni lehessen az MFA-t a VPN használatával. Mielőtt elkezdené, meg kell felelnie a következő előfeltételeknek:

* VPN-infrastruktúra
* Hálózati házirend-és elérési szolgáltatások szerepkör
* Azure AD Multi-Factor Authentication licenc
* Windows Server szoftver
* Kódtárak
* Azure Active Directory (Azure AD) szinkronizálva a helyszíni Active Directory
* Azure Active Directory GUID azonosító

### <a name="vpn-infrastructure"></a>VPN-infrastruktúra

Ez a cikk azt feltételezi, hogy rendelkezik egy olyan működő VPN-infrastruktúrával, amely Microsoft Windows Server 2016 rendszert használ, és hogy a VPN-kiszolgáló jelenleg nincs konfigurálva a kapcsolódási kérelmek RADIUS-kiszolgálóra történő továbbítására. A cikkben a VPN-infrastruktúrát egy központi RADIUS-kiszolgáló használatára konfigurálja.

Ha nem rendelkezik működő VPN-infrastruktúrával, gyorsan létrehozhat egyet a Microsoft és harmadik felek webhelyein megtalált számos VPN-beállítási oktatóanyag útmutatását követve.

### <a name="the-network-policy-and-access-services-role"></a>A hálózati házirend-és elérési szolgáltatások szerepkör

A hálózati házirend-és elérési szolgáltatások biztosítják a RADIUS-kiszolgáló és az ügyfél működését. Ez a cikk azt feltételezi, hogy telepítette a hálózati házirend-és elérési szolgáltatások szerepkört egy tagkiszolgálón vagy tartományvezérlőn a környezetben. Ebben az útmutatóban egy VPN-konfigurációhoz konfigurálja a RADIUS-t. Telepítse a hálózati házirend-és elérési szolgáltatások szerepkört egy olyan kiszolgálóra, amely *nem* a VPN-kiszolgáló.

A hálózati házirend-és elérési szolgáltatások szerepkör-szolgáltatás Windows Server 2012-es vagy újabb verziójának telepítésével kapcsolatos információkért lásd: HÁLÓZATVÉDELMI állapotházirend- [kiszolgáló telepítése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). A NAP elavult a Windows Server 2016 rendszerben. A hálózati házirend-kiszolgálóval kapcsolatos ajánlott eljárások leírását, beleértve a hálózati házirend-kiszolgáló tartományvezérlőre történő telepítésének javaslatát, lásd: [ajánlott eljárások az NPS-hez](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-licenc

Az Azure AD Multi-Factor Authenticationhoz licenc szükséges, és egy prémium szintű Azure AD, Enterprise Mobility + Security vagy egy Multi-Factor Authentication önálló licenccel érhető el. Az Azure AD MFA-hoz készült, például felhasználónként vagy hitelesítési licenccel rendelkező, fogyasztáson alapuló licencek nem kompatibilisek a hálózati házirend-kiszolgáló bővítménnyel. További információ: [Az Azure ad-multi-Factor Authentication beszerzése](concept-mfa-licensing.md). Tesztelési célból próbaverziós előfizetést is használhat.

### <a name="windows-server-software"></a>Windows Server szoftver

A hálózati házirend-kiszolgáló bővítményének a Windows Server 2008 R2 SP1 vagy újabb verziójára van szükség, amelyen telepítve van a hálózati házirend-és elérési szolgáltatások szerepkör. A jelen útmutatóban szereplő összes lépést a Windows Server 2016-es verzióval hajtották végre.

### <a name="libraries"></a>Kódtárak

A következő kódtárak automatikusan települnek a hálózati házirend-kiszolgáló bővítménnyel:

-    [Vizuális C++ újraterjeszthető csomagok a Visual Studio 2013 (x64) rendszerhez](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Microsoft Azure Active Directory modul a Windows PowerShell-verzió 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Ha a Microsoft Azure Active Directory PowerShell-modul még nem létezik, akkor a telepítési folyamat részeként futtatott konfigurációs parancsfájllal települ. Nem kell előre telepíteni a modult, ha még nincs telepítve.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory szinkronizálva a helyszíni Active Directory

A hálózati házirend-kiszolgáló bővítmény használatához a helyszíni felhasználóknak szinkronizálnia kell Azure Active Directory és engedélyezni kell az MFA-t. Ez az útmutató azt feltételezi, hogy a helyszíni felhasználók szinkronizálása Azure Active Directory Azure AD Connect használatával történik. Az alábbi útmutatást követve engedélyezheti az MFA-felhasználók engedélyezését.

További információ a Azure AD Connectről: a [helyszíni címtárak integrálása a Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID azonosító

A hálózati házirend-kiszolgáló bővítmény telepítéséhez ismernie kell a Azure Active Directory GUID azonosítóját. A Azure Active Directory GUID azonosítójának megkeresésére vonatkozó utasításokat a következő szakaszban találja.

## <a name="configure-radius-for-vpn-connections"></a>RADIUS konfigurálása VPN-kapcsolatokhoz

Ha telepítette a hálózati házirend-kiszolgáló szerepkört egy tagkiszolgálón, úgy kell beállítania, hogy hitelesítse és engedélyezze a VPN-kapcsolatokat kérő VPN-ügyfelet. 

Ez a szakasz azt feltételezi, hogy telepítette a hálózati házirend-és elérési szolgáltatások szerepkört, de nem konfigurálta azt az infrastruktúrában való használatra.

> [!NOTE]
> Ha már van olyan működő VPN-kiszolgálója, amely központosított RADIUS-kiszolgálót használ a hitelesítéshez, kihagyhatja ezt a szakaszt.
>

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása Active Directory

A megfelelő működéshez az NPS-kiszolgálónak regisztrálnia kell Active Directoryban.

1. Nyissa meg a Kiszolgálókezelőt.

2. A Kiszolgálókezelőben válassza az **eszközök**, majd a **hálózati házirend-kiszolgáló** lehetőséget.

3. A hálózati házirend-kiszolgáló konzolon kattintson a jobb gombbal az **NPS (helyi)** elemre, majd válassza **a kiszolgáló regisztrálása a Active Directoryban** lehetőséget. Kattintson kétszer **az OK gombra** .

    ![Kiszolgáló regisztrálása Active Directory menüpontban](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Hagyja nyitva a konzolt a következő eljáráshoz.

### <a name="use-wizard-to-configure-the-radius-server"></a>A RADIUS-kiszolgáló konfigurálása varázsló használatával

A RADIUS-kiszolgáló konfigurálásához használhatja a standard (varázsló alapú) vagy a speciális konfigurációs beállítást. Ez a szakasz azt feltételezi, hogy a varázsló-alapú szabványos konfigurációs beállítást használja.

1. A hálózati házirend-kiszolgáló konzolon válassza az **NPS (helyi)** lehetőséget.

2. A **normál konfiguráció** területen válassza **a RADIUS-kiszolgáló lehetőséget a telefonos vagy VPN-kapcsolatok** esetében, majd válassza a **VPN konfigurálása vagy a telefonos** kapcsolat lehetőséget.

    ![A RADIUS-kiszolgáló konfigurálása telefonos vagy VPN-kapcsolatokhoz](./media/howto-mfa-nps-extension-vpn/image3.png)

3. A **telefonos vagy virtuális magánhálózati kapcsolatok típusának kiválasztása** ablakban válassza a **virtuális magánhálózati kapcsolatok** lehetőséget, majd kattintson a **tovább** gombra.

    ![Virtuális magánhálózati kapcsolatok konfigurálása](./media/howto-mfa-nps-extension-vpn/image4.png)

4. A **telefonos vagy VPN-kiszolgáló megadása** ablakban válassza a **Hozzáadás** lehetőséget.

5. Az **Új RADIUS-ügyfél** ablakban adjon meg egy felhasználóbarát nevet, írja be a VPN-kiszolgáló feloldható nevét vagy IP-címét, majd adjon meg egy közös titkos jelszót. Végezze el a közös titkos jelszó hosszú és összetett megadását. Jegyezze fel, mert a következő szakaszban szüksége lesz rá.

    ![Új RADIUS-ügyféloldali ablak létrehozása](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Válassza **az OK**, majd a **tovább** lehetőséget.

7. A **hitelesítési módszerek konfigurálása** ablakban fogadja el az alapértelmezett kijelölést (**Microsoft titkosított hitelesítés 2. verzió [MS-CHAPv2])** , vagy válasszon másik lehetőséget, majd kattintson a **tovább** gombra.

    > [!NOTE]
    > Ha a bővíthető hitelesítési protokollt (EAP) konfigurálja, akkor a Microsoft Challenge-Handshake Authentication Protocol (CHAPv2) vagy a Protected Extensible Authentication Protocol (PEAP) protokollt kell használnia. Nem támogatott más EAP.

8. A **felhasználói csoportok megadása** ablakban válassza a **Hozzáadás** lehetőséget, majd válasszon ki egy megfelelő csoportot. Ha egyetlen csoport sem létezik, hagyja üresen a kijelölést, hogy minden felhasználó számára hozzáférést biztosítson.

    ![Felhasználói csoportok ablak megadásával engedélyezheti vagy megtagadhatja a hozzáférést](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Kattintson a **Tovább** gombra.

10. Az **IP-szűrők megadása** ablakban válassza a **tovább** lehetőséget.

11. A **titkosítási beállítások megadása** ablakban fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább** gombra.

    ![A titkosítási beállítások megadása ablak](./media/howto-mfa-nps-extension-vpn/image8.png)

12. A **tartománynév megadása** ablakban hagyja üresen a tartománynevet, fogadja el az alapértelmezett beállítást, majd kattintson a **tovább** gombra.

    ![A tartománynév megadása ablak](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Az **Új telefonos vagy virtuális magánhálózati kapcsolatok és RADIUS-ügyfelek befejezése** ablakban válassza a **Befejezés** lehetőséget.

    ![Befejezett konfigurációs ablak](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>A RADIUS-konfiguráció ellenőrzése

Ez a szakasz részletesen ismerteti a varázsló segítségével létrehozott konfigurációt.

1. A hálózati házirend-kiszolgálón a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **RADIUS-ügyfelek** csomópontot, majd válassza a **RADIUS-ügyfelek** lehetőséget.

2. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal a létrehozott RADIUS-ügyfélre, majd válassza a **Tulajdonságok parancsot**. A RADIUS-ügyfél (VPN-kiszolgáló) tulajdonságai az itt láthatóhoz hasonlóak:

    ![A VPN tulajdonságainak és konfigurációjának ellenőrzése](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Válassza a **Mégse** lehetőséget.

4. A hálózati házirend-kiszolgálón, az NPS (helyi) konzolon bontsa ki a **házirendek** csomópontot, majd válassza a **Kapcsolatkérelem-házirendek** elemet. A VPN-kapcsolatok házirend az alábbi képen látható módon jelenik meg:

    ![Kapcsolatkérelem-házirend, amely a VPN-kapcsolati házirendet mutatja](./media/howto-mfa-nps-extension-vpn/image12.png)

5. A **házirendek** területen válassza a **hálózati házirendek** elemet. Meg kell jelennie egy virtuális magánhálózati (VPN) kapcsolatok házirendjének, amely az alábbi képen láthatóhoz hasonló:

    ![A virtuális magánhálózati kapcsolatokra vonatkozó házirendet bemutató hálózati házirendek](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>A VPN-kiszolgáló konfigurálása a RADIUS-hitelesítés használatára

Ebben a szakaszban a VPN-kiszolgálót a RADIUS-hitelesítés használatára konfigurálja. Az utasítások feltételezik, hogy rendelkezik egy VPN-kiszolgáló működő konfigurációjával, de nem konfigurálta a RADIUS-hitelesítés használatára. A VPN-kiszolgáló konfigurálása után ellenőrizze, hogy a konfiguráció a várt módon működik-e.

> [!NOTE]
> Ha már van olyan működő VPN-kiszolgáló konfigurációja, amely RADIUS-hitelesítést használ, akkor kihagyhatja ezt a szakaszt.
>

### <a name="configure-authentication-provider"></a>A hitelesítési szolgáltató konfigurálása

1. A VPN-kiszolgálón nyissa meg a Kiszolgálókezelő eszközt.

2. A Kiszolgálókezelőben válassza az **eszközök**, majd az **Útválasztás és távelérés** lehetőséget.

3. Az **Útválasztás és távelérés** ablakban kattintson a jobb gombbal **\<server name> (helyi)**, majd válassza a **Tulajdonságok** lehetőséget.

4. A **\<server name> (helyi) tulajdonságok** ablakban válassza a **Biztonság** fület.

5. A **Biztonság** lap **hitelesítés szolgáltató** területén válassza a **RADIUS-hitelesítés** lehetőséget, majd válassza a **Konfigurálás** lehetőséget.

    ![RADIUS-hitelesítési szolgáltató konfigurálása](./media/howto-mfa-nps-extension-vpn/image15.png)

6. A **RADIUS-hitelesítés** ablakban válassza a **Hozzáadás** lehetőséget.

7. A **RADIUS-kiszolgáló hozzáadása** ablakban tegye a következőket:

    a. A **kiszolgálónév** mezőbe írja be annak a RADIUS-kiszolgálónak a nevét vagy IP-címét, amelyet az előző szakaszban konfigurált.

    b. A **közös titok** esetében válassza a **módosítás** lehetőséget, majd adja meg a korábban létrehozott és rögzített közös titkos jelszót.

    c. Az **időtúllépés (másodperc)** mezőben adja meg a **30** értéket.  
    Az időtúllépési érték szükséges ahhoz, hogy elegendő idő legyen a második hitelesítési tényező befejezésére. Egyes VPN-ek vagy régiók esetében a 30 másodpercnél nagyobb időtúllépési beállítások szükségesek, hogy a felhasználók ne fogadjanak több telefonhívást. Ha a felhasználók ezt a problémát tapasztalják, növelje az **időtúllépés (másodperc)** értékét a 30 másodperces növekményekben, amíg a probléma nem következik be.

    ![RADIUS-kiszolgáló ablakának hozzáadása az időtúllépés konfigurálásához](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. Válassza az **OK** lehetőséget.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése

Ebben a szakaszban megerősíti, hogy a VPN-ügyfelet a RADIUS-kiszolgáló hitelesíti és engedélyezi, amikor megkísérli a VPN virtuális porthoz való kapcsolódást. Az utasítások azt feltételezik, hogy a Windows 10 rendszert VPN-ügyfélként használja.

> [!NOTE]
> Ha már konfigurált egy VPN-ügyfelet a VPN-kiszolgálóhoz való csatlakozáshoz, és mentette a beállításokat, akkor kihagyhatja a VPN-kapcsolati objektum konfigurálásához és mentéséhez kapcsolódó lépéseket.
>

1. A VPN-ügyfélszámítógépen kattintson a **Start** gombra, majd válassza a **Beállítások** gombot.

2. A **Windows-beállítások** ablakban válassza a **hálózati & Internet** lehetőséget.

3. Válassza a **VPN** lehetőséget.

4. Válassza **a VPN-kapcsolat hozzáadása** lehetőséget.

5. A **VPN-kapcsolat hozzáadása** ablakban a **VPN-szolgáltató** mezőben válassza a **Windows (beépített)** lehetőséget, végezze el a fennmaradó mezőket a megfelelő módon, majd kattintson a **Save (Mentés**) gombra.

    ![A "VPN-kapcsolat hozzáadása" ablak](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Nyissa meg a **Vezérlőpultot**, majd válassza a **hálózati és megosztási központ** elemet.

7. Válassza az **adapter beállításainak módosítása** lehetőséget.

    ![Hálózati és megosztási központ – adapter beállításainak módosítása](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Kattintson a jobb gombbal a VPN-hálózati kapcsolatra, majd válassza a **Tulajdonságok parancsot**.

9. A VPN-tulajdonságok ablakban válassza a **Biztonság** fület.

10. A **Biztonság** lapon győződjön meg arról, hogy csak **Microsoft CHAP 2-es verzió (MS-CHAP v2)** van kiválasztva, majd kattintson **az OK gombra**.

    ![A protokollok engedélyezése lehetőség](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Kattintson a jobb gombbal a VPN-kapcsolatra, majd válassza a **Csatlakozás** lehetőséget.

12. A **Beállítások** ablakban válassza a **kapcsolat** lehetőséget.  
    Sikeres kapcsolat jelenik meg a biztonsági naplóban, a RADIUS-kiszolgálón a 6272-as AZONOSÍTÓJÚ eseményként, ahogy az itt látható:

    ![Sikeres kapcsolattal rendelkező esemény Tulajdonságok ablak](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Hibaelhárítási sugár

Tegyük fel, hogy a VPN-konfiguráció a VPN-kiszolgáló központi RADIUS-kiszolgáló hitelesítéshez és engedélyezéshez való használatára való konfigurálása előtt működik. Ha a konfiguráció működik, valószínű, hogy a problémát a RADIUS-kiszolgáló helytelen konfigurációja vagy érvénytelen Felhasználónév vagy jelszó használata okozza. Ha például a felhasználónévben a másodlagos UPN-utótagot használja, a bejelentkezési kísérlet sikertelen lehet. Használja ugyanazt a fióknevet a legjobb eredményekhez.

Ezen problémák elhárításához ideális kiindulópont a biztonsági eseménynaplók vizsgálata a RADIUS-kiszolgálón. Az események keresésének időpontjának megtakarításához használhatja a szerepköralapú hálózati házirend-és elérési kiszolgáló egyéni nézetét Eseménynaplóban, ahogy az itt látható. "A 6273-es azonosítójú esemény" olyan eseményeket jelez, amelyekben az NPS megtagadta a hozzáférést a felhasználóhoz.

![NPAS-eseményeket megjelenítő Eseménynapló](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication konfigurálása

Ha segítségre van a felhasználóknak a Multi-Factor Authentication konfigurálásához, tekintse meg a [felhőalapú Azure AD multi-Factor Authentication üzembe helyezését](howto-mfa-getstarted.md#create-conditional-access-policy) és a [fiókom kétlépéses ellenőrzéshez történő beállítását](../user-help/multi-factor-authentication-end-user-first-time.md) ismertető cikket.

## <a name="install-and-configure-the-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének telepítése és konfigurálása

Ez a szakasz útmutatást nyújt a VPN konfigurálásához az MFA használatára a VPN-kiszolgálóval való ügyfél-hitelesítéshez.

> [!NOTE]
> A REQUIRE_USER_MATCH beállításkulcs megkülönbözteti a kis-és nagybetűket. Az összes értéket nagybetűs formátumban kell megadni.
>

A hálózati házirend-kiszolgáló bővítmény telepítése és konfigurálása után a kiszolgáló által feldolgozott összes RADIUS-alapú ügyfél-hitelesítés szükséges az MFA használatához. Ha az összes VPN-felhasználó nincs regisztrálva az Azure AD Multi-Factor Authenticationban, a következők valamelyikét végezheti el:

* Egy másik RADIUS-kiszolgáló beállítása a nem MFA használatára konfigurált felhasználók hitelesítéséhez.

* Hozzon létre egy beállításjegyzékbeli bejegyzést, amely lehetővé teszi, hogy a megtámadott felhasználók egy második hitelesítési tényezőt adjanak meg, ha regisztrálva vannak az Azure AD Multi-Factor Authenticationban.

Hozzon létre egy REQUIRE_USER_MATCH nevű új karakterláncot _a HKLM\SOFTWARE\Microsoft\AzureMfa-ben_, és állítsa az értéket *igaz* vagy *hamis* értékre.

![A "felhasználói egyeztetés megkövetelése" beállítás](./media/howto-mfa-nps-extension-vpn/image34.png)

Ha az érték *true (igaz* ) vagy üres, az összes hitelesítési kérelem MFA-Challenge-re vonatkozik. Ha az érték *false (hamis*), a rendszer csak az Azure ad multi-Factor Authenticationban regisztrált felhasználók számára bocsát ki MFA-kihívásokat. A *hamis* beállítást csak tesztelési vagy éles környezetben használhatja a bevezetési időszakban.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>A Azure Active Directory bérlő AZONOSÍTÓjának beszerzése

A hálózati házirend-kiszolgáló bővítmény konfigurációjának részeként meg kell adnia a rendszergazdai hitelesítő adatokat és az Azure AD-bérlő AZONOSÍTÓját. A bérlő AZONOSÍTÓjának beszerzéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.
1. A Azure Portal menüben válassza a **Azure Active Directory** lehetőséget, vagy keresse meg és válassza ki az **Azure Active Directory** elemet bármelyik oldalon.
1. Az **Áttekintés** lapon megjelenik a *bérlő adatai* . A *bérlő azonosítója* mellett válassza a **Másolás** ikont az alábbi képernyőképen látható módon:

   ![A bérlő AZONOSÍTÓjának beolvasása a Azure Portalból](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének telepítése

A hálózati házirend-kiszolgáló bővítményét olyan kiszolgálóra kell telepíteni, amelyen telepítve van a hálózati házirend-és elérési szolgáltatások szerepkör, és amely a tervben RADIUS-kiszolgálóként működik. Ne *telepítse az* NPS-bővítményt a VPN-kiszolgálóra.

1. Töltse le a hálózati házirend-kiszolgáló bővítményt a [Microsoft letöltőközpontból](https://aka.ms/npsmfa).

2. Másolja a telepítő végrehajtható fájlját (*NpsExtnForAzureMfaInstaller.exe*) a hálózati házirend-kiszolgálóra.

3. Az NPS-kiszolgálón kattintson duplán a **NpsExtnForAzureMfaInstaller.exe** elemre, és ha a rendszer kéri, válassza a **Futtatás** lehetőséget.

4. Az **Azure ad MFA-telepítő ablakának NPS-bővítményében** tekintse át a szoftverlicenc-feltételeket, jelölje be az Elfogadom **a licencfeltételeket és a kikötések** jelölőnégyzetet, majd válassza a **telepítés** lehetőséget.

    ![Az "NPS-bővítmény az Azure AD MFA-telepítőhöz" ablak](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Az **Azure ad MFA-telepítő ablakának NPS-bővítményében** válassza a **Bezárás** lehetőséget.  

    ![A "sikeres telepítés" megerősítő ablak](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Tanúsítványok konfigurálása a hálózati házirend-kiszolgáló bővítménnyel való használatra PowerShell-parancsfájl használatával

A biztonságos kommunikáció és biztonság biztosítása érdekében konfigurálja a hálózati házirend-kiszolgáló bővítmény által használandó tanúsítványokat. A hálózati házirend-kiszolgáló összetevői közé tartozik egy Windows PowerShell-parancsfájl, amely egy önaláírt tanúsítványt konfigurál a hálózati házirend-kiszolgálóval való használatra.

A parancsfájl a következő műveleteket hajtja végre:

* Létrehoz egy önaláírt tanúsítványt.
* Társítja a tanúsítvány nyilvános kulcsát az Azure AD szolgáltatásban található egyszerű szolgáltatásnév számára.
* A tanúsítványt a helyi számítógép tárolójában tárolja.
* Hozzáférést biztosít a hálózati felhasználónak a tanúsítvány titkos kulcsához.
* Újraindítja a hálózati házirend-kiszolgáló szolgáltatást.

Ha saját tanúsítványokat kíván használni, társítsa a tanúsítvány nyilvános kulcsát az Azure AD szolgáltatáshoz tartozó egyszerű szolgáltatásnév és így tovább.

A parancsfájl használatához adja meg a bővítményt a Azure Active Directory rendszergazdai hitelesítő adataival, valamint a korábban átmásolt Azure Active Directory bérlői AZONOSÍTÓval. A fióknak ugyanabban az Azure AD-bérlőben kell lennie, mint a bővítmény engedélyezéséhez. Futtassa a parancsfájlt minden olyan NPS-kiszolgálón, amelyre telepíti az NPS-bővítményt.

1. Futtassa a Windows PowerShellt rendszergazdaként.

2. A PowerShell-parancssorba írja be a **következőt: CD "C:\Program Files\Microsoft\AzureMfa\Config"**, majd válassza az ENTER billentyűt.

3. A következő parancssorba írja be **.\AzureMfaNpsExtnConfigSetup.ps1**, majd válassza az ENTER billentyűt. A szkript ellenőrzi, hogy telepítve van-e az Azure AD PowerShell-modul. Ha nincs telepítve, a parancsfájl telepíti a modult.

    ![A AzureMfsNpsExtnConfigSetup.ps1 konfigurációs parancsfájl futtatása](./media/howto-mfa-nps-extension-vpn/image38.png)

    Ha a TLS miatt biztonsági hibaüzenetet kap, engedélyezze a TLS 1,2-et a parancs használatával a `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` PowerShell-parancssorból.
    
    Miután a parancsfájl ellenőrzi a PowerShell-modul telepítését, megjeleníti a Azure Active Directory PowerShell-modul bejelentkezési ablakát.

4. Adja meg az Azure AD-rendszergazdai hitelesítő adatait és jelszavát, majd válassza **a bejelentkezés** lehetőséget.

    ![Hitelesítés az Azure AD PowerShell-lel](./media/howto-mfa-nps-extension-vpn/image39.png)

5. A parancssorba illessze be a korábban másolt bérlői azonosítót, majd válassza az ENTER billentyűt.

    ![Adja meg a korábban másolt Azure AD-bérlő AZONOSÍTÓját](./media/howto-mfa-nps-extension-vpn/image40.png)

    A szkript létrehoz egy önaláírt tanúsítványt, és végrehajtja a többi konfigurációs módosítást. A kimenet az alábbi képen láthatóhoz hasonló:

    ![Önaláírt tanúsítványt megjelenítő PowerShell-ablak](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Indítsa újra a kiszolgálót.

### <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A konfiguráció ellenőrzéséhez létre kell hoznia egy új VPN-kapcsolatot a VPN-kiszolgálóval. Miután sikeresen megadta a hitelesítő adatait az elsődleges hitelesítéshez, a VPN-kapcsolat a kapcsolat létrehozása előtt megvárja a másodlagos hitelesítés sikerességét, ahogy az az alábbi ábrán látható.

![A Windows beállításai VPN-ablak](./media/howto-mfa-nps-extension-vpn/image42.png)

Ha sikeresen elvégezte a hitelesítést az Azure AD MFA-ban korábban konfigurált másodlagos ellenőrzési módszerrel, akkor az erőforráshoz csatlakozik. Ha azonban a másodlagos hitelesítés sikertelen, a rendszer megtagadja a hozzáférést az erőforráshoz.

A következő példában a Windows Phone-telefon Microsoft Authenticator alkalmazás a másodlagos hitelesítést biztosítja:

![Példa MFA-kérésre Windows Phone-telefon](./media/howto-mfa-nps-extension-vpn/image43.png)

Miután sikeresen hitelesítette a hitelesítést a másodlagos módszerrel, hozzáférést kap a virtuális porthoz a VPN-kiszolgálón. Mivel ahhoz, hogy egy megbízható eszközön egy mobileszköz használatával másodlagos hitelesítési módszert kellene használni, a bejelentkezési folyamat biztonságosabb, mint ha csak a Felhasználónév és a jelszó kombinációját használta.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>A sikeres bejelentkezési események Eseménynapló naplóinak megtekintése

Ha meg szeretné tekinteni a sikeres bejelentkezési eseményeket a Windows Eseménynapló naplófájljaiban, a következő PowerShell-parancs megadásával kérdezze le a Windows biztonsági naplót a hálózati házirend-kiszolgálón:

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![PowerShell biztonsági Eseménynapló](./media/howto-mfa-nps-extension-vpn/image44.png)

A biztonsági naplót vagy a hálózati házirend-és elérési szolgáltatások egyéni nézetét is megtekintheti, ahogy az itt látható:

![Példa hálózati házirend-kiszolgáló naplóra](./media/howto-mfa-nps-extension-vpn/image45.png)

Azon a kiszolgálón, amelyen az Azure AD Multi-Factor Authentication NPS-bővítményét telepítette, megtalálhatja a bővítményre vonatkozó, az *alkalmazás-és a szolgáltatások Logs\Microsoft\AzureMfa* Eseménynapló alkalmazás-naplókat.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![Példa Eseménynapló AuthZ-naplók panel](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ha a konfiguráció nem a várt módon működik, a hibaelhárítás megkezdéséhez ellenőrizze, hogy a felhasználó az MFA használatára van-e konfigurálva. A felhasználó csatlakozik a [Azure Portalhoz](https://portal.azure.com). Ha a rendszer megkéri a felhasználót a másodlagos hitelesítésre, és sikeresen hitelesíti magát, az MFA hibájának helytelen konfigurációját lehet kizárni.

Ha az MFA dolgozik a felhasználónál, tekintse át a kapcsolódó Eseménynapló naplókat. A naplók közé tartozik a biztonsági esemény, az átjáró működési és az Azure AD Multi-Factor Authentication az előző szakaszban tárgyalt naplók.

Példa egy olyan biztonsági naplóra, amely egy sikertelen bejelentkezési eseményt jelenít meg (6273-es AZONOSÍTÓJÚ esemény). itt látható:

![Sikertelen bejelentkezési eseményt mutató biztonsági napló](./media/howto-mfa-nps-extension-vpn/image47.png)

Az Azure AD Multi-Factor Authentication log-ből származó kapcsolódó esemény itt látható:

![Azure AD-Multi-Factor Authentication naplók](./media/howto-mfa-nps-extension-vpn/image48.png)

A speciális hibaelhárításhoz forduljon a hálózati házirend-kiszolgáló adatbázis-formátumának naplófájljaihoz, ahol a hálózati házirend-kiszolgáló szolgáltatás telepítve van. A naplófájlok a _%systemroot%\System32\Logs_ mappában, vesszővel tagolt szövegfájlként jönnek létre. A naplófájlok leírását lásd: a [hálózati házirend-kiszolgáló adatbázis-formátumú naplófájljainak értelmezése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)).

A naplófájlokban szereplő bejegyzéseket nehéz értelmezni, hacsak nem exportálja őket egy táblázatba vagy adatbázisba. A naplófájlok értelmezéséhez számos internetes hitelesítési szolgáltatás (IAS) elemzési eszköz található az interneten. Itt látható egy ilyen letölthető [shareware alkalmazás](https://www.deepsoftware.com/iasviewer) kimenete:

![Példa a shareware alkalmazás IAS-elemzője](./media/howto-mfa-nps-extension-vpn/image49.png)

A további hibaelhárításhoz használhat egy protokoll-elemzőt, például a Wireshark vagy a [Microsoft Message Analyzert](/message-analyzer/microsoft-message-analyzer-operating-guide). A Wireshark következő képe a VPN-kiszolgáló és a hálózati házirend-kiszolgáló közötti RADIUS-üzeneteket jeleníti meg.

![Szűrt forgalmat bemutató Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

További információ: [a meglévő NPS-infrastruktúra integrálása az Azure ad-val multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Következő lépések

[Azure AD-Multi-Factor Authentication beolvasása](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
---
title: A RDG integrálása az Azure AD MFA NPS-bővítménnyel – Azure Active Directory
description: A Távoli asztali átjáró-infrastruktúra integrálása az Azure AD MFA-val a hálózati házirend-kiszolgáló bővítmény használatával Microsoft Azure
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
ms.openlocfilehash: 20b0150c18f2c007ed104d34daacd49ab03131a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743377"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>A Távoli asztali átjáró-infrastruktúra integrálása a hálózati házirend-kiszolgáló (NPS) bővítmény és az Azure AD használatával

Ez a cikk részletesen ismerteti a Távoli asztali átjáró infrastruktúra Azure AD Multi-Factor Authentication (MFA) szolgáltatással való integrálásának részleteit az Microsoft Azure hálózati házirend-kiszolgáló (NPS) bővítményének használatával.

Az Azure-hoz készült hálózati házirend-kiszolgáló (NPS) bővítmény lehetővé teszi, hogy az ügyfelek az Azure felhőalapú [multi-Factor Authentication (MFA)](./concept-mfa-howitworks.md)használatával megóvják Remote Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítést. Ez a megoldás kétlépéses ellenőrzést biztosít a felhasználói bejelentkezésekhez és tranzakciókra való második biztonsági réteg hozzáadásához.

Ez a cikk lépésről lépésre bemutatja, hogyan integrálhatja az NPS-infrastruktúrát az Azure AD MFA-val az Azure-hoz készült NPS-bővítmény használatával. Ez lehetővé teszi, hogy a felhasználók biztonságosan ellenőrizhessék a Távoli asztali átjáróba való bejelentkezést.

> [!NOTE]
> Ez a cikk nem használható az MFA-kiszolgáló üzembe helyezéséhez, és csak az Azure AD MFA (felhőalapú) környezetekben használható.

A hálózati házirend-és elérési szolgáltatások (NPS) lehetővé teszi a szervezetek számára a következők elvégzését:

* Adja meg a hálózati kérelmek felügyeletének és felügyeletének központi helyeit a kapcsolódási lehetőség megadásával, a napi kapcsolatok időtartamával, a kapcsolatok időtartamától, valamint az ügyfelek által a csatlakozáshoz használt biztonsági szinttel és így tovább. Ahelyett, hogy ezeket a házirendeket az egyes VPN-vagy Távoli asztal-(RD-) átjárókiszolgálón adja meg, ezeket a házirendeket egy központi helyen lehet megadnia. A RADIUS protokoll biztosítja a központosított hitelesítést, engedélyezést és nyilvántartást (AAA).
* A hálózatvédelmi (NAP-) ügyfelek állapot-házirendjeinek létrehozása és kényszerítése, amelyek meghatározzák, hogy az eszközök nem korlátozott vagy korlátozott hozzáférést kapnak-e a hálózati erőforrásokhoz.
* A 802.1 x-kompatibilis vezeték nélküli hozzáférési pontokhoz és Ethernet-kapcsolókhoz való hozzáférés hitelesítésének és engedélyezésének megkövetelése.

A szervezetek általában a hálózati házirend-kiszolgáló (RADIUS) használatával egyszerűsítik és központosítják a VPN-házirendek kezelését. Számos szervezet azonban a hálózati házirend-kiszolgáló használatával egyszerűsíti és központosítja a távoli asztali asztali kapcsolatengedélyezési házirendek (RD CAPs) felügyeletét.

A szervezetek a hálózati házirend-kiszolgálót az Azure AD MFA-nal is integrálva növelhetik a biztonságot, és magas szintű megfelelőséget biztosíthatnak. Ezzel biztosíthatja, hogy a felhasználók kétlépéses ellenőrzést hozzanak létre a Távoli asztali átjáróba való bejelentkezéshez. Ahhoz, hogy a felhasználók hozzáférjenek a hozzáféréshez, meg kell adniuk a Felhasználónév/jelszó kombinációját, valamint azokat az információkat, amelyekkel a felhasználó a vezérlőben található. Ennek az információnak megbízhatónak kell lennie, és nem lehet könnyen duplikált, mint például a mobiltelefonszám, a vezetékes szám, az alkalmazás mobil eszközön stb. A RDG jelenleg támogatja a Microsoft hitelesítő alkalmazási módszereinek telefonos hívását és leküldéses értesítéseit a 2FA. A támogatott hitelesítési módszerekkel kapcsolatos további információkért tekintse [meg a felhasználók által használható hitelesítési módszerek meghatározását](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)ismertető szakaszt.

Az Azure-hoz készült NPS-bővítmény rendelkezésre állása előtt, az integrált hálózati házirend-kiszolgáló és az Azure AD MFA-környezetek kétlépéses ellenőrzésének megvalósítását igénybe vevő ügyfeleknek egy különálló MFA-kiszolgálót kell konfigurálniuk és karbantartani a helyszíni környezetben, ahogyan azt a [Távoli asztali átjáró és az azure multi-Factor Authentication-kiszolgáló RADIUS használatával](howto-mfaserver-nps-rdg.md)dokumentálták.

A hálózati házirend-kiszolgáló bővítményének rendelkezésre állása mostantól lehetővé teszi a szervezetek számára, hogy a RADIUS-ügyfél hitelesítésének biztosításához helyszíni, MFA-vagy felhőalapú MFA-megoldást telepítsenek.

## <a name="authentication-flow"></a>A hitelesítési folyamat

Ahhoz, hogy a felhasználók hozzáférhessenek a hálózati erőforrásokhoz egy Távoli asztali átjáróon keresztül, meg kell felelniük az egy távoli asztali kapcsolat engedélyezési házirendjében (RD CAP) és egy távoli asztali erőforrás-engedélyezési házirendben (RD RAP) meghatározott feltételeknek. A RD CAPs megadja, hogy ki jogosult a távoli asztali átjáróhoz való kapcsolódásra. RD RAP a hálózati erőforrásokat, például a távoli asztalokat vagy a távoli alkalmazásokat határozza meg, hogy a felhasználó csatlakozhat a RD-átjáróon keresztül.

A RD-átjáró konfigurálható úgy, hogy egy központi házirend-tárolót használjanak a távoli asztali kapcsolatengedélyezési szolgáltatásokhoz. A távoli asztali erőforrás-engedélyezési szolgáltatás nem tud központi házirendet használni, mivel azokat a RD-átjáró dolgozzák fel. A távoli asztali kapcsolatengedélyezési szolgáltatás központi házirendjének tárolására konfigurált RD-átjáró például egy RADIUS-ügyfél egy másik NPS-kiszolgálónak, amely központi házirend-tárolóként szolgál.

Ha az Azure-hoz készült NPS-bővítmény integrálva van a hálózati házirend-kiszolgálóval és a Távoli asztali átjáró, a sikeres hitelesítési folyamat a következő:

1. A Távoli asztali átjáró kiszolgáló hitelesítési kérést kap egy távoli asztali felhasználótól egy erőforráshoz, például egy Távoli asztal-munkamenethez való kapcsolódáshoz. RADIUS-ügyfélként a Távoli asztali átjáró-kiszolgáló átalakítja a kérést egy RADIUS-Access-Request üzenetbe, és elküldi az üzenetet arra a RADIUS (NPS) kiszolgálónak, amelyen az NPS bővítmény telepítve van.
1. A Felhasználónév és a jelszó kombinációja Active Directory ellenőrzése alatt áll, és a felhasználó hitelesítése megtörtént.
1. Ha az NPS-csatlakozási kérelemben és a hálózati házirendekben megadott összes feltétel teljesül (például a napszak vagy a csoporttagság korlátozása), akkor a hálózati házirend-kiszolgáló bővítmény a másodlagos hitelesítésre vonatkozó kérelmet indít el az Azure AD MFA-val.
1. Az Azure AD MFA kommunikál az Azure AD-vel, lekéri a felhasználó adatait, és a támogatott módszerek használatával végrehajtja a másodlagos hitelesítést.
1. Az MFA-kérdés sikeressége után az Azure AD MFA a hálózati házirend-kiszolgáló kiterjesztésére küldi az eredményt.
1. Az NPS-kiszolgáló, ahol a bővítmény telepítve van, egy RADIUS-Access-Accept üzenetet küld a RD CAP szabályzatnak a Távoli asztali átjáró-kiszolgálónak.
1. A felhasználó hozzáférést kap a kért hálózati erőforráshoz a RD-átjáróon keresztül.

## <a name="prerequisites"></a>Előfeltételek

Ez a szakasz részletesen ismerteti az Azure AD MFA és a Távoli asztali átjáró integrálásához szükséges előfeltételeket. Mielőtt elkezdené, a következő előfeltételeket kell megadnia.  

* Távoli asztali szolgáltatások (RDS) infrastruktúra
* Azure AD MFA-licenc
* Windows Server szoftver
* Hálózati házirend-és elérési szolgáltatások (NPS) szerepkör
* Azure Active Directory szinkronizálva a helyszíni Active Directory
* Azure Active Directory GUID azonosító

### <a name="remote-desktop-services-rds-infrastructure"></a>Távoli asztali szolgáltatások (RDS) infrastruktúra

A helyén működő Távoli asztali szolgáltatások (RDS) infrastruktúra szükséges. Ha nem, akkor gyorsan létrehozhatja ezt az infrastruktúrát az Azure-ban a következő rövid útmutató sablon használatával: [Távoli asztal munkamenet-gyűjtemény központi telepítésének létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Ha manuálisan kívánja létrehozni a helyszíni RDS-infrastruktúrát tesztelési célokra, kövesse az üzembe helyezés lépéseit.
**További információ**: [az RDS üzembe helyezése az Azure](/windows-server/remote/remote-desktop-services/rds-in-azure) rövid útmutatóval és az [alapszintű RDS infrastruktúra üzembe helyezésével](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-licenc

A kötelező az Azure AD MFA licence, amely prémium szintű Azure AD vagy más, az azt tartalmazó csomagokon keresztül érhető el. Az Azure AD MFA-hoz készült, például felhasználónként vagy hitelesítési licenccel rendelkező, fogyasztáson alapuló licencek nem kompatibilisek a hálózati házirend-kiszolgáló bővítménnyel. További információ: [Az Azure ad-multi-Factor Authentication beszerzése](concept-mfa-licensing.md). Tesztelési célból próbaverziós előfizetést is használhat.

### <a name="windows-server-software"></a>Windows Server szoftver

A hálózati házirend-kiszolgáló bővítményéhez telepíteni kell a Windows Server 2008 R2 SP1 vagy újabb verzióját a hálózati házirend-kiszolgáló szerepkör-szolgáltatással. Az ebben a szakaszban szereplő összes lépés a Windows Server 2016-es verzióval lett végrehajtva.

### <a name="network-policy-and-access-services-nps-role"></a>Hálózati házirend-és elérési szolgáltatások (NPS) szerepkör

A hálózati házirend-kiszolgáló szerepkör-szolgáltatás biztosítja a RADIUS-kiszolgálót és az ügyfelek funkcióit, valamint a hálózati hozzáférési házirend állapotfigyelő szolgáltatását. Ezt a szerepkört az infrastruktúra legalább két számítógépén kell telepíteni: a Távoli asztali átjáró és egy másik tagkiszolgáló vagy tartományvezérlő. Alapértelmezés szerint a szerepkör már szerepel a Távoli asztali átjáró konfigurált számítógépen.  A hálózati házirend-kiszolgáló szerepkört legalább egy másik számítógépen, például tartományvezérlőn vagy tagkiszolgálón is telepítenie kell.

A hálózati házirend-kiszolgáló szerepkör-szolgáltatás Windows Server 2012-es vagy régebbi verziójának telepítésével kapcsolatos információkért lásd: NAP állapotházirend- [kiszolgáló telepítése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). A hálózati házirend-kiszolgálóval kapcsolatos ajánlott eljárások leírását, beleértve a hálózati házirend-kiszolgáló tartományvezérlőre történő telepítésének javaslatát, lásd: [ajánlott eljárások az NPS-hez](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory szinkronizálva a helyszíni Active Directory

A hálózati házirend-kiszolgáló bővítmény használatához a helyszíni felhasználókat szinkronizálni kell az Azure AD-vel, és engedélyezni kell az MFA-t. Ez a szakasz azt feltételezi, hogy a helyszíni felhasználók az AD-kapcsolat használatával szinkronizálva vannak az Azure AD-vel. Az Azure AD-kapcsolattal kapcsolatos információkért lásd: [a helyszíni címtárak integrálása Azure Active Directorysal](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID azonosító

A hálózati házirend-kiszolgáló bővítmény telepítéséhez ismernie kell az Azure AD GUID azonosítóját. Az Azure AD GUID azonosítójának megkeresésére vonatkozó utasítások alább találhatók.

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication konfigurálása

Ez a szakasz útmutatást nyújt az Azure AD MFA és a Távoli asztali átjáró integrálásához. Rendszergazdaként konfigurálnia kell az Azure AD MFA szolgáltatást, mielőtt a felhasználók saját maguk is regisztrálhatják a multi-Factor Devices vagy alkalmazásaikat.

Kövesse az [Azure ad multi-Factor Authentication használatának első lépései a felhőben](howto-mfa-getstarted.md) című témakör lépéseit, és engedélyezze az MFA használatát az Azure ad-felhasználók számára.

### <a name="configure-accounts-for-two-step-verification"></a>Fiókok konfigurálása kétlépéses ellenőrzéshez

Miután engedélyezte a fiók engedélyezését az MFA-ban, nem tud bejelentkezni az MFA-szabályzat hatálya alá tartozó erőforrásokra egészen addig, amíg sikeresen konfigurálta a megbízható eszközt a második hitelesítési tényezőhöz, és a hitelesítés a kétlépéses ellenőrzés használatával történt.

Kövesse a mit jelent az [Azure AD multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-first-time.md) című témakörben ismertetett lépéseket a felhasználói fiókkal kapcsolatos MFA-eszközök megismeréséhez és megfelelő konfigurálásához.

> [!IMPORTANT]
> Távoli asztali átjáró bejelentkezési viselkedése nem adja meg az Azure AD-Multi-Factor Authenticationt ellenőrző kód megadásának lehetőségét. A felhasználói fiókot a telefonos ellenőrzéshez vagy a Microsoft Authenticator alkalmazáshoz kell konfigurálni leküldéses értesítésekkel.
>
> Ha a két hitelesítési módszer egyike nincs konfigurálva egy felhasználóhoz, nem fog tudni befejezni az Azure AD Multi-Factor Authentication a problémát, és be kell jelentkeznie a Távoli asztali átjáróba.

## <a name="install-and-configure-nps-extension"></a>Az NPS bővítmény telepítése és konfigurálása

Ez a szakasz azt ismerteti, hogyan konfigurálható az RDS infrastruktúra az Azure AD MFA használatára az ügyfél-hitelesítéshez a Távoli asztali átjáró használatával.

### <a name="acquire-azure-active-directory-tenant-id"></a>Azure Active Directory bérlő AZONOSÍTÓjának beszerzése

A hálózati házirend-kiszolgáló bővítmény konfigurációjának részeként rendszergazdai hitelesítő adatokat és az Azure ad-bérlőhöz tartozó Azure AD-azonosítót kell megadnia. A bérlő AZONOSÍTÓjának beszerzéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.
1. A Azure Portal menüben válassza a **Azure Active Directory** lehetőséget, vagy keresse meg és válassza ki az **Azure Active Directory** elemet bármelyik oldalon.
1. Az **Áttekintés** lapon megjelenik a *bérlő adatai* . A *bérlő azonosítója* mellett válassza a **Másolás** ikont az alábbi képernyőképen látható módon:

   ![A bérlő AZONOSÍTÓjának beolvasása a Azure Portalból](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének telepítése

Telepítse a hálózati házirend-kiszolgáló bővítményt egy olyan kiszolgálóra, amelyen telepítve van a hálózati házirend-és elérési szolgáltatások (NPS) szerepkör. Ez a megoldás RADIUS-kiszolgálóként működik.

> [!IMPORTANT]
> Ne telepítse az NPS-bővítményt a Távoli asztali átjáró (RDG) kiszolgálóra. A RDG-kiszolgáló nem használja a RADIUS protokollt az ügyfelével, így a bővítmény nem tudja értelmezni és végrehajtani az MFA-t.
>
> Ha a RDG-kiszolgáló és a hálózati házirend-kiszolgáló (NPS) bővítménye különböző kiszolgálók, a RDG belső hálózati házirend-kiszolgálót használ, hogy más hálózati házirend-kiszolgálókkal kommunikáljon, és a RADIUS protokollt használja a megfelelő kommunikációhoz.

1. Töltse le a [hálózati házirend-kiszolgáló bővítményét](https://aka.ms/npsmfa).
1. Másolja a telepítő végrehajtható fájlját (NpsExtnForAzureMfaInstaller.exe) a hálózati házirend-kiszolgálóra.
1. Az NPS-kiszolgálón kattintson duplán a **NpsExtnForAzureMfaInstaller.exe** elemre. Ha a rendszer kéri, kattintson a **Futtatás** gombra.
1. Az Azure AD MFA-telepítésének NPS-bővítménye párbeszédpanelen tekintse át a szoftverlicenc-feltételeket, ellenőrizze, **hogy elfogadom a licencfeltételeket**, majd kattintson a **telepítés** gombra.
1. Az Azure AD MFA-telepítésének NPS-bővítménye párbeszédpanelen kattintson a **Bezárás** gombra.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Tanúsítványok konfigurálása a hálózati házirend-kiszolgáló bővítménnyel való használatra PowerShell-parancsfájl használatával

Ezután konfigurálnia kell a tanúsítványokat a hálózati házirend-kiszolgáló bővítmény általi használatra, hogy biztosítsa a biztonságos kommunikációt és a biztonságot. A hálózati házirend-kiszolgáló összetevői közé tartozik egy Windows PowerShell-parancsfájl, amely egy önaláírt tanúsítványt konfigurál a hálózati házirend-kiszolgálóval való használatra.

A parancsfájl a következő műveleteket hajtja végre:

* Önaláírt tanúsítvány létrehozása
* A tanúsítvány nyilvános kulcsának társítása az Azure AD-ben az egyszerű szolgáltatásnév számára
* A tanúsítvány tárolása a helyi számítógép-tárolóban
* Hozzáférést biztosít a tanúsítvány titkos kulcsához a hálózati felhasználó számára
* A hálózati házirend-kiszolgáló szolgáltatás újraindítása

Ha saját tanúsítványokat kíván használni, a tanúsítvány nyilvános kulcsát az Azure AD-beli egyszerű szolgáltatáshoz kell rendelnie, és így tovább.

A szkript használatához adja meg a bővítményt az Azure AD-beli rendszergazdai hitelesítő adataival és a korábban átmásolt Azure AD-bérlői AZONOSÍTÓval. Futtassa a parancsfájlt minden olyan NPS-kiszolgálón, amelyre az NPS-bővítményt telepítette. Ezután tegye a következőket:

1. Nyisson meg egy rendszergazdai Windows PowerShell-parancssort.
1. A PowerShell parancssorába írja be a parancsot `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` , majd nyomja le az **ENTER** billentyűt.
1. Írja be `.\AzureMfaNpsExtnConfigSetup.ps1` a értéket, majd nyomja le az **ENTER** billentyűt. A parancsfájl ellenőrzi, hogy telepítve van-e a Azure Active Directory PowerShell-modul. Ha nincs telepítve, a parancsfájl telepíti a modult.

   ![AzureMfaNpsExtnConfigSetup.ps1 futtatása az Azure AD PowerShellben](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Miután a parancsfájl ellenőrzi a PowerShell-modul telepítését, megjeleníti a Azure Active Directory PowerShell-modul párbeszédpanelt. A párbeszédpanelen adja meg az Azure AD-beli rendszergazdai hitelesítő adatait és jelszavát, majd kattintson a **Bejelentkezés** elemre.

   ![Hitelesítés az Azure AD-ben a PowerShellben](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Amikor a rendszer kéri, illessze be a vágólapra korábban másolt *bérlői azonosítót* , majd nyomja le az **ENTER** billentyűt.

   ![A bérlői azonosító bevitele a PowerShellben](./media/howto-mfa-nps-extension-rdg/image6.png)

1. A szkript létrehoz egy önaláírt tanúsítványt, és végrehajtja a többi konfigurációs módosítást. A kimenetnek az alább látható képhez hasonlóan kell lennie.

   ![Az önaláírt tanúsítványt megjelenítő PowerShell kimenete](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>NPS-összetevők konfigurálása Távoli asztali átjáró

Ebben a szakaszban a Távoli asztali átjáró a kapcsolatkérelem-házirendeket és az egyéb RADIUS-beállításokat konfigurálja.

A hitelesítési folyamat megköveteli, hogy a RADIUS-üzenetek a Távoli asztali átjáró és az NPS-kiszolgáló között legyenek cserélve, ahol az NPS bővítmény telepítve van. Ez azt jelenti, hogy a RADIUS-ügyfélbeállítások mind Távoli asztali átjáró, mind a hálózati házirend-kiszolgáló bővítményét futtató NPS-kiszolgálón be kell állítani.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Távoli asztali átjáró-hitelesítési házirendek konfigurálása központi tároló használatára

Távoli asztal a kapcsolatengedélyezési házirendek (RD CAPs) a Távoli asztali átjáró-kiszolgálóhoz való csatlakozás követelményeit határozzák meg. A távoli asztali kapcsolatengedélyezési házirendek helyileg tárolhatók (alapértelmezett), vagy egy központi RD CAP-tárolóban tárolhatók, amely NPS-t futtat. Az Azure AD MFA és a távoli asztali szolgáltatások integrálásának konfigurálásához meg kell adnia egy központi tároló használatát.

1. A RD-átjáró-kiszolgálón nyissa meg a **Kiszolgálókezelő eszközt**.
1. A menüben kattintson az **eszközök** elemre, mutasson a **Távoli asztali szolgáltatások** pontra, majd kattintson az **Távoli asztali átjárókezelő** elemre.
1. A RD-átjárókezelő kattintson a jobb gombbal a **\[ kiszolgáló neve \] (helyi)** elemre, majd kattintson a **Tulajdonságok** elemre.
1. A Tulajdonságok párbeszédpanelen válassza a **RD CAP áruház** lapot.
1. A RD CAP tároló lapon válassza ki a **hálózati házirend-kiszolgálót futtató központi kiszolgáló** elemet. 
1. Az **adja meg a hálózati házirend-kiszolgálót futtató kiszolgáló nevét vagy IP-címét** mezőbe írja be annak a kiszolgálónak az IP-címét vagy kiszolgálójának nevét, amelyre az NPS-bővítményt telepítette.

   ![Adja meg az NPS-kiszolgáló nevét vagy IP-címét](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kattintson a **Hozzáadás** parancsra.
1. A **közös titok** párbeszédpanelen adjon meg egy közös titkos kulcsot, majd kattintson **az OK** gombra. Ügyeljen rá, hogy rögzítse ezt a közös titkot, és tárolja biztonságosan a rekordot.

   >[!NOTE]
   >A közös titok a RADIUS-kiszolgálók és az ügyfelek közötti megbízhatósági kapcsolat létrehozására szolgál. Hozzon létre egy hosszú és összetett titkot.
   >

   ![Közös titok létrehozása a megbízhatósági kapcsolat létrehozásához](./media/howto-mfa-nps-extension-rdg/image11.png)

1. A párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>A RADIUS időtúllépési értékének konfigurálása Távoli asztali átjáró hálózati házirend-kiszolgálón

Annak biztosítása érdekében, hogy a felhasználók hitelesítő adatai ellenőrizhetők legyenek, végezze el a kétlépéses ellenőrzést, a válaszok fogadását és a RADIUS-üzenetekre adott válaszokat, a RADIUS időtúllépési értékét kell módosítania.

1. A RD-átjáró-kiszolgálón nyissa meg a Kiszolgálókezelő eszközt. A menüben kattintson az **eszközök**, majd a **hálózati házirend-kiszolgáló** elemre.
1. Az **NPS (helyi)** konzolon bontsa ki a **RADIUS-ügyfelek és-kiszolgálók** csomópontot, és válassza a **távoli RADIUS-kiszolgáló** lehetőséget.

   ![A hálózati házirend-kiszolgáló felügyeleti konzolja, amely a távoli RADIUS-kiszolgálót mutatja](./media/howto-mfa-nps-extension-rdg/image12.png)

1. A részleteket tartalmazó ablaktáblán kattintson duplán a **terminálszolgáltatási ÁTJÁRÓKISZOLGÁLÓ csoport** elemre.

   >[!NOTE]
   >Ez a RADIUS-kiszolgálócsoport akkor jött létre, amikor konfigurálta a központi kiszolgálót az NPS-házirendekhez. A RD-átjáró a RADIUS-üzeneteket továbbítja erre a kiszolgálóra vagy kiszolgálókra, ha a csoportban egynél több szerepel.
   >

1. A **TS Gateway Server-csoport tulajdonságai** párbeszédpanelen válassza ki a RD capss tárolásához konfigurált NPS-kiszolgáló IP-címét vagy nevét, majd kattintson a **Szerkesztés** gombra.

   ![Válassza ki a korábban konfigurált NPS-kiszolgáló IP-címét vagy nevét](./media/howto-mfa-nps-extension-rdg/image13.png)

1. A **RADIUS-kiszolgáló szerkesztése** párbeszédpanelen válassza a **terheléselosztás** lapot.
1. **A terheléselosztás lapon a** **kérelem eldobása előtt másodpercben megadott számú másodpercben, a kérések eldobása előtt** módosítsa az alapértelmezett értéket 3 értékről egy 30 és 60 másodperc közötti értékre.
1. A **kérések közötti másodpercben, ha a kiszolgáló nem elérhető mezőként van azonosítva** , módosítsa az alapértelmezett 30 másodperc értéket olyan értékre, amely egyenlő vagy nagyobb, mint az előző lépésben megadott érték.

   ![A RADIUS-kiszolgáló időtúllépési beállításainak szerkesztése a terheléselosztás lapon](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Kattintson kétszer az **OK** gombra a párbeszédpanelek bezárásához.

### <a name="verify-connection-request-policies"></a>Kapcsolatkérelem-házirendek ellenőrzése

Alapértelmezés szerint, amikor a RD-átjáró egy központi házirend-tároló használatára konfigurálja a kapcsolódási engedélyezési házirendekhez, a RD-átjáró úgy van konfigurálva, hogy a CAP-kérelmeket a hálózati házirend-kiszolgáló felé továbbítsa. Az NPS-kiszolgáló, amelyen telepítve van az Azure AD MFA bővítmény, feldolgozza a RADIUS-hozzáférési kérést. A következő lépések bemutatják, hogyan ellenőrizheti az alapértelmezett kapcsolatkérelem-házirendet.  

1. A RD-átjáró a hálózati házirend-kiszolgáló (helyi) konzolon bontsa ki a **házirendek** csomópontot, és válassza a **Kapcsolatkérelem-házirendek** elemet.
1. Kattintson duplán a **terminálszolgáltatási ÁTJÁRÓ engedélyezési házirendje** elemre.
1. A **terminálszolgáltatási ÁTJÁRÓ engedélyezési házirendjének tulajdonságai** párbeszédpanelen kattintson a **Beállítások** fülre.
1. A **Beállítások** lap kapcsolatkérelem továbbítása területén kattintson a **hitelesítés** elemre. A RADIUS-ügyfél a hitelesítésre irányuló kérelmek továbbítására van konfigurálva.

   ![A kiszolgálói csoport hitelesítési beállításainak megadása](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kattintson a **Mégse** gombra.

>[!NOTE]
> A kapcsolatkérelem-házirend létrehozásával kapcsolatos további információkért tekintse meg a [Kapcsolatkérelem-házirendek](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) dokumentációjának konfigurálása című cikket. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurálja a hálózati házirend-kiszolgálót azon a kiszolgálón, amelyen a hálózati házirend-bővítmény telepítve van

A hálózati házirend-kiszolgáló bővítményét tartalmazó NPS-kiszolgálónak képesnek kell lennie a RADIUS-üzenetek Exchange-kiszolgálóval való átváltására a Távoli asztali átjáró. Az üzenetváltás engedélyezéséhez konfigurálnia kell az NPS-összetevőket azon a kiszolgálón, amelyen telepítve van az NPS-bővítmény.

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása Active Directory

A megfelelő működéshez az NPS-kiszolgálónak regisztrálnia kell Active Directoryban.

1. A hálózati házirend-kiszolgálón nyissa meg a **Kiszolgálókezelő eszközt**.
1. A Kiszolgálókezelőben kattintson az **eszközök**, majd a **hálózati házirend-kiszolgáló** elemre.
1. A hálózati házirend-kiszolgáló konzolon kattintson a jobb gombbal az **NPS (helyi)** elemre, majd kattintson **a kiszolgáló regisztrálása Active Directory** lehetőségre.
1. Kattintson kétszer **az OK gombra** .

   ![A hálózati házirend-kiszolgáló regisztrálása Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Hagyja nyitva a konzolt a következő eljáráshoz.

### <a name="create-and-configure-radius-client"></a>RADIUS-ügyfél létrehozása és konfigurálása

A Távoli asztali átjáró a hálózati házirend-kiszolgáló RADIUS-ügyfeleként kell konfigurálni.

1. Azon az NPS-kiszolgálón, amelyen a hálózati házirend-kiszolgáló bővítmény telepítve van, az **NPS (helyi)** konzolon kattintson a jobb gombbal a **RADIUS-ügyfelek** elemre, majd kattintson az **új** elemre.

   ![Új RADIUS-ügyfél létrehozása az NPS-konzolon](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Az **Új RADIUS-ügyfél** párbeszédpanelen adjon meg egy rövid nevet, például az _átjárót_, valamint a távoli asztali átjáró kiszolgáló IP-címét vagy DNS-nevét.
1. A **közös titok** és a **közös titkos kulcs megerősítése** mezőkben adja meg a korábban használt titkot.

   ![Felhasználóbarát név és az IP-cím vagy a DNS-cím konfigurálása](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Az új RADIUS-ügyfél párbeszédpanel bezárásához kattintson **az OK** gombra.

### <a name="configure-network-policy"></a>Hálózati házirend konfigurálása

Ne felejtse el, hogy az Azure AD MFA kiterjesztésű NPS-kiszolgáló a kijelölt központi házirend-tároló a kapcsolatengedélyezési házirendhez (CAP). Ezért meg kell valósítania egy CAP-t a hálózati házirend-kiszolgálón az érvényes csatlakozási kérelmek engedélyezéséhez.  

1. Az NPS-kiszolgálón nyissa meg a hálózati házirend-kiszolgáló (helyi) konzolt, bontsa ki a **házirendek** csomópontot, majd kattintson a **hálózati házirendek** elemre.
1. Kattintson a jobb gombbal **a kapcsolatok más hozzáférési kiszolgálókhoz** lehetőségre, majd kattintson az **ismétlődő házirend** elemre.

   ![A kapcsolat duplikálása más hozzáférési kiszolgálók házirendjéhez](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kattintson a jobb gombbal a **kapcsolatok másolása más hozzáférési kiszolgálókhoz** lehetőségre, majd kattintson a **Tulajdonságok** elemre.
1. A **kapcsolatok más hozzáférési kiszolgálókhoz való másolásához** párbeszédpanel **Házirend neve** mezőjében adjon meg egy megfelelő nevet, például _RDG_CAP_. Jelölje be a **házirend engedélyezve** jelölőnégyzetet, majd válassza a **hozzáférés engedélyezése** lehetőséget. Ha szükséges, a **hálózati hozzáférési kiszolgáló típusa** területen válassza a **Távoli asztali átjáró** lehetőséget, vagy hagyja meg a **megadott értéket**.

   ![A szabályzat, az engedélyezés és a hozzáférés engedélyezése](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kattintson a **megkötések** lapra, és jelölje be az **ügyfelek kapcsolódásának engedélyezése a hitelesítési módszer egyeztetése nélkül lehetőséget**.

   ![Hitelesítési módszerek módosítása az ügyfelek kapcsolódásának engedélyezéséhez](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Ha szükséges, kattintson a **feltételek** lapra, és adja meg azokat a feltételeket, amelyeknek teljesülniük kell ahhoz, hogy a csatlakozás engedélyezve legyen (például egy adott Windows-csoport tagsága).

   ![Nem kötelezően megadhatja a kapcsolatok feltételeit](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kattintson az **OK** gombra. Amikor a rendszer kéri, hogy tekintse meg a megfelelő súgótémakört, kattintson a **nem** gombra.
1. Győződjön meg arról, hogy az új szabályzat a lista elejére kerül, hogy a házirend engedélyezve van, és hogy hozzáférést biztosít.

   ![A szabályzat áthelyezése a lista elejére](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

A konfiguráció ellenőrzéséhez be kell jelentkeznie a Távoli asztali átjáróba egy megfelelő RDP-ügyféllel. Ügyeljen arra, hogy olyan fiókot használjon, amelyet a kapcsolatkérelem-házirendek engedélyeznek, és engedélyezve van az Azure AD MFA.

Ahogy az alábbi képen is látható, használhatja a **Távoli asztal webes elérés** lapot.

![Tesztelés Távoli asztal webes eléréssel](./media/howto-mfa-nps-extension-rdg/image25.png)

Miután sikeresen megadta a hitelesítő adatait az elsődleges hitelesítéshez, a Távoli asztal csatlakozás párbeszédpanel megjeleníti a távoli kapcsolat kezdeményezésének állapotát az alább látható módon. 

Ha sikeresen elvégezte a hitelesítést az Azure AD MFA-ban korábban konfigurált másodlagos hitelesítési módszerrel, akkor az erőforráshoz csatlakozik. Ha azonban a másodlagos hitelesítés sikertelen, a rendszer megtagadja a hozzáférést az erőforráshoz. 

![Távoli asztali kapcsolat távoli kapcsolatok kezdeményezése](./media/howto-mfa-nps-extension-rdg/image26.png)

Az alábbi példában a hitelesítő alkalmazás a Windows Phone rendszeren a másodlagos hitelesítés biztosítására szolgál.

![Példa Windows Phone-telefon hitelesítő alkalmazásra, amely tartalmazza az ellenőrzést](./media/howto-mfa-nps-extension-rdg/image27.png)

Miután sikeresen elvégezte a hitelesítést a másodlagos hitelesítési módszer használatával, a rendszer a szokásos módon bejelentkezett a Távoli asztali átjáróba. Mivel azonban másodlagos hitelesítési módszert kell használnia egy megbízható eszközön lévő Mobile alkalmazás használatával, a bejelentkezési folyamat biztonságosabb, mint egyébként.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>A sikeres bejelentkezési események naplóinak megtekintése Eseménynapló

Ha meg szeretné tekinteni a sikeres bejelentkezési eseményeket a Windows Eseménynapló naplófájljaiban, az alábbi Windows PowerShell-paranccsal kérdezheti le a Windows Terminálszolgáltatások és a Windows biztonsági naplóit.

A sikeres bejelentkezési események az átjáró operatív naplóiban való lekérdezéséhez _(Event \ alkalmazás és Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ használja a következő PowerShell-parancsokat:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Ez a parancs azokat a Windows-eseményeket jeleníti meg, amelyek megmutatják, hogy a felhasználó megfelelt az erőforrás-engedélyezési házirend követelményeinek (RD RAP).

![Események megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Ez a parancs megjeleníti azokat az eseményeket, amelyek megmutatják, hogy mikor találkozott a felhasználó a kapcsolat engedélyezési házirendjének követelményeivel

![a kapcsolatkérelem-házirend megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image29.png)

Ezt a naplót és a szűrőt megtekintheti az eseményazonosító, a 300 és a 200 azonosítóval is. A sikeres bejelentkezési események lekérdezéséhez a biztonsági eseménynapló naplóiban használja a következő parancsot:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Ez a parancs a központi hálózati házirend-kiszolgáló vagy a RD-átjáró kiszolgálón is futtatható.

![Sikeres bejelentkezési események mintája](./media/howto-mfa-nps-extension-rdg/image30.png)

A biztonsági naplót vagy a hálózati házirend-és elérési szolgáltatások egyéni nézetét is megtekintheti a lent látható módon:

![Hálózati házirend-és elérési szolgáltatások Eseménynapló](./media/howto-mfa-nps-extension-rdg/image31.png)

Azon a kiszolgálón, amelyen telepítette az Azure AD MFA-hoz készült NPS-bővítményt, megtalálhatja a bővítményre vonatkozó Eseménynapló alkalmazás _-és szolgáltatás-Logs\Microsoft\AzureMfa_.

![Eseménynapló AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Útmutató a hibakereséshez

Ha a konfiguráció nem a várt módon működik, az első lépés a hibakereséshez, hogy ellenőrizze, hogy a felhasználó az Azure AD MFA használatára van-e konfigurálva. A felhasználó csatlakozik a [Azure Portalhoz](https://portal.azure.com). Ha a rendszer felszólítja a felhasználókat a másodlagos ellenőrzésre, és sikeresen hitelesíteni tudják, az Azure AD MFA helytelen konfigurációját törölheti.

Ha az Azure AD MFA a felhasználó (k) számára dolgozik, tekintse át a kapcsolódó eseménynaplókat. Ezek közé tartozik a biztonsági esemény, az átjáró működési és az Azure AD MFA-naplók, amelyeket az előző szakaszban ismertetünk.

Az alábbi példa a biztonsági napló egy olyan kimenetét mutatja be, amely egy sikertelen bejelentkezési eseményt mutat (6273-es AZONOSÍTÓJÚ esemény).

![Sikertelen bejelentkezési esemény mintája](./media/howto-mfa-nps-extension-rdg/image33.png)

Alább látható egy kapcsolódó esemény a AzureMFA-naplókból:

![Azure AD MFA-bejelentkezés mintája Eseménynapló](./media/howto-mfa-nps-extension-rdg/image34.png)

A speciális hibakeresési beállítások végrehajtásához tekintse meg az NPS-adatbázis formátumú naplófájlokat, ahol a hálózati házirend-kiszolgáló szolgáltatás telepítve van. Ezek a naplófájlok a _%systemroot%\System32\Logs_ mappában, vesszővel tagolt szövegfájlként jönnek létre.

A naplófájlok leírását lásd: a [hálózati házirend-kiszolgáló adatbázis-formátumú naplófájljainak értelmezése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)). Az ezekben a naplófájlokban található bejegyzéseket nehéz lehet értelmezni anélkül, hogy azokat importálni kellene egy táblázatba vagy egy adatbázisba. A naplófájlok értelmezéséhez több IAS-elemzőt is találhat online.

Az alábbi képen egy ilyen letölthető [shareware-alkalmazás](https://www.deepsoftware.com/iasviewer)kimenete látható.

![Példa a shareware alkalmazás IAS-elemzője](./media/howto-mfa-nps-extension-rdg/image35.png)

Végül a további hibakeresési lehetőségekhez használhatja a Protocol Analyzert, például a [Microsoft Message Analyzert](/message-analyzer/microsoft-message-analyzer-operating-guide)is.

Az alábbi képen a Microsoft Message Analyzer a **CONTOSO\AliceC** felhasználónevet tartalmazó RADIUS protokollon szűrt hálózati forgalmat jeleníti meg.

![Szűrt forgalmat bemutató Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Következő lépések

[Az Azure AD-Multi-Factor Authentication beszerzése](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
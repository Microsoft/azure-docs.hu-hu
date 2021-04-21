---
title: RDG integrálása Azure AD MFA NPS-bővítvekkel – Azure Active Directory
description: Integrálja a Távoli asztali átjáró-infrastruktúrát a Azure AD MFA a Hálózati házirend-kiszolgáló bővítmény használatával a Microsoft Azure
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
ms.openlocfilehash: 6f50792ec45570f7e90893a97150ea26b63ebf9c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829835"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>A hálózati Távoli asztali átjáró integrálása a Hálózati házirend-kiszolgáló (NPS) bővítmény és az Azure AD használatával

Ez a cikk azt részletezi, hogyan integrálható az Távoli asztali átjáró-infrastruktúra az Azure AD Multi-Factor Authentication (MFA) segítségével a hálózati házirend-kiszolgáló (NPS) bővítmény használatával a Microsoft Azure.

Az Azure Hálózati házirend-kiszolgáló (NPS) bővítménye lehetővé teszi az ügyfelek számára az Remote Authentication Dial-In User Service (RADIUS) ügyfél-hitelesítés védelmét az Azure felhőalapú [többtényezős hitelesítésével (MFA).](./concept-mfa-howitworks.md) Ez a megoldás kétlépéses ellenőrzést biztosít a felhasználói bejelentkezések és tranzakciók második biztonsági rétegének hozzáadásához.

Ez a cikk részletes útmutatást nyújt az NPS-infrastruktúra és az Azure AD MFA az Azure NPS-bővítményével való integrálásához. Ez lehetővé teszi a biztonságos ellenőrzést az olyan felhasználók számára, akik egy Távoli asztali átjáró.

> [!NOTE]
> Ez a cikk nem használható MFA-kiszolgáló üzemelő példányokkal, és csak felhőalapú Azure AD MFA használható.

A Hálózati házirend- és hozzáférési szolgáltatások (NPS) a következőket teszi lehetővé a szervezetek számára:

* Határozza meg a hálózati kérések kezeléséhez és vezérléséhez szükséges központi helyeket a csatlakozások időtartamának, a kapcsolatok időtartamának és az ügyfeleknek a csatlakozáshoz használt biztonsági szintjének megadásával stb. Ahelyett, hogy ezeket a házirendeket minden VPN- vagy Távoli asztal (RD) átjárókiszolgálón megadnék, ezeket a házirendeket egyszer meg lehet adni egy központi helyen. A RADIUS protokoll központi hitelesítést, engedélyezést és nyilvántartást (AAA) biztosít.
* Hozzon létre és kényszerítsen ki hálózatvédelmi (NAP) ügyfél-állapot-házirendeket, amelyek meghatározzák, hogy az eszközök korlátlan vagy korlátozott hozzáférést kapnak-e a hálózati erőforrásokhoz.
* A hitelesítés és az engedélyezés kényszerítése a 802.1x-kompatibilis vezeték nélküli hozzáférési pontokhoz és Ethernet-kapcsolókhoz való hozzáféréshez.

A szervezetek általában a HÁLÓZATI házirend-kiszolgáló (RADIUS) használatával egyszerűsítik és központosítják a VPN-házirendek kezelését. Számos szervezet azonban hálózati házirend-kiszolgálót is használ a távoli asztali kapcsolat engedélyezési házirendek (RD CAP-k) felügyeletének egyszerűsítésére és központosizálására.

A szervezetek a biztonság növelése és a magas szintű megfelelőség érdekében Azure AD MFA hálózati házirend-kiszolgálót is integrálnak a hálózati házirend-kiszolgálóval. Ez segít biztosítani, hogy a felhasználók létrehozzák a kétlépéses ellenőrzést a Távoli asztali átjáró. Ahhoz, hogy a felhasználók hozzáférést kapnak, meg kell adniuk a felhasználónév/jelszó kombinációt a felhasználó által vezérelt információkkal együtt. Ezeknek az információknak megbízhatónak és könnyen duplikálhatónak kell lennie, például egy mobiltelefonszámnak, egy vezetékes számnak, egy mobileszközön található alkalmazásnak stb. Az RDG jelenleg a Microsoft hitelesítő alkalmazás metódusaitól származó telefonhívás- és leküldéses értesítéseket támogatja a 2FA-s hitelesítéshez. További információ a támogatott hitelesítési módszerekről: Annak meghatározása, hogy a felhasználók milyen hitelesítési módszereket [használhatnak.](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)

Az Azure NPS-bővítményének rendelkezésre állása előtt az integrált NPS- és Azure AD MFA-környezetek kétlépéses ellenőrzését megvalósítani kívánó ügyfeleknek külön MFA-kiszolgálót kellett konfigurálnia és fenntartania a helyszíni környezetben az Távoli asztali átjáró-ban és az Azure Multi-Factor Authentication-kiszolgáló-ban a RADIUS használatával dokumentált [módon.](howto-mfaserver-nps-rdg.md)

Az Azure NPS-bővítményének rendelkezésre állása lehetővé teszi a szervezetek számára, hogy helyszíni MFA-megoldást vagy felhőalapú MFA-megoldást telepítsenek a RADIUS-ügyfél hitelesítésének biztonságossá helyezéséhez.

## <a name="authentication-flow"></a>A hitelesítési folyamat

Ahhoz, hogy a felhasználók hozzáférést kapnak a hálózati erőforrásokhoz egy Távoli asztali átjáró-n keresztül, meg kell felelnie egy TÁVOLI asztali kapcsolat engedélyezési házirendben (RD CAP) és egy Távoli asztali erőforrás-engedélyezési házirendben (RD RAP). A távoli asztali hitelesítésszolgáltató határozza meg, hogy ki jogosult a távoli asztali átjárókhoz való csatlakozásra. Az RD RAP-k határozzák meg azokat a hálózati erőforrásokat, például a távoli asztalokat vagy a távoli alkalmazásokat, amelyekhez a felhasználó csatlakozhat a RD-átjáró.

A RD-átjáró konfigurálható központi házirendtároló használatára a távoli asztali hitelesítésszolgáltatói számára. Az RD RAP-k nem használhatnak központi házirendet, mivel a feldolgozás a RD-átjáró. A távoli asztali RD-átjáró házirendtároló használatára konfigurált központi házirendtároló használatára egy példa egy MÁSIK NPS-kiszolgáló radius-ügyfele, amely központi házirendtárolóként szolgál.

Ha az Azure NPS-bővítménye integrálva van az NPS-Távoli asztali átjáró, a sikeres hitelesítési folyamat a következő:

1. A Távoli asztali átjáró kiszolgáló hitelesítési kérést kap egy távoli asztali felhasználótól, hogy csatlakozzon egy erőforráshoz, például egy Távoli asztal munkamenethez. A RADIUS-ügyfélként működő Távoli asztali átjáró-kiszolgáló RADIUS Access-Request-üzenetmé konvertálja a kérést, és elküldi az üzenetet annak a RADIUS- (NPS-) kiszolgálónak, ahol az NPS-bővítmény telepítve van.
1. A felhasználónév és a jelszó kombinációjának ellenőrzése a Active Directory a felhasználó hitelesítésével történik.
1. Ha az NPS-kapcsolatkérésben és a hálózati házirendben megadott összes feltétel teljesül (például a nap ideje vagy csoporttagsági korlátozások), az NPS-bővítmény másodlagos hitelesítésre vonatkozó kérést indít Azure AD MFA.
1. Azure AD MFA Azure AD-val kommunikál, lekéri a felhasználó adatait, és a támogatott módszerekkel elvégzi a másodlagos hitelesítést.
1. Az MFA-feladat sikeres Azure AD MFA az eredményt az NPS-bővítménynek.
1. Az NPS-kiszolgáló, ahol a bővítmény telepítve van, egy RADIUS-Access-Accept üzenetet küld a RD CAP házirendről a Távoli asztali átjáró kiszolgálónak.
1. A felhasználó hozzáférést kap a kért hálózati erőforráshoz a RD-átjáró.

## <a name="prerequisites"></a>Előfeltételek

Ez a szakasz a Azure AD MFA integrációja előtt szükséges előfeltételeket részletezi Távoli asztali átjáró. A kezdéshez a következő előfeltételeknek kell megfelelnie.  

* Távoli asztali szolgáltatások (RDS) infrastruktúra
* Azure AD MFA licenc
* Windows Server szoftver
* Hálózati házirend- és hozzáférési szolgáltatások (NPS) szerepkör
* Azure Active Directory szinkronizálása a helyi Active Directory
* Azure Active Directory GUID-azonosító

### <a name="remote-desktop-services-rds-infrastructure"></a>Távoli asztali szolgáltatások (RDS) infrastruktúra

Működő infrastruktúra Távoli asztali szolgáltatások (RDS) kell lennie. Ha nem, akkor ezt az infrastruktúrát gyorsan létrehozhatja az Azure-ban a következő gyorsindítási sablonnal: Create Távoli asztal Session Collection deployment (Munkamenet-gyűjtemény [Távoli asztal létrehozása).](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)

Ha tesztelési célból manuálisan szeretne létrehozni egy helyszíni RDS-infrastruktúrát, kövesse az üzembe helyezés lépéseit.
**További információ:** [RDS üzembe helyezése az Azure rövid útmutatóval](/windows-server/remote/remote-desktop-services/rds-in-azure) és [az RDS-infrastruktúra alapszintű üzembe helyezésével.](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-ad-mfa-license"></a>Azure AD MFA Licenc

A Kötelező egy licenc a Azure AD MFA, amely a prémium szintű Azure AD csomagon keresztül érhető el. Az nps-Azure AD MFA, például felhasználónkénti vagy hitelesítési licencenkénti használatalapú licencek nem kompatibilisek az NPS-bővítvekkel. További információ: [How to get Azure AD Multi-Factor Authentication (Az Azure AD többtényezős hitelesítésének lekért használata).](concept-mfa-licensing.md) Tesztelési célokra próba-előfizetést használhat.

### <a name="windows-server-software"></a>Windows Server szoftver

Az NPS-bővítményhez Windows Server 2008 R2 SP1 vagy magasabb verzió szükséges, és telepítve van az NPS szerepkör-szolgáltatás. Az ebben a szakaszban található összes lépés a Windows Server 2016 használatával történik.

### <a name="network-policy-and-access-services-nps-role"></a>Hálózati házirend- és hozzáférési szolgáltatások (NPS) szerepkör

Az NPS szerepkör-szolgáltatás biztosítja a RADIUS-kiszolgálót és az ügyfélfunkciókat, valamint a hálózati hozzáférési házirend állapotszolgáltatását. Ezt a szerepkört az infrastruktúra legalább két számítógépén telepíteni kell: a Távoli asztali átjáró és egy másik tagkiszolgálóra vagy tartományvezérlőre. Alapértelmezés szerint a szerepkör már jelen van a számítógép számítógépeként konfigurált Távoli asztali átjáró.  Az NPS szerepkört is telepítenie kell legalább egy másik számítógépre, például egy tartományvezérlőre vagy tagkiszolgálóra.

Az NPS szerepkör-szolgáltatás Windows Server 2012 vagy régebbi rendszerű telepítésével kapcsolatos információkért [lásd: NAP Állapot-házirend-kiszolgáló telepítése.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)) Az NPS ajánlott eljárásait, beleértve az NPS tartományvezérlőre való telepítésére vonatkozó javaslatot is, lásd: Ajánlott eljárások az [NPS-hez.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10))

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory szinkronizálása a helyi Active Directory

Az NPS-bővítmény használata esetén a helyszíni felhasználókat szinkronizálni kell az Azure AD-val, és engedélyezni kell az MFA-t. Ez a szakasz feltételezi, hogy a helyszíni felhasználók szinkronizálva vannak az Azure AD-val az AD Connect használatával. További információ az Azure AD Connectről: Helyszíni könyvtárak integrálása a [Azure Active Directory.](../hybrid/whatis-hybrid-identity.md)

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-azonosító

Az NPS-bővítmény telepítéséhez meg kell tudni az Azure AD GUID-azonosítóját. Az Azure AD GUID azonosítóját az alábbiakban találja.

## <a name="configure-multi-factor-authentication"></a>A Multi-Factor Authentication konfigurálása

Ez a szakasz az integrációs Azure AD MFA a Távoli asztali átjáró. Rendszergazdaként konfigurálnia kell a Azure AD MFA szolgáltatást, mielőtt a felhasználók ön regisztrálják többtényezős eszközeiket vagy alkalmazásukat.

Az MFA Azure AD-felhasználók számára való engedélyezéséhez kövesse az [Azure AD Multi-Factor Authentication a](howto-mfa-getstarted.md) felhőben való használatának első lépéseit.

### <a name="configure-accounts-for-two-step-verification"></a>Fiókok konfigurálása kétlépéses ellenőrzéshez

Miután engedélyezte a fiókot az MFA-hoz, nem tud bejelentkezni az MFA-szabályzat által szabályozott erőforrásokba, amíg nem konfigurált sikeresen egy megbízható eszközt a második hitelesítési tényezőhöz való használatra, és nem hitelesítette magát kétlépéses ellenőrzéssel.

Kövesse a Mit jelent az [Azure AD Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-first-time.md) lépéseit, hogy megértse és megfelelően konfigurálja az eszközöket az MFA-hoz a felhasználói fiókjával.

> [!IMPORTANT]
> Az azure-Távoli asztali átjáró bejelentkezési viselkedése nem biztosít lehetőséget ellenőrző kód beíratára az Azure AD Multi-Factor Authentication használatával. A felhasználói fiókot telefonos ellenőrzésre vagy a leküldéses értesítésekkel Microsoft Authenticator alkalmazáshoz kell konfigurálni.
>
> Ha sem a telefonos ellenőrzés, sem a Microsoft Authenticator-alkalmazás leküldéses értesítésekkel nincs konfigurálva a felhasználó számára, a felhasználó nem fogja tudni végrehajtani az Azure AD Multi-Factor Authenticationt, és nem tud bejelentkezni a Távoli asztali átjáró.
>
> Az SMS szöveges metódus nem működik a Távoli asztali átjáró mert nem biztosít lehetőséget ellenőrző kód beíratása céljából.

## <a name="install-and-configure-nps-extension"></a>NPS-bővítmény telepítése és konfigurálása

Ez a szakasz útmutatást nyújt az RDS-infrastruktúra konfigurálásához Azure AD MFA ügyfél-hitelesítéshez a Távoli asztali átjáró.

### <a name="acquire-azure-active-directory-tenant-id"></a>Bérlőazonosító Azure Active Directory le

Az NPS-bővítmény konfigurációjának részeként meg kell adnunk az Azure AD-bérlő rendszergazdai hitelesítő adatait és Azure AD-azonosítóját. A bérlőazonosító lekért lépései a következők:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) azure-bérlő globális rendszergazdájaként.
1. A Azure Portal válassza a **Azure Active Directory** lehetőséget, vagy keresse meg és Azure Active Directory **bármelyik** oldalon.
1. Az Áttekintés **lapon** megjelennek *a bérlő adatai.* A *bérlőazonosító mellett* válassza a **Másolás** ikont, ahogyan az alábbi példa képernyőképen látható:

   ![A bérlőazonosító lekért Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Az NPS-bővítmény telepítése

Telepítse az NPS-bővítményt egy olyan kiszolgálóra, amely rendelkezik telepített Hálózati házirend- és hozzáférési szolgáltatások (NPS) szerepkörrel. Ez a kialakítás RADIUS-kiszolgálóként működik.

> [!IMPORTANT]
> Ne telepítse az NPS-bővítményt a Távoli asztali átjáró (RDG) kiszolgálóra. Az RDG-kiszolgáló nem használja a RADIUS protokollt az ügyféllel, így a bővítmény nem tudja értelmezni és végrehajtani az MFA-t.
>
> Ha az RDG-kiszolgáló és az NPS-bővítményt használó NPS-kiszolgáló eltérő kiszolgálók, az RDG belsőleg az NPS-t használja más NPS-kiszolgálókkal való kommunikációhoz, és a RADIUS protokollt használja a megfelelő kommunikációhoz.

1. Töltse le az [NPS-bővítményt.](https://aka.ms/npsmfa)
1. Másolja a telepítő végrehajtható fájlját (NpsExtnForAzureMfaInstaller.exe) az NPS-kiszolgálóra.
1. Az NPS-kiszolgálón kattintson duplán a **NpsExtnForAzureMfaInstaller.exe.** Ha a rendszer kéri, kattintson a **Futtatás gombra.**
1. Az NPS-bővítmény telepítése Azure AD MFA tekintse át a szoftverlicenc-feltételeket, jelölje be az **Elfogadom** a licencfeltételeket jelölőnégyzetet, majd kattintson a **Telepítés gombra.**
1. A telepítés nps-Azure AD MFA párbeszédpanelen kattintson a Bezárás **gombra.**

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Tanúsítványok konfigurálása az NPS-bővítvánnyal való használatra Egy PowerShell-szkript használatával

Ezután konfigurálnia kell az NPS-bővítmény által használt tanúsítványokat a biztonságos kommunikáció és biztonság biztosítása érdekében. Az NPS-összetevők tartalmaznak egy Windows PowerShell parancsfájlt, amely konfigurál egy önaírt tanúsítványt az NPS-sel való használatra.

A szkript a következő műveleteket végzi el:

* Önaírt tanúsítványt hoz létre
* Társítja a tanúsítvány nyilvános kulcsát egy szolgáltatásnévhez az Azure AD-ban
* A tanúsítványt a helyi számítógép tárolójában tárolja
* Hozzáférést biztosít a tanúsítvány titkos kulcsához a hálózati felhasználó számára
* Újraindítja a hálózati házirend-kiszolgáló szolgáltatást

Ha saját tanúsítványokat szeretne használni, társítania kell a tanúsítvány nyilvános kulcsát az Azure AD szolgáltatásnévhez stb.

A szkript használatának érdekében adja meg a bővítményt az Azure AD-rendszergazdai hitelesítő adataival és a korábban kimásott Azure AD-bérlőazonosítóval. Futtassa a szkriptet minden olyan NPS-kiszolgálón, ahol telepítette az NPS-bővítményt. Ezután tegye a következőket:

1. Nyisson meg egy Windows PowerShell parancssort.
1. A PowerShell parancssorba írja be a parancsot, `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` majd nyomja le az ENTER **billentyűt.**
1. Írja be `.\AzureMfaNpsExtnConfigSetup.ps1` a parancsot, majd nyomja le az **ENTER billentyűt.** A szkript ellenőrzi, hogy telepítve van-e Azure Active Directory PowerShell-modul. Ha nincs telepítve, a szkript telepíti Önnek a modult.

   ![Alkalmazások AzureMfaNpsExtnConfigSetup.ps1 Azure AD PowerShellben](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Miután a szkript ellenőrizte a PowerShell-modul telepítését, megjelenik Azure Active Directory PowerShell-modul párbeszédpanelje. A párbeszédpanelen adja meg az Azure AD rendszergazdai hitelesítő adatait és jelszavát, majd kattintson **a Bejelentkezés gombra.**

   ![Hitelesítés az Azure AD-be a PowerShellben](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Amikor a rendszer  kéri, illessze be a vágólapra korábban kimásolt bérlőazonosítót, majd nyomja le az **ENTER billentyűt.**

   ![A bérlőazonosító bevitele a PowerShellben](./media/howto-mfa-nps-extension-rdg/image6.png)

1. A szkript létrehoz egy önaírt tanúsítványt, és egyéb konfigurációs módosításokat hajt végre. A kimenetnek az alábbi képen láthatóhoz hasonlónak kell lennie.

   ![Az önaírt tanúsítványt megjelenítő PowerShell kimenete](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>NpS-összetevők konfigurálása a Távoli asztali átjáró

Ebben a szakaszban konfigurálhatja a Távoli asztali átjáró engedélyezési házirendeket és egyéb RADIUS-beállításokat.

A hitelesítési folyamat megköveteli a RADIUS-üzenetek cseréjét a Távoli asztali átjáró és az NPS-kiszolgáló között, ahol az NPS-bővítmény telepítve van. Ez azt jelenti, hogy a RADIUS-ügyfélbeállításokat mind a Távoli asztali átjáró, mind pedig az NPS-kiszolgálóra, ahol az NPS-bővítmény telepítve van.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>A Távoli asztali átjáró engedélyezési házirendek konfigurálása a központi tároló használatára

Távoli asztal kapcsolat-engedélyezési házirendek (RD CAP-k) határozzák meg a távoli asztali kiszolgálóhoz való Távoli asztali átjáró követelményeit. A távoli asztali kapcsolati pontokat tárolhatja helyileg (alapértelmezett), vagy tárolhatja őket egy hálózati házirend-kiszolgálót futtató központi RD CAP tárolóban. Az RDS-Azure AD MFA konfigurálni, meg kell adnia egy központi tároló használatát.

1. A RD-átjáró nyissa meg a **Kiszolgálókezelőt.**
1. A menüben kattintson a **Tools**(Eszközök) elemre, mutasson **a Távoli asztali szolgáltatások** elemre, majd kattintson a **Távoli asztali átjárókezelő.**
1. A RD-átjárókezelő kattintson a jobb gombbal a **\[ Kiszolgálónév \] (Helyi)** elemre, majd kattintson a **Tulajdonságok elemre.**
1. A Tulajdonságok párbeszédpanelen válassza  a RD CAP lapfület.
1. A RD CAP lapon válassza az **NPS-t futtató központi kiszolgálót.** 
1. Az **Adja meg az NPS-t** futtató kiszolgáló nevét vagy IP-címét mezőbe írja be annak a kiszolgálónak az IP-címét vagy kiszolgálónevét, ahová az NPS-bővítményt telepítette.

   ![Adja meg az NPS-kiszolgáló nevét vagy IP-címét](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kattintson a **Hozzáadás** parancsra.
1. A Közös **titkos gombra kattintva** adjon meg egy közös titkos gombra, majd kattintson az **OK gombra.** Győződjön meg arról, hogy ezt a közös titkos adatokat rögzíti, és biztonságosan tárolja a rekordot.

   >[!NOTE]
   >A közös titkos adatokat a RADIUS-kiszolgálók és az ügyfelek közötti megbízhatósági kapcsolat létrehozására használják. Hozzon létre egy hosszú és összetett titkos gombra.
   >

   ![Közös titkos ok létrehozása a megbízhatóság létrehozásához](./media/howto-mfa-nps-extension-rdg/image11.png)

1. A párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>RADIUS-időtúllépési érték konfigurálása Távoli asztali átjáró NPS-n

Annak érdekében, hogy legyen idő a felhasználók hitelesítő adatainak ellenőrzésére, kétlépéses ellenőrzésre, válaszok fogadására és RADIUS-üzenetekre való válaszadásra, módosítani kell a RADIUS időtúllépési értékét.

1. A RD-átjáró nyissa meg a Kiszolgálókezelőt. A menüben kattintson a **Tools**(Eszközök) elemre, majd a **Network Policy Server (Hálózati házirend-kiszolgáló) elemre.**
1. Az **NPS (Helyi) konzolon bontsa** ki a **RADIUS-ügyfelek és -kiszolgálók** et, és válassza a **Távoli RADIUS-kiszolgáló lehetőséget.**

   ![Hálózati házirend-kiszolgáló felügyeleti konzolja a távoli RADIUS-kiszolgálóval](./media/howto-mfa-nps-extension-rdg/image12.png)

1. A részleteket tartalmazó ablaktáblán kattintson duplán a **TS GATEWAY SERVER GROUP (TS-ÁTJÁRÓKISZOLGÁLÓ-CSOPORT) elemre.**

   >[!NOTE]
   >Ez a RADIUS-kiszolgálócsoport akkor jött létre, amikor konfigurálta a központi kiszolgálót az NPS-házirendek számára. A RD-átjáró a RADIUS-üzeneteket erre a kiszolgálóra vagy kiszolgálócsoportra továbbítja, ha a csoportban több is található.
   >

1. A **TS GATEWAY SERVER GROUP Properties (TS-ÁTJÁRÓKISZOLGÁLÓCSOPORT** tulajdonságai) párbeszédpanelen válassza ki a távoli asztali ügyfélcímek tárolására konfigurált NPS-kiszolgáló IP-címét vagy nevét, majd kattintson a **Szerkesztés gombra.**

   ![Válassza ki a korábban konfigurált NPS-kiszolgáló IP-címét vagy nevét](./media/howto-mfa-nps-extension-rdg/image13.png)

1. A **RADIUS-kiszolgáló szerkesztése párbeszédpanelen** válassza a **Terheléselosztás lapot.**
1. A **Terheléselosztás** lapon, a  Válasz nélküli másodpercek száma a kérelem eldobása előtt mezőben módosítsa az alapértelmezett értéket 3-ről 30 és 60 másodperc közötti értékre.
1. A **Number of seconds between requests when server is** identified as unavailable (A kiszolgáló nem elérhetőként azonosított kiszolgálói kérelmek közötti másodpercek száma) mezőben módosítsa az alapértelmezett 30 másodperces értéket egy olyan értékre, amely egyenlő vagy nagyobb, mint az előző lépésben megadott érték.

   ![A Radius-kiszolgáló időtúllépési beállításainak szerkesztése a terheléselosztás lapon](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Kattintson kétszer az **OK** gombra a párbeszédpanelek bezárásához.

### <a name="verify-connection-request-policies"></a>Kapcsolatkérési szabályzatok ellenőrzése

Alapértelmezés szerint, ha úgy konfigurálja a RD-átjáró, hogy központi házirendtárolót használjon a kapcsolat-engedélyezési házirendek számára, a RD-átjáró úgy van konfigurálva, hogy cap-kérelmeket továbbítson az NPS-kiszolgálónak. Az NPS-kiszolgáló, Azure AD MFA a RADIUS-hozzáférési kérést dolgozza fel. A következő lépések az alapértelmezett kapcsolatkérési szabályzat ellenőrzését mutatják be.  

1. A RD-átjáró nps (helyi) konzolon bontsa ki a Házirendek bontsa ki a et, és válassza a **Kapcsolatkérési házirendek lehetőséget.**
1. Kattintson duplán a **TS GATEWAY AUTHORIZATION POLICY (TS-ÁTJÁRÓ ENGEDÉLYEZÉSI SZABÁLYZATA) elemre.**
1. A **TS GATEWAY AUTHORIZATION POLICY properties (TS-ÁTJÁRÓ ENGEDÉLYEZÉSI SZABÁLYZATának tulajdonságai)** párbeszédpanelen kattintson a **Settings (Beállítások)** fülre.
1. A **Beállítások lap** Kapcsolati kérelem továbbítása csoportjában kattintson a Hitelesítés **elemre.** A RADIUS-ügyfél úgy van konfigurálva, hogy továbbítsa a hitelesítési kéréseket.

   ![A kiszolgálócsoportot megszabadó hitelesítési beállítások konfigurálása](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kattintson a **Mégse gombra.**

>[!NOTE]
> A kapcsolatkérési szabályzatok létrehozásával kapcsolatos [](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) további információkért tekintse meg a csatlakozási kérelmekre vonatkozó szabályzatok konfigurálása dokumentációját. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NpS konfigurálása arra a kiszolgálóra, ahol az NPS-bővítmény telepítve van

Az NPS-kiszolgálónak, ahol az NPS-bővítmény telepítve van, képesnek kell lennie RADIUS-üzenetek cseréjére a hálózati házirend-kiszolgálóval a Távoli asztali átjáró. Az üzenetváltás engedélyezéséhez konfigurálnia kell az NPS-összetevőket a kiszolgálón, ahol az NPS bővítményszolgáltatás telepítve van.

### <a name="register-server-in-active-directory"></a>Kiszolgáló regisztrálása a Active Directory

Ebben a forgatókönyvben a megfelelő működéshez az NPS-kiszolgálót regisztrálni kell a Active Directory.

1. Az NPS-kiszolgálón nyissa meg a **Kiszolgálókezelőt.**
1. A Kiszolgálókezelőben kattintson az **Eszközök,** majd a **Hálózati házirend-kiszolgáló elemre.**
1. A Hálózati házirend-kiszolgáló konzolon kattintson a jobb gombbal az **NPS (Helyi)** elemre, majd kattintson a Kiszolgáló regisztrálása elemre a **Active Directory.**
1. Kattintson kétszer **az OK** gombra.

   ![Az NPS-kiszolgáló regisztrálása a Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Hagyja megnyitva a konzolt a következő eljáráshoz.

### <a name="create-and-configure-radius-client"></a>RADIUS-ügyfél létrehozása és konfigurálása

A Távoli asztali átjáró az NPS-kiszolgáló RADIUS-ügyfeleként kell konfigurálni.

1. Kattintson a jobb gombbal arra az NPS-kiszolgálóra, ahol az NPS-bővítmény telepítve van, az **NPS (helyi)** konzolon kattintson a jobb gombbal a **RADIUS-ügyfelek** elemre, majd kattintson az Új **elemre.**

   ![Új RADIUS-ügyfél létrehozása az NPS-konzolon](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Az Új **RADIUS-ügyfél** párbeszédpanelen adjon meg egy rövid nevet, például átjárót, valamint a kiszolgáló IP-címét vagy DNS Távoli asztali átjáró nevét.
1. A Közös **titkos adatokat és** a **Közös** titkos adatok megerősítése mezőkben adja meg ugyanazt a titkos adatokat, mint korábban.

   ![Rövid név és IP-cím vagy DNS-cím konfigurálása](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kattintson **az OK** gombra az Új RADIUS-ügyfél párbeszédpanel bezárásához.

### <a name="configure-network-policy"></a>Hálózati házirend konfigurálása

Ne feledje, hogy a hálózati házirend Azure AD MFA bővítménysel az NPS-kiszolgáló a kapcsolatengedélyezési házirend (CAP) kijelölt központi házirendtárolója. Ezért az érvényes kapcsolatkérések engedélyezése érdekében cap-t kell implementálnunk az NPS-kiszolgálón.  

1. Az NPS-kiszolgálón nyissa meg az NPS (helyi) konzolt, bontsa ki a **Házirendek** gombra, majd kattintson a **Hálózati házirendek elemre.**
1. Kattintson a jobb gombbal a Connections to other access servers (Kapcsolatok **más hozzáférési kiszolgálókkal)** elemre, majd kattintson **a Duplicate Policy (Házirend megkettőzése) parancsra.**

   ![Kapcsolat duplikálva más hozzáférésikiszolgáló-házirendekkel](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kattintson a jobb **gombbal a Kapcsolatok másolása más hozzáférési kiszolgálókra elemre,** majd kattintson a **Tulajdonságok parancsra.**
1. A **Kapcsolatok másolása más hozzáférési** kiszolgálókhoz párbeszédpanel Házirend neve területén adjon meg egy megfelelő nevet, például: _RDG_CAP._ Jelölje be **a Szabályzat engedélyezve jelölőnégyzetet,** majd válassza a **Hozzáférés megadása lehetőséget.** Ha szükséges, a Hálózati hozzáférési kiszolgáló típusa **mezőben** válassza **a** Távoli asztali átjáró lehetőséget, vagy hagyja meg a **Nincs megadva lehetőséget.**

   ![A szabályzat elnevezése, engedélyezés és hozzáférés megadása](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kattintson a **Megkötések** lapra, és jelölje be Az ügyfelek kapcsolódásának engedélyezése hitelesítési módszer **egyeztetése nélkül jelölőnégyzetet.**

   ![Hitelesítési módszerek módosítása az ügyfelek csatlakozásának engedélyezése érdekében](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Ha szükséges, kattintson a **Feltételek** lapra, és adjon hozzá olyan feltételeket, amelyek teljesülnek ahhoz, hogy a kapcsolat engedélyezve legyen, például egy adott Windows-csoport tagsága.

   ![Kapcsolati feltételek megadása (nem kötelező)](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kattintson az **OK** gombra. Amikor a rendszer rákérdez a megfelelő súgótémakör megtekintésére, kattintson a **Nem gombra.**
1. Győződjön meg arról, hogy az új szabályzat a lista tetején található, hogy a szabályzat engedélyezve van, és hozzáférést biztosít.

   ![A szabályzat áthelyezése a lista tetejére](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

A konfiguráció ellenőrzéséhez be kell jelentkeznie a Távoli asztali átjáró egy megfelelő RDP-ügyféllel. Győződjön meg arról, hogy olyan fiókot használ, amelyet a kapcsolat-engedélyezési házirendek engedélyeznek, és engedélyezve van a Azure AD MFA.

Ahogyan az alábbi képen is látható, használhatja a Távoli asztal **webelérési lapját.**

![Tesztelés Távoli asztal webes elérésben](./media/howto-mfa-nps-extension-rdg/image25.png)

Miután sikeresen megadta az elsődleges hitelesítéshez szükséges hitelesítő adatokat, a Távoli asztal Csatlakozás párbeszédpanelen a Távoli kapcsolat kezdeményezése állapot jelenik meg az alább látható módon. 

Ha sikeresen végzett hitelesítést a korábban a Azure AD MFA konfigurált másodlagos hitelesítési módszerrel, akkor csatlakozik az erőforráshoz. Ha azonban a másodlagos hitelesítés sikertelen, a rendszer megtagadja az erőforráshoz való hozzáférést. 

![Távoli asztali kapcsolat kapcsolat kezdeményezése](./media/howto-mfa-nps-extension-rdg/image26.png)

Az alábbi példában a Windows Phone-telefonon található Authenticator alkalmazás biztosítja a másodlagos hitelesítést.

![Példa Windows Phone-telefon Authenticator alkalmazásra az ellenőrzéssel](./media/howto-mfa-nps-extension-rdg/image27.png)

Miután sikeresen hitelesített a másodlagos hitelesítési módszerrel, a szokásos módon bejelentkezik a Távoli asztali átjáró módon. Mivel azonban másodlagos hitelesítési módszert kell használnia egy megbízható eszközön található mobilalkalmazással, a bejelentkezési folyamat biztonságosabb, mint egyébként.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>A Eseménynapló bejelentkezési események naplóinak megtekintése

A Windows Eseménynapló-naplók sikeres bejelentkezési eseményeinek megtekintéséhez a következő Windows PowerShell-parancsot használhatja a Windows terminál Services és a Windows biztonság lekérdezéséhez.

Az átjáró működési _naplóiban (Eseménynapló\Alkalmazás- és szolgáltatásnaplók\Microsoft\Windows\TerminalServices-Gateway\Operational)_ található sikeres bejelentkezési események lekérdezéséhez használja a következő PowerShell-parancsokat:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Ez a parancs olyan Windows-eseményeket jelenít meg, amelyek azt mutatják, hogy a felhasználó megfelelt az erőforrás-engedélyezési házirend követelményeinek (RD RAP), és hozzáférést kapott.

![Események megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Ez a parancs megjeleníti azokat az eseményeket, amelyek akkor megjelenikak, ha a felhasználó megfelel a kapcsolat engedélyezési házirend-követelményeinek.

![a kapcsolat engedélyezési szabályzatának megtekintése a PowerShell használatával](./media/howto-mfa-nps-extension-rdg/image29.png)

Emellett megtekintheti ezt a naplót, és szűrhet a 300-as és 200-as eseményazonosítókra. A biztonsági eseménynaplók sikeres bejelentkezési eseményeinek lekérdezéséhez használja a következő parancsot:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Ez a parancs a központi NPS-n vagy a RD-átjáró futtatható.

![Sikeres bejelentkezési események mintája](./media/howto-mfa-nps-extension-rdg/image30.png)

A Biztonsági naplót vagy a Hálózati házirend és hozzáférési szolgáltatások egyéni nézetet is megtekintheti az alábbi módon:

![Hálózati házirend- és hozzáférési szolgáltatások Eseménynapló](./media/howto-mfa-nps-extension-rdg/image31.png)

A kiszolgálón, ahol az Azure AD MFA NPS-bővítményét telepítette, Eseménynapló bővítményre vonatkozó alkalmazásnaplókat az Alkalmazás- és _szolgáltatásnaplók\Microsoft\AzureMfa_ mappában találja.

![Eseménynapló AuthZ-alkalmazásnaplók](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Hibaelhárítási útmutató

Ha a konfiguráció nem a várt módon működik, a hibaelhárítás első lépése annak ellenőrzése, hogy a felhasználó konfigurálva van-e az Azure AD MFA. A felhasználónak csatlakoznia kell a [Azure Portal.](https://portal.azure.com) Ha a rendszer másodlagos ellenőrzést kér a felhasználóktól, és sikeresen hitelesítik magukat, megszüntetheti a nem megfelelő Azure AD MFA.

Ha Azure AD MFA működik a felhasználó(k) számára, tekintse át a megfelelő eseménynaplókat. Ezek közé tartozik a biztonsági esemény, az átjáró működési Azure AD MFA és a naplók, amelyekről az előző szakaszban volt szó.

Az alábbi példa egy sikertelen bejelentkezési eseményt (6273-as eseményazonosító) megjelenítő biztonsági napló kimenetét mutatja.

![Sikertelen bejelentkezési esemény mintája](./media/howto-mfa-nps-extension-rdg/image33.png)

Az alábbiakban egy kapcsolódó esemény látható az AzureMFA-naplókból:

![Minta Azure AD MFA bejelentkezési Eseménynapló](./media/howto-mfa-nps-extension-rdg/image34.png)

A speciális hibaelhárítási beállítások végrehajtásához tekintse meg az NPS-szolgáltatás telepítéséhez szükséges NPS-adatbázisformátum naplófájlját. Ezek a naplófájlok a _%SystemRoot%\System32\Logs_ mappában vesszővel tagolt szövegfájlokként vannak létrehozva.

A naplófájlok ismertetését lásd: [NpS-adatbázisformátum naplófájlok értelmezése.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)) A naplófájlok bejegyzései nehezen értelmezhetők anélkül, hogy táblázatba vagy adatbázisba importálják őket. A naplófájlok értelmezéséhez számos IAS-elemzőt találhat az interneten.

Az alábbi képen egy ilyen letölthető [Shareware-alkalmazás kimenete látható.](https://www.deepsoftware.com/iasviewer)

![Shareware-alkalmazás mintája, IAS-elemző](./media/howto-mfa-nps-extension-rdg/image35.png)

Végül a további hibaelhárítási lehetőségekhez használhat protokollelemzőt, például a [Microsoft Message Analyzert.](/message-analyzer/microsoft-message-analyzer-operating-guide)

A Microsoft Message Analyzer alábbi képe a **CONTOSO\AliceC** felhasználónevet tartalmazó RADIUS protokollra szűrt hálózati forgalmat mutatja.

![A Microsoft Message Analyzer szűrt forgalmat mutat](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Következő lépések

[Az Azure AD Multi-Factor Authentication szolgáltatásának lekért használata](concept-mfa-licensing.md)

[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md)

[A helyszíni címtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)

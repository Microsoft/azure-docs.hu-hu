---
title: Integráció a AD Application Proxy NDES-kiszolgálón
titleSuffix: Azure Active Directory
description: Útmutatás a Azure Active Directory Application Proxy telepítéséhez a NDES-kiszolgáló elleni védelemhez.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 69193296069765ae6e94ffe97913c136a0d033d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257896"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integráció az Azure AD Application Proxy hálózati eszközök tanúsítványigénylési szolgáltatásának (NDES) kiszolgálóján

Azure Active Directory (AD) alkalmazásproxy lehetővé teszi alkalmazások közzétételét a hálózaton belül. Ezek az alkalmazások olyanok, mint a SharePoint-webhelyek, a Microsoft Outlook Web App és más webalkalmazások. Emellett biztonságos hozzáférést biztosít a hálózaton kívüli felhasználók számára az Azure-on keresztül.

Ha még nem ismeri az Azure AD Application Proxyt, és többet szeretne megtudni, tekintse [meg a helyszíni alkalmazások távoli elérését az Azure-on keresztül ad Application proxy](application-proxy.md).

Az Azure AD Application Proxy az Azure-ra épül. Nagy mennyiségű hálózati sávszélességet és kiszolgálói infrastruktúrát biztosít az elosztott szolgáltatásmegtagadási (DDOS) támadásokkal és a kiváló rendelkezésre állással szembeni jobb védelem érdekében. Emellett nincs szükség a külső tűzfal portjainak megnyitására a helyszíni hálózathoz, és nincs szükség DMZ-kiszolgálóra. Minden forgalom bejövő. A kimenő portok teljes listája: [oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directory](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> Az Azure AD Application Proxy egy olyan szolgáltatás, amely csak akkor érhető el, ha a Azure Active Directory prémium vagy alapszintű kiadásait használja. További információ: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/). 
> Ha nagyvállalati mobilitási csomag (EMS) licenccel rendelkezik, jogosult a megoldás használatára.
> Az Azure AD Application Proxy-összekötő csak Windows Server 2012 R2 vagy újabb rendszerre telepíthető. Ez a NDES-kiszolgáló követelménye is.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Az összekötő telepítése és regisztrálása a NDES-kiszolgálón

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az alkalmazásproxy-t használó címtár alkalmazás-rendszergazdájaként. Ha például a bérlő tartománya contoso.com, akkor a rendszergazdának admin@contoso.com vagy más rendszergazdai aliasnak kell lennie az adott tartományban.
1. Válassza ki a felhasználónevét a jobb felső sarokban. Ellenőrizze, hogy be van-e jelentkezve az alkalmazásproxy-t használó könyvtárba. Ha módosítania kell a címtárakat, válassza a **váltás könyvtárat** , és válasszon egy, az alkalmazásproxy-t használó könyvtárat.
1. A bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.
1. A **kezelés** területen válassza a **alkalmazásproxy** elemet.
1. Válassza az **összekötő szolgáltatás letöltése** lehetőséget.

    ![Az összekötő szolgáltatás letöltése a szolgáltatási feltételek megtekintéséhez](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Olvassa el a szolgáltatási feltételeket. Ha elkészült, válassza a **feltételek elfogadása & Letöltés** lehetőséget.
1. Másolja az Azure AD Application Proxy Connector telepítési fájlt a NDES-kiszolgálóra. 
   > Az összekötőt a vállalati hálózaton belüli bármely kiszolgálóra is telepítheti a NDES elérésével. Nem kell telepítenie a NDES-kiszolgálóra.
1. Futtassa a telepítőfájlt, például *AADApplicationProxyConnectorInstaller.exe*. A szoftverlicenc-szerződés elfogadása.
1. A telepítés során a rendszer felszólítja, hogy regisztrálja az összekötőt az alkalmazás-proxyval az Azure AD-címtárban.
   * Adja meg az Azure AD-címtárban a globális vagy az alkalmazás-rendszergazda hitelesítő adatait. Az Azure AD globális vagy alkalmazás-rendszergazdai hitelesítő adatai eltérhetnek a portál Azure-beli hitelesítő adataitól.

        > [!NOTE]
        > Az összekötő regisztrálásához használt globális vagy alkalmazás-rendszergazdai fióknak ugyanahhoz a címtárhoz kell tartoznia, ahol engedélyezte az alkalmazásproxy szolgáltatást.
        >
        > Ha például az Azure AD-tartomány *contoso.com*, a globális/alkalmazás-rendszergazdának `admin@contoso.com` vagy egy másik érvényes aliasnak kell lennie az adott tartományban.

   * Ha az Internet Explorer fokozott biztonsági beállításai be vannak kapcsolva azon a kiszolgálón, amelyen az összekötőt telepíti, előfordulhat, hogy a regisztrációs képernyő blokkolva van. A hozzáférés engedélyezéséhez kövesse a hibaüzenet utasításait, vagy kapcsolja ki az Internet Explorer fokozott biztonságát a telepítési folyamat során.
   * Ha az összekötő regisztrálása meghiúsul, tekintse meg az [alkalmazásproxy hibaelhárítása](application-proxy-troubleshoot.md)című témakört.
1. A beállítás végén megjelenik egy Megjegyzés a kimenő proxyval rendelkező környezetekhez. Ha úgy szeretné konfigurálni az Azure AD Application Proxy-összekötőt, hogy a kimenő proxyn keresztül működjön, futtassa a megadott parancsfájlt, például: `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Az Azure Portal alkalmazásproxy lapján az új összekötő *aktív* állapotú, az alábbi példában látható módon jelenik meg:

    ![Az új Azure AD Application Proxy-összekötő, amely aktívként jelenik meg a Azure Portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Ha magas rendelkezésre állást szeretne biztosítani az Azure AD Application Proxyon keresztül hitelesítő alkalmazások számára, több virtuális gépre is telepíthet összekötőket. Ismételje meg az előző szakaszban felsorolt lépéseket, hogy az összekötőt az Azure AD DS felügyelt tartományhoz csatlakozó más kiszolgálókra telepítse.

1. A sikeres telepítés után térjen vissza a Azure Portal.

1. Válassza a **vállalati alkalmazások** lehetőséget.

   ![Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Válassza az **+ új alkalmazás** lehetőséget, majd válassza **a helyszíni alkalmazás** lehetőséget. 

1. A **saját helyszíni alkalmazás hozzáadása** területen konfigurálja a következő mezőket:

   * **Név**: adja meg az alkalmazás nevét.
   * **Belső URL-cím**: adja meg annak a NDES-kiszolgálónak a belső URL-címét/teljes tartománynevét, amelyre az összekötőt telepítette.
   * **Előzetes hitelesítés**: válassza az **átadó** lehetőséget. Az előhitelesítés bármilyen formáját nem lehet használni. A tanúsítványigénylésekhez használt protokoll (SCEP) nem biztosít ilyen beállítást.
   * Másolja a megadott **külső URL-címet** a vágólapra.

1. Az alkalmazás mentéséhez válassza a **+ Hozzáadás** lehetőséget.

1. Ellenőrizze, hogy az Azure AD-alkalmazásproxy segítségével hozzáférhet-e a NDES-kiszolgálóhoz a 15. lépésben a böngészőben másolt hivatkozás beillesztésével. Ekkor meg kell jelennie az alapértelmezett IIS-kezdőlapnak.

1. Végső tesztként adja hozzá a *mscep.dll* elérési utat az előző lépésben beillesztett meglévő URL-címhez:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Egy **403-es HTTP-hiba – tiltott** válasz jelenik meg.

1. Módosítsa a megadott NDES URL-címet (Microsoft Intune használatával) az eszközökre. Ez a módosítás lehet a Microsoft Endpoint Configuration Manager vagy a Microsoft Endpoint Manager felügyeleti központban.

   * Configuration Manager esetében lépjen a tanúsítvány regisztrációs pontjára, és módosítsa az URL-címet. Ezt az URL-címet kell meghívnia az eszközökre, és be kell mutatnia a feladatát.
   * Az Intune önálló verziójában szerkessze vagy hozzon létre egy új SCEP-házirendet, és adja hozzá az új URL-címet.

## <a name="next-steps"></a>Következő lépések

A NDES-mel integrált Azure-AD Application Proxy alkalmazások közzétételét teszik elérhetővé a felhasználók számára. További információ: [alkalmazások közzététele az Azure ad Application proxy használatával](./application-proxy-add-on-premises-application.md).

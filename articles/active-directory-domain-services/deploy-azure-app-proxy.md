---
title: Azure-AD Application Proxy üzembe helyezése Azure AD Domain Serviceshoz | Microsoft Docs
description: Ismerje meg, hogyan biztosíthat biztonságos hozzáférést a távoli feldolgozók számára a belső alkalmazásokhoz Azure Active Directory Application Proxy Azure Active Directory Domain Services felügyelt tartományban való üzembe helyezésével és konfigurálásával
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8305b2190cf3b157973f5844c3237ffe73adba66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619916"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Az Azure AD Application Proxy üzembe helyezése egy Azure Active Directory Domain Services felügyelt tartomány belső alkalmazásaihoz való biztonságos hozzáféréshez

A Azure AD Domain Services (Azure AD DS) segítségével a helyszínen futó örökölt alkalmazásokat az Azure-ba helyezheti át. A Azure Active Directory (AD) alkalmazásproxy ezt követően segíti a távoli munkatársak támogatását azáltal, hogy biztonságosan közzéteszi ezeket az Azure AD DS felügyelt tartomány részét képező belső alkalmazásokat, hogy azok elérhetők legyenek az interneten keresztül.

Ha még nem ismeri az Azure AD Application Proxy, és többet szeretne megtudni, tekintse meg a [biztonságos távoli hozzáférés biztosítása belső alkalmazásokhoz](../active-directory/manage-apps/application-proxy.md)című témakört.

Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure AD Application Proxy-összekötőt, hogy biztonságos hozzáférést biztosítson a felügyelt tartományba tartozó alkalmazásokhoz.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
    * Az Azure-AD Application Proxy használatához **prémium szintű Azure ad licencre** van szükség.
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Tartományhoz csatlakoztatott Windows rendszerű virtuális gép létrehozása

Ha a környezetben futó alkalmazásokba szeretné irányítani a forgalmat, telepítse az Azure AD Application Proxy Connector összetevőt. Ezt az Azure AD Application Proxy-összekötőt a felügyelt tartományhoz csatlakoztatott Windows Server rendszerű virtuális gépre (VM) kell telepíteni. Egyes alkalmazások esetében több kiszolgálót is telepíthet, amelyeken az összekötő telepítve van. Ez a központi telepítési lehetőség nagyobb rendelkezésre állást biztosít, és segíti a nehezebb hitelesítési terhelések kezelését.

Az Azure AD Application Proxy-összekötőt futtató virtuális gépnek azonos vagy egy felügyelt tartományhoz tartozó, virtuális hálózaton kell lennie. Az alkalmazásproxy használatával közzétett alkalmazásokat futtató virtuális gépeket ugyanezen az Azure-beli virtuális hálózaton is telepíteni kell.

Ha létre szeretne hozni egy virtuális gépet az Azure AD Application Proxy-összekötőhöz, hajtsa végre a következő lépéseket:

1. [Hozzon létre egy egyéni szervezeti egységet](create-ou.md). Az egyéni szervezeti egység felügyelt tartományba tartozó felhasználók számára történő felügyeletéhez engedélyeket delegálhat. Az Azure AD Application Proxy rendszerű virtuális gépek és az alkalmazások futtatásához az egyéni szervezeti egységnek kell tartoznia, nem az alapértelmezett *HRE DC számítógépek* szervezeti egységének.
1. [Tartomány – csatlakozzon a virtuális gépekhez][create-join-windows-vm], amelyek az Azure ad Application proxy-összekötőt futtatják, valamint az alkalmazásokat futtatókat a felügyelt tartományhoz. Hozza létre ezeket a számítógépfiókokat az egyéni szervezeti egységben az előző lépésből.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Az Azure AD Application Proxy-összekötő letöltése

Az Azure AD Application Proxy-összekötő letöltéséhez hajtsa végre a következő lépéseket. A letöltött telepítőfájlt a következő szakaszban másolja az alkalmazás-proxy virtuális gépre.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan felhasználói fiókkal, amely *vállalati rendszergazdai* engedélyekkel rendelkezik az Azure ad-ben.
1. Keresse meg és válassza ki **Azure Active Directory** a portál tetején, majd válassza a **vállalati alkalmazások** lehetőséget.
1. Válassza ki az **alkalmazásproxy** elemet a bal oldali menüben. Az első összekötő létrehozásához és az alkalmazásproxy engedélyezéséhez válassza a hivatkozást az **összekötő letöltéséhez**.
1. A letöltési oldalon fogadja el a licencfeltételeket és az adatvédelmi szerződést, majd válassza a **feltételek elfogadása & Letöltés** lehetőséget.

    ![A Azure AD alkalmazás proxy-összekötő letöltése](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Az Azure AD Application Proxy-összekötő telepítése és regisztrálása

Ha a virtuális gép készen áll az Azure AD Application Proxy-összekötőként való használatra, most másolja és futtassa a Azure Portalból letöltött telepítőfájlt.

1. Másolja az Azure AD Application Proxy Connector telepítési fájlját a virtuális gépre.
1. Futtassa a telepítőfájlt, például *AADApplicationProxyConnectorInstaller.exe*. A szoftverlicenc-szerződés elfogadása.
1. A telepítés során a rendszer felszólítja, hogy regisztrálja az összekötőt az alkalmazás-proxyval az Azure AD-címtárban.
   * Adja meg az Azure AD-címtár globális rendszergazdájának hitelesítő adatait. Az Azure AD globális rendszergazdai hitelesítő adatai eltérőek lehetnek az Azure-beli hitelesítő adatokkal a portálon

        > [!NOTE]
        > Az összekötő regisztrálásához használt globális rendszergazdai fióknak ugyanahhoz a címtárhoz kell tartoznia, ahol engedélyezte az alkalmazásproxy szolgáltatást.
        >
        > Ha például az Azure AD-tartomány *contoso.com*, a globális rendszergazdának `admin@contoso.com` vagy egy másik érvényes aliasnak kell lennie az adott tartományban.

   * Ha az Internet Explorer fokozott biztonsági beállításai be vannak kapcsolva ahhoz a virtuális géphez, amelyen az összekötőt telepíti, lehetséges, hogy a regisztrációs képernyő blokkolva van. A hozzáférés engedélyezéséhez kövesse a hibaüzenet utasításait, vagy kapcsolja ki az Internet Explorer fokozott biztonságát a telepítési folyamat során.
   * Ha az összekötő regisztrálása meghiúsul, tekintse meg az [alkalmazásproxy hibaelhárítása](../active-directory/manage-apps/application-proxy-troubleshoot.md)című témakört.
1. A beállítás végén megjelenik egy Megjegyzés a kimenő proxyval rendelkező környezetekhez. Ha úgy szeretné konfigurálni az Azure AD Application Proxy-összekötőt, hogy a kimenő proxyn keresztül működjön, futtassa a megadott parancsfájlt, például: `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Az Azure Portal alkalmazásproxy lapján az új összekötő *aktív* állapotú, az alábbi példában látható módon jelenik meg:

    ![Az új Azure AD Application Proxy-összekötő, amely aktívként jelenik meg a Azure Portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Ha magas rendelkezésre állást szeretne biztosítani az Azure AD Application Proxyon keresztül hitelesítő alkalmazások számára, több virtuális gépre is telepíthet összekötőket. Ismételje meg az előző szakaszban felsorolt lépéseket, és telepítse az összekötőt a felügyelt tartományhoz csatlakozó többi kiszolgálóra.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Erőforrás-alapú Kerberos által korlátozott delegálás engedélyezése

Ha integrált Windows-hitelesítést (IWA) használó alkalmazásokhoz szeretne egyszeri bejelentkezést használni, engedélyezze az Azure AD Application Proxy-összekötők számára a felhasználók megszemélyesítését és a jogkivonatok küldését és fogadását a nevükben. Ezen engedélyek megadásához konfigurálja a Kerberos által korlátozott delegálást (KCD) ahhoz, hogy az összekötő hozzáférhessen a felügyelt tartomány erőforrásaihoz. Mivel nem rendelkezik tartományi rendszergazdai jogosultságokkal a felügyelt tartományokban, a hagyományos fiók szintű KCD nem konfigurálható felügyelt tartományon. Ehelyett használjon erőforrás-alapú KCD.

További információ: [a Kerberos által korlátozott delegálás (KCD) konfigurálása Azure Active Directory Domain Servicesban](deploy-kcd.md).

> [!NOTE]
> A következő PowerShell-parancsmagok futtatásához be kell jelentkeznie egy olyan felhasználói fiókba, amely tagja az *Azure ad DC-rendszergazdák* csoportjának.
>
> Az alkalmazásproxy-összekötő virtuális gépe és az alkalmazás virtuális gépei számítógépfiókja olyan egyéni szervezeti egységben kell, hogy rendelkezzen az erőforrás-alapú KCD konfigurálásához szükséges engedélyekkel. A beépített *HRE DC Computers* tárolóban nem lehet beállítani erőforrás-alapú KCD a számítógépfiók számára.

A [Get-ADComputer][Get-ADComputer] használatával beolvashatja azon számítógép beállításait, amelyre az Azure ad Application proxy Connector telepítve van. Futtassa a következő parancsmagokat a tartományhoz csatlakoztatott felügyeleti virtuális gépről, és jelentkezzen be felhasználói fiókként, amely az *Azure ad DC-rendszergazdák* csoport tagja.

Az alábbi példa a *appproxy.aaddscontoso.com* nevű számítógépfiók adatait olvassa be. Adja meg az előző lépésekben konfigurált Azure AD Application Proxy virtuális géphez tartozó saját számítógép nevét.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Az Azure AD Application Proxyt futtató összes alkalmazás-kiszolgáló esetében használja a [set-ADComputer PowerShell-][Set-ADComputer] parancsmagot az erőforrás-alapú KCD konfigurálásához. Az alábbi példában az Azure AD Application Proxy-összekötő engedélyt kap a *appserver.aaddscontoso.com* -számítógép használatára:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Ha több Azure AD Application Proxy-összekötőt helyez üzembe, az egyes összekötő-példányokhoz erőforrás-alapú KCD kell konfigurálnia.

## <a name="next-steps"></a>Következő lépések

Az Azure AD DS integrált Azure-AD Application Proxy az alkalmazások közzétételét teszik elérhetővé a felhasználók számára. További információ: [alkalmazások közzététele az Azure ad Application proxy használatával](../active-directory/manage-apps/application-proxy-add-on-premises-application.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
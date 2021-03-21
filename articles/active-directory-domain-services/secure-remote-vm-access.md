---
title: A virtuális gépek biztonságos elérésének biztosítása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan biztonságossá teheti a virtuális gépek távoli elérését a hálózati házirend-kiszolgáló (NPS) és az Azure AD Multi-Factor Authentication használatával, egy Azure Active Directory Domain Services felügyelt tartományban lévő Távoli asztali szolgáltatások-telepítéssel.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618926"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Biztonságos távoli hozzáférés a Azure Active Directory Domain Services virtuális gépekhez

A Azure Active Directory Domain Services (Azure AD DS) által felügyelt tartományban futó virtuális gépek (VM-EK) távelérésének biztonságossá tételéhez a Távoli asztali szolgáltatások (RDS) és a hálózati házirend-kiszolgáló (NPS) használható. Az Azure AD DS hitelesíti a felhasználókat az RDS-környezettel való hozzáférés kérése közben. A fokozott biztonság érdekében az Azure AD-Multi-Factor Authentication integrálásával további hitelesítési kéréseket adhat meg a bejelentkezési események során. Az Azure AD Multi-Factor Authentication egy bővítményt használ a hálózati házirend-kiszolgáló számára a szolgáltatás biztosításához.

> [!IMPORTANT]
> Az Azure AD DS felügyelt tartományokban lévő virtuális gépekhez való biztonságos kapcsolódás ajánlott módja az Azure Bastion, amely egy teljes körű platformon felügyelt, a virtuális hálózaton belül üzembe helyezhető szolgáltatás. A megerősített gazdagépek biztonságos és zökkenőmentes RDP protokoll (RDP) kapcsolatot biztosítanak a virtuális gépekhez közvetlenül a Azure Portal SSL-en keresztül. Ha egy megerősített gazdagépen keresztül csatlakozik, a virtuális gépeknek nincs szüksége nyilvános IP-címekre, és nem kell hálózati biztonsági csoportokat használnia az RDP eléréséhez a 3389-es TCP-porton.
>
> Javasoljuk, hogy minden olyan régióban használja az Azure Bastion-t, ahol a támogatott. Az Azure-ban elérhető rendelkezésre állást nem igénylő régiókban kövesse az ebben a cikkben ismertetett lépéseket, amíg az Azure Bastion nem érhető el. Ügyeljen arra, hogy nyilvános IP-címeket rendeljen hozzá az Azure AD DShoz csatlakoztatott virtuális gépekhez, ahol minden bejövő RDP-forgalom engedélyezett.
>
> További információ: [Mi az az Azure Bastion?][bastion-overview].

Ez a cikk bemutatja, hogyan konfigurálhatja az RDS-t az Azure AD DSban, és igény szerint használhatja az Azure AD Multi-Factor Authentication NPS bővítményt.

![A Távoli asztali szolgáltatások (RDS) áttekintése](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][create-azure-ad-ds-instance].
* A Azure Active Directory Domain Services virtuális hálózatban létrehozott számítási *feladatok* alhálózata.
    * Ha szükséges, [konfigurálja a virtuális hálózatkezelést egy Azure Active Directory Domain Services felügyelt tartományhoz][configure-azureadds-vnet].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>A Távoli asztal környezet üzembe helyezése és konfigurálása

Első lépésként hozzon létre legalább két olyan Azure-beli virtuális gépet, amely a Windows Server 2016 vagy a Windows Server 2019 rendszert futtatja. A Távoli asztal-(RD-) környezet redundancia és magas rendelkezésre állása érdekében később további gazdagépeket adhat hozzá és tölthet fel.

A javasolt RDS-telepítés a következő két virtuális gépet tartalmazza:

* *RDGVM01* – futtatja a RD-kapcsolatszervező-kiszolgálót, a távoli asztali webes elérési kiszolgálót és a RD-átjáró kiszolgálót.
* *RDSHVM01* – futtatja a távoli asztali munkamenetgazda-kiszolgálót.

Győződjön meg arról, hogy a virtuális gépek üzembe helyezése az Azure AD DS Virtual Network *munkaterhelési* alhálózatán történik, majd csatlakoztassa a virtuális gépeket a felügyelt tartományhoz. További információ: Windows Server rendszerű [virtuális gép létrehozása és csatlakoztatása felügyelt tartományhoz][tutorial-create-join-vm].

A távoli asztali környezet üzembe helyezése számos lépést tartalmaz. A meglévő távoli asztali telepítési útmutató a felügyelt tartományokban való használathoz szükséges módosítások nélkül is használható:

1. Jelentkezzen be a távoli asztali környezethez létrehozott virtuális gépekre egy olyan fiókkal, amely az *Azure ad DC-rendszergazdák* csoport tagja, például *contosoadmin*.
1. Az RDS létrehozásához és konfigurálásához használja a meglévő [Távoli asztal környezet telepítési útmutatóját][deploy-remote-desktop]. Szükség szerint ossza el a távoli asztali kiszolgáló összetevőit az Azure-beli virtuális gépeken.
    * Az Azure AD DS-ra jellemző – ha a távoli asztali licencelést konfigurálja, állítsa azt **eszközönkénti** módra **, ne pedig felhasználónként a** telepítési útmutatóban feljegyzett értékre.
1. Ha webböngészővel szeretne hozzáférést biztosítani, [állítsa be az távoli asztal webes ügyfelet a felhasználók][rd-web-client]számára.

A felügyelt tartományba telepített távoli asztali környezettel felügyelheti és használhatja a szolgáltatást, mint a helyszíni AD DS tartománnyal.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>A hálózati házirend-kiszolgáló és az Azure AD MFA NPS-bővítmény üzembe helyezése és konfigurálása

Ha szeretné javítani a felhasználói bejelentkezési élmény biztonságát, lehetősége van a távoli asztali környezet integrálására az Azure AD Multi-Factor Authentication használatával. Ezzel a konfigurációval a felhasználók a bejelentkezés során további kérést kapnak az identitásuk megerősítéséhez.

Ennek a képességnek a biztosításához további hálózati házirend-kiszolgáló (NPS) van telepítve a környezetben az Azure AD Multi-Factor Authentication NPS bővítményével együtt. Ez a bővítmény integrálva van az Azure AD-vel, és visszaküldi a multi-Factor Authentication-kérések állapotát.

[Az Azure ad multi-Factor Authentication használatához regisztrálni kell a][user-mfa-registration]felhasználókat, ami további Azure ad-licenceket igényelhet.

Az Azure AD Multi-Factor Authentication Azure AD DS Távoli asztal-környezetbe való integrálásához hozzon létre egy NPS-kiszolgálót, és telepítse a bővítményt:

1. Hozzon létre egy további, a Windows Server 2016-es vagy 2019-es virtuális GÉPET, például a *NPSVM01*-t, amely az Azure AD DS Virtual Network *munkaterhelési* alhálózatához csatlakozik. Csatlakoztassa a virtuális gépet a felügyelt tartományhoz.
1. Jelentkezzen be a hálózati házirend-kiszolgáló virtuális gépre az *Azure ad DC-rendszergazdák* csoport tagjaként, például *contosoadmin*.
1. A **Kiszolgálókezelőben** válassza a **szerepkörök és szolgáltatások hozzáadása** lehetőséget, majd telepítse a *hálózati házirend-és elérési szolgáltatások* szerepkört.
1. Az [Azure ad MFA NPS-bővítmény telepítéséhez és konfigurálásához][nps-extension]használja a meglévő útmutató cikket.

Ha a hálózati házirend-kiszolgáló és az Azure AD Multi-Factor Authentication NPS bővítmény telepítve van, végezze el a következő szakaszt a konfigurálásához a távoli asztali környezettel való használatra.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Távoli asztali átjáró és az Azure AD integrálása Multi-Factor Authentication

Az Azure AD Multi-Factor Authentication NPS bővítmény integrálásához a meglévő útmutató cikk használatával [integrálhatja távoli asztali átjáró-infrastruktúráját a hálózati házirend-kiszolgáló (NPS) bővítménnyel és az Azure ad][azure-mfa-nps-integration]-vel.

A felügyelt tartományhoz való integráláshoz a következő további konfigurációs beállítások szükségesek:

1. Ne [regisztrálja az NPS-kiszolgálót Active Directory-ben][register-nps-ad]. Ez a lépés nem sikerül felügyelt tartományban.
1. A [hálózati házirend konfigurálásához a 4. lépésben][create-nps-policy]jelölje be a **felhasználói fiók betárcsázási tulajdonságainak figyelmen kívül hagyása** jelölőnégyzetet is.
1. Ha a Windows Server 2019-et használja a hálózati házirend-kiszolgáló és az Azure AD Multi-Factor Authentication NPS bővítmény számára, futtassa a következő parancsot a biztonságos csatorna frissítéséhez, hogy a hálózati házirend-kiszolgáló helyesen kommunikáljon:

    ```powershell
    sc sidtype IAS unrestricted
    ```

A rendszer a bejelentkezéskor további hitelesítési tényezőt kér a felhasználóktól, például szöveges üzenetben vagy Rákérdezés a Microsoft Authenticator alkalmazásban.

## <a name="next-steps"></a>Következő lépések

További információ az üzembe helyezés rugalmasságának javításáról: [Távoli asztali szolgáltatások magas rendelkezésre állású][rds-high-availability].

A felhasználói bejelentkezés biztonságossá tételével kapcsolatos további információkért tekintse [meg a hogyan működik: Azure AD multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability
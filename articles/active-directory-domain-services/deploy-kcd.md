---
title: Kerberos által korlátozott delegálás a Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti az erőforrás-alapú Kerberos által korlátozott delegálást (KCD) egy Azure Active Directory Domain Services felügyelt tartományban.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 138b90a33ff1dbc4b014f17fa0098112e1da66e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619776"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Kerberos által korlátozott delegálás (KCD) konfigurálása Azure Active Directory Domain Services

Az alkalmazások futtatása során szükség lehet arra, hogy az alkalmazások egy másik felhasználó kontextusában férhessenek hozzá az erőforrásokhoz. A Active Directory Domain Services (AD DS) támogatja a *Kerberos-delegálás* nevű mechanizmust, amely engedélyezi ezt a használati esetet. A Kerberos által *korlátozott* DELEGÁLÁS (KCD) ezt a mechanizmust építi ki a felhasználó környezetében elérhető konkrét erőforrások definiálásához.

A Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományok biztonságosabban zárhatók le, mint a hagyományos helyi AD DS környezetek, ezért használjon biztonságosabb *erőforrás-alapú* KCD.

Ez a cikk bemutatja, hogyan konfigurálhatja az erőforrás-alapú Kerberos által korlátozott delegálást egy Azure AD DS felügyelt tartományban.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Windows Server rendszerű virtuális gép létrehozásához és egy felügyelt tartományhoz való csatlakoztatásához][create-join-windows-vm] , majd [telepítse a AD DS felügyeleti eszközöket][tutorial-create-management-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos által korlátozott delegálás – áttekintés

A Kerberos-delegálás lehetővé teszi, hogy az egyik fiók megszemélyesítse egy másik fiókot az erőforrások eléréséhez. Egy háttérbeli webes összetevőhöz hozzáférő webalkalmazás például megszemélyesítheti magát egy másik felhasználói fiókkal, amikor a háttér-kapcsolatot végzi. A Kerberos-delegálás nem biztonságos, mert nem korlátozza, hogy a megszemélyesítő fiók milyen erőforrásokat tud elérni.

A Kerberos által *korlátozott* DELEGÁLÁS (KCD) korlátozza azokat a szolgáltatásokat vagy erőforrásokat, amelyeket a megadott kiszolgáló vagy alkalmazás csatlakozhat egy másik identitás megszemélyesítése során. A hagyományos KCD tartományi rendszergazdai jogosultságok szükségesek a szolgáltatás tartományi fiókjának konfigurálásához, és a fiók egyetlen tartományon való futtatására van korlátozva.

A hagyományos KCD néhány problémával is rendelkezik. A korábbi operációs rendszerekben például a szolgáltatás rendszergazdája nem tudta megismerni, hogy mely előtér-szolgáltatások lettek delegálva az általuk birtokolt erőforrás-szolgáltatásokhoz. Bármely, az erőforrás-szolgáltatásnak delegált előtér-szolgáltatás lehetséges támadási pont volt. Ha egy olyan kiszolgáló, amely az erőforrás-szolgáltatásokra való delegálásra konfigurált előtér-szolgáltatást futtatott, akkor az erőforrás-szolgáltatások biztonsága is sérülhet.

Felügyelt tartományokban nem rendelkezik tartományi rendszergazdai jogosultságokkal. Ennek eredményeképpen a hagyományos, fiókon alapuló KCD nem konfigurálhatók felügyelt tartományokban. Ehelyett erőforrás-alapú KCD is használható, ami még biztonságosabb.

### <a name="resource-based-kcd"></a>Erőforrás-alapú KCD

A Windows Server 2012-es és újabb verziói lehetővé teszi, hogy a szolgáltatás-rendszergazdák konfigurálják a szolgáltatás korlátozott delegálását. Ez a modell erőforrás-alapú KCD néven ismert. Ezzel a módszerrel a háttér-szolgáltatás rendszergazdája engedélyezheti vagy megtagadhatja a KCD használatával adott előtér-szolgáltatásokat.

Az erőforrás-alapú KCD a PowerShell használatával van konfigurálva. A [set-ADComputer][Set-ADComputer] vagy a [set-ADUser][Set-ADUser] parancsmagot használja attól függően, hogy a megszemélyesítési fiók egy számítógépfiók vagy egy felhasználói fiók vagy szolgáltatásfiók.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Erőforrás-alapú KCD konfigurálása számítógépfiók számára

Ebben az esetben tegyük fel, hogy van egy webalkalmazása, amely a *contoso-WebApp.aaddscontoso.com* nevű számítógépen fut.

A webalkalmazásnak hozzá kell férnie egy webes API-hoz, amely a *contoso-API.aaddscontoso.com* nevű számítógépen fut a tartományi felhasználók környezetében.

A forgatókönyv konfigurálásához hajtsa végre a következő lépéseket:

1. [Hozzon létre egy egyéni szervezeti egységet](create-ou.md). Az egyéni szervezeti egység felügyelt tartományba tartozó felhasználók számára történő felügyeletéhez engedélyeket delegálhat.
1. [Tartomány – csatlakozzon a virtuális gépekhez][create-join-windows-vm], amelyek a webalkalmazást futtatják, valamint a webes API-t futtató kiszolgálókat a felügyelt tartományhoz. Hozza létre ezeket a számítógépfiókokat az egyéni szervezeti egységben az előző lépésből.

    > [!NOTE]
    > A webalkalmazás és a webes API számítógépfiókja olyan egyéni szervezeti egységben kell, hogy rendelkezzen az erőforrás-alapú KCD konfigurálásához szükséges engedélyekkel. A beépített *HRE DC Computers* tárolóban nem lehet beállítani erőforrás-alapú KCD a számítógépfiók számára.

1. Végül konfigurálja az erőforrás-alapú KCD a [set-ADComputer PowerShell-][Set-ADComputer] parancsmag használatával.

    Futtassa a következő parancsmagokat a tartományhoz csatlakoztatott felügyeleti virtuális gépről, és jelentkezzen be felhasználói fiókként, amely az *Azure ad DC-rendszergazdák* csoport tagja. Szükség szerint adja meg saját számítógépe nevét:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Erőforrás-alapú KCD konfigurálása felhasználói fiókhoz

Ebben az esetben tegyük fel, hogy van egy webalkalmazása, amely egy *appsvc* nevű szolgáltatásfiókot futtat. A webalkalmazásnak hozzá kell férnie egy webes API-hoz, amely a *backendsvc* nevű szolgáltatási fiókként fut a tartományi felhasználók környezetében. A forgatókönyv konfigurálásához hajtsa végre a következő lépéseket:

1. [Hozzon létre egy egyéni szervezeti egységet](create-ou.md). Az egyéni szervezeti egység felügyelt tartományba tartozó felhasználók számára történő felügyeletéhez engedélyeket delegálhat.
1. [Tartomány – csatlakoztassa a][create-join-windows-vm] háttérbeli webes API-t/erőforrást futtató virtuális gépeket a felügyelt tartományhoz. Hozza létre a számítógép-fiókját az egyéni szervezeti egységen belül.
1. Hozza létre a webalkalmazás egyéni szervezeti egységen belüli futtatásához használt szolgáltatásfiókot (például *appsvc*).

    > [!NOTE]
    > A webes API virtuális gép számítógépfiókja, valamint a webalkalmazáshoz tartozó szolgáltatásfiók esetében egy olyan egyéni szervezeti egységnek kell lennie, amelyben az erőforrás-alapú KCD konfigurálásához szükséges engedélyekkel rendelkezik. Az erőforrás-alapú KCD nem konfigurálható fiókokhoz a beépített *HRE DC számítógépeken* vagy a *HRE DC felhasználói* tárolókban. Ez azt is jelenti, hogy az Azure AD-ből szinkronizált felhasználói fiókok nem használhatók erőforrás-alapú KCD beállításához. Létre kell hoznia és használnia kell a kifejezetten az Azure AD DS-ben létrehozott szolgáltatásfiókot.

1. Végül konfigurálja az erőforrás-alapú KCD a [set-ADUser PowerShell-][Set-ADUser] parancsmag használatával.

    Futtassa a következő parancsmagokat a tartományhoz csatlakoztatott felügyeleti virtuális gépről, és jelentkezzen be felhasználói fiókként, amely az *Azure ad DC-rendszergazdák* csoport tagja. Igény szerint adja meg a saját szolgáltatásának nevét:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan működik a delegálás a Active Directory Domain Servicesban, tekintse meg a [Kerberos által korlátozott delegálás – áttekintés][kcd-technet]című témakört.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)
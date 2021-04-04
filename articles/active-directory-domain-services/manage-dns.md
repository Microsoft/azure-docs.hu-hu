---
title: DNS kezelése Azure AD Domain Serviceshoz | Microsoft Docs
description: Ismerje meg, hogyan telepítheti a DNS-kiszolgálói eszközöket a DNS kezelésére, és hogyan hozhat létre feltételes továbbítókat egy Azure Active Directory Domain Services felügyelt tartományhoz.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: afa6920a36a5a7218571239b36815004d8f2d450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619351"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>DNS felügyelete és feltételes továbbítók létrehozása Azure Active Directory Domain Services felügyelt tartományban

A Azure Active Directory Domain Services (Azure AD DS) egyik kulcsfontosságú összetevője a DNS (tartománynév-feloldás). Az Azure AD DS tartalmaz egy olyan DNS-kiszolgálót, amely névfeloldást biztosít a felügyelt tartomány számára. Ez a DNS-kiszolgáló beépített DNS-rekordokat és frissítéseket tartalmaz a szolgáltatás futtatását lehetővé tevő kulcsfontosságú összetevőkhöz.

A saját alkalmazások és szolgáltatások futtatásakor előfordulhat, hogy DNS-rekordokat kell létrehoznia a tartományhoz nem csatlakoztatott gépekhez, konfigurálnia kell a terheléselosztó virtuális IP-címeit, vagy külső DNS-továbbítókat kell beállítania. Az *HRE DC-rendszergazdák* csoportba tartozó felhasználók DNS-rendszergazdai jogosultságokat kapnak az Azure AD DS felügyelt tartományhoz, és létrehozhatnak és szerkeszthetnek egyéni DNS-rekordokat.

Hibrid környezetben a más DNS-névterekben (például helyszíni AD DS környezetben) konfigurált DNS-zónák és-rekordok nem szinkronizálhatók a felügyelt tartományba. Más DNS-névtérben lévő nevesített erőforrások feloldásához hozzon létre és használjon olyan feltételes továbbítókat, amelyek a környezetében meglévő DNS-kiszolgálókra mutatnak.

Ez a cikk bemutatja, hogyan telepítheti a DNS-kiszolgálói eszközöket, majd a DNS-konzollal kezelheti a rekordokat, és hogyan hozhat létre feltételes továbbítókat az Azure AD DSban.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services felügyelt tartomány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS Virtual Network kapcsolata, ahol a többi DNS-névteret üzemelteti.
    * Ez a kapcsolat [Azure-ExpressRoute][expressroute] vagy [Azure VPN Gateway][vpn-gateway] -kapcsolattal is biztosítható.
* A felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Windows Server rendszerű virtuális gép létrehozásához és egy felügyelt tartományhoz való csatlakoztatásához][create-join-windows-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="install-dns-server-tools"></a>DNS-kiszolgálói eszközök telepítése

A felügyelt tartományokban lévő DNS-rekordok létrehozásához és módosításához telepítenie kell a DNS-kiszolgáló eszközeit. Ezek az eszközök a Windows Server szolgáltatásként is telepíthetők. A felügyeleti eszközök Windows-ügyfélre történő telepítésével kapcsolatos további információkért lásd: install [Távoli kiszolgálófelügyelet eszközei (RSAT)][install-rsat].

1. Jelentkezzen be a felügyeleti virtuális gépre. A Azure Portal használatával történő kapcsolódás lépéseiért lásd: [Kapcsolódás Windows Server rendszerű virtuális géphez][connect-windows-server-vm].
1. Ha a **Kiszolgálókezelő** alapértelmezés szerint nem nyílik meg, amikor bejelentkezik a virtuális gépre, válassza a **Start** menüt, majd a **Kiszolgálókezelő** elemet.
1. A **Kiszolgálókezelő** ablak *irányítópult* paneljén válassza a **szerepkörök és szolgáltatások hozzáadása** lehetőséget.
1. A *szerepkörök és szolgáltatások hozzáadása varázsló* alapismeretek **lapján kattintson a** **Tovább gombra**.
1. A *telepítés típusa* beállításnál hagyja bejelölve a **szerepköralapú vagy a szolgáltatáson alapuló telepítési** beállítást, majd kattintson a **Tovább gombra**.
1. A **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgáló készletéből, például *myvm.aaddscontoso.com*, majd kattintson a **tovább** gombra.
1. A **kiszolgálói szerepkörök** lapon kattintson a **tovább** gombra.
1. A **szolgáltatások** lapon bontsa ki a **Távoli kiszolgálófelügyelet eszközei** csomópontot, majd bontsa ki a **szerepkör-felügyeleti eszközök** csomópontot. A szerepkör-felügyeleti eszközök listájából válassza a **DNS-kiszolgálói eszközök** lehetőséget.

    ![Válassza a DNS-kiszolgáló eszközeinek telepítését az elérhető szerepkör-felügyeleti eszközök listájáról](./media/manage-dns/install-dns-tools.png)

1. A **jóváhagyás** lapon válassza a **telepítés** lehetőséget. A DNS-kiszolgálói eszközök telepítéséhez egy-két percet is igénybe vehet.
1. A szolgáltatás telepítésének befejezése után a **Bezárás** gombra kattintva lépjen ki a **szerepkörök és szolgáltatások hozzáadása** varázslóból.

## <a name="open-the-dns-management-console-to-administer-dns"></a>A DNS-kezelő konzol megnyitása a DNS felügyeletéhez

A DNS-kiszolgálói eszközök telepítése után a felügyelt tartományon felügyelheti a DNS-rekordokat.

> [!NOTE]
> Ha felügyelt tartományban szeretné felügyelni a DNS-t, be kell jelentkeznie egy olyan felhasználói fiókba, amely tagja az *HRE DC-rendszergazdák* csoportnak.

1. A kezdőképernyőn válassza a **felügyeleti eszközök** elemet. Megjelenik az elérhető felügyeleti eszközök listája, beleértve az előző szakaszban telepített **DNS-t** is. Válassza a **DNS** lehetőséget a DNS-kezelő konzol elindításához.
1. A **Kapcsolódás a DNS-kiszolgálóhoz** párbeszédpanelen válassza ki **a következő számítógépet**, majd adja meg a felügyelt tartomány DNS-tartománynevét, például *aaddscontoso.com*:

    ![Kapcsolódás a felügyelt tartományhoz a DNS-konzolon](./media/manage-dns/connect-dns-server.png)

1. A DNS-konzol csatlakozik a megadott felügyelt tartományhoz. Bontsa ki a **címkeresési zónák** vagy a **névkeresési zónák** elemet a szükséges DNS-bejegyzések létrehozásához vagy a meglévő rekordok szükség szerinti szerkesztéséhez.

    ![DNS-konzol – tartomány felügyelete](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Ha a DNS-kiszolgáló eszközeivel kezeli a rekordokat, ügyeljen arra, hogy ne törölje vagy módosítsa az Azure AD DS által használt beépített DNS-rekordokat. A beépített DNS-rekordok közé tartoznak a tartományi DNS-rekordok, a Névkiszolgáló-rekordok és a TARTOMÁNYVEZÉRLŐk helyéhez használt egyéb rekordok. Ha módosítja ezeket a rekordokat, a tartományi szolgáltatások megszakadnak a virtuális hálózaton.

## <a name="create-conditional-forwarders"></a>Feltételes továbbítók létrehozása

Az Azure AD DS DNS-zónának csak a felügyelt tartomány zónáját és rekordjait kell tartalmaznia. Ne hozzon létre további zónákat a felügyelt tartományban a más DNS-névterekben található nevesített erőforrások feloldásához. Ehelyett a felügyelt tartomány feltételes továbbítói segítségével adja meg, hogy a DNS-kiszolgáló hová szeretné feloldani az adott erőforráshoz tartozó címeket.

A feltételes továbbító egy olyan konfigurációs lehetőség a DNS-kiszolgálón, amely lehetővé teszi egy DNS-tartomány, például a *contoso.com* megadását a lekérdezések továbbításához. Ahelyett, hogy a helyi DNS-kiszolgáló megpróbálja feloldani az adott tartományban lévő rekordok lekérdezéseit, a DNS-lekérdezések továbbítása az adott tartományhoz beállított DNS-re történik. Ez a konfiguráció biztosítja a megfelelő DNS-rekordok visszaadását, mivel nem hoz létre olyan helyi DNS-zónát, amely duplikált rekordokkal rendelkezik a felügyelt tartományban, hogy azok tükrözzék ezeket az erőforrásokat.

Ha feltételes továbbítót szeretne létrehozni a felügyelt tartományban, hajtsa végre a következő lépéseket:

1. Válassza ki a DNS-zónát, például *aaddscontoso.com*.
1. Válassza ki a **feltételes továbbítókat**, majd kattintson a jobb gombbal, és válassza az **új feltételes továbbító... lehetőséget.**
1. Adja meg a másik **DNS-tartományt**(például *contoso.com*), majd adja meg az adott névtérhez tartozó DNS-kiszolgálók IP-címeit az alábbi példában látható módon:

    ![Feltételes továbbító hozzáadása és konfigurálása a DNS-kiszolgálóhoz](./media/manage-dns/create-conditional-forwarder.png)

1. Jelölje be a **feltételes továbbító tárolása Active Directoryban** jelölőnégyzetet, majd a következőképpen replikálja azt, majd válassza ki a *tartomány összes DNS-kiszolgálójára* vonatkozó beállítást, az alábbi példában látható módon:

    ![DNS-konzol – a tartomány összes DNS-kiszolgálójának kijelölése](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Ha a feltételes továbbítót az *erdőben* tárolja a *tartomány* helyett, a feltételes továbbító meghibásodik.

1. A feltételes továbbító létrehozásához kattintson **az OK gombra**.

A felügyelt tartományhoz csatlakoztatott virtuális gépekről származó más névterekben lévő erőforrások névfeloldását mostantól megfelelően fel kell oldani. A feltételes továbbítóban konfigurált DNS-tartomány lekérdezéseit a rendszer a megfelelő DNS-kiszolgálókhoz továbbítja.

## <a name="next-steps"></a>Következő lépések

A DNS kezelésével kapcsolatos további információkért tekintse meg a következő témakört: [DNS-eszközök cikk a TechNeten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11)).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
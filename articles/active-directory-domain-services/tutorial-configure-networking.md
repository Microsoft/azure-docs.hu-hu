---
title: Oktatóanyag – virtuális hálózatkezelés konfigurálása a Azure AD Domain Serviceshoz | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure-beli virtuális hálózati alhálózatot vagy hálózati társítást egy Azure Active Directory Domain Services felügyelt tartományhoz a Azure Portal használatával.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618416"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Oktatóanyag: virtuális hálózatkezelés konfigurálása Azure Active Directory Domain Services felügyelt tartományhoz

A felhasználókhoz és az alkalmazásokhoz való kapcsolódás biztosításához egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományt telepítenek egy Azure-beli virtuális hálózati alhálózatba. Ezt a virtuális hálózati alhálózatot csak az Azure platform által biztosított felügyelt tartományi erőforrásokhoz lehet használni.

Ha saját virtuális gépeket és alkalmazásokat hoz létre, azok nem telepíthetők ugyanabba a virtuális hálózati alhálózatba. Ehelyett hozzon létre és helyezzen üzembe alkalmazásokat egy különálló virtuális hálózati alhálózatban, vagy egy különálló virtuális hálózatban, amely az Azure AD DS Virtual Network szolgáltatáshoz tartozik.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat dedikált virtuális hálózati alhálózatokat, illetve hogyan végezheti el egy másik hálózat társítását az Azure AD DS felügyelt tartomány virtuális hálózatához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A tartományhoz csatlakoztatott erőforrások virtuális hálózati csatlakozási lehetőségeinek megismerése az Azure AD DS
> * IP-címtartomány és további alhálózat létrehozása az Azure AD DS Virtual Networkben
> * Virtuális hálózati társítás konfigurálása az Azure-tól különálló hálózatokra AD DS

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Az Azure AD DS konfigurálásához *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services felügyelt tartományt hoz létre és konfigurál][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban a felügyelt tartományt a Azure Portal használatával hozza létre és konfigurálja. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Alkalmazás-munkaterhelés kapcsolódási beállításai

Az előző oktatóanyagban egy felügyelt tartomány lett létrehozva, amely a virtuális hálózat egyes alapértelmezett konfigurációs beállításait használta. Ezek az alapértelmezett beállítások egy Azure-beli virtuális hálózatot és egy virtuális hálózati alhálózatot hoztak létre. A felügyelt tartományi szolgáltatásokat biztosító Azure AD DS tartományvezérlők csatlakoznak ehhez a virtuális hálózati alhálózathoz.

Ha olyan virtuális gépeket hoz létre és futtat, amelyeknek a felügyelt tartományt kell használniuk, meg kell adni a hálózati kapcsolatot. Ez a hálózati kapcsolat az alábbi módszerek egyikével biztosítható:

* Hozzon létre egy további virtuális hálózati alhálózatot a felügyelt tartomány virtuális hálózatában. Ez a további alhálózat a virtuális gépek létrehozásához és összekapcsolásához.
    * Mivel a virtuális gépek ugyanahhoz a virtuális hálózathoz tartoznak, automatikusan elvégezhetik a névfeloldást, és kommunikálhatnak az Azure AD DS tartományvezérlőkkel.
* Konfigurálja az Azure Virtual Network-társítást a felügyelt tartomány virtuális hálózatáról egy vagy több különálló virtuális hálózatra. Ezek a különálló virtuális hálózatok a virtuális gépek létrehozásához és összekapcsolásához szükségesek.
    * A virtuális hálózati társítás konfigurálásakor a DNS-beállításokat is konfigurálnia kell, hogy a névfeloldást visszaállítsa az Azure AD DS tartományvezérlők használatára.

Általában csak az egyik hálózati kapcsolati lehetőség használható. A választási lehetőségek gyakran az Azure-erőforrások elkülönítésére szolgálnak.

* Ha az Azure AD DS és a csatlakoztatott virtuális gépeket egyetlen erőforrás-csoportként szeretné kezelni, létrehozhat egy további virtuális hálózati alhálózatot a virtuális gépekhez.
* Ha el szeretné különíteni az Azure AD DS és a csatlakoztatott virtuális gépek felügyeletét, használhatja a Virtual Network-társat.
    * Azt is megteheti, hogy virtuális hálózati társítást használ a meglévő virtuális hálózathoz csatlakoztatott Azure-környezetben meglévő virtuális gépekhez való kapcsolódás biztosításához.

Ebben az oktatóanyagban csak egy ilyen virtuális hálózati kapcsolati beállítást kell konfigurálnia.

A virtuális hálózat megtervezésével és konfigurálásával kapcsolatos további információkért lásd: [Azure Active Directory Domain Services hálózati szempontjai][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Virtuális hálózati alhálózat létrehozása

Alapértelmezés szerint a felügyelt tartománnyal létrehozott Azure-beli virtuális hálózat egyetlen virtuális hálózati alhálózatot tartalmaz. Ezt a virtuális hálózati alhálózatot csak az Azure platform használja a felügyelt tartományi szolgáltatások biztosításához. Ha saját virtuális gépeket szeretne létrehozni és használni ebben az Azure-beli virtuális hálózaton, hozzon létre egy további alhálózatot.

Ha virtuális hálózati alhálózatot szeretne létrehozni a virtuális gépek és az alkalmazások számítási feladataihoz, hajtsa végre a következő lépéseket:

1. A Azure Portal válassza ki a felügyelt tartományhoz tartozó erőforráscsoportot (például *myResourceGroup*). Az erőforrások listájából válassza ki az alapértelmezett virtuális hálózatot, például *aadds-vnet*.
1. A virtuális hálózat ablak bal oldali menüjében válassza a **címterület** lehetőséget. A virtuális hálózat egy *10.0.2.0/24*-es címtartomány használatával jön létre, amelyet az alapértelmezett alhálózat használ.

    Adjon hozzá egy további IP-címtartományt a virtuális hálózathoz. A címtartomány mérete és a használni kívánt IP-címtartomány a már telepített többi hálózati erőforrástól függ. Az IP-címtartomány nem fedi át az Azure-ban vagy a helyszíni környezetben lévő összes meglévő címtartományt. Győződjön meg arról, hogy az IP-címtartomány elég nagy méretű az alhálózatba telepítendő virtuális gépek számához.

    A következő példában egy *10.0.3.0/24* további IP-címtartomány van hozzáadva. Ha elkészült, válassza a **Mentés** lehetőséget.

    ![További virtuális hálózati IP-címtartomány hozzáadása a Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Ezután a virtuális hálózat ablak bal oldali menüjében válassza az **alhálózatok** elemet, majd válassza az **+ alhálózat** lehetőséget az alhálózat hozzáadásához.
1. Adja meg az alhálózat nevét, például a *munkaterheléseket*. Ha szükséges, frissítse a **címtartományt** , ha az előző lépésekben a virtuális hálózathoz konfigurált IP-címtartomány egy részhalmazát szeretné használni. Egyelőre hagyja meg az alapértelmezett beállításokat, például a hálózati biztonsági csoportot, az útválasztási táblázatot és a szolgáltatási végpontokat.

    A következő példában egy *munkaterhelések* nevű alhálózat jön létre, amely a *10.0.3.0/24* IP-címtartományt használja:

    ![További virtuális hálózati alhálózat hozzáadása a Azure Portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Ha elkészült, kattintson **az OK gombra**. A virtuális hálózat alhálózatának létrehozása néhány percet vesz igénybe.

Ha olyan virtuális gépet hoz létre, amelynek a felügyelt tartományt kell használnia, válassza ki ezt a virtuális hálózati alhálózatot. Ne hozzon létre virtuális gépeket az alapértelmezett *aadds-alhálózatban*. Ha másik virtuális hálózatot választ, nincs hálózati kapcsolat és a DNS-feloldás a felügyelt tartomány eléréséhez, hacsak nem konfigurálja a virtuális hálózati társítást.

## <a name="configure-virtual-network-peering"></a>Virtuális hálózati társak konfigurálása

Lehet, hogy rendelkezik egy meglévő Azure-beli virtuális hálózattal a virtuális gépekhez, vagy külön szeretné megtartani a felügyelt tartományhoz tartozó virtuális hálózatot. A felügyelt tartomány használatához más virtuális hálózatokban lévő virtuális gépeknek az Azure AD DS-tartományvezérlőkkel való kommunikációra van szükségük. Ez a kapcsolat az Azure Virtual Network-peering használatával biztosítható.

Az Azure Virtual Network-társítással két virtuális hálózat kapcsolódik egymáshoz, anélkül, hogy virtuális magánhálózati (VPN) eszközt kellene használnia. A hálózati társítás lehetővé teszi a virtuális hálózatok gyors összekapcsolását és az Azure-környezet forgalmának meghatározását.

A társítással kapcsolatos további információkért lásd: az [Azure Virtual Network társközi áttekintése][peering-overview].

Ha a virtuális hálózatot a felügyelt tartományhoz tartozó virtuális hálózatra szeretné irányítani, hajtsa végre a következő lépéseket:

1. Válassza ki az *aadds-vnet* nevű felügyelt tartományhoz létrehozott alapértelmezett virtuális hálózatot.
1. A virtuális hálózat ablak bal oldali menüjében **válassza a** társítások lehetőséget.
1. A társítás létrehozásához válassza a **+ Hozzáadás** lehetőséget. A következő példában az alapértelmezett *aadds-vnet* egy *myVnet* nevű virtuális hálózathoz van lefoglalva. Konfigurálja a következő beállításokat a saját értékeivel:

    * **A aadds-vnet és a távoli virtuális hálózat** közötti társítás neve: a két hálózat leíró azonosítója, például *aadds-vnet-to-myvnet*
    * **Virtuális hálózat központi telepítésének típusa**: *Resource Manager*
    * **Előfizetés**: annak a virtuális hálózatnak az előfizetése, amelyhez a társ, például az *Azure*
    * **Virtual Network (virtuális hálózat**): az a virtuális hálózat, amelyhez a társ, például a *myVnet*
    * **A myVnet-ből aadds-vnet-re** irányuló társítás neve: a két hálózat leíró azonosítója, például *myVnet – aadds-vnet*

    ![Virtuális hálózati társítás konfigurálása a Azure Portal](./media/tutorial-configure-networking/create-peering.png)

    Hagyja meg a virtuális hálózati hozzáférés vagy a továbbított forgalom egyéb alapértelmezett értékeit, ha nem rendelkezik konkrét követelményekkel a környezetéhez, majd kattintson **az OK gombra**.

1. Néhány percet is igénybe vehet, hogy az Azure AD DS virtuális hálózaton és a kiválasztott virtuális hálózaton is létrehozza a társítást. Ha elkészült, a társítási **állapotra** *vonatkozó jelentések a* következő példában látható módon jelennek meg:

    ![A Azure Portal csatlakoztatott hálózatok sikeresen csatlakoztatva](./media/tutorial-configure-networking/connected-peering.png)

Mielőtt a virtuális hálózatban lévő virtuális gépek használhatják a felügyelt tartományt, konfigurálja úgy a DNS-kiszolgálókat, hogy a megfelelő névfeloldást engedélyezzék.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>DNS-kiszolgálók konfigurálása a társ virtuális hálózaton

A virtuális gépek és alkalmazások a felügyelt tartományhoz való sikeres kommunikációhoz a DNS-beállításokat frissíteni kell. Az Azure AD DS tartományvezérlők IP-címeit a társ virtuális hálózaton lévő DNS-kiszolgálóknak kell konfigurálni. A tartományvezérlőket kétféleképpen lehet DNS-kiszolgálóként konfigurálni a társ virtuális hálózathoz:

* Konfigurálja az Azure-beli virtuális hálózati DNS-kiszolgálókat az Azure AD DS tartományvezérlők használatára.
* Konfigurálja a meglévő DNS-kiszolgálót a használt virtuális hálózaton, hogy feltételes DNS-továbbítást használjon a felügyelt tartományra irányuló közvetlen lekérdezésekhez. Ezek a lépések a jelenleg használt DNS-kiszolgálótól függően változnak.

Ebben az oktatóanyagban úgy konfigurálja az Azure-beli virtuális hálózati DNS-kiszolgálókat, hogy az összes lekérdezést az Azure AD DS tartományvezérlőkre irányítsák.

1. A Azure Portal válassza ki a társ virtuális hálózat (például a *myResourceGroup*) erőforrás-csoportját. Az erőforrások listájából válassza ki a társ virtuális hálózatot, például *myVnet*.
1. A virtuális hálózat ablak bal oldali menüjében válassza a **DNS-kiszolgálók** lehetőséget.
1. Alapértelmezés szerint a virtuális hálózatok az Azure által biztosított beépített DNS-kiszolgálókat használják. Válassza az **Egyéni** DNS-kiszolgálók használata lehetőséget. Adja meg az Azure AD DS tartományvezérlők IP-címeit, amelyek általában *10.0.2.4* és *végpontjául szolgáló*. Erősítse meg ezeket az IP-címeket a felügyelt tartományának **Áttekintés** ablakában a portálon.

    ![A virtuális hálózati DNS-kiszolgálók konfigurálása az Azure AD DS tartományvezérlők használatára](./media/tutorial-configure-networking/custom-dns.png)

1. Ha elkészült, válassza a **Mentés** lehetőséget. A virtuális hálózat DNS-kiszolgálóinak frissítése néhány percet vesz igénybe.
1. A frissített DNS-beállításoknak a virtuális gépekre való alkalmazásához indítsa újra a társ virtuális hálózathoz csatlakoztatott virtuális gépeket.

Ha olyan virtuális gépet hoz létre, amelynek a felügyelt tartományt kell használnia, győződjön meg róla, hogy ezt a társ virtuális hálózatot választja. Ha másik virtuális hálózatot választ, nincs hálózati kapcsolat és a DNS-feloldás a felügyelt tartomány eléréséhez.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A tartományhoz csatlakoztatott erőforrások virtuális hálózati csatlakozási lehetőségeinek megismerése az Azure AD DS
> * IP-címtartomány és további alhálózat létrehozása az Azure AD DS Virtual Networkben
> * Virtuális hálózati társítás konfigurálása az Azure-tól különálló hálózatokra AD DS

A felügyelt tartomány működés közbeni megtekintéséhez hozzon létre egy virtuális gépet, és csatlakozzon a tartományhoz.

> [!div class="nextstepaction"]
> [Windows Server rendszerű virtuális gép csatlakoztatása a felügyelt tartományhoz](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md

---
title: Oktatóanyag – erdőszintű megbízhatósági kapcsolat létrehozása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyirányú kimenő erdőt helyszíni AD DS tartományba a Azure Portalban Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2021
ms.author: justinha
ms.openlocfilehash: e381c80dddc4484d541f5f81de6b5df712cff69b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673468"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Oktatóanyag: kimenő erdőszintű megbízhatósági kapcsolat létrehozása helyi tartományhoz Azure Active Directory Domain Services

Olyan környezetekben, ahol nem lehet szinkronizálni a jelszó-kivonatokat, vagy ha a felhasználók kizárólag intelligens kártyákkal jelentkeznek be, és nem ismerik a jelszavukat, használhat egy erőforrás-erdőt Azure Active Directory Domain Services (Azure AD DS). Az erőforrás-erdő egyirányú kimenő bizalmi kapcsolatot használ az Azure AD DS egy vagy több helyszíni AD DS környezetbe. Ez a megbízhatósági kapcsolat lehetővé teszi a felhasználók, az alkalmazások és a számítógépek számára a helyszíni tartományon belüli hitelesítést az Azure AD DS felügyelt tartományból. Egy erőforrás-erdőben a helyszíni jelszavak kivonatait soha nem szinkronizálja a rendszer.

![Az Azure AD DS és a helyszíni AD DS közötti erdőszintű megbízhatóság diagramja](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A DNS konfigurálása helyszíni AD DS környezetben az Azure AD DS-kapcsolat támogatásához
> * Egyirányú bejövő erdőszintű megbízhatósági kapcsolat létrehozása helyszíni AD DS környezetben
> * Egyirányú kimenő erdőszintű megbízhatósági kapcsolat létrehozása az Azure-ban AD DS
> * A hitelesítés és az erőforrás-hozzáférés megbízhatósági kapcsolatának tesztelése és ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány, amely egy erőforrás-erdő használatával lett létrehozva, és az Azure AD-bérlőben van konfigurálva.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Győződjön meg arról, hogy egy *erőforrás* -erdő használatával felügyelt tartományt hoz létre. Az alapértelmezett beállítás egy *felhasználói* erdőt hoz létre. Csak az erőforrás-erdők hozhatnak létre megbízhatósági kapcsolatot a helyszíni AD DS környezetekben.
    >
    > Emellett legalább a *vállalati* SKU-t kell használnia a felügyelt tartományhoz. Ha szükséges, [módosítsa az SKU-t egy felügyelt tartományhoz][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban a Azure Portal használatával hozza létre és konfigurálja a kimenő erdőszintű megbízhatósági kapcsolatot az Azure-AD DS. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="networking-considerations"></a>Hálózati megfontolások

Az Azure AD DS erőforrás-erdőt üzemeltető virtuális hálózatnak a helyszíni Active Directory hálózati kapcsolatra van szüksége. Az alkalmazásoknak és a szolgáltatásoknak hálózati kapcsolatra van szükségük az Azure AD DS erőforrás-erdőt üzemeltető virtuális hálózathoz is. Az Azure AD DS erőforrás-erdőhöz való hálózati kapcsolatnak mindig és stabilnak kell lennie, különben előfordulhat, hogy a felhasználók nem tudják hitelesíteni vagy elérni az erőforrásokat.

Mielőtt erdőszintű megbízhatósági kapcsolatot konfigurál az Azure AD DSban, győződjön meg arról, hogy az Azure és a helyszíni környezet közötti hálózatkezelés megfelel a következő követelményeknek:

* Magánhálózati IP-címek használata. Ne használja a DHCP-t dinamikus IP-címek hozzárendelésével.
* Kerülje az átfedésben lévő IP-címek használatát, hogy a virtuális hálózatok és az Útválasztás sikeresen kommunikáljon az Azure-ban és a helyszínen.
* Egy Azure-beli virtuális hálózatnak szüksége van egy átjáró-alhálózatra az [Azure-helyek közötti (S2S) VPN-][vpn-gateway] vagy [ExpressRoute][expressroute] -kapcsolat konfigurálásához.
* Hozzon létre elegendő IP-címmel rendelkező alhálózatokat a forgatókönyv támogatásához.
* Győződjön meg arról, hogy az Azure AD DS rendelkezik saját alhálózattal, ne ossza meg ezt a virtuális hálózati alhálózatot az Application VM és a Services szolgáltatással.
* A társ virtuális hálózatok nem tranzitívak.
    * Az Azure-beli virtuális hálózatokat az összes olyan virtuális hálózat között létre kell hozni, amely az Azure AD DS erőforrás-erdő megbízhatóságát szeretné használni a helyszíni AD DS környezetben.
* Folyamatos hálózati kapcsolatot biztosít a helyszíni Active Directory erdőben. Ne használjon igény szerinti kapcsolatokat.
* Győződjön meg arról, hogy folyamatos névfeloldás (DNS) van az Azure AD DS erőforrás-erdő neve és a helyszíni Active Directory erdő neve között.

## <a name="configure-dns-in-the-on-premises-domain"></a>A DNS konfigurálása a helyszíni tartományban

A felügyelt tartomány helyszíni környezetből való megfelelő feloldásához lehetséges, hogy továbbítókat kell hozzáadnia a meglévő DNS-kiszolgálókhoz. Ha nem konfigurálta a helyszíni környezetet a felügyelt tartománysal való kommunikációra, hajtsa végre a következő lépéseket a helyszíni AD DS tartomány felügyeleti munkaállomásán:

1. Válassza a  >  **felügyeleti eszközök**  >  **DNS** indítása lehetőséget.
1. Kattintson a jobb gombbal a DNS-kiszolgáló, például a *myAD01*, majd a **Tulajdonságok** elemre.
1. Válassza a **továbbítók**, majd a **Szerkesztés** lehetőséget a további továbbítók hozzáadásához.
1. Adja hozzá a felügyelt tartomány IP-címeit, például a *10.0.2.4* és a *végpontjául szolgáló*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Bejövő erdőszintű megbízhatóság létrehozása a helyszíni tartományban

A helyszíni AD DS tartománynak rendelkeznie kell egy bejövő erdőszintű megbízhatósági kapcsolattal a felügyelt tartományhoz. Ezt a megbízhatóságot manuálisan kell létrehozni a helyszíni AD DS tartományban, nem hozható létre a Azure Portalból.

A helyi AD DS tartomány bejövő megbízhatóságának konfigurálásához hajtsa végre az alábbi lépéseket a helyszíni AD DS tartomány felügyeleti munkaállomásáról:

1. Válassza   >  a **felügyeleti eszközök** indítása  >  **Active Directory tartományok és Megbízhatóságok** lehetőséget.
1. Kattintson a jobb gombbal a tartományra, például *onprem.contoso.com*, majd válassza a **Tulajdonságok parancsot**.
1. Válassza a **Megbízhatóságok** fület, majd az **új megbízhatóság** lehetőséget.
1. Adja meg az Azure AD DS tartománynév nevét, például *aaddscontoso.com*, majd kattintson a **tovább** gombra.
1. Válassza az **erdőszintű megbízhatóság** létrehozása lehetőséget, majd hozzon létre egy **módszert: bejövő** megbízhatóság.
1. Válassza **ezt a tartományt csak** a megbízhatósági kapcsolat létrehozásához. A következő lépésben létrehozza a megbízhatóságot a felügyelt tartomány Azure Portalban.
1. Válassza az **erdőszintű hitelesítés** használata lehetőséget, majd adja meg és erősítse meg a megbízhatósági jelszót. Ugyanezt a jelszót is megadta a Azure Portal a következő szakaszban.
1. Lépjen be a következő néhány Windows alapértelmezett beállításokkal, majd válassza a nem lehetőséget **, ne erősítse meg a kimenő megbízhatóságot**.
1. Válassza a **Befejezés** gombot.

Ha az erdőszintű megbízhatóságra már nincs szükség egy adott környezetben, a következő lépésekkel távolíthatja el a helyszíni tartományból:

1. Válassza   >  a **felügyeleti eszközök** indítása  >  **Active Directory tartományok és Megbízhatóságok** lehetőséget.
1. Kattintson a jobb gombbal a tartományra, például *onprem.contoso.com*, majd válassza a **Tulajdonságok parancsot**.
1. Válassza a **Megbízhatóságok** fület, majd a **tartomány megbízhatóságát (bejövő Megbízhatóságok)**, kattintson az eltávolítani kívánt megbízhatósági kapcsolatra, majd kattintson az **Eltávolítás** gombra.
1. A Megbízhatóságok lapon, a **tartomány által megbízható tartományban (kimenő Megbízhatóságok)** területen kattintson az eltávolítani kívánt megbízhatósági kapcsolatra, majd kattintson az Eltávolítás gombra.
1. Kattintson a **nem gombra, csak a helyi tartományból távolítsa el a bizalmi kapcsolatot**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Kimenő erdőszintű megbízhatósági kapcsolat létrehozása az Azure-ban AD DS

A felügyelt tartomány feloldásához konfigurált helyszíni AD DS tartománnyal és egy bejövő erdőszintű megbízhatósági kapcsolat létrehozásával hozza létre a kimenő erdőszintű megbízhatóságot. Ez a kimenő erdőszintű megbízhatósági kapcsolat befejezi a helyszíni AD DS tartomány és a felügyelt tartomány közötti megbízhatósági kapcsolatot.

A Azure Portal felügyelt tartomány kimenő megbízhatóságának létrehozásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki a **Azure ad Domain Services** elemet, majd válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. A felügyelt tartomány bal oldali menüjében válassza a **Megbízhatóságok** lehetőséget, majd válassza a **+ megbízhatóság hozzáadása** lehetőséget.

   > [!NOTE]
   > Ha nem látja a **megbízhatósági kapcsolatok** menüt, ellenőrizze a **Tulajdonságok** területen az *erdő típusát*. Csak az *erőforrás* -erdők hozhatnak létre megbízhatósági kapcsolatokat. Ha az erdő típusa *felhasználó*, nem hozhat létre megbízhatósági kapcsolatot. Jelenleg nincs lehetőség a felügyelt tartomány erdő-típusának módosítására. Törölnie kell, majd újra létre kell hoznia a felügyelt tartományt erőforrás-erdőként.

1. Adja meg a megbízhatóságot azonosító megjelenítendő nevet, majd a helyszíni megbízható erdő DNS-nevét, például *onprem.contoso.com*.
1. Adja meg ugyanazt a megbízhatósági jelszót, amelyet az előző szakaszban található helyszíni AD DS tartományhoz tartozó bejövő erdő megbízhatóságának konfigurálásához használt.
1. Adjon meg legalább két DNS-kiszolgálót a helyszíni AD DS tartományhoz, például *10.1.1.4* és *10.1.1.5*.
1. Ha elkészült, **mentse** a kimenő erdő megbízhatóságát.

    ![Kimenő erdőszintű megbízhatóság létrehozása a Azure Portalban](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

Ha az erdőszintű megbízhatóságra már nincs szükség egy adott környezetben, a következő lépésekkel távolíthatja el az Azure AD DS:

1. A Azure Portal keresse meg és válassza ki a **Azure ad Domain Services** elemet, majd válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. A felügyelt tartomány bal oldali menüjében válassza a **Megbízhatóságok** lehetőséget, válassza ki a megbízhatóságot, és kattintson az **Eltávolítás** gombra.
1. Adja meg az erdőszintű megbízhatósági kapcsolat konfigurálásához használt megbízhatósági jelszót, majd kattintson az **OK** gombra.

## <a name="validate-resource-authentication"></a>Erőforrás-hitelesítés ellenőrzése

A következő gyakori forgatókönyvekkel ellenőrizheti, hogy az erdőszintű megbízhatóság helyesen hitelesíti-e a felhasználókat és az erőforrásokhoz való hozzáférést:

* [Helyszíni felhasználói hitelesítés az Azure AD DS erőforrás-erdőben](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Erőforrásokhoz való hozzáférés az Azure AD DS erőforrás-erdőben helyszíni felhasználó használatával](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Fájl-és nyomtatómegosztás engedélyezése](#enable-file-and-printer-sharing)
    * [Biztonsági csoport létrehozása és Tagok hozzáadása](#create-a-security-group-and-add-members)
    * [Fájlmegosztás létrehozása erdők közötti hozzáféréshez](#create-a-file-share-for-cross-forest-access)
    * [Erdők közötti hitelesítés ellenőrzése erőforráshoz](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Helyszíni felhasználói hitelesítés az Azure AD DS erőforrás-erdőben

A felügyelt tartományhoz csatlakoztatva kell lennie a Windows Server rendszerű virtuális gépnek. Ezzel a virtuális géppel ellenőrizheti, hogy a helyszíni felhasználó tud-e hitelesítést végezni egy virtuális gépen. Ha szükséges, [hozzon létre egy Windows rendszerű virtuális gépet, és csatlakoztassa a felügyelt tartományhoz][join-windows-vm].

1. Kapcsolódjon az Azure AD DS erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális géphez az [Azure Bastion](../bastion/bastion-overview.md) használatával és az Azure AD DS rendszergazdai hitelesítő adataival.
1. Nyisson meg egy parancssort, és a `whoami` parancs használatával jelenítse meg az aktuálisan hitelesített felhasználó megkülönböztető nevét:

    ```console
    whoami /fqdn
    ```

1. A `runas` parancs használatával hitelesítheti magát a helyszíni tartományból. A következő parancsban cserélje le a `userUpn@trusteddomain.com` elemet a megbízható helyszíni tartomány felhasználójának egyszerű felhasználónevére. A parancs a felhasználó jelszavát kéri:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Ha a hitelesítés sikeres, megnyílik egy új parancssori ablak. Az új parancssor címe tartalmazza `running as userUpn@trusteddomain.com` .
1. Az `whoami /fqdn` új parancssorban a helyi Active Directory a hitelesített felhasználó megkülönböztető nevének megtekintésére használható.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Erőforrásokhoz való hozzáférés az Azure AD DS erőforrás-erdőben helyszíni felhasználó használatával

Az Azure AD DS erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális gép használatával tesztelheti azt a forgatókönyvet, ahol a felhasználók hozzáférhetnek az erőforrás-erdőben tárolt erőforrásokhoz, amikor a helyszíni tartományban lévő számítógépekkel hitelesítik magukat a helyszíni tartomány számítógépeiről. Az alábbi példák bemutatják, hogyan hozhat létre és tesztelheti a különböző gyakori forgatókönyveket.

#### <a name="enable-file-and-printer-sharing"></a>Fájl-és nyomtatómegosztás engedélyezése

1. Kapcsolódjon az Azure AD DS erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális géphez az [Azure Bastion](../bastion/bastion-overview.md) használatával és az Azure AD DS rendszergazdai hitelesítő adataival.

1. Nyissa meg a **Windows-beállításokat**, majd keresse meg és válassza ki a **hálózati és megosztási központot**.
1. Válassza a **Speciális megosztási beállítások módosítása** lehetőséget.
1. A **tartományi profil** területen válassza a **fájl-és nyomtatómegosztás bekapcsolása** , majd a **módosítások mentése** lehetőséget.
1. **A hálózati és megosztási központ** bezárásához.

#### <a name="create-a-security-group-and-add-members"></a>Biztonsági csoport létrehozása és Tagok hozzáadása

1. Nyissa meg az **Active Directory – felhasználók és számítógépek** beépülő modult.
1. Kattintson a jobb gombbal a tartománynévre, válassza az **új**, majd a **szervezeti egység** elemet.
1. A név mezőbe írja be a *LocalObjects* nevet, majd kattintson az **OK gombra**.
1. Válassza ki, majd kattintson a jobb gombbal a **LocalObjects** elemre a navigációs ablaktáblán. Válassza az **új** , majd a **csoport** lehetőséget.
1. Írja  be a FileServerAccess **nevet a csoport neve** mezőbe. A **Csoport hatóköre** területen válassza a **tartomány helyi** lehetőséget, majd kattintson **az OK gombra**.
1. A tartalom ablaktáblán kattintson duplán a **FileServerAccess** elemre. Válassza a **tagok** lehetőséget, válassza a **Hozzáadás**, majd a **helyszínek** lehetőséget.
1. Válassza ki a helyszíni Active Directory a **hely** nézetből, majd kattintson **az OK gombra**.
1. Írja be a *tartományi felhasználók* értéket az **adja meg a kijelölendő objektumok nevét** mezőbe. Jelölje be a Névellenőrzés **jelölőnégyzetet**, adja meg a helyszíni Active Directory hitelesítő adatait, majd kattintson **az OK gombra**.

    > [!NOTE]
    > Meg kell adnia a hitelesítő adatokat, mert a megbízhatósági kapcsolat csak egy módszer. Ez azt jelenti, hogy az Azure AD DS felügyelt tartomány felhasználóinak nem férnek hozzá az erőforrásokhoz, és nem kereshetnek felhasználókat vagy csoportokat a megbízható (helyszíni) tartományban.

1. A helyszíni Active Directory **tartományi felhasználók** csoportjának a **FileServerAccess** csoport tagjának kell lennie. A csoport mentéséhez és az ablak bezárásához kattintson **az OK gombra** .

#### <a name="create-a-file-share-for-cross-forest-access"></a>Fájlmegosztás létrehozása erdők közötti hozzáféréshez

1. A Windows Server rendszerű virtuális gépen, amely az Azure AD DS erőforrás-erdőhöz csatlakozik, hozzon létre egy mappát, és adja meg a nevet (például *CrossForestShare*).
1. Kattintson a jobb gombbal a mappára, és válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Biztonság** fület, majd kattintson a **Szerkesztés** elemre.
1. A *CrossForestShare engedélyei* párbeszédpanelen válassza a **Hozzáadás** lehetőséget.
1. Írja be a *FileServerAccess* **nevet az írja be a kijelölendő objektumok nevét**, majd kattintson **az OK gombra**.
1. A **csoportok vagy a felhasználónevek** listából válassza a *FileServerAccess* lehetőséget. A **FileServerAccess engedélyei** listán válassza az *Engedélyezés lehetőséget* a **módosítási** és **írási** engedélyekhez, majd kattintson **az OK gombra**.
1. Válassza a **megosztás** fület, majd a **speciális megosztás** lehetőséget.
1. Válassza a **mappa megosztása** lehetőséget, majd adjon meg egy emlékezetes nevet a fájlmegosztás számára a **megosztás nevében** , például *CrossForestShare*.
1. Válassza az **Engedélyek** lehetőséget. Az **engedélyek mindenki** számára listában válassza az **Engedélyezés lehetőséget** a **módosítási** engedélyhez.
1. Kattintson kétszer **az OK** , majd a **Bezárás** gombra.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Erdők közötti hitelesítés ellenőrzése erőforráshoz

1. Jelentkezzen be a helyszíni Active Directoryhoz csatlakoztatott Windows-számítógép használatával a helyszíni Active Directory felhasználói fiókjával.
1. A **Windows Intéző** használatával kapcsolódjon a létrehozott megosztáshoz a teljes állomásnévvel és a megosztással, például: `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Az írási engedély ellenőrzéséhez kattintson a jobb gombbal a mappára, válassza az **új**, majd a **szöveges dokumentum** lehetőséget. Használja az alapértelmezett név **új szöveges dokumentumot**.

    Ha az írási engedélyek helyesen vannak beállítva, létrejön egy új szöveges dokumentum. A következő lépésekkel megnyithatja, szerkesztheti és szükség szerint törölheti a fájlt.
1. Az olvasási engedély ellenőrzéséhez nyissa meg az **új szöveges dokumentumot**.
1. A módosítási engedély érvényességének ellenőrzéséhez adjon hozzá szöveget a fájlhoz, és lépjen be a **Jegyzettömbbe**. Amikor a rendszer kéri a módosítások mentését, válassza a **Mentés** lehetőséget.
1. A törlési engedély ellenőrzéséhez kattintson a jobb gombbal az **új szöveges dokumentum** elemre, és válassza a **Törlés** lehetőséget. A fájl törlésének megerősítéséhez válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DNS konfigurálása helyszíni AD DS környezetben az Azure AD DS-kapcsolat támogatásához
> * Egyirányú bejövő erdőszintű megbízhatósági kapcsolat létrehozása helyszíni AD DS környezetben
> * Egyirányú kimenő erdőszintű megbízhatósági kapcsolat létrehozása az Azure-ban AD DS
> * A hitelesítés és az erőforrás-hozzáférés megbízhatósági kapcsolatának tesztelése és ellenőrzése

Az Azure AD DS erdők típusaival kapcsolatos további információk: Mik az [erőforrás-erdők?][concepts-forest] és [hogyan működnek az erdőszintű megbízhatósági kapcsolatok az Azure ad DSban?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md
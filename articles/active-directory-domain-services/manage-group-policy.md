---
title: Csoportházirend létrehozása és kezelése Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan szerkesztheti a beépített csoportházirend-objektumokat (GPO-kat), és hogyan hozhat létre saját egyéni házirendeket egy Azure Active Directory Domain Services felügyelt tartományban.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619385"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Csoportházirend felügyelete Azure Active Directory Domain Services felügyelt tartományban

Azure Active Directory Domain Services (Azure AD DS) felhasználói és számítógép-objektumainak beállításait a rendszer gyakran Csoportházirend objektumok (GPO-k) használatával kezeli. Az Azure AD DS beépített csoportházirend-objektumokat tartalmaz a *AADDC-felhasználók* és a *AADDC számítógép* -tárolók számára. Ezeket a beépített csoportházirend-objektumokat testreszabhatja úgy, hogy a környezetéhez szükség szerint konfigurálja Csoportházirend. Az *Azure ad DC-rendszergazdák* csoport tagjai csoportházirend rendszergazdai jogosultságokkal rendelkeznek az Azure AD DS tartományban, és egyéni csoportházirend-objektumokat és szervezeti egységeket (OU-ket) is létrehozhatnak. További információ a Csoportházirendről és annak működéséről: [csoportházirend Overview (áttekintés][group-policy-overview]).

Hibrid környezetben a helyszíni AD DS környezetekben konfigurált csoportházirendek nem szinkronizálhatók az Azure AD DS. Az Azure AD DSban található felhasználók vagy számítógépek konfigurációs beállításainak megadásához szerkessze az egyik alapértelmezett csoportházirend-objektumot, vagy hozzon létre egy egyéni GPO-t.

Ez a cikk bemutatja, hogyan telepítheti a Csoportházirend felügyeleti eszközöket, és hogyan szerkesztheti a beépített csoportházirend-objektumokat, és hogyan hozhat létre egyéni csoportházirend-objektumokat.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services felügyelt tartomány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Windows Server rendszerű virtuális gép létrehozásához és egy felügyelt tartományhoz való csatlakoztatásához][create-join-windows-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

> [!NOTE]
> Csoportházirend Felügyeleti sablonok az új sablonok felügyeleti munkaállomásra másolásával is használhatja. Másolja az *. admx* fájlokat a ba `%SYSTEMROOT%\PolicyDefinitions` , és másolja a területi beállítások *. adml* fájlokat a re `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` , ahol a `Language-CountryRegion` megegyezik a *. adml* fájlok nyelvével és régiójával.
>
> Másolja például a *. adml* fájl angol, Egyesült Államok verzióját a `\en-us` mappába.
>
> Azt is megteheti, hogy központilag tárolja a Csoportházirend felügyeleti sablont a felügyelt tartomány részét képező tartományvezérlőkön. További információ: [Csoportházirend felügyeleti sablonok központi tárolójának létrehozása és kezelése a Windowsban](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Csoportházirend felügyeleti eszközök telepítése

Csoportházirend objektum (GPO-k) létrehozásához és konfigurálásához telepítenie kell a Csoportházirend felügyeleti eszközöket. Ezek az eszközök a Windows Server szolgáltatásként is telepíthetők. A felügyeleti eszközök Windows-ügyfélre történő telepítésével kapcsolatos további információkért lásd: install [Távoli kiszolgálófelügyelet eszközei (RSAT)][install-rsat].

1. Jelentkezzen be a felügyeleti virtuális gépre. A Azure Portal használatával történő kapcsolódás lépéseiért lásd: [Kapcsolódás Windows Server rendszerű virtuális géphez][connect-windows-server-vm].
1. A **Kiszolgálókezelő** alapértelmezés szerint meg van nyitva, amikor bejelentkezik a virtuális gépre. Ha nem, a **Start** menüben válassza a **Kiszolgálókezelő** lehetőséget.
1. A **Kiszolgálókezelő** ablak *irányítópult* paneljén válassza a **szerepkörök és szolgáltatások hozzáadása** lehetőséget.
1. A *szerepkörök és szolgáltatások hozzáadása varázsló* alapismeretek **lapján kattintson a** **Tovább gombra**.
1. A *telepítés típusa* beállításnál hagyja bejelölve a **szerepköralapú vagy a szolgáltatáson alapuló telepítési** beállítást, majd kattintson a **Tovább gombra**.
1. A **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgáló készletéből, például *myvm.aaddscontoso.com*, majd kattintson a **tovább** gombra.
1. A **kiszolgálói szerepkörök** lapon kattintson a **tovább** gombra.
1. A **szolgáltatások** lapon válassza ki a **csoportházirend felügyeleti** funkciót.

    ![A "Csoportházirend felügyelet" telepítése a szolgáltatások lapról](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. A **jóváhagyás** lapon válassza a **telepítés** lehetőséget. A Csoportházirend felügyeleti eszközök telepítése egy-két percet is igénybe vehet.
1. A szolgáltatás telepítésének befejezése után a **Bezárás** gombra kattintva lépjen ki a **szerepkörök és szolgáltatások hozzáadása** varázslóból.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>A Csoportházirend-kezelő konzol megnyitása és objektum szerkesztése

Az alapértelmezett csoportházirend-objektumok (GPO-k) léteznek a felügyelt tartományokban lévő felhasználók és számítógépek számára. Az előző szakaszban telepített Csoportházirend felügyeleti szolgáltatással megtekintheti és szerkesztheti a meglévő csoportházirend-objektumokat. A következő szakaszban létrehozhat egy egyéni csoportházirend-objektumot.

> [!NOTE]
> Ha felügyelt tartományban szeretné felügyelni a csoportházirendet, be kell jelentkeznie egy olyan felhasználói fiókba, amely tagja az *HRE DC-rendszergazdák* csoportnak.

1. A kezdőképernyőn válassza a **felügyeleti eszközök** elemet. Megjelenik az elérhető felügyeleti eszközök listája, beleértve az előző szakaszban telepített **csoportházirend felügyeletet** is.
1. A Csoportházirend-kezelő konzol (GPMC) megnyitásához válassza a **csoportházirend felügyelet** lehetőséget.

    ![A Csoportházirend-kezelő konzol készen áll a csoportházirend-objektumok szerkesztésére](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

A felügyelt tartományokban két beépített Csoportházirend objektum (GPO) található – egyet a *AADDC számítógépek* tárolóhoz, egyet pedig a *AADDC Users* tárolóhoz. Ezeket a csoportházirend-objektumokat testreszabhatja úgy, hogy szükség szerint konfigurálja a csoportházirendet a felügyelt tartományon belül.

1. A **csoportházirend felügyeleti** konzolon bontsa ki az **erdő: aaddscontoso.com** csomópontot. Ezután bontsa ki a **tartományok** csomópontokat.

    Két beépített tároló létezik a *AADDC számítógépek* és a *AADDC-felhasználók* számára. Ezen tárolók mindegyike alapértelmezett GPO-t alkalmaz.

    ![Az alapértelmezett "AADDC Computers" és "AADDC Users" tárolók esetében alkalmazott beépített csoportházirend-objektumok](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Ezek a beépített csoportházirend-objektumok testreszabhatók meghatározott csoportházirendek konfigurálásához a felügyelt tartományon. Kattintson a jobb gombbal valamelyik csoportházirend-objektumra, például a *AADDC számítógépek csoportházirend-objektumára*, majd válassza a **Szerkesztés...** lehetőséget.

    ![Válassza a beépített csoportházirend-objektumok egyikének szerkesztésére szolgáló lehetőséget.](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Megnyílik a Csoportházirend-felügyeleti szerkesztő eszköz, amely lehetővé teszi a csoportházirend-objektum testreszabását, például a *fiókok házirendjeit*:

    ![Képernyőkép a Csoportházirend-felügyeleti szerkesztőról.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Ha elkészült, válassza a **fájl > mentés** lehetőséget a szabályzat mentéséhez. A számítógépek alapértelmezés szerint 90 percenként frissülnek, és az elvégzett módosítások alkalmazásával Csoportházirend.

## <a name="create-a-custom-group-policy-object"></a>Egyéni Csoportházirend objektum létrehozása

A hasonló házirend-beállítások csoportosításához gyakran további csoportházirend-objektumokat kell létrehoznia, ahelyett, hogy az egyetlen, alapértelmezett GPO-ban alkalmazza az összes szükséges beállítást. Az Azure AD DS használatával létrehozhatja vagy importálhatja saját egyéni csoportházirend-objektumait, és összekapcsolhatja azokat egy egyéni szervezeti egységgel. Ha először létre kell hoznia egy egyéni szervezeti egységet, tekintse meg az [Egyéni szervezeti egység létrehozása felügyelt tartományban](create-ou.md)című témakört.

1. A **csoportházirend felügyeleti** konzolon válassza ki az egyéni szervezeti egységet (OU), például *MyCustomOU*. Kattintson a jobb gombbal a szervezeti egységre, majd válassza a **csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás itt...**:

    ![Egyéni csoportházirend-objektum létrehozása a Csoportházirend felügyeleti konzolon](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Adja meg az új csoportházirend-objektum nevét, például *az egyéni csoportházirend-objektumot*, majd kattintson **az OK gombra**. Ezt az egyéni csoportházirend-objektumot kiválaszthatja egy meglévő csoportházirend-objektumra, és beállíthatja a házirend-beállításokat.

    ![Adja meg az új egyéni csoportházirend-objektum nevét](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Az egyéni csoportházirend-objektum létrejön, és az egyéni szervezeti egységhez van csatolva. A házirend-beállítások konfigurálásához kattintson a jobb gombbal az egyéni csoportházirend-objektumra, és válassza a **Szerkesztés...** lehetőséget:

    ![Válassza az egyéni csoportházirend-objektum szerkesztése lehetőséget](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. A **csoportházirend-felügyeleti szerkesztő** megnyílik, hogy lehetővé tegye a csoportházirend-objektum testreszabását:

    ![Csoportházirend-objektum testreszabása a szükséges beállítások megadásához](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Ha elkészült, válassza a **fájl > mentés** lehetőséget a szabályzat mentéséhez. A számítógépek alapértelmezés szerint 90 percenként frissülnek, és az elvégzett módosítások alkalmazásával Csoportházirend.

## <a name="next-steps"></a>Következő lépések

További információ a Csoportházirend-kezelő konzol használatával konfigurálható elérhető Csoportházirend beállításokról: [csoportházirend beállításelemek][group-policy-console]használata.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)

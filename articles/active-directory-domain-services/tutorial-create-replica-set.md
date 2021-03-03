---
title: Oktatóanyag – replikakészlet létrehozása Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és használhat replikakészlet-csoportokat a Azure Portal a szolgáltatás rugalmasságához Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: e8887cc39b48f090ff223e5e83c13d65b921dc0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687498"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>Oktatóanyag: replika-készletek létrehozása és használata a rugalmassághoz vagy a földrajzi helyekhez Azure Active Directory Domain Services

Egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány rugalmasságának javításához, vagy az alkalmazásokhoz közelítő további földrajzi helyekre való üzembe helyezéshez használhat *replikakészlet-készleteket*. Minden Azure AD DS felügyelt tartományi névtér, például a *aaddscontoso.com*, egy kezdeti replikakészlet-készletet tartalmaz. A további replikák más Azure-régiókban való létrehozásának képessége földrajzi rugalmasságot biztosít a felügyelt tartományokhoz.

Bármely olyan Azure-régióban, amely támogatja az Azure AD DS-t, hozzáadhat egy replikát bármely olyan társ virtuális hálózathoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A virtuális hálózati követelmények ismertetése
> * Replikakészlet létrehozása
> * Replikakészlet törlése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány, amelyet a Azure Resource Manager üzembe helyezési modell használatával hoztak létre, és az Azure AD-bérlőben vannak konfigurálva.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][tutorial-create-instance].

    > [!IMPORTANT]
    > A klasszikus üzemi modell használatával létrehozott felügyelt tartományok nem használhatnak replikákat. Emellett legalább a *vállalati* SKU-t kell használnia a felügyelt tartományhoz. Ha szükséges, [módosítsa az SKU-t egy felügyelt tartományhoz][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban replikakészlet-készleteket hoz létre és kezel a Azure Portal használatával. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="networking-considerations"></a>Hálózati megfontolások

A replika-készleteket futtató virtuális hálózatoknak képesnek kell lenniük egymással való kommunikációra. Az Azure AD DStól függő alkalmazásoknak és szolgáltatásoknak hálózati kapcsolatra van szükségük a replikákat üzemeltető virtuális hálózatokhoz is. Az Azure Virtual Network-társítást úgy kell konfigurálni, hogy az összes virtuális hálózat között egy teljes szembőségű hálózatot hozzon létre. Ezek a társítások lehetővé teszik a replika-készletek közötti hatékony helyszíni replikációt.

Az Azure AD DS-beli replikák használata előtt tekintse át a következő Azure-beli virtuális hálózati követelményeket:

* Kerülje az átfedésben lévő IP-címek használatát a virtuális hálózatok társításának és útválasztásának engedélyezéséhez.
* Hozzon létre elegendő IP-címmel rendelkező alhálózatokat a forgatókönyv támogatásához.
* Győződjön meg arról, hogy az Azure AD DS rendelkezik saját alhálózattal. Ne ossza meg ezt a virtuális hálózati alhálózatot Application VM-sel és-szolgáltatásokkal.
* A társ virtuális hálózatok nem tranzitívak.

> [!TIP]
> Amikor létrehoz egy replikakészlet-készletet a Azure Portalban, a rendszer létrehozza a virtuális hálózatok közötti hálózati társításokat.
>
> Ha szükséges, hozzon létre egy virtuális hálózatot és alhálózatot, amikor új replikát ad hozzá a Azure Portal. Másik lehetőségként kiválaszthatja a meglévő virtuális hálózati erőforrásokat a céltartományban a kópiakészlet számára, és lehetővé teheti a társítások automatikus létrehozását, ha még nem léteznek.

## <a name="create-a-replica-set"></a>Replikakészlet létrehozása

A felügyelt tartományok, például a *aaddscontoso.com* létrehozásakor létrejön egy kezdeti replikakészlet. A további replika-készletek ugyanazt a névteret és konfigurációt használják. Az Azure AD DS módosításai, beleértve a konfigurációt, a felhasználói identitást és a hitelesítő adatokat, a csoportokat, a csoportházirend-objektumokat, a számítógép-objektumokat és az egyéb módosításokat a felügyelt tartomány összes másodpéldányára alkalmazza AD DS replikáció használatával.

Ebben az oktatóanyagban egy további másodpéldányt hoz létre egy olyan Azure-régióban, amely eltér a kezdeti Azure AD DS-replika-készlettől.

További replikakészlet létrehozásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. A bal oldali oldalon válassza a **másodpéldányok** lehetőséget. Minden felügyelt tartomány tartalmaz egy kezdeti replikát a kiválasztott régióban, ahogy az a következő példában látható:

    ![Példa képernyőképre egy replikakészlet megtekintéséhez és hozzáadásához a Azure Portal](./media/tutorial-create-replica-set/replica-set-list.png)

    További replikakészlet létrehozásához válassza a **+ Hozzáadás** lehetőséget.

1. A *kópiakészlet hozzáadása* ablakban válassza ki a célhelyet, például az *USA keleti* régióját.

    Válasszon ki egy virtuális hálózatot a célhely régióban, például *vnet-eastus*, majd válasszon egy alhálózatot, például *aadds-subnet*. Ha szükséges, válassza az **új létrehozása** lehetőséget a virtuális hálózat a célhelyen való hozzáadásához, majd a **kezelés** elemre, és hozzon létre egy alhálózatot az Azure AD DS számára.

    Ha még nem léteznek, az Azure-beli virtuális hálózati társítások automatikusan létrejönnek a meglévő felügyelt tartomány virtuális hálózata és a célként megadott virtuális hálózat között.

    Az alábbi képernyőképen egy új replikakészlet az *USA keleti* régiójában való létrehozásának folyamata látható:

    ![Példa képernyőképre egy replikakészlet létrehozásához a Azure Portal](./media/tutorial-create-replica-set/create-replica-set.png)

1. Ha elkészült, válassza a **Mentés** lehetőséget.

A replikakészlet létrehozásának folyamata eltarthat egy ideig, amíg a rendszer létrehozza az erőforrásokat a rendeltetési régióban. A felügyelt tartományt ezután replikálja AD DS replikáció használatával.

A replikakészlet az üzembe helyezési folyamat folytatásaként állítja be a *jelentést, ahogy* az a következő példában látható. Ha elkészült, a kópiakészlet *futásként* jelenik meg.

![Példa a replika telepítési állapotának képernyőképére a Azure Portal](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Replikakészlet törlése

A felügyelt tartományok jelenleg legfeljebb négy replikára korlátozódnak – a kezdeti replikára és három további replikakészlet-készletre. Ha már nincs szüksége egy másodpéldányra, vagy ha egy másik régióban kíván replikát létrehozni, akkor törölheti a szükségtelen replikákat.

> [!IMPORTANT]
> A legutóbb beállított replika nem törölhető egy felügyelt tartományban.

Egy replikakészlet törléséhez hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. A bal oldali oldalon válassza a **másodpéldányok** lehetőséget. A kópiakészlet listájából válassza a törölni kívánt replikakészlet melletti **...** helyi menüt.
1. A helyi menüben válassza a **Törlés** lehetőséget, majd erősítse meg, hogy törölni kívánja a replikát.

> [!NOTE]
> A replikakészlet törlése időigényes művelet lehet.

Ha már nincs szüksége a replikakészlet által használt virtuális hálózatra vagy társításra, akkor ezeket az erőforrásokat is törölheti. A törlés előtt győződjön meg arról, hogy a másik régióban nincs más alkalmazás-erőforrás a hálózati kapcsolatokhoz.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális hálózati társak konfigurálása
> * Replikakészlet létrehozása másik földrajzi régióban
> * Replikakészlet törlése

További elméleti információk: hogyan működnek a replika-készletek az Azure AD DSban.

> [!div class="nextstepaction"]
> [A replika alapfogalmai és funkciói][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md

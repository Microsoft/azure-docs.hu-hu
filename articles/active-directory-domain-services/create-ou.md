---
title: Szervezeti egység (OU) létrehozása a Azure AD Domain Servicesban | Microsoft Docs "
description: Megtudhatja, hogyan hozhat létre és kezelhet egyéni szervezeti egységeket Azure AD Domain Services felügyelt tartományokban.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620018"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Szervezeti egység (OU) létrehozása Azure Active Directory Domain Services felügyelt tartományban

A szervezeti egységek (OU-EK) egy Active Directory Domain Services (AD DS) felügyelt tartományban lehetővé teszik, hogy logikailag csoportosítsa az objektumokat, például a felhasználói fiókokat, a szolgáltatásfiókokat vagy a számítógépfiókokat. Ezután hozzárendelheti a rendszergazdákat adott szervezeti egységekhez, és Csoportházirendet alkalmazhat a megcélzott konfigurációs beállítások betartatására.

Az Azure AD DS felügyelt tartományok a következő két beépített szervezeti egységgel rendelkeznek:

* *AADDC számítógépek* – a felügyelt tartományhoz csatlakoztatott összes számítógép számítógép-objektumait tartalmazza.
* *AADDC-felhasználók* – az Azure ad-bérlőből szinkronizált felhasználókat és csoportokat tartalmaz.

Az Azure AD DSt használó munkaterhelések létrehozásakor és futtatásakor előfordulhat, hogy létre kell hoznia egy szolgáltatásfiókot az alkalmazásokhoz, hogy azok hitelesítése megtörténjen. A szolgáltatásfiókok rendszerezéséhez gyakran hozzon létre egy egyéni szervezeti egységet a felügyelt tartományon belül, majd hozzon létre szolgáltatásfiókot a szervezeti egységben.

Hibrid környezetben a helyszíni AD DS környezetben létrehozott szervezeti egységek nem szinkronizálhatók a felügyelt tartományba. A felügyelt tartományok lapos OU-struktúrát használnak. Az összes felhasználói fiókot és csoportot a rendszer a *AADDC-felhasználók* tárolóban tárolja, annak ellenére, hogy a különböző helyszíni tartományokból vagy erdőkből szinkronizálva van, még akkor is, ha van konfigurálva egy hierarchikus szervezeti egység struktúrája.

Ez a cikk bemutatja, hogyan hozhat létre szervezeti egységet a felügyelt tartományában.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services felügyelt tartomány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="custom-ou-considerations-and-limitations"></a>Egyéni OU-megfontolások és korlátozások

Amikor egyéni szervezeti egységeket hoz létre egy felügyelt tartományban, a felhasználók felügyeletéhez és a csoportházirend alkalmazásához további kezelési rugalmasságot szerezhet. A helyszíni AD DS környezethez képest bizonyos korlátozások és megfontolások a felügyelt tartományokban lévő egyéni SZERVEZETIEGYSÉG-struktúra létrehozásakor és kezelésekor:

* Egyéni szervezeti egységek létrehozásához a felhasználóknak a *HRE DC-rendszergazdák* csoport tagjának kell lenniük.
* Az egyéni szervezeti egységeket létrehozó felhasználók a szervezeti egység felett rendszergazdai jogosultságokkal (teljes hozzáférés) rendelkeznek, és az erőforrás tulajdonosa.
    * Alapértelmezés szerint az *HRE DC-rendszergazdák* csoport az egyéni szervezeti egység teljes hozzáférését is ellenőrzi.
* A rendszer létrehoz egy alapértelmezett szervezeti egységet a *AADDC felhasználók számára* , amely az Azure ad-bérlő összes szinkronizált felhasználói fiókját tartalmazza.
    * A felhasználók vagy csoportok nem helyezhetők át a *AADDC felhasználói* szervezeti egységből a létrehozott egyéni szervezeti egységekre. Csak a felügyelt tartományban létrehozott felhasználói fiókok és erőforrások helyezhetők át egyéni szervezeti egységbe.
* Az egyéni szervezeti egységek alatt létrehozott felhasználói fiókok, csoportok, szolgáltatásfiókok és számítógép-objektumok nem érhetők el az Azure AD-bérlőben.
    * Ezek az objektumok nem jelennek meg a Microsoft Graph API vagy az Azure AD felhasználói felületén. csak a felügyelt tartományokban érhetők el.

## <a name="create-a-custom-ou"></a>Egyéni szervezeti egység létrehozása

Egyéni szervezeti egység létrehozásához a Active Directory felügyeleti eszközöket kell használnia egy tartományhoz csatlakoztatott virtuális gépről. A Active Directory felügyeleti központ segítségével megtekintheti, szerkesztheti és létrehozhatja az erőforrásokat egy felügyelt tartományban, beleértve a szervezeti egységeket is.

> [!NOTE]
> Ha egyéni szervezeti egységet szeretne létrehozni egy felügyelt tartományban, be kell jelentkeznie egy olyan felhasználói fiókba, amely az *HRE DC-rendszergazdák* csoport tagja.

1. Jelentkezzen be a felügyeleti virtuális gépre. A Azure Portal használatával történő kapcsolódás lépéseiért lásd: [Kapcsolódás Windows Server rendszerű virtuális géphez][connect-windows-server-vm].
1. A kezdőképernyőn válassza a **felügyeleti eszközök** elemet. Megjelenik a rendelkezésre álló felügyeleti eszközök listája, amely az oktatóanyagban a [felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm]lett telepítve.
1. A szervezeti egységek létrehozásához és kezeléséhez válassza ki a **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblán válassza ki a felügyelt tartományt, például *aaddscontoso.com*. Megjelenik a meglévő szervezeti egységek és erőforrások listája:

    ![Válassza ki a felügyelt tartományt a Active Directory felügyeleti központ](./media/create-ou/create-ou-adac-overview.png)

1. A **feladatok** ablaktábla a Active Directory felügyeleti központ jobb oldalán jelenik meg. A tartomány alatt, például a *aaddscontoso.com* területen válassza az **új > szervezeti egység** lehetőséget.

    ![Válassza ki az új szervezeti egység létrehozásához szükséges lehetőséget a Active Directory felügyeleti központ](./media/create-ou/create-ou-adac-new-ou.png)

1. A **szervezeti egység létrehozása** párbeszédpanelen adja meg az új szervezeti egység (például *MyCustomOu*) **nevét** . Adja meg a szervezeti egység rövid leírását, például az *Egyéni szervezeti egységet a szolgáltatásfiókok számára*. Ha kívánja, megadhatja a szervezeti egység **felügyelt** mezőjét is. Az egyéni szervezeti egység létrehozásához kattintson **az OK gombra**.

    ![Egyéni szervezeti egység létrehozása a Active Directory felügyeleti központ](./media/create-ou/create-ou-dialog.png)

1. A Active Directory felügyeleti központban az egyéni szervezeti egység már fel van sorolva, és használható:

    ![A Active Directory felügyeleti központ használható egyéni szervezeti egység](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Következő lépések

A felügyeleti eszközök használatával, illetve a szolgáltatásfiókok létrehozásával és használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Active Directory felügyeleti központ: Első lépések](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Szolgáltatásfiókok részletes útmutatója](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
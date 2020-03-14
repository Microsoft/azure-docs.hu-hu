---
title: Tulajdonosok és felhasználók hozzáadása a Azure DevTest Labs-ben | Microsoft Docs
description: Azure DevTest Labs tulajdonosainak és felhasználóinak hozzáadása a Azure Portal vagy a PowerShell használatával
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284277"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Tulajdonosok és felhasználók hozzáadása a Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Azure DevTest Labs hozzáférését az [Azure szerepköralapú Access Control (RBAC)](../role-based-access-control/overview.md)vezérli. A RBAC segítségével elkülönítheti a munkatársain belüli feladatokat olyan *szerepkörökbe* , amelyekben csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést kell biztosítania. Ezek közül három RBAC-szerepkör *tulajdonosa*, *DevTest Labs-felhasználó*és *közreműködő*. Ebből a cikkből megtudhatja, milyen műveleteket végezhet el a három fő RBAC-szerepkörben. Ebből a témakörből megtudhatja, hogyan adhat hozzá felhasználókat egy laborhoz – mindkettőt a portálon keresztül, egy PowerShell-parancsfájllal, és hogyan adhat hozzá felhasználókat az előfizetési szinten.

## <a name="actions-that-can-be-performed-in-each-role"></a>Az egyes szerepkörökben végrehajtható műveletek
Három fő szerepkört rendelhet hozzá a felhasználókhoz:

* Tulajdonos
* DevTest Labs User
* Közreműködő

Az alábbi táblázat azokat a műveleteket mutatja be, amelyeket a felhasználók a következő szerepkörökben végezhetnek el:

| **A szerepkör felhasználói által végrehajtható műveletek** | **DevTest Labs-felhasználó** | **Tulajdonos** | **Közreműködő** |
| --- | --- | --- | --- |
| **Laboratóriumi feladatok** | | | |
| Felhasználók hozzáadása laborhoz |Nem |Igen |Nem |
| A költségadatok frissítése |Nem |Igen |Igen |
| **VM-alapfeladatok** | | | |
| Egyéni lemezképek hozzáadása és eltávolítása |Nem |Igen |Igen |
| Képletek hozzáadása, frissítése és törlése |Igen |Igen |Igen |
| Engedélyezési Azure Marketplace-lemezképek |Nem |Igen |Igen |
| **VIRTUÁLIS gépekkel kapcsolatos feladatok** | | | |
| Virtuális gépek létrehozása |Igen |Igen |Igen |
| Virtuális gépek elindítása, leállítása és törlése |Csak a felhasználó által létrehozott virtuális gépek |Igen |Igen |
| Virtuálisgép-házirendek frissítése |Nem |Igen |Igen |
| Adatlemezek hozzáadása vagy eltávolítása a virtuális gépekhez |Csak a felhasználó által létrehozott virtuális gépek |Igen |Igen |
| **Összetevő-feladatok** | | | |
| Összetevő-Tárházak hozzáadása és eltávolítása |Nem |Igen |Igen |
| Összetevők alkalmazása |Igen |Igen |Igen |

> [!NOTE]
> Amikor egy felhasználó létrehoz egy virtuális gépet, a rendszer automatikusan hozzárendeli a felhasználót a létrehozott virtuális gép **tulajdonosi** szerepköréhez.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Tulajdonos vagy felhasználó hozzáadása a labor szintjén
A tulajdonosok és a felhasználók a labor szintjén adhatók hozzá a Azure Portal használatával. A felhasználók lehetnek olyan külső felhasználók, akik érvényes [Microsoft-fiók (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)rendelkeznek.
A következő lépések végigvezetik egy tulajdonos vagy felhasználó egy laborhoz való hozzáadásának folyamatán Azure DevTest Labsban:

1. Jelentkezzen be az [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A laborok listájából válassza ki a kívánt labort.
4. A labor paneljén válassza a **konfiguráció és szabályzatok**lehetőséget. 
5. A **konfiguráció és házirendek** lapon válassza a **hozzáférés-vezérlés (iam)** lehetőséget a bal oldali menüből. 
6. Válassza a **szerepkör-hozzárendelés hozzáadása** lehetőséget az eszköztáron egy felhasználó szerepkörhöz való hozzáadásához.
1. Az **engedélyek hozzáadása** ablakban végezze el a következő műveleteket: 
    1. Válasszon egy szerepkört (például: DevTest Labs user). Az [egyes szerepkörökben elvégezhető műveletek](#actions-that-can-be-performed-in-each-role) a tulajdonos, a DevTest-felhasználó és a közreműködői szerepkörök felhasználói által végrehajtható különféle műveleteket sorolja fel.
    2. Válassza ki a szerepkörbe felvenni kívánt felhasználót. 
    3. Kattintson a **Mentés** gombra. 
11. Amikor visszatér a **felhasználók** panelre, a felhasználó hozzá lett adva.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Külső felhasználó hozzáadása laborhoz a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Azure Portal felhasználók hozzáadásán kívül hozzáadhat egy külső felhasználót a laborhoz egy PowerShell-parancsfájl használatával. A következő példában módosítsa a paraméterek értékeit a Megjegyzés **módosítása** elemre.
A `subscriptionId`, `labResourceGroup`és `labName` értékeket a Azure Portal Lab paneljéről kérheti le.

> [!NOTE]
> A minta parancsfájl feltételezi, hogy a megadott felhasználó vendégként lett hozzáadva a Active Directoryhoz, és sikertelen lesz, ha ez nem így van. Ha egy olyan felhasználót szeretne hozzáadni a Active Directoryhoz, amely nem szerepel a laborban, a Azure Portal használatával rendelje hozzá a felhasználót egy szerepkörhöz, ahogy azt a (z) szakaszban látható módon, [adjon hozzá egy tulajdonost vagy egy felhasználót a labor szintjén](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Tulajdonos vagy felhasználó hozzáadása az előfizetési szinten
Az Azure-engedélyek a fölérendelt hatókörből az Azure-beli alárendelt hatókörbe vannak propagálva. Ezért a Labs-t tartalmazó Azure-előfizetés tulajdonosai automatikusan tulajdonosai. Emellett a labor felhasználói által létrehozott virtuális gépeket és egyéb erőforrásokat, valamint a Azure DevTest Labs szolgáltatást is magukban foglalhatják. 

A labor paneljén keresztül további tulajdonosokat adhat hozzá a laborhoz a [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Azonban a hozzáadott tulajdonos felügyeleti hatóköre sokkal keskenyebb, mint az előfizetés tulajdonosának hatóköre. A hozzáadott tulajdonosok például nem rendelkeznek teljes hozzáféréssel a DevTest Labs szolgáltatás előfizetésében létrehozott egyes erőforrásokhoz. 

A tulajdonos Azure-előfizetéshez való hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza az **előfizetések** elemet a listából.
3. Válassza ki a kívánt előfizetést.
4. Válassza a **hozzáférés** ikont. 
   
    ![Hozzáférés a felhasználókhoz](./media/devtest-lab-add-devtest-user/access-users.png)
5. A **felhasználók** panelen válassza a **Hozzáadás**lehetőséget.
   
    ![Felhasználó hozzáadása](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. A **szerepkör kiválasztása** panelen válassza a **tulajdonos**lehetőséget.
7. A **felhasználók hozzáadása** panelen adja meg a tulajdonosként hozzáadni kívánt felhasználó e-mail-címét vagy nevét. Ha a felhasználó nem található, hibaüzenetet kap a probléma elmagyarázása érdekében. Ha a felhasználó megtalálható, a felhasználó megjelenik a **felhasználó** szövegmezőben.
8. Válassza ki az elhelyezkedő felhasználónevet.
9. Válassza a **kiválasztás**lehetőséget.
10. A **hozzáférés hozzáadása** panel bezárásához kattintson **az OK gombra** .
11. Amikor visszatér a **felhasználók** panelre, a felhasználó tulajdonosként lett hozzáadva. Ez a felhasználó mostantól az előfizetés alatt létrehozott laborok tulajdonosa, így a tulajdonosi feladatok elvégzésére is képes. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]


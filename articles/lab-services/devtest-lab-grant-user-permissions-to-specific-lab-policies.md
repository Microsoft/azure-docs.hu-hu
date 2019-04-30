---
title: Adott laborszabályzatok felhasználói engedélyek megadása |} A Microsoft Docs
description: Ismerje meg, hogyan adhat felhasználói engedélyeket adott laborszabályzatok DevTest Labs szolgáltatásban létrehozott minden egyes felhasználói igények alapján történő
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 70469a9e8737a9df18628951a061c97081c74080
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127378"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Felhasználói engedélyek adott laborszabályzatok megadása
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan felhasználók egy adott labor házirend szükséges engedélyek biztosítása a PowerShell használatával. Ezzel a módszerrel engedélyek alkalmazhatók minden felhasználói igényeknek megfelelően. Például érdemes megadni egy adott felhasználó megváltoztathatja a virtuális gép házirend-beállításokat, de nem a költségek házirendeket.

## <a name="policies-as-resources"></a>Erőforrásként házirendek
Az a [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) a cikk az RBAC lehetővé teszi, hogy az Azure-erőforrások részletes hozzáférés-vezérlést. Az RBAC használatával, feladatköröket a fejlesztési és üzemeltetési csapaton belül, és csak olyan mértékű hozzáférést biztosítson a felhasználók számára, amelyek a feladataik elvégzéséhez szükségük van.

DevTest Labs-környezetben, a házirend egy erőforrás típusa, amely lehetővé teszi, hogy az RBAC-művelet **Microsoft.DevTestLab/labs/policySets/policies/**. Minden labor házirend egy erőforrást a szabályzat erőforrás típusa, és hozzárendelheti az RBAC szerepkör hatóköreként.

Például ahhoz, hogy adjon felhasználók írási/olvasási engedélyt a **engedélyezett Virtuálisgép-méretek** szabályzat, akkor kell létrehoznia egy egyéni biztonsági szerepkört, amely együttműködik a **Microsoft.DevTestLab/labs/policySets/policies/** művelet , majd rendelje hozzá a megfelelő felhasználók az egyéni szerepkör hatókörébe tartozó **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Az RBAC-ben egyéni szerepkörökkel kapcsolatos további információkért tekintse meg a [egyéni szerepkörök hozzáférés-vezérlés](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>PowerShell-lel labor egyéni szerepkörök létrehozása
Annak érdekében, hogy első lépésként kell [Azure PowerShell telepítése](/powershell/azure/install-az-ps). 

Miután beállította az Azure PowerShell-parancsmagokat, akkor a következő feladatokat hajthatja végre:

* A műveletek/műveleteket egy erőforrás-szolgáltató listázása
* Egy adott szerepkörben lévő műveletek listája:
* Egyéni szerepkör létrehozása

A következő PowerShell-parancsfájl példákat ezeket a műveleteket mutatja be:

    ‘List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Egy felhasználó számára egy adott szabályzatot egyéni szerepkörök segítségével engedélyek hozzárendelése
Miután meghatározta a egyéni szerepkörök, hozzárendelheti azokat a felhasználókat. Annak érdekében, hogy egy egyéni szerepkör hozzárendelése felhasználóhoz, be kell szereznie a **ObjectId** jelölő, hogy a felhasználó. Ehhez használja a **Get-AzADUser** parancsmagot.

A következő példában a **ObjectId** , a *SomeUser* felhasználói 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Miután a **ObjectId** a felhasználói és a egy egyéni szerepkör neve rendelhet adott szerepkörrel rendelkező felhasználó a **New-AzRoleAssignment** parancsmagot:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

Az előző példában a **AllowedVmSizesInLab** házirendet használja. Használhatja az alábbi szabályzatokat:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Egyszer, már jogosultságot felhasználó adott laborszabályzatok, érdemes figyelembe venni néhány következő lépések a következők:

* [Biztonságos hozzáférés a laborokhoz](devtest-lab-add-devtest-user.md)
* [Laborszabályzatok megadása](devtest-lab-set-lab-policy.md)
* [Laborsablon létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális géphez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)


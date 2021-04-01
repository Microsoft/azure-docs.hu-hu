---
title: Statikus csoport tagságának módosítása dinamikusra – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan alakíthatja át a meglévő csoportokat statikusról dinamikus tagságra az Azure AD felügyeleti központ vagy a PowerShell-parancsmagok használatával.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a46cc3f4a0f2fb25fc693103a64a319dcec0324
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860881"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Statikus csoporttagság módosítása a Azure Active Directory dinamikus értékre

A csoportok tagságát statikusról dinamikusra (vagy fordítva) is módosíthatja Azure Active Directory (Azure AD). Az Azure AD ugyanazt a csoportnevet és azonosítót tárolja a rendszeren, így a csoport összes meglévő hivatkozása továbbra is érvényes. Ha ehelyett új csoportot hoz létre, akkor frissítenie kell ezeket a hivatkozásokat. A dinamikus csoporttagság kiküszöböli a felügyeleti terhelést a felhasználók hozzáadásával és eltávolításával. Ebből a cikkből megtudhatja, hogyan alakíthatja át a meglévő csoportokat statikusról dinamikus tagságra az Azure AD felügyeleti központ vagy a PowerShell-parancsmagok használatával.

> [!WARNING]
> Egy meglévő statikus csoport dinamikus csoportra való módosításakor a rendszer az összes meglévő tagot eltávolítja a csoportból, majd a tagsági szabályt feldolgozza új tagok hozzáadására. Ha a csoport az alkalmazásokhoz vagy erőforrásokhoz való hozzáférés szabályozására szolgál, vegye figyelembe, hogy az eredeti tagok elvesztik a hozzáférést, amíg a tagsági szabályt nem dolgozza fel teljes mértékben.
>
> Azt javasoljuk, hogy előzetesen tesztelje az új tagsági szabályt, és győződjön meg arról, hogy a csoport új tagja a vártnak megfelelően működik-e.

## <a name="change-the-membership-type-for-a-group"></a>Csoport tagsági típusának módosítása

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely globális rendszergazda, felhasználói rendszergazda vagy csoportok rendszergazdája az Azure ad-szervezetben.
2. Válassza a **csoportok** lehetőséget.
3. A **minden csoport** listából nyissa meg a módosítani kívánt csoportot.
4. Válassza ki a **Tulajdonságok** elemet.
5. A csoport **Tulajdonságok** lapján válassza ki a hozzárendelt (statikus), a dinamikus felhasználó vagy a dinamikus eszköz **tagsági típusát** a kívánt tagsági típustól függően. A dinamikus tagság érdekében a Rule Builder használatával kiválaszthatja az egyszerű szabályok beállításait, vagy saját maga is írhat egy tagsági szabályt. 

A következő lépések a csoportok statikusról dinamikus tagságra történő módosítását szemléltetik egy adott felhasználói csoport esetében.

1. A kiválasztott csoporthoz tartozó **Tulajdonságok** lapon válassza ki a **dinamikus felhasználó** **tagsági típusát** , majd válassza az Igen lehetőséget a csoport tagságának folytatásához szükséges módosítások elvégzése párbeszédpanelen. 
  
   ![Válassza ki a dinamikus felhasználó tagsági típusát](./media/groups-change-type/select-group-to-convert.png)
  
2. Válassza a **dinamikus lekérdezés hozzáadása** lehetőséget, majd adja meg a szabályt.
  
   ![adja meg a dinamikus csoport szabályát](./media/groups-change-type/enter-rule.png)
  
3. A szabály létrehozása után válassza a lap alján található **lekérdezés hozzáadása** lehetőséget.
4. A módosítások mentéséhez kattintson a csoport **Tulajdonságok** lapján a **Mentés** gombra. A csoport **tagsági típusa** azonnal frissül a csoportok listájában.

> [!TIP]
> A csoport átalakítása sikertelen lehet, ha a megadott tagsági szabály helytelen. Egy értesítés jelenik meg a portál jobb felső sarkában, és magyarázatot tartalmaz arról, hogy a rendszer miért nem fogadja el a szabályt. Olvassa el figyelmesen, hogy megtudja, hogyan módosíthatja a szabályt, hogy érvényes legyen. A szabály szintaxisára, valamint a tagsági szabály által támogatott tulajdonságok, operátorok és értékek teljes listájára vonatkozó példákat a következő témakörben talál: [Azure Active Directory csoportok dinamikus tagsági szabályai](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Csoport tagsági típusának módosítása (PowerShell)

> [!NOTE]
> A dinamikus csoport tulajdonságainak módosításához az [Azure ad PowerShell 2. verziójának](/powershell/azure/active-directory/install-adv2) **előzetes verziójából** származó parancsmagokat kell használnia. Az előnézetet a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview)is telepítheti.

Íme egy példa olyan függvényekre, amelyek átváltják a tagságok kezelését egy meglévő csoporton. Ebben a példában a GroupTypes tulajdonság megfelelő kezelését és a dinamikus tagsághoz nem kapcsolódó értékek megőrzését kell elvégezni.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Statikus csoport létrehozása:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Dinamikus csoport létrehozása:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Következő lépések

Ezek a cikkek további információkat nyújtanak Azure Active Directory csoportjairól.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)

---
title: Érzékenységi címkék társítása csoportokhoz – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan rendelhet hozzá érzékenységi címkéket a csoportokhoz. Tekintse meg a hibaelhárítási információkat, és tekintse meg a további elérhető erőforrásokat.
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
ms.openlocfilehash: a78cf9b7d78078030ac0db8bd2f0fddb93a8dda4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881396"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Érzékenységi címkék kiosztása Microsoft 365 csoportokhoz Azure Active Directory

Azure Active Directory (Azure AD) támogatja az [Microsoft 365 megfelelőségi központ](https://sip.protection.office.com/homepage) által Microsoft 365 csoportoknak közzétett érzékenységi címkék alkalmazását. Az érzékenységi címkék olyan szolgáltatásokra vonatkoznak, mint az Outlook, a Microsoft Teams és a SharePoint. Az Microsoft 365 alkalmazások támogatásával kapcsolatos további információkért lásd [az érzékenységi címkék Microsoft 365 támogatását](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)ismertető témakört.

> [!IMPORTANT]
> A szolgáltatás konfigurálásához legalább egy aktív prémium szintű Azure Active Directory P1-licenccel kell rendelkeznie az Azure AD-szervezetben.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Az érzékenységi címke támogatásának engedélyezése a PowerShellben

A közzétett címkék csoportokba való alkalmazásához először engedélyeznie kell a szolgáltatást. Ezekkel a lépésekkel engedélyezheti az Azure AD funkcióját.

1. Nyisson meg egy Windows PowerShell-ablakot a számítógépen. Emelt szintű jogosultságok nélkül is megnyithatja.
1. Futtassa a következő parancsokat a parancsmagok futtatásának előkészítéséhez.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    A **Bejelentkezés a fiókba** lapon adja meg a rendszergazdai fiókját és jelszavát a szolgáltatáshoz való kapcsolódáshoz, majd válassza a **Bejelentkezés** lehetőséget.
1. Az Azure AD-szervezet aktuális csoportjának beállításainak beolvasása.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Ha nem hoztak létre csoportházirend-beállításokat ehhez az Azure AD-szervezethez, hibaüzenet jelenik meg a fenti parancsmagban, amely a következőt olvassa be: "nem köthető argumentum a (z)" id "paraméterhez, mert null". Ebben az esetben először létre kell hoznia a beállításokat. Az Azure AD-szervezethez tartozó csoportházirend-beállítások létrehozásához kövesse a [Azure Active Directory parancsmagok](../enterprise-users/groups-settings-cmdlets.md) című szakasz lépéseit.

1. Ezután jelenítse meg az aktuális csoport beállításait.

    ```PowerShell
    $Setting.Values
    ```

1. Ezután engedélyezze a szolgáltatást:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Ezután mentse a módosításokat, és alkalmazza a beállításokat:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Az érzékenységi címkéket az Azure AD-be is szinkronizálnia kell. Útmutatásért lásd: [a tárolók érzékenységi címkéjének engedélyezése és a címkék szinkronizálása](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels).

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Címke kiosztása egy új csoportba Azure Portal

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com).
1. Válassza a **csoportok** lehetőséget, majd válassza az **új csoport** lehetőséget.
1. Az **új csoport** lapon válassza az **Office 365** lehetőséget, majd adja meg az új csoport szükséges adatait, és válasszon ki egy érzékenységi címkét a listából.

   ![Érzékenységi címke kiosztása az új csoportok lapon](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Mentse a módosításokat, és válassza a **Létrehozás** lehetőséget.

A rendszer létrehozza a csoportot, és a kiválasztott címkéhez társított hely-és csoportfiókok automatikusan érvénybe lép.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Címke kiosztása meglévő csoporthoz Azure Portal

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy csoport-rendszergazdai fiókkal vagy egy csoport tulajdonosaként.
1. Válassza a **csoportok** lehetőséget.
1. A **minden csoport** lapon válassza ki a címkézni kívánt csoportot.
1. A kiválasztott csoport lapon válassza a **Tulajdonságok** lehetőséget, és válasszon egy érzékenységi címkét a listából.

   ![Érzékenységi címke kiosztása egy csoport áttekintés lapján](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. A módosítások mentéséhez válassza a **Mentés** gombot.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Címke eltávolítása meglévő csoportból Azure Portal

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) globális rendszergazdai vagy csoportos rendszergazdai fiókkal, vagy egy csoport tulajdonosaként.
1. Válassza a **csoportok** lehetőséget.
1. A **minden csoport** lapon válassza ki azt a csoportot, amelyből el szeretné távolítani a címkét.
1. A **csoport** lapon válassza a **Tulajdonságok** lehetőséget.
1. Válassza az **Eltávolítás** lehetőséget.
1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

## <a name="using-classic-azure-ad-classifications"></a>Klasszikus Azure AD-besorolások használata

A funkció engedélyezése után a csoportok "klasszikus" besorolása csak a meglévő csoportokat és helyeket fogja megjelenni, és csak akkor használja őket az új csoportokhoz, ha olyan alkalmazásokban hoz létre csoportokat, amelyek nem támogatják az érzékenységi címkéket. A rendszergazda szükség esetén később is átalakíthatja az érzékenységi címkéket. A klasszikus besorolások a régi besorolások, amelyet az `ClassificationList` Azure ad PowerShell beállítás értékeinek meghatározásával állíthat be. Ha ez a szolgáltatás engedélyezve van, a rendszer nem alkalmazza ezeket a besorolásokat a csoportokra.

## <a name="troubleshooting-issues"></a>Problémák hibaelhárítása

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Az érzékenységi címkék nem érhetők el a csoporton való hozzárendeléshez

Az érzékenységi címke beállítás csak akkor jelenik meg a csoportok esetében, ha a következő feltételek teljesülnek:

1. A címkék az Azure AD-szervezet Microsoft 365 megfelelőségi központjában jelennek meg.
1. A szolgáltatás engedélyezve van, a EnableMIPLabels értéke TRUE (igaz) értékre van állítva az Azure AD PowerShell-modulból.
1. A lables szinkronizálva vannak az Azure AD-val a Execute-AzureAdLabelSync parancsmaggal a biztonsági & megfelelőségi PowerShell moduljában.
1. A csoport Microsoft 365 csoport.
1. A szervezet aktív prémium szintű Azure Active Directory P1 licenccel rendelkezik.
1. A jelenlegi bejelentkezett felhasználónak megfelelő jogosultsága van a címkék hozzárendeléséhez. A felhasználónak globális rendszergazdának, csoport rendszergazdának vagy a csoport tulajdonosának kell lennie.

Győződjön meg arról, hogy az összes feltétel teljesül, hogy címkéket rendeljen egy csoporthoz.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>A hozzárendelni kívánt címke nem szerepel a listában

Ha a keresett címke nem szerepel a listán, akkor ez a következő okok egyike lehet:

- Előfordulhat, hogy a címke nem kerül közzétételre a Microsoft 365 megfelelőségi központban. Ez a már nem közzétett címkékre is alkalmazható. További információért forduljon a rendszergazdához.
- Előfordulhat, hogy a címke közzé van téve, de nem érhető el a bejelentkezett felhasználó számára. Kérjen segítséget a rendszergazdától a címke elérésének módjával kapcsolatban.

### <a name="how-to-change-the-label-on-a-group"></a>Csoport címkéének módosítása

A címkék bármikor felcserélhetők, ha ugyanazokat a lépéseket használják, mint a címkék egy meglévő csoportba való hozzárendelésével, az alábbiak szerint:

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) globális vagy csoportos rendszergazdai fiókkal vagy a csoport tulajdonosaként.
1. Válassza a **csoportok** lehetőséget.
1. A **minden csoport** lapon válassza ki a címkézni kívánt csoportot.
1. A kiválasztott csoport lapon válassza a **Tulajdonságok** lehetőséget, és válasszon egy új érzékenységi címkét a listából.
1. Kattintson a **Mentés** gombra.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>A csoportok a közzétett címkékre vonatkozó módosításait nem frissítik a csoportokon

Ajánlott eljárásként Azt javasoljuk, hogy a címke beállításait a csoportokra való alkalmazás után módosítsa a címkékre. Ha a [Microsoft 365 megfelelőségi központban](https://sip.protection.office.com/homepage)közzétett címkékhez társított csoportházirend-beállításokat módosítja, a rendszer ezeket a házirend-módosításokat nem alkalmazza automatikusan az érintett csoportokra.

Ha módosítania kell a módosításokat, az [Azure ad PowerShell-parancsfájllal](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) manuálisan alkalmazhatja az érintett csoportok frissítéseit. Ezzel a módszerrel gondoskodhat arról, hogy minden meglévő csoport érvényesítse az új beállítást.

## <a name="next-steps"></a>Következő lépések

- [A Microsoft Teams, a Microsoft 365 csoportok és a SharePoint-webhelyek érzékenységi feliratainak használata](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Csoportok frissítése a címkézési házirend módosítása után manuálisan az Azure AD PowerShell-parancsfájllal](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [A csoport beállításainak szerkesztése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoportok kezelése PowerShell-parancsokkal](../enterprise-users/groups-settings-v2-cmdlets.md)

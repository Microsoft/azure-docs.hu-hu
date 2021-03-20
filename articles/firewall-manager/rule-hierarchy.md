---
title: Szabály-hierarchia definiálása Azure Firewall házirend használatával
description: Megtudhatja, hogyan határozhat meg Azure Firewall szabályzatot a szabályok hierarchiájának definiálásához és a megfelelőség érvényesítéséhez.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92331736"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Szabály-hierarchia definiálása Azure Firewall házirend használatával

A biztonsági rendszergazdáknak tűzfalat kell kezelniük, és biztosítaniuk kell a helyszíni és a Felhőbeli üzemelő példányok megfelelőségét. A kulcsfontosságú összetevők lehetővé teszik, hogy rugalmasan nyújtsanak be olyan alkalmazási csapatokat, amelyekkel automatizált módon hozhatók létre a tűzfalszabályok és a CD-folyamatok.

Azure Firewall házirend lehetővé teszi a szabály-hierarchia definiálását és a megfelelőség érvényesítését:

- Hierarchikus struktúrát biztosít ahhoz, hogy a központi alapházirendet egy alárendelt alkalmazási csapat házirendjére lehessen befedi. Az alapházirend magasabb prioritással rendelkezik, és a gyermek házirend előtt fut.
- Az Azure egyéni szerepkör-definíciójának használatával megakadályozhatja a véletlen alapházirend-eltávolítást, és szelektív hozzáférést biztosíthat a szabályok gyűjtési csoportjaihoz egy előfizetésen vagy erőforráscsoporton belül. 

## <a name="solution-overview"></a>Megoldás áttekintése

A példához tartozó magas szintű lépések a következők:

1. Hozzon létre egy alapszintű tűzfal-házirendet a biztonsági csoport erőforráscsoporthoz. 
3. Az alapházirendben adja meg az informatikai biztonságra vonatkozó szabályokat. Ezzel a beállítással közös szabályokat adhat meg a forgalom engedélyezéséhez vagy megtagadásához.
4. Hozzon létre olyan Application Team-házirendeket, amelyek öröklik az alapházirendet. 
5. Az alkalmazás-csoportra vonatkozó szabályok definiálása a szabályzatban. A szabályok áttelepíthetők a már meglévő tűzfalakból is.
6. Hozzon létre Azure Active Directory egyéni szerepköröket, amelyekkel részletes hozzáférést biztosíthat a szabályok gyűjtési csoportjához, és szerepköröket adhat hozzá a tűzfalszabályok hatóköréhez. A következő példában az értékesítési csapat tagjai szerkeszthetik a szabályok gyűjtési csoportjait az értékesítési csapatok tűzfal házirendjéhez. Ugyanez vonatkozik az adatbázisra és a mérnöki csapatokra is.
7. Rendelje hozzá a szabályzatot a megfelelő tűzfalhoz. Egy Azure-tűzfalhoz csak egy hozzárendelt szabályzat tartozhat. Ehhez minden alkalmazás-csapatnak saját tűzfallal kell rendelkeznie.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Csapatok és követelmények" border="false":::

### <a name="create-the-firewall-policies"></a>A tűzfal házirendjeinek létrehozása

- Egy alapszintű tűzfal-házirend.

Szabályzatok létrehozása az egyes alkalmazási csapatokhoz:

- Egy értékesítési tűzfal házirendje. Az értékesítési tűzfal házirendje örökli az alapszintű tűzfal házirendjét.
- Egy adatbázis-tűzfal házirend. Az adatbázis-tűzfal házirendje örökli az alapszintű tűzfal házirendjét.
- Egy mérnöki tűzfal házirendje. A mérnöki tűzfal házirendje szintén örökli az alapszintű tűzfal-házirendet.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Házirend-hierarchia" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Egyéni szerepkörök létrehozása a szabálygyűjtemény-csoportok eléréséhez 

Az egyes alkalmazási csapatokhoz egyéni szerepkörök vannak meghatározva. A szerepkör meghatározza a műveleteket és a hatókört. Az alkalmazási csapatok szerkeszthetik a szabályok gyűjtési csoportjait a megfelelő alkalmazásaikban.

Egyéni szerepkörök definiálásához használja a következő magas szintű eljárást:

1. Előfizetés beszerzése:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Futtassa az alábbi parancsot:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Az olvasó szerepkör JSON formátumban való kimenetéhez használja a Get-AzRoleDefinition parancsot. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Nyissa meg a ReaderSupportRole.jsfájlt egy szerkesztőben.

   Az alábbiakban a JSON kimenete látható. További információ a különböző tulajdonságokkal kapcsolatban: [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. A JSON-fájl szerkesztésével adja hozzá a 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   művelet a **műveletek**   tulajdonsághoz. Ügyeljen arra, hogy az olvasási művelet után mindenképpen használjon vesszőt. Ez a művelet lehetővé teszi a felhasználó számára a szabálygyűjtemény-csoportok létrehozását és frissítését.
6. A **AssignableScopes**-ben adja hozzá az előfizetés-azonosítót a következő formátummal: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   Explicit előfizetési azonosítókat kell megadnia, különben nem importálhatja a szerepkört az előfizetésébe.
7. Törölje az **azonosító**   tulajdonság sorát, és módosítsa a **IsCustom**   tulajdonságot True értékre.
8. A  **név**   és a  **Leírás**   tulajdonságainak módosítása a AZFM-szabálygyűjtemény és a *szerepkörhöz tartozó felhasználók számára a tűzfalszabályok szerkesztési csoportjai* 

A JSON-fájlnak az alábbi példához hasonlóan kell kinéznie:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Az új egyéni szerepkör létrehozásához használja a New-AzRoleDefinition parancsot, és határozza meg a JSON-szerepkör definícióját tartalmazó fájlt. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

Az összes egyéni szerepkör listázásához használhatja a Get-AzRoleDefinition parancsot:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

A Azure Portal egyéni szerepkörei is megtekinthetők. Nyissa meg az előfizetését, és válassza a **hozzáférés-vezérlés (iam)**, majd a **szerepkörök** lehetőséget.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="SalesAppPolicy olvasási engedélye":::

További információ: [oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával](../role-based-access-control/tutorial-custom-role-powershell.md).

### <a name="add-users-to-the-custom-role"></a>Felhasználók hozzáadása az egyéni szerepkörhöz

A portálon felhasználókat adhat hozzá a AZFM-szabályok gyűjtési csoportjának szerzők szerepkörhöz, és hozzáférést biztosíthat a tűzfal házirendjeihez.

1. A portálon válassza ki az alkalmazás csapatának tűzfal-házirendjét (például SalesAppPolicy).
2. Válassza a **Access Control** lehetőséget.
3. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
4. Vegyen fel felhasználókat vagy felhasználói csoportokat (például az értékesítési csapatot) a szerepkörbe.

Ismételje meg ezt az eljárást a többi tűzfal-házirendnél.

### <a name="summary"></a>Összefoglalás

Az egyéni szerepkörökkel rendelkező tűzfalszabályok mostantól szelektív hozzáférést biztosítanak a tűzfalszabályok szabály-összegyűjtési csoportjaihoz.

A felhasználók nem rendelkeznek a következő engedélyekkel:
- Törölje a Azure Firewall vagy a tűzfal házirendjét.
- Frissítse a tűzfalszabályok hierarchiáját vagy a DNS-beállításokat vagy a fenyegetési intelligenciát.
- Frissítse a tűzfal-házirendet, ahol nem tagjai a AZFM-szabály gyűjtési csoportjának szerző csoportjának.

A biztonsági rendszergazdák az alapházirenddel kikényszerítik a guardrails, és letilthatják bizonyos típusú forgalmat (például az ICMP-t) a vállalatuk igényei szerint. 

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Firewall házirendről](policy-overview.md).


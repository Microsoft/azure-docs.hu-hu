---
title: Az Azure RBAC hibáinak megoldása
description: Az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) kapcsolatos problémák elhárítása.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d77468619fcd67887273b2fbd452b37add1e19b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555881"
---
# <a name="troubleshoot-azure-rbac"></a>Az Azure RBAC hibáinak megoldása

Ez a cikk az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) kapcsolatos gyakori kérdésekre ad választ, így biztos lehet benne, hogy mire számíthat a szerepkörök használatakor, és elháríthatja a hozzáférési problémákat.

## <a name="azure-role-assignments-limit"></a>Azure szerepkör-hozzárendelések korlátja

Az Azure legfeljebb **2000** szerepkör-hozzárendelést támogat előfizetésenként. Ez a korlát tartalmazza az előfizetés, az erőforráscsoport és az erőforrás-hatókörök szerepkör-hozzárendeléseit. Ha a "nincs több szerepkör-hozzárendelés hozható létre (code: RoleAssignmentLimitExceeded)" hibaüzenet jelenik meg, amikor megpróbál hozzárendelni egy szerepkört, csökkentse a szerepkör-hozzárendelések számát az előfizetésben.

> [!NOTE]
> Az **2000** -os szerepkör-hozzárendelési korlát/előfizetés rögzített, és nem növelhető.

Ha közeledik ehhez a korláthoz, akkor az alábbi módokon csökkentheti a szerepkör-hozzárendelések számát:

- Vegyen fel felhasználókat a csoportokhoz, és rendelje hozzá a szerepköröket a csoportokhoz. 
- Több beépített szerepkör egyesítése egyéni szerepkörrel. 
- Az általános szerepkör-hozzárendeléseket magasabb hatókörben, például előfizetésben vagy felügyeleti csoportban hajthatja végre.
- Ha rendelkezik prémium szintű Azure AD P2-vel, akkor a szerepkör-hozzárendeléseket a rendszer a végleges hozzárendelés helyett [Azure ad Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) jogosult. 
- Adjon hozzá egy további előfizetést. 

A szerepkör-hozzárendelések számának lekéréséhez tekintse [meg a diagramot a Azure Portal hozzáférés-vezérlés (iam) lapján](role-assignments-list-portal.md#list-number-of-role-assignments) . A következő Azure PowerShell parancsokat is használhatja:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure-beli szerepkör-hozzárendelésekkel kapcsolatos problémák

- Ha nem tud szerepkört hozzárendelni a **hozzáférés-vezérlési Azure Portalban (iam)** , mert **a**  >  **szerepkör-hozzárendelés hozzáadása** beállítás le van tiltva, vagy az engedélyekkel kapcsolatos hibaüzenet jelenik meg: "az objektumazonosító nem rendelkezik engedéllyel a művelet végrehajtásához", győződjön meg arról, hogy jelenleg be van jelentkezve egy olyan szerepkörrel rendelkező felhasználóval, amely jogosult a `Microsoft.Authorization/roleAssignments/write` szerepkörhöz hozzárendelni kívánt hatókörre, például [tulajdonos](built-in-roles.md#owner) vagy [felhasználói hozzáférés rendszergazdai](built-in-roles.md#user-access-administrator) jogosultsággal.
- Ha egy egyszerű szolgáltatásnevet használ a szerepkörök hozzárendeléséhez, akkor előfordulhat, hogy a "nincs megfelelő jogosultsága a művelet végrehajtásához" hibaüzenet jelenik meg. Tegyük fel például, hogy rendelkezik egy olyan egyszerű szolgáltatással, amely hozzá lett rendelve a tulajdonosi szerepkörhöz, és a következő szerepkör-hozzárendelést próbálja meg létrehozni az Azure CLI használatával:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Ha a "nem megfelelő jogosultságok a művelet végrehajtásához" hibaüzenet jelenik meg, akkor valószínű, hogy az Azure CLI megpróbálja megkeresni a megbízott identitást az Azure AD-ben, és az egyszerű szolgáltatás alapértelmezés szerint nem tudja beolvasni az Azure AD-t.

    Ez a hiba kétféleképpen oldható fel. Első lépésként rendelje hozzá a [címtár-olvasók](../active-directory/roles/permissions-reference.md#directory-readers) szerepkört az egyszerű szolgáltatáshoz, hogy az képes legyen az információk olvasására a címtárban.

    A hiba megoldásának második módja, ha a szerepkör-hozzárendelést a (z `--assignee-object-id` ) helyett a paraméter használatával hozza létre `--assignee` . A használatával `--assignee-object-id` Az Azure CLI kihagyja az Azure ad-keresést. Meg kell kérnie annak a felhasználónak, csoportnak vagy alkalmazásnak az AZONOSÍTÓját, amelyhez hozzá szeretné rendelni a szerepkört. További információ: Azure- [szerepkörök kiosztása az Azure CLI használatával](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- Ha megpróbálja eltávolítani az utolsó tulajdonosi szerepkör-hozzárendelést egy előfizetéshez, a következő hibaüzenet jelenhet meg: "nem lehet törölni az utolsó RBAC-rendszergazdai hozzárendelést". Az előfizetés utolsó tulajdonosi szerepkör-hozzárendelésének eltávolítása nem támogatott az előfizetés árva használatának elkerülése érdekében. Ha meg szeretné szüntetni az előfizetését, tekintse [meg az Azure-előfizetés lemondása](../cost-management-billing/manage/cancel-azure-subscription.md)című témakört.

## <a name="problems-with-custom-roles"></a>Problémák az egyéni szerepkörökkel

- Ha az egyéni szerepkörök létrehozásához szükséges lépéseket szeretné látni, tekintse meg az egyéni szerepkör-oktatóanyagokat a [Azure Portal](custom-roles-portal.md) (jelenleg előzetes verzióban elérhető), [Azure PowerShell](tutorial-custom-role-powershell.md)vagy az [Azure CLI](tutorial-custom-role-cli.md)használatával.
- Ha nem tud frissíteni egy meglévő egyéni szerepkört, ellenőrizze, hogy jelenleg be van-e jelentkezve olyan felhasználóval, akinek van olyan szerepköre, amely `Microsoft.Authorization/roleDefinition/write` jogosultsággal rendelkezik, például a [tulajdonos](built-in-roles.md#owner) vagy a [felhasználó hozzáférés-rendszergazdája](built-in-roles.md#user-access-administrator).
- Ha nem sikerül törölni egy egyéni szerepkört, és a „There are existing role assignments referencing role (code: RoleDefinitionHasAssignments” (Szerepkör-hozzárendelések hivatkoznak a szerepkörre (kód: RoleDefinitionHasAssignments)) hibaüzenet jelenik meg, akkor vannak olyan szerepkör-hozzárendelések, amelyek továbbra is az adott egyéni szerepkört használják. Távolítsa el a szóban forgó szerepkör-hozzárendeléseket, majd próbálja meg ismét törölni az egyéni szerepkört.
- Ha egy új egyéni szerepkör létrehozásakor a „Role definition limit exceeded. Nem hozhatók létre több szerepkör-definíció (code: RoleDefinitionLimitExceeded), ha új egyéni szerepkört próbál létrehozni, törölje a nem használt egyéni szerepköröket. Az Azure legfeljebb **5000** egyéni szerepkört támogat egy címtárban. (Az Azure Germany és az Azure China 21Vianet esetében a korlát 2000 egyéni szerepkör.)
- Ha a következőhöz hasonló hibaüzenet jelenik meg: "az ügyfélnek van engedélye a" Microsoft. Authorization/roleDefinitions/Write "művelet végrehajtására a (z)"/Subscriptions/{subscriptionid} "hatókörben, de a csatolt előfizetés nem található" Ha egyéni szerepkört próbál frissíteni, ellenőrizze, hogy egy vagy több [hozzárendelhető hatókör](role-definitions.md#assignablescopes) törölve lett-e a címtárban. Ha a hatókör törölve lett, hozzon létre egy támogatási jegyet, mivel jelenleg nem áll rendelkezésre önkiszolgáló megoldás.

## <a name="custom-roles-and-management-groups"></a>Egyéni szerepkörök és felügyeleti csoportok

- Egyéni szerepkörben csak egyetlen felügyeleti csoportot lehet definiálni `AssignableScopes` . A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
- `DataActions`A felügyeleti csoport hatókörében nem lehet hozzárendelni egyéni szerepköröket.
- Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében.
- Az egyéni szerepkörökkel és felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Előfizetés átadása egy másik címtárba

- Ha az előfizetés másik Azure AD-címtárba való átvitelének lépéseire van szüksége, tekintse meg [Az Azure-előfizetés másik Azure ad-címtárba való átvitelét](transfer-subscription.md)ismertető témakört.
- Ha másik Azure AD-címtárba helyezi át az előfizetést, az összes szerepkör-hozzárendelés **véglegesen** törlődik a forrás Azure ad-címtárból, és nem települ át a cél Azure ad-címtárba. A szerepkör-hozzárendeléseket újra létre kell hoznia a cél könyvtárában. Emellett manuálisan újra létre kell hoznia a felügyelt identitásokat az Azure-erőforrásokhoz. További információ: [Gyakori kérdések és ismert problémák a felügyelt identitásokkal](../active-directory/managed-identities-azure-resources/known-issues.md)kapcsolatban.
- Ha Ön az Azure AD globális rendszergazdája, és nem fér hozzá egy előfizetéshez a címtárak közötti átvitel után, használja az **Azure-erőforrások hozzáférés-vezérlése** kapcsolót, hogy ideiglenesen [emelje](elevate-access-global-admin.md) a hozzáférést az előfizetéshez.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémák a szolgáltatásadminisztrátorokkal vagy társadminisztrátorokkal

- Ha a szolgáltatás-rendszergazdával vagy a közös rendszergazdákkal kapcsolatos problémákat tapasztal, tekintse meg az [Azure-előfizetések rendszergazdáinak hozzáadása vagy módosítása](../cost-management-billing/manage/add-change-subscription-administrator.md) , valamint [a klasszikus előfizetés-rendszergazdai szerepkörök, az Azure-szerepkörök és az Azure ad-szerepkörök](rbac-and-directory-admin-roles.md)

## <a name="access-denied-or-permission-errors"></a>Hozzáférés megtagadva vagy engedélyekkel kapcsolatos hibák

- Ha egy erőforrás létrehozásakor a „The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)” (Az adott objektumazonosítójú ügyfél nem rendelkezik a művelet a kiválasztott hatókörben való végrehajtásához szükséges engedéllyel (kód: AuthorizationFailed)) engedélyekkel kapcsolatos hiba lép fel, ellenőrizze, hogy a felhasználó, amelyikkel bejelentkezett, olyan szerepkörhöz van-e hozzárendelve, amely rendelkezik írási engedéllyel a kiválasztott hatókörben található erőforráshoz. Például az erőforráscsoportban található virtuális gépek kezeléséhez a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkörrel kell rendelkeznie az erőforráscsoportban (vagy a szülő hatókörben). Az egyes beépített szerepkörök engedélyeinek listáját az [Azure beépített szerepkörei](built-in-roles.md)című részben tekintheti meg.
- Ha a támogatási jegy létrehozásakor vagy frissítésekor a "nincs engedélye a támogatási kérelem létrehozására" hibaüzenet jelenik meg, ellenőrizze, hogy jelenleg be van-e jelentkezve olyan felhasználóval, akinek van olyan szerepköre, amely rendelkezik `Microsoft.Support/supportTickets/write` engedéllyel, például [támogatási kérelem közreműködője](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Erőforrások áthelyezése szerepkör-hozzárendelésekkel

Ha olyan erőforrást helyez át, amely közvetlenül az erőforráshoz (vagy egy alárendelt erőforráshoz) van hozzárendelve egy Azure-szerepkörrel, a szerepkör-hozzárendelés nem kerül át, és nem lesz árva. Az áthelyezés után újra létre kell hoznia a szerepkör-hozzárendelést. Végül a rendszer automatikusan eltávolítja az árva szerepkör-hozzárendelést, de ez az ajánlott eljárás a szerepkör-hozzárendelés eltávolítására az erőforrás áthelyezése előtt.

További információ az erőforrások áthelyezéséről: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Nem található az identitással rendelkező szerepkör-hozzárendelések

A Azure Portal szerepkör-hozzárendeléseinek listájában észreveheti, hogy a rendszerbiztonsági tag (felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás) nem található **ismeretlen** típusú **identitásként** .

![Az identitás nem található az Azure szerepkör-hozzárendelésekben](./media/troubleshooting/unknown-security-principal.png)

Előfordulhat, hogy az identitás két okból nem található:

- A közelmúltban meghívott egy felhasználót a szerepkör-hozzárendelés létrehozásakor
- Egy szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag törölve

Ha nemrég meghívott egy felhasználót a szerepkör-hozzárendelés létrehozásakor, akkor a rendszerbiztonsági tag továbbra is a régiók közötti replikálási folyamat során lehet. Ha igen, várjon néhány percet, és frissítse a szerepkör-hozzárendelések listáját.

Ha azonban ez a rendszerbiztonsági tag nem a közelmúltban meghívott felhasználó, akkor lehet, hogy egy törölt rendszerbiztonsági tag. Ha szerepkört rendel egy rendszerbiztonsági tag számára, és később törli a rendszerbiztonsági tag törlését a szerepkör-hozzárendelés eltávolítása nélkül, akkor a rendszerbiztonsági tag **nem található identitásként** jelenik meg, és **ismeretlen** típusú.

Ha Azure PowerShell használatával sorolja fel ezt a szerepkör-hozzárendelést, akkor előfordulhat, hogy egy üres `DisplayName` és egy `ObjectType` **ismeretlen** érték jelenik meg. A [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) például a következő kimenethez hasonló szerepkör-hozzárendelést ad vissza:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Hasonlóképpen, ha az Azure CLI használatával sorolja fel ezt a szerepkör-hozzárendelést, üresen jelenhet meg `principalName` . Az az [szerepkör-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list) például a következő kimenethez hasonló szerepkör-hozzárendelést ad vissza:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Nem jelent problémát, ha el szeretné hagyni ezeket a szerepkör-hozzárendeléseket, ahol a rendszerbiztonsági tag törölve lett. Ha szeretné, ezeket a szerepkör-hozzárendeléseket a többi szerepkör-hozzárendeléshez hasonló lépésekkel távolíthatja el. További információ a szerepkör-hozzárendelések eltávolításáról: [Azure szerepkör-hozzárendelések eltávolítása](role-assignments-remove.md).

Ha a PowerShellben megpróbálja eltávolítani a szerepkör-hozzárendeléseket az objektumazonosító és a szerepkör-definíció neve alapján, és egynél több szerepkör-hozzárendelés felel meg a paramétereknek, a következő hibaüzenet jelenik meg: "a megadott információ nem képezhető le szerepkör-hozzárendelésre". A következő kimenet a hibaüzenet példáját mutatja be:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Ha ez a hibaüzenet jelenik meg, győződjön meg arról, hogy a `-Scope` vagy a paramétereket is megadja `-ResourceGroupName` .

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>A szerepkör-hozzárendelés módosításait a rendszer nem észleli

A Azure Resource Manager időnként a konfiguráció és az adat gyorsítótárazásával javítja a teljesítményt. Szerepkörök hozzárendelése vagy szerepkör-hozzárendelések eltávolítása esetén akár 30 percet is igénybe vehet, amíg a módosítások érvénybe lépnek. Ha a Azure Portal, a Azure PowerShell vagy az Azure CLI-t használja, akkor kijelentkezéssel és bejelentkezéssel kényszerítheti a szerepkör-hozzárendelés módosításának frissítését. Ha REST API-hívásokkal módosítja a szerepkör-hozzárendelési módosításokat, a hozzáférési token frissítésével kényszerítheti a frissítést.

Ha szerepkör-hozzárendelést ad hozzá vagy távolít el a felügyeleti csoport hatókörében, és a szerepkörhöz tartozik `DataActions` , előfordulhat, hogy az adatsíkon lévő hozzáférés több órán át nem frissül. Ez csak a felügyeleti csoport hatókörére és az adatsíkon érvényes.

## <a name="web-app-features-that-require-write-access"></a>Írási hozzáférést igénylő webalkalmazás-funkciók

Ha csak olvasási hozzáférést ad a felhasználónak egyetlen webalkalmazáshoz, néhány szolgáltatás le lesz tiltva, és előfordulhat, hogy nem várható. A következő felügyeleti funkciókhoz **írási** hozzáférés szükséges egy webalkalmazáshoz (közreműködő vagy tulajdonos), és nem érhetők el egyetlen írásvédett forgatókönyvben sem.

* Parancsok (például indítás, Leállítás stb.)
* Beállítások módosítása, például általános konfiguráció, méretezési beállítások, biztonsági mentési beállítások és figyelési beállítások
* A közzétételi hitelesítő adatok és egyéb titkos kulcsok, például az Alkalmazásbeállítások és a kapcsolatok karakterláncok elérése
* Streamelési naplók
* Erőforrás-naplók konfigurálása
* Konzol (parancssor)
* Aktív és közelmúltbeli üzemelő példányok (a helyi git folyamatos üzembe helyezéséhez)
* Becsült ráfordítás
* Webes tesztek
* Virtuális hálózat (csak akkor látható az olvasó számára, ha a virtuális hálózatot korábban írási hozzáféréssel rendelkező felhasználó konfigurálta).

Ha nem fér hozzá ezekhez a csempéhöz, meg kell kérnie a rendszergazdát, hogy a webalkalmazáshoz közreműködői hozzáférést kérjen.

## <a name="web-app-resources-that-require-write-access"></a>Írási hozzáférést igénylő webalkalmazás-erőforrások

A webalkalmazások bonyolultak az egymással való interakciót okozó néhány különböző erőforrás jelenléte mellett. Íme egy tipikus erőforráscsoport néhány webhellyel:

![Webalkalmazás-erőforráscsoport](./media/troubleshooting/website-resource-model.png)

Ennek eredményeképpen, ha hozzáférést ad valakinek a webalkalmazáshoz, a Azure Portal webhely paneljének számos funkciója le van tiltva.

Ezeknek az elemeknek **írási** hozzáférésre van szükségük a webhelyének megfelelő **app Service-csomaghoz** :  

* A webalkalmazás díjszabási szintje (ingyenes vagy standard) megtekintése  
* Méretezési konfiguráció (példányok száma, virtuális gép mérete, automatikus skálázási beállítások)  
* Kvóták (tárolás, sávszélesség, processzor)  

Ezeknek az elemeknek **írási** hozzáférésre van szükségük a webhelyet tartalmazó teljes **erőforráscsoport** számára:  

* TLS/SSL-tanúsítványok és-kötések (a TLS/SSL-tanúsítványok megoszthatók a helyek között ugyanazon az erőforráscsoport és a földrajzi hely között)  
* Riasztási szabályok  
* Az autoskálázás beállításai  
* Application bepillantások összetevői  
* Webes tesztek  

## <a name="virtual-machine-features-that-require-write-access"></a>Írási hozzáférést igénylő virtuálisgép-funkciók

A webalkalmazásokhoz hasonlóan a virtuális gép panel egyes funkcióinak írási hozzáférésre van szükségük a virtuális géphez vagy az erőforráscsoport egyéb erőforrásaihoz.

A virtuális gépek a tartománynevek, a virtuális hálózatok, a tárolási fiókok és a riasztási szabályokhoz kapcsolódnak.

Ezeknek az elemeknek **írási** hozzáférésre van szükségük a **virtuális géphez**:

* Végpontok  
* IP-címek  
* Lemezek  
* Bővítmények  

Ezeknek **írási** hozzáférésre van szükségük a **virtuális géphez** és az **erőforráscsoporthoz** (a tartománynévvel együtt):  

* Rendelkezésre állási csoport  
* Elosztott terhelésű készlet  
* Riasztási szabályok  

Ha nem fér hozzá ezekhez a csempéhöz, kérje meg a rendszergazdát, hogy a közreműködői hozzáférjen az erőforráscsoporthoz.

## <a name="azure-functions-and-write-access"></a>Azure Functions és írási hozzáférés

A [Azure functions](../azure-functions/functions-overview.md) egyes funkcióinak írási hozzáférésre van szükségük. Ha például egy felhasználó hozzá van rendelve az [olvasó](built-in-roles.md#reader) szerepkörhöz, nem fogja tudni megtekinteni a functions alkalmazásban lévő függvényeket. Megjelenik a portál **(nincs hozzáférés)**.

![A Function alkalmazások nem férnek hozzá](./media/troubleshooting/functionapps-noaccess.png)

Egy olvasó rákattinthat a **platform szolgáltatásai** lapra, majd a **minden beállítás** elemre kattintva megtekintheti a Function alkalmazáshoz kapcsolódó beállításokat (a webalkalmazáshoz hasonlóan), de ezek a beállítások nem módosíthatók. A szolgáltatások eléréséhez szüksége lesz a [közreműködő](built-in-roles.md#contributor) szerepkörre.

## <a name="next-steps"></a>Következő lépések

- [A vendég felhasználókkal kapcsolatos hibák](role-assignments-external-users.md#troubleshoot)
- [Azure-szerepkörök kiosztása a Azure Portal használatával](role-assignments-portal.md)
- [Az Azure RBAC változásaival kapcsolatos tevékenységek naplóinak megtekintése](change-history-report.md)

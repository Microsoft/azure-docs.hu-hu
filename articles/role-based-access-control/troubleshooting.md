---
title: Az Azure RBAC hibaelhárítása
description: Az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) kapcsolatos problémák elhárítása.
services: azure-portal
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/06/2021
ms.author: rolyon
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d816854c8d8a78931060c6e56fffbaee1fde5150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771712"
---
# <a name="troubleshoot-azure-rbac"></a>Az Azure RBAC hibaelhárítása

Ez a cikk az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) kapcsolatos gyakori kérdésekre ad választ, hogy tudja, mire számíthat a szerepkörök használata esetén, és elháríthatja a hozzáférési problémákat.

## <a name="azure-role-assignments-limit"></a>Azure-beli szerepkör-hozzárendelések korlátja

Az Azure legfeljebb **2000** szerepkör-hozzárendelést támogat előfizetésenként. Ez a korlát magában foglalja az előfizetés, az erőforráscsoport és az erőforrás-hatókörök szerepkör-hozzárendelését. Ha a "Nem lehet több szerepkör-hozzárendelést létrehozni (kód: RoleAssignmentLimitExceeded)" hibaüzenet jelenik meg, amikor megpróbál hozzárendelni egy szerepkört, próbálja meg csökkenteni a szerepkör-hozzárendelések számát az előfizetésben.

> [!NOTE]
> Az **előfizetésenkénti 2000** szerepkör-hozzárendelési korlát rögzített, és nem növelhető.

Ha közelebb kerül ehhez a korláthoz, az íme néhány módszer a szerepkör-hozzárendelések számának csökkentésére:

- Adjon felhasználókat csoportokhoz, és rendeljen szerepköröket a csoportokhoz. 
- Több beépített szerepkör kombinálható egyéni szerepkörekkel. 
- Általános szerepkör-hozzárendeléseket nagyobb hatókörben, például előfizetésben vagy felügyeleti csoportban is el kell edni.
- Ha p2-prémium szintű Azure AD, a szerepkör-hozzárendeléseket végleges [hozzárendelés](../active-directory/privileged-identity-management/pim-configure.md) helyett Azure AD Privileged Identity Management jogosulttá tegye. 
- Adjon hozzá egy további előfizetést. 

A szerepkör-hozzárendelések számának megtekintéséhez tekintse meg a diagramot a hozzáférés-vezérlés [(IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) lapon a Azure Portal. A következő parancsokat Azure PowerShell is használhatja:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure-beli szerepkör-hozzárendelésekkel kapcsolatos problémák

- Ha nem tud szerepkört hozzárendelni a Azure Portal on **Access Control (IAM)** szolgáltatásban, mert a Szerepkör-hozzárendelés hozzáadása lehetőség le van tiltva, vagy az "Az objektumazonosítóval rendelkező ügyfél nem rendelkezik a művelet végrehajtásához szükséges engedéllyel" engedélyekkel rendelkező hiba jelenik meg, ellenőrizze, hogy jelenleg olyan felhasználóval van-e bejelentkezve, aki olyan szerepkörhöz van hozzárendelve, amely rendelkezik a megfelelő engedéllyel (például Tulajdonos vagy Felhasználói hozzáférés rendszergazdája) a szerepkör hozzárendelésére kijelölt   >   `Microsoft.Authorization/roleAssignments/write` hatókörben. [](built-in-roles.md#owner) [](built-in-roles.md#user-access-administrator)
- Ha szolgáltatásnévvel rendel hozzá szerepköröket, a következő hibaüzenet jelenhet meg: "Nincs megfelelő jogosultsága a művelet végrehajtásához.". Tegyük fel például, hogy rendelkezik egy Tulajdonos szerepkörhöz rendelt szolgáltatásnévvel, és a következő szerepkör-hozzárendelést próbálja létrehozni szolgáltatásnévként az Azure CLI használatával:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Ha a "Nem megfelelő jogosultságok a művelet végrehajtásához" hibaüzenet jelenik meg, annak valószínűleg az az oka, hogy az Azure CLI megpróbálja keresni a hozzárendelt identitást az Azure AD-ben, és a szolgáltatásnév alapértelmezés szerint nem tudja olvasni az Azure AD-t.

    A hiba kétféleképpen oldható meg. Első lépésként rendelje hozzá a [Címtárolvasó](../active-directory/roles/permissions-reference.md#directory-readers) szerepkört a szolgáltatásnévhez, hogy beolvassa a címtárban található adatokat.

    A hiba megoldásának másik módja a szerepkör-hozzárendelés létrehozása a helyett a `--assignee-object-id` paraméter `--assignee` használatával. A használatával `--assignee-object-id` az Azure CLI kihagyja az Azure AD-kereséseket. Le kell szereznie annak a felhasználónak, csoportnak vagy alkalmazásnak az objektumazonosítóját, akihez hozzá szeretné rendelni a szerepkört. További információ: [Azure-szerepkörök hozzárendelése az Azure CLI használatával.](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

- Ha létrehoz egy új szolgáltatásnévt, és azonnal megpróbál hozzárendelni egy szerepkört a szolgáltatásnévhez, a szerepkör-hozzárendelés bizonyos esetekben meghiúsulhat.

    A forgatókönyv megoldásához a tulajdonságot a következőre kell `principalType` `ServicePrincipal` beállítania a szerepkör-hozzárendelés létrehozásakor: . A szerepkör-hozzárendeléshez a `apiVersion` vagy újabb szerepkör-hozzárendelést `2018-09-01-preview` is be kell állítania. További információ: [Azure-szerepkörök](role-assignments-rest.md#new-service-principal) hozzárendelése új szolgáltatásnévhez a REST API vagy Azure-szerepkörök hozzárendelése új szolgáltatásnévhez Azure Resource Manager [sablonokkal](role-assignments-template.md#new-service-principal)

- Ha egy előfizetés utolsó tulajdonosi szerepkör-hozzárendelését próbálja eltávolítani, a következő hibaüzenet jelenhet meg: "Az utolsó RBAC-rendszergazdai hozzárendelés nem törölhető." Az előfizetés utolsó tulajdonosi szerepkör-hozzárendelésének eltávolítása nem támogatott, hogy az előfizetés árva legyen. Ha le szeretné mondani az előfizetését, tekintse meg az [Azure-előfizetés lemondását.](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="problems-with-custom-roles"></a>Problémák az egyéni szerepkörökkel

- Ha egyéni szerepkör létrehozására vonatkozó lépésekre van szüksége, tekintse meg az egyéni szerepkör oktatóanyagokat a [Azure Portal](custom-roles-portal.md), [Azure PowerShell](tutorial-custom-role-powershell.md) [Azure CLI](tutorial-custom-role-cli.md)használatával.
- Ha nem tud frissíteni egy meglévő egyéni szerepkört, ellenőrizze, hogy olyan felhasználóval van-e bejelentkezve, aki olyan szerepkörhöz van hozzárendelve, amely rendelkezik a megfelelő engedéllyel (például Tulajdonos vagy Felhasználói hozzáférés `Microsoft.Authorization/roleDefinition/write` [rendszergazdája).](built-in-roles.md#user-access-administrator) [](built-in-roles.md#owner)
- Ha nem sikerül törölni egy egyéni szerepkört, és a „There are existing role assignments referencing role (code: RoleDefinitionHasAssignments” (Szerepkör-hozzárendelések hivatkoznak a szerepkörre (kód: RoleDefinitionHasAssignments)) hibaüzenet jelenik meg, akkor vannak olyan szerepkör-hozzárendelések, amelyek továbbra is az adott egyéni szerepkört használják. Távolítsa el a szóban forgó szerepkör-hozzárendeléseket, majd próbálja meg ismét törölni az egyéni szerepkört.
- Ha egy új egyéni szerepkör létrehozásakor a „Role definition limit exceeded. Nem lehet több szerepkör-definíciót létrehozni (kód: RoleDefinitionLimitExceeded)" új egyéni szerepkör létrehozásakor törölje a nem használt egyéni szerepköröket. Az Azure legfeljebb **5000 egyéni** szerepkört támogat egy címtárban. (Az Azure Germany és Azure China 21Vianet esetében a korlát 2000 egyéni szerepkör.)
- Ha "Az ügyfélnek van engedélye a Microsoft.Authorization/roleDefinitions/write művelet végrehajtásához a/subscriptions/{subscriptionid} hatókörben, de a csatolt előfizetés nem található", amikor egyéni [](role-definitions.md#assignablescopes) szerepkört próbál frissíteni, ellenőrizze, hogy törölve lett-e egy vagy több hozzárendelhető hatókör a címtárban. Ha a hatókör törölve lett, hozzon létre egy támogatási jegyet, mivel jelenleg nem áll rendelkezésre önkiszolgáló megoldás.

## <a name="custom-roles-and-management-groups"></a>Egyéni szerepkörök és felügyeleti csoportok

- Egy egyéni szerepkörben csak egy felügyeleti csoportot `AssignableScopes` definiálhat. A felügyeleti csoport hozzáadása jelenleg `AssignableScopes` előzetes verzióban érhető el.
- Az egyéni `DataActions` szerepkörei nem rendelhetők hozzá a felügyeleti csoport hatókörében.
- Azure Resource Manager nem ellenőrzi a felügyeleti csoport meglétét a szerepkör-definíció hozzárendelhető hatókörében.
- További információ az egyéni szerepkörökről és felügyeleti csoportokról: [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Előfizetés átvitele másik címtárba

- Ha egy előfizetés másik Azure AD-címtárba való átvitelének lépéseire van szüksége, tekintse meg az Azure-előfizetés másik Azure AD-címtárba történő [átvitelét.](transfer-subscription.md)
- Ha egy előfizetést egy másik Azure AD-címtárba  továbbít, az összes szerepkör-hozzárendelés véglegesen törlődik a forrás Azure AD-címtárból, és nem lesz áttelepítve a cél Azure AD-címtárba. A szerepkör-hozzárendeléseket újra létre kell hoznia a célkönyvtárban. Emellett manuálisan újra létre kell hoznia az Azure-erőforrások felügyelt identitását. További információkért lásd a felügyelt identitásokkal kapcsolatos gyakori kérdéseket és [ismert problémákat.](../active-directory/managed-identities-azure-resources/known-issues.md)
- Ha Ön az Azure AD globális rendszergazdája, és nem rendelkezik hozzáféréssel egy előfizetéshez a könyvtárak közötti átvitel [](elevate-access-global-admin.md) után, az **Azure-erőforrások** hozzáférés-kezelési kapcsolójával átmenetileg megemelheti a hozzáférési jogosultságszintet, hogy hozzáférést kap az előfizetéshez.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémák a szolgáltatásadminisztrátorokkal vagy társadminisztrátorokkal

- Ha problémákat szeretne szolgáltatásgazda vagy társadminisériával kapcsolatban, tekintse meg az Azure-előfizetés-rendszergazdák és a klasszikus előfizetés-rendszergazdai szerepkörök, az Azure-szerepkörök és az [Azure AD-szerepkörök](rbac-and-directory-admin-roles.md)hozzáadását vagy változását. [](../cost-management-billing/manage/add-change-subscription-administrator.md)

## <a name="access-denied-or-permission-errors"></a>Hozzáférés megtagadva vagy engedélyekkel kapcsolatos hibák

- Ha egy erőforrás létrehozásakor a „The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)” (Az adott objektumazonosítójú ügyfél nem rendelkezik a művelet a kiválasztott hatókörben való végrehajtásához szükséges engedéllyel (kód: AuthorizationFailed)) engedélyekkel kapcsolatos hiba lép fel, ellenőrizze, hogy a felhasználó, amelyikkel bejelentkezett, olyan szerepkörhöz van-e hozzárendelve, amely rendelkezik írási engedéllyel a kiválasztott hatókörben található erőforráshoz. Például az erőforráscsoportban található virtuális gépek kezeléséhez a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkörrel kell rendelkeznie az erőforráscsoportban (vagy a szülő hatókörben). Az egyes beépített szerepkörök engedélyeinek listáját lásd: [Beépített Azure-szerepkörök.](built-in-roles.md)
- Ha a támogatási jegy létrehozásakor vagy frissítések frissítésekekor a "Nincs engedélye támogatási kérelem létrehozására" engedélyekkel rendelkező hiba jelenik meg, ellenőrizze, hogy olyan felhasználóval van-e bejelentkezve, aki rendelkezik a megfelelő engedéllyel, például a Támogatási kérelem közreműködője szerepkörhöz van-e `Microsoft.Support/supportTickets/write` rendelve. [](built-in-roles.md#support-request-contributor)

## <a name="move-resources-with-role-assignments"></a>Erőforrások áthelyezése szerepkör-hozzárendelésekkel

Ha olyan erőforrást áthelyez, amely közvetlenül az erőforráshoz (vagy egy gyermekerőforráshoz) rendelt Azure-szerepkört rendelkezik, a szerepkör-hozzárendelés nem lesz áthelyezve, és árva lesz. Az áthelyezés után újra létre kell hoznia a szerepkör-hozzárendelést. Végül az árva szerepkör-hozzárendelés automatikusan el lesz távolítva, de az erőforrás áthelyezése előtt ajánlott eltávolítani a szerepkör-hozzárendelést.

További információ az erőforrások áthelyezésével kapcsolatban: Erőforrások áthelyezése új [erőforráscsoportba vagy előfizetésbe.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="role-assignments-with-identity-not-found"></a>Az identitással nem található szerepkör-hozzárendelések

A szerepkör-hozzárendelések listájában észreveheti, hogy Azure Portal rendszerbiztonsági tag (felhasználó, csoport, szolgáltatásnév vagy  felügyelt identitás)  Identitás nem található ismeretlen típussal.

![Az Identitás nem található az Azure-beli szerepkör-hozzárendelések között](./media/troubleshooting/unknown-security-principal.png)

Előfordulhat, hogy az identitás két okból nem található:

- Nemrég meghívott meg egy felhasználót szerepkör-hozzárendelés létrehozásakor
- Törölt egy szerepkör-hozzárendelési szerepkörhöz hozzárendelt rendszerbiztonsági biztonsági adatokat

Ha nemrégiben meghívott meg egy felhasználót egy szerepkör-hozzárendelés létrehozásakor, előfordulhat, hogy ez a rendszerbiztonsági tag a régiók közötti replikációs folyamatban van. Ha igen, várjon néhány percet, és frissítse a szerepkör-hozzárendelések listáját.

Ha azonban ez a rendszerbiztonsági tag nem a közelmúltban meghívott felhasználó, akkor lehet törölt rendszerbiztonsági tag. Ha szerepkört rendel egy rendszerbiztonsági taghoz, majd később a szerepkör-hozzárendelés eltávolítása nélkül törli  azt, a rendszerbiztonsági tag nem található identitásként és **Ismeretlen** típusúként fog szerepelni.

Ha ezt a szerepkör-hozzárendelést a Azure PowerShell listába sorolja, előfordulhat, hogy megjelenik egy üres és egy `DisplayName` `ObjectType` Ismeretlen **érték.** A [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) például az alábbi kimenethez hasonló szerepkör-hozzárendelést ad vissza:

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

Hasonlóképpen, ha ezt a szerepkör-hozzárendelést az Azure CLI használatával sorolja fel, üres érték is `principalName` megjelenik. Az az [role assignment list](/cli/azure/role/assignment#az_role_assignment_list) például az alábbi kimenethez hasonló szerepkör-hozzárendelést ad vissza:

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

Nem probléma ezeket a szerepkör-hozzárendeléseket olyan esetben hagyni, ahol a rendszerbiztonsági tag törölve lett. Ha szeretné, eltávolíthatja ezeket a szerepkör-hozzárendeléseket a többi szerepkör-hozzárendeléshez hasonló lépésekkel. További információ a szerepkör-hozzárendelések eltávolításáról: [Azure-beli szerepkör-hozzárendelések eltávolítása.](role-assignments-remove.md)

Ha a PowerShellben az objektumazonosító és a szerepkör-definíció nevével próbálja eltávolítani a szerepkör-hozzárendeléseket, és több szerepkör-hozzárendelés is megfelel a paramétereknek, a következő hibaüzenet jelenik meg: "A megadott információk nem biztosítanak szerepkör-hozzárendelést". Az alábbi kimenet egy példa a hibaüzenetre:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Ha ezt a hibaüzenetet kap, adja meg a vagy a `-Scope` `-ResourceGroupName` paramétert is.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>A rendszer nem észleli a szerepkör-hozzárendelés módosításait

Azure Resource Manager egyes esetekben gyorsítótárazza a konfigurációkat és adatokat a teljesítmény javítása érdekében. A szerepkörök hozzárendelése vagy a szerepkör-hozzárendelések eltávolítása akár 30 percet is igénybe vehet, amíg a módosítások életbe lépnek. Ha a szerepkör- Azure Portal, Azure PowerShell vagy az Azure CLI-t használja, kényszerítheti a szerepkör-hozzárendelések módosításainak frissítését kijelentkezve és bejelentkezve. Ha a szerepkör-hozzárendelést a hívásokkal REST API módosítja, kényszerítheti a frissítést a hozzáférési jogkivonat frissítését.

Ha a felügyeleti csoport hatókörében szerepkör-hozzárendelést ad hozzá vagy távolít el, és a szerepkörrel rendelkezik, előfordulhat, hogy az adatsík hozzáférése néhány óráig nem `DataActions` frissül. Ez csak a felügyeleti csoport hatókörre és az adatsíkra vonatkozik.

## <a name="web-app-features-that-require-write-access"></a>A webalkalmazás írási hozzáférést igénylő funkciói

Ha csak olvasási hozzáférést ad egy felhasználónak egy webalkalmazáshoz, egyes funkciók le vannak tiltva, amelyekre valószínűleg nem számít. Az alábbi felügyeleti képességek **írási hozzáférést** igényelnek egy webalkalmazáshoz (közreműködő vagy tulajdonos), és nem érhetők el egyetlen írási forgatókönyvben sem.

* Parancsok (például indítás, leállítás stb.)
* Olyan beállítások módosítása, mint az általános konfiguráció, a méretezési beállítások, a biztonsági mentési beállítások és a figyelési beállítások
* Hozzáférés a közzétételi hitelesítő adatokhoz és más titkos kulcsokhoz, például az alkalmazásbeállításokhoz és a kapcsolati sztringekhoz
* Streamelési naplók
* Erőforrásnaplók konfigurálása
* Konzol (parancssor)
* Aktív és legutóbbi üzemelő példányok (a git helyi folyamatos üzembe helyezéséhez)
* Becsült költségek
* Webes tesztek
* Virtuális hálózat (csak akkor látható az olvasó számára, ha egy felhasználó korábban már konfigurált egy virtuális hálózatot írási hozzáféréssel).

Ha egyik csempéhez sem fér hozzá, kérjen közreműködői hozzáférést a rendszergazdától a webalkalmazáshoz.

## <a name="web-app-resources-that-require-write-access"></a>Írási hozzáférést igénylő webalkalmazás-erőforrások

A webalkalmazások bonyolultak, mert több különböző erőforrás is összejátsz. Példa egy tipikus erőforráscsoport néhány webhelyre:

![Webalkalmazás erőforráscsoportja](./media/troubleshooting/website-resource-model.png)

Ennek eredményeképpen, ha csak a webalkalmazáshoz ad hozzáférést valaki számára, a webhely panelének legtöbb funkciója Azure Portal le van tiltva.

Ezekhez az **elemekhez írási** hozzáférés szükséges **App Service webhelynek** megfelelő csomaghoz:  

* A webalkalmazás tarifacsomagja (ingyenes vagy standard) megtekintése  
* Méretezési konfiguráció (példányok száma, virtuális gép mérete, automatikus méretezési beállítások)  
* Kvóták (tárolás, sávszélesség, CPU)  

Ezekhez az  **elemekhez írási hozzáférés** szükséges a webhelyet tartalmazó teljes erőforráscsoporthoz:  

* TLS/SSL-tanúsítványok és -kötések (A TLS-/SSL-tanúsítványok megoszthatóak az azonos erőforráscsoportban és földrajzi helyen található helyek között)  
* Riasztási szabályok  
* Automatikus méretezési beállítások  
* Application Insights-összetevők  
* Webes tesztek  

## <a name="virtual-machine-features-that-require-write-access"></a>Írási hozzáférést igénylő virtuálisgép-funkciók

A webalkalmazásokhoz hasonlóan a virtuális gép panelének egyes funkcióihoz is írási hozzáférés szükséges a virtuális géphez vagy az erőforráscsoport más erőforrásaihoz.

A virtuális gépek tartománynevekhez, virtuális hálózatokhoz, tárfiókokhoz és riasztási szabályokhoz kapcsolódnak.

Ezekhez az **elemekhez írási hozzáférés szükséges** a **virtuális géphez:**

* Végpontok  
* IP-címek  
* Lemezek  
* Bővítmények  

Ezek **írási hozzáférést** igényelnek a virtuális géphez és az erőforráscsoporthoz **(a** tartománynévvel együtt):  

* Rendelkezésre állási csoport  
* Elosztott terhelésű készlet  
* Riasztási szabályok  

Ha ezen csempék egyikét sem tudja elérni, kérjen közreműködői hozzáférést a rendszergazdától az erőforráscsoporthoz.

## <a name="azure-functions-and-write-access"></a>Azure Functions és írási hozzáférés

A szolgáltatás egyes [funkcióinak Azure Functions](../azure-functions/functions-overview.md) írási hozzáférésre van szüksége. Ha például egy felhasználóhoz [](built-in-roles.md#reader) Olvasó szerepkör van hozzárendelve, nem fogja tudni megtekinteni a függvényeket a függvényalkalmazásban. A portálon **megjelenik a (Nincs hozzáférés) üzenet.**

![A függvényalkalmazások nem férnek hozzá](./media/troubleshooting/functionapps-noaccess.png)

Az olvasó a **Platformfunkciók** lapra, majd a Minden beállítás elemre kattintva megtekintheti a függvényalkalmazással kapcsolatos beállításokat (a webalkalmazáshoz hasonlóan), de ezek közül a beállítások közül egyiket sem módosíthatja.  A funkciók eléréséhez a Közreműködő [szerepkörre lesz](built-in-roles.md#contributor) szüksége.

## <a name="next-steps"></a>Következő lépések

- [Vendégfelhasználók hibaelhárítása](role-assignments-external-users.md#troubleshoot)
- [Azure-szerepkörök hozzárendelése a Azure Portal](role-assignments-portal.md)
- [Tevékenységnaplók megtekintése az Azure RBAC módosításaihoz](change-history-report.md)

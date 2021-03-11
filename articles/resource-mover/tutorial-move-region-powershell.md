---
title: Erőforrások áthelyezése régiók között a PowerShell használatával az Azure-erőforrás-mozgató szolgáltatásban
description: Ismerje meg, hogy miként helyezhetők át erőforrások a régiók között a PowerShell használatával az Azure-beli erőforrás-Mozgatóban.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584346"
---
# <a name="move-resources-across-regions-in-powershell"></a>Erőforrások áthelyezése a régiók között a PowerShellben

Ez a cikk azt ismerteti, hogyan helyezhetők át Azure-erőforrások egy másik Azure-régióba a PowerShell használatával az Azure-beli [erőforrás-mozgató](overview.md)szolgáltatásban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az előfeltételek és a követelmények ellenőrzése.
> * Állítsa be az áthelyezési gyűjteményt.
> * Erőforrások hozzáadása az áthelyezési gyűjteményhez és a függőségek feloldása.
> * A forrás erőforráscsoport előkészítése és áthelyezése. 
> * A többi erőforrás előkészítése és áthelyezése.
> * Döntse el, hogy el kívánja-e dobni vagy véglegesíteni az áthelyezést. 
> * Az áthelyezést követően szükség esetén eltávolíthatja az erőforrásokat a forrás régióban.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb elérési útját mutatják be, és az alapértelmezett beállításokat használják. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/). Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="prerequisites"></a>Előfeltételek
**Követelmény** | **Leírás**
--- | ---
**Előfizetés engedélyei** | Győződjön meg arról, hogy *tulajdonosi* hozzáféréssel rendelkezik az áthelyezni kívánt erőforrásokat tartalmazó előfizetéshez<br/><br/> **Miért van szükség tulajdonosi hozzáférésre?** Amikor először ad hozzá egy erőforrást egy adott forráshoz és célhoz egy Azure-előfizetésben, az erőforrás-mozgató létrehoz egy [rendszerhez rendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén felügyelt szolgáltatás azonosítása (MSI)), amelyet az előfizetés megbízhatónak tekint. Az identitás létrehozásához, valamint a szükséges szerepkör (közreműködő vagy felhasználói hozzáférés rendszergazdája a forrás-előfizetésben) való hozzárendeléséhez az erőforrások hozzáadásához használt fióknak *tulajdonosi* engedélyekkel kell rendelkeznie az előfizetésben. [További](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) információ az Azure-szerepkörökről.
**Erőforrás-mozgató támogatás** | [Tekintse át](common-questions.md) a támogatott régiókat és az egyéb gyakori kérdéseket.
**VM-támogatás** |  Győződjön meg arról, hogy az áthelyezni kívánt virtuális gépek támogatottak.<br/><br/> - [Ellenőrizze](support-matrix-move-region-azure-vm.md#windows-vm-support) a támogatott Windows-alapú virtuális gépeket.<br/><br/> - [Ellenőrizze](support-matrix-move-region-azure-vm.md#linux-vm-support) a támogatott Linux-alapú virtuális gépek és kernel-verziók ellenőrzését.<br/><br/> – A támogatott [számítási](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [tárolási](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)és [hálózati](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) beállítások keresése.
**SQL-támogatás** | Ha az SQL-erőforrásokat át szeretné helyezni, tekintse át az [SQL-követelmények listáját](tutorial-move-region-sql.md#check-sql-requirements).
**Cél-előfizetés** | A célhelyen lévő előfizetésnek elegendő kvótára van szüksége ahhoz, hogy létrehozza a célhelyen áthelyezett erőforrásokat. Ha nem rendelkezik kvótával, [kérjen további korlátozásokat](../azure-resource-manager/management/azure-subscription-service-limits.md).
**A célként megadott régió díjai** | Ellenőrizze, hogy a virtuális gépeket áthelyező cél régióhoz tartozó díjszabást és díjakat kell-e használni. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával segítséget nyújthat.

### <a name="review-powershell-requirements"></a>PowerShell-követelmények áttekintése

A legtöbb áthelyezési erőforrás művelet ugyanaz, mint a Azure Portal vagy a PowerShell használata, néhány kivétellel.

**Művelet** | **PowerShell** | **Portál**
--- | --- | ---
**Áthelyezési gyűjtemény létrehozása** | Az áthelyezési gyűjtemény (az összes áthelyezett erőforrás listája) automatikusan létrejön. A szükséges identitási engedélyek a portálon vannak hozzárendelve a háttérbe. | A PowerShell-parancsmagokkal a következőket végezheti el:<br/><br/> – Hozzon létre egy erőforráscsoportot a mozgatási gyűjteményhez, és határozza meg a helyet.<br/><br/> – Felügyelt identitást rendelhet hozzá a gyűjteményhez.<br/><br/> -Erőforrások hozzáadása a gyűjteményhez.
**Áthelyezési gyűjtemény eltávolítása** | Nem távolíthat el közvetlenül egy áthelyezési gyűjteményt a portálon. | Egy PowerShell-parancsmag használatával távolíthatja el az áthelyezési gyűjteményt.
**Erőforrás-áthelyezési műveletek**<br/><br/> (Felkészülés, áthelyezés kezdeményezése, véglegesítés stb.).| Egyetlen lépés az erőforrás-mozgató automatikus ellenőrzésével. | PowerShell-parancsmagok a következőhöz:<br/><br/> 1.) függőségek ellenőrzése.<br/><br/> 2.) végezze el az áthelyezést.
**Forrás erőforrásainak törlése** | Közvetlenül az erőforrás-mozgató portálon. | Az erőforrás típusú szinten található PowerShell-parancsmagok.


### <a name="sample-values"></a>Mintavételezési értékek

Ezeket az értékeket használjuk a parancsfájl-példákban:

**Beállítás** | **Érték** 
--- | ---
Előfizetés azonosítója | előfizetés-azonosító
Forrásoldali régió |  Az USA középső régiója
Cél régió | USA nyugati középső régiója
Erőforráscsoport (az áthelyezési gyűjtemény metaadatainak tárolásához) | RG-MoveCollection-demoRMS
Gyűjtemény nevének áthelyezése | PS-CentralUS-westcentralus-demoRMS
Erőforráscsoport (forrásoldali régió) | PSDemoRM 
Erőforráscsoport (cél régió) | PSDemoRM – cél
Erőforrás-áthelyezési szolgáltatás helye | USA 2. keleti régiója
IdentityType | SystemAssigned
Áthelyezni kívánt virtuális gép | PSDemoVM


## <a name="sign-into-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a Connect-AzAccount parancsmaggal:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Az áthelyezési gyűjtemény beállítása

A MoveCollection objektum az áthelyezni kívánt erőforrásokra vonatkozó metaadatokat és konfigurációs adatokat tárolja. Az áthelyezési gyűjtemény beállításához tegye a következőket:

- Hozzon létre egy erőforráscsoportot az áthelyezési gyűjteményhez.
- Regisztrálja a szolgáltatót az előfizetésben, hogy létre lehessen hozni a MoveCollection-erőforrást.
- Hozza létre a MoveCollection objektumot a felügyelt identitással. Ahhoz, hogy a MoveCollection objektum hozzáférhessen ahhoz az előfizetéshez, amelyben az erőforrás-mozgató szolgáltatás található, szüksége van egy [rendszerhez rendelt felügyelt identitásra](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén Managed Service Identity (MSI)), amelyet az előfizetés megbízhatónak tekint.
- Hozzáférés biztosítása az erőforrás-mozgató előfizetéshez a felügyelt identitáshoz.

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a gyűjtemény metaadatainak és konfigurációs adatainak áthelyezéséhez az alábbiak szerint:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Kimenet**:

![Az erőforráscsoport létrehozása után kimeneti szöveg](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

1. Regisztrálja a Microsoft. migrált erőforrás-szolgáltatót, hogy a MoveCollection-erőforrást létre lehessen hozni a következőképpen:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>MoveCollection objektum létrehozása

Hozzon létre egy MoveCollection objektumot, és rendeljen hozzá egy felügyelt identitást az alábbiak szerint: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Kimenet**:

![Az áthelyezési gyűjtemény létrehozása után kimeneti szöveg](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Hozzáférés biztosítása a felügyelt identitáshoz

A következőképpen adja meg a felügyelt identitás hozzáférését az erőforrás-mozgató előfizetéshez. Az előfizetés tulajdonosának kell lennie.

1. Azonosító adatok beolvasása a MoveCollection objektumból.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Rendelje hozzá a szükséges szerepköröket az identitáshoz, hogy az Azure-erőforrás-mozgató hozzáférhessen az előfizetéséhez, hogy segítsen az erőforrások áthelyezésében.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Erőforrások hozzáadása az áthelyezési gyűjteményhez

Az áthelyezni kívánt meglévő forrás-erőforrások azonosítóinak beolvasása. Hozza létre a cél erőforrás-beállítási objektumot, majd vegyen fel erőforrásokat az áthelyezési gyűjteménybe.

> [!NOTE]
> Az áthelyezési gyűjteményhez hozzáadott erőforrásoknak ugyanahhoz az előfizetéshez kell tartoznia, de különböző erőforráscsoportok is lehetnek.

Adja hozzá az erőforrásokat az alábbiak szerint:

1. A forrás erőforrás-azonosító lekérése:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Kimenet**

    ![Kimeneti szöveg az erőforrás-azonosító lekérése után](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Hozza létre a cél erőforrás-beállítási objektumot az áthelyezett erőforrásnak megfelelően. Ebben az esetben ez egy virtuális gép.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Állítsa be az erőforrás típusát és a cél erőforrás nevét az objektumhoz.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > A célként megadott virtuális gép neve megegyezik a forrás régiójában található virtuális géppel. Választhat másik nevet is.

4. Adja hozzá a forrás erőforrásait az áthelyezési gyűjteményhez a beolvasott/létrehozott erőforrás-azonosító és Target Settings objektum használatával.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Kimenet** ![ Az erőforrás hozzáadása utáni kimeneti szöveg](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Függőségek ellenőrzése és hozzáadása

Győződjön meg arról, hogy a hozzáadott erőforrások függőségei vannak-e más erőforrásokon, és szükség esetén adja hozzá őket. 

1. A függőségek érvényesítése az alábbiak szerint történik:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Kimenet (ha függőségek léteznek)**

    ![A függőségek ellenőrzése után kimeneti szöveg](./media/tutorial-move-region-powershell/dependency-output.png)

2. Hiányzó identitás függőségei:

    - A hiányzó függőségek listájának beolvasása:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Kimenet** ![ Az összes függőség listájának beolvasása után kimeneti szöveg](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Csak az első szintű függőségek lekérése (az erőforrás közvetlen függőségei):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Kimenet** ![ Kimeneti szöveg az első szintű függőségek listájának beolvasása után](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. A függőben lévő Hiányzó függőségek hozzáadásához ismételje meg a fenti utasításokat, és [adja hozzá az erőforrásokat az áthelyezési gyűjteményhez](#add-resources-to-the-move-collection), és ellenőrizze, hogy nincs-e függőben lévő erőforrás.

> [!NOTE]
> Ha bármilyen okból el szeretné távolítani az erőforrásokat az erőforrás-gyűjteményből, kövesse a [cikk](remove-move-resources.md)utasításait.

## <a name="add-the-source-resource-group"></a>A forrás erőforráscsoport hozzáadása

Adja hozzá az áthelyezési gyűjteménybe áthelyezni kívánt erőforrásokat tartalmazó forrásoldali erőforráscsoportot.

1. Az erőforráscsoport AZONOSÍTÓjának beolvasása.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Kimenet** ![ A forrás erőforráscsoport AZONOSÍTÓjának lekérése után kimeneti szöveg](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Olyan erőforráscsoportot használunk, amely már szerepel a célként megadott régióban.

 
2. A beolvasott azonosító használatával adja hozzá az erőforráscsoportot a gyűjteményhez.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Kimenet** ![ Kimeneti szöveg, miután hozzáadta a forrás erőforráscsoportot az áthelyezési gyűjteményhez.](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Ellenőrizze a függőségeket, és győződjön meg arról, hogy az erőforráscsoport hozzáadása után semmit sem hagyott el.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Láthatjuk, hogy nincsenek függőben lévő függőségek.

    **Kimenet** ![ A függőségek ellenőrzése utáni kimeneti szöveg](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Erőforrások előkészítése

Általában az áthelyezés előtt elő kell készítenie az erőforrásokat a forrás régióban. Például:

- Az állapot nélküli erőforrások, például az Azure-beli virtuális hálózatok, a hálózati adapterek, a terheléselosztó és a hálózati biztonsági csoportok áthelyezéséhez szükség lehet egy Azure Resource Manager sablon exportálására.
- Az állapot-nyilvántartó erőforrások, például az Azure-beli virtuális gépek és az SQL-adatbázisok áthelyezéséhez előfordulhat, hogy meg kell kezdenie az erőforrások replikálását a forrásról a rendeltetési régióba.

Ebben az oktatóanyagban, mivel a virtuális gépeket áthelyezjük, elő kell készíteni a forrás erőforráscsoportot, majd a virtuális gépek előkészítése előtt el kell indítani és véglegesíteni kell az áthelyezést.

> [!NOTE]
> Ha van egy meglévő célként megadott erőforráscsoport, közvetlenül véglegesítheti a forrás erőforráscsoport áthelyezését, és kihagyhatja az előkészítési és kezdeményezési áthelyezési fázisokat.

  
### <a name="prepare-the-source-resource-group"></a>A forrásoldali erőforráscsoport előkészítése:

1. Az erőforráscsoport előkészítése:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Kimenet**

    ![Kimeneti szöveg a forrás erőforráscsoport előkészítése után](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Indítsa el a forrás erőforráscsoport áthelyezését.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![A forrás-erőforráscsoport áthelyezésének megkezdése után kimeneti szöveg](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Véglegesítse a forrás erőforráscsoport áthelyezését.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Kimenet**

    ![Kimeneti szöveg a forrásoldali erőforráscsoport véglegesítése után](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>VM-erőforrások előkészítése

A forrás-erőforráscsoport előkészítését és áthelyezését követően előkészítheti a virtuális gépek erőforrásait az áthelyezéshez.

1. A virtuális gépek erőforrásainak előkészítése előtt érvényesítse a függőségeket.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Kimenet**

    ![A virtuális gép a előkészítése előtt történő érvényesítése után kimeneti szöveg](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Szerezze be azokat a függő erőforrásokat, amelyeket elő kell készíteni a virtuális géppel együtt.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Kimenet**

    ![A függő virtuálisgép-erőforrások lekérése után kimeneti szöveg](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Indítsa el az előkészítési folyamatot az összes függő erőforráshoz.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Kimenet**

    ![Kimeneti szöveg az összes erőforrás előkészítése után initating](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > Az erőforrás neve helyett megadhatja a forrás erőforrás-azonosítót az előkészítési parancsmag bemeneti paramétereként, valamint az áthelyezés és a commit parancsmagok megadásával. Ehhez futtassa a következőt:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>VM-erőforrások áthelyezésének kezdeményezése

1. Ellenőrizze, hogy a virtuálisgép-erőforrások *kezdeményezett áthelyezés függőben* állapotú-e:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Kimenet**

    ![A beavatási állapot ellenőrzése után kimeneti szöveg](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Az áthelyezés kezdeményezése:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Kimenet**

    ![Kimeneti szöveg az erőforrások áthelyezésének megkezdése után](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Elveti vagy véglegesíti?

A kezdeti áthelyezés után eldöntheti, hogy véglegesíteni kívánja-e az áthelyezést, vagy elveti azt. 

- **Elvetés**: Ha teszteli a tesztet, elkerülheti az áthelyezést, és nem szeretné ténylegesen áthelyezni a forrás erőforrást. Az áthelyezés elvetése visszaadja az erőforrást a *függőben lévő áthelyezés indításának* állapotára. Ezután kezdeményezheti az áthelyezést, ha szükséges.
- **Véglegesítés**: a véglegesítés befejezi az áthelyezést a célként megadott régióba. A véglegesítést követően a forrás erőforrás a *delete Source függőben* állapotba kerül, és eldöntheti, hogy szeretné-e törölni.

### <a name="discard-the-move"></a>Áthelyezés elvetése

Az áthelyezés elvetéséhez:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Kimenet**

![Kimeneti szöveg az áthelyezés elvetése után](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Az áthelyezés elkövetése

1. Véglegesítse az áthelyezést a következőképpen:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Kimenet**

    ![Kimeneti szöveg az áthelyezés véglegesítése után](./media/tutorial-move-region-powershell/commit-move.png)

2. Győződjön meg arról, hogy az összes erőforrás át lett helyezve a célként megadott régióba:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Az összes erőforrás mostantól *függőben* van a célként megadott régióban.

## <a name="delete-source-resources"></a>Forrás erőforrásainak törlése

Miután véglegesíti az áthelyezést, és ellenőrzi, hogy az erőforrások a megcélzott régióban megfelelően működnek-e, törölheti az egyes forrás-erőforrásokat a [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)a PowerShell vagy az [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources) [használatával](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources).

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure-beli virtuális gépeket egy másik Azure-régióba helyezte át a PowerShell használatával
> * Virtuális gépekhez társított erőforrások áthelyezése egy másik régióba.

Most, hogy az Azure-beli virtuális gépeket a portál használatával szeretné áthelyezni

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek áthelyezése a portálon](./tutorial-move-region-virtual-machines.md)



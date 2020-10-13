---
title: Erőforrások eltávolítása egy áthelyezési gyűjteményből az Azure-erőforrás-Mozgatóban
description: Ismerje meg, hogyan távolíthat el erőforrásokat egy áthelyezési gyűjteményből az Azure-erőforrás-Mozgatóban.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 38a633a7a11ac29271231679e7075920e1f33a70
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945943"
---
# <a name="manage-move-collections-and-resource-groups"></a>Gyűjtemények és erőforráscsoportok kezelése

Ez a cikk azt ismerteti, hogyan távolíthat el erőforrásokat egy áthelyezési gyűjteményből, illetve hogyan távolíthat el egy áthelyezési gyűjteményt vagy erőforráscsoportot az Azure-beli [erőforrás-mozgatóban](overview.md). A gyűjtemények áthelyezése az Azure-erőforrások Azure-régiók közötti áthelyezéséhez használatos.

## <a name="remove-a-resource-portal"></a>Erőforrás eltávolítása (portál)

Az áthelyezési gyűjtemény erőforrásai a következőképpen távolíthatók el az erőforrás-mozgató portálon:

1. Az **egyes régiókban**válassza ki az összes olyan erőforrást, amelyet el szeretne távolítani a gyűjteményből, majd válassza az **Eltávolítás**lehetőséget. 

    ![Eltávolításra kijelölt gomb](./media/remove-move-resources/portal-select-resources.png)

2. Az **erőforrások eltávolítása**területen kattintson az **Eltávolítás**gombra.

    ![Az erőforrások áthelyezési gyűjteményből való eltávolításának kiválasztására szolgáló gomb](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Áthelyezési gyűjtemény/erőforráscsoport (portál) eltávolítása

A portálon törölheti az áthelyezési gyűjteményt/erőforráscsoportot.

1. Az erőforrások gyűjteményből való eltávolításához kövesse a fenti eljárás utasításait. Ha eltávolít egy erőforráscsoportot, győződjön meg róla, hogy nem tartalmaz erőforrást.
2. Törölje az áthelyezési gyűjteményt vagy az erőforráscsoportot.  

## <a name="remove-a-resource-powershell"></a>Erőforrás eltávolítása (PowerShell)

Távolítson el egy olyan erőforrást (példánkban a PSDemoVM-gépeket) egy gyűjteményből a PowerShell használatával, a következőképpen:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**Várt kimenet** 
 ![ Kimeneti szöveg az erőforrás áthelyezési gyűjteményből való eltávolítása után](./media/remove-move-resources/remove-resource.png)



## <a name="remove-a-collection-powershell"></a>Gyűjtemény eltávolítása (PowerShell)

Távolítson el egy teljes áthelyezési gyűjteményt a PowerShell használatával, a következőképpen:

1. Kövesse a fenti utasításokat a gyűjtemény erőforrásainak a PowerShell használatával történő eltávolításához.
2. Futtassa a következőt:

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
    ```
    **Várt kimenet** ![ Egy áthelyezési gyűjtemény eltávolítását követően kimeneti szöveg](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>VM-erőforrás állapota eltávolítás után

Mi történik, ha eltávolít egy virtuálisgép-erőforrást egy áthelyezési gyűjteményből, amely az erőforrás állapotától függ, ahogy az a táblázatban szerepel.

###  <a name="remove-vm-state"></a>Virtuális gép állapotának eltávolítása
**Erőforrás állapota** | **VM** | **Hálózat**
--- | --- | --- 
**Hozzáadás a gyűjtemény áthelyezéséhez** | Törlés az áthelyezési gyűjteményből. | Törlés az áthelyezési gyűjteményből. 
**Függőségek feloldva/felkészülés folyamatban** | Törlés az áthelyezési gyűjteményből  | Törlés az áthelyezési gyűjteményből. 
**Előkészítés folyamatban**<br/> (vagy bármely más állapot folyamatban van) | A törlési művelet hibával meghiúsul.  | A törlési művelet hibával meghiúsul.
**Sikertelen előkészítés** | Törlés az áthelyezési gyűjteményből.<br/>Törölje a megcélzott régióban létrehozott bármit, beleértve a replika lemezeket is. <br/><br/> Az áthelyezés során létrehozott infrastruktúra-erőforrásokat manuálisan kell törölni. | Törlés az áthelyezési gyűjteményből.  
**Áthelyezés folyamatban** | Törlés az áthelyezési gyűjteményből.<br/><br/> Törölje a megcélzott régióban létrehozott bármit, beleértve a virtuális gépet, a replika lemezeket stb.  <br/><br/> Az áthelyezés során létrehozott infrastruktúra-erőforrásokat manuálisan kell törölni. | Törlés az áthelyezési gyűjteményből.
**Az áthelyezés kezdeményezése nem sikerült** | Törlés az áthelyezési gyűjteményből.<br/><br/> Törölje a megcélzott régióban létrehozott bármit, beleértve a virtuális gépet, a replika lemezeket stb.  <br/><br/> Az áthelyezés során létrehozott infrastruktúra-erőforrásokat manuálisan kell törölni. | Törlés az áthelyezési gyűjteményből.
**Véglegesítés függőben** | Javasoljuk, hogy vesse el a mozgatást úgy, hogy először törölje a cél erőforrásait.<br/><br/> Az erőforrás visszatér a **kezdeményezett áthelyezés függő** állapotba, és onnan folytathatja. | Javasoljuk, hogy vesse el a mozgatást úgy, hogy először törölje a cél erőforrásait.<br/><br/> Az erőforrás visszatér a **kezdeményezett áthelyezés függő** állapotba, és onnan folytathatja. 
**A véglegesítés sikertelen** | Javasoljuk, hogy dobja el a cél-erőforrások törlésének első lépéseit.<br/><br/> Az erőforrás visszatér a **kezdeményezett áthelyezés függő** állapotba, és onnan folytathatja. | Javasoljuk, hogy vesse el a mozgatást úgy, hogy először törölje a cél erőforrásait.<br/><br/> Az erőforrás visszatér a **kezdeményezett áthelyezés függő** állapotba, és onnan folytathatja.
**Elvetés befejezve** | Az erőforrás visszatér a **függő áthelyezés folyamatban** állapotba.<br/><br/> A rendszer törli az áthelyezési gyűjteményből, valamint a cél – virtuális gép, a replika lemezek, a tároló stb. alapján létrehozott bármit.  <br/><br/> Az áthelyezés során létrehozott infrastruktúra-erőforrásokat manuálisan kell törölni. <br/><br/> Az áthelyezés során létrehozott infrastruktúra-erőforrásokat manuálisan kell törölni. |  Az erőforrás visszatér a **függő áthelyezés folyamatban** állapotba.<br/><br/> Törölve lett az áthelyezési gyűjteményből.
**Sikertelen elvetés** | Javasoljuk, hogy dobja el a lépéseket, hogy a rendszer először törölje a cél erőforrásait.<br/><br/> Ezután az erőforrás visszatér a **függőben lévő áthelyezés kezdeményezése** állapotba, és folytathatja. | Javasoljuk, hogy dobja el a lépéseket, hogy a rendszer először törölje a cél erőforrásait.<br/><br/> Ezután az erőforrás visszatér a **függőben lévő áthelyezés kezdeményezése** állapotba, és folytathatja.
**Forrás törlése függőben** | Törölve az áthelyezési gyűjteményből.<br/><br/> Nem törli a megcélzott régióban létrehozott semmit.  | Törölve az áthelyezési gyűjteményből.<br/><br/> Nem törli a megcélzott régióban létrehozott semmit.
**Nem sikerült törölni a forrást** | Törölve az áthelyezési gyűjteményből.<br/><br/> Nem törli a megcélzott régióban létrehozott semmit. | Törölve az áthelyezési gyűjteményből.<br/><br/> Nem törli a megcélzott régióban létrehozott semmit.

## <a name="sql-resource-state-after-removing"></a>SQL-erőforrás állapota eltávolítás után

Mi történik, ha egy áthelyezési gyűjteményből eltávolít egy Azure SQL-erőforrást, az erőforrás állapotától függ, ahogy az a táblázatban szerepel.

**Erőforrás állapota** | **SQL** 
--- | --- 
**Hozzáadás a gyűjtemény áthelyezéséhez** | Törlés az áthelyezési gyűjteményből. 
**Függőségek feloldva/felkészülés folyamatban** | Törlés az áthelyezési gyűjteményből 
**Előkészítés folyamatban**<br/> (vagy bármely más állapot folyamatban van)  | A törlési művelet hibával meghiúsul. 
**Sikertelen előkészítés** | Törlés az áthelyezési gyűjteményből<br/><br/>Törölje a megcélzott régióban létrehozott bármit. 
**Áthelyezés folyamatban** |  Törlés az áthelyezési gyűjteményből<br/><br/>Törölje a megcélzott régióban létrehozott bármit. Az SQL-adatbázis már létezik, és törölve lesz. 
**Az áthelyezés kezdeményezése nem sikerült** | Törlés az áthelyezési gyűjteményből<br/><br/>Törölje a megcélzott régióban létrehozott bármit. Az SQL-adatbázis ezen a ponton létezik, és törölni kell. 
**Véglegesítés függőben** | Javasoljuk, hogy vesse el a mozgatást úgy, hogy először törölje a cél erőforrásait.<br/><br/> Az erőforrás visszatér a **kezdeményezett áthelyezés függő** állapotba, és onnan folytathatja.
**A véglegesítés sikertelen** | Javasoljuk, hogy vesse el a mozgatást úgy, hogy először törölje a cél erőforrásait.<br/><br/> Az erőforrás visszatér a **kezdeményezett áthelyezés függő** állapotba, és onnan folytathatja. 
**Elvetés befejezve** |  Az erőforrás visszatér a **függő áthelyezés folyamatban** állapotba.<br/><br/> A rendszer törli az áthelyezési gyűjteményből, valamint a TARGET-ben létrehozott bármit, beleértve az SQL-adatbázisokat is. 
**Sikertelen elvetés** | Javasoljuk, hogy dobja el a lépéseket, hogy a rendszer először törölje a cél erőforrásait.<br/><br/> Ezután az erőforrás visszatér a **függőben lévő áthelyezés kezdeményezése** állapotba, és folytathatja. 
**Forrás törlése függőben** | Törölve az áthelyezési gyűjteményből.<br/><br/> Nem törli a megcélzott régióban létrehozott semmit. 
**Nem sikerült törölni a forrást** | Törölve az áthelyezési gyűjteményből.<br/><br/> Nem törli a megcélzott régióban létrehozott semmit. 

## <a name="next-steps"></a>További lépések

[Helyezzen át egy virtuális gépet](tutorial-move-region-virtual-machines.md) egy másik régióba az erőforrás-mozgató használatával.
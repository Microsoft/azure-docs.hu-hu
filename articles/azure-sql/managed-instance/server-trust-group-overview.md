---
title: Kiszolgálói megbízhatósági csoport
titleSuffix: Azure SQL Managed Instance
description: További információ a kiszolgálói megbízhatósági csoportról és az Azure SQL felügyelt példányai közötti megbízhatósági kapcsolat kezeléséről.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790729"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>Az SQL felügyelt példányai közötti megbízhatóság beállítása és kezelése a kiszolgálói megbízhatósági csoportok használatával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A kiszolgálói megbízhatósági csoport egy, az Azure SQL felügyelt példányai közötti megbízhatóság kezelésére szolgáló fogalom. Egy csoport létrehozásával tanúsítványalapú megbízhatósági kapcsolat jön létre a tagjai között. Ezt a megbízhatóságot különböző, több példányra kiterjedő forgatókönyvek esetében lehet használni. A kiszolgálók eltávolítása a csoportból vagy a csoport törlése eltávolítja a kiszolgálók közötti bizalmi kapcsolatot. A kiszolgálói megbízhatósági csoport létrehozásához vagy törléséhez a felhasználónak írási engedéllyel kell rendelkeznie a felügyelt példányon.
A [kiszolgálói megbízhatósági csoport](/azure/templates/microsoft.sql/allversions) egy Azure Resource Manager objektum, amely Azure Portal SQL- **megbízhatósági csoportként** van megjelölve.

> [!NOTE]
> A kiszolgálói megbízhatósági csoport nyilvános előzetes verzióban érhető el az Azure SQL felügyelt példányai közötti elosztott tranzakciók számára, és jelenleg bizonyos korlátozások vonatkoznak rá a cikk későbbi szakaszaiban.

## <a name="server-trust-group-setup"></a>Kiszolgáló megbízhatósági csoportjának beállítása

A következő szakasz a kiszolgálói megbízhatósági csoportok telepítését ismerteti.

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).

2. Navigáljon a felügyelt Azure SQL-példányhoz, amelyet hozzá szeretne adni egy újonnan létrehozott kiszolgálói megbízhatósági csoporthoz.

3. A **biztonsági** beállítások lapon válassza az **SQL-megbízhatósági csoportok** lapot.

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Kiszolgálói megbízhatósági csoportok":::

4. A kiszolgálói megbízhatósági csoport konfigurációja lapon válassza az **új csoport** ikont.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Új csoport":::

5. Az **SQL-megbízhatósági csoport** létrehozás paneljén állítsa be a **csoport nevét**. Egyedinek kell lennie minden olyan régióban, ahol a csoporttagok találhatók. A **megbízhatósági hatókör** határozza meg a kiszolgálói megbízhatósági csoporttal engedélyezett, több példányra kiterjedő forgatókönyv típusát. Az előzetes verzióban az csak a megfelelő megbízhatósági hatókör az **Elosztott tranzakciók**, ezért az előzetesen kijelölt és nem módosítható. Az összes **csoport tagjának** ugyanahhoz az **előfizetéshez** kell tartoznia, de különböző erőforráscsoportok is lehetnek. Válassza ki az **erőforráscsoportot** és **SQL Server/példányt** , és válassza ki azt az Azure SQL felügyelt példányt, amely tagja lesz a csoportnak.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Kiszolgáló megbízhatósági csoport létrehozása panel":::

6. Az összes kötelező mező kitöltése után kattintson a **Mentés** gombra.

## <a name="server-trust-group-maintenance-and-deletion"></a>Kiszolgáló megbízhatósági csoportjának karbantartása és törlése

A kiszolgálói megbízhatósági csoport nem szerkeszthető. A felügyelt példányok csoportból való eltávolításához törölnie kell a csoportot, és létre kell hoznia egy újat.

A következő szakasz a kiszolgáló megbízhatósági csoportjának törlési folyamatát ismerteti. 
1. Nyissa meg az Azure Portalt.
2. Navigáljon egy olyan felügyelt példányhoz, amely a megbízhatósági csoporthoz tartozik.
3. A **biztonsági** beállítások lapon válassza az **SQL-megbízhatósági csoportok** lapot.
4. Válassza ki a törölni kívánt megbízhatósági csoportot.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Kiszolgáló megbízhatósági csoportjának kiválasztása":::
5. Kattintson a **csoport törlése** elemre.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Kiszolgáló megbízhatósági csoportjának törlése":::
6. A törlés megerősítéséhez írja be a kiszolgáló megbízhatósági csoportjának nevét, majd kattintson a **Törlés** gombra.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Kiszolgáló-megbízhatósági csoport törlésének megerősítése":::

> [!NOTE]
> Előfordulhat, hogy a kiszolgáló megbízhatósági csoportjának törlése nem távolítja el azonnal a két felügyelt példány közötti megbízhatósági kapcsolatot. A megbízhatóság eltávolítása a felügyelt példányok [feladatátvételének](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) meghívásával kényszeríthető ki. Vizsgálja meg a legújabb frissítések [ismert problémáit](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues) .

## <a name="limitations"></a>Korlátozások

A nyilvános előzetes verzióban a következő korlátozások vonatkoznak a kiszolgálói megbízhatósági csoportokra.
 * A kiszolgálói megbízhatósági csoport nevének minden olyan régióban egyedinek kell lennie, ahol a tagjai.
 * A csoport csak az Azure SQL felügyelt példányait tartalmazhatja, és ugyanahhoz az Azure-előfizetéshez kell tartoznia.
 * Az előzetes verzióban a csoportnak pontosan két felügyelt példánya lehet. Az elosztott tranzakciók több mint két felügyelt példányon történő végrehajtásához létre kell hoznia egy kiszolgálói megbízhatósági csoportot a felügyelt példányok mindegyik párja számára.
 * Az elosztott tranzakciók az egyetlen érvényes hatókör a kiszolgálói megbízhatósági csoportok számára.
 * A kiszolgálói megbízhatósági csoport csak Azure Portal felügyelhető. A PowerShell és a CLI támogatása később is megtörténik.
 * A kiszolgálói megbízhatósági csoport nem szerkeszthető a Azure Portalon. Csak lehet létrehozni vagy eldobni.
 * Az elosztott tranzakciók további korlátai lehetnek a forgatókönyvhöz kapcsolódóan. A legjelentősebb az, hogy a felügyelt példányok között kapcsolattal kell rendelkeznie a privát végpontokon keresztül a VNET vagy a VNET-alapú közvetítéssel. Győződjön meg arról, hogy ismeri a [felügyelt példányok aktuális elosztott tranzakcióinak korlátozásait](../database/elastic-transactions-overview.md#limitations).

## <a name="next-steps"></a>Következő lépések

* További információ az Azure SQL felügyelt példányain található elosztott tranzakciókról: [Elosztott tranzakciók](../database/elastic-transactions-overview.md).
* A kiadási frissítések és az ismert problémák állapota: a [felügyelt példány kibocsátási megjegyzései](../database/doc-changes-updates-release-notes.md).
* Ha szolgáltatási kérelmekkel rendelkezik, adja hozzá őket a [felügyelt példányok fórumához](https://feedback.azure.com/forums/915676-sql-managed-instance).
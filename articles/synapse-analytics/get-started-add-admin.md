---
title: 'Oktatóanyag: a rendszergazda hozzáadásának első lépései'
description: Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá egy másik rendszergazda felhasználót a munkaterülethez.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553498"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Rendszergazda hozzáadása a szinapszis-munkaterülethez

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá egy rendszergazdát a szinapszis-munkaterülethez. Ez a felhasználó a munkaterület teljes hozzáférését fogja tartalmazni.

## <a name="overview"></a>Áttekintés

Az első lépések útmutatóban eddig a munkaterületen végzett tevékenységekre  összpontosítunk. Mivel a munkaterületet az 1. lépésben hozta létre, Ön a szinapszis munkaterület rendszergazdája. Most egy másik felhasználó Ryan () felhasználót teszünk elérhetővé `ryan@contoso.com` a rendszergazdának. Ha elkészült, Ryan mindent megtesz, amit a munkaterületen végezhet.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: a munkaterület tulajdonosi szerepköre

Hozzárendelés az `ryan@contoso.com` Azure RBAC **tulajdonosi** szerepköréhez a munkaterületen.

1. Nyissa meg a Azure Portal, és nyissa meg a szinapszis munkaterületet.
1. A bal oldalon válassza a **Access Control (iam)** lehetőséget.
1. Hozzáadás `ryan@contoso.com` a **tulajdonosi** szerepkörhöz. 
1. Kattintson a **Mentés** gombra.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Szinapszis RBAC: szinapszis rendszergazdai szerepkör a munkaterülethez

Hozzárendelés a következőhöz: `ryan@contoso.com` SZINAPSZIS RBAC **szinapszis rendszergazdai** szerepkör a munkaterületen.

1. Nyissa meg a munkaterületet a szinapszis Studióban.
1. A bal oldalon kattintson a **kezelés** elemre a kezelés központ megnyitásához.
1. A **Biztonság** alatt kattintson a **hozzáférés-vezérlés** elemre.
1. Kattintson a **Hozzáadás** parancsra.
1. Hagyja meg a **hatókört** a **munkaterületre**.
1. Adja hozzá `ryan@contoso.com` a **szinapszis rendszergazdai** szerepkört. 
1. Ezután kattintson az **alkalmaz** gombra.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: szerepkör-hozzárendelések az elsődleges Storage-fiókban

Hozzárendelés a `ryan@contoso.com` **tulajdonosi** szerepkörhöz a munkaterület elsődleges Storage-fiókjában.
Rendeljen `ryan@contoso.com` hozzá **Azure Storage blob adatközreműködői** szerepkört a munkaterület elsődleges Storage-fiókjához.

1. Nyissa meg a munkaterület elsődleges Storage-fiókját a Azure Portal.
1. A bal oldalon kattintson a **Access Control (iam)** elemre.
1. Hozzáadás `ryan@contoso.com` a **tulajdonosi** szerepkörhöz. 
1. Hozzáadás `ryan@contoso.com` a **Azure Storage blob adatközreműködői** szerepkörhöz

## <a name="dedicated-sql-pools-db_owner-role"></a>Dedikált SQL-készletek: db_owner szerepkör

Rendelje hozzá `ryan@contoso.com` a **db_owner** a munkaterületen található összes dedikált SQL-készlethez.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Következő lépések

* [Az Azure Synapse Analytics első lépései](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)

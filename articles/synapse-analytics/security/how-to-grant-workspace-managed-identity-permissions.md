---
title: Engedélyek megadása a felügyelt identitásnak a szinapszis munkaterületen
description: Ez a cikk bemutatja, hogyan konfigurálhatja a felügyelt identitás engedélyeit az Azure szinapszis munkaterületen.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459013"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Engedélyek megadása munkaterület által felügyelt identitásnak

Ez a cikk bemutatja, hogyan adhat engedélyeket a felügyelt identitásnak az Azure szinapszis munkaterületen. Az engedélyek lehetővé teszik a dedikált SQL-készletek elérését a munkaterületen, és a Azure Portal keresztül ADLS Gen2 Storage-fiókot.

>[!NOTE]
>Ezt a munkaterület felügyelt identitását felügyelt identitásnak nevezzük a dokumentum többi részén.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Felügyelt identitás engedélyeinek megadása a dedikált SQL-készlethez

A felügyelt identitás engedélyeket biztosít a dedikált SQL-készletekhez a munkaterületen. A megadott engedélyekkel olyan folyamatokat állíthat össze, amelyek dedikált SQL-készlettel kapcsolatos tevékenységeket végeznek. Amikor Azure Portal használatával hoz létre Azure szinapszis-munkaterületet, megadhatja a felügyelt identitás-VEZÉRLÉSi engedélyeket a dedikált SQL-készleteken.

Az Azure szinapszis-munkaterület létrehozásakor válassza a **Biztonság** lehetőséget. Ezután válassza **az SQL-készletek elérésének engedélyezése (a munkaterület rendszerhez rendelt identitása) lehetőséget.**

![VEZÉRLÉSi engedély dedikált SQL-készleteken](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Felügyelt identitás engedélyeinek megadása ADLS Gen2 Storage-fiókhoz

Azure szinapszis-munkaterület létrehozásához ADLS Gen2 Storage-fiók szükséges. A Spark-készletek Azure szinapszis-munkaterületen való sikeres indításához az Azure szinapszis felügyelt identitásának szüksége van a *Storage blob adatközreműködői* szerepkörre ezen a Storage-fiókon. Az Azure szinapszis folyamat-előkészítési funkciói szintén ebből a szerepkörből származnak.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Engedélyek megadása a felügyelt identitásnak a munkaterület létrehozása során

Az Azure szinapszis megpróbálja a Storage blob adatközreműködői szerepkört a felügyelt identitáshoz adni, miután létrehozta az Azure szinapszis-munkaterületet a Azure Portal használatával. Az **alapismeretek** lapon megadhatja a ADLS Gen2 Storage-fiók adatait.

![Alapismeretek lap a munkaterület létrehozási folyamatában](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Válassza ki a ADLS Gen2 Storage-fiókot és a fájlrendszert a **fiók neve** és a fájlrendszer **neve** beállításban.

![ADLS Gen2 Storage-fiók adatainak megadása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Ha a munkaterület létrehozója a ADLS Gen2 Storage-fiók **tulajdonosa** is, akkor az Azure szinapszis a *Storage blob adatközreműködői* szerepkört a felügyelt identitáshoz rendeli hozzá. A következő üzenet jelenik meg a Storage-fiók megadott adatai alatt.

![A Storage blob adatközreműködői hozzárendelésének sikeres hozzárendelése](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Ha a munkaterület létrehozója nem tulajdonosa a ADLS Gen2 Storage-fióknak, akkor az Azure szinapszis nem rendeli hozzá a *Storage blob adatközreműködői* szerepkört a felügyelt identitáshoz. A Storage-fiók adatai alatt megjelenő üzenet értesíti a munkaterület létrehozóját arról, hogy nem rendelkezik megfelelő engedélyekkel a *Storage blob adatközreműködői* szerepkörének a felügyelt identitáshoz való biztosításához.

![Sikertelen tárolási blob-adatközreműködői hozzárendelés](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

A Message állapotok nem hozhatnak létre Spark-készleteket, kivéve, ha a *Storage-blob Adatközreműködői* a felügyelt identitáshoz vannak rendelve.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Engedélyek megadása a felügyelt identitásnak a munkaterület létrehozása után

Ha a munkaterület létrehozása során nem rendeli hozzá a *Storage blob-adatközreműködőt* a felügyelt identitáshoz, akkor a ADLS Gen2 Storage-fiók **tulajdonosa** manuálisan rendeli hozzá ezt a szerepkört az identitáshoz. A következő lépések segítséget nyújtanak a manuális hozzárendelés megvalósításában.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>1. lépés: navigáljon a ADLS Gen2 Storage-fiókhoz Azure Portal

A Azure Portalban nyissa meg a ADLS Gen2 Storage-fiókot, és válassza az **Áttekintés** lehetőséget a bal oldali navigációs sávon. A tároló vagy a fájlrendszer szintjén csak a *Storage blob adatközreműködői* szerepkört kell hozzárendelni. Válassza a **tárolók** lehetőséget.  
![ADLS Gen2 Storage-fiók áttekintése](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>2. lépés: a tároló kiválasztása

A felügyelt identitásnak hozzáféréssel kell rendelkeznie a munkaterület létrehozásakor megadott tárolóhoz (fájlrendszerhez). Ezt a tárolót vagy fájlrendszert Azure Portalban találja. Nyissa meg Azure Portal az Azure szinapszis munkaterületet, és válassza az **Áttekintés** lapot a bal oldali navigációs sávon.
![ADLS Gen2 Storage-fiók tárolója](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Válassza ki ugyanazt a tárolót vagy fájlrendszert, hogy a *tárolási blob adatközreműködői* szerepkört adja a felügyelt identitásnak.
![A kiválasztani kívánt tárolót vagy fájlrendszert megjelenítő képernyőkép.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>3. lépés: navigáljon a hozzáférés-vezérléshez

Válassza a **Access Control (iam)** lehetőséget.

![Hozzáférés-vezérlés (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>4. lépés: új szerepkör-hozzárendelés hozzáadása

Válassza a **+ Hozzáadás** lehetőséget.

![Új szerepkör-hozzárendelés hozzáadása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>5. lépés: az Azure-szerepkör kiválasztása

Válassza ki a **Storage blob adatközreműködői** szerepkört.

![Azure-szerepkör kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>6. lépés: az Azure AD rendszerbiztonsági tag kiválasztása

Válassza ki az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** elemet a **hozzáférés kiosztása** legördülő listából.

![HRE rendszerbiztonsági tag kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>7. lépés: a felügyelt identitás keresése

A felügyelt identitás neve a munkaterület neve is. Keresse meg a felügyelt identitást az Azure szinapszis-munkaterület nevének beírásával a **Select (kiválasztás) területen**. Ekkor meg kell jelennie a felügyelt identitásnak.

![A felügyelt identitás megkeresése](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>8. lépés: a felügyelt identitás kiválasztása

Válassza ki a felügyelt identitást a **kijelölt tagok** számára. A szerepkör-hozzárendelés hozzáadásához válassza a **Mentés** lehetőséget.

![Felügyelt identitás kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>9. lépés: annak ellenőrzése, hogy a tároló blob-adatközreműködői szerepköre hozzá van-e rendelve a felügyelt identitáshoz

Válassza a **Access Control (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelések** lehetőséget.

![Szerepkör-hozzárendelés ellenőrzése](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

A felügyelt identitást a **Storage blob adatközreműködői** című szakaszban találja a *Storage blob-adatközreműködői* szerepkörhöz hozzárendelve. 
![ADLS Gen2 Storage-fiók tárolójának kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Következő lépések

További információ a [munkaterület által felügyelt identitásról](./synapse-workspace-managed-identity.md)

---
title: Felügyelt identitás a szinapszis munkaterületen
description: Az Azure szinapszis munkaterület felügyelt identitását ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 4100640fd619d9d971ac1c7083eedef8e2125fc3
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738285"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Azure szinapszis-munkaterület felügyelt identitása (előzetes verzió)

Ebből a cikkből megismerheti az Azure szinapszis munkaterület felügyelt identitását.

## <a name="managed-identities"></a>Felügyelt identitások

Az Azure-erőforrások felügyelt identitása Azure Active Directory egyik funkciója. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. A felügyelt identitás funkció használatával bármilyen, az Azure AD-hitelesítést támogató szolgáltatáshoz végezhet hitelesítést.

Az Azure-erőforrások felügyelt identitásai a korábban Managed Service Identity (MSI) néven ismert szolgáltatás új neve. További információért lásd: [felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="azure-synapse-workspace-managed-identity"></a>Azure szinapszis-munkaterület felügyelt identitása

A munkaterület létrehozásakor létrejön egy rendszerhez rendelt felügyelt identitás az Azure szinapszis-munkaterülethez.

>[!NOTE]
>Ezt a munkaterület felügyelt identitását felügyelt identitásnak nevezzük a dokumentum többi részén.

Az Azure szinapszis a felügyelt identitás használatával integrálja a folyamatokat. A felügyelt identitás életciklusa közvetlenül az Azure szinapszis munkaterülethez van kötve. Ha törli az Azure szinapszis munkaterületet, akkor a felügyelt identitás is törlődik.

A munkaterület által felügyelt identitásnak engedéllyel kell rendelkeznie a folyamatok műveleteinek végrehajtásához. Az objektumazonosító vagy az Azure szinapszis-munkaterület neve segítségével megkeresheti a felügyelt identitást az engedélyek megadásakor.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Felügyelt identitás beolvasása Azure Portal

A felügyelt identitást Azure Portalban kérheti le. Nyissa meg Azure Portal az Azure szinapszis munkaterületét, és válassza az **Áttekintés** lehetőséget a bal oldali navigációs sávon. A felügyelt identitás objektumazonosító a fő képernyőn jelenik meg.

![Felügyelt identitás objektumának azonosítója](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

A felügyelt identitás adatai is megjelennek, amikor olyan társított szolgáltatást hoz létre, amely támogatja az Azure szinapszis Studio felügyelt identitás-hitelesítését.

Indítsa el az **Azure szinapszis Studio alkalmazást** , és válassza a **kezelés** fület a bal oldali navigációs sávon. Ezután válassza a **társított szolgáltatások** elemet, és válassza az **+ új** lehetőséget egy új társított szolgáltatás létrehozásához.

![Társított szolgáltatás létrehozása 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Az **új társított szolgáltatás** ablakban írja be a következőt: *Azure Data Lake Storage Gen2* . Válassza ki a **Azure Data Lake Storage Gen2** erőforrástípust az alábbi listából, majd válassza a **Folytatás** lehetőséget.

![Társított szolgáltatás létrehozása 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

A következő ablakban válassza a **felügyelt identitás** lehetőséget a **hitelesítési módszerhez** . Ekkor megjelenik a felügyelt identitás **neve** és az **objektum azonosítója** .

![Társított szolgáltatás létrehozása 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Következő lépések

További információ [Az Azure szinapszis-munkaterület felügyelt identitására vonatkozó engedélyek megadásáról](./how-to-grant-workspace-managed-identity-permissions.md)

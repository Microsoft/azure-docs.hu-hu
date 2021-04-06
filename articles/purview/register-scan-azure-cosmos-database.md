---
title: Az Azure Cosmos-adatbázis vizsgálata (SQL API)
description: Ez az útmutató ismerteti az Azure Cosmos Database (SQL API) vizsgálatának részleteit.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696240"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos-adatbázis regisztrálása és vizsgálata (SQL API)

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Azure Cosmos Database (SQL API) fiókot az Azure hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure Cosmos Database (SQL API) teljes és növekményes vizsgálatokat támogat a metaadatok és a séma rögzítéséhez. A vizsgálatok a rendszer és az egyéni besorolási szabályok alapján automatikusan is kategorizálják az adathalmazt.

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- Az Azure-beli adatforrások rendszergazdájának kell lennie

## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Az Azure Cosmos Database (SQL API) hitelesítése csak egy módon állítható be:

- Fiókkulcs
 
### <a name="account-key"></a>Fiókkulcs

Ha a kiválasztott hitelesítési módszer a **fiók kulcsa**, a Key vaultban be kell szereznie a hozzáférési kulcsot, és tárolnia kell a következőt:

1. Navigáljon a Cosmos DB-fiókjához a Azure Portal 
1. **Beállítások**  >  **kulcsának** kiválasztása 
1. Másolja a *kulcsot* , és mentse valahova a következő lépésekhez
1. Navigáljon a kulcstartóhoz
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és írja be a **nevet** és az **értéket** a Storage-fiók *kulcsaként*
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) a kulcs használatával a vizsgálat beállításához

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Azure Cosmos Database-(SQL API-) fiók regisztrálása

Ha új Azure Cosmos Database-(SQL API-) fiókot szeretne regisztrálni a adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába
1. A bal oldali navigációs sávon válassza a **források** lehetőséget
1. **Regisztráció** kiválasztása
1. A **források regisztrálása** lapon válassza a **Azure Cosmos db (SQL API) lehetőséget.**
1. Válassza a **Folytatás** elemet

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="új adatforrás regisztrálása" border="true":::

A **források regisztrálása (Azure Cosmos db (SQL API))** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
1. Válassza ki, hogyan szeretné a kívánt Storage-fiókra mutatni:
   1. Válasszon az **Azure-előfizetésből**, válassza ki a megfelelő előfizetést az **Azure-előfizetés** legördülő listából, és a megfelelő cosmosDB-fiókot a **Cosmos db fiók neve** legördülő listából.
   1. Az **ENTER billentyűt manuálisan** is kiválaszthatja, és megadhat egy szolgáltatási végpontot (URL-címet).
1. **Fejezze** be az adatforrás regisztrálását.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="források regisztrálása – beállítások" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)

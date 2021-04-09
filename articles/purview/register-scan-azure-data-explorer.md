---
title: Az Azure-Adatkezelő vizsgálata
description: Ez az útmutató ismerteti az Azure-Adatkezelő vizsgálatának részleteit.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896112"
---
# <a name="register-and-scan-azure-data-explorer"></a>Azure-Adatkezelő regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Azure Adatkezelő-fiókot az Azure hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure Adatkezelő a metaadatok és a séma rögzítése érdekében teljes és növekményes vizsgálatokat is támogat. A vizsgálatok a rendszer és az egyéni besorolási szabályok alapján automatikusan is kategorizálják az adathalmazt.

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- Az Azure-beli adatforrások rendszergazdájának kell lennie

## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Az Azure adatkezelő hitelesítésének beállítása csak egyetlen módon történik:

- Szolgáltatásnév

### <a name="service-principal"></a>Szolgáltatásnév

Ha az egyszerű szolgáltatás hitelesítését szeretné használni a vizsgálatokhoz, használhat egy meglévőt, vagy létrehozhat egy újat. 

> [!Note]
> Ha létre kell hoznia egy új szolgáltatásnevet, kövesse az alábbi lépéseket:
> 1. Navigáljon a [Azure Portal](https://portal.azure.com).
> 1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
> 1. Válassza az **Alkalmazásregisztrációk** lehetőséget.
> 1. Válassza az **+ új alkalmazás regisztrálása** lehetőséget.
> 1. Adja meg az **alkalmazás** nevét (az egyszerű szolgáltatásnév).
> 1. **Csak a szervezeti címtárban** válassza a fiókok elemet.
> 1. Az átirányítási URI esetében válassza a **web** lehetőséget, és adja meg a kívánt URL-címet. nem kell valós vagy munkahelyi.
> 1. Válassza a **Regisztráció** lehetőséget.

Az egyszerű szolgáltatás alkalmazás-azonosítójának és titkos kódjának beszerzéséhez a következő szükséges:

1. Navigáljon az egyszerű szolgáltatáshoz a [Azure Portal](https://portal.azure.com)
1. Másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** és az **ügyfél titkos kulcsa** a **tanúsítványok & Secrets** elemből.
1. Navigáljon a kulcstartóhoz
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és adja meg az Ön által választott **nevet** és **értéket** az egyszerű **szolgáltatásnév alapján**
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) az egyszerű szolgáltatásnév használatával a vizsgálat beállításához

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>A szolgáltatás egyszerű hozzáférésének biztosítása az Azure-beli adatkezelő-példányhoz

1. Lépjen az Azure Portalra. Ezután navigáljon az Azure-beli adatkezelő-példányra.

1. Adja hozzá a szolgáltatásnevet a **AllDatabasesViewer** szerepkörhöz az **engedélyek** lapon, ahogy az alábbi képernyőképen is látható.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Képernyőkép az egyszerű szolgáltatásnév hozzáadásához az engedélyekben" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Azure Adatkezelő-fiók regisztrálása

Ha új Azure Adatkezelő (Kusto) fiókot szeretne regisztrálni a adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába
1. A bal oldali navigációs sávon válassza a **források** lehetőséget
1. **Regisztráció** kiválasztása
1. A **források regisztrálása** lapon válassza az **Azure adatkezelő**
1. Válassza a **Folytatás** elemet

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="új adatforrás regisztrálása" border="true":::

A **források regisztrálása (Azure adatkezelő (Kusto))** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
1. Válassza ki, hogyan szeretné a kívánt Storage-fiókra mutatni:
   1. Válasszon az **Azure-előfizetésből**, válassza ki a megfelelő előfizetést az **Azure-előfizetés** legördülő listából, és a megfelelő fürtöt a **fürt** legördülő listából.
   1. Az **ENTER billentyűt manuálisan** is kiválaszthatja, és megadhat egy szolgáltatási végpontot (URL-címet).
1. **Fejezze** be az adatforrás regisztrálását.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="források regisztrálása – beállítások" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)

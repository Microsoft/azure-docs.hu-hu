---
title: Az Azure Storage-blob vizsgálata
description: Ismerje meg, hogyan vizsgálhatja meg az Azure Blob Storage-t az Azure hatáskörébe tartozó adatkatalógusban.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: b27b46c68d018d2ddf79d284b20cc05b51640891
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880641"
---
# <a name="register-and-scan-azure-blob-storage"></a>Az Azure Blob Storage regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Azure Blob Storage-fiókot a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure Blob Storage a metaadatok és a séma rögzítése érdekében teljes és növekményes vizsgálatokat is támogat. Emellett a rendszer és az egyéni besorolási szabályok alapján automatikusan osztályozza az adathalmazokat.

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- Az Azure-beli adatforrások rendszergazdájának kell lennie

## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Az Azure Blob Storage hitelesítésének Háromféleképpen állítható be:

- Felügyelt identitás
- Fiók kulcsa
- Szolgáltatásnév

### <a name="managed-identity-recommended"></a>Felügyelt identitás (ajánlott)

Ha a **felügyelt identitást** választja, a csatlakozás beállításához először meg kell adnia a hatáskörébe tartozó fiókot az adatforrás vizsgálatához:

1. Nyissa meg a tárfiókot.
1. Válassza a bal oldali navigációs menü **Access Control (iam)** elemét. 
1. Válassza a **+ Hozzáadás** lehetőséget.
1. Állítsa be a **szerepkört** a **blob-Adatolvasó tárolásához** , és adja meg az Azure hatáskörébe tartozó fiók nevét a beviteli mező **kiválasztása** területen. Ezt követően válassza a **Mentés** lehetőséget, hogy ezt a szerepkör-hozzárendelést a hatáskörébe tartozó fiókjához adja.

> [!Note]
> További részletekért tekintse meg a [blobok és várólisták hozzáférésének engedélyezése a Azure Active Directory használatával](../storage/common/storage-auth-aad.md) című témakör lépéseit.

### <a name="account-key"></a>Fiók kulcsa

Ha a kiválasztott hitelesítési módszer a **fiók kulcsa**, a Key vaultban be kell szereznie a hozzáférési kulcsot, és tárolnia kell a következőt:

1. Navigáljon a Storage-fiókhoz
1. **Beállítások kiválasztása > hozzáférési kulcsok**
1. Másolja a *kulcsot* , és mentse valahova a következő lépésekhez
1. Navigáljon a kulcstartóhoz
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és írja be a **nevet** és az **értéket** a Storage-fiók *kulcsaként*
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) a kulcs használatával a vizsgálat beállításához

### <a name="service-principal"></a>Szolgáltatásnév

Egyszerű szolgáltatásnév használatához használhat egy meglévőt, vagy létrehozhat egy újat. 

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

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a blob Storage-hoz

1. Nyissa meg a tárfiókot.
1. Válassza a bal oldali navigációs menü **Access Control (iam)** elemét. 
1. Válassza a **+ Hozzáadás** lehetőséget.
1. Állítsa be a **szerepkört** a **blob-Adatolvasó tárolásához** , és adja meg az egyszerű szolgáltatásnév vagy objektum azonosítóját a beviteli mező **kiválasztása** területen. Ezután válassza a **Mentés** lehetőséget, ha ezt a szerepkör-hozzárendelést szeretné megadni a szolgáltatáshoz.

## <a name="firewall-settings"></a>Tűzfalbeállítások

> [!NOTE]
> Ha a Storage-fiókhoz engedélyezve van a tűzfal, akkor a vizsgálat beállításakor **felügyelt identitás** -hitelesítési módszert kell használnia.

1. Lépjen be a Storage-fiókjába [Azure Portal](https://portal.azure.com)
1. Navigáljon a **beállítások > hálózatkezelés** és
1. Válassza a **kiválasztott hálózatok** **lehetőséget a hozzáférés engedélyezése**
1. A **tűzfal** szakaszban jelölje be a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz** lehetőséget, majd kattintson a **Mentés** gombra.

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="A tűzfal beállításait ábrázoló képernyőfelvétel":::

## <a name="register-an-azure-blob-storage-account"></a>Azure Blob Storage-fiók regisztrálása

Ha új blob-fiókot szeretne regisztrálni az adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába
1. A bal oldali navigációs sávon válassza a **források** lehetőséget
1. **Regisztráció** kiválasztása
1. A **források regisztrálása** lapon válassza az **Azure Blob Storage**
1. Válassza a **Folytatás** elemet

A **források regisztrálása (Azure Blob Storage)** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban. 
1. Válassza ki az előfizetést a Storage-fiókok szűréséhez
1. Válasszon Storage-fiókot
1. Válasszon ki egy gyűjteményt, vagy hozzon létre egy újat (nem kötelező)
1. **Fejezze** be az adatforrás regisztrálását.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="források regisztrálása – beállítások" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)
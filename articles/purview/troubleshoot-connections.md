---
title: Az Azure hatáskörébe tartozó kapcsolatok hibáinak megoldása
description: Ez a cikk ismerteti az Azure-beli kapcsolatok hibaelhárításának lépéseit.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c176fcafe13749ba89c04b34854f036aa5aea516
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677646"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Az Azure hatáskörébe tartozó kapcsolatok hibáinak megoldása

Ez a cikk azt ismerteti, hogyan lehet elhárítani a kapcsolódási hibákat az Azure-beli adatforrások vizsgálatának beállítása során.

## <a name="permission-the-credential-on-the-data-source"></a>Engedély a hitelesítő adatokra az adatforráson

Ha felügyelt identitást vagy szolgáltatásnevet használ a vizsgálatokhoz hitelesítési módszerként, akkor engedélyeznie kell, hogy ezek az identitások hozzáférhessenek az adatforráshoz.

Az egyes Forrástípus-típusokra vonatkozó konkrét utasítások:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [1. generációs Azure Data Lake Storage](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Felügyelt példány Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)
## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>A hitelesítő adatok tárolása a kulcstartóban és a megfelelő titkos név és verzió használata

A hitelesítő adatokat a Azure Key Vault-példányban is tárolnia kell, és a megfelelő titkos nevet és verziót kell használnia.

Ellenőrizze ezt az alábbi lépésekkel:

1. Navigáljon a Key Vault.
1. Válassza a **Settings** > **Secrets** (Beállítások > Titkos kódok) menüpontot.
1. Válassza ki azt a titkot, amelyet az adatforráshoz való hitelesítéshez használ a vizsgálatokhoz.
1. Válassza ki a használni kívánt verziót, és ellenőrizze, hogy helyes-e a jelszó vagy a fiók kulcsa. ehhez kattintson a **titkos érték megjelenítése** elemre. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Ellenőrizze, hogy a Azure Key Vault felügyelt identitásra vonatkozó engedélyek vannak-e

Ellenőrizze, hogy a megfelelő engedélyek konfigurálva vannak-e a hatáskörébe tartozó felügyelt identitáshoz a Azure Key Vault eléréséhez.

Ennek ellenőrzéséhez hajtsa végre a következő lépéseket:

1. Navigáljon a kulcstartóhoz és a **hozzáférési házirendek** szakaszhoz.

1. Ellenőrizze, hogy a hatáskörébe tartozó felügyelt identitás a *jelenlegi hozzáférési szabályzatok* szakaszban látható-e a titkos kulcsokra vonatkozó minimum **Get** és **List** engedélyekkel

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="A beolvasási és listázási engedélyek legördülő listájának kijelölését bemutató kép":::

Ha nem látja a hatáskörébe tartozó felügyelt identitást, akkor a hozzáadásához kövesse a [hitelesítő adatok létrehozása és kezelése a vizsgálatokhoz](manage-credentials.md) című témakör lépéseit. 

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)

---
title: Hitelesítő adatok létrehozása és kezelése a vizsgálatokhoz
description: A hitelesítő adatok Azure-ban való létrehozásához és kezeléséhez szükséges lépések ismertetése.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461708"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Az Azure hatáskörébe tartozó forrás-hitelesítés hitelesítő adatai

Ez a cikk azt ismerteti, hogyan hozhat létre hitelesítő adatokat az Azure hatáskörébe. Ezek a mentett hitelesítő adatok segítségével gyorsan újra felhasználhatja a mentett hitelesítési adatokat, és alkalmazhatja azokat az adatforrások vizsgálatára.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Key Vault. A létrehozásával kapcsolatos információkért lásd: rövid útmutató [: kulcstartó létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Bevezetés

A hitelesítő adatok olyan hitelesítési információk, amelyeket az Azure hatáskörébe használhat a regisztrált adatforrásokhoz való hitelesítéshez. Hitelesítő objektum hozható létre különféle hitelesítési forgatókönyvekhez, például a Felhasználónév/jelszó megkövetelése egyszerű hitelesítéshez. A hitelesítő adatok a kiválasztott hitelesítési módszer alapján rögzítik a hitelesítéshez szükséges konkrét adatokat. A hitelesítő adatok a meglévő Azure Key Vault-titkokat használják a bizalmas hitelesítési információk beolvasására a hitelesítő adatok létrehozásakor.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>A felügyelet beállítása a hatáskörébe tartozó felügyelt identitás használatával

Ha a hatáskörébe tartozó felügyelt identitást használja a vizsgálatok beállításához, akkor nem kell explicit módon létrehoznia a hitelesítő adatokat, és a Key vaultot csatolnia kell a hatáskörébe, hogy tárolja őket. A hatáskörébe tartozó felügyelt identitásnak az adatforrások vizsgálatához való hozzáadásával kapcsolatos részletes utasításokért tekintse meg az alábbi adatforrás-specifikus hitelesítési szakaszt alább:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [1. generációs Azure Data Lake Storage](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Felügyelt példány Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Key Vault-kapcsolatok létrehozása az Azure hatáskörébe tartozó fiókban

A hitelesítő adatok létrehozása előtt először társítson egy vagy több meglévő Azure Key Vault példányt az Azure hatáskörébe tartozó fiókjával.

1. A [Azure Portal](https://portal.azure.com)válassza ki az Azure-beli hatáskörébe tartozó fiókját, és nyissa meg az Azure hatáskörébe Studio alkalmazást. Navigáljon a **felügyeleti központhoz** az Azure hatáskörébe Studióban, majd navigáljon a **hitelesítő adatokhoz**.

2. A **hitelesítő adatok** lapon válassza a **Key Vault kapcsolatok kezelése** lehetőséget.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault kapcsolatok kezelése":::

3. Válassza az **+ új** lehetőséget a Key Vault kapcsolatok kezelése lapon.

4. Adja meg a szükséges adatokat, majd válassza a **Létrehozás** lehetőséget.

5. Győződjön meg arról, hogy a Key Vault sikeresen hozzá lett rendelve az Azure-beli hatáskörébe-fiókjához, ahogy az ebben a példában is látható:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Azure Key Vault kapcsolatok megtekintése a megerősítéshez.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Adja meg a hatáskörébe tartozó felügyelt identitás hozzáférést a Azure Key Vault

1. Navigáljon a Azure Key Vault.

2. Válassza ki a **hozzáférési szabályzatok** lapot.

3. Válassza a **hozzáférési házirend hozzáadása** lehetőséget.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="A hatáskörébe MSI hozzáadása a AKV-hez":::

4. A **titkok engedélyei** legördülő menüben válassza a **lekérés** és **Listázás** engedélyeket.

5. A **rendszerbiztonsági tag kiválasztása lapon** válassza ki a hatáskörébe tartozó felügyelt identitást. A hatáskörébe tartozó MSI-t a felügyeleti példány **vagy** a felügyelt identitás alkalmazás azonosítójának használatával keresheti meg. Jelenleg nem támogatottak az összetett identitások (felügyelt identitás neve + alkalmazás-azonosító).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Hozzáférési szabályzat hozzáadása":::

6. Válassza a **Hozzáadás** lehetőséget.

7. A hozzáférési szabályzat mentéséhez válassza a **Mentés** lehetőséget.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Hozzáférési szabályzat mentése":::

## <a name="create-a-new-credential"></a>Új hitelesítő adat létrehozása

A következő hitelesítőadat-típusokat támogatja a hatáskörébe:

- Alapszintű hitelesítés: a **jelszót** titkos kulcsként adja hozzá a Key vaulthoz.
- Egyszerű szolgáltatásnév: az **egyszerű szolgáltatásnév kulcsát** hozzáadja a Key Vault titkos kódjához.
- SQL-hitelesítés: a **jelszót** titkos kulcsként adja hozzá a Key vaulthoz.
- Fiók kulcsa: a Key Vault titkos kulcsaként adja hozzá a **fiók kulcsát** .
- ARN szerepkör: az Amazon S3-adatforráshoz adja hozzá az **ARN szerepkört** az AWS-ben. 

További információ: [Secret hozzáadása a Key Vaulthoz](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) , és [új AWS-szerepkör létrehozása a hatáskörébe](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview).

A titkos kulcsok tárolása után a Key vaultban:

1. Az Azure-beli hatáskörébe lépjen a hitelesítő adatok lapon.

2. Az új hitelesítő adatok létrehozásához válassza az **+ új** lehetőséget.

3. Adja meg a szükséges adatokat. Válassza ki azt a **hitelesítési módszert** és **Key Vault-kapcsolódást** , amelyből ki szeretne választani egy titkos kulcsot.

4. A részletek kitöltését követően válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Új hitelesítő adat":::

5. Győződjön meg arról, hogy az új hitelesítő adat megjelenik a lista nézetben, és készen áll a használatra.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Hitelesítő adat megtekintése":::

## <a name="manage-your-key-vault-connections"></a>Key Vault-kapcsolatok kezelése

1. Keresés/keresés Key Vault kapcsolatok név szerint

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Keresés a Key vaultban":::

2. Egy vagy több Key Vault kapcsolat törlése

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Key Vault törlése":::

## <a name="manage-your-credentials"></a>Hitelesítő adatok kezelése

1. Keressen/keressen hitelesítő adatokat név alapján.
  
2. Válasszon ki és frissítsen egy meglévő hitelesítő adatot.

3. Egy vagy több hitelesítő adat törlése.

## <a name="next-steps"></a>Következő lépések

[Vizsgálati szabálykészlet létrehozása](create-a-scan-rule-set.md)

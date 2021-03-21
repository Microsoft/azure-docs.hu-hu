---
title: Adattitkosítás – Azure Portal – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for PostgreSQL önálló kiszolgáló adattitkosítását a Azure Portal használatával.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 98c413f85fe556f5fb413716037163931753e1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240732"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Adattitkosítás Azure Database for PostgreSQL önálló kiszolgáló számára a Azure Portal használatával

Ebből a témakörből megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosítását a Azure Portal használatával.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* A Azure Key Vault-ban hozzon létre egy Key vaultot és egy kulcsot, amelyet az ügyfél által felügyelt kulcshoz szeretne használni.
* A Key vaultnak a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Védett kiürítés](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak az alábbi attribútumokkal kell rendelkeznie felhasználó által felügyelt kulcsként:
  * Nincs lejárati dátum
  * Nincs letiltva
  * Képes a Get, a wrap Key és a dewrap Key művelet végrehajtására

## <a name="set-the-right-permissions-for-key-operations"></a>A megfelelő engedélyek beállítása a kulcsfontosságú műveletekhez

1. A Key Vault területen válassza a **hozzáférési szabályzatok**  >  **hozzáférési házirend hozzáadása** lehetőséget.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Képernyőkép a Key Vaultről, hozzáférési házirendekkel, és Kiemelt hozzáférési szabályzat hozzáadása":::

2. Válassza a **kulcs engedélyei** lehetőséget, majd válassza a **beolvasás**, **becsomagolás**, **kicsomagolás** és a **rendszerbiztonsági tag** lehetőséget, amely a PostgreSQL-kiszolgáló neve. Ha a kiszolgáló rendszerbiztonsági tagja nem található a meglévő rendszerbiztonsági tag listájában, regisztrálnia kell. A rendszer arra kéri, hogy regisztrálja a kiszolgálói rendszerbiztonsági tag-t, amikor első alkalommal kísérli meg az adattitkosítás beállítását, és sikertelen lesz.  

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Hozzáférési szabályzat – áttekintés":::

3. Kattintson a **Mentés** gombra.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Adattitkosítás beállítása Azure Database for PostgreSQL önálló kiszolgálóhoz

1. Az ügyfél által felügyelt kulcs beállításához Azure Database for PostgreSQL válassza az **adattitkosítás** lehetőséget.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Képernyőkép a Azure Database for PostgreSQLről, az adattitkosítás kiemelésével":::

2. Kijelölhet egy kulcstartót és egy kulcspárt, vagy megadhatja a kulcs azonosítóját is.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Képernyőkép a Azure Database for PostgreSQLről, az adattitkosítási lehetőségek kiemelésével":::

3. Kattintson a **Mentés** gombra.

4. Annak biztosítása érdekében, hogy az összes fájl (beleértve az ideiglenes fájlokat is) teljes mértékben titkosítva legyen, indítsa újra a kiszolgálót.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Adattitkosítás használata visszaállítási vagy replika kiszolgálók esetén

Miután Azure Database for PostgreSQL egy kiszolgálót a Key Vault tárolt ügyfél felügyelt kulcsával, a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel vagy replika (helyi/régió) művelettel teheti meg. Tehát titkosított PostgreSQL-kiszolgáló esetén a következő lépésekkel hozhat létre titkosított visszaállított kiszolgálót.

1. A kiszolgálón válassza az **Áttekintés**  >  **visszaállítás** lehetőséget.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Képernyőkép a Azure Database for PostgreSQLről, áttekintés és visszaállítás kiemelve":::

   Vagy replikálásra alkalmas kiszolgáló esetén a **Beállítások** fejléc alatt válassza a **replikálás** lehetőséget.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-replica.png" alt-text="Képernyőkép a Azure Database for PostgreSQLról, a replikálás kiemelve":::

2. A visszaállítási művelet befejezése után a létrehozott új kiszolgáló az elsődleges kiszolgáló kulcsával lesz titkosítva. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló nem érhető el. Ez megakadályozza az adatkezelést, mert az új kiszolgáló identitása még nem kapott engedélyt a kulcstartó elérésére.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Képernyőkép a Azure Database for PostgreSQLről, a nem elérhető állapot kiemelésével":::

3. A kiszolgáló elérhetővé tételéhez érvényesítse újra a kulcsot a visszaállított kiszolgálón. Válassza **az adattitkosítás**  >  **újraellenőrzése kulcsot**.

   > [!NOTE]
   > Az első újraellenőrzési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell adni a kulcstartóhoz. Az egyszerű szolgáltatásnév létrehozásához válassza a **kulcs újraérvényesítése** lehetőséget, amely hibaüzenetet jelenít meg, de létrehozza az egyszerű szolgáltatásnevet. Ezt követően tekintse meg a jelen cikk korábbi részében [ismertetett lépéseket](#set-the-right-permissions-for-key-operations) .

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Képernyőkép a Azure Database for PostgreSQLról, az újraérvényesítési lépés kiemelve":::

   A Key vaultnak hozzáférést kell adnia az új kiszolgálóhoz.

4. Az egyszerű szolgáltatás regisztrálását követően ismét ellenőrizze újra a kulcsot, és a kiszolgáló folytatja a normál működést.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Képernyőkép a Azure Database for PostgreSQLről, amely a visszaállított funkciókat mutatja":::

## <a name="next-steps"></a>Következő lépések

 További információ az adattitkosításról: [Azure Database for PostgreSQL egykiszolgálós adattitkosítás az ügyfél által felügyelt kulccsal](concepts-data-encryption-postgresql.md).

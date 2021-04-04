---
title: A Azure Database for MySQL-adattitkosítás érvényesítésének biztosítása
description: Ismerje meg, hogyan ellenőrizheti a Azure Database for MySQL-adattitkosítás titkosítását az ügyfelek által felügyelt kulcs használatával.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 1f47b3d5679b70461eaba64b5815770162cb6fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241072"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Az Azure Database for MySQL adattitkosításának ellenőrzése

Ebből a cikkből megtudhatja, hogy az adattitkosítás az ügyfél által felügyelt kulcs használatával Azure Database for MySQL a várt módon működik-e.

## <a name="check-the-encryption-status"></a>A titkosítás állapotának keresése

### <a name="from-portal"></a>A portálon

1. Ha szeretné ellenőrizni, hogy az ügyfél kulcsát használja-e a rendszer a titkosításhoz, kövesse az alábbi lépéseket:

    * A Azure Portal navigáljon a **Azure Key Vault**  ->  **kulcsaihoz**
    * Válassza ki a kiszolgáló titkosításához használt kulcsot.
    * Állítsa **a kulcs állapotát** a **nem** értékre.
  
       Némi idő után (**~ 15 perc**) a Azure Database for MySQL kiszolgáló **állapota** nem **érhető** el. A kiszolgálón végrehajtott összes I/O-művelet sikertelen lesz, amely ellenőrzi, hogy a kiszolgáló valóban titkosítva van-e az ügyfelek kulcsával, és a kulcs jelenleg nem érvényes.
    
       Annak érdekében, hogy a kiszolgáló **elérhető** legyen, újraérvényesítheti a kulcsot. 
    
    * Állítsa a kulcs állapotát a Key Vault **Igen** értékre.
    * A kiszolgáló **adattitkosítása** lapon válassza a **kulcs újraérvényesítése** lehetőséget.
    * A kulcs újraérvényesítésének sikeressége után a kiszolgáló **állapota** **elérhetőre** változik.

2. Ha meg szeretné győződni arról, hogy a titkosítási kulcs be van állítva, akkor a Azure Portal a Azure Portal használt ügyfelek kulcsával titkosítja az adatvédelmet.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Hozzáférési szabályzat – áttekintés":::

### <a name="from-cli"></a>A CLI-ből

1. Az az *CLI* paranccsal ellenőrizheti a Azure Database for MySQL-kiszolgálóhoz használt fő erőforrásokat.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Az adattitkosítási készlet nélküli kiszolgálók esetében ez a parancs üres készletet ([]) eredményez.

### <a name="azure-audit-reports"></a>Azure-naplózási jelentések

A [naplózási jelentések](https://servicetrust.microsoft.com) is megtekinthetők, amelyek információkat biztosítanak az adatvédelmi szabványoknak és a jogszabályi követelményeknek való megfelelésről.

## <a name="next-steps"></a>Következő lépések

Az adattitkosítással kapcsolatos további tudnivalókért tekintse meg az [adattitkosítás Azure Database for MySQL az ügyfél által felügyelt kulccsal](concepts-data-encryption-mysql.md)című témakört.
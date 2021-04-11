---
title: Felügyelt identitás használata az Azure SQL Azure Spring Cloud-alkalmazáshoz való összekapcsolásához
description: Felügyelt identitás beállítása az Azure SQL Azure Spring Cloud-alkalmazáshoz való összekapcsolásához.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 3f1cde0bf233c67d02b9b7266ce3b3c8a3696db8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123379"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Felügyelt identitás használata Azure SQL Database Azure Spring Cloud-alkalmazáshoz való kapcsolódásához

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Ebből a cikkből megtudhatja, hogyan hozhat létre felügyelt identitást egy Azure Spring Cloud-alkalmazáshoz, és hogyan használhatja azt Azure SQL Databasehoz való hozzáféréshez.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a felhőhöz készült intelligens, méretezhető, összehasonlítható adatbázis-szolgáltatás. Mindig naprakész, és a teljesítményt és a tartósságot optimalizáló, mesterséges intelligenciával és automatizált funkciókkal rendelkezik. A kiszolgáló nélküli számítási és nagy kapacitású tárolási lehetőségek automatikusan igény szerint méretezhetők az erőforrások, így az új alkalmazások kiépítésére is koncentrálhat anélkül, hogy a tárterület méretét vagy erőforrás-kezelését kellene aggódnia.

## <a name="prerequisites"></a>Előfeltételek
Ez a példa a következő erőforrásokat használja.
* Kövesse a [Spring-alapú ADATközösség oktatóanyagát](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) , amely egy Azure SQL Database kiépítését és a Java-alkalmazásokkal való helyi munkát ismerteti
* Kövesse az [Azure Spring Cloud rendszerhez rendelt felügyelt identitási oktatóanyagot](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) egy Azure Spring Cloud-alkalmazás kiépítéséhez a következővel: mi engedélyezve

## <a name="grant-permission-to-the-managed-identity"></a>Engedély megadása a felügyelt identitásnak
Kapcsolódjon az SQL-kiszolgálóhoz, és futtassa a következő SQL-lekérdezést:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Ez <MIName> a következő szabályt követi: `<service instance name>/apps/<app name>` , például myspringcloud/apps/sqldemo. A MIName az Azure CLI használatával is lekérdezheti:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>A Java-alkalmazás konfigurálása felügyelt identitás használatára
Nyissa meg a `src/main/resources/application.properties` fájlt, és adja hozzá a `Authentication=ActiveDirectoryMSI;` következő sor végén. Ügyeljen arra, hogy a megfelelő értéket használja $AZ _DATABASE_NAME változóhoz.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Az alkalmazás létrehozása és üzembe helyezése az Azure Spring Cloud-ban
Hozza létre újra az alkalmazást, és telepítse az Azure Spring Cloud alkalmazásba, amelyet a második felsorolásjeles pont az előfeltételek területen kiosztott. Most már rendelkezik egy, a felügyelt identitás által hitelesített Spring boot-alkalmazással, amely egy, az Azure Spring Cloud-beli Azure SQL Databaseból származó adatok tárolására és lekérésére használja.

## <a name="next-steps"></a>Következő lépések

* [A Storage-Blobok elérése felügyelt identitással az Azure Spring Cloud-ban](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [A rendszerhez rendelt felügyelt identitás engedélyezése az Azure Spring Cloud Application szolgáltatáshoz](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [További információ az Azure-erőforrások felügyelt identitásáról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Az Azure Spring Cloud hitelesítése Key Vaultekkel a GitHub-műveletekben](./spring-cloud-github-actions-key-vault.md)
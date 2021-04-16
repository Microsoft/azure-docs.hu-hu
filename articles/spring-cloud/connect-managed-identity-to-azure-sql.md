---
title: Felügyelt identitás használata az Azure SQL Azure Spring Cloud csatlakoztatásához
description: Beállíthatja a felügyelt identitást, hogy Azure SQL egy Azure Spring Cloud alkalmazáshoz.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378788"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Felügyelt identitás használata egy Azure SQL Database alkalmazáshoz való Azure Spring Cloud való csatlakozáshoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy Azure Spring Cloud alkalmazáshoz, és hogyan használhatja a Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a felhőhöz készült intelligens, skálázható, relációsadatbázis-szolgáltatás. Mindig naprakész, AI-alapú és automatizált funkciókkal, amelyek optimalizálják a teljesítményt és a tartósságot. A kiszolgáló nélküli számítási és a hyperscale tárolási lehetőségek automatikusan igény szerint skálázják az erőforrásokat, így anélkül összpontosíthat az új alkalmazások építésére, hogy aggódnia kellene a tárterület méretével vagy az erőforrás-kezelésével.

## <a name="prerequisites"></a>Előfeltételek
Ez a példa a következő erőforrásokat használja.
* Kövesse a [Spring Data JPA-oktatóanyagot](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) egy Azure SQL Database és helyileg egy Java-alkalmazással való munkához
* Kövesse a [Azure Spring Cloud felügyelt identitással kapcsolatos](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) oktatóanyagot egy mi Azure Spring Cloud engedélyezett felügyelt identitással rendelkező alkalmazás üzembe Azure Spring Cloud beállításához

## <a name="grant-permission-to-the-managed-identity"></a>Engedély megadása a felügyelt identitáshoz
Csatlakozzon az SQL Serverhez, és futtassa a következő SQL-lekérdezést:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Ez <MIName> a következő szabályt követi: , például `<service instance name>/apps/<app name>` myspringcloud/apps/sqldemo. A MIName paramétert az Azure CLI használatával is lekérdezheti:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>A Java-alkalmazás konfigurálása felügyelt identitás használatára
Nyissa meg a fájlt, és adja hozzá a következő `src/main/resources/application.properties` `Authentication=ActiveDirectoryMSI;` sor végét. Győződjön meg arról, hogy a megfelelő értéket használja a $AZ_DATABASE_NAME változóhoz.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Az alkalmazás összeállítása és üzembe helyezése a Azure Spring Cloud
Építse újra az alkalmazást, és Azure Spring Cloud az Előfeltételek alatti második pont alatt kiépített alkalmazásalkalmazásban. Most már van egy felügyelt Spring Boot által hitelesített alkalmazás, amely JPA-val tárol és kér le adatokat egy Azure SQL Database-Azure Spring Cloud.

## <a name="next-steps"></a>Következő lépések

* [Storage-blob elérése felügyelt identitással a Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure Spring Cloud alkalmazáshoz](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [További információ az Azure-erőforrások felügyelt identitási szolgáltatásról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Hitelesítés Azure Spring Cloud Key Vault GitHub Actions](./spring-cloud-github-actions-key-vault.md)
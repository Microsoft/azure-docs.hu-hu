---
title: Önálló adatbázis – gyors útmutató a tartalomhoz
description: Tekintse meg az összes rövid útmutató tartalmi hivatkozását, amely segítséget nyújt a Azure SQL Database önálló adatbázisainak gyors megkezdésében.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184416"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Első lépések az önálló adatbázisok Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az [önálló adatbázisok](../index.yml) teljes körűen felügyelt platformként szolgálnak (DbaaS), amely ideális tárolási motor a modern Felhőbeli alkalmazásokhoz. Ebből a szakaszból megtudhatja, hogyan konfigurálhat és hozhat létre egyetlen adatbázist a Azure SQL Databaseban.

## <a name="quickstart-overview"></a>Gyors üzembe helyezés áttekintése

Ebben a szakaszban az elérhető cikkek áttekintését láthatja, amelyek segítségével gyorsan megkezdheti az önálló adatbázisok használatának megkezdését. A következő rövid útmutatók lehetővé teszik egyetlen adatbázis gyors létrehozását, a kiszolgálói szintű tűzfalszabály konfigurálását, majd egy adatbázis importálását az új önálló adatbázisba egy `.bacpac` fájl használatával:

- [Hozzon létre egyetlen adatbázist a Azure Portal használatával](single-database-create-quickstart.md).
- Az adatbázis létrehozása után meg kell [védenie az adatbázist a tűzfalszabályok konfigurálásával](firewall-create-server-level-portal-quickstart.md).
- Ha olyan SQL Server meglévő adatbázissal rendelkezik, amelyet át szeretne telepíteni Azure SQL Databasere, akkor telepítenie kell a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) szolgáltatást, amely az adatbázisokat SQL Server fogja elemezni, és megkeresi az áttelepítést blokkoló problémákat. Ha nem talál problémát, exportálhatja az adatbázist `.bacpac` fájlként, és [importálhatja a Azure Portal vagy a SqlPackage használatával](database-import.md).


## <a name="automating-management-operations"></a>Felügyeleti műveletek automatizálása

Az adatbázis létrehozásához, konfigurálásához és méretezéséhez használhatja a PowerShellt vagy az Azure CLI-t.

- [Önálló adatbázis létrehozása és konfigurálása a PowerShell](scripts/create-and-configure-database-powershell.md) vagy az [Azure CLI](scripts/create-and-configure-database-cli.md) használatával
- [Önálló adatbázis frissítése és erőforrások méretezése a PowerShell vagy az](scripts/monitor-and-scale-database-powershell.md) [Azure CLI](scripts/monitor-and-scale-database-cli.md) használatával

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrálás egyetlen adatbázisba minimális állásidővel

Ezekkel a gyors útmutatókkal gyorsan létrehozhatja vagy importálhatja az adatbázist az Azure-ba egy `.bacpac` fájl használatával. A `.bacpac` és a `.dacpac` fájlok azonban úgy lettek kialakítva, hogy gyorsan mozogjanak az adatbázisokat a SQL Server különböző verzióiban, illetve az Azure sqlon belül, vagy a folyamatos integrációt a DevOps-folyamatba. Ez a módszer azonban nem az üzemi adatbázisok minimális állásidővel való áttelepítésére lett tervezve, mert nem kell új adatmennyiséget hozzáadnia, várnia kell, amíg befejeződik a forrásadatbázis exportálása egy `.bacpac` fájlba, majd várnia kell, amíg az importálás befejeződik Azure SQL Database. Az összes várakozási idő az alkalmazás leállását eredményezi, különösen a nagyméretű adatbázisok esetében. Az éles adatbázis áthelyezéséhez jobb módszerre van szüksége az áttelepítéshez, amely minimális állásidőt garantál. Ehhez használja a [adatáttelepítési szolgáltatást (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json) az adatbázis minimális állásidővel való áttelepítéséhez. A DMS ezt úgy hajtja végre, hogy a forrásadatbázis módosításait fokozatosan visszaküldi a visszaállítani kívánt önálló adatbázisra. Így gyorsan válthat az alkalmazás forrásról a cél adatbázisára a minimális állásidővel.

## <a name="hands-on-learning-modules"></a>Gyakorlati tanulási modulok

Az alábbi Microsoft Learn-modulok segítenek a Azure SQL Databaseról való ingyenes tanulásban.

- [Adatbázis kiépítése SQL Databaseban az alkalmazásadatok tárolásához](/learn/modules/provision-azure-sql-db/)
- [Azure SQL Database-adatbázist lekérdező ASP.NET-alkalmazás fejlesztése és konfigurálása](/learn/modules/develop-app-that-queries-azure-sql/)
- [Az adatbázis védelme Azure SQL Database](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [Azure SQL Database támogatott szolgáltatásainak magas szintű listáját](features-comparison.md).
- Ismerje meg, hogyan teheti [biztonságosabbá az adatbázisát](secure-database-tutorial.md).
- További speciális útmutató: [egyetlen adatbázis használata Azure SQL Databaseban](how-to-content-reference-guide.md).
- A [PowerShellben](powershell-script-content-guide.md) és [Az Azure CLI](az-cli-script-samples-content-guide.md)-ben írt több minta parancsfájlt itt találhat.
- További információ az adatbázisok konfigurálásához használható [felügyeleti API](single-database-manage.md) -ról.
- [Azonosítsa a helyszíni adatbázis megfelelő Azure SQL Database vagy az Azure SQL felügyelt példányának SKU-](/sql/dma/dma-sku-recommend-sql-db/)t.
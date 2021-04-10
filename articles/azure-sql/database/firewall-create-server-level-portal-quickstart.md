---
title: Kiszolgálószintű tűzfalszabály létrehozása
description: Kiszolgálói szintű tűzfalszabály létrehozása
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, sstein
ms.date: 02/11/2019
ms.openlocfilehash: bef1e9bdafd91a51290fe84af074c8cece7cc371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486726"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>Gyors útmutató: kiszolgálói szintű tűzfalszabály létrehozása a Azure Portal használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre [kiszolgálói szintű tűzfalszabályok](firewall-configure.md) a Azure SQL Database a Azure Portal segítségével, hogy lehetővé váljon a [logikai SQL-kiszolgálókhoz](logical-servers.md), önálló adatbázisokhoz és rugalmas készletekhez és azok adatbázisaihoz való kapcsolódás. A más Azure-erőforrásokból és a helyszíni erőforrásokból való kapcsolódáshoz tűzfalszabály szükséges. A kiszolgálói szintű tűzfalszabályok nem vonatkoznak az Azure SQL felügyelt példányaira.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az [önálló adatbázis létrehozása a Azure Portal a](single-database-create-quickstart.md) kiindulási pontként létrehozott erőforrásokat használja.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Kiszolgálói szintű IP-tűzfalszabály létrehozása

 A SQL Database egy tűzfalat hoz létre a kiszolgáló szintjén az önálló és a készletezett adatbázisokhoz. Ez a tűzfal megakadályozza, hogy az ügyfélalkalmazások csatlakozzanak a kiszolgálóhoz vagy a hozzá tartozó adatbázisokhoz, kivéve, ha létrehoz egy IP-tűzfalszabály-szabályt a tűzfal megnyitásához. Az Azure-on kívüli IP-címről létesített kapcsolathoz hozzon létre egy tűzfalszabály egy adott IP-cím vagy címtartomány számára, amelyhez csatlakozni szeretne. A kiszolgálói szintű és az adatbázis szintű IP-tűzfalszabályok részletes ismertetését lásd: [kiszolgálói szintű és adatbázis-szintű IP-tűzfalszabályok](firewall-configure.md).

> [!NOTE]
> Az Azure SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni a kiszolgálóhoz, kivéve, ha az informatikai részleg megnyitja a 1433-es portot.
> [!IMPORTANT]
> A 0.0.0.0 tűzfalszabály lehetővé teszi az összes Azure-szolgáltatás számára a kiszolgálói szintű tűzfalszabály továbbítását, és megkísérli a kiszolgálón keresztül csatlakozni egy adatbázishoz.

Kövesse az alábbi lépéseket egy kiszolgálói szintű IP-tűzfalszabály létrehozásához az ügyfél IP-címéhez, és engedélyezze a külső kapcsolatot a Azure SQL Database tűzfalon keresztül csak az IP-címéhez.

1. Az [adatbázis](#prerequisites) központi telepítésének befejezése után válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd az SQL- **adatbázisok** lapon válassza a **mySampleDatabase** lehetőséget. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások.

2. Másolja ezt a teljes kiszolgálónevet, amelyet a kiszolgálóhoz és az adatbázisaihoz való csatlakozáskor használni szeretne más gyors útmutatókban.

   ![kiszolgáló neve](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. Válassza a **kiszolgáló tűzfal beállítása** lehetőséget az eszköztáron. Ekkor megnyílik a kiszolgáló **tűzfalbeállítások** lapja.

   ![kiszolgálói szintű IP-tűzfalszabály](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, és adja hozzá az aktuális IP-címet egy új kiszolgálói szintű IP-tűzfalszabály eléréséhez. A kiszolgálói szintű IP-tűzfalszabály egyetlen IP-cím vagy egy IP-címtartomány 1433-as portját nyithatja meg.

   > [!IMPORTANT]
   > Alapértelmezés szerint a Azure SQL Database tűzfalon keresztüli hozzáférés le van tiltva az összes Azure-szolgáltatás esetében. Ha engedélyezni szeretné az összes Azure-szolgáltatás elérését, válassza az ezen **a** lapon lehetőséget.
   >

5. Kattintson a **Mentés** gombra. A rendszer a kiszolgálón a 1433-as portot nyitja meg a jelenlegi IP-címhez.

6. A **tűzfalbeállítások** oldalának lezárása.

Ha SQL Server Management Studio vagy egy másik eszközt használ, a korábban létrehozott kiszolgálói rendszergazdai fiókkal csatlakozhat a kiszolgálóhoz és az adatbázisaihoz ebből az IP-címről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan milyen módokon csatlakozhat az adatbázishoz és végezhet róla lekérdezéseket. Ha azonban törölni szeretné az ebben a rövid útmutatóban létrehozott erőforrásokat, kövesse az alábbi lépéseket.

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza a **myResourceGroup** lehetőséget.
2. Az erőforráscsoport lapon válassza a **Törlés** lehetőséget, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- Most, hogy rendelkezik egy adatbázissal, [kapcsolódhat hozzá és lekérdezéseket hajthat végre rajta](connect-query-content-reference-guide.md) a kedvenc eszközeinek vagy nyelveinek egyikével, többek között
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Az első adatbázis megtervezésével, a táblák létrehozásával és az adatok beszúrásával kapcsolatos információkért tekintse meg az alábbi oktatóanyagok egyikét:
  - [Az első önálló adatbázis megtervezése Azure SQL Database a SSMS használatával](design-first-database-tutorial.md)
  - [Egyetlen adatbázis tervezése Azure SQL Databaseban és a C# és a ADO.NET közötti kapcsolat](design-first-database-csharp-tutorial.md)

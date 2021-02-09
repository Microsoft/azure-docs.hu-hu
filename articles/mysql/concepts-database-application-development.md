---
title: Alkalmazásfejlesztés – Azure Database for MySQL
description: Bevezeti azokat a tervezési szempontokat, amelyeket a fejlesztőnek követnie kell az alkalmazás kódjának a Azure Database for MySQLhoz való csatlakozásakor
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 45447a26c0455fc5945af8b8e9f7442af7facfbe
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830685"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Az alkalmazások fejlesztésének áttekintése Azure Database for MySQL 
Ez a cikk azokat a tervezési szempontokat ismerteti, amelyeket a fejlesztőnek követnie kell az alkalmazás kódjának Azure Database for MySQLhoz való csatlakozásakor. 

> [!TIP]
> A kiszolgálók létrehozását, kiszolgáló-alapú tűzfal létrehozását, a kiszolgáló tulajdonságainak megtekintését, az adatbázis létrehozását, valamint a Workbench és a mysql.exe használatával történő kapcsolódást és lekérdezést bemutató oktatóanyagért lásd: [az első Azure Database for MySQL-adatbázis megtervezése](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A kód mintáinak hivatkozásait itt találja: a [Azure Database for MySQLhoz való csatlakozáshoz használt kapcsolati kódtárak](concepts-connection-libraries.md)

## <a name="tools"></a>Eszközök
Azure Database for MySQL a MySQL-Közösség verzióját használja, amely kompatibilis a MySQL általános felügyeleti eszközeivel, például a Workbench vagy a MySQL segédprogrammal, mint például a mysql.exe, a [phpMyAdmin](https://www.phpmyadmin.net/), a [Navicat](https://www.navicat.com/products/navicat-for-mysql), [a dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/) és mások. Használhatja a Azure Portal, az Azure CLI és a REST API-kat is az adatbázis-szolgáltatással való kommunikációhoz.

## <a name="resource-limitations"></a>Erőforrás-korlátozások
A Azure Database for MySQL két különböző mechanizmus használatával kezeli a kiszolgálók számára elérhető erőforrásokat: 
- Erőforrások irányítása.
- Korlátok betartatása.

## <a name="security"></a>Biztonság
A Azure Database for MySQL erőforrásokat biztosít a hozzáférés korlátozásához, az adatok védelméhez, a felhasználók és a szerepkörök konfigurálásához, valamint a figyelési tevékenységek létrehozásához egy MySQL-adatbázisban.

## <a name="authentication"></a>Hitelesítés
Azure Database for MySQL támogatja a felhasználók és a bejelentkezések kiszolgálói hitelesítését.

## <a name="resiliency"></a>Rugalmasság
Ha átmeneti hiba történik egy MySQL-adatbázishoz való kapcsolódáskor, a kódnak újra kell próbálkoznia a hívással. Azt javasoljuk, hogy az újrapróbálkozási logika ne használja ki az újrapróbálkozási logikát, hogy az ne haladja meg az SQL-adatbázist, és egyszerre több ügyfél próbálkozzon újra.

- Mintakód: az újrapróbálkozási logikát szemléltető kód minták esetében lásd: minták a választott nyelvhez a következő helyen: [Azure Database for MySQLhoz való csatlakozáshoz használt kapcsolati kódtárak](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Kapcsolatok kezelése
Az adatbázis-kapcsolatok korlátozott erőforrások, ezért javasoljuk a kapcsolatok ésszerű használatát, amikor a MySQL-adatbázishoz fér hozzá a jobb teljesítmény érdekében.
- Hozzáférés az adatbázishoz kapcsolati készletezés vagy állandó kapcsolatok használatával.
- Hozzáférés az adatbázishoz rövid kapcsolati élettartam használatával. 
- Használja az újrapróbálkozási logikát az alkalmazásban a kapcsolódási kísérlet során az egyidejű kapcsolatok miatti hibák észlelése érdekében, elérte a maximálisan megengedett értéket. Az újrapróbálkozási logikában állítson be egy rövid késleltetést, majd várjon egy véletlenszerű időt a további csatlakozási kísérletek előtt.

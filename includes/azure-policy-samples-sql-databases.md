---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 314f29a1135e355597e890b72ef3c0b7372194e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227485"
---
### <a name="sql-databases"></a>SQL Database-adatbázisok

|  |  |
|---------|---------|
| [Engedélyezett termékváltozatok SQL-adatbázisokhoz](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Ehhez az SQL-adatbázisoknak egy jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy engedélyezett termékváltozat-azonosítókat tartalmazó tömböt vagy egy engedélyezett termékváltozat-neveket tartalmazó tömböt. |
| [Adatbázisszintű fenyegetésészlelési beállítás naplózása](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Naplózza az SQL-adatbázis biztonsági riasztási szabályzatait, ha a szabályzatok nem a megadott állapotra vannak beállítva. Meg kell adnia egy értéket annak meghatározásához, hogy a fenyegetésészlelésnek engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
| [SQL Database-adatbázis titkosításának naplózása](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Naplózza, ha az SQL Database-adatbázisban nincs engedélyezve a transzparens adattitkosítás. |
| [SQL-adatbázisszintű naplózási beállítás naplózása](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Naplózza az SQL-adatbázis naplózási beállításait, ha a beállítások eltérnek a megadott beállítástól. Meg kell adnia egy értéket annak meghatározásához, hogy a naplózási beállításnak engedélyezett vagy letiltott állapotúnak kell-e lennie.  |
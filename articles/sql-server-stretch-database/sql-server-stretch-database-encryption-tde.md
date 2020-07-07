---
title: Stretch Database transzparens adattitkosítás engedélyezése
description: Transzparens adattitkosítás (TDE) engedélyezése az Azure-beli SQL Server Stretch Databasehoz
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74034017"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Transzparens adattitkosítás (TDE) engedélyezése az Azure-beli Stretch Databasehoz
> [!div class="op_single_selector"]
> * [Azure Portalra](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

A transzparens adattitkosítás (TDE) segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, anélkül, hogy módosítani kellene az alkalmazást.

A TDE egy teljes adatbázis tartalmát titkosítja egy szimmetrikus kulcs – az adatbázis-titkosítási kulcs – használatával. Az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgáló tanúsítványa minden egyes Azure-kiszolgáló esetében egyedi. A Microsoft automatikusan elforgatja ezeket a tanúsítványokat legalább 90 naponta. A TDE általános ismertetését lásd: [transzparens adattitkosítás (TDE)].

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
Ha engedélyezni szeretné a TDE egy olyan Azure-adatbázishoz, amely a stretch-kompatibilis SQL Server-adatbázisból áttelepített adatok tárolására szolgál, tegye a következőket:

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Az **átlátszó adattitkosítási** lehetőség kiválasztása![][1]
4. Válassza a **be beállítást,** majd kattintson a **Mentés** gombra.
   ![][2]

## <a name="disabling-encryption"></a>Titkosítás letiltása
Ha le szeretné tiltani egy olyan Azure-adatbázis TDE, amely a stretch-kompatibilis SQL Server-adatbázisból áttelepített adatok tárolására szolgál, tegye a következőket:

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Az **átlátszó adattitkosítási** lehetőség kiválasztása
4. Válassza ki a **kikapcsolva** beállítást, majd kattintson a **Mentés** gombra.

<!--Anchors-->
[Transzparens adattitkosítás (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->

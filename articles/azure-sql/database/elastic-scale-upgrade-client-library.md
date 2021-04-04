---
title: Frissítés a legújabb rugalmas adatbázis-ügyféloldali könyvtárra
description: A NuGet használatával frissítse a rugalmas adatbázis ügyféloldali függvénytárát.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793415"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Alkalmazás frissítése a rugalmas adatbázis legújabb ügyféloldali függvénytárának használatára
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az [Elastic Database ügyféloldali kódtár](elastic-database-client-library.md) új verziói a NuGet és a NuGet csomagkezelő felületén keresztül érhetők el a Visual Studióban. A frissítések hibajavításokat és az ügyféloldali kódtár új képességeinek támogatását tartalmazzák.

**A legújabb verzióhoz:** Lépjen a [Microsoft. Azure. SqlDatabase. ElasticScale. Client webhelyre](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Hozza létre újra az alkalmazást az új könyvtárral, valamint módosítsa a meglévő, a Azure SQL Database adatbázisaiban tárolt, az új funkciók támogatásához használt szegmensek közötti Térkép-kezelő metaadatait.

A lépések végrehajtásával biztosíthatja, hogy az ügyféloldali kódtár régi verziói már nem jelennek meg a környezetben a metaadat-objektumok frissítésekor, ami azt jelenti, hogy a régebbi verziójú metaadat-objektumok nem jönnek létre a frissítés után.

## <a name="upgrade-steps"></a>Frissítési lépések

**1. frissítse alkalmazásait.** A Visual Studióban töltse le és hivatkozzon az ügyféloldali kódtár legújabb verziójára a könyvtárat használó összes fejlesztési projektben. Ezután hozza létre és telepítse újra.

* A Visual Studio-megoldásban válassza az **eszközök**  -->  **NuGet csomagkezelő**  -->   **NuGet-csomagok kezelése megoldást**.
* (Visual Studio 2013) A bal oldali panelen válassza a **frissítések** lehetőséget, majd válassza ki a **frissítés** gombot a csomagban **Azure SQL Database rugalmas skálázási ügyféloldali kódtár** , amely megjelenik az ablakban.
* (Visual Studio 2015) Állítsa be a szűrő mezőt az **elérhető frissítéshez**. Válassza ki a frissíteni kívánt csomagot, majd kattintson a **frissítés** gombra.
* (Visual Studio 2017) A párbeszédpanel tetején válassza a **frissítések** lehetőséget. Válassza ki a frissíteni kívánt csomagot, majd kattintson a **frissítés** gombra.
* Létrehozás és üzembe helyezés.

**2. frissítse a szkripteket.** Ha **PowerShell** -parancsfájlokat használ a szegmensek kezeléséhez, [töltse le az új könyvtár verzióját](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) , és másolja azt abba a könyvtárba, amelyből parancsfájlokat hajt végre.

**3. frissítse a Split-Merge szolgáltatást.** Ha a rugalmas adatbázis felosztása és egyesítése eszközt használja a töredezett adatmennyiség átrendezéséhez, [töltse le és telepítse az eszköz legújabb verzióját](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). A szolgáltatás részletes frissítési lépései [itt](elastic-scale-overview-split-and-merge.md)találhatók.

**4. frissítse a szegmens Map Manager adatbázisait**. Frissítse a szegmenses térképeket támogató metaadatokat Azure SQL Database.  Ezt kétféleképpen hajthatja végre a PowerShell vagy a C# használatával. Az alábbiakban mindkét beállítás látható.

***1. lehetőség: metaadatok frissítése a PowerShell használatával***

1. Töltse le a NuGet legújabb parancssori segédprogramját [, és mentse](https://nuget.org/nuget.exe) egy mappába.
2. Nyisson meg egy parancssort, navigáljon ugyanahhoz a mappához, és adja ki a következő parancsot: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navigáljon az imént letöltött új ügyfél-DLL-verziót tartalmazó almappába, például: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Töltse le a rugalmas adatbázis-ügyfél frissítési parancsfájlját a [parancsfájl-központból](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), és mentse a DLL-fájlt tartalmazó mappába.
5. Ebből a mappából futtassa a "PowerShell .\upgrade.ps1" parancsot a parancssorból, és kövesse az utasításokat.

***2. lehetőség: metaadatok frissítése a C használatával #***

Azt is megteheti, hogy létrehoz egy Visual Studio-alkalmazást, amely megnyitja a ShardMapManager, megismétli az összes szegmenst, és elvégzi a metaadatok frissítését úgy, hogy meghívja a [UpgradeLocalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) és a [UpgradeGlobalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) metódusokat a jelen példában látható módon

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

A metaadatok frissítésére szolgáló technikák többször is alkalmazhatók ártalmak nélkül. Ha például egy régebbi verziójú ügyfél véletlenül létrehoz egy szegmenst a már meglévő frissítés után, akkor a frissítést újra futtathatja az összes szegmensen, hogy a legújabb metaadat-verzió a teljes infrastruktúrában megtalálható legyen.

**Megjegyzés:**  Az ügyféloldali kódtár új verziói a mai napig továbbra is együttműködnek a szegmens Map Manager metaadatainak korábbi verzióival Azure SQL Databaseon, és fordítva.   A legújabb ügyfél új funkcióinak kihasználásához azonban frissíteni kell a metaadatokat.   Vegye figyelembe, hogy a metaadatok frissítése nem érinti a felhasználó-vagy alkalmazásspecifikus adatokat, csak a szegmenses Térkép-kezelő által létrehozott és használt objektumokat.  Az alkalmazások továbbra is működnek a fent ismertetett frissítési folyamaton keresztül.

## <a name="elastic-database-client-version-history"></a>Rugalmas adatbázis-ügyfél korábbi verziói

A korábbi verziókért keresse fel a [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
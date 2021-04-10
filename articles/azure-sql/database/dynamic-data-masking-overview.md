---
title: Dinamikus adatmaszkolás
description: A dinamikus adatmaszkolás korlátozza a bizalmas adatok megjelenítését azáltal, hogy a Azure SQL Database, az Azure SQL felügyelt példányának és az Azure szinapszis Analyticsnek nem Kiemelt felhasználók számára történő maszkolását végzi.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/25/2021
tags: azure-synpase
ms.openlocfilehash: b10b00e724324779eb753bfefccce77a5eb2a39d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918077"
---
# <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A Azure SQL Database, az Azure SQL felügyelt példánya és az Azure szinapszis Analytics támogatja a dinamikus adatmaszkolást. A dinamikus adatmaszkolás csökkenti a bizalmas adatok kitettségét azzal, hogy nem jogosult felhasználókhoz maszkolja őket. 

A dinamikus adatmaszkolás azzal segít megelőzni a bizalmas adatokhoz való jogosulatlan hozzáférést, hogy az ügyfél által meghatározhatóvá teszi az alkalmazásrétegre gyakorolt minimális következményekkel felfedhető bizalmas adatok menyiségét. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

Előfordulhat például, hogy egy Call Center egyik szolgáltatása azonosítja a hívót az e-mail-címük több karakterének megerősítésével, de a teljes e-mail-címet nem szabad felderíteni a szolgáltatás képviselőjének. Olyan maszkolási szabály is meghatározható, amely az összes e-mail-címet elrejti az összes lekérdezés eredmény-készletében. Egy másik példaként egy megfelelő adatmaszkot is meghatározhat a személyes adatainak védelme érdekében, így a fejlesztők a megfelelőségi rendeletek megszegése nélkül is lekérhetik a termelési környezeteket a hibaelhárításhoz.

## <a name="dynamic-data-masking-basics"></a>Dinamikus adatmaszkolás alapjai

A dinamikus adatmaszkolási házirend beállításához a Azure Portal a **dinamikus adatmaszkolás** panelt a **Biztonság** elemre kattintva a SQL Database konfigurációs ablaktáblán. Ez a funkció nem állítható be a portálon a felügyelt SQL-példányhoz (PowerShell vagy REST API használata). További információ: [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking).

### <a name="dynamic-data-masking-policy"></a>Dinamikus adatmaszkolási szabályzat

* A **maszkolásból kizárt SQL-felhasználók** – olyan SQL-felhasználók vagy Azure ad-identitások összessége, amelyek az SQL-lekérdezés eredményeiben nem maszkolt adatokkal rendelkeznek. A rendszergazdai jogosultságokkal rendelkező felhasználók mindig ki vannak zárva a maszkolásból, és az eredeti adatok maszk nélkül jelennek meg.
* **Maszkolási szabályok** – a kijelölt mezőket és a használni kívánt maszkolási függvényt meghatározó szabályok halmaza. A kijelölt mezők az adatbázis-séma neve, a tábla neve és az oszlop neve alapján definiálhatók.
* **Maszkolási függvények** – olyan metódusok összessége, amelyek különböző forgatókönyvek esetében szabályozzák az adatok kitettségét.

| Maszkolási függvény | Maszkolási logika |
| --- | --- |
| **Alapértelmezett** |**Teljes maszkolás a kijelölt mezők adattípusainak megfelelően**<br/><br/>• Az XXXX vagy kevesebb XS használata, ha a mező mérete kisebb, mint 4 karakter a sztring adattípusok esetében (NCHAR, ntext, nvarchar).<br/>• Nulla értéket használhat a numerikus adattípusokhoz (bigint, bit, decimális, int, Money, numerikus, smallint, túlcsordulási, tinyint, float, Real).<br/>• Az 01-01-1900-as dátum/idő adattípusok (dátum, datetime2, datetime, DateTimeOffset, idő adattípusúra, Time) használata.<br/>• Az SQL Variant esetében az aktuális típus alapértelmezett értéke lesz használatban.<br/>• XML esetén a rendszer a dokumentumot \<masked/> használja.<br/>• Üres értéket használhat a speciális adattípusokhoz (timestamp-tábla, hierarchyid, GUID, Binary, képek, varbinary térbeli típusok). |
| **Bankkártya** |**Maszkolási módszer, amely a kijelölt mezők utolsó négy számjegyét teszi elérhetővé** , és egy konstans karakterláncot hoz létre egy hitelkártya formájában.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maszkolási módszer, amely az első betűt teszi elérhetővé, és lecseréli a tartományt a xxx.com** egy állandó karakterlánc-előtaggal e-mail-cím formájában.<br/><br/>aXX@XXXX.com |
| **Véletlenszerű szám** |**Maszkolási módszer, amely** a kiválasztott határok és a tényleges adattípusok alapján véletlenszerűen generált számot hoz létre. Ha a kijelölt határok egyenlőek, akkor a maszkolási függvény állandó szám.<br/><br/>![A véletlenszerű számok generálására szolgáló maszkolási módszert bemutató képernyőkép.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Egyéni szöveg** |**Maszkolási módszer, amely az első és az utolsó karaktert teszi elérhetővé** , és egy egyéni kitöltési karakterláncot helyez el a közepén. Ha az eredeti sztring rövidebb a megjelenő előtagnál és utótagnál, csak a kitöltés karakterláncot használja a rendszer. <br/>előtag [kitöltés] utótag<br/><br/>![Navigációs panel](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Maszk ajánlott mezői

A DDM-javaslatok motorja az adatbázis egyes mezőit potenciálisan bizalmas mezőként adja meg, ami valószínűleg a maszkoláshoz is jó választás lehet. A portál dinamikus adatmaszkolás paneljén látni fogja az adatbázis ajánlott oszlopait. Mindössze annyit kell tennie, hogy a **maszk hozzáadása** egy vagy több oszlophoz lehetőséget választja, majd a **Mentés** gombra kattint a mezőkhöz tartozó maszk alkalmazásához.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz PowerShell-parancsmagok használatával

### <a name="data-masking-policies"></a>Adatmaszkolási szabályzatok

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Adatmaszkolási szabályok

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [Új – AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz a REST API használatával

A REST API használatával programozott módon kezelheti az adatmaszkolási szabályzatot és a szabályokat. A közzétett REST API a következő műveleteket támogatja:

### <a name="data-masking-policies"></a>Adatmaszkolási szabályzatok

- [Létrehozás vagy frissítés](/rest/api/sql/datamaskingpolicies/createorupdate): adatbázis-adatmaszkolási szabályzat létrehozása vagy frissítése.
- Get: adatbázis-adatmaszkolási házirend [beolvasása](/rest/api/sql/datamaskingpolicies/get). 

### <a name="data-masking-rules"></a>Adatmaszkolási szabályok

- [Létrehozás vagy frissítés](/rest/api/sql/datamaskingrules/createorupdate): adatbázis-adatmaszkolási szabály létrehozása vagy frissítése.
- [Listázás adatbázis szerint](/rest/api/sql/datamaskingrules/listbydatabase): lekéri az adatbázis-adatmaszkolási szabályok listáját.

## <a name="permissions"></a>Engedélyek

A dinamikus adatmaszkolás konfigurálható a Azure SQL Database-rendszergazda, a kiszolgáló rendszergazdája vagy a szerepköralapú hozzáférés-vezérlés (RBAC) [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) szerepkör használatával.

## <a name="next-steps"></a>Következő lépések

[Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)

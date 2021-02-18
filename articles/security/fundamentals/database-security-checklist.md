---
title: Az Azure Database biztonsági ellenőrzőlistája | Microsoft Docs
description: Az Azure Database biztonsági ellenőrzőlistájának használatával megtekintheti a fontos felhő-számítástechnikai biztonsági problémákat.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 80455b442bbfb9c8a7d40799b2ddd5fc25460578
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595584"
---
# <a name="azure-database-security-checklist"></a>Az Azure Database biztonsági ellenőrzőlistája

A biztonság növelése érdekében az Azure Database számos beépített biztonsági vezérlőt tartalmaz, amelyek segítségével korlátozható és szabályozható a hozzáférés.

Ezek a következők:

-    Tűzfal, amely lehetővé teszi, hogy az IP-cím alapján korlátozza a kapcsolatra [vonatkozó tűzfalszabályok](../../azure-sql/database/firewall-configure.md) létrehozását,
-    A Azure Portal elérhető kiszolgáló szintű tűzfal
-    SSMS elérhető adatbázis-szintű tűzfalszabályok
-    Biztonságos kapcsolódás az adatbázishoz biztonságos kapcsolati karakterláncok használatával
-    Hozzáférés-kezelés használata
-    Adattitkosítás
-    Az SQL Database naplózási funkciója
-    SQL Database fenyegetések észlelése

## <a name="introduction"></a>Bevezetés
A felhő-számítástechnika olyan új biztonsági paradigmokat igényel, amelyek számos alkalmazás-felhasználó, adatbázis-rendszergazda és programozó számára ismeretlenek. Ennek eredményeképpen néhány szervezet vonakodik az adatkezeléshez szükséges felhőalapú infrastruktúra megvalósításában, az észlelt biztonsági kockázatok miatt. A probléma nagy része azonban a Microsoft Azureba beépített biztonsági funkciók jobb megismerésével és Microsoft Azure SQL Databaseával is enyhíthető.

## <a name="checklist"></a>Ellenőrzőlista
Javasoljuk, hogy az ellenőrzőlista áttekintése előtt olvassa el az [Azure-adatbázis biztonsági eljárásairól](../../azure-sql/database/security-best-practice.md)  szóló cikket. Az ajánlott eljárások megismerése után a lehető legtöbbet hozhatja ki ebből az ellenőrzőlistából. Ezt a feladatlistát követve meggyőződhet arról, hogy az Azure Database biztonságával kapcsolatos fontos problémák merültek fel.


|Ellenőrzőlista kategóriája| Description|
| ------------ | -------- |
|**Az adatvédelem**||
| <br> Titkosítás a mozgásban/átvitelben| <ul><li>[Transport Layer Security](/windows-server/security/tls/transport-layer-security-protocol)az adattitkosításhoz, amikor az adatátvitelt a hálózatokra helyezi.</li><li>Az adatbázishoz biztonságos kommunikáció szükséges az ügyfelektől a (z) [TDS (táblázatos adatfolyam)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) protokollon keresztül a TLS protokoll (Transport Layer Security) alapján.</li></ul> |
|<br>Titkosítás inaktív állapotban| <ul><li>[Transzparens adattitkosítás](../../azure-sql/database/transparent-data-encryption-tde-overview.md), ha az inaktív adattárolást fizikailag bármilyen digitális formában tárolják.</li></ul>|
|**Hozzáférés vezérlése**||  
|<br> Adatbázis-hozzáférés | <ul><li>A [hitelesítési](../../azure-sql/database/logins-create-manage.md) (Azure Active Directory hitelesítés) ad-hitelesítés Azure Active Directory által felügyelt identitásokat használ.</li><li>Az [engedélyezési](../../azure-sql/database/logins-create-manage.md) jogosultsággal rendelkező felhasználóknak a legkevésbé szükséges jogosultságokkal kell rendelkezniük.</li></ul> |
|<br>Alkalmazás-hozzáférés| <ul><li>A [sor szintjének biztonsága](/sql/relational-databases/security/row-level-security) (a biztonsági házirend használatával egyidejűleg a felhasználó identitása, szerepköre vagy végrehajtási környezete alapján korlátozza a sor szintű hozzáférést).</li><li>[Dinamikus adatmaszkolás](../../azure-sql/database/dynamic-data-masking-overview.md) (engedélyezési & házirend használata, a bizalmas adatok expozíciójának korlátozása a nem privilegizált felhasználóknak való maszkolással)</li></ul>|
|**Proaktív figyelés**||  
| <br>& észlelésének nyomon követése| <ul><li>A [naplózás](../../azure-sql/database/auditing-overview.md) nyomon követi az adatbázis eseményeit, és az [Azure Storage-fiókban](../../storage/common/storage-account-create.md)lévő naplóba/tevékenység naplóba írja azokat.</li><li>Az Azure-adatbázis állapotának nyomon követése [Azure monitor tevékenységi naplók](../../azure-monitor/essentials/platform-logs-overview.md)használatával.</li><li>A [veszélyforrások észlelése](../../azure-sql/database/threat-detection-configure.md) rendellenes adatbázis-tevékenységeket észlel, ami az adatbázis potenciális biztonsági fenyegetéseket jelez. </li></ul> |
|<br>Azure Security Center| <ul><li>[Adatfigyelés](../../security-center/security-center-remediate-recommendations.md) A Azure Security Center központi biztonsági monitorozási megoldásként használható az SQL és más Azure-szolgáltatások számára.</li></ul>|        

## <a name="conclusion"></a>Összegzés
Az Azure Database egy robusztus adatbázis-platform, amely a számos szervezeti és szabályozási megfelelőségi követelménynek megfelelő biztonsági funkciók széles skáláját tartalmazza. Az adatvédelmet az adataihoz való fizikai hozzáférés szabályozásával, valamint a fájl-, oszlop-vagy sorcsoport szintjén, transzparens adattitkosítás, Cell-Level titkosítással vagy Row-Level biztonsággal számos különböző lehetőség használatával kezelheti. A Always Encrypted a titkosított adatok elleni műveleteket is lehetővé teszi, leegyszerűsítve az alkalmazások frissítéseinek folyamatát. A SQL Database tevékenység naplózási naplóihoz való hozzáférés pedig a szükséges információkat nyújtja, így megtudhatja, hogyan és mikor férhet hozzá az adatokhoz.

## <a name="next-steps"></a>Következő lépések
Mindössze néhány lépés végrehajtásával fokozhatja az adatbázis védelmét a rosszindulatú felhasználókkal és a jogosulatlan hozzáféréssel szemben. Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

- [Tűzfalszabályok](../../azure-sql/database/firewall-configure.md) beállítása a kiszolgálóhoz és az adatbázishoz.
- Az adatainak védelme [titkosítással](/sql/relational-databases/security/encryption/sql-server-encryption).
- [SQL Database naplózás](../../azure-sql/database/auditing-overview.md)engedélyezése.
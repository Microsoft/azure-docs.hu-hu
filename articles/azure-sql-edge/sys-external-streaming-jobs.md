---
title: sys.external_streaming_jobs (Transact-SQL) – Azure SQL Edge
description: Tudnivalók a sys.external_streaming_jobs használatáról az Azure SQL Edge-ben
keywords: sys.external_streaming_jobs, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90900343"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Az adatbázis hatókörében létrehozott összes külső adatfolyam-feladatsor sorát adja vissza.

|Oszlop neve|Adattípus|Leírás|  
|-----------------|---------------|-----------------|
|**név**|**rendszerneve**|Az adatfolyam neve. Egyedi a adatbázison belül.|
|**object_id**|**int**|az adatfolyam-objektumhoz tartozó objektum-azonosító szám. Egyedi a adatbázison belül.|
|**principal_id**|**int**|A szerelvényt birtokló rendszerbiztonsági tag azonosítója|
|**schema_id**|**int**| Az objektumot tartalmazó séma azonosítója.|
|**parent_object_id**|**id**| az adatfolyamhoz tartozó szülőobjektum objektum-azonosító száma. Az aktuális implementációban ez az érték mindig null|
|**típusa**|**char (2)**|Objektumtípus. Stream-objektumok esetében a típus mindig "EJ".|
|**type_desc**|**nvarchar (60)**| Az objektumtípus leírása. Stream-objektumok esetében a típus mindig "EXTERNAL_STREAMING_JOB"|
|**create_date**|**dátum/idő**| Az objektum létrehozásának dátuma.|
|**modify_date**|**dátum/idő**| Az aktuális implementációban ez az érték ugyanaz, mint az adatfolyam-objektum create_date |
|**is_ms_shipped**|**bit**| Egy belső összetevő által létrehozott objektum.|  
|**is_published**|**bit**| Az objektum közzé van téve.|  
|**is_schema_published**|**bit**|Csak az objektum sémája lesz közzétéve.|
|**uses_ansi_nulls**|**bit**| A Stream objektum a ANSI_NULLS adatbázis beállítása beállítással lett létrehozva|
|**nyilatkozat**|**varchar(max)**| A stream Analytics lekérdezési szövege a folyamatos átviteli feladatokhoz. További információ: [sp_create_streaming_job](overview.md) |
|**állapota**|**int**| A folyamatos átviteli feladatok aktuális állapota. A lehetséges értékek a következők <br /><br /> **Létrehozva** = 0. A folyamatos átviteli feladatot létrehozták, de még nem indult el. <br /><br /> **Kezdő** = 1. A folyamatos átviteli feladatok a kezdeti fázisban vannak. <br /><br /> **Sikertelen** = 6. A folyamatos átviteli feladatot nem sikerült végrehajtani. Ez általában egy végzetes hibát jelez a feldolgozás során. <br /><br /> **Leállítva** = 4. A folyamatos átviteli feladatot leállították. <br /><br /> **Tétlen** = 7. A folyamatos átviteli feladatot futtatja, de nincs feldolgozható bemenet. <br /><br /> **Feldolgozás** = 8. A folyamatos átviteli feladatot futtatja, és feldolgozza a bemeneteket. Ez az állapot a folyamatos átviteli feladatokhoz tartozó kifogástalan állapotot jelzi. <br /><br /> **Csökkentett teljesítmény** = 9. A folyamatos átviteli feladatok futtatása folyamatban van, de néhány nem végzetes bemeneti/kimeneti szerializálási/deszerializálási hiba történt a bemeneti feldolgozás során. A bemeneti feladatot a rendszer továbbra is futtatja, de a hibákkal kapcsolatos bemenetek eldobása is megtörténik.|

## <a name="permissions"></a>Engedélyek

A metaadatoknak a katalógus nézeteiben való láthatósága azon biztonságos elemek migrálására korlátozódik, amelyeknek a felhasználó tulajdonában van, vagy amelyeken a felhasználó engedélyt kapott. További információ: [metaadatok láthatóságának konfigurálása](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Lásd még

- [T-SQL streaming Catalog-nézetek](overview.md)
- [Katalógus nézetei (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Rendszernézetek (Transact-SQL)](/sql/t-sql/language-reference/)


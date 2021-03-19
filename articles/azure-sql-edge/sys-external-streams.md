---
title: sys.external_streams (Transact-SQL) – Azure SQL Edge
description: Tudnivalók a sys.external_streams használatáról az Azure SQL Edge-ben
keywords: sys.external_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90900311"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Az adatbázis hatókörében létrehozott minden külső adatfolyam-objektum sorát adja vissza.

|Oszlop neve|Adattípus|Leírás|  
|-----------------|---------------|-----------------|
|**név**|**rendszerneve**|Az adatfolyam neve. Egyedi a adatbázison belül.|
|**object_id**|**int**|az adatfolyam-objektumhoz tartozó objektum-azonosító szám. Egyedi a adatbázison belül.|
|**principal_id**|**int**|A szerelvényt birtokló rendszerbiztonsági tag azonosítója|
|**schema_id**|**int**| Az objektumot tartalmazó séma azonosítója.|
|**parent_object_id**|**id**| az adatfolyamhoz tartozó szülőobjektum objektum-azonosító száma. Az aktuális implementációban ez az érték mindig null|
|**típusa**|**char (2)**|Objektumtípus. Stream-objektumok esetében a típus mindig "ES"|
|**type_desc**|**nvarchar (60)**| Az objektumtípus leírása. Stream-objektumok esetében a típus mindig "EXTERNAL_STREAM"|
|**create_date**|**dátum/idő**| Az objektum létrehozásának dátuma.|
|**modify_date**|**dátum/idő**| Az objektum utolsó módosításának dátuma ALTER utasítás használatával.|
|**is_ms_shipped**|**bit**| Egy belső összetevő által létrehozott objektum.|  
|**is_published**|**bit**|Az objektum közzé van téve.|  
|**is_schema_published**|**bit**|Csak az objektum sémája lesz közzétéve.|
|**max_column_id_used**|**bit**| Ez az oszlop belső célokra szolgál, és a jövőben el lesz távolítva|  
|**uses_ansi_nulls**|**bit**| A Stream objektum a ANSI_NULLS adatbázis beállítása beállítással lett létrehozva|
|**data_source_id**|**int**| A Stream objektum által képviselt külső adatforrás azonosítója |  
|**file_format_id**|**int**| Az adatfolyam-objektum által használt külső fájlformátumhoz tartozó objektumazonosító. A külső fájl formátuma a külső stream által hivatkozott adatok tényleges elrendezésének megadásához szükséges.| 
|**helyen**|**varchar(max)**| A külső Stream objektum célpontja. További információt a [külső stream létrehozása](overview.md) című témakörben talál. |
|**input_option**|**varchar(max)**| A külső stream létrehozásakor használt beviteli beállítások. További információt a [külső stream létrehozása](overview.md) című témakörben talál. |
|**output_option**|**varchar(max)**| A külső adatfolyam létrehozásakor használt kimeneti beállítások. További információt a [külső stream létrehozása](overview.md) című témakörben talál. | 

## <a name="permissions"></a>Engedélyek

A metaadatoknak a katalógus nézeteiben való láthatósága azon biztonságos elemek migrálására korlátozódik, amelyeknek a felhasználó tulajdonában van, vagy amelyeken a felhasználó engedélyt kapott. További információ: [metaadatok láthatóságának konfigurálása](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Lásd még

- [Katalógus nézetei (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Rendszernézetek (Transact-SQL)](/sql/t-sql/language-reference/)

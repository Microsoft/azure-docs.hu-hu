---
title: Lekérdezéstár – ajánlott eljárások a Azure Database for PostgreSQL – Flex Server
description: Ez a cikk a Lekérdezéstár ajánlott eljárásait ismerteti a Azure Database for PostgreSQL - Flex Serverben.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559112"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Ajánlott eljárások a rugalmas lekérdezéstárhoz

**A következőkre vonatkozik:** Azure Database for PostgreSQL – A Flex Server 11., 12-es verziója

Ez a cikk a Lekérdezéstár használatának ajánlott eljárásait ismerteti Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Az optimális lekérdezésrögzítési mód beállítása
Hagyja, hogy a Lekérdezéstár rögzítse az Ön számára fontos adatokat. 

|**pg_qs.query_capture_mode** | **Forgatókönyv**|
|---|---|
|_Mind_  |Alaposan elemezze a számítási feladatokat az összes lekérdezés, a végrehajtásuk gyakoriságának és az egyéb statisztikák alapján. Új lekérdezések azonosítása a számítási feladatban. Annak észlelése, hogy az alkalmi lekérdezésekkel azonosíthatók-e a felhasználó- vagy automatikus paraméterezés lehetőségei. _Mindegyikhez_ magasabb erőforrás-felhasználási költség is jár. |
|_Top_  |Összpontosítson a legfontosabb lekérdezésekre – az ügyfelek által kiadott lekérdezésekre.
|_Nincs_ |Ha a None (Nincs) beállítás van beállítva, a Lekérdezéstár nem rögzít új lekérdezéseket. Már rögzített egy lekérdezéskészletet és egy időablakot, amit meg szeretne vizsgálni, és szeretné kiküszöbölni a zavaró tényezőket, amelyek más lekérdezések miatt előfordulhatnak. _A None_ (Egyik sem) a tesztelési és a jelölőkörnyezetekhez alkalmas. _Egyiket_ sem szabad körültekintően használni, mivel előfordulhat, hogy nem tudja nyomon követni és optimalizálni a fontos új lekérdezéseket. |


> [!NOTE] 
> **pg_qs.query_capture_mode** a **pgms_wait_sampling.query_capture_mode adatokat váltja fel.** Ha pg_qs.query_capture_mode _nincs,_ a pgms_wait_sampling.query_capture_mode beállításnak nincs hatása. 


## <a name="keep-the-data-you-need"></a>A szükséges adatok megtartása
A **pg_qs.retention_period_in_days** paraméter napokban határozza meg a lekérdezéstár adatmegőrzési időszakát. A régebbi lekérdezési és statisztikai adatok törlődnek. Alapértelmezés szerint a Lekérdezéstár úgy van konfigurálva, hogy 7 napig őrizze meg az adatokat. Ne őrizze meg a nem használni tervben található előzményadatokat. Növelje az értéket, ha tovább kell tartania az adatokat.


## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan olvashat be vagy állíthat be paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI használatával.](howto-configure-server-parameters-using-cli.md)

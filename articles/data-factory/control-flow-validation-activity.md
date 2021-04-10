---
title: Érvényesítési tevékenység a Azure Data Factoryban
description: Az érvényesítési tevékenység nem folytatja a folyamat végrehajtását mindaddig, amíg a felhasználó által megadott feltételekkel ellenőrzi a csatolt adatkészletet.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 2c5208f754e66f92cf5019fdad3026decac88284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785954"
---
# <a name="validation-activity-in-azure-data-factory"></a>Érvényesítési tevékenység a Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A folyamat érvényesítésével biztosíthatja, hogy a folyamat csak akkor folytassa a végrehajtást, ha a csatolt adatkészlet hivatkozása már létezik, hogy az megfelel a megadott feltételeknek, vagy elérte az időtúllépést.


## <a name="syntax"></a>Syntax

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
name | Az "Érvényesítés" tevékenység neve | Sztring | Yes |
típus | **Érvényesítésre** kell beállítani. | Sztring | Yes |
adatkészlet | A tevékenység letiltja a végrehajtást, amíg nem ellenőrizte, hogy az adatkészlet-hivatkozás létezik, és megfelel a megadott feltételeknek, vagy elérte az időtúllépést. A megadott adatkészletnek támogatnia kell a "MinimumSize" vagy a "ChildItems" tulajdonságot. | Adatkészlet-hivatkozás | Yes |
timeout | Megadja a futtatni kívánt tevékenység időtúllépését. Ha nincs megadva érték, az alapértelmezett érték 7 nap ("7.00:00:00"). A formátum d. óó: PP: SS | Sztring | No |
aludni | Az érvényesítési kísérletek közötti késleltetés másodpercben. Ha nincs megadva érték, az alapértelmezett érték 10 másodperc. | Egész szám | No |
childItems | Ellenőrzi, hogy a mappában van-e alárendelt elem. Állítható be a-True értékre: Ellenőrizze, hogy a mappa létezik-e, és hogy van-e elemek. A rendszer letiltja, amíg legalább egy elem szerepel a mappában, vagy elérte az időtúllépési értéket.-FALSE: Ellenőrizze, hogy a mappa létezik-e, és hogy üres-e. Blokkolja a mappa üres vagy az időtúllépési érték eléréséig. Ha nincs megadva érték, a tevékenység letiltja, amíg a mappa nem létezik, vagy amíg az időtúllépés el nem éri. | Logikai | No |
minimumSize | A fájl minimális mérete bájtban megadva. Ha nincs megadva érték, az alapértelmezett érték 0 bájt | Egész szám | Nem |


## <a name="next-steps"></a>Következő lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)

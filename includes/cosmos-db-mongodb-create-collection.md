---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 37cdb6b466417add8dae69464304ce2f32247c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564276"
---
Most már használhatja a Azure Portal Adatkezelő eszközét egy Azure Cosmos DB API MongoDB-adatbázis és-tároló létrehozásához. 

1. Válassza ki **adatkezelő**  >  **új tárolót**. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Az Azure Portal Adatkezelője a Tároló hozzáadása panellel":::

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|db|Adja *meg az adatbázist az új* adatbázis neveként. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?` szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Átviteli sebesség**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. Kiválaszthatja az [autoskálázási módot](../articles/cosmos-db/provision-throughput-autoscale.md)is, amely az olyan ru/s-ket is megadja, amelyek szükség szerint dinamikusan növekednek és csökkennek.| 
    |**Gyűjtemény azonosítója**|Coll|Adja meg `coll` az új tároló nevét. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Tárkapacitás**|Rögzített (10 GB)|Adja meg a *rögzített (10gb)* értéket ehhez az alkalmazáshoz. Ha a *korlátlan* lehetőséget választja, létre kell hoznia egy `Shard Key` elemet, amelyhez a beszúrt elemek szükségesek.|
    |**Szilánk kulcsa**| /_id| A cikkben ismertetett minta nem használ szegmens kulcsot, ezért a  *_id* az automatikusan generált azonosító mezőt fogja használni a szegmens kulcsaként. További információ a particionálásról, más néven particionálásról [Azure Cosmos db](../articles/cosmos-db/partitioning-overview.md)|
        
    Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.
---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 076afcfc8abf4e90d3ccedbb26653656032e59be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85115387"
---
Most már használhatja a Azure Portal Adatkezelő eszközét egy adatbázis és egy tároló létrehozásához. 

1. Válassza ki **adatkezelő**  >  **új tárolót**. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Az Azure Portal Adatkezelője a Tároló hozzáadása panellel":::

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?` szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Átviteli sebesség**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Adja meg az *elemeket* az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben ismertetett minta a */category* használja a partíciós kulcsként.|
    
    Az előző beállításokon kívül opcionálisan hozzáadhat **egyedi kulcsokat** a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. A tárolók létrehozásakor egyedi kulcsokra vonatkozó szabályzat létrehozásával biztosíthatja, hogy a partíciós kulcs egy vagy több értéke egyedi legyen. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](../articles/cosmos-db/unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.
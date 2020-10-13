---
title: Azure Event Hubs séma-beállításjegyzék létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy séma-beállításjegyzéket egy Azure Event Hubs-névtérben.
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 90556e0843cda94dc79330321d027f8d28eb7d20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652193"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Azure Event Hubs séma-beállításjegyzék létrehozása (előzetes verzió)
Ez a cikk bemutatja, hogyan hozhat létre sémákkal rendelkező sémákat az Azure Event Hubs által üzemeltetett séma-beállításjegyzékben. Az Azure Event Hubs séma-beállításjegyzékbeli funkciójának áttekintését lásd: [Az Azure Schema Registry in Event Hubs](schema-registry-overview.md).

> [!NOTE]
> - A **séma-beállításjegyzék** szolgáltatás jelenleg **előzetes**verzióban érhető el, és éles számítási feladatokhoz nem ajánlott.
> - A szolgáltatás csak **standard** és **dedikált** szinten érhető el, nem az alapszintű **csomagokban** .

## <a name="prerequisites"></a>Előfeltételek
[Hozzon létre egy Event Hubs névteret](event-hubs-create.md#create-an-event-hubs-namespace). Használhat egy meglévő névteret is. 

## <a name="create-a-schema-group"></a>Séma-csoport létrehozása
1. Navigáljon a **Event Hubs névtér** lapra. 
1. Válassza ki a **séma beállításjegyzékét** a bal oldali menüben. Séma csoport létrehozásához válassza a **+ séma csoportot** az eszköztáron. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Séma beállításjegyzékének lapja":::
1. A **séma létrehozása** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg a séma csoport **nevét** .
    1. A **szerializálási típushoz**válasszon olyan szerializálási formátumot, amely a séma csoport összes sémájára érvényes. Jelenleg a **Avro** az egyetlen támogatott típus, ezért válassza a **Avro**lehetőséget. 
    1. Válassza ki a csoport összes sémájának **kompatibilitási módját** . A **Avro**esetében a továbbítás és a visszamenőleges kompatibilitási módok támogatottak. 
    1. Ezután válassza a **Létrehozás** elemet a séma csoport létrehozásához. 
1. Válassza ki a **séma csoport** nevét a séma-csoportok listájában.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="Séma beállításjegyzékének lapja":::    
1. Megjelenik a csoport **sémakezelő** lapja.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Séma beállításjegyzékének lapja":::
    

## <a name="add-a-schema-to-the-schema-group"></a>Séma hozzáadása a séma csoportjához
Ebben a szakaszban egy sémát ad hozzá a séma csoportjához a Azure Portal használatával. 

1. A **séma csoport** lapon válassza a **+ séma** elemet az eszköztáron. 
1. A **séma létrehozása** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg a séma **nevét** .
    1. Adja meg a következő **sémát** a szövegmezőben. A sémával rendelkező fájlt is kiválaszthatja.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. Kattintson a **Létrehozás** gombra. 
1. Válassza ki a **sémát** a sémák listájából. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Séma beállításjegyzékének lapja":::
1. Megjelenik a séma **áttekintő** lapja a sémához. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Séma beállításjegyzékének lapja":::    
1. Ha a séma több verziója is létezik, a **verziók** legördülő listában láthatók. Válassza ki az adott verzióra váltani kívánt verziót. 

## <a name="create-a-new-version-of-schema"></a>A séma új verziójának létrehozása

1. Frissítse a sémát a szövegmezőben, és válassza az **Érvényesítés**lehetőséget. A következő példában egy új mező `id` lett hozzáadva a sémához. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Séma beállításjegyzékének lapja":::    
    
1. Tekintse át az érvényesítési állapotot és a módosításokat, majd kattintson a **Mentés**gombra. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Séma beállításjegyzékének lapja":::     
1. Ekkor megjelenik a `2` **séma áttekintő** oldalának **verziószáma** . 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Séma beállításjegyzékének lapja":::    
1. Válassza ki a `1` séma 1. verzióját. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Séma beállításjegyzékének lapja":::    


## <a name="next-steps"></a>Következő lépések
A séma beállításjegyzékével kapcsolatos további információkért lásd: [Azure Schema Registry in Event Hubs](schema-registry-overview.md).


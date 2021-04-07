---
title: Objektumok és erőforrások élettartama
description: A különböző típusú élettartam-kezelés ismertetése
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593891"
---
# <a name="object-and-resource-lifetime"></a>Objektumok és erőforrások élettartama

Az Azure távoli renderelés két típust különböztet meg: az **objektumokat** és az **erőforrásokat**.

## <a name="object-lifetime"></a>Objektum élettartama

Az *objektumok* olyan dolgoknak tekintendők, amelyeket a felhasználó saját belátása szerint hozhat létre, módosíthat és semmisít meg. Az objektumok szabadon duplikálható, és az egyes példányok időbeli változékonysága is változhat. Ennek következtében az [entitások](entities.md) és az [összetevők](components.md) objektumok.

Az objektumok élettartama teljes mértékben a felhasználói vezérlés alatt áll. Nem kapcsolódik az ügyféloldali ábrázolás élettartamához, azonban. A (z) és a (z `Entity` `Component` `Destroy` ) függvényt úgy kell meghívni, hogy a távoli renderelési gazdagépen felszabadítsa az objektumot. Emellett `Entity.Destroy()` a megsemmisíti az entitást, annak gyermekeit és a hierarchiában lévő összes összetevőt.

## <a name="resource-lifetime"></a>Erőforrás élettartama

Az *erőforrások* olyan dolgok, amelyek élettartamát teljes mértékben a távoli renderelési gazdagép kezeli. Az erőforrásokra vonatkozó hivatkozás belsőleg van megszámolva. Ha már senki nem hivatkozik rájuk, akkor a lefoglalásra kerül.

A legtöbb erőforrás csak közvetett módon hozható létre, általában egy fájlból való betöltéssel. Ha ugyanaz a fájl többször is be van töltve, az Azure távoli renderelés ugyanezt a hivatkozást fogja visszaadni, és nem tölti be újra az adatfájlokat.

Számos erőforrás nem változtatható, például a [Rácsvonalak](meshes.md) és a [textúrák](textures.md). Bizonyos erőforrások változékonyak, de például [anyagok](materials.md). Mivel az erőforrások gyakran meg vannak osztva, az erőforrások módosítása több objektumra is hatással lehet. Például az anyagok színének módosítása megváltoztatja az összes olyan objektum színét, amely rácsvonalat használ, ami viszont az anyagot is megadja.

### <a name="built-in-resources"></a>Beépített erőforrások

Az Azure Remote rendering tartalmaz néhány beépített erőforrást, amelyek a megfelelő azonosítójuk alapján tölthetők be `builtin://` a hívása során `RenderingSession.Connection.LoadXYZAsync()` . Az elérhető beépített erőforrások az egyes szolgáltatások dokumentációjában szerepelnek. Az [égbolt szakasz](../overview/features/sky.md) például felsorolja a beépített Sky-textúrákat.

## <a name="general-lifetime"></a>Általános élettartam

Az összes objektum és erőforrás élettartama a kapcsolódáshoz van kötve. A kapcsolat bontásakor a rendszer elveti az összeset. Amikor újra csatlakozik ugyanahhoz a munkamenethez, a jelenet gráf üres lesz, és az összes erőforrás törlődik.

A gyakorlatban a leválasztást követően ugyanazt az erőforrást egy munkamenetbe tölti be, ami általában gyorsabb, mint az első alkalommal. Ez azért van így, mert a legtöbb erőforrást először le kell tölteni az Azure Storage-ból, ami nem szükséges a második alkalommal, így jelentős mennyiségű időt takaríthat meg.

## <a name="next-steps"></a>Következő lépések

* [Entitások](entities.md)
* [Összetevők](components.md)
* [Modellek](models.md)

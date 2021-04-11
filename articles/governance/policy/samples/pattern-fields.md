---
title: 'Minta: mezőtulajdonságok a házirend-definícióban'
description: Ez a Azure Policy minta azt szemlélteti, hogyan használható a Mezőtulajdonságok a házirend-definícióban.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: b0b998084c9acf664d4e3e8dd9483af1ee986677
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093333"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy minta: mező tulajdonságai

A [mező](../concepts/definition-structure.md#fields) operátor kiértékeli a megadott tulajdonságot vagy [aliast](../concepts/definition-structure.md#aliases) egy megadott értékre egy adott [feltételnél](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Példa a házirend-definícióra

Ez a szabályzat-definíció lehetővé teszi, hogy olyan engedélyezett régiókat határozzon meg, amelyek megfelelnek a szervezet földrajzi elhelyezkedési követelményeinek. Az engedélyezett erőforrások a **listOfAllowedLocations** (_Array_) paraméterben vannak meghatározva. A definíciónak megfelelő erőforrások [megtagadva](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

A **mező** operátor háromszor van használatban a [logikai operátor](../concepts/definition-structure.md#logical-operators) **allOf**.

- Az első használat kiértékeli a `location` tulajdonságot a **notIn** feltétellel a **listOfAllowedLocations** paraméterrel. a **notIn** úgy működik, ahogy egy _tömböt_ vár, és a paraméter egy _tömb_. Ha a `location` létrehozott vagy frissített erőforrás nem szerepel a jóváhagyott listán, ez az elem igaz értéket ad vissza.
- A második használat a tulajdonságot is kiértékeli `location` , de a **notEquals** feltételt használva ellenőrzi, hogy az erőforrás _globális_-e. Ha a `location` létrehozott vagy frissített erőforrás nem _globális_, ez az elem igaz értéket ad vissza.
- Az utolsó használat kiértékeli a `type` tulajdonságot, és a **notEquals** feltételt használja az erőforrástípus érvényesítéséhez, nem a _Microsoft. AzureActiveDirectory/b2cDirectories_. Ha nem, akkor ez az elem igaz értéket ad vissza.

Ha a **allOf** logikai operátor mindhárom feltétel-utasítása igaz értéket ad meg, a Azure Policy blokkolja az erőforrás-létrehozási vagy-frissítési műveletet.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
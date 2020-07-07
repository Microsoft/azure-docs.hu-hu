---
title: Egyéni szerepkör-definíciók áttekintése
description: A felügyelt alkalmazásokhoz tartozó egyéni szerepkör-definíciók létrehozásának fogalmát ismerteti.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81391827"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Egyéni szerepkör-definíciós összetevő a Azure Managed Applications

Az egyéni szerepkör-definíció egy opcionális összetevő a felügyelt alkalmazásokban. Annak meghatározására szolgál, hogy a felügyelt alkalmazás milyen engedélyeket kell végrehajtania a függvények működéséhez.

Ez a cikk áttekintést nyújt az egyéni szerepkör-definíciós összetevőről és annak képességeiről.

## <a name="custom-role-definition-artifact"></a>Egyéni szerepkör-definíciós összetevő

A (z) customRoleDefinition.jsegyéni szerepkör-definíciós összetevőt kell megadnia. Helyezze el ugyanarra a szintre, mint createUiDefinition.jsbe-és mainTemplate.jsa. zip csomagban, amely létrehoz egy felügyelt alkalmazás definícióját. A. zip csomag létrehozásával és a felügyelt alkalmazás definíciójának közzétételével kapcsolatos további információkért lásd: [felügyelt alkalmazás definíciójának közzététele.](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>Egyéni szerepkör-definíciós séma

A fájl customRoleDefinition.jsegy olyan legfelső szintű `roles` tulajdonsággal rendelkezik, amely a szerepkörök tömbje. Ezek a szerepkörök a felügyelt alkalmazás működéséhez szükséges engedélyek. Jelenleg csak a beépített szerepkörök engedélyezettek, de több szerepkör is megadható. A szerepköröket a szerepkör-definíció vagy a szerepkör neve is hivatkozhat.

Egyéni szerepkör-definíciók JSON-mintája:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Szerepkörök

A szerepkörök vagy a következőkből állnak `$.properties.roleName` `id` :

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Használhatja a `id` vagy a mezőt is `roleName` . Csak egy szükséges. Ezekkel a mezőkkel lehet megkeresni a szerepkör-definíciót, amelyet alkalmazni kell. Ha mindkettő meg van adva, a `id` program a mezőt fogja használni.

|Tulajdonság|Kötelező?|Leírás|
|---------|---------|---------|
|id|Igen|A beépített szerepkör azonosítója. Használhatja a teljes azonosítót vagy csak a GUID azonosítót.|
|roleName|Igen|A beépített szerepkör neve.|
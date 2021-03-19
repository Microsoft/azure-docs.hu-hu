---
title: Definíciós összetevő hivatkozásának megtekintése
description: Példát mutat a Azure Managed Applications megjelenítésének definíciós összetevője számára. A fájl neve viewDefinition.jsa következőn:.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651201"
---
# <a name="reference-view-definition-artifact"></a>Hivatkozás: definíciós összetevő megtekintése

Ez a cikk a Azure Managed Applications összetevőn belüli *viewDefinition.jsra* mutat. További információ a létrehozási nézetek konfigurációjával kapcsolatban: a [definíciós összetevő megtekintése](concepts-view-definition.md).

## <a name="view-definition"></a>Meghatározás megtekintése

A következő JSON-példa a Azure Managed Applications *viewDefinition.js* fájlját mutatja be:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal](tutorial-create-managed-app-with-custom-provider.md)
- [Hivatkozás: felhasználói felület elemeinek összetevője](reference-createuidefinition-artifact.md)
- [Hivatkozás: telepítési sablon összetevő](reference-main-template-artifact.md)
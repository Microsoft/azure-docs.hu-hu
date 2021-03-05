---
title: Szövegmező felhasználói felületének eleme
description: Ismerteti a Microsoft. Common. szövegmező felhasználói felületének a Azure Portalhoz tartozó elemét. Formázatlan szöveg hozzáadására használható.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124329"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. szövegdoboz felhasználói felületének eleme

Felhasználói felületi (UI) elem, amely formázatlan szöveg hozzáadására használható. Az `Microsoft.Common.TextBox` elem egy egysoros beviteli mező, de támogatja a többsoros bevitelt a `multiLine` tulajdonsággal.

## <a name="ui-sample"></a>Felhasználói felület mintája

Az `TextBox` elem egy egysoros vagy többsoros szövegmezőt használ.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Microsoft. Common. szövegmező elem egysoros szövegmező.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Microsoft. Common. szövegmező elem többsoros szövegmező.":::

## <a name="schema"></a>Séma

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
"contoso123"
```

## <a name="remarks"></a>Megjegyzések

- A `toolTip` tulajdonság használatával megjelenítheti az elem szövegét, amikor az egérmutatót az információs szimbólum fölé viszi.
- A `placeholder` tulajdonság egy olyan súgószöveg, amely eltűnik, amikor a felhasználó megkezdi a szerkesztést. Ha a `placeholder` és `defaultValue` mindkettő is meg van adva, a `defaultValue` veszi elsőbbséget, és megjelenik.
- A `multiLine` tulajdonság a logikai `true` vagy a `false` . Többsoros szövegmező használatához állítsa a tulajdonságot a következőre: `true` . Ha nincs szükség többsoros szövegmezőre, állítsa be a tulajdonságot, `false` vagy zárja ki a tulajdonságot. Új sorok esetén a JSON-kimenet `\n` a sor adatcsatornán jelenik meg. A többsoros szövegmező fogadja a `\r` kocsivissza (CR) értéket, és `\n` egy SOREMELÉS (LF) értéket. Az alapértelmezett érték például a `\r\n` CRLF megadására is használható.
- Ha a `constraints.required` értéke `true` , akkor a szövegmezőnek rendelkeznie kell egy, a sikeres ellenőrzéshez szükséges értékkel. Az alapértelmezett érték `false`.
- A `validations` tulajdonság egy olyan tömb, amelyben a szövegmezőben megadott érték ellenőrzéséhez feltételeket adhat hozzá.
- A `regex` tulajdonság egy JavaScript reguláris kifejezési minta. Ha meg van adva, a szövegmező értékének meg kell egyeznie a sikeres ellenőrzéshez szükséges mintázattal. Az alapértelmezett érték `null`. A regex szintaxisával kapcsolatos további információkért lásd a [reguláris kifejezés gyors leírása](/dotnet/standard/base-types/regular-expression-language-quick-reference)című témakört.
- A `isValid` tulajdonság olyan kifejezést tartalmaz, amely a vagy a értékre van kiértékelve `true` `false` . A kifejezésen belül meg kell határoznia azt a feltételt, amely meghatározza, hogy a szövegmező érvényes-e.
- A `message` tulajdonság egy karakterlánc, amely akkor jeleníthető meg, ha a szövegmező értéke sikertelen az ellenőrzés során.
- Meg lehet adni egy értéket, ha a `regex` `required` be van állítva `false` . Ebben az esetben nincs szükség értékre a szövegmező sikeres ellenőrzéséhez. Ha meg van adva, meg kell egyeznie a reguláris kifejezési mintával.

## <a name="examples"></a>Példák

A példák egy egysoros szövegmező és egy többsoros szövegmező használatát mutatják be.

### <a name="single-line"></a>Egysoros

Az alábbi példa egy szövegmezőt használ a [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) vezérlővel az erőforrás-név rendelkezésre állásának ellenőrzéséhez.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Többsoros

Ez a példa a `multiLine` tulajdonságot használja egy többsoros szövegmező helyőrző szöveggel való létrehozásához.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

- A felhasználói felületi definíciók létrehozásának bevezetését az [ Azure felügyelt alkalmazás létrehozási felületénekCreateUiDefinition.jsja](create-uidefinition-overview.md)című témakörben tekintheti meg.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

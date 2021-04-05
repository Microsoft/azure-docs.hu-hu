---
title: ServicePrincipalSelector FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. ServicePrincipalSelector felhasználói felületi elemének ismertetése. Vezérlőelemet biztosít egy alkalmazás és egy szövegmező számára a jelszó vagy a tanúsítvány ujjlenyomatának beviteléhez.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184450"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft. Common. ServicePrincipalSelector felhasználói felületi elem

Olyan vezérlő, amely lehetővé teszi, hogy a felhasználók kiválasszon egy meglévő [szolgáltatásnevet](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) , vagy regisztráljanak egy új alkalmazást. Ha az **új létrehozása** lehetőséget választja, kövesse az új alkalmazás regisztrálásához szükséges lépéseket. Egy meglévő alkalmazás kiválasztásakor a vezérlő szövegmezőt biztosít a jelszó vagy a tanúsítvány ujjlenyomatának beviteléhez.

## <a name="ui-samples"></a>Felhasználói felületi minták

Használhat alapértelmezett alkalmazást, létrehozhat egy új alkalmazást, vagy használhat meglévő alkalmazást is.

### <a name="use-default-application-or-create-new"></a>Alapértelmezett alkalmazás használata vagy új létrehozása

Az alapértelmezett nézetet a tulajdonság értékei határozzák meg, `defaultValue` az **egyszerű szolgáltatásnév típusa** pedig **új létrehozása**. Ha a `principalId` tulajdonság érvényes globálisan egyedi azonosítót (GUID) tartalmaz, a vezérlő megkeresi az alkalmazást `objectId` . Az alapértelmezett érték akkor érvényes, ha a felhasználó nem végez kijelölést a vezérlőből.

Ha új alkalmazást szeretne regisztrálni, válassza a **Kijelölés módosítása** lehetőséget, és megjelenik az **alkalmazás regisztrálása** párbeszédpanel. Adja meg a név és a **támogatott fióktípus** **nevet**, majd kattintson a **regisztráció** gombra.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Microsoft. Common. ServicePrincipalSelector kezdeti nézet.":::

Miután regisztrált egy új alkalmazást, a **hitelesítési típus** használatával adja meg a jelszó vagy a tanúsítvány ujjlenyomatát.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Microsoft. Common. ServicePrincipalSelector hitelesítés.":::

### <a name="use-existing-application"></a>Meglévő alkalmazás használata

Meglévő alkalmazás használatához válassza a **meglévő kijelölése** lehetőséget, majd válassza a **kijelölés elvégzése** lehetőséget. Használja az alkalmazás **kiválasztása** párbeszédpanelt, ahol megkeresheti az alkalmazás nevét. Az eredmények közül válassza ki az alkalmazást, majd a **kiválasztás** gombot. Miután kiválasztott egy alkalmazást, a vezérlő megjeleníti a **hitelesítési típust** a jelszó vagy a tanúsítvány ujjlenyomatának megadásához.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft. Common. ServicePrincipalSelector válassza a meglévő alkalmazás lehetőséget.":::

## <a name="schema"></a>Séma

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések

- A szükséges tulajdonságok a következők:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Az alapértelmezett és a értéket adja meg `principalId` `name` .

- A választható tulajdonságok a következők:
  - `toolTip`: Elemleírást csatol `infoBalloon` az egyes címkékhez.
  - `visible`: A vezérlő elrejtése vagy megjelenítése.
  - `options`: Megadja, hogy elérhető legyen-e a Tanúsítvány ujjlenyomata beállítás.
  - `constraints`: A jelszó-ellenőrzés regex-megkötései.

## <a name="example"></a>Példa

Az alábbi példa a `Microsoft.Common.ServicePrincipalSelector` vezérlőt szemlélteti. A `defaultValue` tulajdonság az `principalId` `<default guid>` alapértelmezett alkalmazásspecifikus azonosító GUID azonosítójának helyőrzőként való beállítására szolgál.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Példa kimenetre

A a `appId` kiválasztott vagy létrehozott alkalmazás-regisztráció azonosítója. A a `objectId` kiválasztott alkalmazás-regisztrációhoz konfigurált egyszerű szolgáltatások objektumazonosítók tömbje.

Ha a vezérlőből nem készül kijelölés, a `newOrExisting` tulajdonság értéke **új**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Ha új vagy meglévő alkalmazást **hoz létre** a vezérlőből, a `newOrExisting` tulajdonság értéke **meglévő**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Következő lépések

- A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
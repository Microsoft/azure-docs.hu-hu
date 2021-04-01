---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 3ebe1ec4c0292a530e5ef2c754e9b002e931300e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994312"
---
#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/) 

1. Válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki a *webapi1* alkalmazást az **áttekintő** oldal megnyitásához.
1. A **kezelés** területen válassza **az API közzététele** lehetőséget.
1. Az **alkalmazás-azonosító URI-ja** mellett válassza a **beállítás** hivatkozást.
1. Cserélje le az alapértelmezett értéket (GUID) a értékre `api` , majd válassza a **Mentés** lehetőséget. A teljes URI látható, és a formátumnak kell lennie `https://your-tenant-name.onmicrosoft.com/api` . Ha a webalkalmazás hozzáférési jogkivonatot kér az API-hoz, adja hozzá ezt az URI-t az API-hoz definiált minden hatókör előtagjaként.
1. Az **API által definiált hatókörök** területen válassza **a hatókör hozzáadása** elemet.
1. Adja meg a következő értékeket egy olyan hatókör létrehozásához, amely az API olvasási hozzáférését határozza meg, majd válassza a **hatókör hozzáadása** elemet:
    1. **Hatókör neve**: `demo.read`
    1. **Rendszergazdai engedély megjelenítendő neve**: `Read access to demo API`
    1. **Rendszergazdai engedély leírása**: `Allows read access to the demo API`
1. Válassza a **hatókör hozzáadása** lehetőséget, és adja meg az alábbi értékeket egy olyan hatókör hozzáadásához, amely írási hozzáférést definiál az API-hoz, majd válassza a **hatókör hozzáadása** elemet:
    1. **Hatókör neve**: `demo.write`
    1. **Rendszergazdai engedély megjelenítendő neve**: `Write access to demo API`
    1. **Rendszergazdai engedély leírása**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Válassza az **alkalmazások (örökölt)** lehetőséget.
1. Válassza ki a *webapi1* alkalmazást a **Tulajdonságok** lap megnyitásához.
1. Válassza a **közzétett hatókörök** elemet. A közzétett hatókörökkel egy ügyfélalkalmazás bizonyos engedélyeket biztosíthat a webes API-nak.
1. A **hatókör**, a `demo.read` és a **Leírás** mezőbe írja be a következőt: `Read access to the web API` .
1. A **hatókör**, a `demo.write` és a **Leírás** mezőbe írja be a következőt: `Write access to the web API` .
1. Válassza a **Mentés** lehetőséget.
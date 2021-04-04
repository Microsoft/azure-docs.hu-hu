---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f8d8ffe8c1802a5cf67b7dc2b0fb071b572d76e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994357"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja az új, egységes **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például *ROPC_Auth_app*.
1. Hagyja meg a többi értéket, és válassza a **regisztráció** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
1. Válassza **az új élmény kipróbálása** (ha látható) lehetőséget.
1. Az **alapértelmezett ügyfél típusa** területen válassza az **Igen** lehetőséget az alkalmazás nyilvános ügyfélként való kezelésére. Ez a beállítás a ROPC folyamathoz szükséges.
1. Kattintson a **Mentés** gombra.
1. A bal oldali menüben válassza a **jegyzékfájl** elemet a jegyzékfájl-szerkesztő megnyitásához. 
1. Állítsa a **oauth2AllowImplicitFlow** attribútumot *true (igaz*) értékre:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Kattintson a **Mentés** gombra.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
1. Adjon nevet az alkalmazásnak. Például *ROPC_Auth_app*.
1. **Natív ügyfél** esetén válassza az **Igen** lehetőséget.
1. Hagyja meg a többi értéket, és válassza a **Létrehozás** lehetőséget.
1. Jegyezze fel az **alkalmazás azonosítóját** egy későbbi lépésben való használatra.
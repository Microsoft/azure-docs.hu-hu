---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 2ad6b90616077a6d18550e86692b109bda622af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026431"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja az új, egységes **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *nativeapp1*.
1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban vagy bármely identitás-szolgáltatóban**.
1. Az **átirányítási URI** alatt válassza a legördülő menüből a **nyilvános ügyfél/natív (mobil & Desktop)** lehetőséget.
1. Adjon meg egy egyedi sémával rendelkező átirányítási URI-t. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Az átirányítási URI kiválasztásakor fontos szempontokat kell figyelembe venni:
    * **Fejlesztés** A fejlesztési célú használatra beállíthatja az átirányítási URI-t, `http://localhost` és Azure ad B2C a kérésben szereplő összes portot tiszteletben tartja. Ha a regisztrált URI tartalmaz portot, Azure AD B2C csak a portot fogja használni. Ha például a regisztrált átirányítási URI, akkor `http://localhost` a kérelemben szereplő átirányítási URI lehet `http://localhost:<randomport>` . Ha a regisztrált átirányítási URI `http://localhost:8080` , a kérelemben szereplő átirányítási URI azonosítónak kell lennie `http://localhost:8080` .
    * **Egyedi**: az átirányítási URI sémájának minden alkalmazás esetében egyedinek kell lennie. A példában a `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` `com.onmicrosoft.contosob2c.exampleapp` séma. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazást. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    * **Befejezés**: az átirányítási URI-nak egy sémával és egy útvonallal kell rendelkeznie. Az elérési útnak legalább egy perjelet tartalmaznia kell a tartomány után. A működése például `//oauth/` `//oauth` sikertelen. Az URI-ban ne szerepeljenek speciális karakterek, például aláhúzások.
1. Az **engedélyek** területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
2. Válassza a **Regisztráció** lehetőséget.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
1. Adjon nevet az alkalmazásnak. Például: *nativeapp1*.
1. **Natív ügyfél** esetén válassza az **Igen** lehetőséget.
1. **Egyéni átirányítási URI** -t adjon meg egyedi sémával. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:
    * **Egyedi**: az átirányítási URI sémájának minden alkalmazás esetében egyedinek kell lennie. A példában a `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` `com.onmicrosoft.contosob2c.exampleapp` séma. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazást. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    * **Befejezés**: az átirányítási URI-nak egy sémával és egy útvonallal kell rendelkeznie. Az elérési útnak legalább egy perjelet tartalmaznia kell a tartomány után. A működése például `//oauth/` `//oauth` sikertelen. Az URI-ban ne szerepeljenek speciális karakterek, például aláhúzások.
1. Válassza a **Létrehozás** lehetőséget.
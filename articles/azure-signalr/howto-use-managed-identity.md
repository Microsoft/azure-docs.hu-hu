---
title: Felügyelt identitások az Azure Signaler szolgáltatásban
description: Ismerje meg, hogyan működnek a felügyelt identitások az Azure Signaler szolgáltatásban, és hogyan használhatók felügyelt identitások kiszolgáló nélküli forgatókönyvekben.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731584"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Felügyelt identitások az Azure Signaler szolgáltatáshoz

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást az Azure Signaler szolgáltatáshoz, és hogyan használhatja azt kiszolgáló nélküli forgatókönyvekben.

> [!Important] 
> Az Azure Signaler szolgáltatás csak egy felügyelt identitást tud támogatni. Ez azt jelenti, hogy a rendszerhez hozzárendelt identitást vagy felhasználó által hozzárendelt identitást adhat hozzá. 

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

A Azure Portal felügyelt identitásának beállításához először létre kell hoznia egy Azure Signal Service-példányt, majd engedélyeznie kell a szolgáltatást.

1. A szokásos módon hozzon létre egy Azure Signaler Service-példányt a portálon. Tallózással keresse meg a portálon.

2. Válassza az **identitás** lehetőséget.

4. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva **értékre**. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Rendszerhez rendelt identitás hozzáadása a portálon":::

## <a name="add-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

A felhasználó által hozzárendelt identitással rendelkező Azure Signaler szolgáltatás-példány létrehozásához létre kell hoznia az identitást, majd hozzá kell adnia annak erőforrás-azonosítóját a szolgáltatáshoz.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitási erőforrást az [utasításoknak](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)megfelelően.

2. A szokásos módon hozzon létre egy Azure Signaler Service-példányt a portálon. Tallózással keresse meg a portálon.

3. Válassza az **identitás** lehetőséget.

4. A **felhasználó által hozzárendelt** lapon válassza a **Hozzáadás** lehetőséget.

5. Keresse meg a korábban létrehozott identitást, és válassza ki azt. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Felhasználó által hozzárendelt identitás hozzáadása a portálon":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Felügyelt identitás használata kiszolgáló nélküli forgatókönyvekben

Az Azure Signaler szolgáltatás egy teljes körűen felügyelt szolgáltatás, ezért nem használhat felügyelt identitást a jogkivonatok manuális lekéréséhez. Ehelyett az Azure Signaler szolgáltatás a hozzáférési token beszerzéséhez beállított felügyelt identitást használja. A szolgáltatás ezután a hozzáférési jogkivonatot egy `Authorization` felsőbb rétegbeli kérelemben lévő fejlécbe állítja be kiszolgáló nélküli forgatókönyvekben.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Felügyelt identitás hitelesítésének engedélyezése a felsőbb rétegbeli beállításokban

1. Rendszerhez rendelt identitás vagy felhasználó által hozzárendelt identitás hozzáadása.

2. Vegyen fel egy felsőbb rétegbeli beállítást, és kattintson bármelyik csillagra, hogy az alább látható részletes lapra kerüljön.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="MSI előtti beállítás":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="MSI – beállítás":::

3. A felügyelt identitás hitelesítési beállításai **erőforrás** esetében megadhatja a cél erőforrást. Az erőforrás a `aud` beszerzett hozzáférési jogkivonat jogcíme lesz, amely a felsőbb rétegbeli végpontok érvényesítésének részeként használható. Az erőforrás a következők egyike lehet:
    - Üres
    - Az egyszerű szolgáltatásnév alkalmazás-(ügyfél-) azonosítója
    - Az egyszerű szolgáltatásnév alkalmazásspecifikus AZONOSÍTÓjának URI-ja
    - [Azure-szolgáltatás erőforrás-azonosítója](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Ha a szolgáltatásban saját maga érvényesít egy hozzáférési jogkivonatot, kiválaszthatja az egyik erőforrás-formátumot is. Győződjön meg arról, hogy az **erőforrás** -érték a **hitelesítési** beállításokban és az érvényesítés konzisztens. Ha Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ egy adatsíkon, akkor a szolgáltató által igényelt erőforrást kell használnia.

### <a name="validate-access-tokens"></a>Hozzáférési tokenek ellenőrzése

A fejlécben szereplő jogkivonat a `Authorization` [Microsoft Identity platform hozzáférési jogkivonata](../active-directory/develop/access-tokens.md#validating-tokens).

A hozzáférési jogkivonatok érvényesítéséhez az alkalmazásnak ellenőriznie kell a célközönséget és az aláíró jogkivonatokat is. Ezeket érvényesíteni kell az OpenID-felderítési dokumentum értékeivel. Tekintse meg például a [dokumentum bérlői független verzióját](https://login.microsoftonline.com/common/.well-known/openid-configuration).

A Azure Active Directory (Azure AD) middleware beépített képességekkel rendelkezik a hozzáférési jogkivonatok érvényesítéséhez. A [minták](../active-directory/develop/sample-v2-code.md) között böngészhet, és megkeresheti az Ön által választott nyelven.

Olyan kódtárakat és kódokat biztosítunk, amelyek bemutatják a jogkivonat-érvényesítés kezelését. A JSON Web Token (JWT) érvényesítéséhez több nyílt forráskódú partneri függvénytár is elérhető. Szinte minden platformhoz és nyelvhez legalább egy lehetőség van. További információ az Azure AD-hitelesítési könyvtárakról és a mintakód-mintákról: [Microsoft Identity platform Authentication librarys](../active-directory/develop/reference-v2-libraries.md).

#### <a name="authentication-in-function-app"></a>Hitelesítés függvényalkalmazás

A hozzáférési jogkivonat érvényesítésének beállítása a függvényalkalmazásban egyszerű és hatékony, kód nélkül működik.

1. A **hitelesítés/engedélyezés** lapon váltson **app Service hitelesítésre** **a** következőn:.

2. **Ha a kérelem nem hitelesítve van**, válassza a **Bejelentkezés a következővel Azure Active Directory** műveletet.

3. A hitelesítési szolgáltatóban kattintson a into **Azure Active Directory**

4. Az új oldalon. Válassza az **expressz** lehetőséget, és **hozzon létre új ad-alkalmazást** , majd kattintson **az OK** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="függvény HRE"::: elemre.

5. Navigáljon a Signaler szolgáltatáshoz, és kövesse a [lépéseket](howto-use-managed-identity.md#add-a-system-assigned-identity) egy rendszerhez rendelt identitás vagy felhasználó által hozzárendelt identitás hozzáadásához.

6. Ismerkedjen meg a Signaler szolgáltatásban a **felsőbb rétegbeli beállításokkal** , és válassza a **felügyelt identitás használata** lehetőséget, és **válasszon a meglévő alkalmazások** közül. Válassza ki a korábban létrehozott alkalmazást.

Ezen beállítások után a függvényalkalmazás a fejlécben hozzáférési jogkivonat nélkül fogja elutasítani a kérelmeket.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Felügyelt identitás használata Key Vault-hivatkozáshoz

A signaler szolgáltatás a felügyelt identitás használatával tud hozzáférni Key Vaulthoz.

1. Rendszerhez rendelt identitás vagy felhasználó által hozzárendelt identitás hozzáadása az Azure Signaler szolgáltatáshoz.

2. Adja meg a titkos olvasási engedélyt a felügyelt identitáshoz a Key Vault hozzáférési házirendjeiben. Lásd: [Key Vault hozzáférési szabályzat kiosztása a Azure Portal használatával](../key-vault/general/assign-access-policy-portal.md)

Ez a funkció jelenleg a következő helyzetekben használható:

- [Hivatkozási titok a felsőbb rétegbeli URL-mintában](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>Következő lépések

- [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](signalr-concept-serverless-development-config.md)
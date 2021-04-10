---
title: 'Oktatóanyag: alkalmazás regisztrálása'
titleSuffix: Azure AD B2C
description: Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat egy webalkalmazást Azure Active Directory B2C a Azure Portal használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ec67669edeb52af1044c97c984eb6ba36fd1a0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579637"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Oktatóanyag: webalkalmazás regisztrálása a Azure Active Directory B2Cban

Az [alkalmazások](application-types.md) Azure Active Directory B2C (Azure ad B2C) való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben. Ez az oktatóanyag bemutatja, hogyan regisztrálhat egy webalkalmazást a Azure Portal használatával. 

A "webalkalmazás" egy hagyományos webalkalmazásra hivatkozik, amely a kiszolgálón futtatja a legtöbb alkalmazás-logikát. Ezeket olyan keretrendszerekkel lehet felépíteni, mint például a ASP.NET Core, a Maven (Java), a lombik (Python) és az Express (Node.js).

> [!IMPORTANT]
> Ha egy egyoldalas alkalmazást ("SPA") használ helyette (például szögletes, Vue vagy reakciót használ), Ismerje meg, [Hogyan regisztrálhat egy egyoldalas alkalmazást](tutorial-register-spa.md).
> 
> Ha inkább natív alkalmazást használ (például iOS, Android, Mobile & Desktop), Ismerje meg, [Hogyan regisztrálhat egy natív ügyfélalkalmazás](add-native-application.md).

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ha még nem hozott létre saját [Azure ad B2C bérlőt](tutorial-create-tenant.md), hozzon létre egyet most. Meglévő Azure AD B2C bérlőt is használhat.

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

Ha webalkalmazást szeretne regisztrálni a Azure AD B2C-bérlőben, használhatja az új, egyesített **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazásokat (örökölt)** . [További információ az új felületről](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *webapp1*.
1. A **támogatott fióktípus** területen válassza a **fiókok lehetőséget bármely identitás-szolgáltató vagy szervezeti címtárban (a felhasználók felhasználói folyamatokkal történő hitelesítéséhez)**.
1. Az **átirányítási URI** területen válassza a **web** lehetőséget, majd írja be `https://jwt.ms` az URL-cím szövegmezőbe.

    Az átirányítási URI azt a végpontot, amelyhez a felhasználót az engedélyezési kiszolgáló Azure AD B2C (ebben az esetben) a felhasználóval való interakció befejezése után küldi el a rendszer, és a sikeres engedélyezéshez hozzáférési jogkivonatot vagy engedélyezési kódot küld. Éles alkalmazásokban ez általában egy nyilvánosan elérhető végpont, amelyben az alkalmazás fut, például: `https://contoso.com/auth-response` . Az oktatóanyaghoz hasonló tesztelési célokra beállíthatja, hogy a egy `https://jwt.ms` Microsoft tulajdonú webalkalmazás, amely egy jogkivonat dekódolású tartalmát jeleníti meg (a jogkivonat tartalma soha nem hagyja el a böngészőt). Az alkalmazásfejlesztés során hozzáadhatja azt a végpontot, amelyben az alkalmazás helyileg, például a következőt figyeli `https://localhost:5000` . A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-ket.

    Az átirányítási URI-k esetében a következő korlátozások érvényesek:

    * A válasz URL-címnek a sémával kell kezdődnie `https` .
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként szerepel `.../abc/response-oidc` , ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, a hozzájuk társított cookie-k `.../abc/response-oidc` kizárható, ha a rendszer átirányítja a kis-és nagybetűket `.../ABC/response-oidc` .

1. Az **engedélyek** területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
1. Válassza a **Regisztráció** lehetőséget.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
1. Adjon nevet az alkalmazásnak. Például: *webapp1*.
1. A **Web App/web API belefoglalásához** válassza az **Igen** lehetőséget.
1. A **Válasz URL-cím** mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Beállíthatja például, hogy a helyi figyelést a következő időpontban: `http://localhost:5000` . A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-ket.

    Az átirányítási URI-k esetében a következő korlátozások érvényesek:

    * A válasz URL-címének a sémával kell kezdődnie `https` , hacsak nem használja `localhost` .
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként szerepel `.../abc/response-oidc` , ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, a hozzájuk társított cookie-k `.../abc/response-oidc` kizárható, ha a rendszer átirányítja a kis-és nagybetűket `.../ABC/response-oidc` .

1. Válassza a **Létrehozás** lehetőséget az alkalmazás regisztrációjának befejezéséhez.

* * *

## <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Webalkalmazások esetén létre kell hoznia egy alkalmazás titkos kulcsát. Az ügyfél titkos kulcsát *alkalmazás jelszavának* is nevezik. Az alkalmazás a titkos kulcsot fogja használni egy hozzáférési jogkivonat engedélyezési kódjának cseréjéhez.

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. A **Azure ad B2C-Alkalmazásregisztrációk** lapon válassza ki a létrehozott alkalmazást, például *webapp1*.
1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza a **tanúsítványok & Secrets** elemet.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg az ügyfél titkos kódjának leírását a **Leírás** mezőben. Például: *clientsecret1*.
1. A **lejárat** szakaszban válassza ki azt az időtartamot, amelynek a titka érvényes, majd válassza a **Hozzáadás** lehetőséget.
1. Jegyezze fel a titkos kulcs **értékét** az ügyfélalkalmazás kódjában való használathoz. Ez a titkos érték soha nem jelenik meg többé, miután elhagyja ezt a lapot. Ezt az értéket használja az alkalmazás kódjában található alkalmazás-titokként.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. A **Azure ad B2C-alkalmazások** lapon válassza ki a létrehozott alkalmazást, például *webapp1*.
1. Válassza a **kulcsok** lehetőséget, majd válassza a **kulcs generálása** elemet.
1. Kattintson a **Save (Mentés** ) gombra a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket használja az alkalmazás kódjában található alkalmazás-titokként.

* * *

> [!NOTE]
> Biztonsági okokból rendszeres időközönként, vagy vészhelyzet esetén azonnal átadhatja az alkalmazás titkos kulcsát. Az Azure AD B2C-mel integrált alkalmazások számára elő kell készíteni a titkos átváltási esemény kezelését, függetlenül attól, hogy milyen gyakran fordul elő. Megadhat két alkalmazás-titkot, így az alkalmazás a régi titkot használja egy alkalmazás titkos rotációs eseménye során. További ügyfél-titkos kód hozzáadásához ismételje meg a szakasz lépéseit. 

## <a name="enable-id-token-implicit-grant"></a>AZONOSÍTÓ jogkivonat implicit engedélyezésének engedélyezése

Az implicit támogatás meghatározó jellemzője, hogy a jogkivonatok, például az azonosító és a hozzáférési jogkivonatok közvetlenül a Azure AD B2Cból lesznek visszaadva az alkalmazásnak. A webalkalmazások, például ASP.NET Core webalkalmazások esetében, [https://jwt.ms](https://jwt.ms) amelyek azonosító jogkivonatot kérnek közvetlenül az engedélyezési végponttól, az alkalmazás regisztrációjában engedélyezze az implicit engedélyezési folyamatot.

1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza a **hitelesítés** lehetőséget.
1. Az implicit Engedélyezés területen jelölje be a **hozzáférési jogkivonatok** és az **azonosító tokenek** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcsának létrehozása

Következő lépésként megtudhatja, hogyan hozhat létre felhasználói folyamatokat, hogy a felhasználók regisztrálhatják, bejelentkezhetnek és kezelhetik a profiljaikat.

> [!div class="nextstepaction"]
> [Felhasználói folyamatok létrehozása Azure Active Directory B2C >](tutorial-create-user-flows.md)

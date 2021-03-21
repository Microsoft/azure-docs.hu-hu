---
title: Hitelesítés és engedélyezés
description: Ismerje meg, hogy az alkalmazás vagy szolgáltatás milyen módon tud hitelesíteni az Azure térbeli Horgonyokban, és hogy milyen szintű vezérléssel kell eljutnia a térbeli horgonyokhoz.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 01065f9ac26599d26d6e2a6979eae1e559a82854
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97722963"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Hitelesítés és engedélyezés az Azure térbeli horgonyokhoz

Ebből a cikkből megtudhatja, hogyan végezhet hitelesítést az Azure térbeli Horgonyokban az alkalmazásból vagy webszolgáltatásból. Azt is megismerheti, hogyan használhatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) a Azure Active Directory (Azure AD) a térbeli horgonyok fiókjaihoz való hozzáférés szabályozásához.

## <a name="overview"></a>Áttekintés

![Az Azure térbeli horgonyokra való hitelesítés áttekintését bemutató diagram.](./media/spatial-anchors-authentication-overview.png)

Egy adott Azure térbeli horgonyhoz tartozó fiók eléréséhez az ügyfeleknek először hozzáférési tokent kell beszerezniük az Azure Mixed Reality biztonságijogkivonat-szolgáltatásból (STS). Az STS-től kapott tokenek élettartama 24 óra. Olyan információkat tartalmaznak, amelyekkel a térbeli horgonyok szolgáltatásai hitelesítési döntéseket hozhatnak a fiókon, és biztosítják, hogy csak a jogosult résztvevői férhessenek hozzá a fiókhoz.

A hozzáférési jogkivonatok az Azure AD által kiállított fiók-kulcsok vagy jogkivonatok Exchange-ben is beszerezhetők.

A fiókok kulcsai lehetővé teszik az Azure térbeli horgonyok szolgáltatás használatának gyors megkezdését. Az alkalmazás éles környezetben való üzembe helyezése előtt azonban javasoljuk, hogy frissítse az alkalmazást az Azure AD-hitelesítés használatára.

Az Azure AD-hitelesítési tokeneket kétféleképpen szerezheti be:

- Ha vállalati alkalmazást hoz létre, és a vállalata az Azure AD-t használja Identity rendszerként, a felhasználó-alapú Azure AD-hitelesítést használhatja az alkalmazásban. Ezután hozzáférést biztosít a térbeli horgonyokhoz tartozó fiókokhoz a meglévő Azure AD-beli biztonsági csoportok használatával. Közvetlenül is megadhat hozzáférést a szervezet felhasználói számára.
- Ellenkező esetben javasoljuk, hogy az Azure AD-jogkivonatokat egy olyan webszolgáltatásból szerezze be, amely támogatja az alkalmazást. Azt javasoljuk, hogy az éles környezetben használt alkalmazások esetében ne ágyazza be az Azure térbeli horgonyokhoz való hozzáférés hitelesítő adatait az ügyfélalkalmazás használatával.

## <a name="account-keys"></a>Fiók kulcsai

Első lépésként a legegyszerűbb módszer a fiók kulcsainak használata az Azure térbeli horgonyok fiókjához való hozzáféréshez. A fiók kulcsai a Azure Portalon szerezhetők be. Nyissa meg a fiókját, és válassza a **kulcsok** fület:

![Képernyőkép, amely megjeleníti a kulcsok lapot a Kiemelt elsődleges kulcs másolás gombjával.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Két kulcs érhető el. Mindkettő egyidejűleg érvényes a térbeli horgonyok fiókhoz való hozzáféréshez. Javasoljuk, hogy rendszeresen frissítse a fiók eléréséhez használt kulcsot. A két különálló érvényes kulcs lehetővé teszi, hogy a frissítések állásidő nélkül legyenek elérhetők. Az elsődleges kulcsot és a másodlagos kulcsot csak akkor kell frissítenie.

Az SDK beépített támogatást nyújt a fiókok kulcsain keresztül történő hitelesítéshez. Csak be kell állítania a `AccountKey` tulajdonságot az `cloudSession` objektumon:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Miután beállította ezt a tulajdonságot, az SDK kezeli a hozzáférési jogkivonat fiókjának kulcsának cseréjét, valamint az alkalmazáshoz tartozó jogkivonatok szükséges gyorsítótárazását.

> [!WARNING]
> Javasoljuk, hogy a gyors bevezetéshez használja a fiók kulcsait, de csak a fejlesztés/prototípus során. Nem javasoljuk, hogy az alkalmazást az élesben lévő beágyazott fiók kulcsával szállítsa. Ehelyett használja a következő témakörben ismertetett felhasználó-vagy szolgáltatás-alapú Azure AD-hitelesítési módszereket.

## <a name="azure-ad-user-authentication"></a>Azure AD-beli felhasználói hitelesítés

A Azure Active Directory felhasználókat célzó alkalmazások esetében javasoljuk, hogy használjon Azure AD-jogkivonatot a felhasználóhoz. Ezt a tokent a [MSAL](../../active-directory/develop/msal-overview.md)használatával szerezheti be. Kövesse a rövid útmutató lépéseit [egy alkalmazás regisztrálásához](../../active-directory/develop/quickstart-register-app.md), amely a következőket tartalmazza:

**A Azure Portal**
1.    Az alkalmazás regisztrálása az Azure AD-ben natív alkalmazásként. A regisztrálás részeként meg kell határoznia, hogy az alkalmazás több-bérlős-e. Emellett meg kell adnia az alkalmazáshoz engedélyezett átirányítási URL-címeket is.
1.  Nyissa meg az **API-engedélyek** lapot.
2.  Válassza **az engedély hozzáadása** lehetőséget.
    1.  Válassza a **vegyes valóság erőforrás-szolgáltató** elemet a **saját szervezet által használt API** -k lapon.
    2.  Válassza a **delegált engedélyek** lehetőséget.
    3.  A **mixedreality** területen válassza a **mixedreality. bejelentkezési** elemet.
    4.  Válassza az **engedélyek hozzáadása** lehetőséget.
3.  Válassza a **rendszergazdai jóváhagyás megadása** lehetőséget.

2. Adja meg az alkalmazás vagy a felhasználók számára az erőforráshoz való hozzáférést:
   1.    Lépjen a térbeli horgonyok erőforrásra a Azure Portal.
   2.    Lépjen a **hozzáférés-vezérlés (iam)** lapra.
   3.    Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
   1.    [Válasszon egy szerepkört](#azure-role-based-access-control).
   2.    A **kiválasztás** mezőbe írja be azoknak a felhasználóknak, csoportoknak és/vagy alkalmazásoknak a nevét, amelyekhez hozzáférést szeretne rendelni.
   3.    Kattintson a **Mentés** gombra.

**A kódban**
1.    Ügyeljen arra, hogy a saját Azure AD-alkalmazásának alkalmazás-AZONOSÍTÓját és átirányítási URI azonosítóját használja az **ügyfél-azonosító** és az **RedirectUri** paraméterekhez a MSAL-ben.
2.    Adja meg a bérlő adatait:
        1.    Ha az alkalmazás **csak a saját szervezetet** támogatja, cserélje le ezt az értéket a **bérlői azonosítójával** vagy a **bérlő nevével**. Például: contoso.microsoft.com.
        2.    Ha az alkalmazás **minden szervezeti címtárban támogatja a fiókokat**, cserélje le ezt az értéket **szervezetekkel**.
        3.    Ha az alkalmazás támogatja az **összes Microsoft-fiók felhasználót**, cserélje le ezt az értéket **közösre**.
3.    A jogkivonat-kérelemben állítsa a **hatókört** **" `https://sts.<account-domain>//.default` "** értékre, ahol a `<account-domain>` helyére az Azure térbeli horgonyok fiókjának **fiók tartománya** kerül. Az USA 2. keleti régiójában található Azure térbeli horgonyok fiókjának hatóköre: " **`https://sts.mixedreality.azure.com//.default` "**. Ez a hatókör jelzi az Azure AD számára, hogy az alkalmazás jogkivonatot kér a vegyes valóság biztonsági jogkivonat-szolgáltatás (STS) számára.

A lépések elvégzése után az alkalmazásnak képesnek kell lennie az Azure AD-token MSAL való beszerzésére. Beállíthatja, hogy az Azure AD-jogkivonat a `authenticationToken` felhőalapú munkamenet-konfigurációs objektumon legyen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD-szolgáltatás hitelesítése

Az Azure térbeli horgonyokat használó alkalmazások éles környezetben való üzembe helyezéséhez javasoljuk, hogy használjon olyan háttér-szolgáltatást, amelyik közvetítő hitelesítési kéréseket fog használni. A folyamat áttekintése:

![Diagram, amely áttekintést nyújt az Azure térbeli horgonyok hitelesítéséről.](./media/spatial-anchors-aad-authentication.png)

Itt feltételezzük, hogy az alkalmazás a saját mechanizmusával hitelesíti a háttérben futó szolgáltatását. (Például egy Microsoft-fiók, PlayFab, Facebook, Google ID vagy egyéni Felhasználónév és jelszó.)  Miután a felhasználók hitelesítése megtörtént a háttér-szolgáltatásban, a szolgáltatás lekérhet egy Azure AD-tokent, kicserélheti az Azure térbeli horgonyok hozzáférési jogkivonatára, majd visszaküldheti azt az ügyfélalkalmazás számára.

Az Azure AD hozzáférési jogkivonatot a [MSAL](../../active-directory/develop/msal-overview.md)keresztül kéri le a rendszer. Kövesse az [alkalmazás regisztrálása](../../active-directory/develop/quickstart-register-app.md)című útmutató lépéseit, amelyek a következőket tartalmazzák:

**A Azure Portal**
1.    Az alkalmazás regisztrálása az Azure AD-ben:
        1.    A Azure Portal válassza a **Azure Active Directory** lehetőséget, majd válassza a **Alkalmazásregisztrációk** lehetőséget.
        2.    Válassza az **új regisztráció** lehetőséget.
        3.    Adja meg az alkalmazás nevét, válassza a **webalkalmazás/API** lehetőséget az alkalmazás típusaként, és adja meg a szolgáltatáshoz tartozó hitelesítési URL-címet. Válassza a **Létrehozás** lehetőséget.
2.    Az alkalmazásban válassza a **Beállítások** lehetőséget, majd válassza a **tanúsítványok és titkok** lapot. Hozzon létre egy új ügyfél titkot, válasszon ki egy időtartamot, majd kattintson a **Hozzáadás** gombra. Ügyeljen arra, hogy a titkos értéket mentse. A webszolgáltatás kódjában szerepelnie kell a szolgáltatásnak.
3.    Adja meg az alkalmazás és/vagy a felhasználók hozzáférését az erőforráshoz:
        1.    Lépjen a térbeli horgonyok erőforrásra a Azure Portal.
        2.    Lépjen a **hozzáférés-vezérlés (iam)** lapra.
        3.    Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
        4.    [Válasszon egy szerepkört](#azure-role-based-access-control).
        5.    A **Select (kiválasztás** ) mezőben adja meg azon alkalmazások nevét vagy nevét, amelyekhez hozzáférést szeretne hozzárendelni. Ha azt szeretné, hogy az alkalmazás felhasználói különböző szerepkörökkel rendelkezzenek a térbeli horgonyok fiókjában, regisztráljon több alkalmazást az Azure AD-ben, és rendeljen hozzá külön szerepkört. Ezután implementálja az engedélyezési logikát, hogy a megfelelő szerepkört használja a felhasználók számára.

              > [!NOTE]
              > A **szerepkör-hozzárendelés hozzáadása** panelen, a **hozzáférés hozzárendelése**, válassza az **Azure ad-felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget.

        6.    Kattintson a **Mentés** gombra.

**A kódban**

>[!NOTE]
> Használhatja a [térbeli horgonyok mintájának](https://github.com/Azure/azure-spatial-anchors-samples)részeként elérhető [szolgáltatási mintát](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample) .

1.    Ügyeljen arra, hogy a saját Azure AD-alkalmazásának alkalmazás-AZONOSÍTÓját, az alkalmazás titkos kulcsát és átirányítási URI-JÁT a MSAL **ügyfél-azonosító**, **titkos** és **RedirectUri** paramétereként használja.
2.    Állítsa be a bérlő AZONOSÍTÓját saját Azure AD-bérlői AZONOSÍTÓra a MSAL-ben a **Authority** paraméterben.
3.    A jogkivonat-kérelemben állítsa a **hatókört** **" `https://sts.<account-domain>//.default` "** értékre, ahol a `<account-domain>` helyére az Azure térbeli horgonyok fiókjának **fiók tartománya** kerül. Az USA 2. keleti régiójában található Azure térbeli horgonyok fiókjának hatóköre: " **`https://sts.mixedreality.azure.com//.default` "**.

A lépések elvégzése után a háttér-szolgáltatás lekérhet egy Azure AD-tokent. Ezután egy MR-tokenre cserélheti azt, amelyet vissza fog térni az ügyfélnek. A MR-tokenek lekéréséhez egy Azure AD-tokent kell használni REST-hívással. Íme egy példa a hívásra:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Az engedélyezési fejléc a következőképpen van formázva: `Bearer <Azure_AD_token>`

A válasz egyszerű szövegben tartalmazza az MR tokent.

Ekkor a rendszer visszaküldi az MR tokent az ügyfélnek. Az ügyfélalkalmazás ezt követően beállíthatja hozzáférési jogkivonatként a felhőalapú munkamenet-konfigurációban:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

A szolgáltatás alkalmazásaihoz, szolgáltatásaihoz vagy az Azure AD-felhasználókhoz nyújtott hozzáférés szintjének szabályozása érdekében a meglévő szerepköröket igény szerint hozzárendelheti az Azure térbeli horgonyok fiókjaihoz:

- **Térbeli horgonyok fiókjának tulajdonosa**. Az ezzel a szerepkörrel rendelkező alkalmazások vagy felhasználók térbeli horgonyokat hozhatnak létre, lekérdezéseket végezhetnek, és törölhetik azokat. Ha a fiók kulcsainak használatával végzi a hitelesítést a fiókjával, a rendszer hozzárendeli a térbeli horgonyok fiók tulajdonosi szerepkörét a hitelesített rendszerbiztonsági tag számára.
- **Térbeli horgonyok fiók közreműködője**. A szerepkörrel rendelkező alkalmazások vagy felhasználók térbeli horgonyokat és lekérdezéseket hozhatnak létre, de nem törölhetik őket.
- **Térbeli horgonyok fiókjának olvasója**. Az ezzel a szerepkörrel rendelkező alkalmazások vagy felhasználók csak térbeli horgonyokat tudnak lekérdezni. Nem hozhatnak létre újakat, törölhetik a meglévőket, vagy frissíthetik a metaadatokat. Ez a szerepkör jellemzően olyan alkalmazásokhoz használatos, ahol egyes felhasználók a környezetet használják, de mások csak a környezetbe helyezett horgonyokat tudják visszahívni.

## <a name="next-steps"></a>Következő lépések

Hozza létre első alkalmazását az Azure térbeli Horgonyokkal:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)

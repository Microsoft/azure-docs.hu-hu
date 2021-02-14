---
title: Alkalmazás-Szerepkörök hozzáadása és jogkivonatból való beszerzése | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat hozzá alkalmazás-szerepköröket egy Azure Active Directory regisztrált alkalmazáshoz, hogyan rendelhet hozzá felhasználókat és csoportokat ezekhez a szerepkörökhöz, és hogyan fogadhatja azokat a tokenben található "roles" jogcímben.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fce963bd9ffdc6f768d7b3de4a9e4870add06136
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104246"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Útmutató: alkalmazás-Szerepkörök hozzáadása az alkalmazáshoz és fogadása a jogkivonatban

A szerepköralapú hozzáférés-vezérlés (RBAC) egy népszerű mechanizmus az alkalmazások engedélyezésének kényszerítéséhez. A RBAC használatakor a rendszergazda engedélyeket biztosít a szerepkörökhöz, és nem az egyes felhasználókhoz vagy csoportokhoz. A rendszergazda ezután szerepköröket rendelhet a különböző felhasználókhoz és csoportokhoz annak szabályozásához, hogy ki férhet hozzá a tartalomhoz és a funkciókhoz.

Az RBAC és a szerepkör-jogcímek használatával a fejlesztők kevesebb erőfeszítéssel biztonságosan tudják érvényesíteni az alkalmazásokban lévő engedélyeket.

Egy másik módszer az Azure AD-csoportok és a csoportos jogcímek használata az [Active Directory-aspnetcore-WebApp-openidconnect-v2](https://aka.ms/groupssample) code minta használatával a githubon. Az Azure AD-csoportok és az alkalmazás-szerepkörök nem zárják ki egymást. használhatók párhuzamosan, hogy még finomabb hozzáférés-vezérlést nyújtsanak.

## <a name="declare-roles-for-an-application"></a>Szerepkörök deklarálása egy alkalmazáshoz

Az alkalmazás-szerepköröket a [Azure Portal](https://portal.azure.com)használatával határozhatja meg. Az alkalmazás szerepkörei általában egy szolgáltatást, alkalmazást vagy API-t képviselő alkalmazás-regisztráción vannak meghatározva. Amikor egy felhasználó bejelentkezik az alkalmazásba, az Azure AD egy `roles` jogcímet bocsát ki minden olyan szerepkörhöz, amelyet a felhasználó vagy a szolgáltatásnév egyénileg adott meg a felhasználónak és a csoport tagságának. Ez a jogcím-alapú hitelesítés megvalósítására használható. Az alkalmazás szerepkörei hozzárendelhetők [egy felhasználóhoz vagy egy felhasználói csoporthoz](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app)is. Az alkalmazás szerepkörei más alkalmazásokhoz, illetve [felügyelt identitásokhoz](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md)is hozzárendelhetők az egyszerű szolgáltatáshoz.

> [!IMPORTANT]
> Jelenleg, ha egy egyszerű szolgáltatásnevet ad hozzá egy csoporthoz, majd hozzárendel egy alkalmazás-szerepkört az adott csoporthoz, az Azure AD nem adja hozzá a `roles` jogcímet az általa felmerülő jogkivonatokhoz.

Az alkalmazás szerepköreinek kétféle módon deklarálható a Azure Portal használatával:

* Az [alkalmazás szerepköreinek felhasználói felülete](#app-roles-ui--preview) | Előnézet
* [Alkalmazás jegyzékfájl-szerkesztője](#app-manifest-editor)

A hozzáadott szerepkörök száma az Azure Active Directory által kényszerített alkalmazások jegyzékfájljának korlátaihoz tartozik. További információ ezekről a korlátokról: [Azure Active Directory alkalmazás jegyzékfájljának](reference-app-manifest.md) [jegyzéke – a jegyzékfájl korlátai](./reference-app-manifest.md#manifest-limits) szakasza.

### <a name="app-roles-ui--preview"></a>Az alkalmazás szerepköreinek felhasználói felülete | Előnézet

> [!IMPORTANT]
> Az alkalmazás szerepkörei portál felhasználói felületének funkciója [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Alkalmazás-szerepkör létrehozása a Azure Portal felhasználói felületének használatával:

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a Azure Active Directory bérlőt, amely az alkalmazás regisztrációját tartalmazza, amelyhez hozzá kívánja adni az alkalmazás-szerepkört.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki azt az alkalmazást, amelyben meg szeretné adni az alkalmazás-szerepköröket.
1. **Alkalmazás-szerepkörök kiválasztása | Tekintse meg az előnézetet**, majd válassza az **alkalmazás szerepkör létrehozása** lehetőséget.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Az alkalmazás regisztrációjának alkalmazás-szerepkörök panelje a Azure Portal":::
1. Az **alkalmazás-szerepkör létrehozása** panelen adja meg a szerepkör beállításait. A képet követő táblázat ismerteti az egyes beállításokat és azok paramétereit.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Az alkalmazás-regisztráció alkalmazásbeli szerepkörei a Azure Portal helyi paneljén hozhatók létre.":::

    | Mező | Leírás | Példa |
    |-------|-------------|---------|
    | **Megjelenített név** | A rendszergazdai jogosultságok és az alkalmazás-hozzárendelések tapasztalataiban megjelenő alkalmazás-szerepkör megjelenített neve. Ez az érték szóközöket is tartalmazhat. | `Survey Writer` |
    | **Engedélyezett tagok Típusai** | Meghatározza, hogy az alkalmazás-szerepkör hozzárendelhető-e felhasználókhoz, alkalmazásokhoz vagy mindkettőhöz.<br/><br/>Ha elérhető, az alkalmazás `applications` szerepkörei alkalmazások engedélyeiként jelennek meg az alkalmazás regisztrációjának **kezelés** szakaszában > API-engedélyek, > engedély hozzáadása az api-khoz > az api-k **> válasszon egy API > alkalmazás-engedélyeket**. | `Users/Groups` |
    | **Érték** | Annak a szerepkör-jogcímnek az értékét adja meg, amelyet az alkalmazásnak el kell várnia a jogkivonatban. Az értéknek pontosan egyeznie kell az alkalmazás kódjában hivatkozott karakterlánccal. Az érték nem tartalmazhat szóközöket. | `Survey.Create` |
    | **Leírás** | Az alkalmazás szerepkör részletesebb leírása, amely a rendszergazdai alkalmazások hozzárendelésekor és a beleegyező felhasználói élményekben jelenik meg. | `Writers can create surveys.` |
    | **Engedélyezi ezt az alkalmazási szerepkört?** | Megadja, hogy engedélyezve van-e az alkalmazás szerepköre. Egy alkalmazás-szerepkör törléséhez törölje a jelölőnégyzet jelölését, majd alkalmazza a módosítást a törlési művelet megkísérlése előtt. | *Ellenőrizni* |

1. A módosítások mentéséhez válassza az **Alkalmaz** elemet.

### <a name="app-manifest-editor"></a>Alkalmazás jegyzékfájl-szerkesztője

Szerepkörök hozzáadása a jegyzékfájl közvetlen szerkesztésével:

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a Azure Active Directory bérlőt, amely az alkalmazás regisztrációját tartalmazza, amelyhez hozzá kívánja adni az alkalmazás-szerepkört.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki azt az alkalmazást, amelyben meg szeretné adni az alkalmazás-szerepköröket.
1. A **kezelés** alatt kattintson ismét a **manifest (jegyzékfájl**) elemre.
1. Szerkessze az alkalmazás-jegyzékfájlt úgy, hogy megkeresi a `appRoles` beállítást, és hozzáadja az alkalmazási szerepköröket. Megadhatja a célként megadott vagy mindkét szerepkörű alkalmazást `users` `applications` . A következő JSON-kódrészletek példákat mutatnak.
1. Mentse a jegyzékfájlt.

A jegyzékfájlban szereplő összes alkalmazás-szerepkör definíciójának egyedi GUID azonosítóval kell rendelkeznie az `id` értékhez.

Az `value` egyes alkalmazás-szerepkör-definíciók tulajdonságának pontosan egyeznie kell az alkalmazás kódjában használt karakterláncokkal. A `value` tulajdonság nem tartalmazhat szóközöket. Ha igen, hibaüzenetet kap a jegyzékfájl mentésekor.

#### <a name="example-user-app-role"></a>Példa: felhasználói alkalmazás szerepkör

Ez a példa egy nevű alkalmazás-szerepkört határoz meg, amely a következőhöz `Writer` rendelhető hozzá `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Példa: Application app role

Ha elérhető, az alkalmazás `applications` szerepkörei alkalmazások engedélyeiként jelennek meg az alkalmazás regisztrációjának **kezelés** szakaszában > API-engedélyek, > engedély hozzáadása az api-khoz > az api-k **> válasszon egy API > alkalmazás-engedélyeket**.

Ez a példa egy olyan alkalmazás-szerepkört mutat be, amely a következőre irányul `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Felhasználók és csoportok társítása szerepkörökhöz

Miután hozzáadta az alkalmazás-szerepköröket az alkalmazáshoz, hozzárendelhet felhasználókat és csoportokat a szerepkörökhöz. A felhasználók és csoportok szerepkörökhöz való hozzárendelése a portál felhasználói felületén vagy programozott módon, [Microsoft Graph](/graph/api/user-post-approleassignments)használatával végezhető el. Ha a különböző alkalmazás-szerepkörökhöz hozzárendelt felhasználók bejelentkeznek az alkalmazásba, a jogkivonatok a jogcímek számára a hozzárendelt szerepkörökkel rendelkeznek `roles` .

Felhasználók és csoportok társítása a szerepkörökhöz a Azure Portal használatával:

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. **Azure Active Directory** a bal oldali navigációs menüben válassza a **vállalati alkalmazások** lehetőséget.
1. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját. Ha az alkalmazás nem jelenik meg a listában, használja a **minden alkalmazás** lista tetején található szűrőket a lista korlátozásához, vagy görgessen le a listából az alkalmazás megkereséséhez.
1. Válassza ki azt az alkalmazást, amelyben hozzá kívánja rendelni a felhasználókat vagy a biztonsági csoportokat a szerepkörökhöz.
1. A **Kezelés** menüpontban válassza a **Felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** elemet a **hozzárendelés hozzáadása** ablaktábla megnyitásához.
1. Válassza ki a **felhasználók és csoportok** választót a **hozzárendelés hozzáadása** panelen. Megjelenik a felhasználók és a biztonsági csoportok listája. Kereshet egy adott felhasználót vagy csoportot, valamint kijelölhet több felhasználót és csoportot is, amelyek megjelennek a listában.
1. A felhasználók és csoportok kiválasztása után a folytatáshoz kattintson a **kiválasztás** gombra.
1. Válassza a **szerepkör kiválasztása** lehetőséget a **hozzárendelés hozzáadása** panelen. Megjelenik az alkalmazáshoz definiált összes szerepkör.
1. Válasszon egy szerepkört, és kattintson a **kiválasztás** gombra.
1. Kattintson a **hozzárendelés** gombra a felhasználók és csoportok alkalmazáshoz való hozzárendelésének befejezéséhez.

Győződjön meg arról, hogy a hozzáadott felhasználók és csoportok megjelennek a **felhasználók és csoportok** listában.

## <a name="assign-app-roles-to-applications"></a>Alkalmazás szerepköreinek kiosztása alkalmazásokhoz

Miután hozzáadta az alkalmazás-szerepköröket az alkalmazáshoz, hozzárendelhet egy alkalmazás-szerepkört az ügyfélalkalmazás számára a Azure Portal használatával vagy programozott módon, [Microsoft Graph](/graph/api/user-post-approleassignments)használatával.

Amikor alkalmazás-szerepköröket rendel hozzá egy alkalmazáshoz, *alkalmazás-engedélyeket* hoz létre. Az alkalmazásra vonatkozó engedélyeket általában Daemon-alkalmazások vagy háttér-szolgáltatások használják, amelyeknek a hitelesítését és a hitelesített API-hívásokat saját maguknak kell elvégezniük a felhasználó beavatkozása nélkül.

Alkalmazás-szerepkörök hozzárendeléséhez a Azure Portal használatával:

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. **Azure Active Directory** a bal oldali navigációs menüben válassza a **Alkalmazásregisztrációk** lehetőséget.
1. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját. Ha az alkalmazás nem jelenik meg a listában, használja a **minden alkalmazás** lista tetején található szűrőket a lista korlátozásához, vagy görgessen le a listából az alkalmazás megkereséséhez.
1. Válassza ki azt az alkalmazást, amelyhez hozzá szeretné rendelni az alkalmazás-szerepkört.
1. Válassza az **API-engedélyek** engedély  >  **hozzáadása** lehetőséget.
1. Válassza a **saját API** -k fület, majd válassza ki azt az alkalmazást, amelyhez az alkalmazás-szerepköröket definiálta.
1. Válassza ki az **alkalmazás engedélyeit**.
1. Válassza ki a hozzárendelni kívánt szerepkör (eke) t.
1. Válassza az **engedélyek hozzáadása** gombot a szerepkör (ok) hozzáadásához.

Az újonnan hozzáadott szerepköröknek meg kell jelennie az alkalmazás regisztrációs **API-engedélyei** ablaktábláján.

#### <a name="grant-admin-consent"></a>Rendszergazdai jóváhagyás megadása

Mivel ezek az *alkalmazások engedélyei*, nem delegált engedélyek, a rendszergazdának meg kell adnia a jóváhagyást az alkalmazáshoz rendelt alkalmazás-szerepkörök használatához.

1. Az alkalmazás-regisztráció **API-engedélyei** panelen válassza a **\<tenant name\> rendszergazdai jóváhagyás megadása** lehetőséget.
1. Válassza az **Igen** lehetőséget, ha a rendszer kéri, hogy adja meg a kért engedélyek jóváhagyását.

Az **állapot** oszlopban meg kell jelennie, hogy a beleegyezik **a következőhöz: \<tenant name\>**.

## <a name="use-app-roles-in-your-web-api"></a>Alkalmazás-szerepkörök használata a webes API-ban

Miután definiálta az alkalmazás-szerepköröket, és hozzárendelte őket egy felhasználóhoz, csoporthoz vagy alkalmazáshoz, a következő lépés a kód hozzáadása a webes API-hoz, amely az API meghívásakor ellenőrzi ezeket a szerepköröket. Ez azt eredményezi, hogy ha egy ügyfélalkalmazás olyan API-műveletet kér, amelyet úgy döntött, hogy engedélyt kér, az API kódjának ellenőriznie kell a hatóköröket az ügyfélalkalmazás hívásában bemutatott hozzáférési jogkivonatban.

A webes API-hoz való hozzáférés engedélyezéséről a [védett webes API: hatókörök és alkalmazás-szerepkörök ellenőrzése](scenario-protected-web-api-verification-scope-app-roles.md)című témakörben olvashat.

## <a name="app-roles-vs-groups"></a>Alkalmazás-szerepkörök és csoportok

Bár az alkalmazás-szerepköröket vagy-csoportokat használhatja az engedélyezéshez, a köztük lévő legfontosabb különbségek befolyásolhatják, hogy milyen módon kívánja használni a forgatókönyvet.

| Alkalmazás szerepkörei                                                                          | Csoportok                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Ezek egy adott alkalmazásra vonatkoznak, és az alkalmazás regisztrálásakor vannak meghatározva. Az alkalmazással együtt mozognak. | Nem kifejezetten egy alkalmazásra, hanem egy Azure AD-bérlőre vonatkoznak. |
| Az alkalmazás szerepkörei el lesznek távolítva az alkalmazás regisztrációjának törlésekor.                      | A csoportok akkor is érintetlenek maradnak, ha az alkalmazást eltávolították.            |
| A `roles` jogcímben van megadva.                                                     | A `groups` jogcímben van megadva.                                 |

A fejlesztők az alkalmazás szerepkörei segítségével szabályozhatják, hogy egy felhasználó tud-e bejelentkezni egy alkalmazásba, vagy egy alkalmazás hozzáférési jogkivonatot szerezhet be egy webes API-hoz. Ahhoz, hogy a biztonsági vezérlőt csoportokba lehessen terjeszteni, a fejlesztők és a rendszergazdák biztonsági csoportokat is hozzárendelhet az alkalmazás szerepköreihez.

Az alkalmazási szerepköröket a fejlesztők részesítik előnyben, amikor az alkalmazásban lévő engedélyezési paramétereket szeretnék leírni és szabályozni. Például az engedélyezéshez csoportokat használó alkalmazások a következő bérlőn lesznek megszakítva, mivel a csoport azonosítója és neve is eltérő lehet. Az alkalmazás-szerepköröket használó alkalmazások biztonságosak maradnak. Valójában a csoportok az alkalmazás szerepköreihez való hozzárendelésével azonos okokból népszerűek az SaaS-alkalmazások.

## <a name="next-steps"></a>Következő lépések

További információ az alkalmazás szerepköreiről a következő erőforrásokkal.

* Kódminták a GitHubon
  * [Engedélyezés hozzáadása csoportok és csoportos jogcímek használatával egy ASP.NET Core webalkalmazáshoz](https://aka.ms/groupssample)
  * [.NET Core webes API-t hívó egyoldalas alkalmazás (SPA) és alkalmazás-szerepkörök és biztonsági csoportok használata](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Dokumentáció
  * [Azure AD-alkalmazás jegyzékfájlja](./reference-app-manifest.md)
  * [Azure AD hozzáférési jogkivonatok](access-tokens.md)
  * [Azure AD azonosító tokenek](id-tokens.md)
  * [Opcionális jogcímek megadása az alkalmazás számára](active-directory-optional-claims.md)
* Videó: [Engedélyezés alkalmazása az alkalmazásokban a Microsoft Identity platformmal](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)

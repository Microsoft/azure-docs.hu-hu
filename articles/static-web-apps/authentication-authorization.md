---
title: Hitelesítés és engedélyezés az Azure statikus Web Apps
description: Megtudhatja, hogyan használhatja a különböző engedélyezési szolgáltatókat a statikus alkalmazások biztonságossá tételéhez.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: cb6b99351a5cb995d87b482b7e707a3913fd86f2
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597624"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Hitelesítés és engedélyezés az Azure statikus Web Apps előzetes verziójában

Az Azure statikus Web Apps a következő szolgáltatók hitelesítésének kezelésével egyszerűsíti a hitelesítési élményt:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

A szolgáltatóra vonatkozó [meghívások](#invitations) társítják a felhasználókat a szerepkörökhöz, és az engedéllyel rendelkező felhasználók a _Routes. JSON_ fájlban meghatározott szabályok szerint kapnak hozzáférést az [útvonalakhoz](routes.md) .

Alapértelmezés szerint minden hitelesítő szolgáltató engedélyezve van. A hitelesítési szolgáltató korlátozásához tiltsa le a [hozzáférést](#block-an-authorization-provider) egy egyéni útválasztási szabállyal.

A hitelesítés és az engedélyezés témaköre jelentősen átfedésben van az útválasztási fogalmakkal. Olvassa el az [útválasztási útmutatót](routes.md) a jelen cikk mellett.

## <a name="roles"></a>Szerepkörök

Minden olyan felhasználó, aki egy statikus webalkalmazáshoz fér hozzá, egy vagy több szerepkörhöz tartozik.  A felhasználók két beépített szerepkörrel rendelkezhetnek:

- **Névtelen**: minden felhasználó automatikusan a _Névtelen_ szerepkörhöz tartozik.
- **hitelesített**: a bejelentkezett összes felhasználó a _hitelesített_ szerepkörbe tartozik.

A beépített szerepkörökön kívül új szerepköröket is létrehozhat, amelyeket meghívókkal rendelhet hozzá a felhasználókhoz, és hivatkozhat rájuk az _Routes. JSON_ fájlban.

## <a name="role-management"></a>Szerepkör-kezelés

### <a name="add-a-user-to-a-role"></a>Felhasználó hozzáadása szerepkörhöz

Ha felhasználókat szeretne hozzáadni a webhelyhez, olyan meghívókat hoz létre, amelyek lehetővé teszik a felhasználók adott szerepkörökhöz való hozzárendelését. A szerepkörök meghatározása és karbantartása az _Routes. JSON_ fájlban történik.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Meghívás létrehozása

A meghívások egyediek az egyes engedélyezési szolgáltatók számára, ezért érdemes megfontolni az alkalmazás igényét, ahogy kiválasztja, hogy melyik szolgáltatót szeretné támogatni. Egyes szolgáltatók teszik elérhetővé a felhasználó e-mail-címét, míg mások csak a hely felhasználónevét biztosítják.

<a name="provider-user-details" id="provider-user-details"></a>

| Engedélyezési szolgáltató | A felhasználó  |
| ---------------------- | ----------------- |
| Azure Active Directory | e-mail-cím;     |
| Facebook               | e-mail-cím;     |
| GitHub                 | felhasználónév          |
| Google<sup>1</sup>     | e-mail-cím;     |
| Twitter                | felhasználónév          |

1. Navigáljon a [Azure Portal](https://portal.azure.com)statikus Web Apps erőforrásához.
1. A _Beállítások_területen kattintson a **szerepkör-kezelés**elemre.
1. Kattintson a **meghívás** gombra.
1. Válasszon ki egy _engedélyezési szolgáltatót_ a lehetőségek listájából.
1. Adja hozzá a címzett felhasználónevét vagy e-mail-címét a _Meghívási részletek_ mezőben.
    - A GitHub és a Twitter esetében adja meg a felhasználónevet. Minden más esetében adja meg a címzett e-mail-címét.
1. Válassza ki a statikus hely tartományát a _tartomány_ legördülő listából.
    - A kiválasztott tartomány a meghívóban megjelenő tartomány. Ha a webhelyhez egyéni tartomány van társítva, akkor valószínűleg az egyéni tartományt szeretné kiválasztani.
1. Adja hozzá a szerepkörök neveinek vesszővel tagolt listáját a _szerepkör_ mezőben.
1. Adja meg azt a maximális óraszámot, ameddig a meghívónak érvényesnek kell lennie.
    - A maximális lehetséges korlát 168 óra, ami 7 nap.
1. Kattintson az **Generate** (Létrehozás) gombra.
1. Másolja a hivatkozást a _meghívó hivatkozás_ mezőjéből.
1. Küldje el a meghívót arra a személyre, akinek hozzáférést ad az alkalmazásához.

Amikor a felhasználó a meghívóban található hivatkozásra kattint, a rendszer kéri, hogy jelentkezzen be a megfelelő fiókkal. Miután sikeresen bejelentkezett, a felhasználó társítva lesz a kiválasztott szerepkörökhöz.

> [!CAUTION]
> Győződjön meg arról, hogy az útválasztási szabályok nem ütköznek a kiválasztott hitelesítő szolgáltatókkal. A szolgáltató útválasztási szabállyal való blokkolásával megakadályozható, hogy a felhasználók meghívókat fogadnak.

### <a name="update-role-assignments"></a>Szerepkör-hozzárendelések frissítése

1. Navigáljon a [Azure Portal](https://portal.azure.com)statikus Web Apps erőforrásához.
1. A _Beállítások_területen kattintson a **szerepkör-kezelés**elemre.
1. Kattintson a listában szereplő felhasználóra.
1. Szerkessze a szerepkörök listáját a _szerepkör_ mezőben.
1. Kattintson a **frissítés** gombra.

### <a name="remove-user"></a>Felhasználó eltávolítása

1. Navigáljon a [Azure Portal](https://portal.azure.com)statikus Web Apps erőforrásához.
1. A _Beállítások_területen kattintson a **szerepkör-kezelés**elemre.
1. Keresse meg a felhasználót a listában.
1. Jelölje be a jelölőnégyzetet a felhasználó sorában.
1. Kattintson a **Törlés** gombra.

Ha eltávolít egy felhasználót, vegye figyelembe a következő elemeket:

1. A felhasználó eltávolítása érvényteleníti az engedélyeiket.
1. A globális propagálás több percet is igénybe vehet.
1. Ha a felhasználó vissza lett adva az alkalmazáshoz, a [ `userId` módosítások](user-information.md).

## <a name="remove-personal-identifying-information"></a>Személyes azonosító adatok eltávolítása

Ha a jóváhagyást végfelhasználóként adja meg egy alkalmazáshoz, az alkalmazás az azonosítótól függően hozzáfér az e-mail-címéhez vagy a felhasználónevéhez. Az információk megadását követően az alkalmazás tulajdonosa dönti el, hogyan kezelje a személyazonosításra alkalmas adatokat.

A végfelhasználóknak kapcsolatba kell lépniük az egyes webalkalmazások rendszergazdájával, hogy visszavonják ezeket az információkat a rendszeréből.

Ha el szeretné távolítani a személyazonosításra alkalmas adatokat az Azure statikus Web Apps platformról, és megakadályozza, hogy a platform a jövőbeli kérelmekre vonatkozó információkat szolgáltasson, küldje el a kérelmet az URL-cím használatával:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Ahhoz, hogy a platform ne szolgáltassa ezeket az információkat az egyes alkalmazásokra vonatkozó jövőbeli kérésekről, küldje el a kérelmet a következő URL-címre:

```url
https://<WEB_APP_DOMAIN_NAME>/identity/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Rendszermappa

Az Azure statikus Web Apps a `/.auth` rendszermappa használatával biztosít hozzáférést az engedélyezéssel kapcsolatos API-khoz. Ahelyett, hogy a mappa valamelyik útvonalát közvetlenül a végfelhasználók számára tenné közzé `/.auth` , érdemes lehet [útválasztási szabályokat](routes.md) létrehozni a felhasználóbarát URL-címek létrehozásához.

## <a name="login"></a>Bejelentkezés

A következő táblázat segítségével megkeresheti a szolgáltatóra jellemző bejelentkezési útvonalat.

| Engedélyezési szolgáltató | Bejelentkezési útvonal             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Például a GitHub használatával való bejelentkezéshez a következő kódrészlethez hasonló bejelentkezési hivatkozás is szerepelhet:

```html
<a href="/.auth/login/github">Login</a>
```

Ha úgy döntött, hogy egynél több szolgáltatót támogat, akkor az egyes webhelyekhez tartozó szolgáltatói hivatkozást kell közzétennie.

Egy [útvonal-szabály](routes.md) használatával leképezheti az alapértelmezett szolgáltatót egy olyan barátságos útvonalra _, mint például_a (z).

```json
{
  "route": "/login",
  "serve": "/.auth/login/github"
}
```

## <a name="logout"></a>Kijelentkezés

Az `/.auth/logout` útvonal naplózza a felhasználókat a webhelyről. Hozzáadhat egy hivatkozást a webhely navigáláshoz, amely lehetővé teszi a felhasználó számára az alábbi példában látható módon való kijelentkezést.

```html
<a href="/.auth/logout">Log out</a>
```

Egy [útvonal-szabály](routes.md) segítségével rövid útvonalat (például _/logout_) képezhető le.

```json
{
  "route": "/logout",
  "serve": "/.auth/logout"
}
```

## <a name="block-an-authorization-provider"></a>Engedélyezési szolgáltató letiltása

Előfordulhat, hogy korlátozni szeretné az alkalmazást egy engedélyezési szolgáltató használatával. Előfordulhat például, hogy az alkalmazás csak az [e-mail-címeket közzétevő szolgáltatókon](#provider-user-details)szeretne szabványosítani.

A szolgáltató letiltásához [útválasztási szabályokat](routes.md) hozhat létre, amelyek egy 404-as értéket adnak vissza a letiltott szolgáltatói útvonalra irányuló kérésekhez. Ha például a Twittert szolgáltatóként szeretné korlátozni, adja hozzá a következő útválasztási szabályt.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzáférés a felhasználói hitelesítéshez és az engedélyezési információhoz](user-information.md)

<sup>1</sup> függőben lévő külső tanúsítvány.

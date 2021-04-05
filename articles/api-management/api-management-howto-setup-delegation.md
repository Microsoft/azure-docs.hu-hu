---
title: Felhasználói regisztráció és termék-előfizetés delegálása
description: Megtudhatja, hogyan delegálhat felhasználói regisztrációt és előfizetést harmadik félnek az Azure API Managementban.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 54193c9333c75fd8b973ebe33470fca3617e2f2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341841"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Felhasználói regisztráció és termék-előfizetés delegálása

A delegálás lehetővé teszi, hogy meglévő webhelyét használja a fejlesztői bejelentkezéshez/regisztrációhoz és előfizetéshez a termékekhez, a fejlesztői portál beépített funkciójának használata helyett. Lehetővé teszi, hogy a webhelye a felhasználói adatok tulajdonosa legyen, és egyéni módon végezze el ezeknek a lépéseknek az érvényesítését.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>A fejlesztői bejelentkezés és a regisztráció delegálása

A fejlesztő delegálásához jelentkezzen be, és regisztráljon a meglévő webhelyre, létre kell hoznia egy speciális delegálási végpontot a webhelyén. A API Management fejlesztői portálról kezdeményezett ilyen kérések belépési pontként kell működnie.

Az utolsó munkafolyamat a következő lesz:

1. A fejlesztő rákattint a bejelentkezés vagy a regisztráció hivatkozásra a API Management fejlesztői portálon
2. A böngésző átirányítja a delegálási végpontra
3. Delegálási végpont a Return átirányítással vagy a felhasználó bejelentkezését kérő felhasználói felület
4. A művelet sikeressége után a rendszer visszairányítja a felhasználót a API Management fejlesztői portálra.

Első lépésként először állítsa be API Management a kérelmek átirányítását a delegálási végponton keresztül. A Azure Portalban keressen rá a **Biztonság** kifejezésre a API Management erőforrásban, majd kattintson a **delegálás** elemre. A jelölőnégyzet bejelölésével engedélyezheti a "delegált bejelentkezés & regisztráció" lehetőséget.

![Delegálás lap][api-management-delegation-signin-up]

* Döntse el, hogy a speciális delegálási végpont URL-címe milyen lesz, majd adja meg a **delegálási végpont URL-címe** mezőben. 
* A delegálás hitelesítési kulcsa mezőben adja meg azt a titkot, amelyet az ellenőrzéshez megadott aláírás kiszámításához fog használni, hogy a kérelem valóban az Azure API Management legyen. A **Létrehozás** gombra kattintva API Management véletlenszerűen generált kulcsot.

Most létre kell hoznia a **delegálási végpontot**. Számos műveletet kell végrehajtania:

1. Kérelem fogadása a következő formában:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl = {forrás lap URL-címe} &Salt = {string} &SIG = {string}*
   
    A bejelentkezési/regisztrációs eset lekérdezési paraméterei:
   
   * **művelet**: azt határozza meg, hogy milyen típusú delegálási kérelem van – csak ebben az esetben lehet **bejelentkezési**
   * **returnUrl**: annak az oldalnak az URL-címe, amelyen a felhasználó a bejelentkezés vagy a regisztráció hivatkozásra kattint
   * **Salt**: a biztonsági kivonatok feldolgozásához használt speciális sós sztring
   * **SIG**: a saját számított kivonathoz való összehasonlításhoz használt számított biztonsági kivonat
2. Győződjön meg arról, hogy a kérés az Azure API Managementról érkezik (opcionális, de kifejezetten ajánlott a biztonság érdekében)
   
   * HMAC-SHA512 kivonatának kiszámítása a **returnUrl** és a **Salt** lekérdezési paraméterek alapján ([példa az alább megadott kódra]):
     
     > HMAC (**Salt** + "\n" + **returnUrl**)

   * Hasonlítsa össze a fenti számított kivonatot a **SIG** Query paraméter értékével. Ha a két kivonat egyezik, lépjen a következő lépésre, ellenkező esetben tagadja meg a kérést.
3. Győződjön meg arról, hogy a bejelentkezéshez vagy a regisztrációhoz kér kérést: a **művelet** lekérdezési paramétere "**bejelentkezési**" lesz.
4. A felhasználó bejelentkezett felhasználói FELÜLETtel a bejelentkezéshez vagy a regisztrációhoz
5. Ha a felhasználó regisztrál, létre kell hoznia egy hozzá tartozó fiókot API Managementban. [Hozzon létre egy felhasználót] a API Management REST API. Ha így tesz, győződjön meg arról, hogy a felhasználói azonosító értéke megegyezik a felhasználói tárolóban szereplő értékkel, vagy egy olyan AZONOSÍTÓval, amely nyomon követhető.
6. A felhasználó sikeres hitelesítése után:
   
   * [Közös hozzáférési token kérése] a API Management REST API
   * Fűzze hozzá a returnUrl lekérdezési paramétert a fenti API-hívásból kapott SSO URL-címhez:
     
     > például: `https://<developer portal domain, for example: contoso.developer.azure-api.net>/signin-sso?token=<URL-encoded token>&returnUrl=<URL-encoded URL, for example: %2Freturn%2Furl>` 
     
   * A felhasználó átirányítása a fenti létrehozott URL-címre

A **bejelentkezési** műveleten kívül az előző lépések végrehajtásával és a következő műveletek egyikének használatával is elvégezheti a fiókok felügyeletét:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **Kijelentkezés**

A következő lekérdezési paramétereket kell átadnia a fiókkezelés műveleteihez.

* **művelet**: meghatározza, hogy milyen típusú delegálási kérelem (ChangePassword, ChangeProfile vagy CloseAccount)
* **felhasználóazonosító**: a felügyelni kívánt fiók felhasználói azonosítója
* **Salt**: a biztonsági kivonatok feldolgozásához használt speciális sós sztring
* **SIG**: a saját számított kivonathoz való összehasonlításhoz használt számított biztonsági kivonat

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>A termék-előfizetés delegálása

A termék-előfizetés delegálása hasonlóan működik a felhasználói bejelentkezés delegálásakor. Az utolsó munkafolyamat a következő lesz:

1. A fejlesztő kiválaszt egy terméket a API Management fejlesztői portálon, és rákattint az Előfizetés gombra.
2. A böngésző átirányítja a delegálási végpontra.
3. A delegálási végpont a szükséges termék-előfizetési lépéseket hajtja végre. A lépések megtervezése. Tartalmazhatnak átirányítást egy másik lapra a számlázási információk kérelmezéséhez, további kérdések feltevéséhez vagy az adatok egyszerű tárolásához, és nem igényelnek felhasználói beavatkozást.

A funkció engedélyezéséhez a **delegálás** lapon kattintson a **termék-előfizetés delegálása** elemre.

Ezután győződjön meg arról, hogy a delegálási végpont a következő műveleteket végzi el:

1. Kérelem fogadása a következő formában:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation? Operation = {Operation} &Termékkód = {a termék előfizetése} &userId = {User Making Request} &Salt = {string} &SIG = {string}*
   >
   
    Lekérdezési paraméterek a termék-előfizetési esethez:
   
   * **művelet**: azt határozza meg, hogy milyen típusú delegálási kérelem van. A termék-előfizetési kérelmek esetében az érvényes beállítások a következők:
     * "Előfizetés": a felhasználó egy adott termékre való előfizetésére vonatkozó kérelem a megadott AZONOSÍTÓval (lásd alább)
     * "Leiratkozás": egy termék felhasználójának lemondására vonatkozó kérelem
     * "Megújítás": az előfizetés megújítására vonatkozó kérelem (például lejáró lehet)
   * **Termékkód**: *előfizetéskor* – annak a terméknek az azonosítója, amelyre az előfizetést kérte
   * **subscriptionId**: *lemondás* és *megújítás* – a termék-előfizetés azonosítója
   * **felhasználóazonosító**: *előfizetés* – a KÉRÉSt kérő felhasználó azonosítója
   * **Salt**: a biztonsági kivonatok feldolgozásához használt speciális sós sztring
   * **SIG**: a saját számított kivonathoz való összehasonlításhoz használt számított biztonsági kivonat

2. Győződjön meg arról, hogy a kérés az Azure API Managementról érkezik (opcionális, de kifejezetten ajánlott a biztonság érdekében)
   
   * A HMAC-SHA512 kiszámítása a **Termékkód**, a **userId** és a **Salt** lekérdezési paraméterek alapján:
     
     > HMAC (**Salt** + ' \n ' + **Termékkód** + ' \n ' + **userId**)
     > 
     > 
   * Hasonlítsa össze a fenti számított kivonatot a **SIG** Query paraméter értékével. Ha a két kivonat egyezik, lépjen a következő lépésre, ellenkező esetben tagadja meg a kérést.
3. Termék-előfizetés feldolgozása a **műveletben** kért művelet típusa alapján – például számlázás, további kérdések stb.
4. Ha sikeresen előfizette a felhasználót az oldalán lévő termékre, a [REST API előfizetések meghívásával]előfizethet a felhasználóra a API Management termékre.

## <a name="example-code"></a><a name="delegate-example-code"></a> Példa kódja

Ezek a kódok a következőket mutatják be:

* Végezze el a *delegálás érvényesítési kulcsát*, amely a közzétevő portál delegálás képernyőjén van beállítva
* Hozzon létre egy HMAC, amely az aláírás érvényesítésére szolgál, amely igazolja az átadott returnUrl érvényességét.

Ugyanez a kód a termékkód és a userId kis módosításával is működik.

**C#-kód a returnUrl kivonatának létrehozásához**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS-kód a returnUrl kivonatának létrehozásához**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> A delegálási módosítások életbe léptetéséhez újra közzé kell tennie [a fejlesztői portált](api-management-howto-developer-portal-customize.md#publish) .

## <a name="next-steps"></a>Következő lépések
A delegálással kapcsolatos további információkért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Megosztott hozzáférési jogkivonat kérése]: /rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken
[felhasználó létrehozása]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[az előfizetések REST APIának meghívása]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[példa az alább megadott kódra]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png

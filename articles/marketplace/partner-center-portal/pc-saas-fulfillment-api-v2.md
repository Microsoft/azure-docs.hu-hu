---
title: SaaS-teljesítési API-k v2 a Microsoft kereskedelmi piactéren
description: Ismerje meg, hogyan hozhat létre és kezelhet SaaS-ajánlatokat a Microsoft AppSource és az Azure Marketplace-en a teljesítési API-k 2-es verziójának használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712351"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>SaaS-teljesítési API-k 2-es verziója a kereskedelmi piactéren

Ez a cikk azokat az API-kat ismerteti, amelyek lehetővé teszik a partnerek számára, hogy a Microsoft AppSource és az Azure piactéren is eladják a szoftveres szolgáltatásként (SaaS). Az ilyen API-kkal való integráció megvalósításához közzétevőre van szükség az SaaS-ajánlatok a partner Centerben való közzétételéhez.

## <a name="managing-the-saas-subscription-life-cycle"></a>Az SaaS-előfizetési életciklus kezelése

A kereskedelmi piactér egy SaaS-előfizetés teljes életciklusát kezeli a végfelhasználók általi megvásárlása után. A kezdőlapot, a teljesítési API-kat, az operatív API-kat és a webhookot használja a tényleges SaaS-előfizetés aktiválásának, használatának, frissítésének és törlésének elvégzéséhez. A végfelhasználói számla a Microsoft által fenntartott SaaS-előfizetés állapotától függ. 

### <a name="states-of-a-saas-subscription"></a>SaaS-előfizetés állapota

Az alábbi ábrán egy SaaS-előfizetés állapota és a vonatkozó műveletek láthatók.

![Egy, a piactéren szolgáltatás-előfizetésként szolgáló szoftver életciklusát bemutató ábra.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Megvásárolt, de még nincs aktiválva (*PendingFulfillmentStart*)

Ha egy végfelhasználó (vagy CSP) SaaS-ajánlatot vásárol a kereskedelmi piactéren, a közzétevőt értesíteni kell a vásárlásról. A közzétevő ezután létrehozhat és konfigurálhat egy új SaaS-fiókot a közzétevő oldalán a végfelhasználó számára.

Fiók létrehozásához:

1. Az ügyfél a Microsoft AppSource vagy a Azure Portal sikeres vásárlása után a SaaS-ajánlathoz elérhető **configure (Konfigurálás** ) gombot választja. Azt is megteheti, hogy az ügyfél használhatja az e-mailben a **configure (Konfigurálás** ) gombot, amelyet röviddel a vásárlás után fog kapni.
2. A Microsoft Ezután értesíti a partnert a vásárlásról, ha megnyitja az új böngésző lapon a Kezdőlap URL-címét a jogkivonat paraméterrel (a kereskedelmi piactéren a vásárlási azonosító jogkivonat).

Ilyen hívás például az, hogy `https://contoso.com/signup?token=<blob>` az SaaS-ajánlat kezdőlapjának URL-címe a partner Centerben van konfigurálva `https://contoso.com/signup` . Ez a jogkivonat olyan azonosítót biztosít a közzétevőnek, amely egyedileg azonosítja az SaaS-vásárlást és az ügyfelet.

>[!NOTE]
>A közzétevőt nem értesíti a SaaS-vásárlásról, amíg az ügyfél el nem indítja a konfigurációs folyamatot a Microsoft-oldalról.

A Kezdőlap URL-címének minden nap, minden nap, és készen kell állnia arra, hogy az új hívásokat mindig megkapja a Microsofttól. Ha a Kezdőlap elérhetetlenné válik, az ügyfelek nem regisztrálhatnak az SaaS szolgáltatásra, és nem tudják használni azt.

Ezután a közzétevőnek vissza kell adnia a *jogkivonatot* a Microsoftnak a [SaaS-feloldási API](#resolve-a-purchased-subscription)meghívásával, majd a tokent a header paraméter értékének megadásával `x-ms-marketplace-token header` . Az API-hívás feloldásának eredményeképpen a jogkivonat a SaaS-vásárlás részleteit cseréli ki, mint például a vásárlás, a megvásárolt ajánlat azonosítója és a megvásárolt csomag AZONOSÍTÓjának egyedi azonosítója.

A kezdőlapon az ügyfélnek Azure Active Directory (Azure AD) egyszeri bejelentkezéssel (SSO) keresztül be kell jelentkeznie az új vagy meglévő SaaS-fiókba.

A közzétevőnek be kell vezetnie az SSO-t, hogy elérhetővé tegye a Microsoft által ehhez a folyamathoz szükséges felhasználói élményt. Ügyeljen arra, hogy a több-bérlős Azure AD-alkalmazást használja, és engedélyezze a munkahelyi és iskolai fiókokat, illetve a személyes Microsoft-fiókokat az egyszeri bejelentkezés konfigurálásakor. Ez a követelmény csak a kezdőlapra vonatkozik azon felhasználók esetében, akik a SaaS szolgáltatáshoz vannak átirányítva, amikor már bejelentkezett a Microsoft hitelesítő adataival. Az SaaS szolgáltatáshoz nem szükséges az egyszeri bejelentkezés.

> [!NOTE]
>Ha az SSO megköveteli, hogy a rendszergazdának engedélyt kell adnia egy alkalmazáshoz, a partner Centerben lévő ajánlat leírásának közzé kell tennie, hogy rendszergazdai szintű hozzáférésre van szükség. Ez a közzététel megfelel a [kereskedelmi Piactéri minősítési szabályzatoknak](/legal/marketplace/certification-policies#10003-authentication-options).

Bejelentkezés után az ügyfélnek el kell végeznie az SaaS-konfigurációt a közzétevő oldalán. Ezután a közzétevőnek meg kell hívnia az [előfizetés aktiválása API](#activate-a-subscription) -t, hogy küldjön egy jelet az Azure Marketplace-nek, hogy az SaaS-fiók üzembe helyezése befejeződött.
Ez a művelet elindítja az ügyfél számlázási ciklusát. Ha az előfizetés aktiválása API-hívás sikertelen, az ügyfél nem számít fel díjat a vásárlásért.


![Diagram, amely az a P-t hívja a kiépítési forgatókönyvhöz.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktív (*előfizetett*)

Az *aktív (előfizetett)* egy kiépített SaaS-előfizetés állandó állapota. Miután a Microsoft-oldal feldolgozta az [előfizetés aktiválása API](#activate-a-subscription) -hívást, az SaaS-előfizetés *feliratkozottként* van megjelölve. Az ügyfél most már használhatja az SaaS szolgáltatást a kiadó oldalán, és a számlázása is megtörténik.

Ha egy SaaS-előfizetés már aktív, az ügyfél kiválaszthatja az **SaaS-élmény kezelése** lehetőséget a Azure Portal vagy Microsoft 365 felügyeleti központban. Ez a művelet azt is okozhatja, hogy a Microsoft meghívja a Kezdőlap **URL-címét** a *jogkivonat* paraméterrel, ahogy az aktiválja a flow-t. A közzétevőnek különbséget kell tennie az új vásárlások és a meglévő SaaS-fiókok kezelése között, és ennek megfelelően kell kezelnie a Kezdőlap URL-címét.

#### <a name="being-updated-subscribed"></a>Folyamatban van a frissítés (*előfizetett*)

Ez a művelet azt jelenti, hogy egy meglévő aktív SaaS-előfizetés frissítését a Microsoft és a közzétevő dolgozza fel. Ilyen frissítést a következőket kezdeményezhet:

- Az ügyfél a kereskedelmi piactéren.
- A kereskedelmi piactéren található CSP.
- Az ügyfél a közzétevő SaaS-webhelyéről (de a CSP által készített vásárlások esetében nem).

Az SaaS-előfizetések esetében két típusú frissítés érhető el:

- Frissítési terv, ha az ügyfél egy másik csomagot választ az előfizetéshez.
- Mennyiség frissítése, ha az ügyfél megváltoztatja a megvásárolt helyek számát az előfizetéshez.

Csak aktív előfizetés lehet frissítve. Az előfizetés frissítése közben az állapota aktív marad a Microsoft oldalán.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>A kereskedelmi piactéren kezdeményezett frissítés

Ebben a folyamatban az ügyfél módosítja az előfizetési tervet vagy a munkaállomások számát a Azure Portal vagy Microsoft 365 felügyeleti központból.

1. A frissítés beírása után a Microsoft meghívja a közzétevő webhook URL-címét, amelyet a partner Center **kapcsolati webhook** mezőjében konfigurál, és megfelelő értéket kell megadnia a *művelethez* és egyéb releváns paraméterekhez. 
1. A közzétevő oldalnak el kell végeznie a szükséges módosításokat az SaaS szolgáltatásban, és a Microsoft értesítése után értesítenie kell a [műveletet az Operation API frissítési állapotának](#update-the-status-of-an-operation)meghívásával.
1. Ha a javítást *sikertelen* állapottal küldik el, a frissítési folyamat nem fejeződik be a Microsoft oldalán. Az SaaS-előfizetés megtartja a meglévő csomagot és az ülőhelyek mennyiségét.

> [!NOTE]
> A közzétevőnek meg kell hívnia a javítást az [Operation API állapotának frissítéséhez](#update-the-status-of-an-operation) a hiba/sikeres válasz alapján a webhook értesítésének kézhezvételétől számított *10 másodperces időszakon belül* . Ha a művelet állapota nem érkezik meg 10 másodpercen belül, a módosítási terv *automatikusan sikeres* lesz. 

A kereskedelmi piactéren kezdeményezett frissítési forgatókönyv API-hívásainak sorát az alábbi ábra mutatja be.

![A piactér által kezdeményezett frissítésre vonatkozó, a P-t meghívó ábrát ábrázoló diagram.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>A közzétevőtől kezdeményezett frissítés

Ebben a folyamatban az ügyfél az SaaS-szolgáltatástól vásárolt előfizetési tervet vagy munkaállomások mennyiségét módosítja. 

1. A közzétevő kódjának meg kell hívnia a [módosítási terv API](#change-the-plan-on-the-subscription) -t és/vagy a [mennyiség módosítása API](#change-the-quantity-of-seats-on-the-saas-subscription) -t, mielőtt a közzétevő oldalon megtenné a kért változást. 

1. A Microsoft alkalmazza a módosítást az előfizetésre, majd a **kapcsolaton** keresztül értesíti a közzétevőt, hogy alkalmazza ugyanezt a módosítást.

1. Ezt követően a közzétevőnek csak akkor kell végrehajtania a szükséges módosításokat az SaaS-előfizetésben, és értesítenie kell a Microsoftot, ha a módosítást az [Operation API frissítési állapotának](#update-the-status-of-an-operation)meghívásával végzi.

A közzétevő oldalról kezdeményezett frissítési forgatókönyv API-hívásainak sorát az alábbi ábra mutatja be.

![A közzétevői oldal által kezdeményezett frissítéssel rendelkező P-t meghívó ábrát ábrázoló diagram.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Felfüggesztve (*felfüggesztve*)

Ez az állapot azt jelzi, hogy a rendszer nem fogadta el az ügyfél SaaS szolgáltatásának fizetését. A közzétevő értesítést kap erről a változásról a Microsoft SaaS-előfizetési állapotában. Az értesítés a *Felfüggesztettre* beállított *Action* paraméterrel való webhook hívása útján történik.

Előfordulhat, hogy a közzétevő nem módosítja a SaaS szolgáltatást a közzétevő oldalán. Javasoljuk, hogy a közzétevő elérhetővé teszi ezeket az információkat a felfüggesztett ügyfél számára, és korlátozza vagy blokkolja az ügyfél hozzáférését az SaaS szolgáltatáshoz. A fizetés soha nem fog megjelenni.

A Microsoft az előfizetés automatikus megszakítása előtt 30 napos türelmi időszakot biztosít az ügyfélnek. Ha egy előfizetés *felfüggesztett* állapotban van:

* A partnernek vagy az ISV-nek helyreállítható állapotban kell tartania az SaaS-fiókot, így a teljes funkcionalitás visszaállítható az adat-és a beállítások elvesztése nélkül.
* A partnernek vagy az ISV-nak az előfizetés visszavonására vonatkozó kérést kell várnia, ha a fizetés a türelmi időszak alatt érkezik, vagy egy, a türelmi időszak végén kiépített előfizetésre vonatkozó kérelem. Mindkét kérelmet a webhook-mechanizmuson keresztül küldi el a rendszer.

Az előfizetés állapota felfüggesztve állapotra változik a Microsoft oldalon, mielőtt a közzétevő bármilyen műveletet végrehajt. Csak az aktív előfizetéseket lehet felfüggeszteni.

#### <a name="reinstated-suspended"></a>Visszaállítás (*felfüggesztve*)

Ez a művelet azt jelzi, hogy az ügyfél fizetési eszköze ismét érvényes lesz, az SaaS-előfizetésre vonatkozó fizetés történt, és az előfizetés visszaállításra kerül. Ebben az esetben: 

1. A Microsoft meghívja a webhookot egy *műveleti* paraméterrel, amely a *visszaállítás értékre* van beállítva.
1. A közzétevő ellenőrzi, hogy az előfizetés teljesen működőképes-e a közzétevő oldalán.
1. A közzétevő meghívja a [javítási művelet API](#update-the-status-of-an-operation) -ját a sikeres állapottal.
1. Az újratelepítési folyamat sikeres, és az ügyfél számlázása újra történik az SaaS-előfizetés esetében. 

Ha a javítást *sikertelen* állapottal küldik el, a visszaállítási folyamat nem fejeződik be a Microsoft oldalán, és az előfizetés *felfüggesztve* marad.

Csak a felfüggesztett előfizetés állítható vissza. A felfüggesztett SaaS-előfizetés *felfüggesztett* állapotban marad a visszaállítás során. A művelet befejeződése után az előfizetés állapota *aktív* lesz.

#### <a name="renewed-subscribed"></a>Megújított (*előfizetett*)

Az SaaS-előfizetést a Microsoft a hónap vagy egy év előfizetési időszakának végén automatikusan megújítja. Az automatikus megújítási beállítás alapértelmezett értéke az összes SaaS-előfizetés esetében *igaz* . Az aktív SaaS-előfizetések továbbra is megújítva lesznek a rendszeres lépésszám használatával. A Microsoft nem értesíti a közzétevőt az Előfizetés megújítása esetén. Az ügyfelek kikapcsolhatják az SaaS-előfizetések automatikus megújítását a Microsoft 365 felügyeleti portálon keresztül. Ebben az esetben a rendszer az aktuális számlázási időszak végén automatikusan megszakítja az SaaS-előfizetést. Az ügyfél bármikor megszakíthatja az SaaS-előfizetést.

A rendszer csak az aktív előfizetéseket újítja meg automatikusan. Az előfizetések aktívak maradnak a megújítási folyamat során, és ha az automatikus megújítás sikeres. A megújítás után az előfizetési időszak kezdő és záró dátuma frissül az új kifejezés dátumaira.

Ha az automatikus megújítás egy fizetési probléma miatt meghiúsul, akkor az előfizetés fel lesz *függesztve* , és a közzétevő értesítést kap.

#### <a name="canceled-unsubscribed"></a>Megszakított (*leiratkozott*) 

Az előfizetések ezt az állapotot egy explicit ügyfél-vagy CSP-műveletre adott válaszként egy előfizetésnek a közzétevő webhelyről, a Azure Portal vagy Microsoft 365 felügyeleti központból való megszakításával érik el. Egy előfizetést implicit módon is meg lehet szüntetni a díjak fizetésének eredményeképpen, a *felfüggesztett* állapot után 30 napig.

Miután a közzétevő kézhez kapott egy lemondási webhook-hívást, legalább hét napig meg kell őriznie a helyreállításhoz szükséges ügyféladatokat. Csak ezután törölheti az ügyféladatokat.

Az SaaS-előfizetés bármikor megszakítható az életciklusa bármely pontján. Az előfizetés megszakítása után nem aktiválható újra.

## <a name="api-reference"></a>API-referencia

Ez a szakasz a SaaS-előfizetés és az Operations API-k dokumentálása.

Az **előfizetési API** -k segítségével kezelheti a SaaS-előfizetési életciklusát a vásárlástól a törlésig.

Az **operatív API-kat** a következőknek kell használni:

* Ellenőrizze és fogadja el a feldolgozott webhook-hívásokat.
* Szerezze be azon alkalmazások listáját, amelyek a közzétevő által tudomásul vehetők.

> [!NOTE]
> A TLS-verzió 1,2-es verziója hamarosan érvénybe lép a HTTPS-kommunikáció minimális verziójaként. Ügyeljen rá, hogy ezt a TLS-verziót használja a kódban. A TLS 1,0-es és 1,1-es verziója hamarosan elavulttá válik.

### <a name="subscription-apis"></a>Előfizetési API-k

#### <a name="resolve-a-purchased-subscription"></a>Megvásárolt előfizetés feloldása

A feloldási végpont lehetővé teszi, hogy a közzétevő kicserélje a vásárlási azonosító tokent a kereskedelmi piactérről (ez az úgynevezett *jogkivonat* a [megvásárolt, de még nincs aktiválva](#purchased-but-not-yet-activated-pendingfulfillmentstart)) egy állandó megvásárolt SaaS-előfizetés azonosítójával és annak részleteivel.

Amikor a rendszer átirányítja az ügyfelet a partner kezdőlapjának URL-címére, a rendszer az ügyfél-azonosító jogkivonatot az URL-hívásban szereplő *jogkivonat* -paraméterként adja át. A partnernek a tokent kell használnia, és el kell végeznie a feloldását. Az API feloldása válasz tartalmazza a SaaS-előfizetés AZONOSÍTÓját, valamint a vásárlás egyedi azonosításának egyéb részleteit. A Kezdőlap URL-hívásához megadott *jogkivonat* általában 24 órára érvényes. Ha a kapott *jogkivonat* már lejárt, javasoljuk, hogy a következő útmutatást adja meg a végfelhasználónak:

"Nem sikerült azonosítani ezt a vásárlást. Nyissa meg újra ezt az SaaS-előfizetést a Azure Portal vagy a Microsoft 365 felügyeleti központban, és válassza a "fiók konfigurálása" vagy a "fiók kezelése" lehetőséget.

A feloldási API meghívásával az összes támogatott állapotú SaaS-előfizetéshez tartozó előfizetési adatokat és állapotot fogja visszaadni.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Utáni `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.   |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt. A formátum az, `"Bearer <accessaccess_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |
|  `x-ms-marketplace-token`  | A feloldani kívánt beszerzési azonosító *jogkivonat* paramétere.  A rendszer átadja a tokent a Kezdőlap URL-címének hívásakor, ha az ügyfél át lesz irányítva az SaaS-partner webhelyére (például: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  Vegye figyelembe, hogy a kódolt *jogkivonat* -érték a Kezdőlap URL-címének része, ezért dekódolni kell, mielőtt az API-hívás paramétereként használja.  <br> <br> Íme egy példa egy kódolt karakterláncra az URL-címben: `contoso.com/signup?token=ab%2Bcd%2Fef` , ahol a *token* van `ab%2Bcd%2Fef` .  Ugyanaz a jogkivonat dekódolása a következőket eredményezi: `Ab+cd/ef` |
| | |

*Reagálási kódok:*

Kód: a 200 egyedi SaaS-előfizetési azonosítót ad vissza a megadott érték alapján `x-ms-marketplace-token` .

Példa a válasz törzsére:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Kód: 400 hibás kérelem. `x-ms-marketplace-token` hiányzik, helytelen formátumú, érvénytelen vagy lejárt.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amely az engedélyezési jogkivonat létrehozásához használt másik Azure AD-alkalmazás-AZONOSÍTÓval lett közzétéve.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Előfizetés aktiválása

Miután a SaaS-fiók konfigurálva van egy végfelhasználó számára, a közzétevőnek meg kell hívnia az előfizetés aktiválása API-t a Microsoft oldalán.  Az ügyfél számlázása csak akkor történik meg, ha ez az API-hívás sikeres.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Utáni `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 használata.   |
| `subscriptionId` | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az [API feloldása](#resolve-a-purchased-subscription)paranccsal.
 |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a karakterlánc a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`      |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt. A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Adattartalom kérése példa:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Reagálási kódok:*

Kód: 200 az előfizetés a Microsoft oldalán előfizetettként van megjelölve.

Ehhez a híváshoz nincs válasz törzs.

Kód: 400 hibás kérelem: az érvényesítés nem sikerült.

* `planId` nem létezik a kérelem adattartalmát.
* `planId` a kérelem adattartalma nem egyezik meg a megvásárolttal.
* `quantity` a kérelem adattartalma nem egyezik meg a megvásárolttal
* Az SaaS-előfizetés *előfizetett* vagy *felfüggesztett* állapotban van.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva. A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amely az engedélyezési jogkivonat létrehozásához használt másik Azure AD-alkalmazás-AZONOSÍTÓval lett közzétéve.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404. Az SaaS-előfizetés *leiratkozott* állapotban van.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Az összes előfizetés listájának beolvasása

Ez az API lekéri az összes megvásárolt SaaS-előfizetés listáját a közzétevő által a kereskedelmi piactéren közzétett összes ajánlathoz.  A rendszer az összes lehetséges állapotú SaaS-előfizetést adja vissza. A rendszer a leiratkozott SaaS-előfizetéseket is visszaadja, mivel ezek az adatok nem törlődnek a Microsoft-oldalon.

Az API a 100/oldal oldalszámmal elszámolt eredményeket adja vissza.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31 használata.  |
| `continuationToken`  | Nem kötelező megadni. Az eredmények első oldalának lekéréséhez hagyja üresen a következőt:.  `@nextLink`A következő lap beolvasásához használja a paraméterben visszaadott értéket. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`      |  Egyedi hozzáférési jogkivonat, amely az API-hívást készítő közzétevőt azonosítja.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Reagálási kódok:*

Kód: a 200 a közzétevő engedélyezési jogkivonatának alapján a közzétevő által készített összes meglévő előfizetés listáját adja vissza.

*Példa a válasz törzsére:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Ha nem található megvásárolt SaaS-előfizetés ehhez a közzétevőhöz, a rendszer üres választ ad vissza.

Kód: 403 Tiltott. Az engedélyezési jogkivonat nem érhető el, érvénytelen vagy lejárt.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

Kód: 500 belső kiszolgálóhiba. Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Előfizetés lekérése

Ez az API lekéri a megadott megvásárolt SaaS-előfizetést a közzétevő által a kereskedelmi piactéren közzétett SaaS-ajánlathoz. Ezzel a hívással lekérheti az adott SaaS-előfizetéshez tartozó összes elérhető információt az azonosítójával, nem pedig az összes előfizetés listájának lekéréséhez használt API meghívásával.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   2018-08-31 használata. |
| `subscriptionId`     |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `authorization`     | Egyedi hozzáférési jogkivonat, amely az API-hívást készítő közzétevőt azonosítja. A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Code: 200 egy SaaS-előfizetés részleteit adja vissza a `subscriptionId` megadott érték alapján.

*Példa a válasz törzsére:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva. A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD-alkalmazás-AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  A megadott SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Elérhető csomagok listázása

Ez az API az ajánlat adott vásárlása által azonosított SaaS-ajánlat összes csomagját lekéri `subscriptionId` .  Ezzel a hívással lekérheti az összes olyan magán-és nyilvános csomag listáját, amelyet a SaaS-előfizetés kedvezményezettje frissíthet az előfizetésre.  A visszaadott csomagok ugyanabban a földrajzban lesznek elérhetők, mint a már megvásárolt csomag.

Ez a hívás az adott ügyfél számára elérhető csomagok listáját adja vissza, a már megvásároltak mellett.  A lista a közzétevő webhelyén a végfelhasználók számára is megjeleníthető.  A végfelhasználó módosíthatja az előfizetési tervet a visszaadott listán szereplő csomagok bármelyikére.  A terv nem a listában való módosítása sikertelen lesz.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`    |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid`  |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely az API-hívást készítő közzétevőt azonosítja.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Kód: a 200 egy meglévő SaaS-előfizetés összes elérhető csomagjának listáját adja vissza, beleértve a már megvásároltat is.

Példa a válasz törzsére:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Ha `subscriptionId` nem található, az üres válasz törzsét adja vissza.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  Előfordulhat, hogy a kérelem megpróbál hozzáférni egy SaaS-előfizetéshez egy olyan ajánlathoz, amelyet az engedélyezési jogkivonat létrehozásához használt másik Azure AD-alkalmazás-AZONOSÍTÓval közzétett.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Az előfizetés tervének módosítása

Ezzel az API-val frissítheti az SaaS-előfizetéshez megvásárolt meglévő csomagot egy új csomagra (nyilvános vagy privát).  A közzétevőnek meg kell hívnia ezt az API-t, ha a kereskedelmi piactéren megvásárolt SaaS-előfizetésre vonatkozó terv megváltozik a közzétevő oldalán.

Ezt az API-t csak *aktív* előfizetések esetében lehet meghívni.  Bármely terv módosítható bármely más meglévő csomagra (nyilvános vagy magánjellegű), de nem saját magára.  A privát csomagok esetében az ügyfél bérlőjét a csomag célközönségének részeként kell meghatározni a partner Centerben.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
| `subscriptionId`     | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid`  | Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely az API-hívást készítő közzétevőt azonosítja.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Adattartalom kérése példa:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Reagálási kódok:*

Kód: 202 a terv módosítására irányuló kérelem aszinkron módon lett elfogadva és kezelve.  A partnernek a változási tervre vonatkozó kérelem sikerességének vagy meghibásodásának megállapításához a **művelet-hely URL-címét** kell lekérdezni.  A lekérdezésnek néhány másodpercen belül meg kell történnie, amíg a művelet *sikertelen*, *sikeres* vagy *ütközési* állapotba nem kerül.  A végső műveleti állapotot gyorsan kell visszaadni, de bizonyos esetekben több percet is igénybe vehet.

A partner akkor is bekerül a webhook-értesítésbe, ha a művelet készen áll a kereskedelmi piactéren való sikeres befejezésre.  Ekkor a közzétevőnek kell megváltoztatnia a tervet a közzétevő oldalán.

*Válasz fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  A művelet állapotának beolvasására szolgáló URL-cím.  Például: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kód: 400 hibás kérelem: érvényesítési hibák.

* Az új csomag nem létezik, vagy nem érhető el ehhez az adott SaaS-előfizetéshez.
* Az új csomag ugyanaz, mint az aktuális terv.
* Az SaaS-előfizetés állapota nincs *előfizetve*.
* A SaaS-előfizetés frissítési művelete nem része a következőnek: `allowedCustomerOperations` .

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nem lett megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD-alkalmazás-AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>A csomag vagy az ülések mennyisége egyszerre is módosítható, nem mindkettő.

>[!Note]
>Ezt az API-t csak akkor hívhatja a rendszer, ha a végfelhasználótól való váltáshoz explicit jóváhagyást kap.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Az SaaS-előfizetésben lévő ülőhelyek mennyiségének módosítása

Ezzel az API-val frissítheti (növelheti vagy csökkentheti) az SaaS-előfizetéshez megvásárolt helyek mennyiségét.  A közzétevőnek meg kell hívnia ezt az API-t, amikor a kereskedelmi piactéren létrehozott SaaS-előfizetések száma megváltozik a közzétevő oldaláról.

Az ülőhelyek mennyisége nem lehet nagyobb, mint az aktuális tervben engedélyezett mennyiség.  Ebben az esetben a közzétevőnek meg kell változtatnia a tervet a munkaállomások mennyiségének módosítása előtt.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Javítás `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`     | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid`  | Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     | Egyedi hozzáférési jogkivonat, amely az API-hívást készítő közzétevőt azonosítja.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Adattartalom kérése példa:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Reagálási kódok:*

Kód: 202 a mennyiség módosítására irányuló kérelem aszinkron módon lett elfogadva és kezelve. A partnernek a **művelet – hely URL-címével** kell lekérdezni a módosítási mennyiség kérésének sikerességét vagy sikertelenségét.  A lekérdezésnek néhány másodpercen belül meg kell történnie, amíg a művelet *sikertelen*, *sikeres* vagy *ütközési* állapotba nem kerül.  A végső műveleti állapotot gyorsan kell visszaadni, de bizonyos esetekben több percet is igénybe vehet.

A partner akkor is bekerül a webhook-értesítésbe, ha a művelet készen áll a kereskedelmi piactéren való sikeres befejezésre.  Ezt követően a közzétevőnek a mennyiség módosítását kell tennie a közzétevő oldalán.

*Válasz fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.  Például: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Kód: 400 hibás kérelem: érvényesítési hibák.

* Az új mennyiség nagyobb vagy kisebb, mint a jelenlegi csomag korlátja.
* Az új mennyiség hiányzik.
* Az új mennyiség megegyezik az aktuális mennyiséggel.
* Az SaaS-előfizetés állapota nincs előfizetve.
* A SaaS-előfizetés frissítési művelete nem része a következőnek: `allowedCustomerOperations` .

Kód: 403 Tiltott.  Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem olyan előfizetéshez próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Egyszerre csak egy csomag vagy mennyiség módosítható, mindkettő nem lehet egyszerre.

>[!Note]
>Ez az API csak akkor hívható meg, ha a felhasználó explicit jóváhagyást kapott a módosításhoz.

#### <a name="cancel-a-subscription"></a>Előfizetés megszakítása

Ezzel az API-val lemondhatja a megadott SaaS-előfizetést.  A közzétevőnek nem kell használnia ezt az API-t, és javasoljuk, hogy az ügyfeleket a kereskedelmi piactérre irányítsa az SaaS-előfizetések megszakításához.

Ha a közzétevő úgy dönt, hogy végrehajtja a kereskedelmi piactéren a kiadó oldalán megvásárolt SaaS-előfizetés megszüntetését, meg kell hívnia ezt az API-t.  A hívás befejezése után az előfizetés állapota *leiratkozott* lesz a Microsoft oldalán.

Az ügyfél számlázása nem történik meg, ha a következő türelmi időszakon belül megszakítanak egy előfizetést:

* 24 óra az aktiválás után havi előfizetés esetén.
* Tizennégy nap az aktiválás utáni éves előfizetéshez.

Az ügyfél számlázása akkor történik meg, ha az előző türelmi időszak után megszakítják az előfizetést.  Az ügyfél nem fér hozzá a Microsoft oldalán lévő SaaS-előfizetéshez közvetlenül a visszavonás után. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Törlése `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`     | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid`  | Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Reagálási kódok:*

Kód: 202 a leiratkozási kérelem elfogadása és kezelése aszinkron módon megtörtént.  A partnernek meg kell kérdezni a **művelet – hely URL-címét** a kérelem sikerességének vagy meghibásodásának megállapításához.  A lekérdezésnek néhány másodpercen belül meg kell történnie, amíg a művelet *sikertelen*, *sikeres* vagy *ütközési* állapotba nem kerül.  A végső műveleti állapotot gyorsan kell visszaadni, de bizonyos esetekben több percet is igénybe vehet.

A partner akkor is bekerül a webhook-értesítésbe, ha a művelet sikeresen befejeződött a kereskedelmi piactér oldalon.  Ezt követően a közzétevőnek meg kell szakítania az előfizetést a közzétevő oldalán.

*Válasz fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Egy erőforrásra mutató hivatkozás a művelet állapotának lekéréséhez.  Például: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Kód: 400 hibás kérelem.  A DELETE nem szerepel a listában ehhez az `allowedCustomerOperations` SaaS-előfizetéshez.

Kód: 403 Tiltott.  Az engedélyezési jogkivonat érvénytelen, lejárt vagy nem érhető el. A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD-alkalmazás-AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba. Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>Operatív API-k

#### <a name="list-outstanding-operations"></a>Függőben lévő műveletek listázása 

A megadott SaaS-előfizetésre vonatkozó függőben lévő műveletek listájának beolvasása.  A közzétevőnek visszaigazolnia kell a visszaadott műveleteket a [Operation patch API](#update-the-status-of-an-operation)meghívásával.

A rendszer jelenleg csak az **újravisszaállítási műveleteket** adja vissza válaszként ehhez az API-híváshoz.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  2018-08-31 használata.         |
|    `subscriptionId` | A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |

*Kérelem fejlécei:*
 
|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Kód: a 200 visszaadja a megadott SaaS-előfizetés függőben lévő visszahívási műveletét.

*Válasz hasznos adat például:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Üres JSON-értéket ad vissza, ha nincs függőben lévő visszaállítási művelet.

Kód: 400 hibás kérelem: érvényesítési hibák.

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD-alkalmazás-AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  A SaaS-előfizetés `subscriptionId` nem található.

Kód: 500 belső kiszolgálóhiba. Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Művelet állapotának beolvasása

Ez az API lehetővé teszi a közzétevő számára a megadott aszinkron művelet állapotának nyomon követését:  **leiratkozás**, **ChangePlan** vagy **ChangeQuantity**.

Az ehhez az `operationId` API-híváshoz a **művelet – hely**, a függőben lévő Operations API-hívás beolvasása vagy a `<id>` webhook-hívásban kapott paraméterérték értéke olvasható be.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31 használata.  |
|  `subscriptionId`    |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal. |
|  `operationId`       |  A beolvasott művelet egyedi azonosítója. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit.  |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely azonosítja az API-hívást készítő közzétevőt.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint.  |

*Reagálási kódok:*

Code: 200 – a megadott SaaS-művelet részleteinek beolvasása. 

*Válasz hasznos adat például:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Kód: 403 Tiltott. Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD-alkalmazás-AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete. 

A következő kód nem található: 404.  

* A `subscriptionId` nem található előfizetés.
* A művelet `operationId` nem található.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Művelet állapotának frissítése

Ezzel az API-val frissítheti egy függőben lévő művelet állapotát, hogy jelezze a művelet sikerességét vagy hibáját a közzétevő oldalán.

Az ehhez az `operationId` API-híváshoz a **művelet – hely**, a függőben lévő Operations API-hívás beolvasása vagy a `<id>` webhook-hívásban kapott paraméterérték értéke olvasható be.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Javítás `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  2018-08-31 használata.  |
|   `subscriptionId`   |  A megvásárolt SaaS-előfizetés egyedi azonosítója.  Ez az azonosító a kereskedelmi piactér engedélyezési jogkivonatának feloldása után érhető el az API feloldása paranccsal.  |
|   `operationId`      |  A befejezett művelet egyedi azonosítója. |

*Kérelem fejlécei:*

|  Paraméter         | Érték             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Egyedi karakterlánc-érték, amely a kérésnek az ügyféltől való nyomon követésére szolgál, lehetőleg egy GUID-azonosítóval.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|   `x-ms-correlationid` |  Egyedi karakterlánc-érték a művelethez az ügyfélen.  Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét.  Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
|  `authorization`     |  Egyedi hozzáférési jogkivonat, amely az API-hívást készítő közzétevőt azonosítja.  A formátum az, `"Bearer <access_token>"` Amikor a közzétevő lekéri a jogkivonat értékét az [Azure ad-alkalmazáson alapuló jogkivonat beszerzése](./pc-saas-registration.md#get-the-token-with-an-http-post)című részben leírtak szerint. |

*Adattartalom kérése példa:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Reagálási kódok:*

Kód: 200 egy műveletnek a partner oldalán való betöltésére irányuló hívás.  Ez a válasz például megadhatja az ülések vagy a csomagok megváltoztatásának befejezését a közzétevő oldalán.

Kód: 403
- Tilos.  Az engedélyezési jogkivonat nem érhető el, érvénytelen vagy lejárt. Előfordulhat, hogy a kérelem olyan előfizetéshez próbál hozzáférni, amely nem tartozik az aktuális közzétevőhöz.
- Tilos.  Az engedélyezési jogkivonat érvénytelen, lejárt vagy nincs megadva.  A kérelem egy SaaS-előfizetéshez próbál hozzáférni egy olyan ajánlathoz, amelyet a rendszer az engedélyezési jogkivonat létrehozásához használt eltérő Azure AD-alkalmazás-AZONOSÍTÓval tesz közzé.

Ez a hiba gyakran a [SaaS-regisztráció](pc-saas-registration.md) helyes végrehajtásának tünete.

A következő kód nem található: 404.

* A `subscriptionId` nem található előfizetés.
* A művelet `operationId` nem található.

Kód: 409 ütközés.  Egy újabb frissítés például már teljesül.

Kód: 500 belső kiszolgálóhiba.  Próbálja megismételni az API-hívást.  Ha a hiba továbbra is fennáll, forduljon a [Microsoft ügyfélszolgálatához](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Webhook megvalósítása az SaaS szolgáltatásban

Az SaaS-ajánlatok a partner Centerben való létrehozásakor a partner biztosítja a **kapcsolati webhook** URL-címét, amelyet http-végpontként kíván használni.  Ezt a webhookot a Microsoft hívja meg a POST HTTP-hívással, hogy értesítse a közzétevő oldalát a következő eseményekről, amelyek a Microsoft oldalán történnek:

* Ha az SaaS-előfizetés *előfizetett* állapotban van:
    * ChangePlan 
    * ChangeQuantity
    * Felfüggesztés
    * Leiratkozás
* Ha az SaaS-előfizetés *felfüggesztett* állapotban van:
    * Visszaállítás
    * Leiratkozás

A közzétevőnek be kell vezetnie egy webhookot az SaaS-szolgáltatásban, hogy az SaaS-előfizetés állapota összhangban legyen a Microsoft oldalával.  Az SaaS szolgáltatásnak meg kell hívnia a Get Operation API-t, hogy érvényesítse és engedélyezze a webhook-hívást és a hasznos adatokat, mielőtt a webhook-értesítésen alapuló műveleteket hozna.  A közzétevőnek a HTTP 200-et a Microsoftnak kell visszaadnia, amint a webhook-hívást feldolgozták.  Ez az érték megerősíti, hogy a közzétevő sikeresen megkapta a webhook hívását.

>[!Note]
>A webhook URL-címének nonstop kell lennie és futnia, és készen kell állnia arra, hogy a Microsoft időről időre új hívásokat kapjon.  A Microsoft újrapróbálkozási szabályzattal rendelkezik a webhook-híváshoz (500 újrapróbálkozás több mint 8 órát), de ha a közzétevő nem fogadja el a hívást, és nem ad vissza választ, a webhook által beküldött művelet végül sikertelen lesz a Microsoft oldalán.

*Webhook hasznos adatokra vonatkozó példák:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Fejlesztés és tesztelés

A fejlesztési folyamat elindításához javasoljuk, hogy hozzon létre dummy API-válaszokat a közzétevő oldalon.  Ezek a válaszok a jelen cikkben ismertetett válaszok alapján is alapulhatnak.

Ha a közzétevő készen áll a végpontok közötti tesztelésre:

* Egy SaaS-ajánlat közzététele egy korlátozott előzetes verzióban, és megtarthatja az előzetes verziót.
* Állítsa a csomag árát 0-ra, hogy elkerülje a tényleges számlázási költségek kiváltását a tesztelés során.  Egy másik lehetőség, hogy nem nulla értékű árat állít be, és 24 órán belül megszakítja az összes teszt beszerzését.
* Győződjön meg arról, hogy az összes folyamat véget ért, hogy szimulálja a valódi felhasználói forgatókönyvet.
* Ha a partner a teljes vásárlási és számlázási folyamatot szeretné tesztelni, a $0-nál magasabb áron kínáljuk az ajánlatot.  A vásárlás után a számlázás után létrejön egy számla.

A vásárlás folyamata az ajánlat közzétételének helyétől függően Azure Portal vagy Microsoft AppSource helyekről indítható el.

A *terv módosítása*, a *mennyiség módosítása* és a *leiratkozási* műveletek tesztelése a közzétevő oldaláról történik.  A Microsoft-oldalról a *leiratkozás* a Azure Portal és a felügyeleti központban is elindítható (a portálon, ahol a Microsoft AppSource megvásárlások vannak kezelve).  A *mennyiség és a terv módosítása* csak a felügyeleti központban indítható el.

## <a name="get-support"></a>Támogatás kérése

A kiadói támogatási lehetőségekért lásd: [a kereskedelmi piactér program támogatása a partner Centerben](../support.md) .

## <a name="next-steps"></a>Következő lépések

Tekintse meg a kereskedelmi [Piactéri mérési szolgáltatás API-kat](marketplace-metering-service-apis.md) a kereskedelmi piactéren elérhető SaaS-ajánlatok további lehetőségeiért.

Tekintse át és használja az [ügyfeleket különböző programozási nyelvekhez és mintákhoz](https://github.com/microsoft/commercial-marketplace-samples).

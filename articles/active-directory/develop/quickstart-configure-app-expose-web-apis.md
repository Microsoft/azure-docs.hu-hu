---
title: 'Gyors útmutató: webes API regisztrálása és közzététele | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a rövid útmutatóban a webes API-t regisztrálja a Microsoft Identity platformmal, és konfigurálja annak hatóköreit, és kiteszi az ügyfelek számára az API erőforrásaihoz való engedélyek-alapú hozzáférést.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 820bc7dfe9123db495c151cd5cd0ea5ae337619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103974"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Gyors útmutató: alkalmazás konfigurálása webes API-k megjelenítéséhez

Ebben a rövid útmutatóban egy webes API-t regisztrál a Microsoft Identity platformmal, és elérhetővé teheti azt az ügyfélalkalmazások számára egy példa hatókör hozzáadásával. Ha regisztrálja a webes API-t, és a hatókörökön keresztül teszi közzé azt, az API-hoz hozzáférő jogosult felhasználók és ügyfélalkalmazások számára engedélyezheti az engedélyek alapján történő hozzáférést az erőforrásaihoz.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók – [ingyenes fiók létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A rövid útmutató befejezése [: bérlő beállítása](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>A webes API regisztrálása

A webes API erőforrásaihoz való hatókörön belüli hozzáférés biztosításához először regisztrálnia kell az API-t a Microsoft Identity platformon.

1. Hajtsa végre a következő témakörben ismertetett lépéseket: **alkalmazás** regisztrálása szakasz, rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md).
1. Ugorja **át az átirányítási URI hozzáadása** és a **platform beállításainak konfigurálása** szakaszt. Nem kell konfigurálnia egy átirányítási URI-t a webes API-hoz, mivel a felhasználó nem jelentkezett be interaktív módon.
1. A **hitelesítő adatok hozzáadása** szakasz kihagyása most. Csak abban az esetben, ha az API hozzáfér egy alsóbb rétegbeli API-hoz, a saját hitelesítő adataira van szüksége, a jelen cikkben nem szereplő forgatókönyvre.

A webes API-val regisztrálva felveheti azokat a hatóköröket, amelyekkel az API-kód lehetővé teszi, hogy részletes engedélyeket nyújtson az API felhasználói számára.

## <a name="add-a-scope"></a>Hatókör hozzáadása

Az ügyfélalkalmazás kódja engedélyt kér a webes API által meghatározott műveletek elvégzésére azáltal, hogy hozzáférési jogkivonatot továbbít a védett erőforráshoz (a webes API-hoz) tartozó kérésekkel együtt. A webes API ezt követően csak akkor hajtja végre a kért műveletet, ha a kapott hozzáférési jogkivonat tartalmazza a művelethez szükséges hatóköröket.

Először hajtsa végre az alábbi lépéseket egy nevű példa hatókör létrehozásához `Employees.Read.All` :

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, használja a felső menüben található **Directory + előfizetés** szűrőt, :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: és válassza ki az ügyfélalkalmazás regisztrációját tartalmazó bérlőt.
1. Válassza ki **Azure Active Directory**  >  **Alkalmazásregisztrációk**, majd válassza ki az API-alkalmazás regisztrációját.
1. Válassza ki **az API**  >  **hozzáadása hatókört**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Az alkalmazás regisztrációja elérhetővé teszi az API-panelt a Azure Portal":::

1. Ha még nincs konfigurálva, a rendszer kéri, hogy állítson be egy **alkalmazás-azonosító URI** -t.

   Az alkalmazás-azonosító URI az API kódjában hivatkozott hatókörök előtagjaként működik, és globálisan egyedinek kell lennie. Használhatja a megadott alapértelmezett értéket, amely az űrlapon van `api://<application-client-id>` , vagy megadhat egy OLVASHATÓBB URI-t, például: `https://contoso.com/api` .

1. Ezután adja meg a hatókör attribútumait a **hatókör hozzáadása** panelen. Ebben az útmutatóban használhatja a példa értékeket, vagy megadhatja a sajátját.

    | Mező | Leírás | Példa |
    |-------|-------------|---------|
    | **Hatókör neve** | A hatókör neve. Általános hatókör-elnevezési konvenció `resource.operation.constraint` . | `Employees.Read.All` |
    | **Ki adhat hozzájárulást?** | Azt határozza meg, hogy a hatókört engedélyezheti-e a felhasználók, vagy ha rendszergazdai hozzájárulásra van szükség. Magasabb jogosultsági szintű engedélyeknél válassza a **Csak rendszergazdák** lehetőséget. | **Rendszergazdák és felhasználók** |
    | **Rendszergazdai hozzájárulás megjelenítendő neve** | A hatókör azon céljának rövid leírása, amely csak a rendszergazdák számára jelenik meg. | `Read-only access to Employee records` |
    | **Rendszergazdai jóváhagyás leírása** | A hatókör által megadott engedély részletesebb leírása, amelyet csak a rendszergazdák látnak. | `Allow the application to have read-only access to all Employee data.` |
    | **Felhasználói jóváhagyás megjelenítendő neve** | A hatókör céljának rövid leírása. Csak akkor jelenik meg a felhasználók számára, ha beállítja, hogy **ki férhet** hozzá a **rendszergazdákhoz és a felhasználókhoz**. | `Read-only access to your Employee records` |
    | **Felhasználói jóváhagyás leírása** | A hatókör által biztosított engedély részletesebb leírása. Csak akkor jelenik meg a felhasználók számára, ha beállítja, hogy **ki férhet** hozzá a **rendszergazdákhoz és a felhasználókhoz**. | `Allow the application to have read-only access to your Employee data.` |

1. Állítsa az **állapotot** **engedélyezve** értékre, majd válassza a **hatókör hozzáadása** elemet.

1. Választható Ha nem szeretné, hogy az alkalmazás felhasználói beleférjenek az Ön által meghatározott hatókörökbe, az ügyfélalkalmazás *előzetes engedélyezésével* elérheti a webes API-t. Előzetes engedélyezés *csak* a megbízható ügyfélalkalmazások számára, mivel a felhasználók nem fogják tudni visszautasítani a hozzájárulásukat.
    1. Az **engedélyes ügyfélalkalmazások** területen válassza **az ügyfélalkalmazás hozzáadása** elemet.
    1. Adja meg az előre engedélyezni kívánt ügyfélalkalmazás **alkalmazás-(ügyfél-) azonosítóját** . Például egy korábban regisztrált webalkalmazáshoz.
    1. Az **engedéllyel rendelkező hatókörök** területen válassza ki azokat a hatóköröket, amelyekhez meg kívánja szüntetni az engedély megadását, majd válassza az **alkalmazás hozzáadása** lehetőséget.

    Ha követte ezt a választható lépést, az ügyfélalkalmazás mostantól egy előre megadott ügyfélalkalmazás (PEM), és a felhasználóknak nem kell megadniuk a hozzájárulásukat a bejelentkezéshez.

## <a name="add-a-scope-requiring-admin-consent"></a>Rendszergazdai jogosultságot igénylő hatókör hozzáadása

Ezután adjon hozzá egy másik példa nevű hatókört, `Employees.Write.All` amely szerint csak a rendszergazdák vehetnek fel engedélyt. A rendszergazdai jogosultságot igénylő hatóköröket jellemzően a magasabb szintű jogosultságú műveletekhez való hozzáférés biztosítására használják, és gyakran olyan ügyfélalkalmazások, amelyek háttér-szolgáltatásként vagy démonként futnak, amelyek nem tudnak interaktív módon bejelentkezni.

A példában szereplő `Employees.Write.All` hatókör hozzáadásához kövesse a [hatókör hozzáadása](#add-a-scope) szakasz lépéseit, és adja meg ezeket az értékeket a **hatókör hozzáadása** panelen:

| Mező                          | Példaérték                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Hatókör neve**                 | `Employees.Write.All`                                              |
| **Ki adhat hozzájárulást?**            | **Csak rendszergazdák**                                                    |
| **Rendszergazdai hozzájárulás megjelenítendő neve** | `Write access to Employee records`                                 |
| **Rendszergazdai jóváhagyás leírása**  | `Allow the application to have write access to all Employee data.` |
| **Felhasználói jóváhagyás megjelenítendő neve**  | *Nincs (hagyja üresen)*                                               |
| **Felhasználói jóváhagyás leírása**   | *Nincs (hagyja üresen)*                                               |

## <a name="verify-the-exposed-scopes"></a>A feltehetően elérhető hatókörök ellenőrzése

Ha sikeresen felvette az előző szakaszokban ismertetett, példaként megadott hatóköröket, akkor az ehhez a képhez hasonlóan a webes API-alkalmazás regisztrációjának **elérhetővé tétele API** -paneljén fog megjelenni:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Képernyőfelvétel: az API-panel közzététele két elérhető hatókörrel.":::

Ahogy az ábrán is látható, a hatókör teljes karakterlánca a webes API **alkalmazás-azonosító URI-ja** és a hatókör **hatókörének neve**.

Ha például a webes API alkalmazásspecifikus AZONOSÍTÓjának URI-ja, `https://contoso.com/api` és a hatókör neve `Employees.Read.All` , a teljes hatókör a következő:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>A feltehetően elérhető hatókörök használata

Az adatsorozat következő cikkében az ügyfélalkalmazás regisztrációját konfigurálja a webes API-hoz, valamint a jelen cikk lépéseinek követésével meghatározott hatóköröket.

Miután az ügyfélalkalmazás regisztrálva van a webes API-hoz való hozzáféréshez, az ügyfél OAuth 2,0 hozzáférési jogkivonatot adhat ki a Microsoft Identity platform használatával. Amikor az ügyfél meghívja a webes API-t, egy hozzáférési jogkivonatot jelenít meg, amelynek hatóköre ( `scp` ) jogcíme az ügyfél alkalmazás-regisztrációjában megadott engedélyekre van állítva.

A későbbiekben igény szerint további hatóköröket is közzétehet. Vegye figyelembe, hogy a webes API több művelethez társított több hatókört is ki tud tenni. Az erőforrás a (z) `scp` OAuth 2,0 hozzáférési jogkivonatban található hatókör () jogcímek kiértékelésével képes a webes API-hoz való hozzáférés szabályozására.

## <a name="next-steps"></a>Következő lépések

Most, hogy kitette a webes API-t a hatókörök konfigurálásával, konfigurálja az ügyfélalkalmazás regisztrációját, és engedélyezze a hatókörök elérését.

> [!div class="nextstepaction"]
> [Alkalmazás-regisztráció konfigurálása webes API-hozzáféréshez](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application

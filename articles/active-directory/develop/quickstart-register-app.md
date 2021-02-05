---
title: 'Gyors útmutató: alkalmazás regisztrálása a Microsoft Identity platformon | Azure'
description: Ebből a rövid útmutatóból megtudhatja, hogyan regisztrálhat egy alkalmazást a Microsoft Identity platformon.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 430ab980f51ff06ad5e39d6402abf5f649cc6d39
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593095"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Gyors útmutató: alkalmazás regisztrálása a Microsoft Identity platformmal

Ebben a rövid útmutatóban egy alkalmazást regisztrál a Azure Portal, így a Microsoft Identity platform hitelesítési és engedélyezési szolgáltatásokat biztosíthat az alkalmazáshoz és a felhasználóihoz.

A Microsoft Identity platform csak a regisztrált alkalmazások esetében végzi az identitás-és hozzáférés-kezelést (IAM). Függetlenül attól, hogy egy ügyfélalkalmazás, például web vagy Mobile alkalmazás, vagy egy olyan webes API, amely egy ügyfélalkalmazás biztonsági mentését támogatja, regisztrálja az alkalmazás és az identitás-szolgáltató, a Microsoft Identity platform közötti megbízhatósági kapcsolatot.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [bérlői gyors üzembe helyezési](quickstart-create-new-tenant.md) útmutató befejezése.

## <a name="register-an-application"></a>Alkalmazás regisztrálása

Az alkalmazás regisztrálása megbízhatósági kapcsolatot létesít az alkalmazás és a Microsoft Identity platform között. A bizalmi kapcsolat egyirányú: az alkalmazás megbízhatónak tartja a Microsoft Identity platformot, nem pedig fordítva.

Az alkalmazás regisztrációjának létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **címtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet. Később is megváltoztathatja.
1. Itt adhatja meg, hogy ki használhatja ki az alkalmazást, más néven a *bejelentkezési közönségnek*.

    | Támogatott fióktípusok | Description |
    |-------------------------|-------------|
    | **Csak az ebben a szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha olyan alkalmazást hoz létre, amelyet csak felhasználók (vagy vendégek) használhatnak *a* bérlőben.<br><br>Gyakran *üzletági (LOB* ) alkalmazásnak nevezik, ez az alkalmazás egy *egybérlős* alkalmazás a Microsoft Identity platformon. |
    | **Tetszőleges szervezeti címtárban található fiókok** | Válassza ezt a lehetőséget, ha azt szeretné, hogy a felhasználók *bármely* Azure Active Directory (Azure ad-) bérlőben használni tudják az alkalmazást. Ez a beállítás akkor lehet hasznos, ha például olyan szoftveres (SaaS) alkalmazást készít, amelyet több szervezet számára szeretne biztosítani.<br><br>Ezt a fajta alkalmazást több- *bérlős* alkalmazásnak nevezzük a Microsoft Identity platformon. |
    | **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha a lehető legszélesebb ügyfélkört szeretbé megcélozni.<br><br>Ha ezt a lehetőséget választja, regisztrál egy több- *bérlős* alkalmazást, amely támogatja a személyes *Microsoft-fiókkal* rendelkező felhasználók támogatását is. |
    | **Személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha csak személyes Microsoft-fiókkal rendelkező felhasználók számára hoz létre alkalmazást. A személyes Microsoft-fiókok közé tartoznak a Skype, az Xbox, az élő és a Hotmail-fiókok. |

1. Ne adjon meg semmit az **átirányítási URI-hoz (nem kötelező)**. A következő szakaszban egy átirányítási URI-t kell konfigurálnia.
1. A kezdeti alkalmazás regisztrációjának befejezéséhez válassza a **regisztráció** lehetőséget.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Képernyőfelvétel a webböngészőben lévő Azure Portalről, amely az alkalmazás regisztrálása ablaktáblát mutatja.":::

A regisztráció befejeződése után a Azure Portal megjeleníti az alkalmazás regisztrációjának **Áttekintés** paneljét. Megjelenik az **alkalmazás (ügyfél) azonosítója**. Az ügyfél- *azonosítónak* is nevezik, ez az érték egyedileg azonosítja az alkalmazást a Microsoft Identity platformon.

Az alkalmazás kódja vagy az alkalmazásban használt hitelesítési tár általában az ügyfél-azonosítót is használja. Az azonosító az Identity platform által fogadott biztonsági jogkivonatok érvényesítésének részeként használatos.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Képernyőfelvétel a webböngészőben lévő Azure Portalről, amely az alkalmazások regisztrálásának áttekintés paneljét jeleníti meg.":::

## <a name="add-a-redirect-uri"></a>Átirányítási URI hozzáadása

Az *átirányítási URI* az a hely, ahol a Microsoft Identity platform átirányítja a felhasználó ügyfelét, és a hitelesítés után biztonsági jogkivonatokat küld.

Egy éles webalkalmazásban például az átirányítási URI általában egy nyilvános végpont, amelyben az alkalmazás fut, például: `https://contoso.com/auth-response` . A fejlesztés során gyakori, hogy hozzáadja azt a végpontot is, amelyben az alkalmazást helyileg futtatja, például `https://127.0.0.1/auth-response` vagy `http://localhost/auth-response` .

A regisztrált alkalmazások átirányítási URI-k hozzáadásával és módosításával konfigurálhatja a [platform beállításait](#configure-platform-settings).

### <a name="configure-platform-settings"></a>A platform beállításainak konfigurálása

Az egyes alkalmazások, például az átirányítási URI-k beállításai a Azure Portal **platform konfigurációjában** konfigurálhatók. Egyes platformokon, például a **web** -és **egyoldalas alkalmazásokban** manuálisan kell megadnia egy átirányítási URI-t. Más platformokhoz, például a mobil-és asztali eszközökhöz, a más beállítások konfigurálásakor létrehozott átirányítási URI-k közül választhat.

Alkalmazásbeállítások konfigurálása a célzott platform vagy eszköz alapján:

1. A Azure Portal **Alkalmazásregisztrációk** területen válassza ki az alkalmazást.
1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
1. A **platform-konfigurációk** területen válassza **a platform hozzáadása** lehetőséget.
1. A **platformok konfigurálása** területen válassza ki az alkalmazás típusa (platform) csempéjét a beállítások konfigurálásához.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Képernyőkép a Azure Portal platform konfigurációs paneljéről." border="false":::

    | Platform | Konfigurációs beállítások |
    | -------- | ---------------------- |
    | **Web** | Adjon meg egy **átirányítási URI** -t az alkalmazáshoz. Ez az URI az a hely, ahol a Microsoft Identity platform átirányítja a felhasználó ügyfelét, és biztonsági jogkivonatokat küld a hitelesítés után.<br/><br/>Válassza ezt a platformot a kiszolgálón futó szabványos webalkalmazásokhoz. |
    | **Egyoldalas alkalmazás** | Adjon meg egy **átirányítási URI** -t az alkalmazáshoz. Ez az URI az a hely, ahol a Microsoft Identity platform átirányítja a felhasználó ügyfelét, és biztonsági jogkivonatokat küld a hitelesítés után.<br/><br/>Válassza ezt a platformot, ha a JavaScript vagy egy olyan keretrendszer, például a szögletes, a Vue.js, a React.js vagy a Blazer webszerelvény használatával hoz létre ügyféloldali webalkalmazást. |
    | **iOS/macOS** | Adja meg az alkalmazáscsomag **azonosítóját**. Keresse meg a **Build Settings** vagy a Xcode in *info. plist* fájlban.<br/><br/>A rendszer létrehoz egy átirányítási URI-t a **köteg-azonosító** megadásakor. |
    | **Android** | Adja meg az **alkalmazáscsomag nevét**. Keresse meg a *AndroidManifest.xml* fájlban. Az **aláírási kivonatot** is létrehozza és adja meg.<br/><br/>A rendszer létrehoz egy átirányítási URI-t, amikor megadja ezeket a beállításokat. |
    | **Mobil-és asztali alkalmazások** | Válassza ki a **javasolt átirányítási URI**-k egyikét. Vagy **Egyéni átirányítási URI**-t is megadunk.<br/><br/>Asztali alkalmazások esetén ajánlott<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Válassza ki ezt a platformot olyan mobileszközök esetében, amelyek nem a legújabb Microsoft Authentication Library (MSAL) szolgáltatást használják, vagy nem használ közvetítőt. A platformot asztali alkalmazásokhoz is kiválaszthatja. |
1. Válassza a **Konfigurálás** lehetőséget a platform konfigurációjának befejezéséhez.

### <a name="redirect-uri-restrictions"></a>Átirányítási URI-korlátozások

Bizonyos korlátozások vonatkoznak az alkalmazások regisztrálásához hozzáadott átirányítási URI-k formátumára. Ezekről a korlátozásokról az [átirányítási URI (válasz URL-cím) korlátozásai és korlátozásai](reply-url.md)című témakörben olvashat bővebben.

## <a name="add-credentials"></a>Hitelesítő adatok hozzáadása

A hitelesítő adatokat a webes API-hoz hozzáférő [bizalmas ügyfélalkalmazások](msal-client-applications.md) használják. Ilyenek például a [webalkalmazások](scenario-web-app-call-api-overview.md), más [webes API](scenario-protected-web-api-overview.md)-k, [valamint a szolgáltatás típusú és a démon típusú alkalmazások](scenario-daemon-overview.md). A hitelesítő adatok lehetővé teszik az alkalmazás számára, hogy önmagát hitelesítse magát, ami nem igényel interakciót a felhasználótól futásidőben. 

A bizalmas ügyfélalkalmazás regisztrációjának hitelesítő adataiként is hozzáadhat tanúsítványokat és ügyfél-titkot (karakterláncot).

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Képernyőkép a Azure Portalről, amely a tanúsítványok és titkok panelt jeleníti meg az alkalmazás regisztrálásakor.":::

### <a name="add-a-certificate"></a>Tanúsítvány hozzáadása

Más néven *nyilvános kulcs*, a tanúsítvány az ajánlott hitelesítő adat. Nagyobb megbízhatóságot biztosít, mint az ügyfél titkos kulcsa. További információ a tanúsítvány hitelesítési módszerként való használatáról az alkalmazásban: [Microsoft Identity platform alkalmazás-hitelesítési tanúsítvány hitelesítő adatai](active-directory-certificate-credentials.md).

1. A Azure Portal **Alkalmazásregisztrációk** területen válassza ki az alkalmazást.
1. Válassza a **tanúsítványok & titkok**  >  **feltöltési tanúsítvány** elemet.
1. Válassza ki a feltölteni kívánt fájlt. A következő fájltípusok egyikének kell lennie: *. cer*, *. PEM*, *. CRT*.
1. Válassza a **Hozzáadás** elemet.

### <a name="add-a-client-secret"></a>Ügyfél titkos kulcsának hozzáadása

Az ügyfél titkos kulcsát *alkalmazás jelszavának* is nevezik. Ez egy karakterlánc-érték, amelyet az alkalmazás a tanúsítvány helyett saját maga is használhat. Az ügyfél titkos kulcsa a két hitelesítő adat használatának a megkönnyítése. A fejlesztés során gyakran használják, de kevésbé biztonságos, mint a tanúsítvány. Használjon tanúsítványokat az éles környezetben futó alkalmazásaiban. 

További információ az alkalmazás biztonsági javaslatairól: [Microsoft Identity platform – ajánlott eljárások és javaslatok](identity-platform-integration-checklist.md#security).

1. A Azure Portal **Alkalmazásregisztrációk** területen válassza ki az alkalmazást.
1. Válassza a **tanúsítványok & titkos kulcsok**  >   **új ügyfél titka** lehetőséget.
1. Adja meg titkos ügyfélkódja leírását.
1. Válassza ki az időtartamot.
1. Válassza a **Hozzáadás** elemet.
1. *Jegyezze fel a titkos kulcs értékét* az ügyfélalkalmazás kódjában való használathoz. Ez a titkos érték *soha nem jelenik meg többé* , miután elhagyja ezt a lapot.


## <a name="next-steps"></a>Következő lépések

Az ügyfélalkalmazások általában a webes API-k erőforrásaihoz férnek hozzá. Az ügyfélalkalmazás a Microsoft Identity platform használatával biztosítható. A platform használatával is engedélyezheti a hatókörön belüli, engedélyek alapján elérhető hozzáférést a webes API-hoz.

A sorozat következő rövid útmutatójában hozzon létre egy újabb alkalmazást a webes API-hoz, és tegye elérhetővé a hatókörét.

> [!div class="nextstepaction"]
> [Alkalmazás konfigurálása webes API-k megjelenítéséhez](quickstart-configure-app-expose-web-apis.md)

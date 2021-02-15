---
title: 'Gyors útmutató: alkalmazás konfigurálása webes API-hoz való hozzáféréshez | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a rövid útmutatóban egy, a Microsoft Identity platformon webes API-t képviselő alkalmazás-regisztrációt konfigurál, amely lehetővé teszi a hatókörön belüli erőforrás-hozzáférés (engedélyek) alkalmazását az ügyfélalkalmazások számára.
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
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: bd6acc5d3b7cd6e88b56e4a922e777adca031538
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102563"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Gyors útmutató: ügyfélalkalmazás konfigurálása webes API-k eléréséhez

Ebben a rövid útmutatóban egy, a Microsoft Identity platformon regisztrált ügyfélalkalmazás a saját webes API-hoz tartozó hatókörön belüli, engedély-alapú hozzáféréssel rendelkezik. Megadhatja az ügyfélalkalmazások hozzáférését Microsoft Graphhoz is.

Ha megad egy webes API hatókörét az ügyfélalkalmazás regisztrációjában, az ügyfélalkalmazás olyan hozzáférési jogkivonatot szerezhet be, amely tartalmazza ezeket a hatóköröket a Microsoft Identity platformon. A kód keretében a webes API a hozzáférési jogkivonatban található hatókörök alapján jogosultság-alapú hozzáférést biztosíthat az erőforrásaihoz.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók – [ingyenes fiók létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A rövid útmutató befejezése [: alkalmazás regisztrálása](quickstart-register-app.md)
* A rövid útmutató befejezése [: alkalmazás konfigurálása webes API-k elérhetővé tételéhez](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Engedélyek hozzáadása a webes API-hoz való hozzáféréshez

Az első forgatókönyvben egy ügyfélalkalmazás hozzáférést biztosít a saját webes API-hoz, mindkettőt az előfeltételek részeként regisztrálnia kell. Ha még nem rendelkezik egy ügyfélalkalmazás és egy webes API regisztrálva, hajtsa végre a két [előfeltételt](#prerequisites) ismertető cikkben leírt lépéseket.

Ez az ábra azt szemlélteti, hogy a két alkalmazás regisztrációja hogyan kapcsolódik egymáshoz. Ebben a szakaszban az ügyfélalkalmazás regisztrációjának engedélyeit adja hozzá.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Vonal diagram, amely egy webes API-t mutat be a jobb oldalon elérhető hatókörökkel, a bal oldalon pedig egy ügyfélalkalmazás" border="false":::

Miután regisztrálta az ügyfélalkalmazás és a webes API-t, és hatókörök létrehozásával adta meg az API-t, a következő lépések végrehajtásával konfigurálhatja az ügyfél engedélyeit az API-hoz:

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, használja a felső menüben található **Directory + előfizetés** szűrőt, :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: és válassza ki az ügyfélalkalmazás regisztrációját tartalmazó bérlőt.
1. Válassza ki **Azure Active Directory**  >  **Alkalmazásregisztrációk**, majd válassza ki az ügyfélalkalmazás (*nem* a webes API-t).
1. Válassza az **API-engedélyek**  >  **Hozzáadás engedély**  >  **saját API**-k lehetőséget.
1. Válassza ki az előfeltételek részeként regisztrált webes API-t.

    A **delegált engedélyek** alapértelmezés szerint ki vannak választva. A delegált engedélyek olyan ügyfélalkalmazások számára megfelelőek, amelyek a webes API-hoz a bejelentkezett felhasználóként férnek hozzá, és amelyek hozzáférését a következő lépésben kiválasztott engedélyekre kell korlátozni. Hagyja kiválasztva a példához tartozó **delegált engedélyeket** .

    Az **alkalmazás engedélyei** olyan szolgáltatás-vagy démon-típusú alkalmazásokhoz szükségesek, amelyeknek saját maguknak kell elérniük a webes API-t, felhasználói beavatkozás nélkül a bejelentkezéshez vagy az engedélyhez. Ha nem adott meg alkalmazás-szerepköröket a webes API-hoz, ez a beállítás le van tiltva.
1. Az **engedélyek kiválasztása** területen bontsa ki azt az erőforrást, amelynek a hatóköreit a webes API-hoz definiálta, és válassza ki azokat az engedélyeket, amelyeket az ügyfélalkalmazás a bejelentkezett felhasználó nevében kell lennie.

    Ha a példában az előző rövid útmutatóban megadott hatókör-neveket használta, az alkalmazottakat kell látnia. **READ. All** és **Employees. Write. All**.
    Válassza az **alkalmazottak. Read. All** vagy más engedélyt, amelyet az előfeltételek végrehajtásakor hozott létre.
1. A folyamat befejezéséhez válassza az **engedélyek hozzáadása** elemet.

Miután hozzáadta az engedélyeket az API-hoz, a **konfigurált engedélyek** területen látnia kell a kiválasztott engedélyeket. Az alábbi ábrán a példa *alkalmazottak. Read.* az ügyfélalkalmazás regisztrációja során hozzáadott összes delegált engedély.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Az újonnan hozzáadott engedélyt megjelenítő Azure Portal konfigurált engedélyek panelje":::

Azt is megfigyelheti, hogy a *felhasználó. olvasás* engedéllyel rendelkezik a Microsoft Graph API-hoz. Ezt az engedélyt automatikusan felveszi a rendszer, amikor regisztrál egy alkalmazást a Azure Portal.

## <a name="add-permissions-to-access-microsoft-graph"></a>Engedélyek hozzáadása a Microsoft Graph eléréséhez

Ahhoz, hogy a bejelentkezett felhasználó nevében hozzáférjen a saját webes API-hoz, az alkalmazásnak szüksége lehet a felhasználó (vagy más) Microsoft Graph tárolt adatai elérésére vagy módosítására is. Vagy lehet, hogy rendelkezik olyan szolgáltatás-vagy Daemon-alkalmazással, amelynek hozzá kell férnie a Microsoft Graphhoz, felhasználói beavatkozás nélkül végezheti el a műveleteket.

### <a name="delegated-permission-to-microsoft-graph"></a>Delegált engedély Microsoft Graph

Delegált engedély konfigurálása a Microsoft Graphhoz, hogy az ügyfélalkalmazás a bejelentkezett felhasználó nevében hajtson végre műveleteket, például az e-mailek olvasása vagy a profil módosítása. Alapértelmezés szerint az ügyfélalkalmazás felhasználói megkérik, amikor bejelentkeznek az Ön számára konfigurált delegált engedélyekre.

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, használja a felső menüben található **Directory + előfizetés** szűrőt, :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: és válassza ki az ügyfélalkalmazás regisztrációját tartalmazó bérlőt.
1. Válassza ki **Azure Active Directory**  >  **Alkalmazásregisztrációk**, majd válassza ki az ügyfélalkalmazás.
1. Adja meg az **API-engedélyek**  >  **engedélyt**  >  **Microsoft Graph**
1. Válassza a **delegált engedélyek** lehetőséget. Microsoft Graph számos engedélyt tesz elérhetővé a lista tetején leggyakrabban használt módon.
1. Az **engedélyek kiválasztása** területen válassza ki a következő engedélyeket:

    | Engedély       | Description                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Felhasználók e-mail-címének megtekintése                           |
    | `offline_access` | Az Ön számára biztosított adathozzáférés fenntartása |
    | `openid`         | A felhasználók beléptetése                                       |
    | `profile`        | A felhasználók alapszintű profiljának megtekintése                           |
1. A folyamat befejezéséhez válassza az **engedélyek hozzáadása** elemet.

Az engedélyek konfigurálásakor a rendszer a bejelentkezéskor megkéri az alkalmazás felhasználóit, hogy engedélyezze az alkalmazásnak az erőforrás-API elérését az Ön nevében.

Rendszergazdaként az *összes* felhasználó nevében is megadhatja a jóváhagyást, hogy a rendszer ne Kérdezzen rá. A rendszergazdai jogosultságot később, a jelen cikk [API-engedélyek és rendszergazdai beleegyezike](#more-on-api-permissions-and-admin-consent) című szakaszában tárgyaljuk.

### <a name="application-permission-to-microsoft-graph"></a>Alkalmazás-engedély Microsoft Graph

Konfigurálja az alkalmazás engedélyeit egy olyan alkalmazáshoz, amely felhasználói beavatkozás vagy beleegyezettés nélkül kell hitelesítenie magát. Az alkalmazásra vonatkozó engedélyeket általában a háttérben futó szolgáltatások vagy démoni alkalmazások használják, amelyek "fej nélkül", illetve egy másik (alsóbb szintű) API-t használó webes API-k használatával érik el az API-t.

A következő lépésekben engedélyeket adhat Microsoft Graph *fájljaihoz. olvassa el az összes* engedélyt példaként.

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, használja a felső menüben található **Directory + előfizetés** szűrőt, :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: és válassza ki az ügyfélalkalmazás regisztrációját tartalmazó bérlőt.
1. Válassza ki **Azure Active Directory**  >  **Alkalmazásregisztrációk**, majd válassza ki az ügyfélalkalmazás.
1. Válassza az **API-engedélyek** engedély megadása  >    >  **Microsoft Graph** az  >  **alkalmazás engedélyei** lehetőséget.
1. Az Microsoft Graph által elérhetővé tett összes engedély a **Select permissions (engedélyek kiválasztása**) területen látható.
1. Válassza ki az engedélyt vagy az engedélyeket, amelyeket meg szeretne adni az alkalmazás számára. Előfordulhat például, hogy van egy olyan Daemon-alkalmazás, amely megkeresi a szervezeten belüli fájlokat, és egy adott fájltípusra vagy névre figyelmeztet.

    Az **engedélyek kiválasztása** alatt bontsa ki a **fájlok** elemet, majd válassza ki a *fájlokat. olvassa el az összes* engedélyt.
1. Válassza az **engedélyek hozzáadása** lehetőséget.

Bizonyos engedélyek, például a Microsoft Graph *fájljai. Read. All* engedély, rendszergazdai beleegyezés szükséges. Adja meg a rendszergazdai jóváhagyást a rendszergazdai jóváhagyás **megadása** gombra kattintva, amelyet később a [rendszergazdai jóváhagyások gomb](#admin-consent-button) szakasza ismertet.

### <a name="configure-client-credentials"></a>Ügyfél hitelesítő adatainak konfigurálása

Az alkalmazás engedélyeit használó alkalmazások saját hitelesítő adataik használatával hitelesítik magukat, anélkül, hogy felhasználói beavatkozásra lenne szükség. Ahhoz, hogy az alkalmazás (vagy az API) hozzáférhessen a Microsoft Graphhoz, a saját webes API-hoz vagy más API-hoz az alkalmazás engedélyeinek használatával, konfigurálnia kell az ügyfélalkalmazás hitelesítő adatait.

Az alkalmazások hitelesítő adatainak konfigurálásával kapcsolatos további információkért tekintse meg a következőt: a [hitelesítő adatok hozzáadása](quickstart-register-app.md#add-credentials) szakasz, rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>További információ az API-engedélyekről és a rendszergazdai engedélyekről

Az alkalmazás-regisztráció **API-engedélyei** ablaktáblája egy [konfigurált engedélyek](#configured-permissions) táblát tartalmaz, és tartalmazhat [más engedélyeket](#other-permissions-granted) tartalmazó táblát is. A következő szakaszokban szerepelnek a táblák és a rendszergazdai jogosultságok [gomb](#admin-consent-button) is.

### <a name="configured-permissions"></a>Konfigurált engedélyek

Az **API-engedélyek** ablaktábla **konfigurált engedélyek** táblája megjeleníti az alkalmazás által az alapszintű művelethez szükséges engedélyek listáját – a *szükséges erőforrás-hozzáférés* (RRA) listát. A felhasználóknak vagy a rendszergazdáknak hozzá kell járulniuk ezekhez az engedélyekhez az alkalmazás használata előtt. Egyéb, opcionális engedélyek később is megkérhető (dinamikus beleegyező).

Az engedélyek minimális listája, amelyeknek a felhasználóknak meg kell egyezniük az alkalmazáshoz. Több is lehet, de ezek mindig szükségesek lesznek. A biztonság érdekében, és a felhasználók és a rendszergazdák könnyebben érezhetik magukat az alkalmazásban, soha ne Kérdezzen rá semmit.

A táblázatban megjelenő engedélyek hozzáadásával vagy eltávolításával a fent ismertetett lépéseket követve vagy a [megadott egyéb engedélyekkel](#other-permissions-granted) (a következő szakaszban leírt módon) adhat hozzá vagy távolíthat el. Rendszergazdaként megadhatja a rendszergazdai beleegyezést a táblázatban megjelenő API-engedélyek teljes készletéhez, és visszavonhatja az egyes engedélyek beleegyezését.

### <a name="other-permissions-granted"></a>Egyéb megadott engedélyek

Előfordulhat, hogy az **API-engedélyek** ablaktáblán egy olyan táblát is látni fog, amely a (z) **{Your bérlő} számára megadott egyéb engedélyekkel rendelkezik** . A **(z) {Your bérlő} táblázathoz megadott egyéb engedélyek** a bérlő számára biztosított engedélyeket jelenítik meg, amelyek nem lettek explicit módon konfigurálva az Application objektumon. Ezeket az engedélyeket dinamikusan kérték és fogadták el. Ez a szakasz csak akkor jelenik meg, ha legalább egy engedély vonatkozik rá.

Az API-k teljes készletét hozzáadhatja, vagy az egyes engedélyek a **konfigurált engedélyek** táblába jelennek meg. Rendszergazdaként visszavonhatja az API-k vagy az egyes engedélyek rendszergazdai beleegyezését ebben a szakaszban.

### <a name="admin-consent-button"></a>Rendszergazdai beleegyezett gomb

A rendszergazdai **jóváhagyás engedélyezése {a bérlőnek}** gomb lehetővé teszi a rendszergazda számára, hogy rendszergazdai jogosultságot adjon az alkalmazáshoz konfigurált engedélyekhez. A gomb kiválasztásakor megjelenik egy párbeszédpanel, amely azt kéri, hogy erősítse meg a jóváhagyást.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Adja meg a rendszergazdai jóváhagyás gombot a Azure Portal konfigurált engedélyek paneljén":::

A jóváhagyás megadása után a rendszergazdai jóváhagyást igénylő engedélyek a jóváhagyásnak megfelelően jelennek meg:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Konfigurálja az engedélyek táblát Azure Portal a fájlokhoz tartozó rendszergazdai beleegyezést. Read. All engedély":::

A **rendszergazdai jóváhagyás megadása** gomb *le van tiltva* , ha Ön nem rendszergazda, vagy ha nincs beállítva az alkalmazáshoz tartozó engedély. Ha rendelkezik a megadott, de még nem konfigurált engedélyekkel, a rendszergazdai jóváhagyás gomb megkéri, hogy kezelje ezeket az engedélyeket. Hozzáadhatók a konfigurált engedélyekhez, vagy eltávolíthatók.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő rövid útmutatóval, amelyből megtudhatja, hogyan konfigurálhatja, hogy mely fióktípus férhet hozzá az alkalmazáshoz. Előfordulhat például, hogy korlátozni szeretné a hozzáférést csak a szervezetben lévő felhasználókra (egybérlős), vagy más Azure AD-bérlők (több-bérlős) és személyes Microsoft-fiókkal (MSA) rendelkező felhasználók számára.

> [!div class="nextstepaction"]
> [Alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)
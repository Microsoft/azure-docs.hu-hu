---
title: Integráció a Logic Apps szolgáltatással
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan csatlakozhat Logic Apps az Azure Digital Twinshoz egyéni összekötő használatával
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: cbd8c91391cc1e3afe930094f34e5015ea3c3450
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097524"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integrálás a Logic Apps használatával egyéni összekötővel

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) egy felhőalapú szolgáltatás, amely segít a munkafolyamatok automatizálásában az alkalmazások és szolgáltatások között. Az Azure digitális Twins API-khoz való Logic Apps csatlakoztatásával az Azure digitális Twins szolgáltatásban és az azokban lévő adatforgalomban is létrehozhat ilyen automatizált folyamatokat.

Az Azure Digital Twins jelenleg nem rendelkezik hitelesített (előre elkészített) összekötővel a Logic Appshoz. Ehelyett a Logic Apps és az Azure Digital Twins használatának jelenlegi folyamata egy [**egyéni Logic apps-összekötő**](../logic-apps/custom-connector-overview.md)létrehozása, amely egy [Egyéni Azure digitális Twins hencegés](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) használatával lett módosítva, hogy működjön a Logic apps.

> [!NOTE]
> A fent hivatkozott egyéni hencegő mintában a hencegés több verziója található. A legújabb verzió a legutóbbi dátummal rendelkező almappában található, de a mintában szereplő korábbi verziók továbbra is támogatottak.

Ebben a cikkben a [Azure Portal](https://portal.azure.com) használatával **hozzon létre egy egyéni összekötőt** , amely a Logic apps Azure digitális Twins-példánnyal való összekapcsolására használható. Ezután létre fog **hozni egy logikai alkalmazást** , amely ezt a kapcsolatot használja egy példaként, amelyben az időzítő által aktivált események automatikusan frissítik a Twin-et az Azure Digital Twins-példányban. 

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt **hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ** .
Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ezzel a fiókkal. 

A következő elemeket is el kell végeznie az előfeltétel-telepítés részeként. Ennek a szakasznak a további lépései a következő lépésekben találhatók:
- Azure digitális Twins-példány beállítása
- Az alkalmazás regisztrációs ügyfelének titkának beolvasása
- Digitális Twin hozzáadása

### <a name="set-up-azure-digital-twins-instance"></a>Azure digitális Twins-példány beállítása

Ha egy Azure digitális Twins-példányt szeretne összekapcsolással Logic Apps ebben a cikkben, akkor már be kell állítania az **Azure digitális Twins-példányát** . 

Először állítson be egy Azure digitális Twins-példányt és a szükséges hitelesítést ahhoz, hogy működjön vele. Ehhez kövesse az útmutató [*: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md)című témakör útmutatását. Az előnyben részesített felhasználói élménytől függően a telepítési cikk a [Azure Portal](how-to-set-up-instance-portal.md), a [CLI](how-to-set-up-instance-cli.md)vagy az [automatizált Cloud Shell üzembe helyezési parancsfájl-minta](how-to-set-up-instance-scripted.md)számára elérhető. Az utasítások összes verziója olyan lépéseket is tartalmaz, amelyekkel ellenőrizheti, hogy sikeresen elvégezte-e az egyes lépéseket, és készen áll az új példány használatára való áttérésre.

Ebben az oktatóanyagban több értékre lesz szüksége a példány beállításakor. Ha újra össze kell gyűjtenie ezeket az értékeket, az alábbi hivatkozásokra kattintva megkeresheti azokat a [Azure Portalban](https://portal.azure.com).
* Azure digitális Twins-példány **_állomásneve_** ([Keresés a portálon](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD App Registration **_Application (ügyfél-) azonosító_** ([Keresés a portálon](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD-alkalmazás regisztrációs **_könyvtárának (bérlői) azonosítója_** ([Keresés a portálon](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Az alkalmazás regisztrációs ügyfelének titkának beolvasása

Emellett létre kell hoznia egy **_ügyfél-titkot_** az Azure ad-alkalmazás regisztrálásához. Ehhez keresse meg a Azure Portal [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) lapját (ezt a hivatkozást használhatja, vagy keresse meg azt a portálon található keresősáv használatával). Válassza ki a regisztrációt a listából, és nyissa meg a részleteit. 

A *tanúsítványokat és a titkos kulcsokat* a regisztráció menüjéből, majd az *+ új ügyfél titka*elemre kattintva érheti el.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Adja meg a leíráshoz és a lejárathoz szükséges értékeket, majd kattintson a *Hozzáadás gombra*.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Most ellenőrizze, hogy az ügyfél titka látható-e a _tanúsítványok & titkok_ oldalon a _lejárat_ és az _érték_ mezőkkel. Jegyezze fel a későbbi használat _értékét_ (a vágólapra másolhatja a másolás ikont is)

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

### <a name="add-a-digital-twin"></a>Digitális Twin hozzáadása

Ez a cikk a Logic Apps használatával frissíti a Twin-et az Azure Digital Twins-példányban. A folytatáshoz vegyen fel legalább egy Twin-példányt a példányba. 

Az ikreket a [DigitalTwins API](how-to-use-apis-sdks.md)-k, a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)vagy az [Azure Digital Twins CLI](how-to-use-cli.md)használatával veheti fel. Az ikrek a következő módszerekkel történő létrehozásával kapcsolatos részletes útmutatásért lásd [*: útmutató: digitális ikrek kezelése*](how-to-manage-twin.md).

Az Ön által létrehozott példányban szüksége lesz egy Twin **_dupla azonosítóra_** .

## <a name="create-custom-logic-apps-connector"></a>Egyéni Logic Apps-összekötő létrehozása

Ebben a lépésben egy [egyéni Logic apps-összekötőt](../logic-apps/custom-connector-overview.md) fog létrehozni az Azure Digital Twins API-khoz. Ezt követően az Azure Digital Twins összekapcsolható a logikai alkalmazások következő szakaszban való létrehozásakor.

Navigáljon a Azure Portal [Logic apps egyéni összekötő](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) oldalára (ezt a hivatkozást használhatja, vagy megkeresheti a portál keresési sávján). Hit *+ Hozzáadás*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Az alábbi *Logic apps egyéni összekötő létrehozása* oldalon válassza ki az előfizetést és az erőforráscsoportot, valamint az új összekötő nevét és üzembe helyezési helyét. Találatok *áttekintése + létrehozás*. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Ekkor megnyílik a *felülvizsgálat + létrehozás* lap, ahol a *Létrehozás* alul található az erőforrás létrehozásához.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Ekkor megjelenik az összekötő üzembe helyezési lapja. Ha befejezte az üzembe helyezést, *lépjen az erőforrás* megnyitása gombra, és tekintse meg az összekötő részleteit a portálon.

### <a name="configure-connector-for-azure-digital-twins"></a>Az Azure Digital Twins-összekötő konfigurálása

Ezután konfigurálja a létrehozott összekötőt az Azure digitális Twins eléréséhez.

Először töltsön le egy egyéni Azure Digital Twins hencegés, amely úgy lett módosítva, hogy működjön a Logic Apps. Töltse le az **Azure Digital Twins Custom hencegő (Logic apps Connector)** mintát [**ebből a hivatkozásból**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) a *zip letöltése* gomb megnyomásával. Navigáljon a letöltött *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* mappára, és csomagolja ki. 

Az oktatóanyaghoz tartozó egyéni hencegés a _* * Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps **_ mappában található. Ez a mappa a *STABLE* és a *Preview*nevű almappákat tartalmaz, amelyek mindegyike dátum szerint rendezi a hencegés különböző verzióit. A legutóbbi dátummal rendelkező mappa a hencegés legújabb példányát fogja tartalmazni. A kiválasztható verziótól függően a hencegő fájl neve _** digitaltwins.jsa * * _.

> [!NOTE]
> Hacsak nem dolgozik előzetes verziójú szolgáltatással, általánosan ajánlott a hencegés legújabb *stabil* verziójának használata. A hencegés korábbi verziói és előzetes verziói azonban továbbra is támogatottak. 

Ezután nyissa meg az összekötő áttekintés lapját a [Azure Portal](https://portal.azure.com) , és kattintson a *Szerkesztés*elemre.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható." formátumban a színhez.
    - Leírás: adja meg a kívánt értékeket.
    - Séma: HTTPS (alapértelmezett érték)
    - Gazdagép: az Azure Digital Twins-példány *állomásneve* .
    - Alap URL-cím:/(hagyja meg az alapértelmezett értéket)

Ezután nyomja meg az ablak alján található *biztonsági* gombot, és folytassa a következő konfigurációs lépéssel.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

A biztonsági lépésben nyomja meg az alábbi információk *szerkesztését* és konfigurálását:
* **Hitelesítés típusa**: OAuth 2,0
* **OAuth 2,0**:
    - Identitás-szolgáltató: Azure Active Directory
    - Ügyfél-azonosító: az Azure AD-alkalmazás regisztrálásához használt *alkalmazás (ügyfél) azonosítója*
    - Ügyfél titka: az Azure AD-alkalmazás regisztrálásának [*előfeltételei*](#prerequisites) között létrehozott *ügyfél-titkos kulcs*
    - Bejelentkezési URL-cím: https://login.windows.net (alapértelmezett elhagyás)
    - Bérlő azonosítója: az Azure AD-alkalmazás regisztrációjának *címtár-(bérlői) azonosítója*
    - Erőforrás URL-címe: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Hatókör: Directory. AccessAsUser. All
    - Átirányítási URL-cím: (most hagyja meg az alapértelmezett értéket)

Vegye figyelembe, hogy az átirányítási URL-cím mező azt jelzi, hogy az *egyéni összekötő mentésével létrehozza az átirányítási URL-címet*. Ezt most úgy teheti meg, hogy a panel tetején megnyomja a *frissítési összekötőt* az összekötő beállításainak megerősítéséhez.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

<!-- Success message? didn't see one -->

Térjen vissza az átirányítási URL-címhez, és másolja ki a generált értéket. Ezt a következő lépésben fogja használni.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Ez az összekötő létrehozásához szükséges összes információ (nincs szükség a definíciós lépéshez való korábbi biztonság folytatására). A *szerkesztés Logic apps egyéni összekötő* panelt is lezárhatja.

>[!NOTE]
>Vissza kell térnie az összekötő áttekintő oldalára, ahol eredetileg megnyomta a *szerkesztést* *Edit* Nem tölti fel az értékeket az utolsó lépéstől kezdve, így ha egy frissített konfigurációt szeretne menteni bármilyen módosult értékkel, újra meg kell adnia az összes többi értéket is, hogy elkerülje az alapértelmezett beállítások felülírását.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Összekötő engedélyeinek megadása az Azure AD-alkalmazásban

Ezután használja az egyéni összekötő *átirányítási URL-címét* , amelyet az utolsó lépésben másolt, hogy megadja az összekötő engedélyeit az Azure ad-alkalmazás regisztrációjában.

Navigáljon a Azure Portal [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) lapjára, és válassza ki a regisztrációt a listából. 

A regisztráció menü *hitelesítés* területén adjon hozzá egy URI-t.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható."::: 

Adja meg az egyéni összekötő *átirányítási URL-címét* az új mezőbe, és nyomja le a *Save (Mentés* ) ikont.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Ezzel létrehozta az Azure Digital Twins API-khoz hozzáférő egyéni összekötőt. 

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Ezután létre fog hozni egy logikai alkalmazást, amely az új összekötő használatával automatizálja az Azure digitális Twins-frissítéseit.

A [Azure Portalban](https://portal.azure.com)keressen a *Logic apps* kifejezésre a portál keresési sávján. Ha kiválasztja, a *Logic apps (logikai alkalmazások* ) lapra kerül. Új logikai alkalmazás létrehozásához kattintson a *logikai alkalmazás létrehozása* gombra.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Az alábbi *logikai alkalmazás* oldalon adja meg az előfizetését és az erőforráscsoportot. Továbbá válassza ki a logikai alkalmazás nevét, és válassza ki a központi telepítési helyet.

Nyomja meg a _felülvizsgálat + létrehozás_ gombot.

Ekkor a *felülvizsgálat + létrehozás* lapra kerül, ahol áttekintheti az adatokat, és az alján található *create* (létrehozás) gombra kattintva létrehozhatja az erőforrást.

Ekkor megjelenik a logikai alkalmazás üzembe helyezési lapja. Ha befejezte az üzembe helyezést, nyomja le az *erőforrás* megnyitása gombot a *Logic apps Designer*folytatásához, ahol a munkafolyamat logikáját fogja kitölteni.

### <a name="design-workflow"></a>Tervezési munkafolyamat

A *Logic apps Designerben*az *Indítás általános eseményindítóval*területen válassza az _**Ismétlődés**_ lehetőséget.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Az alábbi *Logic apps Designer* lapon módosítsa az **ismétlődési** gyakoriságot a *második*értékre, hogy az eseményt 3 másodpercenként aktiválja. Ez megkönnyíti az eredmények későbbi megtekintését anélkül, hogy sokáig várnia kellene.

Nyomja meg az *+ új lépést*.

Ekkor megnyílik a *művelet kiválasztása* jelölőnégyzet. Váltson az *Egyéni* lapra. Az egyéni összekötőt a felső mezőbe kell látni a korábbiak közül.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

Válassza ki az összekötőben található API-k listájának megjelenítéséhez. A keresősáv használatával vagy a lista görgetésével válassza ki a **DigitalTwins_Add**. (Ez a cikk az API-t használja, de más API-t is kijelölhet egy Logic Apps-kapcsolatok esetében érvényes választási lehetőségként).

Előfordulhat, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-beli hitelesítő adataival az összekötőhöz való csatlakozáshoz. Ha a *szükséges engedélyek* megadását kéri, kövesse az utasításokat, és fogadja el az alkalmazás jóváhagyását.

Az új *DigitalTwinsAdd* mezőbe írja be a mezőket a következőképpen:
* _azonosító_: töltse ki a példányban a digitális kettős *azonosítót* , amelyet a logikai alkalmazás frissítésére szeretne.
* _Twin_: ebben a mezőben adhatja meg azt a törzset, amelyhez a kiválasztott API-kérelem szükséges. A *DigitalTwinsUpdate*esetében ez a törzs JSON-javítási kód formájában szerepel. Ha többet szeretne megtudni egy JSON-javításról a Twin-fájl frissítéséhez, tekintse meg a következő témakört: a [Digital Twins](how-to-manage-twin.md#update-a-digital-twin) című rész, *útmutató: digitális ikrek kezelése*.
* _API-Version_: a legújabb API-verzió. Az aktuális nyilvános előzetes verzióban ez az érték *2020-05-31 – előzetes* verzió

Kattintson a *Mentés gombra* a Logic apps Designerben.

Más műveleteket is kiválaszthat, ha az _+ új lépést_ választja ugyanazon az ablakban.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Azure AD-alkalmazás regisztrációjának portál nézete. Az erőforrás menüben a &quot;tanúsítványok és titkok&quot; elemre mutató kiemelés, valamint az &quot;új ügyfél titka&quot; nevű oldalon egy kiemelés látható.":::

## <a name="query-twin-to-see-the-update"></a>Dupla lekérdezés a frissítés megtekintéséhez

Most, hogy létrejött a logikai alkalmazás, a Logic Apps Designerben definiált Twin Update eseményt három másodpercenként ismétlődik. Ez azt jelenti, hogy három másodperc elteltével le kell tudnia kérdezni a Twin-et, és látnia kell az új, javított értékeit.

Lekérdezheti a Twin metódust a választott módszer (például [Egyéni ügyfélalkalmazás](tutorial-command-line-app.md), az [Azure Digital Twins Explorer minta alkalmazás](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), az [SDK-k és az API](how-to-use-apis-sdks.md)-k, illetve a [CLI](how-to-use-cli.md)) használatával. 

Ha többet szeretne megtudni az Azure Digital Twins-példány lekérdezéséről, olvassa el [*az útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)című témakört.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy olyan logikai alkalmazást hozott létre, amely rendszeresen frissít egy Twin-et az Azure Digital Twins-példányban egy megadott javítással. Kipróbálhatja a többi API-t az egyéni összekötőn, hogy Logic Apps hozzon létre különböző műveletekhez a példányon.

Ha többet szeretne megtudni az elérhető API-műveletekről és a szükséges adatokról, tekintse meg a következő [*témakört: az Azure Digital Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md).
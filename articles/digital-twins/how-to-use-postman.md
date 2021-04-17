---
title: Kérelem küldése a Postman használatával
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan konfigurálhatja és használhatja a Postmant a Azure Digital Twins API-k tesztelésére.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588003"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Kérések küldése a Postman használatával a Azure Digital Twins API-knak

[A Postman](https://www.getpostman.com/) egy REST-teszteszköz, amely fő HTTP-kérési funkciókat biztosít egy asztali és beépülő modulon alapuló grafikus felhasználói felülethez. A használatával HTTP-kéréseket küldhet, és elküldheti azokat a Azure Digital Twins [REST API-kba.](how-to-use-apis-sdks.md)

Ez a cikk azt ismerteti, hogyan konfigurálhatja a [Postman](https://www.getpostman.com/) REST-ügyfelet a Azure Digital Twins API-okkal való kommunikációra a következő lépésekben:

1. Az Azure CLI használatával [**lekért**](#get-bearer-token) egy jogkivonatot, amely API-kérések postmanben való igénylését teszi lehetővé.
1. Állítson be egy [**Postman-gyűjteményt,**](#about-postman-collections) és konfigurálja a Postman REST-ügyfelet úgy, hogy a saját jogkivonatát használja a hitelesítéshez. A gyűjtemény beállításakor az alábbi lehetőségek közül választhat:
    1. [**Importálja**](#import-collection-of-azure-digital-twins-apis) a Azure Digital Twins API-kérelmek előre felépített gyűjteményét.
    1. [**Hozzon**](#create-your-own-collection) létre egy saját gyűjteményt az újból.
1. [**Adjon hozzá kéréseket**](#add-an-individual-request) a konfigurált gyűjteményhez, és küldje el őket a Azure Digital Twins API-knak.

Azure Digital Twins két API-készlete van, amelyeken dolgozhat: **adatsík** és **vezérlősík.** További információ az API-készletek közötti [*különbségről: How-to: Use the Azure Digital Twins API-k and SDKs (Az API-k és az SDK-k használata).*](how-to-use-apis-sdks.md) Ez a cikk mindkét API-készletről tartalmaz információkat.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Postman használatával hozzáférjen a Azure Digital Twins API-khoz, be kell állítania egy Azure Digital Twins-példányt, és le kell töltenie a Postmant. A szakasz további részében ezeket a lépéseket kell végigvezetni.

### <a name="set-up-azure-digital-twins-instance"></a>A Azure Digital Twins beállítása

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Postman letöltése

Ezután töltse le a Postman-ügyfél asztali verzióját. Lépjen a [*www.getpostman.com/apps,*](https://www.getpostman.com/apps) és kövesse az utasításokat az alkalmazás letöltéséhez.

## <a name="get-bearer-token"></a>A bearer token beszerzése

Most, hogy beállította a Postmant és a Azure Digital Twins-példányt, be kell szereznie egy olyan jogkivonatot, amely alapján a Postman-kérések engedélyezhet a Azure Digital Twins API-kon.

A jogkivonat beszerzésének számos módja van. Ez a cikk az [Azure CLI használatával](/cli/azure/install-azure-cli) jelentkezik be az Azure-fiókjába, és így szerez be egy jogkivonatot.

Ha az Azure [CLI](/cli/azure/install-azure-cli)helyileg van telepítve, a következő parancsok futtatásához elindíthat egy parancssort a gépen.
Ellenkező esetben megnyithat egy Azure Cloud Shell [a](https://shell.azure.com) böngészőben, és ott futtathatja a parancsokat.

1. Először a következő parancs futtatásával győződjön meg arról, hogy a megfelelő hitelesítő adatokkal jelentkezett be az Azure-ba:

    ```azurecli-interactive
    az login
    ```

2. Ezután az [az account get-access-token paranccsal](/cli/azure/account#az_account_get_access_token) lekért egy, a Azure Digital Twins hozzáféréssel Azure Digital Twins jogkivonatot. Ebben a parancsban a Azure Digital Twins-szolgáltatásvégpont erőforrás-azonosítóját fogja átadni, hogy olyan hozzáférési jogkivonatot kapjon, amely hozzáfér Azure Digital Twins erőforrásokhoz. 

    A jogkivonathoz szükséges környezet attól függ, hogy melyik API-készletet használja, [](how-to-use-apis-sdks.md#overview-data-plane-apis) ezért használja az alábbi lapokat az adatsík és a vezérlősík [API-k](how-to-use-apis-sdks.md#overview-control-plane-apis) közötti kiválasztásához.

    # <a name="data-plane"></a>[Adatsík](#tab/data-plane)
    
    Az adatsík API-ihoz használt jogkivonat lekért értékéhez használja a következő statikus értéket a jogkivonat-környezetben:  `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Ez a szolgáltatásvégpont erőforrás-Azure Digital Twins azonosítója.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Vezérlősík](#tab/control-plane)
    
    A vezérlősík API-ihoz használható jogkivonat lekért értékként használja a következő értéket a jogkivonat környezetében:  `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Ha a Azure Digital Twins-példányhoz olyan szolgáltatásnévvel vagy felhasználói fiókkal kell hozzáférnie, amely a példánytól eltérő Azure Active Directory-bérlőhöz tartozik, jogkivonatot kell kérnie a Azure Digital Twins-példány "home" bérlőjéhez.  További információ erről a folyamatról: [*How-to: Write app authentication code*](how-to-authenticate-client.md#authenticate-across-tenants)( Alkalmazáshitelesítési kód írása).

3. Másolja ki a értékét az eredményből, és mentse a következő `accessToken` szakaszban való használathoz. Ez a **jogkivonat értéke,** amely a Postmannek lesz megszabadva engedélyezi a kéréseket.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Képernyőkép a konzolról, amely az az account get-access-token parancs eredményét mutatja. Az accessToken mező és annak mintaértéke ki van emelve.":::

>[!TIP]
>Ez a jogkivonat legalább öt percig és legfeljebb 60 percig érvényes. Ha elfogy az aktuális jogkivonathoz megadott idő, az ebben a szakaszban található lépéseket megismételve újat kaphat.

A következő lépésként be kell állítania, hogy a Postman ezzel a jogkivonattal API-kéréseket Azure Digital Twins.

## <a name="about-postman-collections"></a>A Postman-gyűjtemények

A Postmanben a kérések **gyűjteményekbe** (kéréscsoportokba) vannak mentve. Amikor létrehoz egy gyűjteményt a kérések csoportosításaként, számos kérésre egyszerre alkalmazhat általános beállításokat. Ez jelentősen leegyszerűsítheti az engedélyezést, ha egynél több kérést tervez létrehozni a Azure Digital Twins API-khoz, mivel ezeket az adatokat csak egyszer kell konfigurálnia a teljes gyűjteményhez.

A Azure Digital Twins első lépésekhez importálhatja az összes Azure Digital Twins [gyűjteményét.](#import-collection-of-azure-digital-twins-apis) Ezt akkor érdemes megtenni, ha az API-kat vizsgálja, és gyorsan szeretne beállítani egy projektet a példák kérésével.

Másik lehetőségként az nulláról is kezdhet, ha létrehoz egy saját üres gyűjteményt, és egyéni kérésekkel adja meg, amelyek csak a szükséges API-kat hívják meg. [](#create-your-own-collection) 

A következő szakaszok mindkét folyamatot ismertetik. A cikk többi része a helyi Postman-alkalmazásban történik, ezért most nyissa meg a Postman alkalmazást a számítógépén.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Api-k Azure Digital Twins importálása

A Postmanben történő Azure Digital Twins első lépések gyors módja a kérések előre felépített gyűjteményének importálása a Azure Digital Twins API-khoz.

### <a name="download-the-collection-file"></a>A gyűjteményfájl letöltése

Az API-készlet importálásának első lépése egy gyűjtemény letöltése. Válassza ki az alábbi lapot a választott adatsíkhoz vagy vezérlősíkhoz az előre felépített gyűjteménybeállításokhoz.

# <a name="data-plane"></a>[Adatsík](#tab/data-plane)

Jelenleg két Azure Digital Twins adatsík-gyűjtemény közül választhat:
* [**Azure Digital Twins Postman-gyűjtemény:**](https://github.com/microsoft/azure-digital-twins-postman-samples)Ez a gyűjtemény egyszerű első lépésekre kínál lehetőséget a Postmanben Azure Digital Twins adatokhoz. A kérések mintaadatokat tartalmaznak, így minimális szerkesztéssel futtathatja őket. Válassza ezt a gyűjteményt, ha a mintaadatokat tartalmazó kulcs API-kérések kivonatoló készletét szeretné használni.
    - A gyűjtemény megkereséhez keresse meg az adattűnés hivatkozását, és nyissa meg az nevű *fájlt,postman_collection.jsa következőn:*.
* **[Azure Digital Twins Swagger:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)** Ez az adattára tartalmazza a Azure Digital Twins API-készlet teljes Swagger-fájlját, amely gyűjteményként letölthető és importálható a Postmanbe. Ez minden API-kérelem átfogó készletét biztosítja, de mintaadatok helyett üres adattületekkel. Válassza ezt a gyűjteményt, ha minden API-híváshoz hozzá szeretne férni, és minden adatot saját maga szeretne kitölteni.
    - A gyűjtemény megkeresése után keresse meg a repo hivatkozást, és válassza ki a legújabb specifikációs verzió mappáját. Innen nyissa meg a következő *fájlját:digitaltwins.jsa következőn:*.

# <a name="control-plane"></a>[Vezérlősík](#tab/control-plane)

A vezérlősíkhoz jelenleg elérhető gyűjtemény a [**Azure Digital Twins Swagger**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Ez az adattára tartalmazza a Azure Digital Twins API-készlet teljes Swagger-fájlját, amely letölthető és importálható gyűjteményként a Postmanbe. Ez minden API-kérelem átfogó készletét biztosítja.

A gyűjtemény megkeresése után keresse meg a repo hivatkozást, és válassza ki a legújabb specifikációs verzió mappáját. Innen nyissa meg a következő *fájlját:digitaltwins.jsa következőn:*.

---

Így töltheti le a kiválasztott gyűjteményt a gépére, hogy importálni tudja a Postmanbe.
1. A fenti hivatkozásokkal nyissa meg a gyűjteményfájlt a GitHubon a böngészőben.
1. Válassza a **Raw (Nyers)** gombot a fájl nyers szövegének megnyitásához.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Képernyőkép a GitHubon digitaltwins.jsadatsíkról. A Raw (Nyers) gomb körül egy kiemelés látható." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Másolja ki a szöveget az ablakból, és illessze be egy új fájlba a gépen.
1. Mentse a fájlt *.json* kiterjesztéssel (a fájlnév bármilyen lehet, ha nem felejti el, hogy később megtalálja a fájlt).

### <a name="import-the-collection"></a>A gyűjtemény importálása

Ezután importálja a gyűjteményt a Postmanbe.

1. A fő Postman ablakban válassza az **Importálás gombot.**
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Az újonnan megnyitott Postman-ablak képernyőképe. Az &quot;Importálás&quot; gomb ki van emelve." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. A következő Importálás ablakban válassza  a Fájlok feltöltése lehetőséget, és keresse meg a korábban létrehozott gyűjteményfájlt a gépen. Válassza a Megnyitás lehetőséget.
1. Válassza az **Importálás gombot** a megerősítéshez.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="A Postman &quot;Importálás&quot; ablakának képernyőképe, amely a gyűjteményként importálni kívánt fájlt és az Importálás gombot mutatja.":::

Az újonnan importált gyűjtemény most már használhatja a Fő Postman-nézet Gyűjtemények lapját.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="A fő Postman-ablak képernyőképe. Az újonnan importált gyűjtemény ki van emelve a &quot;Gyűjtemények&quot; lapon." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Ezután lépjen tovább a következő szakaszra, és adjon hozzá egy jogkivonatot a gyűjteményhez engedélyezéshez, és csatlakoztassa az Azure Digital Twins-példányhoz.

### <a name="configure-authorization"></a>Engedélyezés konfigurálása

Ezután szerkessze a létrehozott gyűjteményt, hogy konfiguráljon néhány hozzáférési adatot. Jelölje ki a létrehozott gyűjteményt, és válassza a További műveletek megtekintése **ikont** egy menü lekért megtekintéséhez. Válassza a **Szerkesztés** elemet.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="A Postman képernyőképe. Az importált gyűjtemény &quot;További műveletek megtekintése&quot; ikonja ki van emelve, a &quot;Szerkesztés&quot; pedig ki van emelve a lehetőségek között." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Az alábbi lépésekkel adhat hozzá egy jogkivonatot a gyűjteményhez engedélyezésre. Itt használhatja a Get  [bearer token](#get-bearer-token) szakaszban összegyűjtött jogkivonat-értéket, hogy a gyűjteményben található összes API-kérelemhez használva azt használni tudja.

1. A gyűjtemény szerkesztési párbeszédpanelén ellenőrizze, hogy az Engedélyezés **lapon** van-e. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Képernyőkép az importált gyűjtemény Postmanben megjelenő szerkesztési párbeszédpanelről, az &quot;Engedélyezés&quot; lapjának megjelenítésével." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Állítsa a Típust **OAuth 2.0-ra,** illessze be a hozzáférési jogkivonatot a Hozzáférési jogkivonat mezőbe, majd válassza a **Mentés lehetőséget.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Képernyőkép az importált gyűjtemény Postman-szerkesztési párbeszédpaneljről az &quot;Engedélyezés&quot; lapon. A típus az &quot;OAuth 2.0&quot;, és a Hozzáférési jogkivonat mező ki van emelve." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>További konfigurálás

# <a name="data-plane"></a>[Adatsík](#tab/data-plane)

Ha adatsík-gyűjteményt készít, segítsen [a](how-to-use-apis-sdks.md#overview-data-plane-apis) gyűjteménynek könnyedén csatlakozni a  Azure Digital Twins erőforrásokhoz a gyűjtemények által biztosított változók beállításával. Ha egy gyűjteményben számos kéréshez ugyanaz az érték szükséges (például a Azure Digital Twins-példány gazdagépneve), az értéket egy olyan változóban tárolhatja, amely a gyűjtemény minden kérésére vonatkozik. Mindkét letölthető gyűjteményhez Azure Digital Twins előre létrehozott változók is vannak, amelyek a gyűjtemény szintjén állíthatók be.

1. A gyűjtemény szerkesztési párbeszédpanelén maradva lépjen a **Változók lapra.**

1. A példány [*prerequisites*](#prerequisites) (Előfeltételek) szakaszból származó állomásnevével állítsa be a megfelelő változó CURRENT VALUE (AKTUÁLIS ÉRTÉK) mezőjét.  Kattintson a **Mentés** gombra.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Képernyőkép az importált gyűjtemény Szerkesztés párbeszédpanelről a Postmanben, a &quot;Változók&quot; lapjának megjelenítésével. A &quot;CURRENT VALUE&quot; mező ki van emelve." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Ha a gyűjteményben további változók is vannak, töltse ki és mentse ezeket az értékeket is.

Ha végzett a fenti lépésekkel, befejezte a gyűjtemény konfigurálását. Ha szeretné, bezárhatja a gyűjtemény szerkesztési lapját.

# <a name="control-plane"></a>[Vezérlősík](#tab/control-plane)

Ha vezérlősík-gyűjteményt készít, mindent megtett, amire szüksége volt a gyűjtemény konfiguráláshoz. [](how-to-use-apis-sdks.md#overview-control-plane-apis) Ha szeretné, bezárhatja a gyűjtemény szerkesztési lapját, és továbbléphet a következő szakaszra.

--- 

### <a name="explore-requests"></a>Kérelmek feltárása

Ezután vizsgálja meg a kéréseket a Azure Digital Twins API-gyűjteményben. A gyűjtemény kibontható az előre létrehozott (műveletkategória szerint rendezve) kérelmek megtekintéséhez. 

A különböző kérések eltérő információkat igényelnek a példányról és az adatairól. Az adott kérések igényléséhez szükséges összes információért tekintse meg a kérelem részleteit az Azure Digital Twins REST API [dokumentációjában.](/rest/api/azure-digitaltwins/)

A Postman-gyűjteményben a kérések részleteit az alábbi lépésekkel szerkesztheti:

1. A listából kiválasztva lekérte a szerkeszthető adatait. 

1. Töltse ki a Path Variables (Elérésiút-változók) alatti **Params (Paraméterek)** lapon felsorolt **változók értékeit.**

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="A Postman képernyőképe. A gyűjtemény kibontva egy kérést fog mutatni. A &quot;Path Variables&quot; (Elérésiút-változók) szakasz ki van emelve a kérelem részletei között." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Adja meg a **szükséges fejléceket** vagy **törzsadatokat** a megfelelő lapokon.

Ha minden szükséges adatot megadott, a Küldés gombbal futtathatja a **kérést.**

Saját kéréseket is hozzáadhat a gyűjteményhez az alábbi Egyéni kérés [*hozzáadása szakaszban*](#add-an-individual-request) ismertetett folyamattal.

## <a name="create-your-own-collection"></a>Saját gyűjtemény létrehozása

Ahelyett, hogy importálja az összes meglévő Azure Digital Twins API-t, létrehozhatja a saját gyűjteményét is az új api-kból. Ezután a referenciadokumentációban található Azure Digital Twins REST API feltöltheti [egyéni kérésekkel.](/rest/api/azure-digitaltwins/)

### <a name="create-a-postman-collection"></a>Postman-gyűjtemény létrehozása

1. Gyűjtemény létrehozásához válassza az **Új gombot** a fő postman ablakban.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="A fő Postman-ablak képernyőképe. Az &quot;Új&quot; gomb ki van emelve." lightbox="media/how-to-use-postman/postman-new.png":::

    Válasszon egy **gyűjteménytípust.**

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="A Postman &quot;Új létrehozása&quot; párbeszédpanelének képernyőképe. A &quot;Gyűjtemény&quot; lehetőség ki van emelve.":::

1. Ez megnyit egy lapot az új gyűjtemény részleteinek kitöltéséhez. Válassza a gyűjtemény alapértelmezett neve **(Új** gyűjtemény) melletti Szerkesztés ikont, hogy lecseréljük a saját választott névre. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Képernyőkép az új gyűjtemény Szerkesztés párbeszédpanelről a Postmanben. Az &quot;Új gyűjtemény&quot; név melletti Szerkesztés ikon ki van emelve." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Ezután folytassa a következő szakaszsal, és adjon hozzá egy jogkivonatot a gyűjteményhez engedélyezésre.

### <a name="configure-authorization"></a>Engedélyezés konfigurálása

Az alábbi lépésekkel adhat hozzá egy jogkivonatot a gyűjteményhez engedélyezésre. Itt használhatja a begyűjtési jogkivonat [](#get-bearer-token) lekérése szakaszban összegyűjtött jogkivonatértéket, hogy a gyűjteményben található összes API-kéréshez használva azt használni tudja. 

1. Az új gyűjtemény szerkesztési párbeszédpanelén maradva lépjen az Engedélyezés **lapra.**

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Képernyőkép az új gyűjtemény Szerkesztés párbeszédpanelről a Postmanben, az &quot;Engedélyezés&quot; lapjának megjelenítésével." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Állítsa a Típust **OAuth 2.0-ra,** illessze be a hozzáférési jogkivonatot a Hozzáférési jogkivonat mezőbe, majd válassza a **Mentés lehetőséget.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Képernyőkép az új gyűjtemény Postman-szerkesztési párbeszédpaneljről az &quot;Engedélyezés&quot; lapon. A típus az &quot;OAuth 2.0&quot;, és a Hozzáférési jogkivonat mező ki van emelve." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Ha végzett a fenti lépésekkel, befejezte a gyűjtemény konfigurálását. Ha szeretné, bezárhatja az új gyűjtemény Szerkesztés lapját.

Az új gyűjtemény a Fő Postman-nézet Gyűjtemények lapján látható.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="A fő Postman-ablak képernyőképe. Az újonnan létrehozott egyéni gyűjtemény ki van emelve a &quot;Gyűjtemények&quot; lapon." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Egyéni kérés hozzáadása

Most, hogy beállította a gyűjteményt, hozzáadhatja a saját kéréseket az Azure Digital Twin API-khoz.

1. Kérés létrehozásához használja ismét az **Új** gombot.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="A fő Postman-ablak képernyőképe. Az &quot;Új&quot; gomb ki van emelve." lightbox="media/how-to-use-postman/postman-new.png":::

    Válassza ki a **kérelem típusát.**

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="A Postman &quot;Új létrehozása&quot; párbeszédpanelének képernyőképe. A &quot;Request&quot; (Kérés) lehetőség ki van emelve.":::

1. Ez a művelet megnyitja a SAVE REQUEST (KÉRELEM MENTÉSE) ablakot, ahol megadhatja a kérelem nevét, leírást adhat meg hozzá, és kiválaszthatja azt a gyűjteményt, amelyben a kérelem található. Adja meg az adatokat, és mentse a kérést a korábban létrehozott gyűjteménybe.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="A Postman &quot;Kérés mentése&quot; ablakának képernyőképe a leírt mezőkkel. A &quot;Mentés Azure Digital Twins gyűjteménybe&quot; gomb ki van emelve.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Most már megtekintheti a kérelmet a gyűjtemény alatt, és kiválaszthatja a szerkeszthető adatok lekéréséhez.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="A Postman képernyőképe. A Azure Digital Twins ki van bontva a kérelem részleteinek megjelenítéseként." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Kérés részleteinek beállítása

Ahhoz, hogy Postman-kérést Azure Digital Twins API-k egyikére, szüksége lesz az API URL-címére és a szükséges részletekre. Ezt az információt a referenciadokumentációban [Azure Digital Twins REST API találhatja meg.](/rest/api/azure-digitaltwins/)

A példalekérdezéshez ez a cikk a Query [](/rest/api/digital-twins/dataplane/query/querytwins)API -t (és annak referenciadokumentációját) használja egy példány összes digitális ikereszközének lekérdezéséhez.

1. Kérje le a kérelem URL-címét, és írja be a következőt a referenciadokumentációból: . A Query API esetén ez jelenleg *POST. `https://digitaltwins-hostname/query?api-version=2020-10-31`*
1. A Postmanben állítsa be a kérelem típusát, és adja meg a kérés URL-címét, szükség szerint töltse ki a helyőrzőket az URL-címben. Itt használhatja a példánya **gazdagépnevét** az [*Előfeltételek szakaszban.*](#prerequisites)
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Képernyőkép az új kérelem részleteiről a Postmanben. A referenciadokumentáció lekérdezési URL-címe ki van töltve a kérelem URL-címe mezőben." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Ellenőrizze, hogy a kérelemhez a **Paraméterek** lapon megjelenő paraméterek megegyeznek-e a referenciadokumentációban leírt paraméterekkel. A Postmanben a kérelemhez tartozó paraméter automatikusan ki lett töltve, amikor az előző lépésben meg lett adva a `api-version` kérés URL-címe. A Query API esetén ez az egyetlen kötelező paraméter, így ez a lépés kész.
1. Az Engedélyezés **lapon** állítsa a Típust A hitelesítés **öröklése szülőtől beállításra.** Ez azt jelzi, hogy ez a kérés a korábban a teljes gyűjteményhez beállított engedélyezést fogja használni.
1. Ellenőrizze, hogy a kérés fejlécei  a Fejlécek lapon megegyeznek-e a referenciadokumentációban leírt fejlécekkel. Ehhez a kéréshez több fejléc is automatikusan ki van töltve. A Query API-hoz nincs szükség fejlécbeállításokra, ezért ez a lépés kész.
1. Ellenőrizze, hogy a Kérelem törzse lapon látható törzs megfelel-e a referenciadokumentációban ismertetett igényeknek.  A Query API-hoz egy JSON-törzsre van szükség a lekérdezés szövegének megszabadozásához. Példa a kérés törzsére, amely a példány összes digitális ikereszközét lekérdezi:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Képernyőkép az új kérelem részleteiről a Postman Törzs lapján. Egy nyers JSON-törzset tartalmaz, amely a &quot;SELECT * FROM DIGITALTWINS&quot; lekérdezést tartalmazza." lightbox="media/how-to-use-postman/postman-request-body.png":::

   További információ a lekérdezések Azure Digital Twins: [*How-to: Query the twin graph*](how-to-query-graph.md)(Az ikergráf lekérdezése).

1. Ellenőrizze a referenciadokumentációban, hogy van-e olyan mező, amely a kérelem típusához szükséges lehet. A Query API összes követelménye teljesült a Postman-kérelemben, így ez a lépés kész.
1. A kész **kérés elküldését** a Küldés gombbal küldheti el.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Képernyőkép az új kérelem részleteiről a Postmanben. A Küldés gomb ki van emelve." lightbox="media/how-to-use-postman/postman-request-send.png":::

A kérés elküldése után a válasz részletei a postman ablakban jelennek meg a kérés alatt. Megtekintheti a válasz állapotkódját és a törzs szövegét.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Képernyőkép az elküldött kérésről a Postmanben. A kérelem részletei alatt a válasz látható. Az állapot 200 OK, a törzs pedig a lekérdezés eredményeit jeleníti meg." lightbox="media/how-to-use-postman/postman-request-response.png":::

Összehasonlíthatja a választ a referenciadokumentációban megadott várt válaszadatokkal, így ellenőrizheti az eredményt, vagy többet megtudhat az esetlegesen felmerülő hibákról.

## <a name="next-steps"></a>Következő lépések

További információ a Digital Twins [*API-król: Útmutató: Az Azure Digital Twins API-k*](how-to-use-apis-sdks.md)és az SDK-k használata, vagy tekintse meg a REST API-k referenciadokumentációját. [](/rest/api/azure-digitaltwins/)
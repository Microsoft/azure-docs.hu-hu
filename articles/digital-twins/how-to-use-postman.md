---
title: Kérelem küldése a Postman használatával
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan konfigurálhatja és használhatja a Poster-t az Azure digitális Twins API-k teszteléséhez.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783812"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>A Poster használata a kérelmek Azure digitális Twins API-khoz való küldéséhez

A [Poster](https://www.getpostman.com/) egy REST-alapú tesztelési eszköz, amely kulcsfontosságú HTTP-kérelmek funkcióit biztosítja egy asztali és beépülő Modulos grafikus felhasználói felületen. Használhatja a HTTP-kérelmeket, és beküldheti azokat az [Azure Digital Twins REST API](how-to-use-apis-sdks.md)-khoz.

Ez a cikk azt ismerteti, hogyan konfigurálhatja a [Poster Rest-ügyfelet](https://www.getpostman.com/) az Azure digitális Twins API-kkal való interakcióhoz az alábbi lépések végrehajtásával:

1. Az Azure CLI használatával [**beszerezhet egy tulajdonosi jogkivonatot**](#get-bearer-token) , amelyet a Poster API-kérések készítéséhez fog használni.
1. Állítson be egy [**Poster-gyűjteményt**](#about-postman-collections) , és konfigurálja a POSTer Rest-ügyfelet, hogy a tulajdonosi jogkivonatot használja a hitelesítéshez. A gyűjtemény beállításakor a következők közül választhat:
    1. Azure digitális Twins API-kérelmek előre összeállított gyűjteményének [**importálása**](#import-collection-of-azure-digital-twins-apis) .
    1. [**Hozzon létre**](#create-your-own-collection) saját gyűjteményt a semmiből.
1. [**Adja hozzá a kérelmeket**](#add-an-individual-request) a konfigurált gyűjteményhez, és küldje el azokat az Azure digitális Twins API-khoz.

Az Azure Digital Twins két API-készlettel rendelkezik, amelyekkel a következőket végezheti el: **adatsík** és **vezérlési sík**. További információ az API-készletek közötti különbségről [*: útmutató: az Azure Digital Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md). Ez a cikk mindkét API-készletre vonatkozó információt tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Poster használatával hozzáférhessen az Azure digitális Twins API-khoz, be kell állítania egy Azure digitális Twins-példányt, és le kell töltenie a Poster-t. A szakasz további részében végigvezeti a lépéseket.

### <a name="set-up-azure-digital-twins-instance"></a>Azure digitális Twins-példány beállítása

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Poster letöltése

Ezután töltse le a Poster-ügyfél asztali verzióját. Navigáljon a [*www.getpostman.com/apps*](https://www.getpostman.com/apps) , és kövesse az utasításokat az alkalmazás letöltéséhez.

## <a name="get-bearer-token"></a>Tulajdonosi jogkivonat beolvasása

Most, hogy beállította a Poster és az Azure Digital Twins-példányát, be kell szereznie egy tulajdonosi jogkivonatot, amelyet a Poster-kérések az Azure Digital Twins API-k engedélyezésére használhatnak.

A token beszerzésének számos lehetséges módja van. Ez a cikk az [Azure CLI](/cli/azure/install-azure-cli) használatával jelentkezik be az Azure-fiókjába, és beszerezhet egy jogkivonatot.

Ha [helyileg telepítette](/cli/azure/install-azure-cli)az Azure CLI-t, a következő parancsok futtatásához indítson el egy parancssort a gépen.
Ellenkező esetben megnyithat egy [Azure Cloud Shell](https://shell.azure.com) ablakot a böngészőben, és futtathatja a parancsokat.

1. Először is győződjön meg arról, hogy a megfelelő hitelesítő adatokkal jelentkezett be az Azure-ba a következő parancs futtatásával:

    ```azurecli-interactive
    az login
    ```

2. Ezután az az [Account Get-Access-Token](/cli/azure/account#az_account_get_access_token) paranccsal szerezzen be egy tulajdonosi jogkivonatot, amely hozzáfér az Azure Digital Twins szolgáltatáshoz. Ebben a parancsban átadja az Azure Digital Twins szolgáltatás végpontjának erőforrás-AZONOSÍTÓját, hogy olyan hozzáférési jogkivonatot kapjon, amely hozzáfér az Azure digitális Twins-erőforrásaihoz. 

    A jogkivonat kötelező környezete attól függ, hogy melyik API-kat használja, ezért az alábbi lapok használatával válassza ki az [adatsík](how-to-use-apis-sdks.md#overview-data-plane-apis) és a [vezérlési sík](how-to-use-apis-sdks.md#overview-control-plane-apis) API-k közötti választást.

    # <a name="data-plane"></a>[Adatsík](#tab/data-plane)
    
    Az **adatközpont** API-kkal használni kívánt jogkivonat lekéréséhez használja a következő statikus értéket a jogkivonat-környezethez: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Ez az Azure Digital Twins szolgáltatás végpontjának erőforrás-azonosítója.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Vezérlősík](#tab/control-plane)
    
    A **vezérlő síkja** API-kkal használni kívánt jogkivonat lekéréséhez használja a következő értéket a jogkivonat-környezethez: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. Másolja a értéket az `accessToken` eredménybe, és mentse a következő szakaszban való használatra. Ezt a **jogkivonat-értéket** kell megadnia a Poster számára a kérések engedélyezéséhez.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Képernyőkép a konzolról, amely az az Account Get-Access-Token parancs eredményét jeleníti meg. A accessToken mező és a hozzá tartozó minta értéke kiemelve.":::

>[!TIP]
>Ez a jogkivonat legalább öt percig érvényes, és legfeljebb 60 percet vesz igénybe. Ha elfogyott az aktuális jogkivonat időkorlátja, megismételheti a szakasz lépéseit, hogy újat kapjon.

Ezután állítsa be a Poster ezt a tokent, hogy API-kéréseket hozzon létre az Azure Digital Twins számára.

## <a name="about-postman-collections"></a>A Poster-gyűjtemények ismertetése

A Poster-kérelmek **gyűjteményekben** (kérelmek csoportjai) lesznek mentve. Amikor létrehoz egy gyűjteményt a kérelmek csoportosításához, egyszerre több kérelemre is alkalmazhat közös beállításokat. Ez nagy mértékben egyszerűsítheti az engedélyezést, ha egynél több kérést szeretne létrehozni az Azure digitális Twins API-kkal, mivel csak egyszer kell konfigurálnia ezeket a részleteket a teljes gyűjteményhez.

Az Azure Digital Twins használatakor [Az Azure digitális Twins-kérelmek előre összeállított gyűjteményének](#import-collection-of-azure-digital-twins-apis)importálásával kezdheti meg a munkát. Ezt akkor teheti meg, ha az API-kat vizsgálja, és szeretne gyorsan beállítani egy projektet a kérelmekre vonatkozó példákkal.

Másik lehetőségként dönthet úgy is, hogy [a saját üres gyűjtemény létrehozásával](#create-your-own-collection) és a csak a szükséges API-k meghívásával feltölti a teljesen új adatokat. 

A következő szakaszok ezeket a folyamatokat ismertetik. A cikk további részében a helyi Poster-alkalmazásban kerül sor, így most megnyithatja a Poster alkalmazást a számítógépen.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Azure digitális Twins API-k gyűjteményének importálása

A Poster Azure Digital Twins használatának első lépéseiben az Azure Digital Twins API-kra vonatkozó kérelmek előre összeállított gyűjteményét kell importálnia.

### <a name="download-the-collection-file"></a>A gyűjtemény fájljának letöltése

Az API-készlet importálásának első lépése egy gyűjtemény letöltése. Válassza ki az alábbi fület az Ön által választott adatsík vagy vezérlési sík számára az előre elkészített gyűjtemény beállításainak megtekintéséhez.

# <a name="data-plane"></a>[Adatsík](#tab/data-plane)

Jelenleg két Azure Digital Twins adatsíkok-gyűjtemény érhető el, amelyek közül választhat:
* [**Azure Digital Twins Poster-gyűjtemény**](https://github.com/microsoft/azure-digital-twins-postman-samples): Ez a gyűjtemény egy egyszerű, első lépésekből álló élményt nyújt az Azure Digital Twins számára a Poster-ben. A kérelmek közé tartoznak a mintaadatok, így a minimálisan szükséges módosításokkal futtathatja őket. Válassza ezt a gyűjteményt, ha a legfontosabb API-kérések emészthető készletét szeretné használni.
    - A gyűjtemény megkereséséhez navigáljon a tárház hivatkozására, és nyissa meg az *postman_collection.js* nevű fájlt.
* **[Azure Digital Twins adatsík hencegés](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: Ez a tárház tartalmazza az Azure digitális Twins API-készletének teljes hencegő fájlját, amely gyűjteményként tölthető le és importálható. Ez minden API-kérelem átfogó készletét biztosítja, de a mintaadatok helyett üres adattörzseket is tartalmaz. Válassza ezt a gyűjteményt, ha szeretné, hogy minden API-híváshoz hozzáférhessen, és töltse ki az összes adatot.
    - A gyűjtemény megkereséséhez navigáljon a tárház hivatkozására, és válassza ki a legújabb specifikációs verziót tartalmazó mappát. Innen nyissa meg adigitaltwins.jsnevű fájlt *a* következő helyen:.

# <a name="control-plane"></a>[Vezérlősík](#tab/control-plane)

A jelenleg a vezérlési síkon elérhető gyűjtemény az [**Azure digitális Twins vezérlési sík hencegés**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Ez a tárház tartalmazza az Azure Digital Twins API-készlet teljes hencegő fájlját, amely letölthető és importálható a Poster gyűjteményként. Ez minden API-kérelem átfogó készletét biztosítja.

A gyűjtemény megkereséséhez navigáljon a tárház hivatkozására, és válassza ki a legújabb specifikációs verziót tartalmazó mappát. Innen nyissa meg adigitaltwins.jsnevű fájlt *a* következő helyen:.

---

A következőképpen töltheti le a kiválasztott gyűjteményt a gépre, hogy a Poster-be importálható legyen.
1. A fenti hivatkozások segítségével nyissa meg a gyűjtemény fájlját a GitHubban a böngészőben.
1. Kattintson a **RAW** gombra a fájl nyers szövegének megnyitásához.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Képernyőkép az adatsíkon digitaltwins.jsa GitHubon található fájlon. A RAW gomb körül van egy kiemelés." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Másolja a szöveget az ablakból, és illessze be egy új fájlba a számítógépén.
1. Mentse a fájlt *. JSON* kiterjesztéssel (a fájl nevét tetszés szerint megadhatja, ha később szeretné megkeresni a fájlt).

### <a name="import-the-collection"></a>A gyűjtemény importálása

Ezután importálja a gyűjteményt Poster-ba.

1. A fő posta ablakban válassza az **Importálás** gombot.
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Képernyőkép az újonnan megnyitott Poster-ablakról. Az &quot;import&quot; gomb ki van emelve." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Az alábbi importálási ablakban válassza a **fájlok feltöltése** lehetőséget, és navigáljon a korábban létrehozott számítógépen található gyűjteményi fájlhoz. Válassza a Megnyitás lehetőséget.
1. A megerősítéshez kattintson az **Importálás** gombra.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Képernyőkép a Poster &quot;import&quot; (Importálás) ablakáról, amely megjeleníti az importálandó fájlt gyűjteményként és az Importálás gombra kattintva.":::

Az újonnan importált gyűjtemény most már megtekinthető a fő postás nézetből, a gyűjtemények lapon.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Képernyőfelvétel a főposta ablakáról. Az újonnan importált gyűjtemény ki van emelve a gyűjtemények lapon." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Ezután folytassa a következő szakasszal, és adjon hozzá egy tulajdonosi jogkivonatot a gyűjteményhez az engedélyezéshez, és kapcsolja össze az Azure Digital Twins-példánnyal.

### <a name="configure-authorization"></a>Engedélyezés konfigurálása

Ezután szerkessze a létrehozott gyűjteményt, és konfigurálja a hozzáférési adatokat. Jelölje ki a létrehozott gyűjteményt, és válassza a **További műveletek megtekintése** ikont a menü lekéréséhez. Válassza a **Szerkesztés** elemet.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Képernyőkép a Poster-ról. A &quot;További műveletek megtekintése&quot; ikon ki van emelve az importált gyűjteményhez, és a &quot;szerkesztés&quot; ki van emelve a beállítások között." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

A következő lépésekkel adhat hozzá egy tulajdonosi jogkivonatot a gyűjteményhez az engedélyezéshez. Itt fogja használni a " [tulajdonosi jogkivonat beolvasása](#get-bearer-token) " szakaszban összegyűjtött **jogkivonat-értéket** a gyűjteményben lévő összes API-kérelemhez.

1. A gyűjtemény szerkesztés párbeszédpaneljén ellenőrizze, hogy az **Engedélyezés** lapon van-e. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Képernyőkép az importált gyűjtemény szerkesztési párbeszédpaneléről az &quot;engedélyezés&quot; lapon." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Állítsa a típust **OAuth 2,0** értékre, illessze be a hozzáférési tokent a hozzáférési token mezőbe, és válassza a **Mentés** lehetőséget.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Képernyőkép az importált gyűjteményhez tartozó Poster Edit (Engedélyezés) lapon. A típus: &quot;OAuth 2,0&quot;, a hozzáférési jogkivonat mező pedig ki van emelve." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>További konfigurálás

# <a name="data-plane"></a>[Adatsík](#tab/data-plane)

Ha [adatsíkok](how-to-use-apis-sdks.md#overview-data-plane-apis) -gyűjteményt készít, a gyűjtemények számára elérhető **változók** megadásával segít a gyűjteménynek az Azure digitális Twins-erőforrásaihoz való könnyű kapcsolódásban. Ha egy gyűjteményben számos kérelemhez ugyanazt az értéket kell megadni (például az Azure Digital Twins-példány állomásneve), akkor az értéket egy olyan változóban tárolhatja, amely a gyűjtemény összes kérelmére érvényes. Az Azure Digital Twins-hoz készült letölthető gyűjtemények mind a gyűjtemény szintjén megadható előre létrehozott változókkal rendelkeznek.

1. Továbbra is a gyűjtemény szerkesztési párbeszédpanelén lépjen a **változók** lapra.

1. A példány **állomásneve** alapján az [*Előfeltételek*](#prerequisites) szakaszban állíthatja be a megfelelő változó aktuális érték mezőjét. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Képernyőkép az importált gyűjtemény szerkesztési párbeszédpaneléről a Poster-ban, amely a &quot;változók&quot; fület mutatja. Az &quot;aktuális érték&quot; mező ki van emelve." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Ha a gyűjtemény további változókkal rendelkezik, töltse ki és mentse ezeket az értékeket is.

Ha végzett a fenti lépésekkel, befejezte a gyűjtemény konfigurálását. Ha szeretné, a gyűjtemény szerkesztési lapját is lezárhatja.

# <a name="control-plane"></a>[Vezérlősík](#tab/control-plane)

Ha a [vezérlési síkon](how-to-use-apis-sdks.md#overview-control-plane-apis) végzett gyűjteményt készít, akkor mindent megtett, amire konfigurálnia kell a gyűjteményt. Ha szeretné, lezárhatja a gyűjtemény szerkesztési lapját, és folytassa a következő szakasszal.

--- 

### <a name="explore-requests"></a>A kérelmek megismerése

Ezután vizsgálja meg az Azure Digital Twins API-gyűjteményben található kérelmeket. A gyűjtemény kibontásával megtekintheti az előre létrehozott kérelmeket (a művelet kategóriája szerint rendezve). 

A különböző kérelmek különböző adatokat igényelnek a példányról és az adatairól. Ha meg szeretné tekinteni az adott kérelem elvégzéséhez szükséges összes információt, tekintse meg a kérelem részleteit az [Azure digitális Twins REST API dokumentációjában](/rest/api/azure-digitaltwins/).

Az alábbi lépéseket követve szerkesztheti a kérelem részleteit a Poster-gyűjteményben:

1. Válassza ki a listából a szerkeszthető adatok lekéréséhez. 

1. Adja meg a **Paraméterek** lapon felsorolt változók értékeit a **path változók** alatt.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Képernyőkép a Poster-ról. A gyűjtemény kibontva jelenik meg a kérelem megjelenítéséhez. A &quot;Path változók&quot; szakasz ki van emelve a kérelem részletei között." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Adja meg a szükséges **fejléceket** vagy **törzseket** a megfelelő lapokon.

Az összes szükséges adat megadása után a kérést a **Send (Küldés** ) gombbal futtathatja.

A gyűjteményhez saját kéréseit is hozzáadhatja, az alábbi, [*Egyéni kérelem hozzáadása*](#add-an-individual-request) című szakaszban ismertetett eljárás használatával.

## <a name="create-your-own-collection"></a>Saját gyűjtemény létrehozása

Az összes Azure digitális Twins API meglévő gyűjteményének importálása helyett saját gyűjteményt is létrehozhat a semmiből. Ezt követően az [Azure Digital Twins REST API dokumentációjában](/rest/api/azure-digitaltwins/)töltheti fel az egyéni kérelmeket.

### <a name="create-a-postman-collection"></a>Postman-gyűjtemény létrehozása

1. Gyűjtemény létrehozásához kattintson az **új** gombra a főposta ablakban.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Képernyőfelvétel a főposta ablakáról. Az &quot;új&quot; gomb ki van emelve." lightbox="media/how-to-use-postman/postman-new.png":::

    Válasszon egy **gyűjtemény** típust.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Képernyőkép az &quot;új létrehozása&quot; párbeszédablakról a Poster-ben. A &quot;gyűjtemény&quot; beállítás ki van emelve.":::

1. Ekkor megnyílik egy lap, amely kitölti az új gyűjtemény részleteit. Válassza a gyűjtemény alapértelmezett neve (**új gyűjtemény**) melletti Szerkesztés ikont, hogy a saját nevével cserélje le. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Képernyőkép az új gyűjtemény szerkesztési párbeszédpaneléről a Poster-ben. Az &quot;új gyűjtemény&quot; neve melletti szerkesztési ikon ki van emelve." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Ezután folytassa a következő szakasszal, és adjon hozzá egy tulajdonosi jogkivonatot a gyűjteményhez az engedélyezéshez.

### <a name="configure-authorization"></a>Engedélyezés konfigurálása

A következő lépésekkel adhat hozzá egy tulajdonosi jogkivonatot a gyűjteményhez az engedélyezéshez. Itt fogja használni a " [tulajdonosi jogkivonat beolvasása](#get-bearer-token) " szakaszban összegyűjtött **jogkivonat-értéket** a gyűjteményben lévő összes API-kérelemhez.

1. Továbbra is az új gyűjtemény szerkesztés párbeszédpanelén lépjen az **Engedélyezés** lapra.

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Képernyőkép az új gyűjtemény szerkesztési párbeszédpaneléről a Poster-ban, amely az &quot;engedélyezés&quot; lapot mutatja." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Állítsa a típust **OAuth 2,0** értékre, illessze be a hozzáférési tokent a hozzáférési token mezőbe, és válassza a **Mentés** lehetőséget.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Képernyőkép az új gyűjteményhez tartozó Poster Edit párbeszédablakról az &quot;engedélyezés&quot; lapon. A típus: &quot;OAuth 2,0&quot;, a hozzáférési jogkivonat mező pedig ki van emelve." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Ha végzett a fenti lépésekkel, befejezte a gyűjtemény konfigurálását. Ha szeretné, az új gyűjtemény szerkesztés lapját is lezárhatja.

Az új gyűjteményt a fő Poster nézetből, a gyűjtemények lapon tekintheti meg.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Képernyőfelvétel a főposta ablakáról. Az újonnan létrehozott egyéni gyűjtemény ki van emelve a gyűjtemények lapon." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Egyéni kérelem hozzáadása

Most, hogy beállította a gyűjteményt, hozzáadhatja saját kéréseit az Azure Digital Twin API-khoz.

1. Kérelem létrehozásához használja újra az **új** gombot.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Képernyőfelvétel a főposta ablakáról. Az &quot;új&quot; gomb ki van emelve." lightbox="media/how-to-use-postman/postman-new.png":::

    Válassza ki a **kérelem** típusát.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Képernyőkép az &quot;új létrehozása&quot; párbeszédablakról a Poster-ben. A &quot;kérelem&quot; beállítás ki van emelve.":::

1. Ez a művelet megnyitja a mentési kérelem ablakát, ahol megadhatja a kérelem nevét, opcionális leírást adhat meg, és kiválaszthatja a gyűjteményt, amelynek a része. Adja meg a részleteket, és mentse a kérést a korábban létrehozott gyűjteménybe.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Képernyőkép a &quot;Mentés kérése&quot; ablakról a Poster-ben, amely a leírt mezőket mutatja. Kiemelte a &quot;Mentés az Azure Digital Twins Collection&quot; gombot.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Most már megtekintheti a kérést a gyűjtemény alatt, és kiválaszthatja a szerkeszthető adatok lekéréséhez.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Képernyőkép a Poster-ról. Az Azure digitális Twins-gyűjtemény kibontva jelenik meg a kérelem részleteinek megjelenítéséhez." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Kérelem részleteinek beállítása

Ahhoz, hogy egy Poster-kérést küldjön az egyik Azure digitális Twins API-nak, szüksége lesz az API URL-címére és a szükséges adatokra. Ezeket az információkat az [Azure digitális Twins REST API dokumentációjában](/rest/api/azure-digitaltwins/)találja.

Egy példa lekérdezés folytatásához ez a cikk a lekérdezési API-t (és annak [hivatkozási dokumentációját](/rest/api/digital-twins/dataplane/query/querytwins)) fogja használni egy példányban lévő összes digitális ikrek lekérdezéséhez.

1. Kérje le a kérelem URL-címét és típusát a hivatkozási dokumentációból. A lekérdezési API esetében ez jelenleg *post `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. A Poster mezőben állítsa be a kérelem típusát, és írja be a kérelem URL-címét, majd adja meg a helyőrzőket az URL-címben a szükséges módon. Itt fogja használni a példány **állomásneve** az [*Előfeltételek*](#prerequisites) szakaszban.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Képernyőkép az új kérelem részleteiről a Poster-ben. A hivatkozási dokumentációban található lekérdezési URL-cím be lett töltve a kérelem URL-címe mezőbe." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Győződjön meg arról, hogy a **Paraméterek lapon a** kérelemhez megjelenített paraméterek egyeznek a dokumentációban ismertetett paraméterekkel. A Poster-kérelemben a `api-version` paraméter automatikusan ki lett töltve, amikor az előző lépésben megadta a kérelem URL-címét. A lekérdezési API esetében ez az egyetlen kötelező paraméter, így ez a lépés elvégezhető.
1. Az **Engedélyezés** lapon állítsa be a típust úgy, hogy a **szülőtől örökölje a hitelesítést**. Ez azt jelzi, hogy ez a kérelem a teljes gyűjteményhez korábban beállított engedélyt fogja használni.
1. Győződjön meg arról, hogy a **fejlécek** lapon a kérelemhez megjelenített fejlécek megfelelnek a dokumentációban leírt feltételeknek. Ennél a kérésnél több fejléc automatikusan ki lett töltve. A lekérdezési API esetében a fejléc egyik beállítása sem szükséges, így ez a lépés megtörténik.
1. Győződjön meg arról, hogy a **törzs** lapon a kérelemhez megjelenített törzs megfelel a dokumentációban ismertetett követelményeknek. A lekérdezési API esetében egy JSON-törzs szükséges a lekérdezés szövegének megadásához. Itt látható egy példa erre a kérelemre, amely a példányban található összes digitális ikrek lekérdezését hajtja végre:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Képernyőkép az új kérelem részleteiről a Body (törzs) lapon. Egy nyers JSON-törzset tartalmaz, amelynek a &quot;SELECT * FROM DIGITALTWINS&quot; lekérdezése van." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Az Azure Digital Twins-lekérdezések készítésével kapcsolatos további információkért lásd [*: útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md).

1. A kérelem típusához esetlegesen szükséges egyéb mezőkre vonatkozó dokumentációban talál további tudnivalókat. A lekérdezési API esetében az összes követelmény teljesült a Poster-kérelemben, így ez a lépés elvégezhető.
1. A kész kérelem elküldéséhez használja a **Küldés** gombot.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Képernyőkép a Poster-ról, amely az új kérelem részleteit jeleníti meg. A Küldés gomb ki van emelve." lightbox="media/how-to-use-postman/postman-request-send.png":::

A kérelem elküldése után a válasz részletei a kérelem alatt, a Poster ablakban fognak megjelenni. Megtekintheti a válasz állapotkód és a szövegtörzs szövegét.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="A Poster által elküldett kérelem képernyőképe. A kérelem részletei alatt a válasz jelenik meg. Az állapot 200 OK, és a törzs a lekérdezés eredményeit jeleníti meg." lightbox="media/how-to-use-postman/postman-request-response.png":::

Összehasonlíthatja a dokumentációban megadott, várt válaszokra adott választ is, hogy ellenőrizze az eredményt, vagy többet tudjon meg az esetlegesen felmerülő hibákról.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a digitális Twins API-król, olvassa el a következő [*témakört: útmutató: az Azure Digital Twins API-k és SDK*](how-to-use-apis-sdks.md)-k használata, illetve a [REST API-k dokumentációjának](/rest/api/azure-digitaltwins/)megtekintése.
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
ms.openlocfilehash: 18ae21c4b1348a1690818f8c07a5b3fae45102cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232273"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>A Poster használata a kérelmek Azure digitális Twins API-khoz való küldéséhez

A [Poster](https://www.getpostman.com/) egy REST-alapú tesztelési eszköz, amely kulcsfontosságú HTTP-kérelmek funkcióit biztosítja egy asztali és beépülő Modulos grafikus felhasználói felületen. Használhatja a HTTP-kérelmeket, és beküldheti azokat az [Azure Digital Twins REST API](how-to-use-apis-sdks.md)-khoz.

Ez a cikk azt ismerteti, hogyan konfigurálhatja a [Poster Rest-ügyfelet](https://www.getpostman.com/) az Azure digitális Twins API-kkal való interakcióhoz az alábbi lépések végrehajtásával:

1. Az [Azure CLI](/cli/azure/install-azure-cli) használatával beszerezhet egy tulajdonosi jogkivonatot, amelyet a Poster API-kérések készítéséhez fog használni.
1. Állítson be egy Poster-gyűjteményt, és konfigurálja a Poster REST-ügyfelet, hogy a tulajdonosi jogkivonatot használja a hitelesítéshez.
1. A konfigurált Poster használatával hozzon létre és küldjön egy kérést az Azure Digital Twins API-khoz.

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

1. Ezután az az [Account Get-Access-Token](/cli/azure/account#az_account_get_access_token) paranccsal szerezzen be egy tulajdonosi jogkivonatot, amely hozzáfér az Azure Digital Twins szolgáltatáshoz. Ebben a parancsban az Azure Digital Twins szolgáltatás végpontjának (statikus értéke) erőforrás-AZONOSÍTÓját kell átadni ahhoz `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` , hogy olyan hozzáférési jogkivonatot kapjon, amely hozzáfér az Azure digitális Twins-erőforrásaihoz.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Másolja a értéket az `accessToken` eredménybe, és mentse a következő szakaszban való használatra. Ez az a **jogkivonat-érték** , amelyet a Poster-nek meg kell adnia a kérések hitelesítéséhez.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Egy helyi konzolablak nézete, amely az az Account Get-Access-Token parancs eredményét jeleníti meg. Az eredmény egyik mezőjének neve accessToken, a--kezdet és a ey – kiemelve.":::

>[!TIP]
>Ez a jogkivonat legalább öt percig érvényes, és legfeljebb 60 percet vesz igénybe. Ha elfogyott az aktuális jogkivonat időkorlátja, megismételheti a szakasz lépéseit, hogy újat kapjon.

## <a name="set-up-postman-collection-and-authorization"></a>Poster-gyűjtemény és-engedélyezés beállítása

Ezután állítsa be a Poster-t az API-kérések létrehozásához.
Ezek a lépések a helyi Poster-alkalmazásban történnek, ezért nyissa meg a Poster alkalmazást a számítógépen.

### <a name="create-a-postman-collection"></a>Postman-gyűjtemény létrehozása

A Poster-kérelmek **gyűjteményekben** (kérelmek csoportjai) lesznek mentve. Amikor létrehoz egy gyűjteményt a kérelmek csoportosításához, egyszerre több kérelemre is alkalmazhat közös beállításokat. Ez nagy mértékben egyszerűsítheti az engedélyezést, ha egynél több kérést szeretne létrehozni az Azure digitális Twins API-kkal, mivel csak egyszer kell konfigurálnia a teljes gyűjteményhez tartozó hitelesítést.

1. Gyűjtemény létrehozásához nyomja le az *+ új gyűjtemény* gombot.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Egy újonnan megnyitott Poster-ablak megtekintése. Az &quot;új gyűjtemény&quot; gomb ki van emelve":::

1. Az alábbi *új gyűjtemény létrehozása* ablakban adja meg a gyűjtemény **nevét** és **leírását** (nem kötelező).

Ezután folytassa a következő szakasszal, és adjon hozzá egy tulajdonosi jogkivonatot a gyűjteményhez az engedélyezéshez.

### <a name="add-authorization-token-and-finish-collection"></a>Engedélyezési jogkivonat hozzáadása és a befejezési gyűjtemény

1. Az *új gyűjtemény létrehozása* párbeszédpanelen lépjen az *Engedélyezés* lapra. Itt helyezheti el azt a **jogkivonat-értéket** , amelyet a gyűjteményben lévő összes API-kérelemhez használni fog a [tulajdonosi jogkivonat beolvasása](#get-bearer-token) szakaszban.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="Az &quot;új gyűjtemény létrehozása&quot; Poster-ablak, amely az &quot;engedélyezés&quot; lapot mutatja.":::

1. Állítsa a *típust* _**OAuth 2,0**_ értékre, és illessze be a hozzáférési tokent a *hozzáférési jogkivonat* mezőbe.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="Az &quot;új gyűjtemény létrehozása&quot; Poster-ablak, amely az &quot;engedélyezés&quot; lapot mutatja. A &quot;OAuth 2,0&quot; típus van kiválasztva, és a hozzáférési jogkivonat mező, ahol a hozzáférési jogkivonat értéke beilleszthető, ki van emelve.":::

1. Miután beillesztette a tulajdonosi jogkivonatba, a *Létrehozás* gombra kattintva fejezze be a gyűjtemény létrehozását.

Az új gyűjtemény most már megtekinthető a fő Poster-nézetből a *gyűjtemények* területen.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="A főposta ablakának nézete Az újonnan létrehozott gyűjtemény ki van emelve a gyűjtemények lapon.":::

## <a name="create-a-request"></a>Kérelem létrehozása

Az előző lépések elvégzése után kéréseket hozhat létre az Azure Digital Twin API-khoz.

1. Kérelem létrehozásához nyomja meg az *+ új* gombot.

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="A főposta ablakának nézete Az &quot;új&quot; gomb ki van emelve":::

1. Válassza a *kérelem* lehetőséget.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Az új elemek létrehozásához választható beállítások megtekintése. A &quot;kérelem&quot; beállítás ki van emelve":::

1. Ez a művelet megnyitja a *mentési kérelem* ablakát, ahol megadhatja a kérelem nevét, opcionális leírást adhat meg, és kiválaszthatja a gyűjteményt, amelynek a része. Adja meg a részleteket, és mentse a kérést a korábban létrehozott gyűjteménybe.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="A Save Request (kérelem mentése) ablak megtekintése, ahol kitöltheti a leírt mezőket. A &quot;Mentés az Azure digitális ikrek számára&quot; gomb ki van emelve":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Most már megtekintheti a kérést a gyűjtemény alatt, és kiválaszthatja a szerkeszthető adatok lekéréséhez.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="A főposta ablakának nézete A rendszer kibontja az Azure digitális Twins-gyűjteményt, és kiemelte a lekérdezési ikrek kérését. A kérelem részletei az oldal közepén láthatók." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Kérelem részleteinek beállítása

Ahhoz, hogy egy Poster-kérést küldjön az egyik Azure digitális Twins API-nak, szüksége lesz az API URL-címére és a szükséges adatokra. Ezeket az információkat az [Azure digitális Twins REST API dokumentációjában](/rest/api/azure-digitaltwins/)találja.

Egy példa lekérdezés folytatásához ez a cikk a lekérdezési API-t (és annak [hivatkozási dokumentációját](/rest/api/digital-twins/dataplane/query/querytwins)) fogja használni egy példányban lévő összes digitális ikrek lekérdezéséhez.

1. Kérje le a kérelem URL-címét és típusát a hivatkozási dokumentációból. A lekérdezési API esetében ez jelenleg *post `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. A Poster mezőben állítsa be a kérelem típusát, és írja be a kérelem URL-címét, majd adja meg a helyőrzőket az URL-címben a szükséges módon. Itt fogja használni a példány **állomásneve** az [*Előfeltételek*](#prerequisites) szakaszban.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Az új kérelem részleteiben a hivatkozási dokumentációban található lekérdezési URL-cím be lett töltve a kérelem URL-címe mezőbe." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Győződjön meg arról, hogy a *Paraméterek lapon a* kérelemhez megjelenített paraméterek egyeznek a dokumentációban ismertetett paraméterekkel. A Poster-kérelemben a `api-version` paraméter automatikusan ki lett töltve, amikor az előző lépésben megadta a kérelem URL-címét. A lekérdezési API esetében ez az egyetlen kötelező paraméter, így ez a lépés elvégezhető.
1. Az *Engedélyezés* lapon állítsa be a *típust* úgy, hogy a *szülőtől örökölje a hitelesítést*. Ez azt jelzi, hogy ez a kérelem a teljes gyűjteményhez korábban beállított hitelesítést fogja használni.
1. Győződjön meg arról, hogy a *fejlécek* lapon a kérelemhez megjelenített fejlécek megfelelnek a dokumentációban leírt feltételeknek. Ennél a kérésnél több fejléc automatikusan ki lett töltve. A lekérdezési API esetében a fejléc egyik beállítása sem szükséges, így ez a lépés megtörténik.
1. Győződjön meg arról, hogy a *törzs* lapon a kérelemhez megjelenített törzs megfelel a dokumentációban ismertetett követelményeknek. A lekérdezési API esetében egy JSON-törzs szükséges a lekérdezés szövegének megadásához. Itt látható egy példa erre a kérelemre, amely a példányban található összes digitális ikrek lekérdezését hajtja végre:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Az új kérelem részleteiben megjelenik a törzs lap. Egy nyers JSON-törzset tartalmaz, amelynek a &quot;SELECT * FROM DIGITALTWINS&quot; lekérdezése van." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Az Azure Digital Twins-lekérdezések készítésével kapcsolatos további információkért lásd [*: útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md).

1. A kérelem típusához esetlegesen szükséges egyéb mezőkre vonatkozó dokumentációban talál további tudnivalókat. A lekérdezési API esetében az összes követelmény teljesült a Poster-kérelemben, így ez a lépés elvégezhető.
1. A kész kérelem elküldéséhez használja a *Küldés* gombot.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Az új kérelem részleteinek közelében a Küldés gomb kiemelve jelenik meg." lightbox="media/how-to-use-postman/postman-request-send.png":::

A kérelem elküldése után a válasz részletei a kérelem alatt, a Poster ablakban fognak megjelenni. Megtekintheti a válasz állapotkód és a szövegtörzs szövegét.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Az elküldött kérelem részletei alatt a válasz részleteit kiemeljük. 200 OK és szövegtörzs szerepel a lekérdezés által visszaadott digitális ikrek leírásában." lightbox="media/how-to-use-postman/postman-request-response.png":::

Összehasonlíthatja a dokumentációban megadott, várt válaszokra adott választ is, hogy ellenőrizze az eredményt, vagy többet tudjon meg az esetlegesen felmerülő hibákról.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a digitális Twins API-król, olvassa el a következő [*témakört: útmutató: az Azure Digital Twins API-k és SDK*](how-to-use-apis-sdks.md)-k használata, illetve a [REST API-k dokumentációjának](/rest/api/azure-digitaltwins/)megtekintése.
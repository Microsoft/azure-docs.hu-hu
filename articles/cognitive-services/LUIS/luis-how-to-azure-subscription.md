---
title: Szerzői és futtatókörnyezeti kulcsok használata – LUIS
description: Ha először használja a LUIS-t, nem kell létrehoznia authoring-kulcsot. Ha közzé szeretné tenni az alkalmazást, majd használja a futásidejű végpontját, létre kell hoznia és hozzá kell rendelnie a futásidejű kulcsot az alkalmazáshoz.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8944c9e49bde8c452a10a1886cae316a0f7a33f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945066"
---
# <a name="create-luis-resources"></a>LUIS-erőforrások létrehozása

A szerzői és lekérdezési előrejelzési futtatókörnyezet erőforrásai hitelesítést biztosítanak a Language Understanding (LUIS) alkalmazás és az előrejelzési végpont számára.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS-erőforrások

A LUIS három típusú Azure-erőforrást és egy nem Azure-erőforrást is lehetővé tesz:

|Erőforrás|Rendeltetés|Kognitív szolgáltatás `kind`|Kognitív szolgáltatás `type`|
|--|--|--|--|
|Erőforrás létrehozása|Lehetővé teszi az alkalmazások létrehozását, kezelését, betanítását, tesztelését és közzétételét. [Hozzon létre egy Luis authoring-erőforrást](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) , ha a Luis-alkalmazásokat programozott módon vagy a Luis-portálon szeretné létrehozni. [Telepítenie kell a Luis-fiókját](luis-migration-authoring.md#what-is-migration) , mielőtt összekapcsolja az Azure authoring-erőforrásait az alkalmazásával. A szerzői erőforrás engedélyeit a [közreműködő szerepkörrel](#contributions-from-other-authors)rendelkező személyek hozzárendelésével szabályozhatja. <br><br> A LUIS authoring Resource egyik szintje elérhető:<br> <ul> <li>**Ingyenes F0 authoring Resource**, amely 1 000 000 ingyenes szerzői tranzakciókat és 1 000 ingyenes tesztelési előrejelzési végpontot biztosít havonta. |`LUIS.Authoring`|`Cognitive Services`|
|Előrejelzési erőforrás| A LUIS-alkalmazás közzététele után az előrejelzési erőforrás/kulcs használatával kérdezheti le az előrejelzési végpontok kéréseit. Hozzon létre egy LUIS-előrejelzési erőforrást, mielőtt az ügyfélalkalmazás a szerzői vagy kezdő erőforrás által biztosított 1 000-kérelmeken kívüli előrejelzéseket kér. <br><br> Az előrejelzési erőforráshoz két szint érhető el:<br><ul> <li> **Ingyenes F0 előrejelzési erőforrás**, amely 10 000 ingyenes előrejelzési végpontot biztosít havonta.<br> <li> **Standard S0 előrejelzési erőforrás**, amely a fizetős szint. [További információ a díjszabásról.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Kezdő/próbaverziós erőforrás|Lehetővé teszi az alkalmazások létrehozását, kezelését, betanítását, tesztelését és közzétételét. Ez az erőforrás alapértelmezés szerint jön létre, ha az alapszintű erőforrás lehetőséget választja, amikor először bejelentkezik a LUIS-be. Az alapszintű kulcs végül elavulttá válik. Minden LUIS felhasználónak [át kell telepítenie a fiókját](luis-migration-authoring.md#what-is-migration) , és hozzá kell kapcsolnia a Luis-alkalmazásait egy authoring-erőforráshoz. A szerzői erőforrástól eltérően ez az erőforrás nem ad Önnek engedélyeket az Azure szerepköralapú hozzáférés-vezérléséhez. <br><br> A szerzői erőforráshoz hasonlóan a kezdő erőforrás 1 000 000 ingyenes szerzői tranzakciókat és a 1 000 ingyenes tesztelési végponti kéréseket biztosít.|-|Nem Azure-erőforrás.|
|[Cognitive Services több szolgáltatásból származó erőforrás kulcsa](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|A lekérdezés előrejelzési végpontjának kérelmei megosztva a LUIS és más támogatott kognitív szolgáltatásokkal.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> A LUIS kétféle F0 (ingyenes szint) erőforrást biztosít: az egyiket a tranzakciók és egy az előrejelzési tranzakciók létrehozásához. Ha kifogyott az előrejelzési tranzakciók ingyenes kvótája, győződjön meg arról, hogy a F0 előrejelzési erőforrást használja, amely havi 10 000 ingyenes tranzakciót biztosít, nem pedig a szerzői erőforrást, amely havonta 1 000-os előrejelzési tranzakciókat biztosít.

Ha az Azure-Erőforrás-létrehozási folyamat elkészült, [rendelje hozzá az erőforrást](#assign-a-resource-to-an-app) az alkalmazáshoz a Luis portálon.

> [!important]
> LUIS-alkalmazásokat kell létrehoznia azokon a [régiókban](luis-reference-regions.md#publishing-regions) , ahol közzé szeretné tenni a közzétételt és a lekérdezést.

## <a name="resource-ownership"></a>Erőforrás-tulajdonos

Az Azure-erőforrásokhoz, például a LUIS-erőforrásokhoz az erőforrást tartalmazó előfizetés tulajdonosa.

Egy erőforrás tulajdonjogának megváltoztatásához hajtsa végre az alábbi műveletek egyikét:
* Vigye át az előfizetés [tulajdonjogát](../../cost-management-billing/manage/billing-subscription-transfer.md) .
* Exportálja a LUIS alkalmazást fájlként, majd importálja az alkalmazást egy másik előfizetésre. Az Exportálás a LUIS portál **saját alkalmazások** lapján érhető el.

## <a name="resource-limits"></a>Erőforráskorlátok

### <a name="authoring-key-creation-limits"></a>Szerzői kulcsok létrehozási korlátai

Régiónként, előfizetéshez legfeljebb 10 szerzői kulcsot hozhat létre. A közzétételi régiók eltérnek a szerzői régióktól. Győződjön meg arról, hogy olyan alkalmazást hoz létre a szerzői régióban, amely megfelel a közzétételi régiónak, ahol az ügyfélalkalmazás található. További információ arról, hogyan lehet a szerzői régiók a közzétételi régiókban leképezni a létrehozási [és közzétételi](luis-reference-regions.md)régiókat. 

A kulcsok korlátozásával kapcsolatos további információkért lásd: [kulcs korlátai](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>A kulcshasználat korlátaival kapcsolatos hibák

A használati korlátok a díjszabási szinten alapulnak.

Ha túllépett a másodpercenkénti tranzakciók (TPS) kvótáján, HTTP 429-es hibaüzenetet kap. Ha túllépi a tranzakció/hónap (TPM) kvótáját, HTTP 403-es hibát kap.


### <a name="reset-an-authoring-key"></a>Szerzői kulcs alaphelyzetbe állítása

[Migrált authoring Resource](luis-migration-authoring.md) apps esetén: Ha a szerzői kulcs biztonsága sérül, állítsa alaphelyzetbe a kulcsot a Azure Portal az authoring Resource **kulcsok** lapján.

A nem áttelepített alkalmazások esetében: a kulcs alaphelyzetbe áll a LUIS-portál összes alkalmazásában. Ha a szerzői API-kon keresztül készíti el az alkalmazásokat, módosítania kell az értéket `Ocp-Apim-Subscription-Key` az új kulcsra.

### <a name="regenerate-an-azure-key"></a>Azure-kulcs újralétrehozása

Az Azure-kulcsok újragenerálása a Azure Portal **kulcsok** lapjáról végezhető el.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Alkalmazások tulajdonlása, hozzáférése és biztonsága

Az alkalmazást az Azure-erőforrásai határozzák meg, amelyeket a tulajdonos előfizetése határoz meg.

Elvégezheti a LUIS-alkalmazás áthelyezését. Az alábbi források segítséget nyújtanak a Azure Portal vagy az Azure CLI használatával:

* [Alkalmazás áthelyezése a LUIS authoring-erőforrások között](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Erőforrás áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Erőforrás áthelyezése ugyanazon az előfizetésen belül vagy előfizetések között](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Más szerzők hozzájárulásai

[Migrált authoring Resource](luis-migration-authoring.md) apps esetén: a Azure Portalban a **hozzáférés-vezérlés (iam)** lapon kezelheti a szerzői műveletek _közreműködőit_ . Megtudhatja [, hogyan adhat hozzá felhasználót](luis-how-to-collaborate.md) a közreműködő e-mail-címével és a közreműködő szerepkörrel.

A még nem migrált alkalmazások esetében: az összes _közreműködőt_ kezelheti a Luis portál **Manage-> közreműködők** lapján.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Lekérdezés-előrejelzési hozzáférés magán-és nyilvános alkalmazásokhoz

A privát alkalmazások esetében a lekérdezések előrejelzési futtatókörnyezetének hozzáférése a tulajdonosokhoz és a közreműködők számára érhető el. A nyilvános alkalmazások esetében a futásidejű hozzáférés olyan felhasználók számára érhető el, akik saját Azure [kognitív szolgáltatással](../cognitive-services-apis-create-account.md) vagy [Luis](#create-resources-in-the-azure-portal) Runtime-erőforrással és a nyilvános alkalmazás azonosítójával rendelkeznek.

Jelenleg nem a nyilvános alkalmazások katalógusa.

### <a name="authoring-permissions-and-access"></a>Szerzői engedélyek és hozzáférés
A [Luis](luis-reference-regions.md#luis-website) -portálról vagy az [authoring API](https://go.microsoft.com/fwlink/?linkid=2092087) -k alkalmazáshoz való hozzáférését az Azure authoring Resource vezérli.

A tulajdonos és az összes közreműködő hozzáfér az alkalmazás létrehozásához.

|A szerzői hozzáférés az alábbiakat tartalmazza:|Jegyzetek|
|--|--|
|Végponti kulcsok hozzáadása vagy eltávolítása||
|Verzió exportálása||
|Végponti naplók exportálása||
|Importálási verzió||
|Alkalmazás nyilvánosvé tétele|Ha egy alkalmazás nyilvános, a szerzői vagy végponti kulccsal rendelkező bárki lekérdezheti az alkalmazást.|
|Modell módosítása|
|Közzététel|
|Az [aktív tanulási](luis-how-to-review-endpoint-utterances.md) végpont hosszúságú kimondott szöveg áttekintése|
|Betanítás|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>A végpont futásidejű hozzáférésének előrejelzése

Az előrejelzési végpont lekérdezésének hozzáférését a **kezelés** szakasz **alkalmazás adatai** lapján található beállítás szabályozza.

|[Privát végpont](#runtime-security-for-private-apps)|[Nyilvános végpont](#runtime-security-for-public-apps)|
|:--|:--|
|Elérhető a tulajdonos és a közreműködők számára|Elérhető a tulajdonos, a közreműködők és bárki másnak, aki ismeri az alkalmazás AZONOSÍTÓját|

Megadhatja, hogy ki látja el a LUIS Runtime-kulcsot úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha a LUIS-t egy robotból használja, a robot és a LUIS közötti kapcsolat már biztonságosabb. Ha közvetlenül hívja meg a LUIS-végpontot, hozzon létre egy kiszolgálóoldali API-t (például egy Azure- [függvényt](https://azure.microsoft.com/services/functions/)) szabályozott hozzáféréssel (például az [Azure ad](https://azure.microsoft.com/services/active-directory/)-val). A kiszolgálóoldali API meghívása és hitelesítése, valamint az engedélyezés ellenőrzése után adja át a hívást a LUIS-nek. Ez a stratégia nem akadályozza meg az ember általi támadásokat. Ez azonban a felhasználók által használt kulcs-és végpont-URL-címet is felhasználja, lehetővé téve a hozzáférés nyomon követését, és lehetővé teszi a végponti válaszok naplózásának hozzáadását (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Futásidejű biztonság privát alkalmazásokhoz

A privát alkalmazások futtatókörnyezete csak a következő kulcsoknál érhető el:

|Kulcs és felhasználó|Magyarázat|
|--|--|
|Tulajdonos szerzői kulcsa| Akár 1 000 végpontot elér|
|Közreműködő/közreműködő szerzői kulcsok| Akár 1 000 végpontot elér|
|Egy szerző vagy közreműködő vagy közreműködő által a LUIShoz rendelt kulcs|Kulcshasználat szintje alapján|

### <a name="runtime-security-for-public-apps"></a>Futtatókörnyezeti biztonság nyilvános alkalmazásokhoz

Ha az alkalmazás nyilvánosként van konfigurálva, _minden_ érvényes Luis authoring kulcs vagy Luis Endpoint kulcs lekérdezheti, ha a kulcs nem használta a teljes végponti kvótát.

Egy felhasználó, aki nem tulajdonosa vagy közreműködője, csak akkor férhet hozzá egy nyilvános alkalmazás futtatókörnyezetéhez, ha az alkalmazás azonosítója van megadva. A LUIS nem rendelkezik nyilvános piaccal, vagy bármilyen más módon, hogy a felhasználók megkeressék a nyilvános alkalmazást.

A nyilvános alkalmazások minden régióban közzé vannak téve. Így a régión alapuló LUIS-erőforrás kulccsal rendelkező felhasználók hozzáférhetnek az alkalmazáshoz, bármelyik régióban az erőforrás-kulcshoz társítva.


### <a name="control-access-to-your-query-prediction-endpoint"></a>A lekérdezés-előrejelzési végpont elérésének szabályozása

Megadhatja, hogy ki láthatja a LUIS előrejelzési futtatókörnyezet végpontjának kulcsát úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha a LUIS-t egy robotból használja, a robot és a LUIS közötti kapcsolat már biztonságosabb. Ha közvetlenül hívja meg a LUIS-végpontot, hozzon létre egy kiszolgálóoldali API-t (például egy Azure- [függvényt](https://azure.microsoft.com/services/functions/)) szabályozott hozzáféréssel (például az [Azure ad](https://azure.microsoft.com/services/active-directory/)-val). A kiszolgálóoldali API meghívásakor, valamint a hitelesítés és az engedélyezés ellenőrzése után adja át a hívást a LUIS-nek. Ez a stratégia nem akadályozza meg az ember általi támadásokat. Azonban a végpontokat a felhasználóktól eltorzítja, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végponti válaszok naplózásának hozzáadását (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Jelentkezzen be a LUIS-portálra, és kezdje el a szerzői műveleteket

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai) , és fogadja el a használati feltételeket.
1. A LUIS-alkalmazás létrehozásához válassza ki az Azure LUIS authoring Key:

   ![Képernyőkép, amely megjeleníti az üdvözlő képernyőt.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Ha elkészült az erőforrás-kiválasztási folyamattal, [hozzon létre egy új alkalmazást](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Erőforrások létrehozása az Azure CLI-ben

Az [Azure CLI](/cli/azure/install-azure-cli) használatával egyenként hozhatja létre az erőforrásokat.

Erőforrás `kind` :

* Authoring `LUIS.Authoring`
* Jóslás `LUIS`

1. Jelentkezzen be az Azure CLI-be:

    ```azurecli
    az login
    ```

    Ez a parancs egy böngészőt nyit meg, így kiválaszthatja a megfelelő fiókot, és megadhatja a hitelesítést.

1. Hozzon létre egy nevű LUIS Author `LUIS.Authoring` -erőforrást `my-luis-authoring-resource` . Hozza létre a nevet  a `my-resource-group` régióhoz tartozó meglévő erőforráscsoport számára `westus` .

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Hozzon létre egy nevű LUIS előrejelzési végpont-erőforrást `LUIS` `my-luis-prediction-resource` . Hozza létre a nevet  a `my-resource-group` régióhoz tartozó meglévő erőforráscsoport számára `westus` . Ha azt szeretné, hogy az ingyenes szinten nagyobb átviteli sebesség legyen elérhető, váltson a következőre: `F0` `S0` . [További információ a díjszabási szintekről és az átviteli sebességről.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Ezeket a kulcsokat a Luis portál nem használja, amíg hozzá nem rendelik az   >  **Azure-erőforrások** kezelése oldalon a Luis portálon.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Erőforrások kiosztása a LUIS-portálon

Létrehozhat egy authoring-erőforrást egyetlen alkalmazáshoz vagy a LUIS összes alkalmazásához. Az alábbi eljárás az összes alkalmazást egyetlen szerzői erőforráshoz rendeli.

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai).
1. A jobb felső sarokban válassza ki a felhasználói fiókot, majd válassza a **Beállítások** lehetőséget.
1. A **felhasználói beállítások** lapon válassza a **szerzői erőforrás hozzáadása** lehetőséget, majd válasszon ki egy meglévő authoring-erőforrást. Kattintson a **Mentés** gombra.

## <a name="assign-a-resource-to-an-app"></a>Erőforrás kiosztása egy alkalmazáshoz

>[!NOTE]
>Ha nem rendelkezik Azure-előfizetéssel, nem tud új erőforrást hozzárendelni vagy létrehozni. Létre kell hoznia egy [ingyenes Azure-fiókot](https://azure.microsoft.com/en-us/free/) , majd vissza kell térnie a Luis-hoz egy új erőforrás létrehozásához a portálon.

Ezzel az eljárással hozhat létre szerzői vagy előrejelzési erőforrásokat, vagy hozzárendelhet egyet egy alkalmazáshoz: 

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai). Válasszon ki egy alkalmazást a **saját alkalmazások** listából.
1. Ugrás az Azure-erőforrások **kezeléséhez**  >  :

    ![Képernyőkép, amely az Azure-erőforrások oldalt jeleníti meg.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Az **előrejelzési erőforrás** vagy a **szerzői erőforrás** lapon jelölje be az **előrejelzési erőforrás hozzáadása** vagy a **szerzői erőforrás hozzáadása** gomb.
1. Az űrlap mezőinek használatával keresse meg a megfelelő erőforrást, majd kattintson a **Mentés** gombra.
1. Ha nem rendelkezik meglévő erőforrással, létrehozhat egyet az **új Luis-erőforrás létrehozása** lehetőség kiválasztásával az ablak alján.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Lekérdezés-előrejelzési futtatókörnyezet erőforrásának kiosztása a LUIS-portál használata nélkül

Automatizált folyamatok, például a CI/CD-folyamatok esetében érdemes automatizálni a LUIS Runtime-erőforrások egy LUIS-alkalmazáshoz való hozzárendelését. Ehhez hajtsa végre a következő lépéseket:

1. Azure Resource Manager jogkivonat beszerzése a [webhelyről](https://resources.azure.com/api/token?plaintext=true). Ez a token lejár, ezért azonnal használja. A kérelem Azure Resource Manager tokent ad vissza.

    ![A Azure Resource Manager tokent kérő webhelyet bemutató képernyőkép.](./media/luis-manage-keys/get-arm-token.png)

1. Használja a tokent a LUIS Runtime-erőforrások előfizetések közötti kéréséhez. Használja a [Get Luis Azure accounts API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)-t, amelyhez a felhasználói fiókja hozzáfér.

    A POST API-nak a következő értékeket kell megadnia:

    |Fejléc|Érték|
    |--|--|
    |`Authorization`|A értéke: `Authorization` `Bearer {token}` . A jogkivonat értékének előtt szerepelnie kell a szónak `Bearer` és a szóköznek.|
    |`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|

    Az API a LUIS-előfizetéseit képviselő JSON-objektumok tömbjét adja vissza. A visszaadott értékek közé tartozik az előfizetés azonosítója, az erőforráscsoport és az erőforrás neve, amelyet a következőként adott vissza: `AccountName` . Keresse meg azt a tömböt, amely a LUIS-alkalmazáshoz hozzárendelni kívánt LUIS-erőforrás.

1. Rendelje hozzá a tokent a LUIS-erőforráshoz a [Luis Azure-fiókok alkalmazás-API-hoz való hozzárendelésével](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) .

    A POST API-nak a következő értékeket kell megadnia:

    |Típus|Beállítás|Érték|
    |--|--|--|
    |Fejléc|`Authorization`|A értéke: `Authorization` `Bearer {token}` . A jogkivonat értékének előtt szerepelnie kell a szónak `Bearer` és a szóköznek.|
    |Fejléc|`Ocp-Apim-Subscription-Key`|A szerzői kulcs.|
    |Fejléc|`Content-type`|`application/json`|
    |QueryString|`appid`|A LUIS-app azonosítója.
    |Törzs||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "ResourceGroup-2",<br>"AccountName": "Luis-USWest-S0-2"}|

    Ha ez az API sikeres, a függvény visszaadja `201 - created status` .

## <a name="unassign-a-resource"></a>Erőforrás hozzárendelésének megszüntetése

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), majd válasszon ki egy alkalmazást a **saját alkalmazások** listából.
1. Ugrás az Azure-erőforrások **kezeléséhez**  >  .
1. Az erőforrás- **előrejelzési** erőforrás vagy a **szerzői erőforrás** lapon válassza az erőforrás **hozzárendelésének megszüntetése** gombot.

Ha törli az erőforrás hozzárendelését, az nem törlődik az Azure-ból. Csak a LUIS-ról van lecsatolva.


## <a name="delete-an-account"></a>Fiók eltávolítása

Az adatok [tárolásával és eltávolításával](luis-concept-data-storage.md#accounts) kapcsolatos információkért tekintse meg a fiók törlése után megjelenő adatokat.

## <a name="change-the-pricing-tier"></a>Árképzési szintek módosítása

1.  [A Azure Portalban](https://portal.azure.com)keresse meg és válassza ki a Luis-előfizetését:

    ![A Azure Portal LUIS-előfizetését bemutató képernyőkép.](./media/luis-usage-tiers/find.png)
1.  Válassza ki az **árképzési szintet** a rendelkezésre álló díjszabási szintek megtekintéséhez:

    ![A díjszabási csomag menüelemét bemutató képernyőkép.](./media/luis-usage-tiers/subscription.png)
1.  Válassza ki a díjszabási szintet, majd kattintson a **kiválasztás** gombra a módosítás mentéséhez:

    ![Képernyőkép, amely bemutatja, hogyan lehet kijelölni és menteni egy árképzési szintet.](./media/luis-usage-tiers/plans.png)

    A díjszabás megváltozása után egy előugró ablak ellenőrzi az árképzési réteg frissítését:

    ![A díjszabási frissítést ellenőrző előugró ablak képernyőképe.](./media/luis-usage-tiers/updated.png)
1. Ne felejtse el [hozzárendelni ezt a végponti kulcsot](#assign-a-resource-to-an-app) a **közzétételi** lapon, és használja azt az összes végponti lekérdezésben.

## <a name="view-azure-resource-metrics"></a>Azure-erőforrás metrikáinak megtekintése

### <a name="view-a-summary-of-azure-resource-usage"></a>Az Azure-erőforrás-használat összefoglalásának megtekintése
A LUIS használati adatait a Azure Portalban tekintheti meg. Az **Áttekintés** oldalon egy Összegzés látható, beleértve a legutóbbi hívásokat és hibákat. Ha LUIS-végponti kérelmet hoz létre, legfeljebb öt percet vehet igénybe, amíg a módosítás megjelenik.

![Képernyőkép, amely az Áttekintés oldalt mutatja.](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure-Erőforrás-használati diagramok testreszabása
A **metrikák** lap részletesebben megjeleníti az adatokat:

![A metrikák oldalt megjelenítő képernyőkép.](./media/luis-usage-tiers/metrics-default.png)

A metrikák diagramjait egy adott időszakra és metrikai típusra is konfigurálhatja:

![A testreszabott diagramot megjelenítő képernyőkép.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Tranzakciók teljes küszöbértékének riasztása
Ha tudni szeretné, hogy mikor ér el egy bizonyos tranzakciós küszöbértéket, például 10 000 tranzakciót, létrehozhat egy riasztást:

![A riasztási szabályok lapot megjelenítő képernyőkép.](./media/luis-usage-tiers/alert-default.png)

Adjon hozzá egy metrikai riasztást a **hívások teljes** metrikája számára egy adott időtartamra vonatkozóan. Adja meg az összes olyan személy e-mail-címét, akik számára meg kell kapnia a riasztást. A riasztást fogadó összes rendszerhez adjon hozzá webhookokat. Egy logikai alkalmazást is futtathat a riasztás elindítása után.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja [, hogyan használhatja a verzióit](luis-how-to-manage-versions.md) az alkalmazás életciklusának szabályozására.
* Migrálás az új [szerzői erőforrásba](luis-migration-authoring.md).

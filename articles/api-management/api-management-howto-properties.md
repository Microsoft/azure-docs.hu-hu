---
title: Elnevezett értékek használata az Azure API Management szabályzatban
description: Megtudhatja, hogyan használhatja a megnevezett értékeket az Azure API Management szabályzatokkal. A nevestűs értékek literális sztringeket, szabályzatkifejezéseket és titkos adatokat tartalmazhatnak a Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: b0e076f3b248942870ba58a51c85c3df1f1277a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750607"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Elnevezett értékek használata az Azure API Management szabályzatában

[API Management szabályzatok](api-management-howto-policies.md) a rendszer hatékony képességei, amelyek lehetővé teszik, hogy a közzétevő konfigurációval módosítsa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A szabályzat-utasítások konstans szöveges értékekkel, szabályzatkifejezésekkel és nevessel elnevezett értékekkel is összeépítve építkezők.

*A nevesizált* értékek név/érték párok globális gyűjteményei minden API Management példányban. A gyűjteményben lévő elemek száma nincs korlátozva. A nevestűs értékek konstans sztringértékek és titkos kulcsok kezelésére használhatók az összes API-konfigurációban és szabályzatban. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Nevestű értékek a Azure Portal":::

## <a name="value-types"></a>Értéktípusok

|Típus  |Description  |
|---------|---------|
|Sima     |  Literális sztring vagy szabályzatkifejezés     |
|Titkos     |   Konstans sztring vagy szabályzatkifejezés, amelyet a API Management      |
|[Key Vault](#key-vault-secrets)     |  Az Azure Key Vaultban tárolt titkos kulcs azonosítója.      |

Az egyszerű értékek vagy titkos kulcsok tartalmazhatnak [szabályzatkifejezéseket.](./api-management-policy-expressions.md) A kifejezés például egy `@(DateTime.Now.ToString())` sztringet ad vissza, amely az aktuális dátumot és időt tartalmazza.

A megnevezett értékattribútumokkal kapcsolatos részletekért tekintse meg a API Management [REST API referenciáját.](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)

## <a name="key-vault-secrets"></a>Key Vault – titkos kulcsok

A titkos kulcsok értékei tárolhatók titkosított sztringként a API Management -ban (egyéni titkos kulcsok), vagy a titkos kulcsokra való hivatkozásokkal a [Azure Key Vault.](../key-vault/general/overview.md) 

A Key Vault titkos kulcsának használata azért ajánlott, mert segít a API Management javításában:

* A kulcstartókban tárolt titkos kulcsok újra felhasználhatók a szolgáltatások között
* Részletes hozzáférési [szabályzatok](../key-vault/general/security-overview.md#privileged-access) alkalmazhatók titkos kulcsokra
* A kulcstartóban frissített titkos kulcsok automatikusan le vannak forgatva a API Management. A kulcstartóban való frissítés után a rendszer 4 órán belül API Management egy elnevezett értéket a kulcstartóban. A titkos adatokat manuálisan is frissítheti a Azure Portal vagy a felügyeleti REST API.

### <a name="prerequisites-for-key-vault-integration"></a>A Key Vault-integráció előfeltételei

1. A kulcstartók létrehozási lépéseiért lásd: Rövid útmutató: Kulcstartó létrehozása [a Azure Portal.](../key-vault/general/quick-create-portal.md)
1. Rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás [engedélyezése a](api-management-howto-use-managed-service-identity.md) API Management példányban.
1. Rendeljen hozzá [egy kulcstartó-hozzáférési](../key-vault/general/assign-access-policy-portal.md) szabályzatot a felügyelt identitáshoz, amely engedélyekkel rendelkezik a titkos kulcsok lekért és listához a tárolóból. A szabályzat hozzáadása:
    1. A portálon keresse meg a kulcstartót.
    1. Válassza **a Beállítások > hozzáférési szabályzatok lehetőséget > +Hozzáférési szabályzat hozzáadása lehetőséget.**
    1. Válassza **a Titkos engedély,** majd a **Be- és lista** **lehetőséget.**
    1. A **Rendszerbiztonsági tag kiválasztása** mezőben válassza ki a felügyelt identitás erőforrásnevét. Ha rendszer által hozzárendelt identitást használ, a rendszerbiztonsági tag a API Management neve.
1. Titkos kulcs létrehozása vagy importálása a kulcstartóba. Lásd: Rövid útmutató: Titkos adat beállítása és lekérése [Azure Key Vault a Azure Portal.](../key-vault/secrets/quick-create-portal.md)

A Key Vault titkos kulcsának használatával adjon [hozzá](#add-or-edit-a-named-value)vagy szerkesszen egy nevestű értéket, és adja meg a **Kulcstartó típusát.** Válassza ki a titkos kulcsot a kulcstartóból.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Elnevezett érték hozzáadása vagy szerkesztése

### <a name="add-a-key-vault-secret"></a>Kulcstartó titkos kulcsának hozzáadása

Lásd: [A Key Vault-integráció előfeltételei.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> Amikor key vault titkos adatokat használ a API Management ügyeljen arra, hogy ne törölje a kulcstartó eléréséhez használt titkos kulcsot, kulcstartót vagy felügyelt identitást.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a API Management példányát.
1. Az **API-k alatt** válassza **a Megnevezett értékek**  >  **+Hozzáadás lehetőséget.**
1. Adjon meg **egy Névazonosítót,** és adjon meg egy **Megjelenítendő** nevet, amely a tulajdonságra hivatkozik a szabályzatok között.
1. Az **Érték mezőbe írja be a** következőt: , válassza a Key Vault **lehetőséget.**
1. Adja meg a Key Vault titkos kulcsának  azonosítóját (verzió nélkül), vagy válassza a Kijelölés lehetőséget a titkos kulcs kulcstartóból való kiválasztásához.
    > [!IMPORTANT]
    > Ha saját maga adja meg a key vault titkos azonosítóját, győződjön meg arról, hogy az nem rendelkezik verzióinformációkkal. Ellenkező esetben a titkos kulcs nem lesz automatikusan elforgatva a API Management a kulcstartó frissítése után.
1. Az **Ügyfélidentitás** beállításban válasszon ki egy rendszer által hozzárendelt vagy egy meglévő, felhasználó által hozzárendelt felügyelt identitást. Megtudhatja, hogyan adhat hozzá vagy módosíthat felügyelt [identitásokat a API Management szolgáltatásban.](api-management-howto-use-managed-service-identity.md)
    > [!NOTE]
    > Az identitásnak engedélyekre van szüksége a titkos kulcsok kulcstartóból való lekért és listához. Ha még nem konfigurálta a kulcstartóhoz való hozzáférést, a API Management kéri, hogy automatikusan konfigurálni tudja az identitást a szükséges engedélyekkel.
1. Adjon hozzá egy vagy több választható címkét a nevesult értékek rendszerezésének segítése, majd a **Mentés gombra.**
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Key Vault titkos kulcs értékének hozzáadása":::

### <a name="add-a-plain-or-secret-value"></a>Egyszerű vagy titkos érték hozzáadása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)nyissa meg a API Management példányát.
1. Az **API-k alatt** válassza a **Megnevezett értékek**+ Hozzáadás  >  **lehetőséget.**
1. Adjon meg **egy névazonosítót,** és adjon meg egy **Megjelenítendő** nevet a szabályzatok tulajdonságára való hivatkozáshoz.
1. Az **Érték mezőbe írja be a** következőt: , válassza a Plain **(Egyszerű) vagy** a Secret **(Titkos) lehetőséget.**
1. Az **Érték mezőben** adjon meg egy sztringet vagy egy szabályzatkifejezést.
1. Adjon hozzá egy vagy több választható címkét a nevesult értékek rendszerezésének segítése, majd a **Mentés gombra.**
1. Válassza a **Létrehozás** lehetőséget.

A névvel létrehozott érték a név kiválasztásával szerkeszthető. Ha módosítja a megjelenített nevet, a rendszer automatikusan frissíti a megnevezett értékre hivatkozó szabályzatokat, hogy az új megjelenítendő nevet használják.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdése:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Elnevezett érték hozzáadásához használja az [az apim nv create](/cli/azure/apim/nv#az_apim_nv_create) parancsot:

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Miután létrehozott egy elnevezett értéket, az [az apim nv update](/cli/azure/apim/nv#az_apim_nv_update) paranccsal frissítheti. Az összes elnevezett értékhez futtassa az [az apim nv list parancsot:](/cli/azure/apim/nv#az_apim_nv_list)

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Az ebben a példában létrehozott megnevezett érték részleteinek megtekintése az [az apim nv show paranccsal:](/cli/azure/apim/nv#az_apim_nv_show)

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Ez a példa egy titkos érték. Az előző parancs nem ad vissza értéket. Az értékeket az [az apim nv show-secret paranccsal láthatja:](/cli/azure/apim/nv#az_apim_nv_show_secret)

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Elnevezett érték törléséhez használja az [az apim nv delete](/cli/azure/apim/nv#az_apim_nv_delete) parancsot:

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Elnevezett érték használata

Az ebben a szakaszban található példák az alábbi táblázatban látható elnevezett értékeket használják.

| Name               | Érték                      | Titkos | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Hamis  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Igaz   | 
| ExpressionProperty (Kifejezéstulajdonság) | `@(DateTime.Now.ToString())` | Hamis  | 

Ha elnevezett értéket szeretne használni egy szabályzatban, a megjelenítendő nevét helyezze el egy kapcsos zárójelpárban (például : ) az alábbi `{{ContosoHeader}}` példában látható módon:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában a egy szabályzat fejlécének neve, és a fejléc `ContosoHeader` `set-header` `ContosoHeaderValue` értékeként lesz használva. Ha a házirend kiértékelése egy kérés vagy válasz során történik a API Management átjáróra, és a rendszer lecseréli őket `{{ContosoHeader}}` `{{ContosoHeaderValue}}` a megfelelő értékekre.

A nevesult értékek teljes attribútum- vagy elemértékekként használhatók az előző példában látható módon, de beszúrhatók egy literális szöveges kifejezésbe, vagy kombinálhatók egy literális szövegkifejezés egy részével, ahogyan az alábbi példában látható: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

A nevestűdő értékek szabályzatkifejezéseket is tartalmazhatnak. A következő példában a `ExpressionProperty` kifejezést használjuk.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

A szabályzat kiértékelése után a helyére a értékét `{{ExpressionProperty}}` () kell `@(DateTime.Now.ToString())` cserélni. Mivel az érték egy szabályzatkifejezés, a rendszer kiértékeli a kifejezést, és a szabályzat végrehajtása folytatódik.

Ezt tesztelheti a Azure Portal fejlesztői [portálon](api-management-howto-developer-portal.md) egy olyan művelet hívásával, amely hatókörbe tartozó nevestűs szabályzatot tartalmaz. A következő példában egy műveletet hívunk meg, amely a két korábbi példa-szabályzatot tartalmazza `set-header` elnevezett értékekkel. Figyelje meg, hogy a válasz két egyéni fejlécet tartalmaz, amelyek nevestűs szabályzatokkal vannak konfigurálva.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API-válasz tesztelése":::

Ha megnézi egy [](api-management-howto-api-inspector.md) olyan hívás kimenő API-nyomkövetését, amely tartalmazza a két korábbi, nevesedt értékű minta-szabályzatot, láthatja a két szabályzatot a beszúrt nevestű értékekkel, valamint a szabályzatkifejezést tartalmazó elnevezett érték szabályzatkifejezés-kiértékelését. `set-header`

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API Inspector-nyomkövetés":::

> [!CAUTION]
> Ha egy szabályzat titkos kulcsra hivatkozik a Azure Key Vault-ben, a kulcstartó értéke látható lesz az API-kérelmek nyomkövetéséhez engedélyezett előfizetésekkel rendelkező [felhasználók számára.](api-management-howto-api-inspector.md)

Bár a megnevezett értékek tartalmazhatnak szabályzatkifejezéseket, más nevestű értékeket nem. Ha egy névvel elnevezett értékhivatkozást tartalmazó szöveget használ egy értékhez, például az értékhez, akkor a hivatkozás nem lesz feloldva `Text: {{MyProperty}}` és lecserélve.

## <a name="delete-a-named-value"></a>Elnevezett érték törlése

Elnevezett érték törléséhez válassza ki  a nevet, majd válassza a Törlés lehetőséget a helyi **menüben**( ... ).

> [!IMPORTANT]
> Ha a megnevezett értékre bármely szabályzat hivatkozik API Management, addig nem törölheti, amíg el nem távolítja a megnevezett értéket az azt felhasználó összes szabályzatból.

## <a name="next-steps"></a>Következő lépések

-   További információ a szabályzatokkal való munkavégzésről
    -   [Szabályzatok a API Management](api-management-howto-policies.md)
    -   [Házirend-referencia](./api-management-policies.md)
    -   [Házirend-kifejezések](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png


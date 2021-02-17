---
title: Nevesített értékek használata az Azure API Management-házirendekben
description: Megtudhatja, hogyan használhatja a nevesített értékeket az Azure API Management-házirendekben. A nevesített értékek tartalmazhatnak literál karakterláncokat, házirend-kifejezéseket és Azure Key Vault tárolt titkos kulcsokat.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 2bc9b1c5724fa7bab1fdf5ac9332d87ba03a6d11
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545819"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Nevesített értékek használata az Azure API Management-házirendekben

[API Management a házirendek](api-management-howto-policies.md) a rendszer hatékony funkciója, amely lehetővé teszi a közzétevő számára, hogy konfiguráción változtassa meg az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A házirend-utasítások literális szöveges értékekkel, házirend-kifejezésekkel és elnevezett értékekkel állíthatók össze.

A *nevesített értékek* a név/érték párok globális gyűjteménye minden API Management-példányban. A gyűjtemény elemeinek száma nincs korlátozva. A nevesített értékek használatával állandó karakterlánc-értékeket és titkos kulcsokat kezelhet az összes API-konfiguráció és-szabályzat között. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Megnevezett értékek a Azure Portal":::

## <a name="value-types"></a>Értékek típusai

|Típus  |Leírás  |
|---------|---------|
|Egyszerű     |  Literális karakterlánc vagy házirend kifejezése     |
|Titkos     |   A API Management által titkosított literális karakterlánc vagy házirend-kifejezés      |
|[Key Vault](#key-vault-secrets)     |  Egy Azure Key vaultban tárolt titok azonosítója.      |

Az egyszerű értékek vagy titkos kódok tartalmazhatnak [házirend-kifejezéseket](./api-management-policy-expressions.md). A kifejezés például `@(DateTime.Now.ToString())` egy karakterláncot ad vissza, amely az aktuális dátumot és időt tartalmazza.

Az elnevezett érték attribútumaival kapcsolatos részletekért tekintse meg a API Management [REST API-referenciát](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate).

## <a name="key-vault-secrets"></a>Key Vault-titkok

A titkos értékek tárolhatók titkosított karakterláncként API Managementban (egyéni titok), vagy [Azure Key Vault](../key-vault/general/overview.md)a titkos kulcsokra hivatkozva. 

A Key Vault-titkok használata ajánlott, mert segít javítani a API Management biztonságot:

* A Key vaultokban tárolt titkos kódok újra felhasználhatók a szolgáltatások között
* A részletes [hozzáférési szabályzatok](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) a titkokra alkalmazhatók
* A Key vaultban frissített titkokat API Management automatikusan elforgatja a rendszer. A Key vaultban történt frissítés után a rendszer 4 órán belül frissíti API Managementban megnevezett értéket. Manuálisan is frissítheti a titkot a Azure Portal vagy a felügyeleti REST API használatával.

### <a name="prerequisites-for-key-vault-integration"></a>A Key Vault-integráció előfeltételei

1. A kulcstartó létrehozásának lépéseiért lásd: gyors útmutató [: kulcstartó létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md).
1. A rendszer által hozzárendelt vagy felhasználó által hozzárendelt [felügyelt identitás](api-management-howto-use-managed-service-identity.md) engedélyezése a API Management-példányban.
1. Rendeljen hozzá egy [Key Vault-hozzáférési szabályzatot](../key-vault/general/assign-access-policy-portal.md) a felügyelt identitáshoz, amely engedélyekkel rendelkezik a titkos kódok lekéréséhez és listázásához a tárból. A szabályzat hozzáadása:
    1. A portálon navigáljon a kulcstartóhoz.
    1. Válassza a **beállítások > hozzáférési szabályzatok > + hozzáférési házirend hozzáadása** lehetőséget.
    1. Válassza a **titkos engedélyek**, majd a **beolvasás** és a **lista** lehetőséget.
    1. A **rendszerbiztonsági tag kiválasztása** területen válassza ki a felügyelt identitás erőforrásának nevét. Ha rendszer által hozzárendelt identitást használ, a rendszerbiztonsági tag a API Management példányának neve.
1. Hozzon létre vagy importáljon egy titkos kulcsot a kulcstartóba. Lásd [: gyors útmutató: Azure Key Vault titkos kód beállítása és beolvasása a Azure Portal használatával](../key-vault/secrets/quick-create-portal.md).

A Key Vault-titok használatához [adjon hozzá vagy szerkesszen egy megnevezett értéket](#add-or-edit-a-named-value), és adja meg a **Key Vault** típusát. Válassza ki a titkos kulcsot a Key vaultból.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Megnevezett érték hozzáadása vagy szerkesztése

### <a name="add-a-key-vault-secret"></a>Key Vault-titok hozzáadása

Lásd: [a Key Vault-integráció előfeltételei](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Ha API Management Key Vault-titkot használ, ügyeljen arra, hogy ne törölje a Key Vault eléréséhez használt titkos kulcsot, kulcstartót vagy felügyelt identitást.

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Az **API**-k területen válassza a **nevesített értékek**  >  **+ Hozzáadás** lehetőséget.
1. Adja meg a **név** azonosítóját, és adjon meg egy **megjelenítendő nevet** , amely a tulajdonságra hivatkozik a szabályzatokban.
1. Az **érték típusa mezőben** válassza a **Key Vault** elemet.
1. Adja meg egy Key Vault-titok azonosítóját (verzió nélkül), vagy válassza a **kijelölés** lehetőséget egy kulcstartó titkos kódjának kiválasztásához.
    > [!IMPORTANT]
    > Ha saját maga adja meg a Key Vault titkos azonosítóját, győződjön meg róla, hogy nem rendelkezik a verzióval kapcsolatos információkkal. Ellenkező esetben a titkos kulcs nem forog automatikusan API Management a Key Vault frissítését követően.
1. Az **ügyfél identitása** területen válasszon ki egy rendszer által hozzárendelt vagy egy meglévő felhasználó által hozzárendelt felügyelt identitást. Ismerje meg, hogyan [adhat hozzá vagy módosíthat felügyelt identitásokat a API Management szolgáltatásban](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Az identitásnak jogosultsággal kell rendelkeznie a Key vaultban található titkos kódok beolvasásához és listázásához. Ha még nem konfigurálta a Key vaulthoz való hozzáférést, API Management felszólítja, hogy automatikusan konfigurálja az identitást a szükséges engedélyekkel.
1. Adjon hozzá egy vagy több opcionális címkét a megnevezett értékek rendszerezéséhez, majd **mentse** a következőt:.
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Key Vault titkos értékének hozzáadása":::

### <a name="add-a-plain-or-secret-value"></a>Egyszerű vagy titkos érték hozzáadása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Az **API**-k területen válassza a **nevesített értékek**  >  **+ Hozzáadás** lehetőséget.
1. Adja meg a **név** azonosítóját, és adjon meg egy **megjelenítendő nevet** , amely a tulajdonságra hivatkozik a szabályzatokban.
1. Az **érték típusa** mezőben válassza az **egyszerű** vagy a **titkos** lehetőséget.
1. Az **érték** mezőben adjon meg egy karakterlánc-vagy házirend-kifejezést.
1. Adjon hozzá egy vagy több opcionális címkét a megnevezett értékek rendszerezéséhez, majd **mentse** a következőt:.
1. Válassza a **Létrehozás** lehetőséget.

A megnevezett érték létrehozása után a név kiválasztásával szerkesztheti. Ha megváltoztatja a megjelenítendő nevet, a rendszer automatikusan frissíti az adott nevesített értékre hivatkozó házirendeket az új megjelenítendő név használatára.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdéséhez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Megnevezett érték hozzáadásához használja az az [APIM NV Create](/cli/azure/apim/nv#az_apim_nv_create) parancsot:

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Megnevezett érték létrehozása után az az [APIM NV Update](/cli/azure/apim/nv#az_apim_nv_update) paranccsal frissítheti azt. Az összes elnevezett érték megjelenítéséhez futtassa az az [APIM NV List](/cli/azure/apim/nv#az_apim_nv_list) parancsot:

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Az ehhez a példához létrehozott megnevezett érték részleteinek megtekintéséhez futtassa az az [APIM NV show](/cli/azure/apim/nv#az_apim_nv_show) parancsot:

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Ez a példa egy titkos érték. Az előző parancs nem ad vissza értéket. Az érték megjelenítéséhez futtassa az az [APIM NV show-Secret](/cli/azure/apim/nv#az_apim_nv_show_secret) parancsot:

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Megnevezett érték törléséhez használja az az [APIM NV delete](/cli/azure/apim/nv#az_apim_nv_delete) parancsot:

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Névvel ellátott érték használata

Az ebben a szakaszban szereplő példák az alábbi táblázatban látható megnevezett értékeket használják.

| Name               | Érték                      | Titkos | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Hamis  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Igaz   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Hamis  | 

Ha megnevezett értéket szeretne használni egy házirendben, a megjelenítendő nevet a kapcsos zárójelek közé helyezheti, `{{ContosoHeader}}` ahogy az a következő példában látható:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Ebben a példában `ContosoHeader` a rendszer a szabályzat fejlécének neveként használja `set-header` , és `ContosoHeaderValue` a fejléc értékeként használja. Ha ezt a házirendet az API Management-átjáróra vonatkozó kérelem vagy válasz alapján értékeli ki, a `{{ContosoHeader}}` `{{ContosoHeaderValue}}` rendszer a megfelelő értékekkel helyettesíti a szabályzatot.

Az elnevezett értékek teljes attribútumként vagy elemként használhatók, ahogy az az előző példában is látható, de az alábbi példában látható módon egy literális kifejezés egy részébe is beilleszthető vagy kombinálható: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

A nevesített értékek házirend-kifejezéseket is tartalmazhatnak. A következő példában a `ExpressionProperty` kifejezés van használatban.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Ha kiértékelik ezt a házirendet, a helyére az értékét adja meg `{{ExpressionProperty}}` `@(DateTime.Now.ToString())` . Mivel az érték egy házirend-kifejezés, a rendszer kiértékeli a kifejezést, és a szabályzat végrehajtásával folytatja.

Ezt a Azure Portal vagy a [fejlesztői portálon](api-management-howto-developer-portal.md) tesztelheti úgy, hogy olyan műveletet hív meg, amelynek a hatókörében megnevezett értékekkel rendelkező szabályzat található. A következő példában egy műveletet kell meghívni a két korábbi `set-header` , nevesített értékekkel rendelkező házirenddel. Figyelje meg, hogy a válasz két olyan egyéni fejlécet tartalmaz, amelyek nevesített értékekkel rendelkező házirendek használatával lettek konfigurálva.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API-válasz tesztelése":::

Ha megtekinti a kimenő [API-nyomkövetést](api-management-howto-api-inspector.md) egy olyan híváshoz, amely tartalmazza a két korábbi, nevesített értékkel rendelkező minta szabályzatot, akkor a két, `set-header` beszúrt értékkel rendelkező szabályzatot, valamint a házirend kifejezés kiértékelését a házirend kifejezését tartalmazó megnevezett értékre.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API Inspector nyomkövetés":::

> [!CAUTION]
> Ha egy házirend Azure Key Vault titkos kulcsra hivatkozik, akkor a kulcstartóban lévő érték látható lesz azoknak a felhasználóknak, akik hozzáférnek az [API-kérelmek nyomkövetéséhez](api-management-howto-api-inspector.md)engedélyezett előfizetésekhez.

Míg a nevesített értékek tartalmazhatnak házirend-kifejezéseket, nem tartalmazhatnak más nevesített értékeket. Ha egy megnevezett értékű hivatkozást tartalmazó szöveget használ egy értékhez, például a `Text: {{MyProperty}}` hivatkozást, a hivatkozás nem lesz feloldva és lecserélve.

## <a name="delete-a-named-value"></a>Megnevezett érték törlése

Megnevezett érték törléséhez válassza ki a nevet, majd válassza a helyi menü **Törlés** elemét (**...**).

> [!IMPORTANT]
> Ha az elnevezett érték bármely API Management házirendre hivatkozik, akkor nem törölheti, amíg el nem távolítja a megnevezett értéket az összes olyan házirendből, amelyik azt használja.

## <a name="next-steps"></a>Következő lépések

-   További információ a szabályzatok használatáról
    -   [Szabályzatok API Management](api-management-howto-policies.md)
    -   [Házirend-referencia](./api-management-policies.md)
    -   [Házirend-kifejezések](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png


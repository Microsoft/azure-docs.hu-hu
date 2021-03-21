---
title: A házirend-definíciós struktúra részletei
description: Leírja, hogyan használhatók a szabályzat-definíciók a szervezeten belüli Azure-erőforrásokra vonatkozó konvenciók létrehozásához.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: cebba214671cfab75a3f44720578b51febacdfcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215068"
---
# <a name="azure-policy-definition-structure"></a>Azure szabályzatdefiníciók struktúrája

Azure Policy az erőforrásokra vonatkozó konvenciókat hoz létre. A szabályzat-definíciók írják le az erőforrás-megfelelőségi [feltételeket](#conditions) , valamint azt, hogy egy adott feltétel teljesül-e. A feltétel összehasonlítja az erőforrás-tulajdonság [mezőjét](#fields) vagy egy [értéket](#value) egy szükséges értékkel. Az erőforrás-tulajdonságok mezői [aliasok](#aliases)használatával érhetők el. Ha egy erőforrás-tulajdonság egy tömb, a rendszer egy speciális [tömb aliast](#understanding-the--alias) használ az összes tömb összes tagjából származó értékek kiválasztására, és az egyes feltételek alkalmazására. További információ a [feltételekről](#conditions).

Az egyezmények meghatározásával szabályozhatja a költségeket, és könnyebben kezelheti az erőforrásokat. Megadhatja például, hogy csak bizonyos típusú virtuális gépek engedélyezettek legyenek. Azt is megkövetelheti, hogy az erőforrások egy adott címkével rendelkezzenek. A házirend-hozzárendeléseket a gyermek erőforrások öröklik. Ha a szabályzat-hozzárendelést egy erőforráscsoporthoz alkalmazza, az az adott erőforráscsoport összes erőforrására érvényes lesz.

A szabályzat-definíció _' policyrule osztály_ sémája itt található: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

A JSON használatával hozhat létre szabályzat-definíciót. A házirend-definíció a következő elemeit tartalmazza:

- megjelenítendő név
- leírás
- mód
- metaadatok
- parameters
- házirend-szabály
  - logikai Értékelés
  - érvénybe

A következő JSON például egy olyan szabályzatot mutat be, amely korlátozza az erőforrások központi telepítését:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Azure Policy a beépített és a minták a [Azure Policy mintákban](../samples/index.md)találhatók.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **DisplayName** és a **Leírás** használatával azonosíthatja a házirend-definíciót, és megadhatja a környezetét a használathoz. a **DisplayName** maximális hossza _128_ karakter, és a **Leírás** legfeljebb _512_ karakter hosszúságú lehet.

> [!NOTE]
> A házirend-definíció létrehozása vagy frissítése során a JSON-on kívüli tulajdonságok definiálják az **azonosítót**, a **típust** és a **nevet** , és nem szükségesek a JSON-fájlban. Az SDK-n keresztüli szabályzat-definíció beolvasása az **azonosító**, a **típus** és a **név** tulajdonságokat adja vissza a JSON részeként, de mindegyik írásvédett információ a házirend-definícióhoz kapcsolódik.

## <a name="type"></a>Típus

Amíg a **Type (típus** ) tulajdonság nem állítható be, az SDK által visszaadott három érték jelenik meg a portálon:

- `Builtin`: Ezeket a szabályzat-definíciókat a Microsoft biztosította és tartja karban.
- `Custom`: Az ügyfelek által létrehozott összes házirend-definíció rendelkezik ezzel az értékkel.
- `Static`: A Microsoft **tulajdonában** lévő [szabályozási megfelelőségi](./regulatory-compliance.md) szabályzat definícióját jelzi. Ezeknek a szabályzat-definícióknak a megfelelőségi eredményei a Microsoft-infrastruktúrával kapcsolatos harmadik féltől származó ellenőrzések eredményei. A Azure Portalban ez az érték időnként **Microsoft által felügyelt** jelenik meg. További információ: [megosztott felelősség a felhőben](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Mód

A **mód** attól függően van konfigurálva, hogy a házirend Azure Resource Manager tulajdonságot vagy erőforrás-szolgáltatói tulajdonságot céloz meg.

### <a name="resource-manager-modes"></a>Resource Manager-módok

A **mód** határozza meg, hogy mely erőforrástípusok legyenek kiértékelve egy házirend-definícióhoz. A támogatott módok a következők:

- `all`: erőforráscsoportok, előfizetések és minden erőforrástípus kiértékelése
- `indexed`: csak a címkéket és helyet támogató erőforrástípusok kiértékelése

Például az erőforrás `Microsoft.Network/routeTables` támogatja a címkéket és a helyet, és mindkét módban kiértékelésre kerül. Az erőforrás azonban `Microsoft.Network/routeTables/routes` nem címkézhető, és nincs kiértékelve a `Indexed` módban.

Javasoljuk, hogy a legtöbb esetben állítsa be a **módot** `all` . A portálon keresztül létrehozott összes házirend-definíció a `all` módot használja. Ha a PowerShellt vagy az Azure CLI-t használja, manuálisan is megadhatja a **Mode** paramétert. Ha a házirend-definíció nem tartalmaz **Mode** értéket, az alapértelmezett értéke `all` Azure PowerShell és az `null` Azure CLI-ben. A `null` `indexed` visszafelé való kompatibilitás támogatásához egy mód ugyanaz, mint a használatával.

`indexed` a címkéket vagy helyszíneket kényszerítő házirendek létrehozásakor kell használni. Habár nem kötelező, megakadályozza, hogy a címkék és a hely nem támogatja az olyan erőforrásokat, amelyek nem felelnek meg a megfelelőségi eredményeknek. A kivétel az **erőforráscsoportok** és az **előfizetések**. Az erőforráscsoport vagy előfizetés helyét vagy címkéit kényszerítő szabályzat-definíciók **módot** kell beállítani a (z `all` ) és a (z) és a (z `Microsoft.Resources/subscriptions/resourceGroups` `Microsoft.Resources/subscriptions` ) típusra. Példaként tekintse meg a [minta: címkék – minta #1](../samples/pattern-tags.md). A címkéket támogató erőforrások listáját lásd: az Azure- [erőforrások támogatásának címkézése](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes"></a>Erőforrás-szolgáltatói módok

A következő erőforrás-szolgáltatói mód teljes mértékben támogatott:

- `Microsoft.Kubernetes.Data` Az Azure-beli Kubernetes-fürtök kezeléséhez. Az erőforrás-szolgáltatói üzemmódot használó definíciók a következő hatásokat használják: _naplózás_, _Megtagadás_ és _Letiltva_. A [EnforceOPAConstraint](./effects.md#enforceopaconstraint) -effektus használata _elavult_.

A következő erőforrás-szolgáltatói módok jelenleg **előzetes** verzióként támogatottak:

- `Microsoft.ContainerService.Data` a belépésvezérlés szabályainak kezeléséhez az [Azure Kubernetes szolgáltatásban](../../../aks/intro-kubernetes.md). Az ezt az erőforrás-szolgáltatói módot használó definícióknak a [EnforceRegoPolicy](./effects.md#enforceregopolicy) effektust **kell** használniuk. Ez a mód _elavult_.
- `Microsoft.KeyVault.Data` a [Azure Key Vault](../../../key-vault/general/overview.md)tárolók és tanúsítványok kezeléséhez. A szabályzat-definíciókkal kapcsolatos további információkért lásd: [a Azure Key Vault integrálása Azure Policyokkal](../../../key-vault/general/azure-policy.md).

> [!NOTE]
> Az erőforrás-szolgáltatói módok csak a beépített szabályzat-definíciókat támogatják, és nem támogatják a [kivételeket](./exemption-structure.md).

## <a name="metadata"></a>Metaadatok

A választható `metadata` tulajdonság a házirend-definícióval kapcsolatos adatokat tárolja. Az ügyfelek a szervezete számára hasznos tulajdonságokat és értékeket adhatnak meg `metadata` . Vannak azonban olyan _általános_ tulajdonságok, amelyeket a Azure Policy és a beépített modulok használnak. Minden `metadata` tulajdonság 1024 karakterből állhat.

### <a name="common-metadata-properties"></a>Gyakori metaadatok tulajdonságai

- `version` (karakterlánc): nyomon követi a szabályzat-definíció tartalmának verziószámát.
- `category` (karakterlánc): meghatározza, hogy a házirend-definíció milyen kategóriában jelenjen meg Azure Portal.
- `preview` (Boolean): igaz vagy hamis jelző, ha a házirend-definíció _előzetes_ verzió.
- `deprecated` (Boolean): igaz vagy hamis jelző, ha a házirend-definíció _elavultként_ van megjelölve.

> [!NOTE]
> A Azure Policy szolgáltatás a, a `version` `preview` és a tulajdonságot használja a `deprecated` beépített szabályzat-definícióra vagy kezdeményezésre és állapotra való váltáshoz. A formátuma `version` : `{Major}.{Minor}.{Patch}` . Bizonyos állapotokat (például az _elavult_ vagy az _előnézet_) a `version` tulajdonsághoz vagy egy másik tulajdonsághoz ( **Boolean**) kell hozzáfűzni. További információ a Azure Policy-verziókról: [beépített verziószámozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Paraméterek

A paraméterek segítségével egyszerűsítheti a házirendek kezelését a házirend-definíciók számának csökkentésével. Gondoljon olyan paraméterekre, mint például az űrlap mezői – `name` , `address` , `city` , `state` . Ezek a paraméterek mindig azonosak maradnak, de az értékek az űrlap kitöltése alapján változnak.
A paraméterek ugyanúgy működnek, mint a házirendek létrehozásakor. A házirend-definícióban szereplő paraméterekkel együtt más értékekkel is felhasználhatja a szabályzatot különböző forgatókönyvekhez.

> [!NOTE]
> A paraméterek hozzáadhatók meglévő és hozzárendelt definícióhoz is. Az új paraméternek tartalmaznia kell a **defaultValue** tulajdonságot. Ez megakadályozza, hogy a házirend vagy kezdeményezés meglévő hozzárendelései érvénytelenek legyenek.

### <a name="parameter-properties"></a>Paraméter tulajdonságai

A paraméter a következő tulajdonságokkal rendelkezik, amelyek a szabályzat-definícióban használatosak:

- `name`: A paraméter neve. A házirend- `parameters` szabályon belüli központi telepítési függvény használja. További információ: [paraméter értékének használata](#using-a-parameter-value).
- `type`: Meghatározza, hogy a paraméter **karakterlánc**, **tömb**, **objektum**, **logikai**, **egész**, **lebegőpontos** vagy **datetime**.
- `metadata`: Meghatározza a Azure Portal által elsődlegesen használt altulajdonságokat a felhasználóbarát információk megjelenítéséhez:
  - `description`: Annak magyarázata, hogy mit használ a paraméter. A használható az elfogadható értékek példáinak megadására.
  - `displayName`: A (z) paraméterben a portálon megjelenő rövid név.
  - `strongType`: (Nem kötelező) a szabályzat definíciójának a portálon való hozzárendeléséhez használatos. Környezetfüggő listát biztosít. További információ: [strongType](#strongtype).
  - `assignPermissions`: (Nem kötelező) állítsa _igaz_ értékre, hogy Azure Portal hozzon létre szerepkör-hozzárendeléseket a házirend-hozzárendelés során. Ez a tulajdonság akkor hasznos, ha az engedélyeket a hozzárendelési hatókörön kívül szeretné hozzárendelni. Szerepkör-definícióban egy szerepkör-hozzárendelés van a házirendben (vagy a szerepkör-definícióban a kezdeményezés összes házirendje esetében). A paraméter értékének érvényes erőforrásnak vagy hatókörnek kell lennie.
- `defaultValue`: (Nem kötelező) megadja a paraméter értékét egy hozzárendelésben, ha nincs megadva érték.
  Egy meglévő, hozzárendelt szabályzat-definíció frissítésekor szükséges.
- `allowedValues`: (Nem kötelező) az értékek egy tömbjét adja meg, amelyet a paraméter elfogad a hozzárendelés során. Az engedélyezett érték-összehasonlítások kis-és nagybetűk megkülönböztetését teszik lehetővé. 

Például meghatározhat egy házirend-definíciót, amely korlátozza az erőforrások üzembe helyezésének helyét. A házirend-definíció paraméterét **allowedLocations** lehet. Ezt a paramétert a házirend-definíció egyes hozzárendelései használják az elfogadott értékek korlátozására. A **strongType** használata fokozott élményt nyújt a hozzárendelésnek a portálon keresztül történő elvégzése során:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Paraméter értékének használata

A házirend-szabályban a paramétereket a következő `parameters` függvény szintaxisával hivatkozhat:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ez a példa a **allowedLocations** paraméterre hivatkozik, amely a [paraméter tulajdonságainál](#parameter-properties)látható.

### <a name="strongtype"></a>strongType

A `metadata` tulajdonságon belül a **strongType** használatával több választási lehetőséget is megadhat a Azure Portalon belül. a **strongType** lehet egy támogatott _erőforrástípus_ vagy egy megengedett érték. Annak megállapításához, hogy az _erőforrástípus_ érvényes-e a **strongType**, használja a [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). A **strongType** _erőforrástípus_ formátuma `<Resource Provider>/<Resource Type>` . Például: `Microsoft.Network/virtualNetworks/subnets`.

Bizonyos, a **Get-AzResourceProvider** által nem visszaadott _erőforrástípusok_ támogatottak. Ezek a típusok a következők:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

A **strongType** által engedélyezett nem _erőforrástípus_ -érték a következő:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definíció helye

Kezdeményezés vagy szabályzat létrehozásakor meg kell adnia a definíció helyét. A definíció helyének felügyeleti csoportnak vagy előfizetésnek kell lennie. Ez a hely határozza meg azt a hatókört, amelyhez a kezdeményezést vagy házirendet hozzá lehet rendelni. Az erőforrásoknak a definíció helyének hierarchiájában kell közvetlen tagoknak vagy gyermekeknek lenniük a hozzárendelés céljára.

Ha a definíció helye:

- Az előfizetéshez tartozó csak **előfizetéshez** tartozó erőforrások rendelhetők hozzá a szabályzat-definícióhoz.
- **Felügyeleti csoport** – csak a gyermek-felügyeleti csoportokon belüli erőforrások és a gyermek előfizetések rendelhetők hozzá a szabályzat-definícióhoz. Ha azt tervezi, hogy a házirend-definíciót több előfizetésre is alkalmazza, akkor a helynek minden előfizetést tartalmazó felügyeleti csoportnak kell lennie.

További információ: [a hatókör megismerése Azure Policy](./scope.md#definition-location).

## <a name="policy-rule"></a>Házirend-szabály

A **házirend-szabály** az **IF** és a blokkból áll. Az **IF** blokkban meg kell adnia egy vagy több olyan feltételt, amely megadja, hogy a rendszer mikor kényszerítse ki a házirendet. Ezekhez a feltételekhez logikai operátorokat alkalmazhat, így pontosan meghatározhatja a házirend forgatókönyvét.

Az **Ezután** blokkban definiálhatja azt a hatást, amely az **IF** feltételek teljesülése esetén történik.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logikai operátorok

A támogatott logikai operátorok a következők:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

A **nem** szintaxis invertálja a feltétel eredményét. A **allOf** szintaxisa (a logikai **és** a művelethez hasonlóan) minden feltételnek igaznak kell lennie. A **anyOf** szintaxisa (a logikai **vagy** a művelethez hasonlóan) egy vagy több feltétel teljesülése szükséges.

A logikai operátorok beágyazására is lehetőség van. A következő példa egy **nem** műveletet mutat be, amely egy **allOf** -műveletbe van beágyazva.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Feltételek

Egy feltétel kiértékeli, hogy egy érték megfelel-e bizonyos feltételeknek. A támogatott feltételek a következők:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

**Kevesebb**, **lessOrEquals**, **nagyobb** és **greaterOrEquals** esetén, ha a tulajdonság típusa nem egyezik a feltétel típusával, a rendszer hibát jelez. A karakterlánc-összehasonlítások használata a használatával történik `InvariantCultureIgnoreCase` .

A **hasonló** és **notLike** feltételek használatakor helyettesítő karaktert kell megadni `*` az értékben. Az érték legfeljebb egy helyettesítő karakterből állhat `*` .

A **egyezési** és **notMatch** feltételek használatakor az adott `#` számjegyre, `?` betűre, `.` bármilyen karakterre és bármely más karakterre illeszkedik, amely megfelel a tényleges karakternek. Ha a **egyezés** és a **notMatch** is megkülönbözteti a kis-és nagybetűket, a _stringValue_ kiértékelésére szolgáló összes egyéb feltétel nem tesz különbséget Kis-és nagybetűket megkülönböztető alternatívák a **matchInsensitively** és a **notMatchInsensitively** szolgáltatásban érhetők el.

### <a name="fields"></a>Mezők

Azok a feltételek, amelyek kiértékelik, hogy az erőforrás-kérelemben szereplő tulajdonságok értékei megfelelnek-e bizonyos feltételeknek egy **mező** kifejezés használatával. A következő mezők támogatottak:

- `name`
- `fullName`
  - Az erőforrás teljes nevét adja vissza. Az erőforrás teljes neve az erőforrás neve előtagértéke (például "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - A hely mezői normalizálva vannak a különböző formátumok támogatásához. Például `East US 2` egyenlőnek számít `eastus2` .
  - **Globálisan** használhatja az olyan erőforrásokat, amelyek a helytől függetlenek.
- `id`
  - A kiértékelt erőforrás erőforrás-AZONOSÍTÓját adja vissza.
  - Például: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - Az erőforráson engedélyezett [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusát adja vissza.
- `tags`
- `tags['<tagName>']`
  - Ez a zárójel-szintaxis támogatja a írásjeleket, például kötőjelet, pontot vagy szóközt.
  - Ahol a a **\<tagName\>** címke neve, amely ellenőrzi a feltételt.
  - Példák: `tags['Acct.CostCenter']` az **acct. CostCenter** a címke neve.
- `tags['''<tagName>''']`
  - Ez a zárójel-szintaxis támogatja az aposztrófokat tartalmazó címkéket dupla aposztrófokkal.
  - Ahol a **" \<tagName\> "** annak a címkének a neve, amely a feltételt érvényesíti.
  - Példa: `tags['''My.Apostrophe.Tag''']` ahol a **"My. aposztróf. tag"** a címke neve.
- tulajdonság-aliasok – lista esetén lásd: [aliasok](#aliases).

> [!NOTE]
> `tags.<tagName>`a, `tags[tagName]` , és `tags[tag.with.dots]` továbbra is elfogadható módon deklarálhatja a címkék mezőt. Az előnyben részesített kifejezések azonban a fentiekben láthatók.

> [!NOTE]
> Az **\[ \* \] aliasra** hivatkozó **mezők** kifejezései a tömb minden elemét egyedileg értékelik ki a logikai **és** az elemek között. További információ: a [tömb erőforrás-tulajdonságainak hivatkozása](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="use-tags-with-parameters"></a>Címkék használata paraméterekkel

Egy paraméter értéke adható át egy címke mezőnek. Ha egy paramétert egy címke mezőre továbbít, a házirend-hozzárendelés során növeli a házirend-definíció rugalmasságát.

A következő példában a `concat` **TagName** paraméter értékének megadásához a címkék mezőhöz tartozó keresőmezőt kell létrehozni. Ha ez a címke nem létezik, a **módosítás** hatására a rendszer hozzáadja a címkét a naplózott erőforrások szülő erőforráscsoporthoz tartozó megnevezett címke értékével a `resourcegroup()` keresési függvénnyel.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f"
            ]
        }
    }
}
```

### <a name="value"></a>Érték

Azok a feltételek, amelyek kiértékelik, hogy egy érték megfelel-e bizonyos feltételeknek, egy **érték** kifejezés használatával hozható létre. Az értékek lehetnek literálok, a [Paraméterek](#parameters)értékei, illetve a [támogatott sablonok függvényei](#policy-functions)által visszaadott értékek.

> [!WARNING]
> Ha egy _sablon függvény_ eredménye hibát jelez, a szabályzat kiértékelése sikertelen lesz. A sikertelen értékelés implicit **Megtagadás**. További információ: a [sablon meghibásodásának elkerülése](#avoiding-template-failures). A [enforcementMode](./assignment-structure.md#enforcement-mode) használatával  megakadályozhatja az új vagy frissített erőforrások sikertelen értékelésének hatását az új házirend-definíció tesztelése és érvényesítése során.

#### <a name="value-examples"></a>Példák az értékekre

Ez a házirend-szabály például az **érték** használatával hasonlítja össze a függvény eredményét `resourceGroup()` és a visszaadott **Name** tulajdonságot a **hasonló** feltétellel `*netrg` . A szabály minden olyan erőforrást megtagad, `Microsoft.Network/*` amely **nem egy** olyan erőforráscsoport, amelynek a neve véget ér `*netrg` .

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Ez a házirend-szabály például az **érték** használatával ellenőrizze, hogy a több beágyazott függvény eredménye **egyenlő** -e `true` . A szabály minden olyan erőforrást megtagad, amely nem rendelkezik legalább három címkével.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Sablon meghibásodásának elkerülése

A _template functions_ in **Value** használata számos összetett beágyazott függvényt tesz lehetővé. Ha egy _sablon függvény_ eredménye hibát jelez, a szabályzat kiértékelése sikertelen lesz. A sikertelen értékelés implicit **Megtagadás**. Egy példa olyan **értékre** , amely bizonyos helyzetekben meghiúsul:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

A fenti példában az [alsztring ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) érték a **név** első három karakterének **ABC**-re való összevetését használja. Ha a **név** rövidebb, mint három karakter, a `substring()` függvény hibát eredményez. Ez a hiba azt eredményezi, hogy a házirend **megtagadási** hatást vált ki.

Ehelyett a [IF ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) függvény használatával ellenőrizze, hogy az első három **karakter egyenlő-e** az **ABC** -vel anélkül, hogy a **név** három karakternél rövidebb legyen, ami hibát okozhat:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

A módosított szabályzattal rendelkező szabály a `if()` **név** hosszát ellenőrzi, mielőtt egy olyan értéket próbál meg elérni, `substring()` amely kevesebb mint három karakterből áll. Ha a **név** túl rövid, a "nem az ABC-től kezdődően" értéket adja vissza, és az **ABC**-hez képest. Az **ABC** -vel nem kezdődő rövid névvel rendelkező erőforrás továbbra is meghiúsul a házirend-szabályban, de az értékelés során már nem okoz hibát.

### <a name="count"></a>Darabszám

Azok a feltételek, amelyek megszámolják, hogy egy tömb hány tagja felel meg bizonyos feltételeknek, egy **Count** kifejezés használatával hozható létre. A gyakori forgatókönyvek azt ellenőrzik, hogy a tömb tagjainak legalább az egyike, pontosan az egyik, az "összes" vagy a "None" érték teljesül-e feltételnek. A **Count** az egyes tömb tagjait kiértékeli egy feltétel kifejezéséhez, és a _valódi_ eredményeket összegzi, amely a kifejezés operátorával összehasonlítva történik.

#### <a name="field-count"></a>Mezők száma

Megszámolja, hogy egy tömb hány tagja a kérelem adattartalmát kielégítse egy feltétel kifejezésének. A **mezők száma** kifejezések szerkezete a következő:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

A **mezők száma** a következő tulajdonságokkal használható:

- **Count. Field** (kötelező): a tömb elérési útját tartalmazza, és tömb aliasnak kell lennie.
- **Count** (nem kötelező): a feltétel kifejezése az egyes [ \[ \* \] aliasok](#understanding-the--alias) tömbje egyes tagjainak egyenkénti kiértékeléséhez `count.field` . Ha ez a tulajdonság nincs megadva, a "Field" elérési úttal rendelkező összes tömb-tag _igaz_ értékre van kiértékelve. Ezen a tulajdonságon belül bármely [feltétel](../concepts/definition-structure.md#conditions) használható.
  A tulajdonságon belül a [logikai operátorok](#logical-operators) összetett értékelési követelmények létrehozására használhatók.
- **\<condition\>** (kötelező): a rendszer összehasonlítja az értéket a darabszámban teljesített elemek számával **. where** feltétel kifejezése. Numerikus [feltételt](../concepts/definition-structure.md#conditions) kell használni.

A **mezők számának** kifejezése egy **' policyrule osztály** -definícióban akár háromszor is enumerálhatja ugyanazt a tömböt.

Ha további információt szeretne a tömb tulajdonságainak Azure Policy való használatáról, beleértve a **mezők számának** kiértékelésével kapcsolatos részletes magyarázatot, tekintse meg a [tömb erőforrás-tulajdonságainak hivatkozása](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)című témakört.

#### <a name="value-count"></a>Értékek száma

Megszámolja, hogy egy tömb hány tagja felel meg a feltételnek. A tömb lehet literális tömb vagy egy [Array paraméterre mutató hivatkozás](#using-a-parameter-value). Az **értékek száma** kifejezések felépítése:

```json
{
    "count": {
        "value": "<literal array | array parameter reference>",
        "name": "<index name>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Az **értékek száma** a következő tulajdonságokkal együtt használható:

- **Count. Value** (kötelező): az értékelendő tömb.
- **Count.name** (kötelező): az index neve, amely angol betűkből és számjegyből áll. Meghatározza az aktuális iterációban kiértékelt tömb tag értékének nevét. A név az aktuális értékre hivatkozik a `count.where` feltételen belül. Nem kötelező, ha a **Count** kifejezés nem egy másik **Count** kifejezés gyermeke. Ha nincs megadva, az index neve implicit módon be lesz állítva `"default"` .
- **darabszám. where** (nem kötelező): a feltétel kifejezése az egyes tömb tagjainak külön kiértékeléséhez `count.value` . Ha ez a tulajdonság nincs megadva, az összes tömb-tag _igaz_ értékre lesz kiértékelve. Ezen a tulajdonságon belül bármely [feltétel](../concepts/definition-structure.md#conditions) használható. A tulajdonságon belül a [logikai operátorok](#logical-operators) összetett értékelési követelmények létrehozására használhatók. Az [aktuális](#the-current-function) függvény meghívásával a jelenleg enumerált Array tag értéke érhető el.
- **\<condition\>** (kötelező): a rendszer összehasonlítja az értéket a feltétel kifejezésének megfelelő elemek számával `count.where` . Numerikus [feltételt](../concepts/definition-structure.md#conditions) kell használni.

A következő korlátokat kell kikényszeríteni:
- Legfeljebb 10 **Value Count** kifejezés használható egyetlen **' policyrule osztály** -definícióban.
- Minden **Value Count** kifejezés legfeljebb 100 iterációt tud végrehajtani. Ez a szám a szülő **Value Count** kifejezések által végrehajtott ismétlések számát tartalmazza.

#### <a name="the-current-function"></a>Az aktuális függvény

A `current()` függvény csak a `count.where` feltételen belül érhető el. A **szám** kifejezés kiértékelése által jelenleg enumerált tömbbeli tag értékét adja vissza.

**Értékek számának használata**

- `current(<index name defined in count.name>)`. Példa: `current('arrayMember')`.
- `current()`. Csak akkor engedélyezett, ha az **értékek száma** kifejezés nem egy másik **Count** kifejezés gyermeke. A fentivel megegyező értéket adja vissza.

Ha a hívás által visszaadott érték egy objektum, a tulajdonság-hozzáférések támogatottak. Példa: `current('objectArrayMember').property`.

**Mezők számának használata**

- `current(<the array alias defined in count.field>)`. Például: `current('Microsoft.Test/resource/enumeratedArray[*]')`.
- `current()`. Csak akkor engedélyezett, ha a **mezők száma** kifejezés nem egy másik **Count** kifejezés gyermeke. A fentivel megegyező értéket adja vissza.
- `current(<alias of a property of the array member>)`. Például: `current('Microsoft.Test/resource/enumeratedArray[*].property')`.

#### <a name="field-count-examples"></a>Mezők száma – példák

1. példa: Ellenőrizze, hogy egy tömb üres-e

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

2. példa: csak egy tömb tag keresése a feltétel kifejezésének teljesítéséhez

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

3. példa: legalább egy tömbhöz tartozó tag keresése a feltétel kifejezésének teljesítéséhez

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

4. példa: annak megjelölése, hogy az összes objektum összes tagja megfelel-e a feltétel kifejezésének

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

5. példa: Győződjön meg arról, hogy legalább egy tömb tagja megegyezik a feltétel kifejezésben szereplő több tulajdonsággal

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

6. példa: használja `current()` a feltételeken belüli függvényt az `where` aktuálisan enumerált Array tag értékének eléréséhez egy sablon függvényben. Ez az állapot ellenőrzi, hogy egy virtuális hálózat tartalmaz-e olyan 10.0.0.0-előtagot, amely nem tartozik az IP-CIDR tartományhoz.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
          "equals": false
        }
    },
    "greater": 0
}
```

7. példa: használja a `field()` feltételeken belüli függvényt az `where` aktuálisan enumerált Array tag értékének eléréséhez. Ez az állapot ellenőrzi, hogy egy virtuális hálózat tartalmaz-e olyan 10.0.0.0-előtagot, amely nem tartozik az IP-CIDR tartományhoz.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', first(field(('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]')))]",
          "equals": false
        }
    },
    "greater": 0
}
```

#### <a name="value-count-examples"></a>Értékek száma példák

1. példa: Ellenőrizze, hogy az erőforrás neve megegyezik-e a megadott név mintázatával.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

2. példa: Ellenőrizze, hogy az erőforrás neve megegyezik-e a megadott név mintázatával. A `current()` függvény nem adja meg az index nevét. Az eredmény ugyanaz, mint az előző példában.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

3. példa: Ellenőrizze, hogy az erőforrás neve megegyezik-e a tömb paraméterében megadott névvel.

```json
{
    "count": {
        "value": "[parameters('namePatterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

4. példa: Ellenőrizze, hogy a virtuális hálózati címek bármelyikének előtagjai nem szerepelnek-e a jóváhagyott előtagok listáján.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
            "count": {
                "value": "[parameters('approvedPrefixes')]",
                "name": "approvedPrefix",
                "where": {
                    "value": "[ipRangeContains(current('approvedPrefix'), current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
                    "equals": true
                },
            },
            "equals": 0
        }
    },
    "greater": 0
}
```

5. példa: annak ellenőrzését, hogy az összes fenntartott NSG-szabály definiálva van-e egy NSG. A fenntartott NSG-szabályok tulajdonságai az objektumokat tartalmazó tömb paraméterben vannak definiálva.

Paraméter értéke:

```json
[
    {
        "priority": 101,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 22
    },
    {
        "priority": 102,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 3389
    }
]
```

Politika
```json
{
    "count": {
        "value": "[parameters('reservedNsgRules')]",
        "name": "reservedNsgRule",
        "where": {
            "count": {
                "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
                "where": {
                    "allOf": [
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].priority",
                            "equals": "[current('reservedNsgRule').priority]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                            "equals": "[current('reservedNsgRule').access]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                            "equals": "[current('reservedNsgRule').direction]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                            "equals": "[current('reservedNsgRule').destinationPortRange]"
                        }
                    ]
                }
            },
            "equals": 1
        }
    },
    "equals": "[length(parameters('reservedNsgRules'))]"
}
```

### <a name="effect"></a>Hatás

Azure Policy a következő típusú hatásokat támogatja:

- **Hozzáfűzés**: hozzáadja a mezők meghatározott készletét a kéréshez.
- **Naplózás**: figyelmeztetési esemény generálása a tevékenység naplójában, de a kérelem nem sikerül
- **AuditIfNotExists**: figyelmeztetési eseményt állít elő a tevékenység naplójában, ha nem létezik kapcsolódó erőforrás
- **Megtagadás**: eseményt hoz létre a tevékenység naplójában, és sikertelenül kéri a kérést.
- **DeployIfNotExists**: egy kapcsolódó erőforrás üzembe helyezése, ha még nem létezik
- **Letiltva**: nem értékeli ki a házirend-szabálynak való megfeleléshez szükséges erőforrásokat
- **Módosítás**: felveszi, frissíti vagy eltávolítja a definiált címkéket egy erőforrásból vagy előfizetésből.
- **EnforceOPAConstraint** (elavult): az Azure-beli önfelügyelt Kubernetes-fürtökhöz az Open Policy Agent beléptetési vezérlőt konfigurálja forgalomirányító v3-vel
- **EnforceRegoPolicy** (elavult): az Azure Kubernetes Service-ben az Open Policy Agent beléptetési vezérlőt a forgalomirányító v2 protokollal konfigurálja

Az egyes effektusok, a kiértékelési sorrend, a tulajdonságok és a példák részletes ismertetését lásd: a [Azure Policy effektusok ismertetése](effects.md).

### <a name="policy-functions"></a>Házirend-függvények

Az összes [Resource Manager-sablon funkció](../../../azure-resource-manager/templates/template-functions.md) egy házirend-szabályon belül használható, az alábbi függvények és a felhasználó által definiált függvények kivételével:

- copyIndex ()
- üzembe helyezés ()
- listáját
- newGuid()
- pickZones()
- szolgáltatók ()
- hivatkozás ()
- resourceId ()
- változók ()

> [!NOTE]
> Ezek a függvények továbbra is elérhetők a `details.deployment.properties.template` sablon központi telepítésének részeként egy **deployIfNotExists** házirend-definícióban.

A következő függvény használható egy házirend-szabályban, de eltér a használattól egy Azure Resource Manager sablonban (ARM-sablon):

- `utcNow()` – Az ARM-sablonoktól eltérően ez a tulajdonság a _defaultValue_-n kívül is használható.
  - Egy olyan karakterláncot ad vissza, amely az univerzális ISO 8601 DateTime formátumú aktuális dátumra és időpontra van beállítva `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

A következő függvények csak a házirend-szabályokban érhetők el:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **datetime**: [Required] String-String az univerzális ISO 8601 datetime formátumban éééé-hh-NNTóó: PP: mm. FFFFFFFZ'
  - **numberOfDaysToAdd**: [kötelező] egész szám – hozzáadandó napok száma
- `field(fieldName)`
  - **Mezőnév**: [kötelező] karakterlánc – a beolvasandó [mező](#fields) neve
  - Annak az erőforrásnak az értékét adja vissza, amelyet az IF feltétel kiértékel.
  - `field` elsődlegesen a **AuditIfNotExists** és a **DeployIfNotExists** használja a kiértékelt erőforráson található hivatkozási mezőkre. Erre a használatra példa látható az [DeployIfNotExists példában](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - A szabályzat kiértékelését kiváltó kérelem API-verzióját adja vissza (például: `2019-09-01` ).
    Ez az érték az a API-verzió, amelyet a PUT/PATCH kérelemben használt az erőforrás-létrehozási/frissítési kérelmekre vonatkozó értékelésekhez. A meglévő erőforrásokon a megfelelőségi értékelés során mindig a legújabb API-verziót használja a rendszer.
- `policy()`
  - A kiértékelt házirendre vonatkozó alábbi adatokat adja vissza. A tulajdonságok a visszaadott objektumból is elérhetők (például: `[policy().assignmentId]` ).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```

- `ipRangeContains(range, targetRange)`
  - **tartomány**: [kötelező] karakterlánc – karakterlánc, amely az IP-címek tartományát határozza meg.
  - **targetRange**: [kötelező] karakterlánc-karakterlánc, amely az IP-címek tartományát határozza meg.

  Azt adja vissza, hogy a megadott IP-címtartomány tartalmazza-e a célként megadott IP-címtartományt. Az üres tartományok, illetve az IP-családok közötti keverés nem engedélyezett, és kiértékelési hibát eredményez.

  Támogatott formátumok:
  - Egyetlen IP-cím (példák: `10.0.0.0` , `2001:0DB8::3:FFFE` )
  - CIDR-tartomány (példák: `10.0.0.0/24` , `2001:0DB8::/110` )
  - A kezdő és a záró IP-címek által meghatározott tartomány (példák: `192.168.0.1-192.168.0.9` , `2001:0DB8::-2001:0DB8::3:FFFF` )

- `current(indexName)`
  - Speciális függvény, amely csak [Count kifejezéseken](#count)belül használható.

#### <a name="policy-function-example"></a>Példa a házirend-függvényre

Ez a házirend-szabály például az erőforrás-függvénnyel kéri le `resourceGroup` a **Name (név** ) tulajdonságot, `concat` amely a tömb és objektum függvénnyel együtt egy olyan `like` feltételt hoz létre, amely kikényszeríti az erőforrás nevét, hogy az erőforráscsoport nevével kezdődjön.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliasok

Tulajdonság-Aliasok használatával férhet hozzá az erőforrástípus adott tulajdonságaihoz. Az aliasok lehetővé teszik annak korlátozását, hogy az adott erőforrás egy tulajdonsága milyen értékeket vagy feltételeket engedélyezzen. Az egyes aliasok egy adott erőforrástípus különböző API-verzióinak elérési útjaira mutatnak. A házirend kiértékelése során a házirend-kezelő beolvassa az adott API-verzióhoz tartozó tulajdonság elérési útját.

Az aliasok listája mindig növekszik. A Azure Policy által jelenleg támogatott aliasok megkereséséhez használja az alábbi módszerek egyikét:

- Azure Policy-bővítmény a Visual Studio Code-hoz (ajánlott)

  A [Visual Studio Code](../how-to/extension-for-vscode.md) -hoz készült Azure Policy-bővítmény használatával megtekintheti és derítheti fel az erőforrás-tulajdonságok aliasait.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Képernyőkép a Visual Studio Code-hoz készült Azure Policy-bővítményről, amely egy tulajdonsággal jeleníti meg az aliasok nevét." border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > A [módosítás](./effects.md#modify) hatással használható aliasok megkereséséhez használja a következő parancsot Azure PowerShell **4.6.0** vagy magasabb értékben:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>A [*] alias ismertetése

A rendelkezésre álló aliasok közül több olyan verzióval rendelkezik, amely "normál" néven jelenik meg, és egy másik, amely hozzá van **\[\*\]** csatolva. Például:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

A "normál" alias a mezőt egyetlen értékként jelöli. Ez a mező az összehasonlítási forgatókönyvek pontos egyeztetésére szolgál, ha az értékek teljes halmazának pontosan meghatározottnak kell lennie, nem több és nem kevesebb.

Az **\[\*\]** alias a tömb erőforrás-tulajdonság elemei közül kiválasztott értékek gyűjteményét jelöli. Például:

| Alias | Kijelölt értékek |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | A `ipRules` tömb elemei. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | A tulajdonság értékei a `action` tömb egyes elemeiből `ipRules` . |

Ha egy [mező](#fields) feltételben szerepel, a tömb aliasai összehasonlítják az egyes tömb elemeit egy célként megadott értékkel. A [Count](#count) kifejezéssel való használat esetén a következő lehetőségek lehetségesek:

- Tömb méretének megkeresése
- Annak megkeresése, hogy a tömb elemeinek all\any\none megfelel-e egy összetett feltételnek
- Ellenőrizze, hogy pontosan ***n*** tömb elemei megfelelnek-e egy összetett feltételnek

További információkat és példákat a [tömb erőforrás-tulajdonságainak hivatkozása](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [kezdeményezési definíció szerkezetét](./initiative-definition-structure.md)
- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).

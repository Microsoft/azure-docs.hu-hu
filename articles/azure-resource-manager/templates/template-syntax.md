---
title: Sablon szerkezete és szintaxisa
description: Ismerteti Azure Resource Manager sablonok (ARM-sablonok) szerkezetét és tulajdonságait a deklaratív JSON szintaxis használatával.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 31576c72fb845677f132fd9cd6ee776db922d436
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722704"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Az ARM-sablonok struktúrájának és szintaxisának megismerése

Ez a cikk egy Azure Resource Manager sablon (ARM-sablon) szerkezetét ismerteti. Bemutatja a sablon különböző részeit és az ezekben a részekben elérhető tulajdonságokat.

Ez a cikk olyan felhasználók számára készült, akik jól ismerik az ARM-sablonokat. Részletes információkat tartalmaz a sablon struktúrájáról. A sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd [: oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](template-tutorial-create-first-template.md). Ha szeretne többet megtudni az ARM-sablonokról a Microsoft Learn modulok irányított készletén keresztül, tekintse meg az [erőforrások üzembe helyezése és kezelése az Azure-ban ARM-sablonok használatával című részt](/learn/paths/deploy-manage-resource-manager-templates/).

## <a name="template-format"></a>Sablon formátuma

A legegyszerűbb struktúrájában a sablon a következő elemekből áll:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| $schema |Igen |A sablon nyelvének verzióját leíró JavaScript Object Notation (JSON) sémafájl helye. A használt verziószám az üzemelő példány hatókörétől és a JSON-szerkesztőtől függ.<br><br>Ha [a Visual Studio Code-ot használja a Azure Resource Manager Tools bővítménnyel](quickstart-create-templates-use-visual-studio-code.md), használja a legújabb verziót az erőforráscsoport-telepítésekhez:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Előfordulhat, hogy más szerkesztők (például a Visual Studio) nem tudják feldolgozni ezt a sémát. Az ilyen szerkesztők esetében használja a következőt:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Az előfizetés központi telepítéséhez használja a következőt:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>A felügyeleti csoportok központi telepítéséhez használja a következőt:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Bérlői központi telepítések esetén használja a következőt:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Igen |A sablon verziója (például 1.0.0.0). Ehhez az elemhez bármilyen értéket megadhat. Ezzel az értékkel dokumentálhatja a sablon jelentős változásait. Ha a sablonnal telepít erőforrásokat, ezzel az értékkel meggyőződhet arról, hogy a megfelelő sablon használatban van-e. |
| apiProfile |Nem | Egy API-verzió, amely az erőforrástípusok API-verzióinak gyűjteménye szolgál. Ezzel az értékkel nem kell megadnia az API-verziókat a sablon egyes erőforrásaihoz. Ha megad egy API-profil verzióját, és nem ad meg API-verziót az erőforrás típushoz, a Resource Manager a profilban definiált erőforrástípus API-verzióját használja.<br><br>Az API-profil tulajdonság különösen hasznos lehet egy sablon különböző környezetekben, például a Azure Stack és a globális Azure-ban való telepítésekor. Az API-profil verziójának használatával győződjön meg arról, hogy a sablon automatikusan használ mindkét környezetben támogatott verziót. Az API-profilok aktuális verzióinak és a profilban definiált erőforrások API-verzióinak listáját lásd: [API-profil](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>További információ: [verziók nyomon követése az API-profilokkal](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [paraméterek](#parameters) |Nem |A központi telepítés végrehajtásakor megadott értékek az erőforrás-telepítés testreszabásához. |
| [változók](#variables) |Nem |A sablonban JSON-töredékként használt értékek a sablon nyelvi kifejezésének egyszerűsítése érdekében. |
| [funkciók](#functions) |Nem |A sablonon belül elérhető, felhasználó által definiált függvények. |
| [erőforrások](#resources) |Igen |Erőforráscsoport vagy előfizetés által központilag telepített vagy frissített erőforrástípusok. |
| [kimenetek](#outputs) |Nem |Az üzembe helyezés után visszaadott értékek. |

Minden elemnek van beállítható tulajdonsága. Ez a cikk részletesebben ismerteti a sablon szakaszait.

## <a name="data-types"></a>Adattípusok

Az ARM-sablonon belül a következő adattípusokat használhatja:

* sztring
* SecureString
* int
* logikai
* object
* secureObject
* array

Az alábbi sablon az adattípusok formátumát mutatja be. Minden típushoz megfelelő formátumú alapértelmezett érték tartozik.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "boolParameter": {
      "type": "bool",
      "defaultValue": true
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    }
  },
  "resources": [],
  "outputs": {}
}
```

A biztonságos karakterlánc ugyanazt a formátumot használja, mint a karakterlánc, és a Secure objektum ugyanazt a formátumot használja, mint az objektum. Ha egy paramétert biztonságos sztringre vagy biztonságos objektumra állít be, a paraméter értéke nem kerül a telepítési előzményekbe, és nincs naplózva. Ha azonban olyan tulajdonságra állítja be a biztonságos értéket, amely nem vár biztonságos értéket, az érték nem védett. Ha például egy biztonságos karakterláncot állít be egy címkére, az értéket egyszerű szövegként tárolja a rendszer. Biztonságos karakterláncok használata jelszavakhoz és titkos kulcsokhoz.

A beágyazott paraméterekként átadott egész számok esetében az értékek tartományát az SDK vagy az üzembe helyezéshez használt parancssori eszköz korlátozza. Ha például a PowerShell használatával telepít egy sablont, az egész szám típusú érték-2147483648 és 2147483647 között lehet. Ha el szeretné kerülni ezt a korlátozást, adjon meg nagyméretű egész értékeket egy [paraméter fájljában](parameter-files.md). Az erőforrástípusok a saját korlátait alkalmazzák az egész tulajdonságok esetében.

Ha a sablonban logikai és egész értékeket ad meg, ne adja meg idézőjelek közé az értéket. Kezdő és záró karakterlánc-értékek dupla idézőjelekkel ( `"string value"` ).

Az objektumok bal oldali kapcsos zárójeltel () kezdődnek, `{` és a végén egy jobb oldali kapcsos zárójel ( `}` ) áll. A tömbök bal oldali szögletes zárójeltel () kezdődnek, `[` és a jobb oldali szögletes zárójel () végére állnak `]` .

## <a name="parameters"></a>Paraméterek

A `parameters` sablon szakaszban megadhatja, hogy mely értékeket adhatja meg az erőforrások telepítésekor. Egy sablonban legfeljebb 256 paramétert használhat. A paraméterek számát a több tulajdonságot tartalmazó objektumok használatával csökkentheti.

A paraméterek elérhető tulajdonságai a következők:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| paraméter – név |Igen |A paraméter neve. Érvényes JavaScript-azonosítónak kell lennie. |
| típus |Igen |A paraméter értékének típusa A megengedett típusok és értékek a következők: **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject** és **Array**. Lásd [az adattípusokat](#data-types). |
| defaultValue |Nem |A paraméter alapértelmezett értéke, ha a paraméterhez nincs megadva érték. |
| allowedValues |Nem |A paraméter számára engedélyezett értékek tömbje, hogy meggyőződjön arról, hogy a megfelelő érték van megadva. |
| minValue |Nem |Az int Type paraméterek minimális értéke, ez az érték tartalmazza a befogadó értéket. |
| maxValue |Nem |Az int Type paraméterek maximális értéke, ez az érték tartalmazza a befogadó értéket. |
| minLength |Nem |A karakterlánc, a biztonságos karakterlánc és a tömb típusú paraméterek minimális hossza, ez az érték a következő: inclusive. |
| maxLength |Nem |A karakterlánc, a biztonságos karakterlánc és a tömb típusú paraméterek maximális hossza, ez az érték a következő: inclusive. |
| leírás |Nem |A felhasználók számára a portálon megjelenő paraméter leírása. További információ: [Megjegyzések a sablonokban](#comments). |

A paraméterek használatáról a következő témakörben talál példákat: [paraméterek az ARM-sablonokban](template-parameters.md).

## <a name="variables"></a>Változók

A `variables` szakaszban a sablonban használható értékeket hozhat létre. Nem kell megadnia a változókat, de gyakran egyszerűsíti a sablont a komplex kifejezések csökkentésével. Az egyes változók formátuma megegyezik az egyik [adattípussal](#data-types).

A következő példa egy változó definiálásához elérhető lehetőségeket mutatja be:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

További információ a `copy` változó értékének a használatával történő létrehozásáról: [változó iteráció](copy-variables.md).

Példák a változók használatára: [változók az ARM-sablonban](template-variables.md).

## <a name="functions"></a>Functions

A sablonon belül létrehozhat saját függvényeket is. Ezek a függvények a sablonban használhatók. Jellemzően olyan bonyolult kifejezéseket határozhat meg, amelyeket nem szeretne megismételni a sablon során. A felhasználó által definiált függvényeket a sablonok által támogatott kifejezésekből és [függvényekből](template-functions.md) hozza létre.

A felhasználói függvények meghatározásakor bizonyos korlátozások vonatkoznak:

* A függvény nem fér hozzá a változókhoz.
* A függvény csak a függvényben definiált paramétereket tudja használni. Ha a [Parameters függvényt](template-functions-deployment.md#parameters) egy felhasználó által definiált függvényen belül használja, akkor a függvény paraméterei vannak korlátozva.
* A függvény nem hívhat meg más, felhasználó által definiált függvényeket.
* A függvény nem tudja használni a [Reference függvényt](template-functions-resource.md#reference).
* A függvény paraméterei nem rendelkezhetnek alapértelmezett értékekkel.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| névtér |Igen |Az egyéni függvények névterét. A használatával elkerülhető a sablon függvényekkel való névütközés. |
| függvény – név |Igen |Az egyéni függvény neve. A függvény meghívásakor egyesítse a függvény nevét a névtérrel. Ha például a contoso névtérben megnevezett függvényt szeretne meghívni `uniqueName` , használja a következőt: `"[contoso.uniqueName()]"` . |
| paraméter – név |Nem |Az egyéni függvényben használandó paraméter neve. |
| paraméter – érték |Nem |A paraméter értékének típusa A megengedett típusok és értékek a következők: **String**, **SecureString**, **int**, **bool**, **Object**, **secureObject** és **Array**. |
| kimeneti típus |Igen |A kimeneti érték típusa A kimeneti értékek ugyanazokat a típusokat támogatják, mint a függvény bemeneti paraméterei. |
| kimenet – érték |Igen |A függvény által kiértékelt és visszaadott sablon nyelvi kifejezése. |

Az egyéni függvények használatáról az [ARM-sablon felhasználó által definiált függvények](template-user-defined-functions.md)című részében talál példákat.

## <a name="resources"></a>Források

A `resources` szakaszban megadhatja a telepített vagy frissített erőforrásokat.

Az erőforrásokat az alábbi struktúrával definiálhatja:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| feltétel | Nem | Logikai érték, amely azt jelzi, hogy az erőforrás a telepítés során lesz-e kiépítve. Amikor `true` az erőforrás létrejön az üzembe helyezés során. Ha `false` az erőforrás ki van hagyva ehhez a központi telepítéshez. Lásd a [feltételt](conditional-resource-deployment.md). |
| típus |Igen |Az erőforrás típusa. Ez az érték az erőforrás-szolgáltató névterének és az erőforrás típusának (például) a kombinációja `Microsoft.Storage/storageAccounts` . Az elérhető értékek meghatározásához tekintse meg a [sablon-referenciát](/azure/templates/). Gyermek erőforrás esetén a típus formátuma attól függ, hogy a szülő erőforráson belül van-e beágyazva, vagy a szülő erőforráson kívül van-e definiálva. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md). |
| apiVersion |Igen |Az erőforrás létrehozásához használandó REST API verziója. Új sablon létrehozásakor állítsa ezt az értéket az üzembe helyezett erőforrás legújabb verziójára. Ha a sablon szükség szerint működik, használja továbbra is ugyanazt az API-verziót. Ha továbbra is ugyanazt az API-verziót használja, azzal csökkentheti az új API-verzió kockázatát, amely megváltoztatja a sablon működését. Érdemes csak akkor frissíteni az API-verziót, ha egy későbbi verzióban bevezetett új funkciót szeretne használni. Az elérhető értékek meghatározásához tekintse meg a [sablon-referenciát](/azure/templates/). |
| name |Igen |Az erőforrás neve. A névnek követnie kell a RFC3986-ben definiált URI-összetevők korlátozásait. Azok az Azure-szolgáltatások, amelyek az erőforrás nevét külső feleknek teszik elérhetővé, érvényesítik a nevet, hogy a rendszer ne Próbálkozzon másik identitás hamisításával. Gyermek erőforrás esetén a név formátuma attól függ, hogy a szülő erőforráson belül van-e beágyazva, vagy a szülő erőforráson kívül van-e definiálva. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md). |
| Megjegyzések |Nem |A sablon erőforrásainak dokumentálására szolgáló megjegyzései. További információ: [Megjegyzések a sablonokban](template-syntax.md#comments). |
| location |Változó |A megadott erőforrás támogatott földrajzi helyei. Bármelyik elérhető helyet kiválaszthatja, de általában érdemes lehet a felhasználókhoz közelebbi választ adni. Általában az is fontos, hogy olyan erőforrásokat helyezzen el, amelyek egymással együttműködnek ugyanabban a régióban. A legtöbb erőforrástípus egy helyet igényel, de bizonyos típusok (például a szerepkör-hozzárendelés) nem igényelnek helyet. Lásd az [erőforrás helyének beállítása](resource-location.md)című témakört. |
| dependsOn |Nem |Az erőforrás üzembe helyezése előtt telepítendő erőforrások. A Resource Manager kiértékeli az erőforrások közötti függőségeket, és a megfelelő sorrendben telepíti őket. Ha az erőforrások nem függnek egymástól, párhuzamosan lesznek üzembe helyezve. Az érték lehet az erőforrásnevek vagy az erőforrás egyedi azonosítóinak vesszővel tagolt listája. Csak a sablonban üzembe helyezett erőforrások listázása. A sablonban nem definiált erőforrásoknak már léteznie kell. Kerülje a szükségtelen függőségek hozzáadását, és lassíthatja az üzembe helyezést, és körkörös függőségeket hozhat létre. A függőségek beállításával kapcsolatos útmutatásért lásd: [erőforrások üzembe helyezési sorrendjének meghatározása ARM-sablonokban](define-resource-dependency.md). |
| tags |Nem |Az erőforráshoz társított címkék. Címkék alkalmazása az erőforrások logikai rendszerezéséhez az előfizetésen belül. |
| SKU | Nem | Egyes erőforrások lehetővé teszik a telepítendő SKU-t meghatározó értékek használatát. Megadhatja például, hogy milyen típusú redundancia van egy Storage-fiókhoz. |
| típusú | Nem | Egyes erőforrások lehetővé teszik egy olyan érték használatát, amely meghatározza a telepített erőforrás típusát. Megadhatja például a létrehozandó Cosmos DB típusát. |
| másolás |Nem |Ha több példányra van szükség, a létrehozandó erőforrások száma. Az alapértelmezett üzemmód párhuzamos. Ha nem szeretné, hogy az összes vagy az erőforrások egyszerre legyenek telepítve, akkor a soros módot kell megadnia. További információ: az [erőforrások több példányának létrehozása Azure Resource Managerban](copy-resources.md). |
| csomag | Nem | Egyes erőforrások lehetővé teszik az olyan értékek használatát, amelyek meghatározzák az üzembe helyezési tervet. Megadhatja például a virtuális gép Marketplace-rendszerképét. |
| properties |Nem |Erőforrás-specifikus konfigurációs beállítások. A tulajdonságok értékei megegyeznek a REST API művelet (PUT metódus) által a kérelem törzsében megadott értékekkel az erőforrás létrehozásához. Egy másolási tömböt is megadhat egy tulajdonság több példányának létrehozásához. Az elérhető értékek meghatározásához tekintse meg a [sablon-referenciát](/azure/templates/). |
| resources |Nem |A definiált erőforrástól függő alárendelt erőforrások. Csak a szülő erőforrás sémája által engedélyezett erőforrástípusok megadása. A fölérendelt erőforrástól való függőség nincs befoglalva. Explicit módon meg kell határoznia ezt a függőséget. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md). |

## <a name="outputs"></a>Kimenetek

A `outputs` szakaszban megadhatja az üzemelő példányból visszaadott értékeket. Jellemzően az üzembe helyezett erőforrások értékeit kell visszaadnia.

A következő példa egy kimeneti definíció szerkezetét mutatja be:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| kimenet – név |Igen |A kimeneti érték neve. Érvényes JavaScript-azonosítónak kell lennie. |
| feltétel |Nem | Logikai érték, amely jelzi, hogy a rendszer visszaadja-e ezt a kimeneti értéket. Ha a `true` érték szerepel a központi telepítés kimenetében. Ekkor `false` a rendszer kihagyja a kimeneti értéket a központi telepítéshez. Ha nincs megadva, az alapértelmezett érték: `true` . |
| típus |Igen |A kimeneti érték típusa A kimeneti értékek ugyanazokat a típusokat támogatják, mint a sablon bemeneti paraméterei. Ha a kimeneti típushoz **SecureString** ad meg, az érték nem jelenik meg a telepítési előzményekben, és nem kérhető le másik sablonból. Ha egy titkos értéket több sablonban szeretne használni, tárolja a titkot egy Key Vaultban, és hivatkozzon a titkos kulcsra a paraméter fájljában. További információ: [a Azure Key Vault használata a biztonságos paraméterek értékének](key-vault-parameter.md)átadására az üzembe helyezés során. |
| érték |Nem |A sablon nyelvi kifejezése, amelyet a rendszer kiértékel, és kimeneti értékként ad vissza. Adjon meg **értéket** vagy **másolatot**. |
| másolás |Nem | Egy kimenet egynél több értékének visszaküldésére szolgál. **Érték** vagy **Másolás** megadása. További információ: [a kimenet iterációja az ARM-sablonokban](copy-outputs.md). |

Példák a kimenetek használatára: az [ARM-sablon kimenetei](template-outputs.md).

<a id="comments"></a>

## <a name="comments-and-metadata"></a>Megjegyzések és metaadatok

Van néhány lehetőség, amelyekkel megjegyzéseket és metaadatokat adhat hozzá a sablonhoz.

### <a name="comments"></a>Megjegyzések

A beágyazott megjegyzésekhez használhatja a vagy a parancsot `//` , `/* ... */` de ez a szintaxis nem működik az összes eszközzel. Ha ezt a megjegyzést adja hozzá, győződjön meg arról, hogy az eszköz támogatja a beágyazott JSON-megjegyzéseket.

> [!NOTE]
> A sablonok megjegyzésekkel történő üzembe helyezéséhez az Azure CLI és a 2.3.0 verziójának használatával a kapcsolót kell használnia `--handle-extended-json-format` .

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

A Visual Studio Code-ban a [Azure Resource Manager Tools bővítmény](quickstart-create-templates-use-visual-studio-code.md) képes automatikusan felderíteni egy ARM-sablont, és módosítani a nyelvi módot. Ha a Visual Studio Code jobb alsó sarkában **Azure Resource Manager sablon** látható, használhatja a beágyazott megjegyzéseket. A beágyazott megjegyzések már nem érvénytelenként vannak megjelölve.

![Visual Studio Code Azure Resource Manager sablon üzemmód](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metaadatok

A `metadata` sablonhoz szinte bárhol hozzáadhat objektumokat. A Resource Manager figyelmen kívül hagyja az objektumot, de a JSON-szerkesztő figyelmeztetni lehet, ha a tulajdonság érvénytelen. Az objektumban adja meg a szükséges tulajdonságokat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

A esetében `parameters` adjon hozzá egy `metadata` objektumot egy `description` tulajdonsággal.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

A sablonnak a portálon keresztüli üzembe helyezése során a leírásban megadott szöveget a rendszer automatikusan tippként használja a paraméterhez.

![Paraméteres tipp megjelenítése](./media/template-syntax/show-parameter-tip.png)

A esetében `resources` adjon hozzá egy `comments` elemet vagy egy `metadata` objektumot. Az alábbi példa egy `comments` elemet és egy objektumot mutat be `metadata` .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

A esetében `outputs` adjon hozzá egy `metadata` objektumot a kimeneti értékhez.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

`metadata`Felhasználó által definiált függvényekhez nem adhat hozzá objektumot.

## <a name="multi-line-strings"></a>Többsoros karakterláncok

A sztringeket több sorba is lehet bontani. Például tekintse meg a `location` tulajdonságot, és az alábbi JSON-példában szereplő megjegyzések egyikét.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

> [!NOTE]
> Ha többsoros karakterláncokkal szeretné telepíteni a sablonokat az Azure CLI és a 2.3.0 verziójának használatával, akkor a kapcsolót kell használnia `--handle-extended-json-format` .

## <a name="next-steps"></a>Következő lépések

* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használható függvények részleteiért lásd: [ARM-sablon függvények](template-functions.md).
* Ha több sablont szeretne egyesíteni az üzembe helyezés során, olvassa el a [csatolt és beágyazott sablonok használata Azure-erőforrások telepítésekor](linked-templates.md)című témakört.
* A sablonok létrehozásával kapcsolatos javaslatokért lásd: [ARM-sablon – ajánlott eljárások](template-best-practices.md).
* A gyakori kérdésekre adott válaszokért tekintse meg az [ARM-sablonokkal kapcsolatos gyakori kérdések](frequently-asked-questions.md)című témakört.

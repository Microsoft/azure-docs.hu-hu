---
title: Áttekintés – az üzembe helyezés automatizálása Azure Logic Apps
description: Ismerkedjen meg Azure Resource Manager-sablonokkal a Azure Logic Apps üzembe helyezésének automatizálásához
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: ec72431d927fd59677075e7adfdf7df171574882
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772943"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Áttekintés: üzembe helyezés automatizálása Azure Logic Appshez Azure Resource Manager sablonok használatával

Ha készen áll a logikai alkalmazás létrehozásának és üzembe helyezésének automatizálására, kiterjesztheti a logikai alkalmazás alapjául szolgáló munkafolyamat-definícióját egy [Azure Resource Manager sablonba](../azure-resource-manager/management/overview.md). Ez a sablon határozza meg az infrastruktúrát, az erőforrásokat, a paramétereket és az egyéb információkat a logikai alkalmazás üzembe helyezéséhez és telepítéséhez. Ha a központi telepítésben (más néven *parameterizing*) eltérő értékekre vonatkozó paramétereket határoz meg, akkor a különböző telepítési igények alapján ismételten és következetesen telepítheti a Logic apps-alkalmazásokat.

Ha például fejlesztési, tesztelési és éles környezetekben végez üzembe helyezést, akkor az egyes környezetekhez valószínűleg különböző kapcsolódási karakterláncokat fog használni. Deklarálhatja a sablon azon paramétereit, amelyek különböző kapcsolatok karakterláncokat fogadnak el, majd egy külön [paraméter-fájlban](../azure-resource-manager/templates/parameter-files.md)tárolják ezeket a karakterláncokat. Így módosíthatja ezeket az értékeket a sablon frissítése és újbóli üzembe helyezése nélkül. Olyan esetekben, ahol olyan paraméterek vannak, amelyek érzékenyek, vagy amelyeket biztosítani kell, például a jelszavakat és a titkos kulcsokat, ezeket az értékeket [Azure Key Vaultban](../azure-resource-manager/templates/key-vault-parameter.md) tárolhatja, és a paramétereket tartalmazó fájllal is lekérheti ezeket az értékeket. Ezekben az esetekben azonban az aktuális értékek beolvasásához újra üzembe kell helyezni.

Ez az Áttekintés egy Resource Manager-sablon azon attribútumait ismerteti, amelyek tartalmazzák a logikai alkalmazás munkafolyamatának definícióját. A sablon és a munkafolyamat-definíció JSON-szintaxist használ, de néhány eltérés létezik, mert a munkafolyamat-definíció a [munkafolyamat-definíció nyelvi sémáját](../logic-apps/logic-apps-workflow-definition-language.md)is követi. Például a sablonok kifejezései és a munkafolyamat-definíciós kifejezések különböznek a paraméterek és az általuk elfogadható értékek [függvényében](#parameter-references) .

> [!TIP]
> A legegyszerűbben az üzembe helyezésre kész, a Visual Studio (ingyenes Community Edition vagy újabb verzió) és a Visual studióhoz készült Azure Logic Apps Tools használatával lehet megszerezni az érvényes paraméteres logikai alkalmazás sablonját. Ezután [létrehozhatja a logikai alkalmazást a Visual Studióban](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) , vagy [megkeresheti és letöltheti az Azure-ból egy meglévő logikai alkalmazást a Visual studióba](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Emellett létrehozhat logikai alkalmazások sablonjait is [Azure PowerShell használatával a LogicAppTemplate modullal](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

A jelen témakörben szereplő logikai alkalmazás egy [Office 365 Outlook-eseményindítót](/connectors/office365/) használ, amely új e-mailek érkezésekor, valamint egy [Azure Blob Storage művelet](/connectors/azureblob/) , amely létrehoz egy blobot az e-mail törzséhez, és feltölti a blobot egy Azure Storage-tárolóba. A példák azt is bemutatják, hogyan parametrizálja a telepítéskor változó értékek.

A Resource Manager-sablonokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Azure Resource Manager sablon szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md)
* [Ajánlott eljárások az Azure Resource Manager-sablonokhoz](../azure-resource-manager/templates/template-best-practices.md)
* [Azure Resource Manager-sablonok fejlesztése felhőkonzisztenciához](../azure-resource-manager/templates/templates-cloud-consistency.md)

A Logic apps, az integrációs fiókok, az integrációs fiók összetevői és az integrációs szolgáltatási környezetek sablon-erőforrásairól a [Microsoft. Logic erőforrástípusok](/azure/templates/microsoft.logic/allversions)című témakörben talál további információt.

A minta logikai alkalmazások sablonjai a következő példákban találhatók:

* A témakörben szereplő példákhoz használt [teljes sablon](#full-example-template)
* [Minta rövid útmutató Logic app-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) a githubban

A Logic Apps REST API a [Azure Logic Apps REST API áttekintésével](/rest/api/logic)kezdje.

<a name="template-structure"></a>

## <a name="template-structure"></a>Sablonszerkezet

A legfelső szinten egy Resource Manager-sablon követi ezt a struktúrát, amely teljes mértékben a [Azure Resource Manager sablon szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md) című témakörben olvasható:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

A Logic app-sablonok esetében elsősorban a következő sablon-objektumok használhatók:

| Attribútum | Leírás |
|-----------|-------------|
| `parameters` | A [sablon paramétereit](../azure-resource-manager/templates/template-syntax.md#parameters) deklarálja az Azure-ban üzembe helyezett erőforrások létrehozásakor és testreszabásakor használandó értékek elfogadásához. Ezek a paraméterek például elfogadják a logikai alkalmazás nevének és helyének, kapcsolatainak és az üzembe helyezéshez szükséges egyéb erőforrásoknak az értékeit. Ezeket a [paramétereket egy paraméter-fájlban](#template-parameter-files)tárolhatja, amelyet a témakör későbbi részében talál. Az általános részleteket lásd: [Paraméterek – Resource Manager-sablon szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Meghatározza az Azure-erőforráscsoport létrehozásához vagy frissítéséhez és üzembe helyezéséhez szükséges [erőforrásokat](../azure-resource-manager/templates/template-syntax.md#resources) , például a logikai alkalmazást, a kapcsolatokat, az Azure Storage-fiókokat stb. Az általános részleteket lásd: [erőforrások – Resource Manager-sablon szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

A logikai alkalmazás sablonja a következő formátumnév-formátumot használja:

**<*Logic-app-Name* # C0.jsbekapcsolva**

> [!IMPORTANT]
> A sablon szintaxisa megkülönbözteti a kis-és nagybetűket, ezért ügyeljen arra, hogy konzisztens burkolatot használjon. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Sablon paraméterei

A logikai alkalmazások sablonja több, `parameters` különböző szinteken található objektummal rendelkezik, és különböző funkciókat hajt végre. Például a legfelső szinten deklarálhatja a [sablon paramétereit](../azure-resource-manager/templates/template-syntax.md#parameters) , amelyeket az Azure-beli erőforrások létrehozásakor és üzembe helyezéskor a telepítéskor elfogad és használhat, például:

* A logikai alkalmazás
* Kapcsolatok, amelyeket a logikája a [felügyelt összekötők](../connectors/apis-list.md) használatával más szolgáltatásokhoz és rendszerekhez való hozzáféréshez használ
* A logikai alkalmazás által az üzembe helyezéshez szükséges egyéb erőforrások

  Ha például a logikai alkalmazás egy [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) használ a vállalatközi (B2B) forgatókönyvekhez, a sablon legfelső szintű `parameters` objektuma deklarálja azt a paramétert, amely elfogadja az adott integrációs fiók erőforrás-azonosítóját.

Itt látható egy paraméter definíciójának általános szerkezete és szintaxisa, amelyet a [Paraméterek – Resource Manager-sablon szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md#parameters)teljes egészében ismertet:

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Ez a példa az Azure-beli erőforrások létrehozásához és üzembe helyezéséhez használt értékek sablonjának paramétereit mutatja be:

* A logikai alkalmazás neve és helye
* A logikai alkalmazáshoz csatolt integrációs fiókhoz használandó azonosító

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

A bizalmas vagy védeni kívánt értékeket (például felhasználóneveket, jelszavakat és titkokat) kezelő paraméterek kivételével az összes paraméternek tartalmaznia kell `defaultValue` az attribútumokat, bár bizonyos esetekben az alapértelmezett értékek üres értékek. A sablon paramétereinek használatára szolgáló központi telepítési értékeket a jelen témakör későbbi részében ismertetett példa [paraméterrel rendelkező fájl](#template-parameter-files) tartalmazza.

A sablon paramétereinek biztonságossá tételével kapcsolatos további információkért tekintse meg a következő témaköröket:

* [A sablon paramétereinek biztonsági javaslatai](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [A sablon paramétereinek biztonságának javítása](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Biztonságos paraméterek értékeinek továbbítása Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Más sablon-objektumok gyakran hivatkoznak a sablon paramétereinek használatára, így például a sablon paraméterei között továbbított értékeket használhatják, például:

* A sablon a jelen témakör későbbi részében ismertetett [erőforrás-objektuma](#template-resources)meghatározza az Azure-ban létrehozni és telepíteni kívánt összes erőforrást, például a [logikai alkalmazás erőforrás-definícióját](#logic-app-resource-definition). Ezek az erőforrások gyakran használják a sablon paramétereinek értékeit, például a logikai alkalmazás nevét, helyét és a kapcsolatok adatait.

* A logikai alkalmazás erőforrás-definíciójának mélyebb szintjén a [munkafolyamat-definíció Parameters objektuma](#workflow-definition-parameters) deklarálja a logikai alkalmazás futtatókörnyezetében használandó értékek paramétereit. Deklarálhatja például a munkafolyamat-definíció paramétereit arra a felhasználónévre és jelszóra vonatkozóan, amelyet egy HTTP-trigger használ a hitelesítéshez. A munkafolyamat-definíciós paraméterek értékeinek megadásához használja a `parameters` munkafolyamat-definíción *kívüli* objektumot, de a logikai alkalmazás erőforrás-definícióján *belül* maradjon. Ebben a külső `parameters` objektumban hivatkozhat a korábban deklarált sablon-paraméterekre, amelyek a paraméter-fájlból való üzembe helyezéskor elfogadják az értékeket.

Ha a paraméterekre hivatkozik, a sablon kifejezései és a függvények eltérő szintaxist használnak, és eltérően viselkednek a munkafolyamat-definíciós kifejezések és függvények alapján. További információ ezekről a különbségekről: [hivatkozás a paraméterekre](#parameter-references) a témakör későbbi részében.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Ajánlott eljárások – sablon paraméterei

Íme néhány ajánlott eljárás paraméterek definiálásához:

* A paramétereket csak olyan értékeknél deklarálhatja, amelyek a központi telepítési igények alapján változnak. Ne deklaráljon paramétereket olyan értékekhez, amelyek a különböző központi telepítési követelményeknél azonosak maradnak.

* Adja `defaultValue` meg az attribútumot, amely üres értékeket adhat meg az összes paraméterhez, kivéve a bizalmas vagy a védeni kívánt értékeket. Mindig használjon biztonságos paramétereket a felhasználónevek, jelszavak és titkos kódok számára. A bizalmas paraméterek értékeinek elrejtéséhez vagy kivédéséhez kövesse az alábbi témakörökben található útmutatást:

  * [A sablon paramétereinek biztonsági javaslatai](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [A sablon paramétereinek biztonságának javítása](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Biztonságos paraméterek értékeinek továbbítása Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* A sablon-paraméterek neveinek a munkafolyamat-definíciós paraméterek neveiből való megkülönböztetéséhez használhatja a leíró sablon paraméterének nevét, például: `TemplateFabrikamPassword`

További sablon ajánlott eljárásai: [ajánlott eljárások a sablon paramétereinek](../azure-resource-manager/templates/template-best-practices.md#parameters)megtekintéséhez.

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Sablon paramétereinek fájlja

A sablon paramétereinek értékeinek megadásához tárolja ezeket az értékeket egy [paraméter-fájlban](../azure-resource-manager/templates/parameter-files.md). Így a telepítési igények alapján különböző paraméterekkel rendelkező fájlokat használhat. Itt látható a használni kívánt Fájlnév formátuma:

* Logic app-sablon fájlneve: **< *Logic-app-Name* # C0.json**
* Parameters Fájlnév: **< *Logic-app-Name* # C0.parameters.json**

Itt látható a (z) paraméterek fájljának struktúrája, amely tartalmaz egy Key Vault-referenciát a [biztonságos paraméterek értékének átadásához Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>"
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Ez a példa paraméter a jelen témakör korábbi részében deklarált sablon-paraméterek értékeit adja meg:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Sablonerőforrások

A sablon tartalmaz egy `resources` objektumot, amely egy olyan tömb, amely az Azure-ban létrehozandó és üzembe helyezett erőforrások definícióit tartalmazza, például a [logikai alkalmazás erőforrás-definícióját](#logic-app-resource-definition), a [kapcsolati erőforrás definícióit](#connection-resource-definitions)és minden olyan erőforrást, amelyet a logikai alkalmazásnak üzembe kell helyeznie.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> A sablonok több logikai alkalmazáshoz is tartalmazhatnak erőforrás-definíciókat, ezért ügyeljen arra, hogy a logikai alkalmazás összes erőforrása ugyanazt az Azure-erőforráscsoportot adja meg. Amikor a Visual Studióval üzembe helyezi a sablont egy Azure-erőforráscsoporthoz, a rendszer felszólítja, hogy melyik logikai alkalmazást szeretné megnyitni. Emellett az Azure erőforráscsoport-projekt több sablont is tartalmazhat, ezért ügyeljen arra, hogy a megfelelő paramétereket tartalmazó fájlt válassza ki, ha a rendszer kéri.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Erőforrás-definíciók megtekintése

Egy Azure-erőforráscsoport összes erőforrás-definíciójának áttekintéséhez [töltse le a logikai alkalmazást az Azure-ból a Visual studióba](../logic-apps/manage-logic-apps-with-visual-studio.md), amely a legegyszerűbben az üzembe helyezésre kész, érvényes paraméteres Logic app-sablon létrehozása.

A sablonok erőforrásaival és attribútumaival kapcsolatos általános információkért tekintse meg a következő témaköröket:

* [Erőforrások – Resource Manager-sablon szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md#resources)
* [Ajánlott eljárások a sablonok erőforrásaihoz](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Logikai alkalmazás erőforrás-definíciója

A logikai alkalmazás [munkafolyamat-erőforrás-definíciója egy sablonban](/azure/templates/microsoft.logic/workflows) az `properties` objektummal kezdődik, amely tartalmazza ezt az információt:

* A logikai alkalmazás állapota az üzembe helyezéskor
* A logikai alkalmazás által használt bármely integrációs fiók azonosítója
* A logikai alkalmazás munkafolyamat-definíciója
* Egy `parameters` objektum, amely a futásidőben használandó értékeket állítja be
* A logikai alkalmazással kapcsolatos egyéb információforrások, például a név, a típus, a hely, a futásidejű konfigurációs beállítások stb.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {},
            "runtimeConfiguration": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

A logikai alkalmazás erőforrás-definíciójának attribútumai a következők:

| Attribútum | Kötelező | Típus | Leírás |
|-----------|----------|------|-------------|
| `state` | Igen | Sztring | A logikai alkalmazás állapota az üzembe helyezéskor `Enabled` azt jelenti, hogy a logikai alkalmazás él, és `Disabled` azt jelenti, hogy a logikai alkalmazás inaktív. Ha például nem áll készen a logikai alkalmazás életbe léptetésére, de a vázlat verzióját szeretné telepíteni, használhatja a `Disabled` lehetőséget. |
| `integrationAccount` | Nem | Objektum | Ha a logikai alkalmazás egy integrációs fiókot használ, amely a vállalatok közötti (B2B) forgatókönyvek összetevőit tárolja, ez az objektum tartalmazza az `id` attribútumot, amely meghatározza az integrációs fiók azonosítóját. |
| `definition` | Igen | Objektum | A logikai alkalmazás mögöttes munkafolyamat-definíciója, amely ugyanaz az objektum, amely a kód nézetben jelenik meg, és teljes mértékben le van írva a [séma-referenciában a munkafolyamat-definíció nyelve](../logic-apps/logic-apps-workflow-definition-language.md) témakörben. Ebben a munkafolyamat-definícióban az `parameters` objektum deklarálja a Logic app Runtime-ban használandó értékek paramétereit. További információ: [munkafolyamat-definíció és paraméterek](#workflow-definition-parameters). <p><p>Ha meg szeretné tekinteni a logikai alkalmazás munkafolyamat-definíciójában lévő attribútumokat, váltson a "design View" kifejezésre a "Code View" értékre a Azure Portal vagy a Visual Studióban, vagy egy olyan eszköz használatával, mint például a [Azure erőforrás-kezelő](https://resources.azure.com). |
| `parameters` | Nem | Objektum | A Logic app Runtime-ban használandó [munkafolyamat-definíciós paraméter értéke](#workflow-definition-parameters) . Ezekhez az értékekhez tartozó definíciók a [munkafolyamat-definíció Parameters objektumán](#workflow-definition-parameters)belül jelennek meg. Továbbá, ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md) használ a más szolgáltatások és rendszerek eléréséhez, ez az objektum tartalmaz egy `$connections` objektumot, amely beállítja a futásidőben használandó kapcsolódási értékeket. |
| `accessControl` | Nem | Objektum | A logikai alkalmazás biztonsági attribútumainak megadásához, például az eseményindítók igényléséhez vagy a futtatási előzményekhez és kimenetekhez való IP-hozzáférés korlátozásához. További információ: [a Logic apps biztonságos elérése](../logic-apps/logic-apps-securing-a-logic-app.md). |
| `runtimeConfiguration` | Nem | Objektum | Olyan tulajdonságok megadásához `operationOptions` , amelyek vezérlik a logikai alkalmazás futási idejét. Például futtathatja a logikai alkalmazást [nagy átviteli sebességű módban](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode). |
|||||

A Logic Apps objektumok erőforrás-definíciókkal kapcsolatos további információkért lásd: [Microsoft. Logic Resource types](/azure/templates/microsoft.logic/allversions):

* [Munkafolyamat-erőforrás definíciója](/azure/templates/microsoft.logic/workflows)
* [Integrációs szolgáltatás környezeti erőforrás-definíciója](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [Integrációs szolgáltatás környezetének felügyelt API-erőforrásának definíciója](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [Integrációs fiók erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts)

* Integrációs fiók összetevői:

  * [Megállapodás erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [Szerelvény erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [A Batch-konfiguráció erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [Tanúsítvány erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [Térkép erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [Partner erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [Séma erőforrás-definíciója](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [Munkamenet-erőforrás definíciója](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Munkafolyamat-definíció és paraméterek

A logikai alkalmazás munkafolyamat-definíciója megjelenik az `definition` objektumban, amely a `properties` logikai alkalmazás erőforrás-definíciójában megjelenő objektumban jelenik meg. Ez `definition` az objektum ugyanaz az objektum, amely a kód nézetben jelenik meg, és teljes mértékben le van írva a [séma-referenciában a munkafolyamat-definíció nyelve](../logic-apps/logic-apps-workflow-definition-language.md) témakörben. A munkafolyamat-definíció egy belső `parameters` deklarációs objektumot tartalmaz, ahol megadhat új vagy szerkesztheti a meglévő paramétereket a munkafolyamat-definíció által futásidőben használt értékekhez. Ezeket a paramétereket a munkafolyamat triggerén vagy műveletein belül is hivatkozhat. Alapértelmezés szerint ez az `parameters` objektum üres, hacsak a logikai alkalmazás nem hoz létre kapcsolatokat más szolgáltatásokkal és rendszerekkel a [felügyelt összekötők](../connectors/apis-list.md)segítségével.

A munkafolyamat-definíciós paraméterek értékeinek megadásához használja a `parameters` munkafolyamat-definíción *kívüli* objektumot  , de még a logikai alkalmazás erőforrás-definíciójában is. Ebben a külső `parameters` objektumban a korábban deklarált sablon paramétereit is felhasználhatja, amelyek a paraméter-fájlból való üzembe helyezéskor elfogadják az értékeket.

> [!TIP]
>
> Ajánlott eljárásként ne hivatkozzon közvetlenül a sablon paramétereit, amelyek kiértékelése az üzembe helyezéskor történik a munkafolyamat-definíción belül. Ehelyett deklaráljon egy munkafolyamat-definíciós paramétert, amelyet aztán beállíthat a `parameters` munkafolyamat-definíción *kívüli* objektumon, de  még a logikai alkalmazás erőforrás-definíciójában is. További információ: [hivatkozások a paraméterekre](#parameter-references).

Ez a szintaxis azt mutatja be, hogy hol deklarálhatja a paramétereket a sablon és a munkafolyamat-definíció szintjén, valamint azt is, hogy hol állíthatók be a paraméterek értékei a sablonra és a munkafolyamat-definíciós paraméterekre való hivatkozással:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Biztonságos munkafolyamat-definíciós paraméterek

Egy munkafolyamat-definíciós paraméter esetében, amely bizalmas adatokat, jelszavakat, hozzáférési kulcsokat vagy titkos kódokat kezel futásidőben, deklarálja vagy szerkessze a paramétert a `securestring` vagy a `secureobject` paraméter típusának használatára. Ezt a paramétert a munkafolyamat-definíciójában és a teljes munkafolyamaton belül is hivatkozhat. A sablon legfelső szintjén deklaráljon egy olyan paramétert, amely ugyanolyan típusú, mint az üzembe helyezéskor szükséges adatok kezelése.

A munkafolyamat-definíciós paraméter értékének megadásához használja a `parameters` munkafolyamat-definíción *kívüli* objektumot,  de továbbra is a logikai alkalmazás erőforrás-definíciójában a sablon paraméterre való hivatkozáshoz. Végül, ha az értéket át szeretné adni a sablon paraméterének az üzembe helyezéskor, tárolja az értéket a [Azure Key Vaultban](../azure-resource-manager/templates/key-vault-parameter.md) , és hivatkozzon a Key vaultra a sablon által a telepítéskor használt [Paraméterek fájlban](#template-parameter-files) .

Ez a példa azt mutatja be, hogyan végezheti el ezeket a feladatokat a biztonságos paraméterek meghatározásával, ha szükséges, hogy az értékeket a Azure Key Vaultban lehessen tárolni:

* Deklaráljon biztonságos paramétereket a hozzáférés hitelesítéséhez használt értékekhez.
* Ezeket az értékeket a sablon és a munkafolyamat-definíció szintjén is használhatja.
* Adja meg ezeket az értékeket a parameters (paraméterek) fájl használatával.

**Sablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Parameters fájl**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Ajánlott eljárások – munkafolyamat-definíciós paraméterek

Az alábbi ajánlott eljárásokkal gondoskodhat arról, hogy a Logic app Designer helyesen tudja megjeleníteni a munkafolyamat-definíciós paramétereket:

* Adja `defaultValue` meg az attribútumot, amely üres értékeket adhat meg az összes paraméterhez, kivéve a bizalmas vagy a védeni kívánt értékeket.

* Mindig használjon biztonságos paramétereket a felhasználónevek, jelszavak és titkos kódok számára. A bizalmas paraméterek értékeinek elrejtéséhez vagy kivédéséhez kövesse az alábbi témakörökben található útmutatást:

  * [Biztonsági javaslatok a műveleti paraméterekhez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [A paraméterek biztonsági javaslatai a munkafolyamat-definíciókban](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Biztonságos paraméterek értékeinek továbbítása Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

A munkafolyamat-definíciós paraméterekkel kapcsolatos további információkért lásd: [Paraméterek – munkafolyamat-definíciós nyelv](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>A kapcsolatok erőforrás-definíciói

Ha a logikai alkalmazás [felügyelt összekötők](../connectors/apis-list.md)használatával hoz létre és használ kapcsolatokat más szolgáltatásokhoz és rendszerekhez, a sablon `resources` objektuma tartalmazza a kapcsolatok erőforrás-definícióit. Bár a logikai alkalmazásokból hoz létre kapcsolatokat, a kapcsolatok külön Azure-erőforrások, saját erőforrás-definíciókkal rendelkeznek. A kapcsolódási erőforrás-definíciók áttekintéséhez [töltse le a logikai alkalmazást az Azure-ból a Visual studióba](../logic-apps/manage-logic-apps-with-visual-studio.md), amely a legegyszerűbben az üzembe helyezésre kész, érvényes paraméteres Logic app-sablon létrehozása.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

A kapcsolódási erőforrás-definíciók a sablon legfelső szintű paramétereit hivatkozzák az értékekre, így ezeket az értékeket egy paraméter-fájl használatával adhatja meg a központi telepítésben. Győződjön meg arról, hogy a kapcsolatok ugyanazt az Azure-erőforráscsoportot és-helyet használják, mint a logikai alkalmazás.

Az alábbi példa egy Office 365 Outlook-kapcsolat és a hozzá tartozó sablon paramétereinek egy példáját határozza meg:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

A logikai alkalmazás erőforrás-definíciója a következő módokon is együttműködik a kapcsolati erőforrás-definíciókkal:

* A munkafolyamat-definícióban az `parameters` objektum deklarál egy `$connections` paramétert a Logic app Runtime-ban használandó kapcsolati értékekhez. Emellett a kapcsolat létrehozásához használt trigger vagy művelet a paraméteren keresztül továbbított megfelelő értékeket használja `$connections` .

* A munkafolyamat-definíción *kívül* , de továbbra is a logikai alkalmazás erőforrás-definícióján *belül* , egy másik `parameters` objektum állítja be a futásidőben használandó értékeket a `$connections` paraméterhez a megfelelő sablon paramétereinek hivatkozásával. Ezek az értékek a sablon kifejezéseit használják a logikai alkalmazásban található kapcsolatok metaadatait biztonságosan tároló erőforrásokra.

  A metaadatok tartalmazhatnak például kapcsolati karakterláncokat és hozzáférési jogkivonatokat, amelyeket [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)tárolhat. Ha ezeket az értékeket át szeretné adni a sablon paramétereinek, a rendszer a kulcstárolót a sablon által a telepítéskor használt [Parameters fájlban](#template-parameter-files) hivatkozik. A hivatkozó paraméterekkel kapcsolatos különbségekről a témakör későbbi, a [paraméterekre mutató hivatkozások](#parameter-references) című részében olvashat bővebben.

  Ha a logikai alkalmazás munkafolyamat-definícióját kód nézetben nyitja meg a Azure Portal vagy a Visual Studio használatával, az `$connections` objektum a munkafolyamat-definíción kívül, de ugyanazon a szinten jelenik meg. A kód nézetben megjelenő rendezés megkönnyíti ezeket a paramétereket a munkafolyamat-definíció manuális frissítésekor:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* A logikai alkalmazás erőforrás-definíciója egy olyan `dependsOn` objektummal rendelkezik, amely meghatározza a logikai alkalmazás által használt kapcsolatok függőségeit.

A létrehozott kapcsolatok egyedi névvel rendelkeznek az Azure-ban. Ha ugyanahhoz a szolgáltatáshoz vagy rendszerhez több kapcsolatot hoz létre, az egyes kapcsolatok neve egy számmal lesz hozzáfűzve, amely minden létrehozott új kapcsolattal növekszik, például: `office365` , `office365-1` stb.

Ez a példa a logikai alkalmazás erőforrás-definíciója és az Office 365 Outlook kapcsolati erőforrás-definíciója közötti interakciókat mutatja be:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            }
         },
         <other-logic-app-resource-information>,
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Biztonságos kapcsolatok paramétereinek megadása

A bizalmas adatokat, jelszavakat, hozzáférési kulcsokat vagy titkos kódokat kezelő kapcsolati paraméter esetén a kapcsolat erőforrás-definíciója egy olyan objektumot tartalmaz, `parameterValues` amely név-érték párok formátumban adja meg ezeket az értékeket. Ezen információk elrejtéséhez a (z) és a (z) paraméterek használatával deklarálhatja vagy szerkesztheti a sablon paramétereit ezekhez az értékekhez `securestring` `secureobject` . Ezt az információt Ezután [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)tárolhatja. Ha ezeket az értékeket át szeretné adni a sablon paramétereinek, a rendszer a kulcstárolót a sablon által a telepítéskor használt [Parameters fájlban](#template-parameter-files) hivatkozik.

Az alábbi példa egy Azure Blob Storage-kapcsolathoz tartozó fiók nevét és elérési kulcsát adja meg:

**Parameters fájl**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Sablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Kapcsolatok hitelesítése

Az üzembe helyezés után a logikai alkalmazás teljes körűen működik, és érvényes paraméterekkel rendelkezik. A [hitelesítő adatok hitelesítéséhez](../active-directory/develop/authentication-vs-authorization.md)azonban továbbra is engedélyezni kell minden OAuth-kapcsolatot, hogy érvényes hozzáférési jogkivonatokat állítson elő. További információ: OAuth- [kapcsolatok engedélyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Néhány kapcsolat támogatja az [Azure ad-ben regisztrált](../active-directory/develop/quickstart-register-app.md)logikai alkalmazások kapcsolatainak engedélyezését Azure Active Directory (Azure ad) [egyszerű szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md) használatával. Ez a Azure Data Lake kapcsolati erőforrás-definíció például azt mutatja be, hogyan lehet hivatkozni az egyszerű szolgáltatásnév adatait kezelő sablon-paraméterekre, valamint arról, hogy a sablon hogyan deklarálja ezeket a paramétereket:

**Kapcsolati erőforrás definíciója**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Attribútum | Leírás |
|-----------|-------------|
| `token:clientId` | Az egyszerű szolgáltatáshoz társított alkalmazás vagy ügyfél-azonosító |
| `token:clientSecret` | Az egyszerű szolgáltatáshoz társított kulcs értéke |
| `token:TenantId` | Az Azure AD-bérlő címtár-azonosítója |
| `token:grantType` | A kért engedélyezési típus, amelynek a következőnek kell lennie: `client_credentials` . További információ: [Microsoft Identity platform és a OAuth 2,0 Client hitelesítő adatok folyamata](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Sablon paraméterének definíciói**

A sablon legfelső szintű `parameters` objektuma deklarálja ezeket a paramétereket a példában szereplő kapcsolatban:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Az egyszerű szolgáltatásokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Egyszerű szolgáltatásnév létrehozása a Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure-szolgáltatásnév létrehozása Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps)
* [Egyszerű szolgáltatás létrehozása tanúsítvánnyal Azure PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Paraméterekre mutató hivatkozások

A sablon paramétereinek hivatkozásához használhatja a Template [functions](../azure-resource-manager/templates/template-functions.md)kifejezést a telepítéskor kiértékelt sablon-kifejezésekkel. A sablon kifejezései szögletes zárójeleket használnak (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

A munkafolyamat-definíciós paraméterekre való hivatkozáshoz a [munkafolyamat-definíciós nyelv kifejezéseit és függvényeit](../logic-apps/workflow-definition-language-functions-reference.md)használhatja, amelyek kiértékelése futásidőben történik. Észreveheti, hogy a Template függvények és a munkafolyamat-definíciós függvények neve azonos. A munkafolyamat-definíciós kifejezések a "at" szimbólummal kezdődnek ( **@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

A sablon paramétereinek értékeit átadhatja a logikai alkalmazás futtatásához használt munkafolyamat-definícióba. Ne használja azonban a sablon paramétereit, kifejezéseit és szintaxisát a munkafolyamat-definícióban, mert a Logic app Designer nem támogatja a sablon elemeit. Emellett a sablon szintaxisa és kifejezései a kifejezések kiértékelése során felhasználható különbségek miatt megnehezítik a kódokat.

Ehelyett kövesse az alábbi általános lépéseket a munkafolyamat-definíciós paraméterek futtatásához és a futásidőben való használatra való hivatkozáshoz, deklarálja és hivatkozzon a telepítéskor használni kívánt sablon-paraméterekre, és adja meg a központi telepítéshez a paraméter-fájl használatával átadandó értékeket. A részletekért tekintse meg a jelen témakör korábbi, [munkafolyamat-definíció és paraméterek](#workflow-definition-parameters) szakaszát.

1. Hozza létre a sablont, és deklarálja a sablon paramétereit az elfogadáshoz és a használathoz a telepítéskor használandó értékekhez.

1. A munkafolyamat-definícióban deklarálja az elfogadásra és a futásidőben használni kívánt értékek paramétereit. Ezeket az értékeket az egész és a munkafolyamat-definícióban is hivatkozhatjuk.

1. A `parameters` munkafolyamat-definíción *kívüli* , de még a  logikai alkalmazás erőforrás-definíciójában lévő objektumban állítsa be a munkafolyamat-definíciós paraméterek értékeit a megfelelő sablon paramétereinek hivatkozásával. Ily módon átadhatja a sablon paramétereinek értékeit a munkafolyamat-definíciós paraméterekbe.

1. A parameters (paraméterek) fájlban adja meg a sablonnak az üzembe helyezéskor használandó értékeit.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Teljes példa sablonra

Itt látható a témakörben ismertetett paraméteres minta sablon, amelyet a következő példa használ:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Logikaialkalmazás-sablonok létrehozása](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)

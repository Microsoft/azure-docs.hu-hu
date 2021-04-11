---
title: Hozzáférés- és adatvédelem
description: Biztonságos hozzáférés a bemenetekhez, a kimenetekhez, a kérelmeken alapuló triggerekhez, a futtatási előzményekhez, a felügyeleti feladatokhoz és a más erőforrásokhoz való hozzáférés Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla, rarayudu
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 7b082c226b38633d6c34ee2fe4d5227252b2bfcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556383"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Biztonságos hozzáférés és az adatAzure Logic Apps

Azure Logic Apps az [Azure Storage](../storage/index.yml) -ra támaszkodik az adatok tárolására és automatikus [titkosítására](../security/fundamentals/encryption-atrest.md). Ez a titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Alapértelmezés szerint az Azure Storage a Microsoft által felügyelt kulcsokat használja az adatai titkosítására. További információ: az [Azure Storage titkosítása inaktív adatokhoz](../storage/common/storage-service-encryption.md).

A Azure Logic Appsban található bizalmas adatok védelmének szabályozása és védelme érdekében további biztonságot is beállíthat ezekben a területeken:

* [Hozzáférés a kérelmeken alapuló triggerekhez bejövő hívásokhoz](#secure-inbound-requests)
* [Hozzáférés a Logic app-műveletekhez](#secure-operations)
* [Hozzáférés a futtatási előzményekhez bemenetekhez és kimenetekhez](#secure-run-history)
* [Hozzáférés paraméter bemenetekhez](#secure-action-parameters)
* [Hozzáférés a kimenő hívások más szolgáltatásokhoz és rendszerekhez](#secure-outbound-requests)
* [Kapcsolatok létrehozásának tiltása adott összekötők számára](#block-connections)
* [Elkülönítési útmutató a Logic apps szolgáltatásokhoz](#isolation-logic-apps)
* [Azure Logic Apps Azure biztonsági alapterve](../logic-apps/security-baseline.md)

Az Azure-beli biztonsággal kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Az Azure-titkosítás áttekintése](../security/fundamentals/encryption-overview.md)
* [Azure-beli adatok titkosítása – Rest](../security/fundamentals/encryption-atrest.md)
* [Azure-biztonsági teljesítményteszt](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>Hozzáférés a kérelmeken alapuló triggerekhez bejövő hívásokhoz

Bejövő hívások, amelyeket a logikai alkalmazás egy kérelem-alapú triggeren keresztül fogad, például a [kérelem](../connectors/connectors-native-reqres.md) -trigger vagy a [http-webhook](../connectors/connectors-native-webhook.md) -trigger, támogatja a titkosítást, és a [Transport Layer Security (TLS) 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security)-as minimális, korábban SSL (SSL) néven védett. Logic Apps érvényesíti ezt a verziót, ha bejövő hívást fogad a kérelem-triggerre, vagy visszahívást küld a HTTP webhook-triggerre vagy műveletre. Ha TLS-kézfogási hibákat kap, győződjön meg arról, hogy a TLS 1,2-et használja. További információ: [a TLS 1,0-probléma megoldása](/security/solving-tls1-problem).

A bejövő hívások a következő titkosítási csomagokat támogatják:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Az alábbiakban további módszereket is megadhat, amelyekkel korlátozhatja a logikai alkalmazás bejövő hívásait fogadó eseményindítók elérését, így csak a jogosult ügyfelek hívhatják meg a logikai alkalmazást:

* [Közös hozzáférésű aláírások (SAS) előállítása](#sas)
* [Azure Active Directory nyílt hitelesítés engedélyezése (Azure AD OAuth)](#enable-oauth)
* [Logikai alkalmazás közzététele az Azure API Management](#azure-api-management)
* [Bejövő IP-címek korlátozása](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Közös hozzáférésű aláírások (SAS) előállítása

A logikai alkalmazás minden kérelem végpontja [közös hozzáférési aláírással (SAS)](/rest/api/storageservices/constructing-a-service-sas) rendelkezik a végpont URL-címében, amely a következő formátumot követi:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Minden URL-cím tartalmazza a `sp` , a `sv` és a `sig` lekérdezési paramétert a következő táblázatban leírtak szerint:

| Lekérdezési paraméter | Leírás |
|-----------------|-------------|
| `sp` | Megadja az engedélyezett HTTP-metódusok használatára vonatkozó engedélyeket. |
| `sv` | Megadja az aláírás generálásához használandó SAS-verziót. |
| `sig` | Meghatározza az triggerhez való hozzáférés hitelesítéséhez használandó aláírást. Ezt az aláírást a SHA256 algoritmussal hozza létre a rendszer az összes URL-útvonalon és tulajdonságon található titkos hozzáférési kulccsal. Soha nem érhető el vagy nem lett közzétéve, a kulcs titkosítva marad, és a logikai alkalmazás tárolja őket. A logikai alkalmazás csak azokat az eseményindítókat engedélyezi, amelyekben a titkos kulccsal létrehozott érvényes aláírás szerepel. |
|||

A kérések végpontjának bejövő hívásai csak egy engedélyezési sémát használhatnak, vagy a SAS vagy a [Azure Active Directory nyílt hitelesítés](#enable-oauth). Habár az egyik séma használata nem tiltja le a másik sémát, mindkét sémát használva egy hiba miatt a szolgáltatás nem tudja, melyik sémát kell választania.

Az SAS-vel való hozzáférés biztonságossá tételével kapcsolatos további információkért tekintse meg a jelen témakör következő szakaszait:

* [Hozzáférési kulcsok újragenerálása](#access-keys)
* [Lejáró visszahívási URL-címek létrehozása](#expiring-urls)
* [Elsődleges vagy másodlagos kulccsal rendelkező URL-címek létrehozása](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

Ha bármikor új biztonsági hozzáférési kulcsot szeretne előállítani, használja az Azure REST API vagy Azure Portal. Az összes korábban létrehozott, a régi kulcsot használó URL-cím érvénytelenítve lett, és már nincs engedélye a logikai alkalmazás aktiválására. Az újragenerálás után lekért URL-címek az új hozzáférési kulccsal lesznek aláírva.

1. A [Azure Portal](https://portal.azure.com)nyissa meg azt a logikai alkalmazást, amely rendelkezik az újragenerált kulccsal.

1. A logikai alkalmazás menüjében, a **Beállítások** területen válassza a **hozzáférési kulcsok** elemet.

1. Válassza ki azt a kulcsot, amelyet újra szeretne készíteni, majd fejezze be a folyamatot.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Lejáró visszahívási URL-címek létrehozása

Ha egy kérelem-alapú trigger végpontjának URL-címét megosztja más felekkel, létrehozhat olyan visszahívási URL-címeket, amelyek meghatározott kulcsokat használnak, és lejárati dátummal rendelkeznek. Így zökkenőmentesen válthat kulcsokat, vagy korlátozhatja a hozzáférést a logikai alkalmazás aktiválására egy adott TimeSpan alapján. Az URL-cím lejárati dátumának megadásához használja a [Logic Apps REST API](/rest/api/logic/workflowtriggers), például:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A törzsben adja meg a `NotAfter` tulajdonságot egy JSON-dátum karakterlánc használatával. Ez a tulajdonság egy visszahívási URL-címet ad vissza, amely csak a `NotAfter` dátumig és időpontig érvényes.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Elsődleges vagy másodlagos titkos kulccsal rendelkező URL-címek létrehozása

A kérelem alapú trigger visszahívási URL-címeinek létrehozásakor vagy listázásakor megadhatja az URL-cím aláírásához használni kívánt kulcsot. Egy adott kulcs által aláírt URL-cím létrehozásához használja a [Logic Apps REST API](/rest/api/logic/workflowtriggers), például:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

A törzsben adja meg a `KeyType` tulajdonságot a következők egyikével: `Primary` vagy `Secondary` . Ez a tulajdonság a megadott biztonsági kulcs által aláírt URL-címet adja vissza.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Azure Active Directory nyílt hitelesítés engedélyezése (Azure AD OAuth)

A kérelem-alapú trigger által létrehozott végpontra irányuló bejövő hívások esetén engedélyezheti az [Azure ad-OAuth](../active-directory/develop/index.yml) a logikai alkalmazáshoz tartozó engedélyezési szabályzat definiálásával vagy hozzáadásával. Így a bejövő hívások OAuth [hozzáférési jogkivonatokat](../active-directory/develop/access-tokens.md) használnak az engedélyezéshez.

Ha a logikai alkalmazás egy OAuth hozzáférési tokent tartalmazó bejövő kérelmet kap, akkor a Azure Logic Apps szolgáltatás összehasonlítja a jogkivonat jogcímeit az egyes engedélyezési házirendek által meghatározott jogcímekre. Ha egyezés van a jogkivonat jogcímeinek és a legalább egy házirendben szereplő összes jogcím között, akkor a hitelesítés a bejövő kérelem esetében sikeres lesz. A jogkivonat több jogcímet is tartalmazhat, mint az engedélyezési házirend által megadott szám.

#### <a name="considerations-before-you-enable-azure-ad-oauth"></a>Megfontolandó szempontok az Azure AD-OAuth engedélyezése előtt

* A kérelem végpontjának bejövő hívása csak egy engedélyezési sémát használhat, vagy az Azure AD OAuth vagy a [közös hozzáférési aláírást (SAS)](#sas). Habár az egyik séma használata nem tiltja le a másik sémát, mindkét sémát használva egy hiba miatt a Logic Apps szolgáltatás nem tudja, melyik sémát szeretné kiválasztani.

* Csak a [tulajdonos típusú](../active-directory/develop/active-directory-v2-protocols.md#tokens) engedélyezési sémák támogatottak az Azure ad OAuth hozzáférési jogkivonatai esetében, ami azt jelenti, hogy a `Authorization` hozzáférési token fejlécének meg kell adnia a `Bearer` típust.

* A logikai alkalmazás maximális számú engedélyezési házirendre van korlátozva. Az egyes engedélyezési házirendek maximális számú [jogcímet](../active-directory/develop/developer-glossary.md#claim)is tartalmaz. További információ: [Azure Logic apps korlátai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Az engedélyezési házirendnek tartalmaznia kell legalább a **kiállítói** jogcímet, amelynek értéke a `https://sts.windows.net/` vagy `https://login.microsoftonline.com/` a (OAuth v2) karakterrel kezdődik az Azure ad kiállítói azonosítójaként.

  Tegyük fel például, hogy a logikai alkalmazás olyan engedélyezési házirenddel rendelkezik, amely két jogcím-típust, egy **célközönséget** és egy **kiállítót** igényel. A dekódolású hozzáférési tokenek esetében ez a mintavételi [hasznos terület](../active-directory/develop/access-tokens.md#payload-claims) tartalmazza a jogcím típusát is, ahol `aud` a **célközönség** értéke és `iss` a **kiállító** értéke:

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

#### <a name="enable-azure-ad-oauth-for-your-logic-app"></a>Azure AD-OAuth engedélyezése a logikai alkalmazáshoz

Kövesse az alábbi lépéseket a Azure Portal vagy a Azure Resource Manager sablonhoz:

<a name="define-authorization-policy-portal"></a>

#### <a name="portal"></a>[Portál](#tab/azure-portal)

A [Azure Portal](https://portal.azure.com)adjon hozzá egy vagy több engedélyezési szabályzatot a logikai alkalmazáshoz:

1. A [Azure Portalban](https://portal.microsoft.com)keresse meg és nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjének **Beállítások** területén válassza az **Engedélyezés** lehetőséget. Az engedélyezési ablaktábla megnyitása után válassza a **házirend hozzáadása** lehetőséget.

   ![Válassza az "engedélyezés" > "házirend hozzáadása" lehetőséget.](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Adja meg az engedélyezési szabályzattal kapcsolatos információkat úgy, hogy megadja a logikai alkalmazás által az egyes bejövő hívások által a kérelem-trigger által bemutatott hozzáférési jogkivonatban szereplő [jogcím-típusokat](../active-directory/develop/developer-glossary.md#claim) és-értékeket.

   ![Adja meg az engedélyezési házirend adatait](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Házirend neve** | Yes | Az engedélyezési házirendhez használni kívánt név |
   | **Igénylések** | Yes | A logikai alkalmazás által a bejövő hívásokból elfogadott jogcím-típusok és-értékek. A jogcím értéke [legfeljebb karakter hosszúságú](logic-apps-limits-and-config.md#authentication-limits)lehet. Az elérhető jogcím-típusok a következők: <p><p>- **Kibocsátó** <br>- **Célközönség** <br>- **Tulajdonos** <br>- **JWT-azonosító** (JSON web token azonosító) <p><p>A **jogcímek** listájának legalább a **kiállítói** jogcímet tartalmaznia kell, amelynek értéke `https://sts.windows.net/` vagy `https://login.microsoftonline.com/` Az Azure ad kiállító azonosítója. További információ ezekről a jogcím-típusokról: [jogcímek az Azure ad biztonsági jogkivonatokban](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Megadhatja a saját jogcím típusát és értékét is. |
   |||

1. Egy másik Jogcím hozzáadásához válasszon a következő lehetőségek közül:

   * Másik jogcím típusának hozzáadásához válassza a **standard jogcím hozzáadása** lehetőséget, válassza ki a jogcím típusát, és adja meg a jogcím értékét.

   * Saját Jogcím hozzáadásához válassza az **egyéni jogcím hozzáadása** lehetőséget. További információkért lásd: [választható jogcímek megadása az alkalmazáshoz](../active-directory/develop/active-directory-optional-claims.md). A rendszer ezután az egyéni jogcímet a JWT AZONOSÍTÓjának részeként tárolja; például: `"tid": "72f988bf-86f1-41af-91ab-2d7cd011db47"` . 

1. Másik engedélyezési házirend hozzáadásához válassza a **házirend hozzáadása** lehetőséget. Az előző lépések megismétlésével állítsa be a szabályzatot.

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

1. Ha a `Authorization` hozzáférési jogkivonat fejlécét szeretné felvenni a kérelmeken alapuló triggerek kimenetében, tekintse meg az ["engedélyezés" fejléc belefoglalása a kérelem-trigger kimenetei közé](#include-auth-header).


A munkafolyamat-tulajdonságok, például a szabályzatok nem jelennek meg a logikai alkalmazás kódjának nézetében a Azure Portal. Ha programozott módon szeretné elérni a szabályzatokat, hívja meg a következő API-t Azure Resource Manager (ARM) használatával: `https://management.azure.com/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Logic/workflows/{your-workflow-name}?api-version=2016-10-01&_=1612212851820` . Győződjön meg arról, hogy az Azure-előfizetési azonosító, az erőforráscsoport neve és a munkafolyamat neve helyőrző értékeket cseréli ki.


<a name="define-authorization-policy-template"></a>

#### <a name="resource-manager-template"></a>[Resource Manager-sablon](#tab/azure-resource-manager)

Az ARM-sablonban Definiáljon egy engedélyezési házirendet az alábbi lépésekkel és szintaxissal:

1. A `properties` [logikai alkalmazás erőforrás-definíciójának](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)szakaszában adjon hozzá egy objektumot ( `accessControl` Ha nincs ilyen), amely tartalmaz egy `triggers` objektumot.

   További információ az `accessControl` objektumról: a [bejövő IP-címtartományok korlátozása Azure Resource Manager sablonban](#restrict-inbound-ip-template) és a [Microsoft. Logic munkafolyamatok sablonjának referenciája](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. Az `triggers` objektumban adjon hozzá egy `openAuthenticationPolicies` objektumot, amely tartalmazza `policies` azt az objektumot, amelyben egy vagy több engedélyezési házirendet definiál.

1. Adja meg az engedélyezési házirend nevét, állítsa be a házirend típusát `AAD` , és `claims` adjon meg egy tömböt, amelyben meg kell adnia egy vagy több jogcím-típust.

   Legalább a `claims` tömbnek tartalmaznia kell a kiállítói jogcím típusát, amelyben a jogcím tulajdonságát be kell állítani `name` `iss` , és be kell állítani a-t `value` vagy az `https://sts.windows.net/` `https://login.microsoftonline.com/` Azure ad kiállítói azonosítóját. További információ ezekről a jogcím-típusokról: [jogcímek az Azure ad biztonsági jogkivonatokban](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Megadhatja a saját jogcím típusát és értékét is.

1. Ha a `Authorization` hozzáférési jogkivonat fejlécét szeretné felvenni a kérelmeken alapuló triggerek kimenetében, tekintse meg az ["engedélyezés" fejléc belefoglalása a kérelem-trigger kimenetei közé](#include-auth-header).

A következő szintaxist kell követnie:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

---

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>Az "engedélyezés" fejléc belefoglalása a kérelem-trigger kimenetei közé

A [Azure Active Directory nyílt hitelesítéssel (Azure ad OAuth)](#enable-oauth) engedélyező logikai alkalmazások esetében, amelyek engedélyezik a bejövő hívások hozzáférését a kérelmeken alapuló eseményindítók számára, engedélyezheti a kérelem eseményindítóját vagy a http-webhook eseményindító kimenetét, hogy tartalmazza a `Authorization` fejlécet a OAuth hozzáférési jogkivonatból. Az trigger mögöttes JSON-definíciójában adja hozzá és állítsa be a tulajdonságot a következőre: `operationOptions` `IncludeAuthorizationHeadersInOutputs` . Példa a kérelem triggerre:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

További információt az alábbi témakörökben talál:

* [Séma-hivatkozás trigger és Művelettípus esetén – kérelem trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Séma-hivatkozás trigger és Művelettípus esetén – HTTP webhook-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Séma-hivatkozás trigger és Művelettípus esetén – műveleti beállítások](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Logikai alkalmazás közzététele az Azure API Management

Ha további [hitelesítési protokollokat](../active-directory/develop/authentication-vs-authorization.md) szeretne hozzáadni a logikai alkalmazáshoz, érdemes lehet az [Azure API Management](../api-management/api-management-key-concepts.md) szolgáltatást használni. Ez a szolgáltatás lehetővé teszi a logikai alkalmazás API-ként való kihirdetését, és részletes monitorozást, biztonságot, szabályzatot és dokumentációt biztosít bármely végpont számára. A API Management nyilvános vagy privát végpontot tehet közzé a logikai alkalmazás számára. A végponthoz való hozzáférés engedélyezéséhez használhatja az Azure AD-OAuth, az [ügyféltanúsítvány](#client-certificate-authentication)-t vagy más biztonsági szabványokat a hozzáférés engedélyezéséhez. Ha API Management kap egy kérést, a szolgáltatás elküldi a kérést a logikai alkalmazásnak, valamint a szükséges átalakításokat vagy korlátozásokat a módszernek megfelelően. Ahhoz, hogy csak API Management meghívja a logikai alkalmazást, [korlátozhatja a logikai alkalmazás bejövő IP-címeit](#restrict-inbound-ip).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Bejövő IP-címek korlátozása

A közös hozzáférésű aláírás (SAS) mellett érdemes lehet kifejezetten korlátozni a logikai alkalmazást meghívó ügyfeleket is. Ha például az [Azure API Management](../api-management/api-management-key-concepts.md)használatával kezeli a kérelem végpontját, akkor a logikai alkalmazás csak az [ön által létrehozott API Management-szolgáltatás](../api-management/get-started-create-service-instance.md)IP-címéről fogadhatja a kérelmeket.

A megadott IP-címektől függetlenül továbbra is futtathat egy olyan logikai alkalmazást, amely a [Logic Apps REST API: munkafolyamat-eseményindítók futtatására](/rest/api/logic/workflowtriggers/run) vonatkozó kérelem vagy a API Management használatával futtatja a kérelem-alapú eseményindítót. Ebben a forgatókönyvben azonban továbbra is [hitelesítésre](../active-directory/develop/authentication-vs-authorization.md) van szükség az Azure REST API. Az összes esemény megjelenik az Azure naplóban. Ügyeljen rá, hogy ennek megfelelően állítsa be a hozzáférés-vezérlési házirendeket.

A logikai alkalmazás bejövő IP-címeinek korlátozásához hajtsa végre az alábbi lépéseket a Azure Portal vagy a Azure Resource Manager sablonhoz:

<a name="restrict-inbound-ip-portal"></a>

#### <a name="portal"></a>[Portál](#tab/azure-portal)

A [Azure Portalban](https://portal.azure.com)ez a szűrő az eseményindítókat *és* műveleteket is érinti, ellentétben a portál leírásával az **engedélyezett bejövő IP-címek** területen. Ha külön szeretné beállítani ezt a szűrőt az eseményindítók és a műveletek esetében, használja a `accessControl` logikai alkalmazás Azure Resource Manager sablonjában lévő objektumot, vagy a [Logic apps REST API: munkafolyamat-létrehozási vagy frissítési művelet](/rest/api/logic/workflows/createorupdate).

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások** területen válassza a **munkafolyamat-beállítások** elemet.

1. A **hozzáférés-vezérlési konfiguráció** szakasz **engedélyezett bejövő IP-címek** területén válassza ki a forgatókönyv elérési útját:

   * Ha a logikai alkalmazást csak beágyazott logikai alkalmazásként szeretné meghívóként használni a beépített [Azure Logic apps művelettel](../logic-apps/logic-apps-http-endpoint.md), válassza ki a **csak a többi Logic apps** lehetőséget, amely *csak* akkor működik, ha az **Azure Logic apps** művelettel hívja meg a beágyazott logikai alkalmazást.

     Ez a lehetőség üres tömböt ír a logikai alkalmazás erőforrásaiba, és megköveteli, hogy csak a beépített **Azure Logic apps** műveletet használó szülő logikai alkalmazásokból érkező hívások aktiválják a beágyazott logikai alkalmazást.

   * Ha a logikai alkalmazást csak beágyazott alkalmazásként szeretné meghívóként használni a HTTP-művelettel, válassza ki az **adott IP-címtartományok** lehetőséget, *ne* **csak más Logic apps**. Amikor megjelenik az **IP-címtartományok az eseményindítók számára** mezőben, adja meg a szülő logikai alkalmazás [kimenő IP-címeit](../logic-apps/logic-apps-limits-and-config.md#outbound). Egy érvényes IP-címtartomány a következő formátumokat használja: *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x*.

     > [!NOTE]
     > Ha az **egyetlen másik Logic apps** lehetőséget és a http-műveletet használja a beágyazott logikai alkalmazás meghívásához, a hívás le lesz tiltva, és "401 jogosulatlan" hibaüzenet jelenik meg.

   * Olyan esetekben, ahol a más IP-címekről érkező bejövő hívásokat szeretné korlátozni, amikor megjelenik az **IP-címtartományok az eseményindítók számára** mezőben, megadhatja az eseményindító által elfogadott IP-címtartományt. Egy érvényes IP-címtartomány a következő formátumokat használja: *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x*.

1. Ha nem szeretné, hogy a **hívások korlátozása lehetőséggel beolvassa a bemeneti és kimeneti üzeneteket a megadott IP-címekre**, megadhatja azokat az IP-címtartományt a bejövő hívásoknál, amelyek hozzáférhetnek a bemeneti és kimeneti üzenetekhez a futtatási előzményekben.

<a name="restrict-inbound-ip-template"></a>

#### <a name="resource-manager-template"></a>[Resource Manager-sablon](#tab/azure-resource-manager)

Az ARM-sablonban adja meg az engedélyezett bejövő IP-címtartományt a logikai alkalmazás erőforrás-definíciójában a `accessControl` szakasz használatával. Ebben a szakaszban a (, `triggers` ) `actions` és a választható `contents` szakaszokat használja, `allowedCallerIpAddresses` Ha a tulajdonságot tartalmazza, `addressRange` és a tulajdonság értékét az *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x* formátumban adja meg az engedélyezett IP-tartományhoz.

* Ha a beágyazott logikai alkalmazás az **egyetlen másik Logic apps** lehetőséget használja, amely csak a beépített Azure Logic apps műveletet használó logikai alkalmazásoktól engedélyezi a bejövő hívásokat, állítsa a `allowedCallerIpAddresses` tulajdonságot egy üres tömbre (**[]**), és *hagyja* ki a `addressRange` tulajdonságot.

* Ha a beágyazott logikai alkalmazás a **megadott IP-címtartományok** beállítást használja más bejövő hívásokhoz, például a http-műveletet használó más logikai alkalmazásokhoz, adja `allowedCallerIpAddresses` meg a szakaszt, és állítsa a `addressRange` TULAJDONSÁGOT az engedélyezett IP-tartományra.

Ez a példa egy beágyazott logikai alkalmazás erőforrás-definícióját mutatja be, amely csak a beépített Azure Logic Apps műveletet használó logikai alkalmazásokból engedélyezi a bejövő hívásokat:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": []
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               },
               // Optional
               "contents": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

Ez a példa egy olyan beágyazott logikai alkalmazás erőforrás-definícióját mutatja be, amely engedélyezi a bejövő hívásokat a HTTP-műveletet használó logikai alkalmazásokból:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Hozzáférés a Logic app-műveletekhez

Csak meghatározott felhasználók vagy csoportok számára engedélyezheti adott feladatok futtatását, például a Logic apps felügyeletét, szerkesztését és megtekintését. Az engedélyeik szabályozásához használja az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) , hogy testreszabott vagy beépített szerepköröket rendeljen az Azure-előfizetés tagjaihoz:

* [Logic app közreműködő](../role-based-access-control/built-in-roles.md#logic-app-contributor): lehetővé teszi a logikai alkalmazások kezelését, de nem tudja módosítani őket.

* [Logikai alkalmazás kezelője](../role-based-access-control/built-in-roles.md#logic-app-operator): lehetővé teszi a Logic apps olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissítheti őket.

Annak megakadályozása érdekében, hogy mások módosíthassák vagy töröljék a logikai alkalmazást, használhatja az [Azure erőforrás-zárolást](../azure-resource-manager/management/lock-resources.md). Ez a funkció megakadályozza, hogy mások módosíthassák vagy töröljék a termelési erőforrásokat.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Hozzáférés a futtatási előzményekhez

A logikai alkalmazások futtatása során az összes adatok [titkosítva](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) vannak az átvitel során TRANSPORT Layer Security (TLS) és [a nyugalmi](../security/fundamentals/encryption-atrest.md)állapotban. Ha a logikai alkalmazás futása befejeződött, megtekintheti a Futtatás előzményeit, beleértve az egyes műveletek állapotával, időtartamával, bemenetével és kimenetével együtt futtatott lépéseket is. Ez a részletes információk betekintést biztosítanak a logikai alkalmazás futtatásának módjára, valamint az esetlegesen felmerülő problémák elhárítására.

Amikor megtekinti a logikai alkalmazás futási előzményeit, Logic Apps hitelesíti a hozzáférést, majd az egyes futtatásokhoz tartozó kérelmek és válaszok bemeneteit és kimeneteit is tartalmazza. A jelszavakat, titkos kulcsokat vagy más bizalmas adatokat kezelő műveletek esetében azonban meg kell akadályozni, hogy mások megtekintsék és hozzáférjenek az adatokhoz. Ha például a logikai alkalmazás a HTTP-műveletek hitelesítéséhez [Azure Key Vault](../key-vault/general/overview.md) titkos kulcsot kap, akkor a titkos kulcsot el szeretné rejteni a nézetből.

A logikai alkalmazás futtatási előzményeiben található bemenetekhez és kimenetekhez való hozzáférés szabályozásához a következő lehetőségek közül választhat:

* [Korlátozza a hozzáférést az IP-címtartomány alapján](#restrict-ip).

  Ezzel a beállítással biztonságos hozzáférést biztosíthat a futtatási előzményekhez egy adott IP-címtartomány kérései alapján.

* [A futtatási előzményekben lévő adatkezelést a elhomályosítás használatával biztosíthatja](#obfuscate).

  Számos Eseményindítóban és műveletben a logikai alkalmazás futtatási előzményeiben is biztonságossá teheti a bemeneteket, kimeneteket vagy mindkettőt.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP-címtartomány általi hozzáférés korlátozása

Korlátozhatja a logikai alkalmazás futtatási előzményeinek bemeneteit és kimeneteit úgy, hogy csak adott IP-címtartományok kérései megtekinthessék ezeket az adatokat.

Ha például meg szeretné tiltani a bemenetek és kimenetek elérését, adja meg az IP-címtartományt, például: `0.0.0.0-0.0.0.0` . Csak egy rendszergazdai engedélyekkel rendelkező személy távolíthatja el ezt a korlátozást, amely lehetővé teszi, hogy "igény szerinti" hozzáférést biztosítson a logikai alkalmazás adataihoz.

Az engedélyezett IP-címtartományok megadásához hajtsa végre az alábbi lépéseket a Azure Portal vagy a Azure Resource Manager sablonhoz:

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjében, a **Beállítások** területen válassza a **munkafolyamat-beállítások** elemet.

1. A **hozzáférés-vezérlési konfiguráció**  >  **engedélyezett bejövő IP-címek** területen válassza az **adott IP-címtartományok** lehetőséget.

1. A **tartalom IP-címtartományok** területén adja meg azokat az IP-címtartományt, amelyek hozzáférhetnek a tartalomhoz a bemenetek és a kimenetek között.

   Egy érvényes IP-címtartomány a következő formátumokat használja: *x. x. x. x/x* vagy *x. x. x. x-x. x. x. x*

#### <a name="resource-manager-template"></a>[Resource Manager-sablon](#tab/azure-resource-manager)

Az ARM-sablonban adja meg az IP-tartományokat a `accessControl` `contents` logikai alkalmazás erőforrás-definíciójának szakasza alapján, például:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>A futtatási előzményekben tárolt adatvédelem a elhomályosítás használatával

Számos eseményindító és művelet rendelkezik olyan beállításokkal, amelyek a logikai alkalmazás futtatási előzményeiből származó bemeneteket, kimeneteket vagy mindkettőt biztosítják. Mielőtt ezekkel a beállításokkal biztonságossá tenné ezeket az információkat, tekintse át a következő szempontokat:

* Ha eltakarja egy trigger vagy művelet bemeneteit vagy kimeneteit, Logic Apps nem küldi el a védett adatokat az Azure Log Analyticsnak. Emellett nem adhat hozzá [nyomon követett tulajdonságokat](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) az adott triggerhez vagy művelethez a figyeléshez.

* A [munkafolyamat-előzmények kezelésére szolgáló Logic apps API](/rest/api/logic/) nem ad vissza biztonságos kimenetet.

* Ha olyan művelet kimeneteit szeretné biztonságossá tenni, amely nem takarja a bemeneteket, vagy explicit módon eltakarja a kimeneteket, manuálisan kapcsolja be a **biztonságos kimeneteket** az adott műveletben.

* Győződjön meg arról, hogy be van kapcsolva a **biztonságos bemenetek** vagy a **biztonságos kimenetek** az alsóbb rétegbeli műveletekben, ahol a futtatási előzmények az adatok elrejtésére várnak.

  **Biztonságos kimenetek beállítása**

  Ha manuálisan kapcsolja be a **biztonságos kimeneteket** egy triggerben vagy műveletben, Logic apps elrejti ezeket a kimeneteket a futtatási előzményekben. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja ezeket a biztonságos kimeneteket bemenetként, Logic Apps elrejti ezt a műveletet a futtatási előzményekben, de *nem engedélyezi* a művelet **biztonságos bevitelének** beállítását.

  ![A legtöbb művelethez bemenetként és alsóbb rétegbeli hatásként biztosított kimenetek](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Az összeállítási, elemzési JSON-és válasz-műveletek csak a **biztonságos bemenetek** beállítással rendelkeznek. Ha be van kapcsolva, a beállítás ezen műveletek kimeneteit is elrejti. Ha ezek a műveletek explicit módon használják a felsőbb rétegbeli biztonságos kimeneteket bemenetként, Logic Apps elrejti ezeket a műveleteket a bemenetek és a kimenetek között, de *nem engedélyezi* ezeket a műveleteket a " **biztonságos bemenetek** " beállításban. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja a rejtett kimeneteket az összeállítás, az elemzés JSON-vagy a válasz-műveletekből bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemeneteit és kimeneteit*.

  ![Biztonságos kimenetek bemenetként, ha az adott műveletekre gyakorolt hatás van](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Biztonságos bemenetek beállítása**

  Ha manuálisan kapcsolja be a **biztonságos bemeneteket** egy triggerben vagy műveletben, Logic apps elrejti ezeket a bemeneteket a futtatási előzményekben. Ha egy alsóbb rétegbeli művelet explicit módon az adott triggerből vagy műveletből származó látható kimeneteket használ bemenetként, Logic Apps elrejti az alsóbb rétegbeli művelet bemeneteit a futtatási előzményekben, de *nem engedélyezi* a **biztonságos bemeneteket** ebben a műveletben, és nem rejti el a művelet kimeneteit.

  ![A legtöbb művelethez kapcsolódó biztonságos bemenetek és alsóbb rétegbeli hatások](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Ha az összeállítás, a JSON és a Response művelet explicit módon használja az triggerből vagy a biztonságos bemenetekkel rendelkező műveletből származó látható kimeneteket, Logic Apps elrejti ezeket a műveleteket a bemenetek és kimenetek számára, de *nem engedélyezi* a művelet **biztonságos bevitelének** beállítását. Ha egy alsóbb rétegbeli művelet explicit módon felhasználja a rejtett kimeneteket az összeállítás, az elemzés JSON-vagy a válasz-műveletekből bemenetként, Logic Apps *nem rejti el az alsóbb rétegbeli művelet bemeneteit és kimeneteit*.

  ![Biztonságos bemenetek és alsóbb rétegbeli hatás az egyes műveletekre](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Biztonságos bemenetek és kimenetek a tervezőben

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

   ![Logikai alkalmazás megnyitása a Logic app Designerben](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Az trigger vagy művelet esetében, ahol a bizalmas adatokat szeretné védeni, válassza a három pont (**...**) gombot, majd válassza a **Beállítások** lehetőséget.

   ![Trigger vagy művelet beállításainak megnyitása](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Kapcsolja be a **biztonságos bemeneteket**, a **biztonságos kimeneteket** vagy mindkettőt. Ha elkészült, válassza a **Kész** lehetőséget.

   ![A "biztonságos bemenetek" vagy a "biztonságos kimenetek" bekapcsolása](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A művelet vagy az aktiválás mostantól egy lakat ikont jelenít meg a címsorban.

   ![A művelet vagy az trigger címsorában a zárolás ikon látható](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   A korábbi műveletekből származó biztonságos kimeneteket képviselő tokenek a zárolási ikonokat is megjelenítik. Ha például egy ilyen kimenetet választ ki a dinamikus tartalmak listájából egy műveletben való használatra, akkor a jogkivonat zárolási ikont jelenít meg.

   ![Válassza ki a tokent a biztonságos kimenethez](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. A logikai alkalmazás futtatása után megtekintheti a futtatási előzményeket.

   1. A logikai alkalmazás **Áttekintés** paneljén válassza ki a megtekinteni kívánt futtatást.

   1. A **logikai alkalmazás futtatása** ablaktáblán bontsa ki az áttekinteni kívánt műveleteket.

      Ha úgy döntött, hogy mindkét bemenetet és kimenetet elrejti, akkor ezek az értékek már rejtve maradnak.

      ![Rejtett bemenetek és kimenetek a futtatási előzményekben](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Biztonságos bemenetek és kimenetek kód nézetben

Az alapul szolgáló trigger vagy művelet definíciójában adja hozzá vagy frissítse a `runtimeConfiguration.secureData.properties` tömböt a következő értékek egyikével vagy mindkettővel:

* `"inputs"`: A bemenetek védelme a futtatási előzményekben.
* `"outputs"`: A kimenetek védelme a futtatási előzményekben.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Hozzáférés paraméter bemenetekhez

Ha különböző környezetekben végez üzembe helyezést, érdemes parameterizing a munkafolyamat-definícióban szereplő értékeket, amelyek az adott környezettől függően változnak. Így elkerülhetők a rögzített adatok egy [Azure Resource Manager sablonnal](../azure-resource-manager/templates/overview.md) a logikai alkalmazás üzembe helyezéséhez, a bizalmas adatok védelméhez a biztonságos paraméterek meghatározásával, valamint az adatoknak a [sablon paraméterei](../azure-resource-manager/templates/template-parameters.md) között külön bemenetként való továbbítása a [paraméter](../azure-resource-manager/templates/parameter-files.md)használatával.

Ha például a HTTP-műveleteket [Azure Active Directory nyílt hitelesítéssel](#azure-active-directory-oauth-authentication) (Azure ad OAuth) hitelesíti, megadhatja és eltakarhatja azokat a paramétereket, amelyek elfogadják a hitelesítéshez használt ügyfél-azonosítót és az ügyfél titkos kulcsát. Ha ezeket a paramétereket a logikai alkalmazásban szeretné megadni, használja a `parameters` Logic apps munkafolyamat-definíciójának és Resource Manager-sablonjának szakaszát a telepítéshez. A logikai alkalmazás szerkesztése vagy a futtatási előzmények megtekintésekor nem kívánt paraméterek megadásához adja meg a paramétereket a vagy a típus használatával `securestring` , `secureobject` és szükség szerint használja a kódolást. Az ilyen típusú paramétereket nem adja vissza a rendszer az erőforrás-definícióval, és nem érhető el, amikor az üzembe helyezés után megtekinti az erőforrást. A paraméter értékének futtatókörnyezetben való eléréséhez használja a `@parameters('<parameter-name>')` kifejezést a munkafolyamat-definícióban. A kifejezés kiértékelése csak futásidőben történik, és a munkafolyamat- [definíció nyelve](../logic-apps/logic-apps-workflow-definition-language.md)határozza meg.

> [!NOTE]
> Ha a kérelem fejlécében vagy törzsében paramétert használ, akkor ez a paraméter látható lesz a logikai alkalmazás futtatási előzményeinek és a kimenő HTTP-kérések megtekintésekor. Győződjön meg arról, hogy a tartalom-hozzáférési házirendeket is megfelelően állította be.
> A futtatási előzményekben lévő bemenetek és kimenetek elrejtéséhez is használhatja a [elhomályosítás](#obfuscate) lehetőséget. Az engedélyezési fejlécek soha nem láthatók bemeneteken vagy kimeneteken keresztül. Tehát ha van titok, a titkos kód nem kérhető le.

További információt a jelen témakör következő részeiben talál:

* [Biztonságos paraméterek a munkafolyamat-definíciókban](#secure-parameters-workflow)
* [A futtatási előzményekben tárolt adatvédelem a elhomályosítás használatával](#obfuscate)

Ha [Resource Manager-sablonok használatával automatizálja a Logic apps üzembe helyezését](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), megadhatja a biztonságos [sablon paramétereit](../azure-resource-manager/templates/template-parameters.md), amelyek kiértékelése a telepítés során a és a típus használatával történik `securestring` `secureobject` . A sablon paramétereinek definiálásához használja a sablon legfelső szintű `parameters` szakaszát, amely külön és eltér a munkafolyamat-definíciók `parameters` szakaszából. A sablon paramétereinek értékeinek megadásához használjon egy külön [paraméter-fájlt](../azure-resource-manager/templates/parameter-files.md).

Ha például a titkokat használja, megadhatja és használhatja a biztonságos sablon paramétereit, amelyek lekérik ezeket a titkokat [Azure Key Vaultról](../key-vault/general/overview.md) a telepítéskor. Ezután hivatkozhat a Key vaultra és a titkos kulcsra a paraméter fájljában. További információt az alábbi témakörökben talál:

* [Bizalmas értékek átadása az üzembe helyezés során Azure Key Vault használatával](../azure-resource-manager/templates/key-vault-parameter.md)
* [Biztonságos paraméterek Azure Resource Manager-sablonokban](#secure-parameters-deployment-template) a témakör későbbi részében

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Biztonságos paraméterek a munkafolyamat-definíciókban

A logikai alkalmazás munkafolyamat-definíciójában lévő bizalmas adatok védelméhez használjon biztonságos paramétereket, hogy a logikai alkalmazás mentése után ne legyen látható az információ. Tegyük fel például, hogy egy HTTP-művelethez egyszerű hitelesítés szükséges, amely felhasználónevet és jelszót használ. A munkafolyamat-definícióban a `parameters` szakasz a `basicAuthPasswordParam` és a `basicAuthUsernameParam` paramétereket a típus használatával határozza meg `securestring` . A művelet definíciója ezeket a paramétereket a `authentication` szakaszon hivatkozik.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Biztonságos paraméterek Azure Resource Manager-sablonokban

A logikai alkalmazások [Resource Manager-sablonjai](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) több szakaszt is tartalmaz `parameters` . A jelszavak, kulcsok, titkos kódok és egyéb bizalmas adatok védelméhez adja meg a biztonságos paramétereket a sablon szintjén és a munkafolyamat-definíciós szinten a `securestring` vagy a `secureobject` típus használatával. Ezután tárolhatja ezeket az értékeket [Azure Key Vault](../key-vault/general/overview.md) , és a [paraméter fájllal](../azure-resource-manager/templates/parameter-files.md) hivatkozhat a Key vaultra és a titkos kulcsra. A sablon ezután beolvassa ezt az információt a telepítéskor. További információ: [bizalmas értékek átadása az üzembe helyezés során Azure Key Vault használatával](../azure-resource-manager/templates/key-vault-parameter.md).

További információ az alábbi `parameters` fejezetekről:

* A sablon legfelső szintjén egy `parameters` szakasz határozza meg azokat az értékeket, amelyeket a sablon a *telepítéskor* használ. Ezek az értékek például tartalmazhatnak egy adott központi telepítési környezethez tartozó kapcsolódási karakterláncokat is. Ezeket az értékeket külön [paraméteres fájlban](../azure-resource-manager/templates/parameter-files.md)tárolhatja, így az értékek könnyebben módosíthatók.

* A logikai alkalmazás erőforrás-definícióján belül, de a munkafolyamat-definíción kívül egy `parameters` szakasz a munkafolyamat-definíció paramétereinek értékeit határozza meg. Ebben a szakaszban ezeket az értékeket a sablon paramétereit hivatkozó sablon-kifejezések használatával rendelheti hozzá. Ezeket a kifejezéseket a rendszer kiértékeli a telepítéskor.

* A munkafolyamat-definícióban egy `parameters` szakasz határozza meg, hogy a logikai alkalmazás milyen paramétereket használ futásidőben. Ezeket a paramétereket a logikai alkalmazás munkafolyamatán belül hivatkozhat a munkafolyamat-definíciós kifejezések használatával, amelyek kiértékelése futásidőben történik.

Ez a példa több, a típust használó biztonságos paraméter-definícióval rendelkező sablont tartalmaz `securestring` :

| Paraméter neve | Leírás |
|----------------|-------------|
| `TemplatePasswordParam` | A munkafolyamat-definíció paraméterének átadott jelszót fogadó sablon-paraméter `basicAuthPasswordParam` |
| `TemplateUsernameParam` | A munkafolyamat-definíció paraméterének átadandó felhasználónevet megadó sablon-paraméter `basicAuthUserNameParam` |
| `basicAuthPasswordParam` | Egy munkafolyamat-definíciós paraméter, amely egy HTTP-műveletben fogadja el az alapszintű hitelesítéshez szükséges jelszót |
| `basicAuthUserNameParam` | Munkafolyamat-definíciós paraméter, amely elfogadja a felhasználónevet az egyszerű hitelesítéshez egy HTTP-műveletben |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Hozzáférés a kimenő hívások más szolgáltatásokhoz és rendszerekhez

A célként megadott végpont képességei, a [http-trigger vagy http-művelet](../connectors/connectors-native-http.md)által küldött kimenő hívások, a titkosítás támogatása és a [Transport Layer Security (TLS) 1,0, 1,1 vagy 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security), korábbi nevén SSL (SSL) által védettek. Logic Apps egyeztet a célként megadott végponttal az által támogatott legmagasabb lehetséges verzió használatával. Ha például a cél végpont támogatja a 1,2-et, akkor a HTTP-trigger vagy a művelet először a 1,2-et használja. Ellenkező esetben az összekötő a következő legmagasabb támogatott verziót használja.

Az alábbi információk a TLS/SSL önaláírt tanúsítványokról:

* A globális, több-bérlős Azure-környezetben található Logic apps esetében a HTTP-összekötő nem engedélyezi az önaláírt TLS/SSL-tanúsítványokat. Ha a logikai alkalmazás HTTP-hívást kezdeményez egy kiszolgálónak, és egy TLS/SSL önaláírt tanúsítványt jelenít meg, akkor a HTTP-hívás `TrustFailure` hibával meghiúsul.

* Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében a http-összekötő engedélyezi az önaláírt tanúsítványokat a TLS/SSL-kézfogásokhoz. Először is engedélyeznie kell az [önaláírt tanúsítvány-támogatást](../logic-apps/create-integration-service-environment-rest-api.md#request-body) egy meglévő ISE vagy új ISE számára a Logic apps REST API használatával, és a nyilvános tanúsítványt a helyen kell telepíteni `TrustedRoot` .

Az alábbiakban több módon is elvégezheti a logikai alkalmazásból érkező hívásokat kezelő végpontok biztonságossá tételét:

* [Hitelesítés hozzáadása a kimenő kérelmekhez](#add-authentication-outbound).

  Ha a HTTP-trigger vagy a művelet segítségével küld kimenő hívásokat, a logikai alkalmazás által küldött kérelemhez is hozzáadhat hitelesítést. Kiválaszthatja például a következő hitelesítési típusokat:

  * [Alapszintű hitelesítés](#basic-authentication)

  * [Ügyféltanúsítvány-alapú hitelesítés](#client-certificate-authentication)

  * [Active Directory OAuth-hitelesítés](#azure-active-directory-oauth-authentication)

  * [Felügyelt identitás hitelesítése](#managed-identity-authentication)

* A logikai alkalmazás IP-címeitől való hozzáférés korlátozása.

  A Logic apps-végpontokra irányuló összes hívás a logikai alkalmazások régiói alapján megadott kijelölt IP-címekről származik. Hozzáadhat olyan szűrést, amely csak az adott IP-címekről fogad kérelmeket. Ezeknek az IP-címeknek a megszerzéséhez tekintse meg [Azure Logic apps korlátozásait és konfigurációját](logic-apps-limits-and-config.md#firewall-ip-configuration).

* A helyszíni rendszerekkel létesített kapcsolatok biztonságának javítása.

  Azure Logic Apps ezen szolgáltatásokkal való integrációt biztosít a biztonságosabb és megbízható helyszíni kommunikáció biztosításához.

  * Helyszíni adatátjáró

    Azure Logic Apps számos felügyelt összekötő segíti a helyszíni rendszerek, például a fájlrendszer, az SQL, a SharePoint és a DB2 biztonságos kapcsolatait. Az átjáró a helyszíni forrásokból érkező adatokat a Azure Service Bus keresztül küldi el titkosított csatornákon. Az összes forgalom biztonságos kimenő forgalomnak minősül az átjáró ügynöktől. Ismerje meg, [Hogyan működik a helyszíni adatátjáró](logic-apps-gateway-install.md#gateway-cloud-service).

  * Kapcsolódjon az Azure API Management

    Az [Azure API Management](../api-management/api-management-key-concepts.md) helyszíni kapcsolódási lehetőségeket, például helyek közötti virtuális magánhálózat és [ExpressRoute](../expressroute/expressroute-introduction.md) -integrációt biztosít a biztonságos proxyhoz és a helyszíni rendszerekkel való kommunikációhoz. Ha olyan API-val rendelkezik, amely hozzáférést biztosít a helyszíni rendszerhez, és az API-t egy [API Management Service-példány](../api-management/get-started-create-service-instance.md)létrehozásával adta ki, akkor a logikai alkalmazás munkafolyamataiban meghívhatja az API-t úgy, hogy kijelöli a beépített API Management triggert vagy műveletet a Logic app Designerben.

    > [!NOTE]
    > Az összekötő csak azokat a API Management szolgáltatásokat jeleníti meg, amelyeken megtekintésre és csatlakozásra jogosult, de nem jeleníti meg a fogyasztáson alapuló API Management szolgáltatásokat.

    1. A Logic app Designerben írja be `api management` a kifejezést a keresőmezőbe. Válassza ki a lépést attól függően, hogy triggert vagy műveletet ad hozzá:<p>

       * Ha olyan triggert ad hozzá, amely mindig a munkafolyamat első lépése, válassza **Az Azure API Management trigger kiválasztása** lehetőséget.

       * Ha műveletet ad hozzá, válassza **Az Azure API Management művelet kiválasztása** lehetőséget.

       Ez a példa egy triggert szúr be:

       ![Azure API Management trigger hozzáadása](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Válassza ki a korábban létrehozott API Management Service-példányt.

       ![API Management szolgáltatási példány kiválasztása](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Válassza ki a használni kívánt API-hívást.

       ![Meglévő API kiválasztása](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Hitelesítés hozzáadása a kimenő hívásokhoz

A HTTP-és HTTPS-végpontok különböző típusú hitelesítést támogatnak. Egyes eseményindítók és műveletek esetén, amelyeket kimenő hívások vagy kérések küldésére használ a végpontokra, megadhat egy hitelesítési típust. A Logic app Designerben az eseményindítók és a hitelesítési típus kiválasztását támogató műveletek rendelkeznek **hitelesítési** tulajdonsággal. Előfordulhat azonban, hogy ez a tulajdonság nem mindig jelenik meg alapértelmezés szerint. Ezekben az esetekben az triggeren vagy a műveleten nyissa meg az **új paraméter hozzáadása** listát, és válassza a **hitelesítés** lehetőséget.

> [!IMPORTANT]
> A logikai alkalmazás által kezelt bizalmas információk védelme érdekében biztonságos paramétereket használjon, és szükség szerint kódolja az adatokat.
> A paraméterek használatával és biztonságossá tételével kapcsolatos további információkért lásd: [hozzáférés paraméter bemenetekhez](#secure-action-parameters).

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>A hitelesítést támogató eseményindítók és műveletek hitelesítési típusai

Ez a táblázat az eseményindítók és műveletek által elérhető hitelesítési típusokat azonosítja, ahol kiválaszthat egy hitelesítési típust:

| Hitelesítéstípus | Támogatott eseményindítók és műveletek |
|---------------------|--------------------------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + hencegés, HTTP webhook |
| [Ügyféltanúsítvány](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + hencegés, HTTP webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + hencegés, HTTP webhook |
| [Nyers](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + hencegés, HTTP webhook |
| [Kezelt identitás](#managed-identity-authentication) | **Beépített eseményindítók és műveletek** <p><p>Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP webhook <p><p>**Felügyelt összekötők** <p><p>Azure AD Identity Protection, Azure Automation, Azure Container instance, Azure Adatkezelő, Azure Data Factory, Azure Data Lake, Azure Event Grid, Azure IoT Central v3, Azure Key Vault, Azure Resource Manager, Azure Sentinel, HTTP az Azure AD-vel <p><p>**Megjegyzés**: a felügyelt összekötők támogatása jelenleg előzetes verzióban érhető el. |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>Alapszintű hitelesítés

Ha az [alapszintű](../active-directory-b2c/secure-rest-api.md) beállítás elérhető, akkor a következő tulajdonságértékeket kell megadnia:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Yes | Alapszintű | A használni kívánt hitelesítési típus |
| **Felhasználónév** | `username` | Yes | <*Felhasználónév*>| A cél szolgáltatási végponthoz való hozzáférés hitelesítéséhez használt Felhasználónév |
| **Jelszó** | `password` | Yes | <*jelszó*> | A cél szolgáltatási végponthoz való hozzáférés hitelesítéséhez használt jelszó |
||||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), használhat kifejezéseket a paraméterek értékének futtatásához futásidőben. Ez a példa a HTTP-művelet definíciója határozza meg a hitelesítést `type` , `Basic` és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékének lekéréséhez:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

#### <a name="client-certificate-authentication"></a>Ügyféltanúsítvány-alapú hitelesítés

Ha az [ügyféltanúsítvány](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) lehetőség elérhető, akkor a következő tulajdonságértékeket kell megadnia:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Yes | **Ügyféltanúsítvány** <br>vagy <br>`ClientCertificate` | A használni kívánt hitelesítési típus. A tanúsítványokat az [Azure API Management](../api-management/api-management-howto-mutual-certificates.md)segítségével kezelheti. <p></p>**Megjegyzés**: az egyéni összekötők nem támogatják a tanúsítvány alapú hitelesítést a bejövő és a kimenő hívások esetében is. |
| **Pfx** | `pfx` | Yes | <*kódolt-pfx-file-Content*> | A Base64 kódolású tartalom egy személyes információcsere (PFX) fájlból <p><p>A PFX-fájl Base64 kódolású formátumba való átalakításához a következő lépéseket követve használhatja a PowerShellt: <p>1. mentse a tanúsítvány tartalmát egy változóba: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. alakítsa át a tanúsítvány tartalmát a `ToBase64String()` függvény használatával, és mentse a tartalmat egy szövegfájlba: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**Hibaelhárítás**: Ha a parancsot használja `cert mmc/PowerShell` , a következő hibaüzenet jelenhet meg: <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>A hiba elhárításához próbálja meg átkonvertálni a PFX-fájlt egy PEM-fájlba, és vissza újra a `openssl` paranccsal: <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>Ezt követően, amikor a tanúsítvány újonnan átalakított PFX-fájljának Base64 kódolású karakterláncát kapja, a karakterlánc most már Azure Logic Apps működik. |
| **Jelszó** | `password`| No | <*jelszó – pfx-fájl*> | A PFX-fájl eléréséhez használt jelszó |
|||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), használhat kifejezéseket a paraméterek értékének futtatásához futásidőben. Ez a példa a HTTP-művelet definíciója határozza meg a hitelesítést `type` , `ClientCertificate` és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékének lekéréséhez:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

A szolgáltatások ügyféltanúsítvány-alapú hitelesítéssel történő biztonságossá tételével kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Az API-k biztonságának növelése az Azure-beli ügyféltanúsítvány-alapú hitelesítés használatával API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [A háttérbeli szolgáltatások biztonságának növelése az Azure-beli ügyféltanúsítvány-alapú hitelesítés használatával API Management](../api-management/api-management-howto-mutual-certificates.md)
* [A REST-szolgáltatás biztonságának növelése Ügyféltanúsítványok használatával](../active-directory-b2c/secure-rest-api.md)
* [Tanúsítvány hitelesítő adatai az alkalmazás hitelesítéséhez](../active-directory/develop/active-directory-certificate-credentials.md)
* [TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory nyílt hitelesítés

Kéréses eseményindítók esetén [Azure Active Directory nyílt hitelesítés (Azure ad OAuth)](../active-directory/develop/index.yml)használatával hitelesítheti a bejövő hívásokat, miután [beállította az Azure ad-engedélyezési szabályzatokat](#enable-oauth) a logikai alkalmazáshoz. Az **Active Directory OAuth** hitelesítési típust megadó összes más eseményindító és művelet esetében adja meg a következő tulajdonságértékeket:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Yes | **Active Directory OAuth** <br>vagy <br>`ActiveDirectoryOAuth` | A használni kívánt hitelesítési típus. A Logic Apps jelenleg a [OAuth 2,0 protokollt](../active-directory/develop/v2-overview.md)követi. |
| **Authority** | `authority` | No | <*URL-cím-a-Authority-token-kiállító*> | A hozzáférési jogkivonatot biztosító szolgáltató URL-címe. Alapértelmezés szerint ez az érték `https://login.windows.net` . |
| **Bérlő** | `tenant` | Yes | <*Bérlő azonosítója*> | Az Azure AD-bérlő bérlői azonosítója |
| **Célközönség** | `audience` | Yes | <*erőforrás-engedélyezés*> | Az engedélyezéshez használni kívánt erőforrás, például: `https://management.core.windows.net/` |
| **Ügyfél-azonosító** | `clientId` | Yes | <*ügyfél-azonosító*> | Az engedélyezést kérő alkalmazás ügyfél-azonosítója |
| **Hitelesítő adat típusa** | `credentialType` | Yes | Tanúsítvány <br>vagy <br>Titkos | Az ügyfél által a hitelesítés kérelmezéséhez használt hitelesítő adat típusa. Ez a tulajdonság és érték nem jelenik meg a logikai alkalmazás alapjául szolgáló definícióban, de meghatározza a kiválasztott hitelesítőadat-típushoz megjelenő tulajdonságokat. |
| **Titkos** | `secret` | Igen, de csak a "titkos" hitelesítő adatok típusához | <*ügyfél – titok*> | Az engedélyezést kérő ügyfél titka |
| **Pfx** | `pfx` | Igen, de csak a "tanúsítvány" hitelesítő adat típusa esetén | <*kódolt-pfx-file-Content*> | A Base64 kódolású tartalom egy személyes információcsere (PFX) fájlból |
| **Jelszó** | `password` | Igen, de csak a "tanúsítvány" hitelesítő adat típusa esetén | <*jelszó – pfx-fájl*> | A PFX-fájl eléréséhez használt jelszó |
|||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), használhat kifejezéseket a paraméterek értékének futtatásához futásidőben. Ebben a példában a HTTP-művelet definíciója a hitelesítési típust adja meg, `type` `ActiveDirectoryOAuth` a hitelesítő adat pedig a `Secret` [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékeinek lekéréséhez:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

#### <a name="raw-authentication"></a>Nyers hitelesítés

Ha a **RAW** beállítás elérhető, akkor használhatja ezt a hitelesítési típust, ha olyan [hitelesítési sémákat](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) kell használnia, amelyek nem követik a [OAuth 2,0 protokollt](https://oauth.net/2/). Ezzel a típussal manuálisan hozza létre a kimenő kérelemmel elküldött engedélyezési fejléc értékét, és adja meg a fejléc értékét az triggerben vagy a műveletben.

Íme például egy minta fejléc egy HTTPS-kérelemhez, amely a [OAuth 1,0 protokollt](https://tools.ietf.org/html/rfc5849)követi:

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

A nyers hitelesítést támogató triggerben vagy műveletben a következő tulajdonságértékeket kell megadnia:

| Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
|---------------------|-----------------|----------|-------|-------------|
| **Hitelesítés** | `type` | Yes | Nyers | A használni kívánt hitelesítési típus |
| **Érték** | `value` | Yes | <*engedélyezés – fejléc-érték*> | A hitelesítéshez használandó engedélyezési fejléc értéke |
||||||

Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), használhat kifejezéseket a paraméterek értékének futtatásához futásidőben. Ez a példa a HTTP-művelet definíciója határozza meg a hitelesítést `type` `Raw` , és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékeinek lekéréséhez:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

#### <a name="managed-identity-authentication"></a>Felügyelt identitás hitelesítése

Ha a [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) beállítás elérhető a [felügyelt identitás hitelesítését támogató triggeren vagy műveleten](#add-authentication-outbound), a logikai alkalmazás a rendszer által hozzárendelt identitást vagy *egy manuálisan létrehozott* , felhasználó által hozzárendelt identitást használhat a Azure Active Directory (Azure ad) által védett Azure-erőforrásokhoz való hozzáférés hitelesítéséhez, nem pedig a hitelesítő adatokat, a titkokat vagy az Azure ad-jogkivonatokat. Az Azure kezeli ezt az identitást, és segít a hitelesítő adatok védelmében, mivel nem rendelkezik a titkok kezelésével, vagy közvetlenül nem használja az Azure AD-jogkivonatokat. További információ az Azure [AD-hitelesítés felügyelt identitásait támogató Azure-szolgáltatásokról](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Mielőtt a logikai alkalmazás felügyelt identitást tud használni, kövesse az [Azure-erőforrásokhoz való hozzáférés hitelesítése a Azure Logic apps felügyelt identitások használatával](../logic-apps/create-managed-service-identity.md)című témakör lépéseit. Ezekkel a lépésekkel engedélyezheti a felügyelt identitást a logikai alkalmazáson, és beállíthatja az identitás hozzáférését a cél Azure-erőforráshoz.

1. Mielőtt egy Azure-függvény felügyelt identitást használhat, először [engedélyezze az Azure functions hitelesítést](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

1. A felügyelt identitást támogató triggerben vagy műveletben adja meg a következő információkat:

   **Beépített eseményindítók és műveletek**

   | Tulajdonság (Designer) | Tulajdonság (JSON) | Kötelező | Érték | Leírás |
   |---------------------|-----------------|----------|-------|-------------|
   | **Hitelesítés** | `type` | Yes | **Felügyelt identitás** <br>vagy <br>`ManagedServiceIdentity` | A használni kívánt hitelesítési típus |
   | **Felügyelt identitás** | `identity` | Yes | * **Rendszerhez rendelt felügyelt identitás** <br>vagy <br>`SystemAssigned` <p><p>* <*felhasználó által hozzárendelt identitás-név*> | A használni kívánt felügyelt identitás |
   | **Célközönség** | `audience` | Yes | <*cél – erőforrás-azonosító*> | Az elérni kívánt cél erőforráshoz tartozó erőforrás-azonosító. <p>Például `https://storage.azure.com/` az összes Storage-fiók esetében érvényes lesz a [hozzáférési tokenek](../active-directory/develop/access-tokens.md) hitelesítése. Megadhat azonban egy gyökérszintű szolgáltatás URL-címét is, például `https://fabrikamstorageaccount.blob.core.windows.net` egy adott Storage-fiókhoz. <p>**Megjegyzés**: Előfordulhat, hogy a **célközönség** tulajdonság egyes eseményindítókban vagy műveletekben el van rejtve. A tulajdonság láthatóvá tételéhez az triggerben vagy a műveletben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **célközönség** lehetőséget. <p><p>**Fontos**: Ügyeljen arra, hogy a célként megadott erőforrás-azonosító *pontosan EGYEZZEN* az Azure ad által várt értékkel, beleértve a szükséges záró perjeleket is. Így az `https://storage.azure.com/` összes Azure Blob Storage-fiók erőforrás-azonosítója záró perjelet igényel. Egy adott Storage-fiók erőforrás-azonosítója azonban nem igényel záró perjelet. Az erőforrás-azonosítók megkereséséhez tekintse meg az [Azure ad-t támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Ha [biztonságos paramétereket](#secure-action-parameters) használ a bizalmas adatok kezelésére és védelmére, például egy [Azure Resource Manager sablonban az üzembe helyezés automatizálásához](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), használhat kifejezéseket a paraméterek értékének futtatásához futásidőben. Ez a HTTP-műveleti definíció például meghatározza a hitelesítést `type` , `ManagedServiceIdentity` és a [Parameters () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#parameters) használja a paraméterek értékének beolvasásához:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

   **Felügyelt összekötő-eseményindítók és műveletek**

   | Tulajdonság (Designer) | Kötelező | Érték | Leírás |
   |---------------------|----------|-------|-------------|
   | **Kapcsolatok neve** | Yes | <*kapcsolattípus*> ||
   | **Kezelt identitás** | Yes | **Rendszer által hozzárendelt felügyelt identitás** <br>vagy <br> <*felhasználó által hozzárendelt – felügyelt azonosító – név*> | A használni kívánt hitelesítési típus |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Kapcsolatok létrehozásának tiltása

Ha a szervezete nem engedélyezi a kapcsolódást bizonyos erőforrásokhoz a Azure Logic Apps összekötői segítségével, [letilthatja, hogy a](../logic-apps/block-connections-connectors.md) logikai alkalmazások munkafolyamataiban lévő összekötők a [Azure Policy](../governance/policy/overview.md)használatával képesek legyenek a kapcsolatok létrehozására. További információ: [adott összekötők által létrehozott kapcsolatok blokkolása a Azure Logic Appsban](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Elkülönítési útmutató a Logic apps szolgáltatásokhoz

A Azure Logic Apps [Azure Government](../azure-government/documentation-government-welcome.md) a [Azure Government Impact Level 5 elkülönítési](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) útmutatója és az [USA védelmi Minisztériumának Cloud Computing biztonsági követelményeinek útmutatója (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html)című témakörben ismertetett régiókban lévő összes Impact szintjének támogatásához használhatja. A követelmények teljesítése érdekében a Logic Apps támogatja a munkafolyamatok létrehozását és futtatását egy dedikált erőforrásokkal rendelkező környezetben, így csökkentheti a teljesítményre gyakorolt hatást más Azure-bérlők által a logikai alkalmazásokban, és elkerülheti a számítási erőforrások más Bérlővel való megosztásának lehetőségét.

* Saját kód futtatásához vagy XML-transzformáció végrehajtásához [hozzon létre és hívjon fel egy Azure-függvényt, és](../logic-apps/logic-apps-azure-functions.md)ne használja a [beágyazott kód](../logic-apps/logic-apps-add-run-inline-code.md) funkcióját, vagy adja [meg a térképként használandó szerelvényeket](../logic-apps/logic-apps-enterprise-integration-maps.md). Az elkülönítési követelményeknek való megfelelés érdekében állítsa be a Function alkalmazás üzemeltetési környezetét is.

  Például az 5. szintű követelmények teljesítéséhez hozzon létre egy Function alkalmazást az [app Service terv](../azure-functions/dedicated-plan.md) használatával az [ **elkülönített** díjszabási szinttel](../app-service/overview-hosting-plans.md) , valamint egy olyan [app Service Environmenttal](../app-service/environment/intro.md) , amely az **elkülönített** árképzési szintet is használja. Ebben a környezetben a Function apps dedikált Azure-beli virtuális gépeken és dedikált Azure-beli virtuális hálózatokon fut, amelyek hálózati elkülönítést biztosítanak az alkalmazások számítási elkülönítéséhez és a maximális kibővíthető képességekhez. További információ: [Azure Government Impact Level 5 elkülönítési útmutató – Azure functions](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  További információt az alábbi témakörökben talál:<p>

  * [Azure App Service csomagok](../app-service/overview-hosting-plans.md)
  * [Az Azure Functions hálózatkezelési lehetőségei](../azure-functions/functions-networking-options.md)
  * [Azure dedikált gazdagépek virtuális gépekhez](../virtual-machines/dedicated-hosts.md)
  * [Virtuális gépek elkülönítése az Azure-ban](../virtual-machines/isolation.md)
  * [Dedikált Azure-szolgáltatások üzembe helyezése virtuális hálózatokon](../virtual-network/virtual-network-for-azure-services.md)

* A dedikált erőforrásokon futó logikai alkalmazások létrehozásához, valamint az Azure-beli virtuális hálózat által védett erőforrásokhoz való hozzáféréshez létrehozhat egy [integrációs szolgáltatási környezetet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Néhány Azure-beli virtuális hálózat privát végpontokat ([Azure Private-hivatkozást](../private-link/private-link-overview.md)) használ az Azure-beli (Azure-beli) szolgáltatások, például az Azure Storage, a Azure Cosmos db vagy a Azure SQL Database, a partner Services vagy az Azure-ban üzemeltetett ügyfélszolgálatok számára. Ha a logikai alkalmazásoknak hozzá kell férniük a privát végpontokat használó virtuális hálózatokhoz, létre kell hoznia, telepítenie és futtatnia kell ezeket a logikai alkalmazásokat egy ISE-ben.

  * Az Azure Storage által használt titkosítási kulcsok részletesebb szabályozásához [Azure Key Vault](../key-vault/general/overview.md)használatával állíthatja be, használhatja és kezelheti a saját kulcsát. Ezt a képességet "Bring Your Own Key" (BYOK) néven is nevezik, és a kulcs neve "ügyfél által felügyelt kulcs". További információ: [az ügyfél által felügyelt kulcsok beállítása az inaktív adatok titkosításához az integrációs szolgáltatási környezetekhez (ISEs) Azure Logic Appsban](../logic-apps/customer-managed-keys-integration-service-environment.md).

További információt az alábbi témakörökben talál:

* [Elkülönítés az Azure nyilvános felhőben](../security/fundamentals/isolation-choices.md)
* [Fokozottan kényes IaaS-alkalmazások biztonsága az Azure-ban](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Következő lépések

* [Azure Logic Apps Azure biztonsági alapterve](../logic-apps/security-baseline.md)
* [Üzembe helyezés automatizálása Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Logikai alkalmazások figyelése](../logic-apps/monitor-logic-apps-log-analytics.md)

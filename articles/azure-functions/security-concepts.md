---
title: Azure Functions biztonságossá tétele
description: Ismerje meg, hogyan teheti meg az Azure-ban futó funkció kódját az általános támadásokkal szemben.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 351bdca7ff94b6c058b5ab62fd9c16d707e7dc78
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368489"
---
# <a name="securing-azure-functions"></a>Azure Functions biztonságossá tétele

A kiszolgáló nélküli függvények biztonságos fejlesztésének, üzembe helyezésének és működésének megtervezése számos szempontból ugyanaz, mint bármely webalapú vagy felhőben üzemeltetett alkalmazás esetében. [Azure app Service](../app-service/index.yml) biztosítja az üzemeltetési infrastruktúrát a Function apps-alkalmazásokhoz. Ez a cikk biztonsági stratégiákat biztosít a függvény kódjának futtatásához, valamint arról, hogy App Service hogyan segíthet a függvények biztonságossá tételében. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Az [Azure biztonsági teljesítménytesztet](../security/benchmarks/overview.md)követő biztonsági javaslatokért lásd: [Azure functions Azure biztonsági alapterve](security-baseline.md).

## <a name="secure-operation"></a>Biztonságos művelet 

Ez a szakasz a Function alkalmazás lehető legbiztonságosabb konfigurálását és futtatását ismerteti. 

### <a name="security-center"></a>Security Center

A Security Center a portálon integrálható a Function alkalmazással. A szolgáltatás ingyenes, gyors értékelést nyújt a konfigurációval kapcsolatos lehetséges biztonsági rések biztonságáról. A dedikált csomagban futtatott functions-alkalmazások felár ellenében a Security Center valós idejű biztonsági funkcióit is használhatják. További információ: [a Azure app Service webalkalmazások és API-k elleni védelem](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Naplózás és monitorozás

A támadások észlelésének egyik módja a tevékenység-figyelési tevékenység és a naplózási elemzés. A függvények integrálva vannak a Application Insights a Function alkalmazás napló-, teljesítmény-és hibajelentési adatainak gyűjtésére. Application Insights automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a függvények használatának megismerésében. További információért lásd: [Azure functions figyelése](functions-monitoring.md).

A functions a Azure Monitor-naplók integrálásával is lehetővé teszi, hogy a rendszer eseményeivel összevonja a Function app-naplókat a könnyebb elemzés érdekében. A diagnosztikai beállításokkal konfigurálhatja a függvények és mérőszámok adatfolyamként történő exportálását az Ön által választott célra, például egy naplók Analytics-munkaterületre. További információ: [Azure functions figyelése Azure monitor naplókkal](functions-monitor-log-analytics.md). 

A vállalati szintű fenyegetések észlelése és a reagálás automatizálása érdekében a naplók és az események továbbítása egy naplók Analytics-munkaterületre. Ezután ehhez a munkaterülethez kapcsolódhat az Azure Sentinel használatával. További információ: [Mi az az Azure Sentinel](../sentinel/overview.md).  

A megfigyelhető biztonsági javaslatokért tekintse meg a [Azure functions Azure biztonsági alaptervét](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>HTTPS megkövetelése

Alapértelmezés szerint a-ügyfelek HTTP-vagy HTTPS-kapcsolaton keresztül is csatlakozhatnak a függvény-végpontokhoz. A HTTP-t HTTPs-re kell átirányítani, mivel a HTTPS SSL/TLS protokollt használ a biztonságos kapcsolat biztosításához, amely titkosítva és hitelesítve van. További információ: [https kikényszerítés](../app-service/configure-ssl-bindings.md#enforce-https).

Ha HTTPS-re van szüksége, a legújabb TLS-verziót is meg kell követelnie. További információ: a [TLS-verziók betartatása](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

További információ: [Secure Connections (TSL)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Függvény-hozzáférési kulcsok

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Rendszerkulcs 

Előfordulhat, hogy a bővítmények rendszerfelügyelt kulcsot igényelnek a webhook-végpontok eléréséhez. A rendszerkulcsok olyan bővítmény-specifikus függvény-végpontokhoz lettek tervezve, amelyeket belső összetevők hívnak meg. A [Event Grid trigger](functions-bindings-event-grid-trigger.md) például megköveteli, hogy az előfizetés rendszerkulcsot használjon az trigger végpontjának meghívásakor. A Durable Functions a rendszerkulcsokat is használja a [tartós feladatok bővítmény API](durable/durable-functions-http-api.md)-k hívásához. 

A rendszerkulcsok hatókörét a bővítmény határozza meg, de általában a teljes Function alkalmazásra vonatkozik. A rendszerkulcsokat csak bizonyos bővítmények hozhatják létre, és nem lehet explicit módon beállítani az értékeket. A többi kulcshoz hasonlóan új értéket is létrehozhat a kulcshoz a portálon vagy a fő API-k használatával.

#### <a name="keys-comparison"></a>Kulcsok összehasonlítása

A következő táblázat a különböző típusú hozzáférési kulcsok használatát hasonlítja össze:

| Művelet                                        | Hatókör                    | Érvényes kulcsok         |
|-----------------------------------------------|--------------------------|--------------------|
| Függvény végrehajtása                            | Adott függvény        | Függvény           |
| Függvény végrehajtása                            | Bármely függvény             | Függvény vagy gazdagép   |
| Rendszergazdai végpont meghívása                        | Függvényalkalmazás             | Gazdagép (csak Master) |
| Tartós feladat-kiterjesztési API-k hívása              | <sup>1</sup> . funkció alkalmazás | <sup>2</sup> . System |
| Bővítmény-specifikus webhook meghívása (belső) | <sup>1</sup> . funkció alkalmazás | <sup>2</sup> . System |

<sup>1</sup> A kiterjesztés által meghatározott hatókör.  
<sup>2</sup> A kiterjesztés által meghatározott nevek.

A hozzáférési kulcsokkal kapcsolatos további tudnivalókért tekintse meg a [http-trigger kötését ismertető cikket](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Titkos adattárak

Alapértelmezés szerint a kulcsok tárolása blob Storage-tárolóban történik a beállítás által biztosított fiókban `AzureWebJobsStorage` . Adott Alkalmazásbeállítások használatával felülbírálhatja ezt a viselkedést, és más helyen tárolhatja a kulcsokat.

|Hely  |Beállítás | Érték | Leírás  |
|---------|---------|---------|---------|
|Eltérő Storage-fiók     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Egy második Storage-fiók blob Storage-tárolójában tárolja a kulcsokat a megadott SAS URL-cím alapján. A kulcsok titkosítva vannak, mielőtt a rendszer a Function-alkalmazáshoz tartozó titkos kulcs használatával tárolja őket. |
|Fájlrendszer   | `AzureWebJobsSecretStorageType`   |  `files`       | A kulcsok megmaradnak a fájlrendszerben, és a rendszer titkosítja a tárterületet a Function alkalmazás titkos kódjának egyedi titkát használva. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | A tárolónak rendelkeznie kell egy olyan hozzáférési házirenddel, amely megfelel az üzemeltetési erőforrás rendszerhez rendelt felügyelt identitásának. A hozzáférési házirendnek a következő titkos engedélyeket kell megadnia a személyazonossághoz:,, `Get` `Set` `List` és `Delete` . <br/>Helyi futtatásakor a rendszer a fejlesztői identitást használja, és a beállításoknak a [ fájllocal.settings.js](functions-run-local.md#local-settings-file)kell lenniük. | 
|A Kubernetes titkos kódjai  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (nem kötelező) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Csak akkor támogatott, ha a functions futtatókörnyezetet futtatja a Kubernetes-ben. Ha `AzureWebJobsKubernetesSecretName` nincs beállítva, a tárház írásvédettnak minősül. Ebben az esetben az értékeket az üzembe helyezés előtt kell létrehozni. A Azure Functions Core Tools automatikusan hozza létre az értékeket a Kubernetes való üzembe helyezéskor.|

### <a name="authenticationauthorization"></a>Hitelesítés/engedélyezés

Míg a funkcióbillentyűk bizonyos enyhítő megoldást biztosíthatnak a nemkívánatos hozzáféréshez, a függvény-végpontok valódi biztonságossá tételének egyetlen módja a függvények elérését biztosító ügyfelek pozitív hitelesítésének megvalósítása. Ezután az identitás alapján teheti meg az engedélyezési döntéseket.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Engedélyek

Mint bármely alkalmazás vagy szolgáltatás esetében, a cél a lehető legalacsonyabb engedélyekkel futtatja a Function alkalmazást. 

#### <a name="user-management-permissions"></a>Felhasználói kezelési engedélyek

A functions támogatja a beépített [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/overview.md). A függvények által támogatott Azure-szerepkörök a [közreműködő](../role-based-access-control/built-in-roles.md#contributor), a [tulajdonos](../role-based-access-control/built-in-roles.md#owner)és az [olvasó](../role-based-access-control/built-in-roles.md#owner). 

Az engedélyek a Function app szintjén érvényesek. A közreműködő szerepkörnek a legtöbb alkalmazás-szintű feladat végrehajtásához kell tartoznia. Csak a tulajdonosi szerepkör törölheti a Function alkalmazást. 

#### <a name="organize-functions-by-privilege"></a>Függvények rendszerezése jogosultság alapján 

A (z) alkalmazás beállításaiban tárolt kapcsolatok és egyéb hitelesítő adatok lehetővé teszi, hogy a Function alkalmazás összes funkciója ugyanazokat az engedélyeket adja meg a társított erőforrásban. Érdemes lehet minimalizálni az adott hitelesítő adatokhoz hozzáféréssel rendelkező függvények számát azáltal, hogy áthelyezi azokat a függvényeket, amelyek nem használják ezeket a hitelesítő adatokat egy különálló Function alkalmazásnak. Mindig használhat olyan technikákat, mint például a függvények [láncolása](/learn/modules/chain-azure-functions-data-using-bindings/) , hogy az adatok áthaladnak a függvények között különböző Function Apps-alkalmazásokban.  

#### <a name="managed-identities"></a>Felügyelt identitások

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

A felügyelt identitások bizonyos eseményindítók és kötések esetén a titkos kulcsok helyett is használhatók. Lásd: [identitás-alapú kapcsolatok](#identity-based-connections).

További információ: a [felügyelt identitások használata app Service és Azure Functionshoz](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>CORS-hozzáférés korlátozása

Az eltérő [eredetű erőforrások megosztása (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) lehetővé teszi, hogy egy másik tartományban futó webalkalmazások a http-trigger végpontokra irányuló kérelmeket tegyenek elérhetővé. A App Service beépített támogatást nyújt a szükséges CORS-fejlécek HTTP-kérelmekben való átadásához. A CORS szabályok definiálva vannak a függvény alkalmazási szintjén.  

Habár a csábító olyan helyettesítő karakter használatát, amely lehetővé teszi, hogy az összes hely hozzáférjen a végponthoz. Ez azonban a CORS célját eredményezi, ami segít megakadályozni a helyek közötti parancsfájlok elleni támadásokat. Ehelyett adjon hozzá egy külön CORS-bejegyzést az egyes webalkalmazások tartományához, amelyeknek hozzá kell férniük a végponthoz. 

### <a name="managing-secrets"></a>Titkok kezelése 

Ahhoz, hogy csatlakozni tudjon a különböző szolgáltatásokhoz és erőforrásokhoz a kód futtatásához, az alkalmazásoknak képesnek kell lenniük a titkos kulcsokhoz való hozzáférésre, például a kapcsolati karakterláncok és a szolgáltatási kulcsok elérésére. Ez a szakasz azt ismerteti, hogyan tárolhatók a függvények által igényelt titkok.

Soha ne tárolja a titkokat a függvény kódjában. 

#### <a name="application-settings"></a>Alkalmazásbeállítások

Alapértelmezés szerint a Function app és a kötések alkalmazás-beállításokként tárolja a kapcsolati karakterláncokat és a titkos kulcsokat. Így ezek a hitelesítő adatok a függvény kódjához és a függvény által használt különféle kötésekhez is elérhetővé válnak. Az Alkalmazásbeállítások (Key) neve a tényleges érték beolvasására szolgál, amely a titok. 

Például minden Function alkalmazáshoz hozzá kell rendelni egy társított Storage-fiókot, amelyet a futtatókörnyezet használ. Alapértelmezés szerint a rendszer a Storage-fiókhoz való kapcsolódást egy nevű alkalmazás-beállításban tárolja `AzureWebJobsStorage` .

Az Alkalmazásbeállítások és a kapcsolatok karakterláncai titkosítva tárolódnak az Azure-ban. A visszafejtésük csak az alkalmazás folyamatának memóriába való beadása előtt történik meg, amikor az alkalmazás elindul. A titkosítási kulcsok elforgatása rendszeresen történik. Ha inkább a titkos kulcsok biztonságos tárolását szeretné felügyelni, az alkalmazás beállításának Ehelyett a Azure Key Vaultra kell hivatkoznia. 

A beállításokat a helyi számítógépen lévő függvények fejlesztésekor alapértelmezés szerint a local.settings.jsfájlban is titkosíthatja. További információért tekintse meg a `IsEncrypted` tulajdonságot a [helyi beállítások fájlban](functions-run-local.md#local-settings-file).  

#### <a name="key-vault-references"></a>Key Vault referenciák

Noha az Alkalmazásbeállítások a legtöbb függvényhez elegendőek, érdemes lehet ugyanazokat a titkokat több szolgáltatás között megosztani. Ebben az esetben a titkok redundáns tárolása nagyobb potenciális biztonsági réseket eredményez. A biztonságosabb megközelítés egy központi titkos tárolási szolgáltatás, amely a titkok helyett a szolgáltatásra mutató hivatkozásokat használ.      

A [Azure Key Vault](../key-vault/general/overview.md) egy olyan szolgáltatás, amely központosított titkok felügyeletét teszi lehetővé a hozzáférési házirendek és a naplózási előzmények teljes körű szabályozásával. Az alkalmazás beállításai között használhat egy Key Vault hivatkozást a kapcsolatok karakterláncának vagy kulcsának helyén. További információ: [Key Vault referenciák használata app Service és Azure Functionshoz](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="identity-based-connections"></a>Identitás-alapú kapcsolatok

Az identitások felhasználhatók a titkok helyett az egyes erőforrásokhoz való csatlakozáshoz. Ennek az az előnye, hogy nem követeli meg a titkos kód kezelését, és részletesebb hozzáférés-vezérlést és naplózást biztosít. 

Ha olyan kódot ír, amely létrehozza az [Azure ad-hitelesítést támogató Azure-szolgáltatásokhoz](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)való kapcsolódást, akkor a titkos vagy a kapcsolódási karakterlánc helyett identitást is használhat. Mindkét kapcsolati módszer részleteit az egyes szolgáltatások dokumentációja tartalmazza.

Egyes Azure Functions triggerek és kötési bővítmények identitás-alapú kapcsolat használatával konfigurálhatók. Napjainkban ez magában foglalja az [Azure Blob](./functions-bindings-storage-blob.md) és az [Azure üzenetsor](./functions-bindings-storage-queue.md) -bővítmények használatát. A bővítmények identitás használatára való konfigurálásával kapcsolatos további információkért lásd: az [identitás-alapú kapcsolatok használata a Azure Functionsban](./functions-reference.md#configure-an-identity-based-connection).

### <a name="set-usage-quotas"></a>Használati kvóták beállítása

Érdemes lehet használati kvótát beállítani egy felhasználási tervben futó függvényekhez. Ha a Function alkalmazásban a függvények teljes végrehajtásához a napi GB-s korlátot állítja be, a végrehajtás leáll, ha eléri a korlátot. Ez potenciálisan segíthet enyhíteni a függvényeket végrehajtó kártékony kódok ellen. A függvények felhasználásának becsléséről a [felhasználási terv költségeinek becslése](functions-consumption-costs.md)című témakörben olvashat. 

### <a name="data-validation"></a>Adatkiértékelés

A függvények által használt eseményindítók és kötések nem biztosítanak további adatellenőrzést. A kódnak a triggertől vagy a bemeneti kötéstől kapott adatokat kell érvényesíteni. Ha egy felsőbb rétegbeli szolgáltatás biztonsága sérül, nem szeretné, hogy az érvényesített bemenetek át legyenek hajtva a függvények között. Ha például a függvény egy Azure Storage-üzenetsor adatait tárolja egy kapcsolódó adatbázisban, ellenőriznie kell az adatait, és parametrizálja kell a parancsokat az SQL-injektálási támadások elkerülése érdekében. 

Ne Tételezzük fel, hogy a függvénybe érkező adat már ellenőrizve lett vagy megtisztítva. Érdemes ellenőrizni azt is, hogy a kimeneti kötéseknek írt adatokat érvényesek-e. 

### <a name="handle-errors"></a>Hibakezelés

Habár az alapszintű, fontos, hogy jó hibakezelés legyen a függvényeknél. Nem kezelt hibák a gazdagépen, és a futtatókörnyezet kezeli őket. A különböző kötések eltérő módon kezelik a hibák feldolgozását. További információ: [Azure functions hibakezelés](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Távoli hibakeresés letiltása

Győződjön meg arról, hogy a távoli hibakeresés le van tiltva, kivéve, ha aktívan végzi a függvények hibakeresését. A távoli hibakeresést letilthatja a Function app **konfigurációjának** **általános beállítások** lapján a portálon. 

### <a name="restrict-cors-access"></a>CORS-hozzáférés korlátozása

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Ne használjon helyettesítő karaktereket az engedélyezett Origins listán. Ehelyett sorolja fel azokat a tartományokat, amelyekről a kérelmeket várta.

### <a name="store-data-encrypted"></a>Az adattárolás titkosított

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Biztonságos üzembe helyezés

Azure Functions az integrációs eszköz megkönnyíti az Azure-ban a helyi függvény projekt kódjának közzétételét. Fontos tisztában lenni azzal, hogyan működik az üzembe helyezés, amikor egy Azure Functions topológia biztonságát fontolgatja.   

### <a name="deployment-credentials"></a>Üzembehelyezési hitelesítő adatok

App Service központi telepítések a központi telepítési hitelesítő adatok készletét igénylik. A rendszer ezeket az üzembe helyezési hitelesítő adatokat használja a Function app-telepítések biztonságossá tételéhez. Az üzembe helyezési hitelesítő adatokat a App Service platform felügyeli, és a rendszer a nyugalmi állapotban titkosítja. 

Kétféle központi telepítési hitelesítő adat létezik:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

Jelenleg a Key Vault nem támogatott az üzembe helyezési hitelesítő adatok esetén. Az üzembe helyezési hitelesítő adatok kezelésével kapcsolatos további információkért lásd: [telepítési hitelesítő adatok konfigurálása Azure app Servicehoz](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>FTP letiltása

Alapértelmezés szerint az egyes functions-alkalmazásokhoz engedélyezve van egy FTP-végpont. Az FTP-végpont az üzembe helyezési hitelesítő adatok használatával érhető el. 

Az FTP nem ajánlott a függvény kódjának üzembe helyezéséhez. Az FTP-telepítések kéziek, és az eseményindítók szinkronizálását igénylik. További információ: FTP- [telepítés](functions-deployment-technologies.md#ftp). 

Ha nem tervezi az FTP használatát, tiltsa le a portálon. Ha úgy dönt, hogy FTP-t használ, [kényszerítse](../app-service/deploy-ftp.md#enforce-ftps)ki a FTPS.

### <a name="secure-the-scm-endpoint"></a>Az SCM-végpont védelme

Minden Function alkalmazásnak van egy megfelelő `scm` szolgáltatási végpontja, amelyet a speciális eszközök (kudu) szolgáltatás használ a központi telepítések és más app Service [hely-bővítmények](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)számára. A Function app SCM-végpontja mindig URL-cím az űrlapon `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . Ha hálózati elkülönítést használ a függvények biztonságossá tételéhez, akkor a végpontot is figyelembe kell vennie. 

Egy külön SCM-végponttal szabályozhatja az üzemelő példányokat és az egyéb speciális eszközök funkcióit, amelyek elkülönítettek vagy virtuális hálózaton futnak. Az SCM-végpont támogatja az alapszintű hitelesítést (központi telepítési hitelesítő adatok használatával) és az egyszeri bejelentkezést az Azure Portal hitelesítő adataival. További információ: [a kudu szolgáltatás elérése](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Folyamatos biztonsági ellenőrzés

Mivel a biztonsági lépéseket a fejlesztési folyamat minden lépésének figyelembe kell vennie, érdemes lehet a biztonsági érvényesítést is megvalósítani a folyamatos üzembe helyezési környezetben. Ezt néha DevSecOps nevezik. Az Azure DevOps használata az üzembe helyezési folyamathoz lehetővé teszi az ellenőrzés integrálását az üzembe helyezési folyamatba. További információ: a [folyamatos biztonsági ellenőrzés hozzáadása a CI/CD-folyamathoz](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Hálózati biztonság

Ha korlátozni szeretné a Function-alkalmazás hálózati hozzáférését, szabályozhatja, hogy ki férhet hozzá a függvények végpontokhoz. A functions App Service-infrastruktúrát használ, hogy lehetővé tegye a függvények számára az erőforrások elérését az internetről átirányítható címek használata nélkül, illetve az Internet-hozzáférés korlátozását a függvény végpontja számára. Ha többet szeretne megtudni ezekről a hálózati beállításokról, tekintse meg a [Azure functions hálózati beállítások](functions-networking-options.md)című témakört.

### <a name="set-access-restrictions"></a>Hozzáférési korlátozások beállítása

A hozzáférési korlátozások lehetővé teszik az engedélyezési/megtagadási szabályok listáját az alkalmazás felé irányuló forgalom szabályozásához. A szabályok prioritási sorrendben vannak kiértékelve. Ha nincsenek szabályok meghatározva, akkor az alkalmazás bármely címről fogadja a forgalmat. További információ: [Azure app Service hozzáférési korlátozások #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Hozzáférés személyes oldalakhoz

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>A Function alkalmazás üzembe helyezése elkülönítésben

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Átjáró szolgáltatás használata

Az átjáró-szolgáltatások, például az [azure Application Gateway](../application-gateway/overview.md) és az [Azure bejárati ajtó](../frontdoor/front-door-overview.md) lehetővé teszik a webalkalmazási tűzfal (WAF) beállítását. A WAF-szabályok az észlelt támadások figyelésére vagy letiltására szolgálnak, amelyek további védelmi réteget biztosítanak a függvények számára. A WAF beállításához a Function app-nak egy beadási vagy privát végpontok (előzetes verzió) használatával kell futnia. További információt a [privát végpontok használata](../app-service/networking/private-endpoint.md)című témakörben talál.    

## <a name="next-steps"></a>Következő lépések

+ [Azure Functions Azure biztonsági alapterve](security-baseline.md)
+ [Azure Functions diagnosztika](functions-diagnostics.md)

---
title: 'ML Studio (klasszikus): REST API hibakódok – Azure'
description: Ezeket a hibakódokat egy Azure Machine Learning webszolgáltatáson végzett művelet is visszaküldheti.
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c5a457a673500c542dd6130d5cfadd73ab677f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518351"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klasszikus) REST API hibakódok

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A következő hibakódokat egy Azure Machine Learning Studio (klasszikus) webszolgáltatás egyik művelete is visszaküldheti.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (400-es HTTP-állapotkód)
 
Érvénytelen argumentum van megadva.
 
Ez az osztály azt jelenti, hogy a megadott argumentum érvénytelen. Ez lehet egy hitelesítő adat vagy az Azure Storage egy olyan helye, amelyet a webszolgáltatásnak továbbítanak. A "Részletek" szakasz "code" (kód) mezőjéből megtudhatja, hogy melyik adott argumentum érvénytelen.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BadParameterValue | A paraméter megadott értéke nem felel meg a paraméter szabályának. |
| BadSubscriptionId | A pontszámhoz használt előfizetés-azonosító nem az erőforrásban lévő egyik. |
| BadVersionCall | Érvénytelen Version paraméter lett átadva az API-hívás során: {0} . A megfelelő verzió átadásához tekintse meg az API-Súgó lapot, és próbálkozzon újra. |
| BatchJobInputsNotSpecified | A kérelemben nem voltak megadva a következő kötelező bevitel (ek): {0} . Gondoskodjon arról, hogy minden bemeneti adat meg legyen adva, majd próbálkozzon újra. |
| BatchJobInputsTooManySpecified | A kérelem több bemenetet adott meg, mint amennyit a szolgáltatás meghatároz. Elfogadott bevitel (ek) listája: {0} . Győződjön meg arról, hogy az összes bemeneti adat helyesen van megadva, és próbálkozzon újra. |
| BlobNameTooLong | A diagnosztikai kimenethez megadott Azure Blob Storage-elérési út túl hosszú: {0} . Rövidítse le az elérési utat, és próbálkozzon újra. |
| BlobNotFound | Nem lehet hozzáférni a megadott Azure-blobhoz {0} .  Azure-hibaüzenet: {1} . |
| ContainerIsEmpty | Nem adta meg az Azure Storage-tároló nevét. Adjon meg egy érvényes nevet, és próbálkozzon újra. |
| ContainerSegmentInvalid | A tároló neve érvénytelen. Adjon meg egy érvényes nevet, és próbálkozzon újra. |
| ContainerValidationFailed | A blob-tároló érvényesítése sikertelen volt a következő hibával: {0} . |
| DataTypeNotSupported | Nem támogatott adattípus van megadva. Adjon meg érvényes adattípust, és próbálkozzon újra. |
| DuplicateInputInBatchCall | A Batch-kérelem érvénytelen. Egyszerre csak egy és több bemenet adható meg. Távolítsa el az egyik elemet a kérelemből, majd próbálkozzon újra. |
| ExpiryTimeInThePast | A megadott lejárati idő a múltban: {0} . Adja meg az UTC jövőbeli lejárati idejét, és próbálkozzon újra. Ha soha nem jár le, állítsa a lejárati időt NULL értékre. |
| IncompleteSettings | A diagnosztikai beállítások hiányosak. |
| InputBlobRelativeLocationInvalid | Nem adta meg az Azure Storage-blob nevét. Adjon meg egy érvényes blob-nevet, és próbálkozzon újra. |
| InvalidBlob | Érvénytelen blob-specifikáció a blobhoz: {0} . Győződjön meg arról, hogy a kapcsolati karakterlánc/relatív elérési út vagy az SAS-jogkivonat specifikációja helyes, és próbálkozzon újra. |
| InvalidBlobConnectionString | A bemeneti/kimeneti Blobok egyikéhez megadott, érvénytelen kapcsolatok karakterlánca: {0} . Javítsa ki ezt, és próbálkozzon újra. |
| InvalidBlobExtension | A blob-hivatkozás: {0} érvénytelen vagy hiányzó fájlkiterjesztés. A következő kimeneti típushoz támogatott fájlkiterjesztések: " {1} ". |
| InvalidInputNames | Érvénytelen a kérésben megadott szolgáltatás-bemeneti név (ek): {0} . Rendelje hozzá a bemeneti adatokat a megfelelő szolgáltatási bemenetekhez, és próbálkozzon újra. |
| InvalidOutputOverrideName | A kimeneti felülbírálás neve érvénytelen: {0} . A szolgáltatásnak nincs ilyen nevű kimeneti csomópontja. Adja át a megfelelő kimeneti csomópont nevét a felülbíráláshoz (a kis-és nagybetűk megkülönböztetése érvényes). |
| InvalidQueryParameter | A lekérdezési paraméter (" {0} ") érvénytelen. {1} |
| MissingInputBlobInformation | Hiányzó Azure Storage-blob-információ. Adjon meg érvényes karakterláncot és relatív elérési utat vagy URI-t, majd próbálkozzon újra. |
| MissingJobId | Nem adta meg a megadott azonosítójú feladatot. A rendszer egy feladatot ad vissza, amikor első alkalommal küldi el a feladatot. Ellenőrizze, hogy helyes-e a feladatokhoz tartozó azonosító, majd próbálkozzon újra. |
| MissingKeys | Nincsenek megadva kulcsok, vagy az elsődleges vagy a másodlagos kulcs egyike sincs megadva. |
| MissingModelPackage | Nincs megadva modell-csomag azonosítója vagy modell-csomag. Adjon meg egy érvényes Model Package ID-t vagy modell-csomagot, és próbálkozzon újra. |
| MissingOutputOverrideSpecification | A kérelemből hiányzik a blob-specifikáció a kimenet felülbírálásához {0} . Adjon meg egy érvényes blob-helyet a kérelemben, vagy távolítsa el a kimeneti specifikációt, ha nincs szükség a hely felülbírálására. |
| MissingRequestInput | A webszolgáltatás egy bemenetet vár, de nincs megadva bemenet. Győződjön meg arról, hogy az érvényes bemenetek a modellben közzétett bemeneti portok alapján vannak megadva, és próbálkozzon újra. |
| MissingRequiredGlobalParameters | Nem minden szükséges webszolgáltatás-paraméter (ek) van megadva. Ellenőrizze, hogy helyesek-e a modul (ok) re várt paraméter (ek), és próbálkozzon újra. |
| MissingRequiredOutputOverrides | Egy titkosított szolgáltatási végpont meghívásakor kötelező megadni a kimeneti felülbírálásokat az összes szolgáltatás kimenete számára. A kimenetek jelenleg nincsenek felülbírálások: {0} |
| MissingWebServiceGroupId | Nincs megadva webszolgáltatás-csoport azonosítója. Adja meg egy érvényes webszolgáltatás-csoport azonosítóját, és próbálkozzon újra. |
| MissingWebServiceId | Nincs megadva webszolgáltatás-azonosító. Adjon meg egy érvényes webszolgáltatás-azonosítót, és próbálkozzon újra. |
| MissingWebServicePackage | Nincs megadva webszolgáltatás-csomag. Adjon meg egy érvényes webszolgáltatás-csomagot, és próbálkozzon újra. |
| MissingWorkspaceId | Nincs megadva munkaterület-azonosító. Adjon meg egy érvényes munkaterület-azonosítót, és próbálkozzon újra. |
| ModelConfigurationInvalid | Érvénytelen modell-konfiguráció a modell csomagban. Győződjön meg arról, hogy a modell konfigurációja kimeneti végpont (ok) definícióját, STD-hiba végpontját és kifelé tartozó végpontot tartalmaz, és próbálkozzon újra. |
| ModelPackageIdInvalid | A modell-csomag azonosítója érvénytelen. Ellenőrizze, hogy helyes-e a modell-csomag azonosítója, és próbálkozzon újra. |
| RequestBodyInvalid | A kérés törzsének deszerializálása során nincs megadva a kérelem törzse vagy a hiba. |
| RequestIsEmpty | Nincs megadva kérelem. Adjon meg egy érvényes kérelmet, és próbálkozzon újra. |
| UnexpectedParameter | Váratlan paraméterek vannak megadva. Ellenőrizze, hogy az összes paraméter neve helyesen van-e megadva, csak a várt paraméterek lesznek átadva, majd próbálkozzon újra. |
| UnknownError | Ismeretlen hiba. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | A webszolgáltatásra vonatkozó egyidejű kérelmekre vonatkozó követelmények nem módosíthatók {0} . |
| WebServiceIdInvalid | Érvénytelen webszolgáltatás-azonosító van megadva. A webszolgáltatás azonosítójának érvényes GUID-azonosítónak kell lennie. |
| WebServiceTooManyConcurrentRequestRequirement | Az egyidejű kérelmek nem állíthatók be több értékre {0} . |
| WebServiceTypeInvalid | Érvénytelen a megadott webszolgáltatás-típus. Győződjön meg arról, hogy az érvényes webszolgáltatás-típus helyes, és próbálkozzon újra. Érvényes webszolgáltatás-típusok: {0} . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (400-es HTTP-állapotkód)
 
A megadott felhasználói argumentum érvénytelen.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| InputMismatchError | A bemeneti adatok nem felelnek meg a bemeneti port sémájának. |
| InputParseError | A bemeneti vektor elemzése sikertelen volt.  Ellenőrizze, hogy a bemeneti vektor megfelelő számú oszloppal és adattípussal rendelkezik-e.  További részletek: {0} . |
| MissingRequiredGlobalParameters | A webszolgáltatás által várt paraméter (ek) hiányoznak. Győződjön meg arról, hogy a webszolgáltatás által várt összes szükséges paraméter helyes, és próbálkozzon újra. |
| UnexpectedParameter | Győződjön meg arról, hogy csak a webszolgáltatás által várt szükséges paraméterek lesznek átadva, majd próbálkozzon újra. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (400-es HTTP-állapotkód)
 
A kérelem érvénytelen az aktuális környezetben.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| CannotStartJob | A feladatot nem lehet elindítani, mert állapotban van {0} . |
| IncompatibleModel | A modell nem kompatibilis a kérelem verziójával. A kérelem verziója csak egyetlen DataTable kimeneti modellt támogat. |
| MultipleInputsNotAllowed | A modell nem engedélyez több bemenetet. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (400-es HTTP-állapotkód)
 
A modul végrehajtása belső függvénytár-hibát észlelt.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (400-es HTTP-állapotkód)
 
A modul végrehajtása hibát észlelt.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (400-es HTTP-állapotkód)
 
Érvénytelen webszolgáltatási csomag. Ellenőrizze, hogy a megadott webszolgáltatás-csomag megfelelő-e, majd próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| FormatError | A webszolgáltatási csomag helytelen formátumú. Részletek {0} |
| RuntimesError | A webszolgáltatás-csomag gráfja érvénytelen. Részletek {0} |
| ValidationError | A webszolgáltatás-csomag gráfja érvénytelen. Részletek {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Nem engedélyezett (HTTP-állapotkód 401)
 
A kérés nem engedélyezett az erőforráshoz való hozzáféréshez.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminRequestUnauthorized | Nem engedélyezett |
| ManagementRequestUnauthorized | Nem engedélyezett |
| ScoreRequestUnauthorized | A megadott hitelesítő adatok érvénytelenek. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (404-es HTTP-állapotkód)
 
Az erőforrás nem található.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelPackageNotFound | A modell csomagja nem található. Ellenőrizze, hogy helyes-e a modell-csomag azonosítója, és próbálkozzon újra. |
| WebServiceIdNotFoundInWorkspace | Nem található webszolgáltatás ezen a munkaterületen. Eltérés van a webServiceId és a munkaterület azonosítója között. Ellenőrizze, hogy a megadott webszolgáltatás része-e a munkaterületnek, majd próbálkozzon újra. |
| WebServiceNotFound | A webszolgáltatás nem található. Ellenőrizze a webszolgáltatás azonosítójának helyességét, és próbálkozzon újra. |
| WorkspaceNotFound | A munkaterület nem található. Ellenőrizze a munkaterület azonosítójának helyességét, és próbálkozzon újra. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (408-es HTTP-állapotkód)
 
A művelet nem hajtható végre a megengedett időn belül.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| RequestCanceled | Az ügyfél megszakította a kérést. |
| ScoreRequestTimeout | A végrehajtási kérelem időkorlátja lejárt. |
 
## <a name="conflict-http-status-code-409"></a>Ütközés (HTTP-állapotkód 409)
 
Az erőforrás már létezik.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModelOutputMetadataMismatch | A kimeneti paraméter neve érvénytelen. A metaadatok szerkesztő moduljának használatával nevezze át az oszlopokat, és próbálkozzon újra. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (413-es HTTP-állapotkód)
 
A modell túllépte a hozzá rendelt memória-kvótát.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| OutOfMemoryLimit | A modell a megfelelőnél több memóriát használt. A modell számára engedélyezett memória maximális mérete {0} MB. Ellenőrizze a modellt a problémákhoz. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (500-es HTTP-állapotkód)
 
A végrehajtás belső hibába ütközött.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | A tároló folyamata rendszerhiba miatt összeomlott |
| ContainerProcessTerminatedWithUnknownError | A tároló folyamata ismeretlen hiba miatt összeomlott |
| ContainerValidationFailed | A blob-tároló érvényesítése sikertelen volt a következő hibával: {0} . |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nincsenek megadva argumentumok. Győződjön meg arról, hogy érvényes argumentumok lettek átadva, majd próbálkozzon újra. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | A port ID = {0} nem támogatott adattípusú: {1} . |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | A hencegés létrehozása nem sikerült, részletek: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Ismeretlen a feladathoz tartozó állapotkód {0} . |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, részletek: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (500-es HTTP-állapotkód)
 
A végrehajtás belső hibába ütközött. A rendszer kevés a memóriában. Próbálkozzon újra.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (500-es HTTP-állapotkód)
 
A modell csomagja érvénytelen. Győződjön meg arról, hogy a megadott modell-csomag helyes, és próbálkozzon újra.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (500-es HTTP-állapotkód)
 
Érvénytelen webszolgáltatási csomag. Ellenőrizze, hogy helyes-e a megadott webcsomag, és próbálkozzon újra.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| ModuleError | A webszolgáltatás-csomag gráfja érvénytelen. Részletek {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (503-es HTTP-állapotkód)
 
A kérelem nem hajtható végre, mert a tárolók inicializálva vannak.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (503-es HTTP-állapotkód)
 
A szolgáltatás átmenetileg nem érhető el.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| NoMoreResources | Nem érhető el erőforrás a kérelemhez. |
| RequestThrottled | A kérelem le lett szabályozva a {0} végpont számára. A végponthoz tartozó maximális Egyidejűség {1} . |
| TooManyConcurrentRequests | Túl sok egyidejű kérelem elküldése. |
| TooManyHostsBeingInitialized | Túl sok gazdagép van inicializálva egyszerre. Vegye fontolóra a szabályozást és az újrapróbálkozást. |
| TooManyHostsBeingInitializedPerModel | Túl sok gazdagép van inicializálva egyszerre. Vegye fontolóra a szabályozást és az újrapróbálkozást. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (504-es HTTP-állapotkód)
 
A művelet nem hajtható végre a megengedett időn belül.
 
| Hibakód | Felhasználói üzenet |
| ---------- |--------------|
| BackendInitializationTimeout | A webszolgáltatás inicializálása nem fejeződött be a megengedett időn belül. |
| BackendScoreTimeout | A webszolgáltatás-kérelem végrehajtása nem fejeződött be a megengedett időn belül. |

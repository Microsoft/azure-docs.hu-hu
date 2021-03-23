---
title: A CI-CD, az Azure DevOps és a GitHub hibáinak elhárítása az ADF-ben
description: A CI-CD-vel kapcsolatos hibák elhárításához használja az ADF különböző módszereit.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 2b6f97f0966cb2c92dbd88c4a70188282ed3ed27
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802033"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>A CI-CD, az Azure DevOps és a GitHub hibáinak elhárítása az ADF-ben 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a folyamatos Integration-Continuous üzembe helyezés (CI-CD), az Azure DevOps és a GitHub-problémák gyakori hibaelhárítási módszereit ismerteti Azure Data Factoryokban.

Ha kérdése van vagy probléma merül fel a verziókövetés vagy a DevOps technikák használatával, itt talál néhány cikket, amely hasznos lehet:

- Tekintse meg az [ADF forrás-vezérlőelemét](source-control.md) , amelyből megtudhatja, hogyan gyakorolja a VERZIÓKÖVETÉS az ADF-ben. 
- Ha többet szeretne megtudni arról, hogy az DevOps CI-CD-je hogyan legyen az ADF-ben, tekintse meg a  [CI-CD in ADF című részt](continuous-integration-deployment.md) .
 
## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>A git-tárházhoz való kapcsolódás más bérlő miatt nem sikerült

#### <a name="issue"></a>Probléma
    
Időnként olyan hitelesítési problémákba ütközik, mint például a 401-es HTTP-állapot. Különösen akkor, ha több Bérlővel rendelkezik vendég fiókkal, a dolgok bonyolultabbá válhatnak.

#### <a name="cause"></a>Ok

A megfigyelt érték az, hogy a token az eredeti bérlőtől származik, de az ADF a vendég bérlőn van, és a token használatával próbálja meglátogatni a DevOps a vendég bérlőben. Ez nem a várt viselkedés.

#### <a name="recommendation"></a>Ajánlás

Ehelyett a vendég bérlőtől kiállított jogkivonatot kell használnia. Tegyük fel, hogy ugyanazt a Azure Active Directory kell hozzárendelnie a vendég bérlőhöz és a DevOps, így helyesen állíthatja be a jogkivonat viselkedését, és használhatja a megfelelő bérlőt.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Érvénytelenek a sablon paraméterei a paraméterek fájlban.

#### <a name="issue"></a>Probléma

Ha törölünk egy triggert a fejlesztői ágban, amely már elérhető tesztelési vagy termelési ágban **ugyanazzal** a konfigurációval (például gyakorisággal és intervallummal), akkor a kiadási folyamat üzembe helyezése sikeres lesz, és a megfelelő trigger törlődik a megfelelő környezetekben. Ha azonban **eltérő** konfigurációval (például gyakorisággal és intervallummal) rendelkezik az trigger tesztelési vagy éles környezetekben való használatakor, és ha ugyanazt a triggert törli a dev-ben, akkor a telepítés hibát jelez.

#### <a name="cause"></a>Ok

A CI/CD-folyamat a következő hiba miatt meghiúsul:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Ajánlás

A hiba azért fordul elő, mert gyakran törölünk egy paraméteres triggert, így a paraméterek nem lesznek elérhetők az ARM-sablonban (mivel az trigger már nem létezik). Mivel a paraméter nem az ARM-sablonban van, frissíteni kell a felülbírált paramétereket a DevOps-folyamatban. Ellenkező esetben az ARM-sablon paramétereinek megváltozásakor a rendszernek frissítenie kell a felülbírált paramétereket a DevOps-folyamatban (a telepítési feladatban).

### <a name="updating-property-type-is-not-supported"></a>A tulajdonság típusának frissítése nem támogatott

#### <a name="issue"></a>Probléma

A CI/CD kiadási folyamat a következő hibával meghiúsult:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Ok

Ezt egy azonos nevű integrációs futtatókörnyezet okozza a cél gyárban, de egy másik típussal. A telepítéskor a Integration Runtimenak azonos típusúnak kell lennie.

#### <a name="recommendation"></a>Ajánlás

- Tekintse át az alábbi, CI/CD-re vonatkozó ajánlott eljárásokat:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Az integrációs modulok nem változnak gyakran, és a CI/CD összes fázisában hasonlóak, így Data Factory az integrációs modul azonos nevét és típusát adja meg a CI/CD összes szakaszában. Ha a név és a típus & tulajdonságok eltérőek, ügyeljen arra, hogy egyezzen a forrás és a cél integrációs modul konfigurációjával, majd telepítse a kiadási folyamatot.
- Ha az integrációs modulokat minden fázisban meg szeretné osztani, érdemes lehet egy Ternáris-gyárat használni, amely csak a megosztott integrációs modulokat tartalmazza. Ezt a megosztott gyárat az összes környezetében használhatja társított Integration Runtime-típusként.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Érvénytelen hivatkozás miatt nem sikerült a dokumentumok létrehozása vagy frissítése.

#### <a name="issue"></a>Probléma

Amikor a Data Factory módosításokat próbál közzétenni, a következő hibaüzenet jelenik meg:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>Ok

Leválasztotta a git-konfigurációt, és ismét beállította az "erőforrások importálása" jelzőt, amely a Data Factory "szinkron" értékre állítja. Ez azt jelenti, hogy a közzététel nem módosul.

#### <a name="resolution"></a>Feloldás

Válassza le a git-konfigurációt, és állítsa be újra, és ügyeljen rá, hogy ne jelölje be a "meglévő erőforrások importálása" jelölőnégyzetet.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Nem sikerült áthelyezni az egyik erőforráscsoportot a másikba Data Factory

#### <a name="issue"></a>Probléma

Nem lehet áthelyezni Data Factory egyik erőforráscsoporthoz a másikba, a következő hiba miatt:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Feloldás

Az áthelyezési művelet engedélyezéséhez törölnie kell a SSIS-IR és a megosztott IRs-t. Ha nem szeretné törölni az integrációs modulokat, a legjobb módszer a másolási és klónozási dokumentum a másolás és a végrehajtás befejezése után, a régi Data Factory törlése.

###  <a name="unable-to-export-and-import-arm-template"></a>Az ARM-sablon nem exportálható és nem importálható

#### <a name="issue"></a>Probléma

Az ARM-sablon nem exportálható és nem importálható. Nem történt hiba a portálon, azonban a böngésző nyomkövetésében a következő hibaüzenet jelenhet meg:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Ok

Felhasználóként létrehozott egy felhasználói szerepkört, és nem rendelkezett a szükséges engedélyekkel. Ha a gyár be van töltve a felhasználói felületen, a rendszer bejelöli a gyári expozíció-vezérlési értékek egy sorozatát. Ebben az esetben a felhasználó hozzáférési szerepköre nem rendelkezik engedéllyel a *queryFeaturesValue* API eléréséhez. Az API eléréséhez a globális paraméterek funkció ki van kapcsolva. Az ARM-exportálási kód elérési útja részben a globális paraméterek szolgáltatásra támaszkodik.

#### <a name="resolution"></a>Feloldás

A probléma megoldásához hozzá kell adnia a következő engedélyeket a szerepkörhöz: *Microsoft. DataFactory/Factorys/queryFeaturesValue/Action*. Ezt az engedélyt alapértelmezés szerint a "Data Factory közreműködő" szerepkörbe kell foglalni.

###  <a name="cannot-automate-publishing-for-cicd"></a>Nem automatizálható a CI/CD közzétételének automatizálása 

#### <a name="cause"></a>Ok

Egészen a közelmúltig csak az ADF-folyamatnak a központi telepítésekhez való közzététele volt az ADF Portal gomb kattintson a lehetőségre. Ezt követően a folyamat automatikusan elvégezhető. 

#### <a name="resolution"></a>Feloldás

A CI/CD-folyamat ki lett bővítve. Az **automatikus közzétételi** funkció az ADF UX összes Azure Resource Manager (ARM) sablonjának funkcióit végrehajtja, ellenőrzi és exportálja. Egy nyilvánosan elérhető NPM-csomagon keresztül teszi a logikát [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Ez lehetővé teszi, hogy programozott módon aktiválja ezeket a műveleteket ahelyett, hogy az ADF felhasználói felületére ugorjon, majd kattintson a gombra. Így a CI/CD-folyamatok **valódi** folyamatos integrációs élményt biztosítanak. A részletekért kövesse az [ADF CI/CD közzétételének tökéletesítése](./continuous-integration-deployment-improvements.md) című témakört. 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>4MB nál ARM-sablon korlátja miatt nem lehet közzétenni  

#### <a name="issue"></a>Probléma

A telepítés nem végezhető el, mert elérte a 4MB nál teljes méretének Azure Resource Manager korlátját. A korlát átlépése után telepítenie kell egy megoldást. 

#### <a name="cause"></a>Ok

Azure Resource Manager korlátozza a sablon méretének 4MB nál. Korlátozza a sablon méretét 4 MB-ra, az egyes paramétereket pedig 64 KB-ra. A 4 MB-os korlát a sablon végső állapotára vonatkozik, miután az ismétlődő erőforrás-definíciókkal bővült, valamint a változók és paraméterek értékeit. Azonban átlépte a korlátot. 

#### <a name="resolution"></a>Feloldás

Kis és közepes mérető megoldások esetében könnyebb egyetlen sablont megérteni és karbantartani. Így az összes erőforrás és értéke egyetlen fájlban látható. Speciális felhasználási helyzetekben a megoldás csatolt sablonokkal bontható fel a kívánt összetevőkre. A [csatolt és beágyazott sablonok használata esetén](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell)kövesse az ajánlott eljárásokat.

### <a name="cannot-connect-to-git-enterprise"></a>Nem lehet csatlakozni a GIT Enterprise-hoz  

##### <a name="issue"></a>Probléma

Engedélyekkel kapcsolatos problémák miatt nem lehet csatlakozni a GIT Enterprise-hoz. A következőhöz hasonló hiba látható: **422 – feldolgozható entitás.**

#### <a name="cause"></a>Ok

* Nem konfigurálta a OAuth az ADF-hez. 
* Az URL-cím helytelenül van konfigurálva.

##### <a name="resolution"></a>Feloldás

Először a OAuth-hozzáférést kell megadnia az ADF-hez. Ezután a GIT Enterprise-hoz való kapcsolódáshoz a helyes URL-címet kell használnia. A konfigurációt az ügyfél Szervezete (ke) t kell beállítani. Az ADF például először a. *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> ...* időpontban próbálkozik, és sikertelen lesz. Ezután megpróbálja *https://hostname/api/v3/orgs/ <org> / <repo> ...* és sikeres lesz. 
 
### <a name="cannot-recover-from-a-deleted-data-factory"></a>Nem lehet helyreállítani a törölt adatok gyárból

#### <a name="issue"></a>Probléma
Az ügyfél törölte az adatgyárat vagy a Data Factory tartalmazó erőforráscsoportot. Szeretné tudni, hogyan kell visszaállítani a törölt adatgyárat.

#### <a name="cause"></a>Ok

A Data Factory csak akkor állítható helyre, ha az ügyfélen van konfigurálva a verziókövetés (DevOps vagy git). Ezzel az összes legújabb közzétett erőforrást fogja visszaállítani, és nem állítja vissza a **nem** közzétett folyamatot, az adatkészletet és a társított szolgáltatást.

Ha nincs verziókövetés, nem lehet helyreállítani egy törölt Data Factory a háttérből, mert a szolgáltatás a törölt parancsot fogadja, a példány törlődik, és a biztonsági mentés nem lett tárolva.

#### <a name="resolution"></a>Feloldás

A következő lépésekkel állíthatja helyre a törölt Data Factory, amely a verziókövetés:

 * Hozzon létre egy új Azure Data Factory.

 * Konfigurálja újra a git-t ugyanazzal a beállításokkal, de győződjön meg arról, hogy meglévő Data Factory erőforrásokat importál a kiválasztott tárházba, és válassza az új ág lehetőséget.

 * Hozzon létre egy lekéréses kérelmet az együttműködési ág változásainak egyesítéséhez és közzétételéhez.

 * Ha az ügyfél egy saját üzemeltetésű Integration Runtimet törölt az ADF-ben, akkor létre kell hoznia egy új példányt az új ADF-ben, majd el kell távolítania és újra kell telepítenie a példányt a helyszíni gépen/virtuális gépen a kapott új kulccsal. Az IR beállításának befejeződése után az ügyfélnek módosítania kell a társított szolgáltatást, hogy az új IR-re mutasson, és tesztelje a kapcsolatot, vagy a hiba **érvénytelen hivatkozással meghiúsul.**



## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)

---
title: Felhőszinkronizálás programozott konfigurálása MS Graph API
description: Ez a témakör azt ismerteti, hogyan engedélyezheti a bejövő szinkronizálást csak a Graph API
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe220cf7b5cb8b67e5ab7ded221494e89a28aa5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530270"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Felhőszinkronizálás programozott konfigurálása MS Graph API

A következő dokumentum bemutatja, hogyan replikálhatja a szinkronizálási profilokat az nulláról, csak MSGraph API-k használatával.  
Ennek struktúrája az alábbi lépésekből áll.  Ezek a következők:

- [Felhőszinkronizálás programozott konfigurálása MS Graph API](#how-to-programmatically-configure-cloud-sync-using-ms-graph-api)
  - [Alapszintű beállítás](#basic-setup)
    - [Bérlőjelölők engedélyezése](#enable-tenant-flags)
  - [Szolgáltatásnevek létrehozása](#create-service-principals)
  - [Szinkronizálási feladat létrehozása](#create-sync-job)
  - [Céltartomány frissítése](#update-targeted-domain)
  - [Jelszó-hashek szinkronizálásának engedélyezése a konfigurációs panelen](#enable-sync-password-hashes-on-configuration-blade)
  - [Véletlen törlések](#accidental-deletes)
    - [A küszöbérték engedélyezése és beállítása](#enabling-and-setting-the-threshold)
    - [Törlések engedélyezésével](#allowing-deletes)
  - [Szinkronizálási feladat elindítani](#start-sync-job)
  - [Állapot áttekintése](#review-status)
  - [Következő lépések](#next-steps)

Használja ezeket [Microsoft Azure Active Directory](/powershell/module/msonline/) modult Windows PowerShell-parancsokhoz az éles bérlők szinkronizálásának engedélyezéséhez, ami előfeltétele annak, hogy meg tudja hívni az adminisztrációs webszolgáltatást a bérlő számára.

## <a name="basic-setup"></a>Alapszintű beállítás

### <a name="enable-tenant-flags"></a>Bérlőjelölők engedélyezése

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
A két parancs közül az elsőhez hitelesítő Azure Active Directory van szükség. Ezek a parancsmagok implicit módon azonosítják a bérlőt, és engedélyezik a szinkronizálást.

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
Ezután létre kell hoznunk az [AD2AAD-alkalmazást/-szolgáltatásnévt](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http&preserve-view=true)

Az alkalmazásazonosító: 1a4721b3-e57f-4451-ae87-ef078703ec94. A displayName a portálon használt AD-tartomány URL-címe (például contoso.com), de lehet, hogy valami más nevet ad.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Szinkronizálási feladat létrehozása
A fenti parancs kimenete visszaadja a létrehozott szolgáltatásnév objectId-ját. Ebben a példában az objectId a következő: 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  A Microsoft Graph a synchronizationJob a szolgáltatásnévhez való hozzáadásához.  

A szinkronizálási feladat létrehozásának dokumentációját itt [találhatja.](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)

Ha nem jegyezta fel a fenti azonosítót, a szolgáltatásnév az alábbi MS Graph-hívás futtatásával található meg. A híváshoz Directory.Read.All engedélyre lesz szüksége:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Ezután keresse meg az alkalmazás nevét a kimenetben.

Futtassa az alábbi két parancsot két feladat létrehozásához: egyet a felhasználók/csoportok üzembe állásához, egyet pedig a jelszó kivonatának szinkronizálásához. Ez kétszer ugyanaz a kérés, de különböző sablon-ekkel.


Hívja meg a következő két kérést:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Ha mindkettőt létre szeretné hozni, két hívásra lesz szüksége.

Példa visszaadott érték (kiépítéshez):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Céltartomány frissítése
Ebben a bérlőben a szolgáltatásnév objektum- és alkalmazásazonosítója a következő:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-00000000000 DisplayName: testApp

Frissíteni kell a tartományt, amelyre ez a konfiguráció van megcélzott, ezért frissítse a tartomány titkos beállításait.

Győződjön meg arról, hogy a használt tartománynév ugyanaz az URL-cím, mint a saját tartományvezérlője számára.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Adja hozzá a következő kulcs/érték párt az alábbi értéktömbhöz a tenni próbált lépések alapján:
 - Engedélyezze a PHS és a szinkronizálás bérlői jelzőit { kulcs: "AppKey", érték: "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - Csak a bérlő jelzője szinkronizálásának engedélyezése (a PHS nem kapcsolható be) { kulcs: "AppKey", érték: "{"appKeyScenario":"AD2AADProvisioning"}" }
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

A várt válasz... HTTP 204/Nincs tartalom

Itt a kiemelt "Tartomány" érték annak a helyi Active Directory tartománynak a neve, amelyből a bejegyzéseket ki kell Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Jelszó-hasheek szinkronizálásának engedélyezése a konfigurációs panelen

 Ez a szakasz az adott konfigurációhoz szükséges jelszó-hashek szinkronizálásának engedélyezését fedi le. Ez eltér az AppKey titkos kulcstól, amely lehetővé teszi a bérlői szintű funkció jelzőt – ez csak egyetlen tartományra/konfigurációra van. Az alkalmazáskulcsot a PHS-hez kell beállítania, hogy ez a munka végához működjön.

1. Fogja meg a sémát (figyelmeztetés, hogy ez elég nagy) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Használja a következő CredentialData attribútumleképezést:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Keresse meg a következő objektumleképezéseket a sémában az alábbi nevekkel
 - Felhasználók Active Directory kiépítése
 - Üzembe Active Directory inetOrgPersons

 Az objektumleképezések a schema.synchronizationRules[0].objectMappings fájlban találhatóak (jelenleg feltételezheti, hogy csak 1 szinkronizálási szabály van)

4. A 2. lépésben szereplő CredentialData-leképezés beszúrása a 3. lépésben szereplő objektumleképezésekbe

 Az objektumleképezés a következőre hasonlít:
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Másolja/illessze be a fenti **AD2AADProvisioning és AD2AADPasswordHash** feladatok létrehozása lépésből származó leképezést az attributeMappings tömbbe. 

 A tömb elemeinek sorrendje nem számít (a háttérrendszer rendezi az elemet). Ügyeljen arra, hogy ezt az attribútumleképezést adja hozzá, ha a név már létezik a tömbben (például ha már létezik egy elem a targetAttributeName CredentialData attribútummal az attribútumtérképen) – ütközési hibákat okozhat, vagy a meglévő és az új leképezések kombinálhatók (általában nem kívánt eredmény). A háttér nem deduppetálja az Ön számára. 

 Ezt ne felejtse el a Users (Felhasználók) és az inetOrgpersons (inetOrgpersons) esetén is megtenni

5. A létrehozott séma mentése 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Adja hozzá a sémát a kérelem törzsében. 

## <a name="accidental-deletes"></a>Véletlen törlések
Ez a szakasz bemutatja, hogyan engedélyezheti/tilthatja le és használhatja programozott módon a véletlen [törléseket.](how-to-accidental-deletes.md)


### <a name="enabling-and-setting-the-threshold"></a>A küszöbérték engedélyezése és beállítása
Feladatonként két beállítást használhat:

 - DeleteThresholdEnabled – Engedélyezi a feladat véletlen törlésének megelőzését, ha "true" (igaz) érték van beállítva. Az alapértelmezett érték "true" (igaz).
 - DeleteThresholdValue – Meghatározza a feladat egyes végrehajtásai során engedélyezett törlések maximális számát, ha a véletlen törlés-megelőzés engedélyezve van. Az alapértelmezett érték 500.  Tehát ha az érték 500, akkor az engedélyezett törlések maximális száma 499 lesz az egyes végrehajtások során.

A törlési küszöbérték beállításai a részét képezi, `SyncNotificationSettings` és gráfon keresztül módosíthatók. 

Frissíteni kell a SyncNotificationSettings beállítást, mert ez a konfiguráció a cél, ezért frissítse a titkos okat.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Adja hozzá a következő kulcs/érték párt az alábbi értéktömbhöz a tenni próbált lépések alapján:

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

A fenti példában az "Engedélyezve" beállítás az értesítő e-mailek engedélyezésére/letiltására használható, amikor a feladat karanténba kerül.


Jelenleg nem támogatjuk a PATCH-kéréseket a titkos kulcsokhoz, ezért a többi érték megőrzése érdekében hozzá kell adni a PUT-kérelem törzsében lévő összes értéket (a fenti példához hasonló).

Az összes titkos adat meglévő értékei a következővel szerezhetők be: 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Törlések engedélyezésével
Ahhoz, hogy a feladat karanténba kerülés utáni törlések végig tudjanak haladni, újraindítást kell végeznie, és csak a "ForceDeletes" hatókört kell alkalmaznia. 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>Szinkronizálási feladat elindítani
A feladat a következő paranccsal olvasható be újra:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

A feladatok leolvasásának dokumentációját itt [találhatja meg.](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta&preserve-view=true) 
 
A feladat elindítani ezt a kérést az első lépésben létrehozott szolgáltatásnév objectId azonosítójának, valamint a feladatot létrehozó kérelem által visszaadott feladatazonosítónak a használatával kell kiküldeni.

A feladat indítási dokumentációját itt [találhatja.](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true) 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

A várt válasz... HTTP 204/Nincs tartalom.

A feladat vezérlésére vonatkozó egyéb parancsokat itt [dokumentáljuk.](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta&preserve-view=true)
 
A feladat újraindításához használja a ...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Állapot áttekintése
A feladat állapotának lekérése...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

A releváns részleteket a visszatérési objektum "status" szakasza alatt keresse meg

## <a name="next-steps"></a>Következő lépések 

- [Mit jelent a Azure AD Connect szinkronizálása?](what-is-cloud-sync.md)
- [Átalakítások](how-to-transformation.md)
- [Azure AD Synchronization API](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)

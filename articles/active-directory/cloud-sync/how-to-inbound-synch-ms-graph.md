---
title: A Cloud Sync programozott konfigurálása MS Graph API használatával
description: Ez a témakör azt ismerteti, hogyan engedélyezheti a bejövő szinkronizálást csak a Graph API használatával
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
ms.openlocfilehash: 6c84636ea86b3b640aef365c1c5d8e634b9a1f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593160"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>A Cloud Sync programozott konfigurálása MS Graph API használatával

Az alábbi dokumentum azt ismerteti, hogyan replikálhat egy szinkronizációs profilt a semmiből kizárólag MSGraph API-k használatával.  
Ennek a folyamatnak a szerkezete a következő lépésekből áll.  Ezek a következők:

- [Alapszintű beállítás](#basic-setup)
- [Egyszerű szolgáltatás létrehozása](#create-service-principals)
- [Szinkronizálási feladatok létrehozása](#create-sync-job)
- [Célként megadott tartomány frissítése](#update-targeted-domain)
- [Szinkronizálási jelszó kivonatának engedélyezése](#enable-sync-password-hashes-on-configuration-blade)
- [Véletlen törlések](#accidental-deletes)
- [Szinkronizálási feladatok indítása](#start-sync-job)
- [Felülvizsgálat állapota](#review-status)

Ezekkel a [Microsoft Azure Active Directory modullal Windows PowerShell](/powershell/module/msonline/) -parancsokkal engedélyezheti a szinkronizálást egy éles bérlő számára, amely az adott bérlő adminisztrációs webszolgáltatásának meghívásához szükséges előfeltételek egyike.

## <a name="basic-setup"></a>Alapszintű beállítás

### <a name="enable-tenant-flags"></a>Bérlői jelzők engedélyezése

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
A két parancs első részében Azure Active Directory hitelesítő adatokat kell megadnia. Ezek a parancsmagok implicit módon azonosítják a bérlőt, és lehetővé teszik a szinkronizálást.

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
Ezután létre kell hoznia a [AD2AAD alkalmazást/szolgáltatásnevet](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

Ezt az azonosítót kell használnia a 1a4721b3-e57f-4451-ae87-ef078703ec94. A displayName az AD-tartomány URL-címe, ha a portálon van használatban (például contoso.com), de lehet, hogy más nevet kapott.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Szinkronizálási feladatok létrehozása
A fenti parancs kimenete a létrehozott egyszerű szolgáltatásnév objectId adja vissza. Ebben a példában a objectId a 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  A Microsoft Graph használatával adjon hozzá egy synchronizationJob az adott egyszerű szolgáltatáshoz.  

A szinkronizálási feladatok létrehozásával kapcsolatos dokumentáció [itt](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta)található.

Ha a fenti azonosítót nem rögzíti, az egyszerű szolgáltatásnév a következő MS Graph-hívás futtatásával található. Szüksége lesz a könyvtár. Read. All engedélyekre, hogy a hívást tegye:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Ezután keresse meg az alkalmazás nevét a kimenetben.

Futtassa a következő két parancsot két feladat létrehozásához: egyet a felhasználó/csoport kiépítés számára, egyet pedig a jelszó-kivonatolás szinkronizálásához. Ugyanez a kérés kétszer, de különböző sablon-azonosítókkal van ellátva.


Hívja meg a következő két kérelmet:

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

Példa visszatérési értékre (kiépítés esetén):

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

## <a name="update-targeted-domain"></a>Célként megadott tartomány frissítése
Ehhez a bérlőhöz az objektumazonosító és az egyszerű szolgáltatásnév alkalmazásának azonosítója a következő:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Frissíteni kell azt a tartományt, amelyet ez a konfiguráció céloz, ezért frissítse a tartományhoz tartozó titkokat.

Győződjön meg arról, hogy a használt tartománynév megegyezik-e a helyszíni tartományvezérlőhöz beállított URL-címmel

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Adja hozzá a következő kulcs/érték párokat az alábbi érték tömbben a végrehajtani kívánt művelet alapján:
 - PHS-és szinkronizálási bérlői jelzők engedélyezése {Key: "AppKey", érték: "{" appKeyScenario ":" AD2AADPasswordHash "}"}
 
 - Csak szinkronizálási bérlői jelző engedélyezése (ne kapcsolja be a PHS) {Key: "AppKey", érték: "{" appKeyScenario ":" AD2AADProvisioning "}"}
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

A várt válasz:... HTTP 204/nincs tartalom

Itt a Kiemelt "tartomány" érték annak a helyszíni Active Directory tartománynak a neve, amelyből a bejegyzéseket Azure Active Directory kell kiépíteni.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Jelszó-kivonatok szinkronizálásának engedélyezése a konfiguráció panelen

 Ez a szakasz a jelszó-kivonatok adott konfigurációhoz való szinkronizálásának engedélyezését fedi le. Ez eltér a bérlői szintű szolgáltatás jelölőjét engedélyező titkos AppKey, ez csak egyetlen tartományhoz/konfigurációhoz tartozik. Az alkalmazás kulcsát az PHS kell beállítania, hogy a folyamat véget fejezzen.

1. A séma megragadása (Figyelmeztetés ez elég nagy) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Ezt a CredentialData-attribútum leképezése:
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
3. Keresse meg a következő objektum-hozzárendeléseket a sémában a következő nevekkel:
 - Active Directory felhasználók kiépítése
 - Active Directory inetOrgPerson rendszer kiépítése

 Az objektum-hozzárendelések a Schema. synchronizationRules [0]. objectMappings (egyelőre feltételezhető, hogy csak 1 szinkronizálási szabály található)

4. Végezze el a CredentialData-leképezést a (2) lépésből, majd szúrja be az objektum-hozzárendelésbe a lépésben (3)

 Az objektum-hozzárendelés a következőképpen néz ki:
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
 Másolja/illessze be a leképezést a fenti **AD2AADProvisioning és AD2AADPasswordHash feladatok létrehozása** lépésből a attributeMappings tömbbe. 

 A tömb elemeinek sorrendje nem számít (a háttér rendezi az Ön számára). Ügyeljen arra, hogy hozzáadja ezt az attribútumot, ha a név már szerepel a tömbben (például ha már van olyan elem a attributeMappings, amelyben a targetAttributeName CredentialData van) – ütközési hibák merülhetnek fel, vagy a már meglévő és az új leképezések kombinálhatók egymással (általában nem kívánt eredmény). A háttérrendszer nem rendelkezik az Ön számára. 

 Ne felejtse el, hogy a felhasználók és az inetOrgperson

5. A létrehozott séma mentése 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Adja hozzá a sémát a kérelem törzsében. 

## <a name="accidental-deletes"></a>Véletlen törlések
Ez a szakasz bemutatja, hogyan lehet programozott módon engedélyezni vagy letiltani a [véletlen törléseket](how-to-accidental-deletes.md) .


### <a name="enabling-and-setting-the-threshold"></a>A küszöbérték engedélyezése és beállítása
Kétféle feladatot használhat, ezek a következők:

 - DeleteThresholdEnabled – lehetővé teszi a művelet véletlen törlésének megelőzését, ha az értéke "true" (igaz). Alapértelmezés szerint a "true" értékre van állítva.
 - DeleteThresholdValue – meghatározza, hogy a rendszer milyen számú törlést engedélyezzen a feladatok egyes végrehajtásakor, ha a véletlen törlések megakadályozása engedélyezve van. Alapértelmezés szerint a 500 érték van beállítva.  Tehát ha az érték 500, akkor a törlés engedélyezett maximális száma az egyes végrehajtásokban 499 lesz.

A küszöbértékek törlési beállításai a és a `SyncNotificationSettings` Graph használatával módosíthatók. 

Frissíteni kell a konfiguráció SyncNotificationSettings, ezért frissítse a titkokat.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Adja hozzá a következő kulcs/érték párokat az alábbi érték tömbben a végrehajtani kívánt művelet alapján:

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

A fenti példában szereplő "enabled" beállítás az értesítő e-mailek engedélyezésére/letiltására szolgál a feladatoknak a karanténba helyezésekor.


Jelenleg nem támogatottak a titkokra vonatkozó javítási kérések, így a többi érték megőrzése érdekében hozzá kell adnia a PUT kérelem törzsében szereplő összes értéket (például a fenti példában).

Az összes titok meglévő értékeit a következő paranccsal kérheti le: 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Törlés engedélyezése
Annak engedélyezéséhez, hogy a törlés a feladatoknak a karanténba helyezése után is folytatódjon, ki kell állítania a hatókörnek megfelelő "ForceDeletes" nevű újraindítást. 

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






## <a name="start-sync-job"></a>Szinkronizálási feladatok indítása
A feladatot újra lekérheti a következő parancs használatával:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

A feladatok beolvasására vonatkozó dokumentáció [itt](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta)található. 
 
A művelet elindításához adja ki ezt a kérelmet az első lépésben létrehozott egyszerű szolgáltatásnév objectId, és a feladatot létrehozó kérelemből visszaadott feladathoz tartozó azonosító.

A feladatok elindításához szükséges dokumentáció [itt](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta)található. 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

A várt válasz:... HTTP 204/nincs tartalom.

A feladatok szabályozására szolgáló többi parancsot [itt](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)dokumentáljuk.
 
A feladatok újraindításához az egyik a következő lesz:...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Felülvizsgálat állapota
A feladatok állapotának lekérése a-on keresztül...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

A megfelelő részletekért tekintse meg a Return objektum "status" szakaszát.

## <a name="next-steps"></a>Következő lépések 

- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [Átalakítások](how-to-transformation.md)
- [Azure AD szinkronizációs API](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
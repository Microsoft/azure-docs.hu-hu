---
title: Azure FarmBeats API-k
description: Ismerje meg az Azure FarmBeats API-kat, amelyek egy szabványosított REST-felülettel és JSON-alapú válaszokkal biztosítják a mezőgazdasági vállalkozásokat.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674507"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats API-k

Ez a cikk az Azure FarmBeats API-kat ismerteti. Az Azure FarmBeats API-k szabványosított REST-felületet biztosító, JSON-alapú válaszokat biztosítanak a mezőgazdasági vállalkozásoknak az Azure FarmBeats képességeinek kihasználása érdekében, például:

- API-k az érzékelő, a kamera, a drone, az időjárás, a műhold és a megválogatott alapértékek beszerzéséhez.
- Az adatnormalizálás és contextualization a közös adatszolgáltatókon keresztül.
- Sematikus hozzáférés és lekérdezési képességek az összes betöltött adattal kapcsolatban.
- A lekérdezhető metaadatok automatikus generálása agronómiai-funkciók alapján.
- Automatikusan generált idősorozat-összesítések a gyors modell-létrehozáshoz.
- Integrált Azure Data Factory motor, amellyel egyszerűen hozhat létre egyéni adatfeldolgozási folyamatokat.

## <a name="application-development"></a>Alkalmazásfejlesztés

A FarmBeats API-k hencegő technikai dokumentációt tartalmaznak. Az API-kkal és a hozzájuk kapcsolódó kérésekkel és válaszokkal kapcsolatos információkért lásd: [hencegés](https://aka.ms/FarmBeatsDatahubSwagger).

A következő táblázat összefoglalja a FarmBeats-Datahub található összes objektumot és erőforrást:

| Objektumok és erőforrások | Description
--- | ---|
Farm | A farm a FarmBeats rendszeren belüli fizikai helynek felel meg. Minden Farm rendelkezik egy farm nevével és egy egyedi Farm-AZONOSÍTÓval. |
Eszköz  | Az eszköz megfelel a farmon található fizikai eszköznek. Minden eszköz egyedi AZONOSÍTÓval rendelkezik. Az eszközök általában Farm-AZONOSÍTÓval rendelkező farmhoz vannak kiépítve.
DeviceModel  | A DeviceModel az eszköz metaadatainak felelnek meg, például a gyártó és az eszköz típusa, amely átjáró vagy csomópont lehet.
Érzékelő  | Az érzékelő olyan fizikai érzékelőnek felel meg, amely értékeket rögzít. Az érzékelő általában eszköz-AZONOSÍTÓval van csatlakoztatva egy eszközhöz.
SensorModel  | A SensorModel megfelel az érzékelő metaadatainak, például a gyártó, az érzékelő típusa, amely az analóg vagy a digitális, valamint az érzékelő mérése, például a környezeti hőmérséklet és a nyomás.
Telemetria  | A telemetria lehetővé teszi egy adott érzékelő és időtartomány telemetria-üzeneteinek olvasását.
Feladat  | A feladat megfelel a FarmBeats rendszer által a kívánt kimenet megszerzéséhez végrehajtott tevékenységek munkafolyamatainak. Mindegyik feladattípus egy adott AZONOSÍTÓJÚ és feladattípushoz van társítva.
JobType  | A JobType a rendszer által támogatott különböző feladatoknak felel meg. A rendszer által definiált és felhasználó által definiált feladattípusok szerepelnek benne.
ExtendedType  | A ExtendedType megfelel a rendszer és a felhasználó által definiált típusok listájának. A ExtendedType segítségével új érzékelő, jelenet vagy jelenet típusú fájl állítható be a FarmBeats rendszerbe.
Partner  | A partner megfelel a FarmBeats érzékelő-és képintegrációs partnerének.
Jelenet  | A jelenet a farm környezetében generált kimenetnek felel meg. Minden jelenet rendelkezik egy jelenet-AZONOSÍTÓval, egy jelenet forrásával, a jelenet típusával és a hozzá társított Farm-AZONOSÍTÓval. Minden egyes jelenet-AZONOSÍTÓhoz több jelenet is tartozhat.
SceneFile |A SceneFile az egyetlen jelenethez létrehozott összes fájlnak felel meg. Egyetlen jelenet-AZONOSÍTÓhoz több SceneFile-azonosító is tartozhat.
Szabály  |A szabály megfelel a farmhoz kapcsolódó, riasztást kiváltó adat feltételének. Mindegyik szabály egy farm adattartalmának kontextusában van.
Riasztás  | A riasztás megfelel egy értesítésnek, amely akkor jön létre, amikor egy szabály feltétele teljesül. Minden riasztás egy szabály kontextusában van.
Definíciós  | A definíciós az engedélyezett és a nem engedélyezett műveleteket definiálja egy szerepkörhöz.
RoleAssignment  |A RoleAssignment megfelel a szerepkör felhasználóhoz vagy egyszerű szolgáltatáshoz való hozzárendelésének.

### <a name="data-format"></a>Adatformátum

A JSON egy közös nyelvtől független adatformátum, amely tetszőleges adatstruktúrák egyszerű szöveges ábrázolását teszi lehetővé. További információ: [JSON-webhely](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

A REST API HTTP-kérelmeket az Azure Active Directory (Azure AD) védi.
Ahhoz, hogy a REST API-khoz hitelesített kérelmet lehessen készíteni, az ügyfél kódjának az API meghívása előtt érvényes hitelesítő adatokkal kell rendelkeznie a hitelesítéshez. A hitelesítés a különböző szereplők által az Azure AD-ben koordinálva van. Hozzáférési jogkivonattal látja el az ügyfelet a hitelesítés bizonyítékaként. Ezután a jogkivonat a REST API kérelmek HTTP-engedélyezési fejlécében lesz elküldve. Az Azure AD-hitelesítéssel kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory](https://portal.azure.com) fejlesztők számára című témakört.

A hozzáférési jogkivonatot a következő API-kérelmekben kell elküldeni a fejléc szakaszban, ahogy:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP-kérelmek fejlécei

Az Azure FarmBeats Datahub API-hívásakor a leggyakoribb kérések fejléceit kell megadnia.


**Fejléc** | **Leírás és példa**
--- | ---
Content-Type  | A kérelem formátuma (Content-Type: Application/ <format> ). Az Azure FarmBeats Datahub API-k formátuma a következő: JSON. Content-Type: Application/JSON
Engedélyezés  | Meghatározza az API-hívások létrehozásához szükséges hozzáférési jogkivonatot. Engedélyezés: tulajdonos <Access-Token>
Elfogadás | A válasz formátuma. Az Azure FarmBeats Datahub API-k formátuma a következő: JSON. Elfogadás: alkalmazás/JSON

### <a name="api-requests"></a>API-kérelmek

REST API kérelem elvégzéséhez kombinálja a HTTP-(GET, POST, PUT vagy DELETE) metódust, az API szolgáltatás URL-címét, az erőforráshoz tartozó URI-t a lekérdezéshez, az adatküldéshez, a frissítéshez vagy a törléshez, majd adjon hozzá egy vagy több HTTP-kérelem fejlécét.

Az API-szolgáltatás URL-címe a Datahub URL-címe, például: https:// \<yourdatahub-website-name> . azurewebsites.net.

Igény szerint a lekérdezési paramétereket is megadhatja a szűréshez, korlátozhatja az adatok méretét, és rendezheti a válaszokat.

Az eszközök listájának beolvasásához a következő minta-kérelem használható:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A legtöbb GET, POST és PUT híváshoz JSON-kérést tartalmazó törzs szükséges.

A következő minta-kérelem létrehoz egy eszközt. Ez a kérelem a JSON-t a kérelem törzsében adja meg.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Lekérdezési paraméterek

REST-hívások esetén a kérések URI-ja alapján szűrheti, korlátozhatja és rendezheti az adatok méretét, és egy vagy több lekérdezési paramétert is megadhat. A lekérdezési paraméterekért tekintse meg az API dokumentációját és az egyes GET hívásokat.
Ha például lekérdezi az eszközök listáját (/Device hívása), a következő lekérdezési paramétereket lehet megadni:

![Eszközök listája](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Hibakezelés

Az Azure FarmBeats Datahub API-jai a szabványos HTTP-hibákat adják vissza. A leggyakoribb hibakódok a következők:

 |Hibakód             | Description |
 |---                    | --- |
 |200                    | Siker |
 |201                    | Sikeres létrehozás (post) |
 |400                    | Hibás kérelem. Hiba történt a kérelemben. |
 |401                    | Jogosulatlan. Az API hívója nem jogosult az erőforrás elérésére. |
 |404                    | Az erőforrás nem található |
 |5XX                    | Belső kiszolgálóhiba. A 5XX-től kezdődő hibakódok azt jelzik, hogy hiba történt a kiszolgálón. További részletekért tekintse meg a kiszolgáló naplófájljait és a következő szakaszt. |


A szabványos HTTP-hibák mellett az Azure FarmBeats Datahub API-k a következő formátumban is visszaadnak belső hibákat:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Ebben a példában a farm létrehozásakor a kötelező "Name" mező nem lett megadva a bemeneti adattartalomban. Az eredményül kapott hibaüzenet a következő:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Felhasználók vagy alkalmazások regisztrációjának hozzáadása a Azure Active Directory

Az Azure FarmBeats API-kat egy felhasználó vagy egy alkalmazás-regisztráció Azure Active Directoryban érheti el. Ha Azure Active Directory szeretné létrehozni az alkalmazás regisztrációját, kövesse az alábbi lépéseket:

1. Lépjen a [Azure Portalra](https://portal.azure.com), és válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget. Másik lehetőségként használhat egy meglévő fiókot is.
2. Új fiók esetén tegye a következőket:

    - Adjon meg egy nevet.
    - **Csak a szervezeti címtárban válassza ki a fiókokat (egyetlen bérlő)**.
    - Használja a többi mező alapértelmezett értékeit.
    - Válassza a **Regisztráció** lehetőséget.

3. Az új és meglévő alkalmazás-regisztráció **– Áttekintés** panelen tegye a következőket:

    - Rögzítse az **ügyfél-azonosítót** és a **bérlő azonosítóját**.
    - Nyissa meg a **tanúsítványokat és a titkokat** egy új ügyfél-titkos kód létrehozásához, és rögzítse az **ügyfél titkos kulcsát**.
    - Lépjen vissza az **Áttekintés** elemre, és válassza az **alkalmazás kezelése a helyi címtárban** lehetőséget.
    - Az **objektumazonosító** rögzítéséhez lépjen a **Tulajdonságok menüpontba** .

4. Nyissa meg a Datahub hencegő (https:// <yourdatahub> . azurewebsites.net/swagger/index.html), és tegye a következőket:
    - Nyissa meg a **ROLEASSIGNMENT API**-t.
    - A POST művelettel hozzon létre egy **RoleAssignment** objektumot az imént létrehozott **objektumazonosító** számára.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > A felhasználók hozzáadásával és Active Directory regisztrációval kapcsolatos további információkért lásd: [Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

Az előző lépések befejezése után az alkalmazás regisztrálása (ügyfél) az Azure FarmBeats API-kat a tulajdonosi hitelesítésen keresztül egy hozzáférési jogkivonat használatával hívhatja meg.

A hozzáférési jogkivonat segítségével küldje el azt a következő API-kérelmekben a fejléc szakaszban, ahogy:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
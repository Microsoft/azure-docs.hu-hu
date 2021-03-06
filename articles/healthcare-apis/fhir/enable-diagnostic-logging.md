---
title: Diagnosztikai naplózás engedélyezése a FHIR készült Azure API-ban
description: Ez a cikk bemutatja, hogyan engedélyezheti a diagnosztikai naplózást az Azure API FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: a884dac90273e98868fed6bfe1cbed23b939d286
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557700"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Diagnosztikai naplózás engedélyezése a FHIR készült Azure API-ban

Ebből a cikkből megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást az Azure API-ban a FHIR, és áttekintheti a naplókhoz tartozó egyes lekérdezéseket. A diagnosztikai naplókhoz való hozzáférés elengedhetetlen minden olyan egészségügyi szolgáltatáshoz, ahol a szabályozási követelményeknek (például HIPAA) való megfelelés kötelező. A diagnosztikai naplókat engedélyező Azure API-FHIR az Azure Portal [**diagnosztikai beállításai**](../../azure-monitor/essentials/diagnostic-settings.md) . 

## <a name="view-and-download-fhir-metrics-data"></a>FHIR-metrikai adatok megtekintése és letöltése

A mérőszámokat a Figyelés területen tekintheti meg | Metrikák a portálról. A metrikák közé tartozik a kérelmek száma, az átlagos késés, a hibák száma, az adatok mérete, a felhasznált RUs, a meghaladt mennyiségű kérések száma és a rendelkezésre állás (százalékban). Az alábbi képernyőképen az elmúlt 7 napban egy kis tevékenységgel rendelkező mintavételi környezetben használt RUs látható. Az adatletöltés JSON formátumban is elvégezhető.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Azure API FHIR Mérőszámokhoz a portálon" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Naplók engedélyezése
1. Ha engedélyezni szeretné a diagnosztikai naplózást az Azure API FHIR, válassza ki az Azure API-t a FHIR szolgáltatáshoz a Azure Portal 
2. Navigáljon a **diagnosztikai beállításokhoz** 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Adja hozzá az Azure FHIR diagnosztikai beállításait." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Válassza a **+ diagnosztikai beállítás hozzáadása** lehetőséget

4. Adja meg a beállítás nevét

5. Válassza ki a diagnosztikai naplók eléréséhez használni kívánt módszert:

    1. Archiválás vagy manuális ellenőrzés a **Storage-fiókba** . A használni kívánt Storage-fióknak már létre kell hoznia.
    2. **Adatfolyam küldése az Event hub** -nak egy harmadik féltől származó szolgáltatás vagy egyéni analitikai megoldás betöltéséhez. Ennek a lépésnek a konfigurálásához létre kell hoznia egy Event hub-névteret és egy Event hub-házirendet.
    3. **Stream a Azure Monitor log Analytics** munkaterületére. Ennek a lehetőségnek a kiválasztásához létre kell hoznia a naplók Analytics-munkaterületet.

6. Válassza a **AuditLogs** és/vagy a **AllMetrics** lehetőséget. A metrikák közé tartozik a szolgáltatás neve, a rendelkezésre állás, az adatok mérete, a teljes késés, a kérelmek teljes száma, az összes hiba és az időbélyeg. A [támogatott mérőszámokról](../../azure-monitor/essentials/metrics-supported.md#microsofthealthcareapisservices)részletesebben is tájékozódhat. 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Az Azure FHIR diagnosztikai beállításai. Válassza a AuditLogs és/vagy a AllMetrics lehetőséget." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Válassza a **Mentés** lehetőséget


> [!Note] 
> Akár 15 percet is igénybe vehet, hogy az első naplók megjelenjenek Log Analyticsban. Ha a FHIR készült Azure API-t egy erőforráscsoport vagy egy másik előfizetésbe helyezi át, frissítse a beállítást az áthelyezés befejeződése után. 
 
A diagnosztikai naplók használatával kapcsolatos további tudnivalókért tekintse meg az [Azure Resource log dokumentációját](../../azure-monitor/essentials/platform-logs-overview.md) .

## <a name="audit-log-details"></a>Napló részletei
Jelenleg az Azure API for FHIR szolgáltatás a következő mezőket adja vissza a naplóban: 

|Mezőnév  |Típus  |Jegyzetek  |
|---------|---------|---------|
|CallerIdentity|Dinamikus|Az azonosító adatokat tartalmazó általános tulajdonság
|CallerIdentityIssuer|Sztring|Kiállító 
|CallerIdentityObjectId|Sztring|Object_Id 
|CallerIPAddress|Sztring|A hívó IP-címe 
|CorrelationId|Sztring| Korrelációs azonosító
|FhirResourceType|Sztring|Az erőforrás típusa, amelyre a műveletet végrehajtják
|LogCategory|Sztring|A napló kategóriája (jelenleg "AuditLogs" LogCategory vissza)
|Hely|Sztring|A kérelmet feldolgozó kiszolgáló helye (például az USA déli középső régiója)
|OperationDuration|Int|A kérelem végrehajtásához szükséges idő másodpercben
|OperationName|Sztring| A művelet típusát írja le (például Update, Search-Type)
|RequestUri|Sztring|A kérelem URI-ja 
|ResultType|Sztring|Az elérhető értékek a jelenleg **elindítva**, **sikeres** vagy **sikertelen**
|StatusCode|Int|A HTTP-állapotkód. (például 200) 
|TimeGenerated|DateTime|Az esemény dátuma és időpontja|
|Tulajdonságok|Sztring| A fhirResourceType tulajdonságainak leírása
|SourceSystem|Sztring| Forrásoldali rendszerek (ebben az esetben az Azure mindig)
|TenantId|Sztring|Bérlőazonosító
|Típus|Sztring|A napló típusa (ebben az esetben mindig MicrosoftHealthcareApisAuditLog)
|_ResourceId|Sztring|Az erőforrás részletei

## <a name="sample-queries"></a>Mintalekérdezések

Íme néhány alapszintű Application Insights lekérdezés, amellyel megismerheti a naplózási adatait.

A lekérdezés futtatásával tekintse meg a **100** legújabb naplókat:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Futtassa ezt a lekérdezést, hogy **FHIR erőforrástípus** szerint csoportosítsa a műveleteket:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

A lekérdezés futtatása az összes **sikertelen eredmény** beolvasásához

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Összegzés 
A diagnosztikai naplókhoz való hozzáférés elengedhetetlen a szolgáltatás monitorozásához és a megfelelőségi jelentések biztosításához. A FHIR készült Azure API lehetővé teszi ezeket a műveleteket a diagnosztikai naplókon keresztül. 
 
Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.

## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan engedélyezheti a FHIR készült Azure API-naplókat. A következő lépés a FHIR készült Azure API-ban konfigurálható további beállítások megismerése
 
>[!div class="nextstepaction"]
>[További beállítások](azure-api-for-fhir-additional-settings.md)
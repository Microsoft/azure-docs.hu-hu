---
title: Az összekötő állapotával kapcsolatos hibák a ITSMC irányítópultján
description: Ismerkedjen meg a IT-szolgáltatásmenedzsmenti csatoló-irányítópulton található gyakori hibákkal.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471526"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Az összekötő állapotával kapcsolatos hibák a ITSMC irányítópultján

A IT-szolgáltatásmenedzsmenti csatoló (ITSMC) irányítópulton olyan hibák jelennek meg, amelyek segíthetnek az összekötővel kapcsolatos problémák megoldásában.

A következő szakaszok ismertetik az irányítópult összekötő állapota szakaszában megjelenő gyakori hibákat, valamint a megoldásuk módját.

## <a name="unexpected-response"></a>Váratlan válasz

**Hiba**: "váratlan válasz érkezett a ServiceNow, valamint a sikeres állapotkód. Válasz: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [{"transform_map": "OMS incidens", "Table": "incidens", "status": "hiba", "error_message": "{a cél rekord nem található | Érvénytelen tábla | Érvénytelen előkészítési tábla: "}"

**OK**: a ServiceNow ezt a hibát adja vissza a következő esetekben:

* A ServiceNow-példányokban üzembe helyezett egyéni parancsfájlok figyelmen kívül hagyják az incidenseket.
* A "OMS integrátor alkalmazás" kódot módosították a ServiceNow oldalon (például a `onBefore` szkripten keresztül).

**Megoldás**: tiltsa le az összes egyéni parancsfájlt vagy programkód-módosítást.

## <a name="exception-update-failure"></a>Kivétel-frissítési hiba

**Hiba**: a (z) "{" hiba ": {" üzenet ":" sikertelen művelet "," Részletek ":" ACL-kivétel frissítése sikertelen volt a biztonsági korlátozások miatt "}

**OK**: a ServiceNow engedélyei helytelenül vannak konfigurálva.

**Megoldás**: Győződjön meg arról, hogy az összes szerepkör megfelelően van-e rendelve a [megadott](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)módon.

## <a name="problem-sending-a-request"></a>Hiba a kérés küldésekor

**Hiba**: "hiba történt a kérelem küldése közben."

**OK**: a ServiceNow-példány nem érhető el.

**Megoldás**: vizsgálja meg a példányt a ServiceNow-ben. Lehet, hogy törölték, vagy nem érhető el.

## <a name="servicenow-rate-problem"></a>ServiceNow-díjszabási probléma

**Hiba**: "ServiceDeskHttpBadRequestException: statuscode = 429"

**OK**: a ServiceNow arányának korlátai túl magasak vagy túl alacsonyak.

**Megoldás**: a [ServiceNow dokumentációjában](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)ismertetett módon növelje vagy szakítsa meg a ServiceNow-példányra vonatkozó díjszabást.

## <a name="invalid-refresh-token"></a>Érvénytelen frissítési jogkivonat

**Hiba**: 
  * "A AccessToken és a RefreshToken érvénytelen. A felhasználónak újra kell hitelesítenie magát. "
  * "Nem lehetett szinkronizálni a sablonok konfigurációját az eseményekhez, a riasztásokhoz, az incidensekhez. További részletekért tekintse meg a kivételt jelző üzenetet. "

**OK**: a frissítési jogkivonat lejárt.

**Megoldás**: a Sync ITSMC új frissítési token létrehozásához a szinkronizálási [problémák manuális javítását](./itsmc-resync-servicenow.md)ismerteti.

## <a name="missing-connector"></a>Hiányzó összekötő

**Hiba**: "nem sikerült létrehozni/frissíteni a munkaelemet a (z) {alertName} riasztáshoz. A (z) {connectionIdentifier} ITSM-csatoló nem létezik vagy törölték. "

**OK**: a ITSMC törölve lett.

**Megoldás**: a ITSMC törölve lett, de a definiált IT Service Management (ITSM) műveleti csoportok továbbra is hozzá vannak rendelve. A probléma megoldásához három lehetőség közül választhat:

* Megkeresheti és letilthatja vagy törölheti az ilyen műveleti csoportokat.
* [Konfigurálja újra a műveleti csoportokat](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) egy meglévő ITSMC-példány használatára.
* [Hozzon létre egy új ITSMC-példányt](./itsmc-definition.md#create-an-itsm-connection) , és [konfigurálja újra a műveleti csoportokat a használatára](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Kapcsolat részleteinek hiánya

**Hiba**: "probléma történt. Nem lehetett lekérni a kapcsolat részleteit. " Ez a hiba akkor jelenik meg, ha ITSM-műveleti csoportot ad meg.

**OK**: ilyen hiba a következő helyzetekben jelenik meg:

* Az újonnan létrehozott ITSM-csatoló példánynak még be kell fejeznie a kezdeti szinkronizálást.
* Az összekötő nem lett megfelelően definiálva.

**Megoldás**: 

* Új ITSMC-példány létrehozásakor elkezdi szinkronizálni az adatokat a ITSM rendszerből, például a munkaelemek sablonjait és a munkaelemeket. [ITSMC szinkronizálása új frissítési jogkivonat létrehozásához](./itsmc-resync-servicenow.md).
* [Tekintse át a kapcsolat részleteit a ITSMC](./itsmc-connections-servicenow.md#create-a-connection) , és ellenőrizze, hogy a ITSMC sikeresen tud-e [szinkronizálni](./itsmc-resync-servicenow.md).


## <a name="ip-restrictions"></a>IP-korlátozások
**Hiba**: "nem sikerült hozzáadni a (z)" xxx "nevű ITSM-kapcsolódást a helytelen kérelem miatt. Hiba: hibás kérelem. Érvénytelen paraméterek vannak megadva a kapcsolatok számára. Http-kivétel: tiltott állapotkód. "

**OK**: a ITSM-alkalmazás IP-címe nem teszi lehetővé a ITSM-kapcsolatok elérését a partnerek ITSM eszközeiből.

**Megoldás**: a ITSM IP-címeinek listázásához, hogy az ITSM-kapcsolatok elérhetők legyenek a partnerek ITSM eszközeitől, javasoljuk, hogy az Azure-régió teljes nyilvános IP-tartományát sorolja fel, ahol a LogAnalytics-munkaterület tartozik. [részletek](https://www.microsoft.com/download/details.aspx?id=56519) A EUS/NYEU/EUS2/WUS2/USA déli középső régiójában az ügyfél csak a ActionGroup hálózati címkét listázhatja.

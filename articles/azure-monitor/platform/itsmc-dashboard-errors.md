---
title: Gyakori hibák
description: Ez a dokumentum az irányítópulton található gyakori hibákról tartalmaz információkat.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: be6d47d8f40746bfb2154ddb62cf2e9ce93e74aa
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955683"
---
# <a name="errors-in-the-connector-status-section"></a>Hibák az összekötő állapota szakaszban

Az irányítópult összekötő állapota lista szakaszában olyan hibákat talál, amelyek segíthetnek a ITSM-összekötő hibáinak kijavításában.

## <a name="status-common-errors"></a>Gyakori hibák állapota

Ebben a szakaszban megtalálhatja az összekötő állapota szakaszban bemutatott gyakori hibákat, valamint azt, hogyan oldja fel őket:

* **Hiba**: "váratlan válasz érkezett a ServiceNow, valamint a sikeres állapotkód. Válasz: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [{"transform_map": "OMS incidens", "Table": "incidens", "status": "hiba", "error_message": "{a cél rekord nem található | Érvénytelen tábla | Érvénytelen előkészítési tábla: "}"

    **OK**: ezt a hibát a ServiceNow a következő esetekben adja vissza:
  * A ServiceNow-példányban üzembe helyezett egyéni parancsfájlok figyelmen kívül hagyják az incidenseket.
  * A "OMS integrátori alkalmazás" kódját maga a ServiceNow oldalon módosították, például a onBefore-szkriptet.

  **Megoldás**: tiltsa le az összes egyéni parancsfájlt vagy programkód-módosítást.

* **Hiba**: a (z) "{" hiba ": {" üzenet ":" sikertelen művelet "," Részletek ":" ACL-kivétel frissítése sikertelen volt a biztonsági korlátozások miatt "}

    **OK**: a ServiceNow engedélyeinek helytelen konfigurációja

    **Megoldás**: Győződjön meg arról, hogy az összes szerepkör megfelelően van-e rendelve a [megadott](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)módon.

* **Hiba**: "hiba történt a kérelem küldése közben."

    **OK**: "a ServiceNow példánya nem érhető el"

    **Megoldás**: Győződjön meg róla, hogy a példánya a ServiceNow törölve lett, vagy nem érhető el.

* **Hiba**: "ServiceDeskHttpBadRequestException: statuscode = 429"

    **OK**: a ServiceNow arányának korlátai túl magasak/alacsonyak.

    **Megoldás**: növelje vagy szakítsa meg a ServiceNow-példányon a díjszabási korlátokat az [itt](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)leírtak szerint.

* **Hiba**: a AccessToken és a RefreshToken érvénytelen. A felhasználónak újra kell hitelesítenie magát. "

    **OK**: a frissítési jogkivonat lejárt.

    **Megoldás**: az [itt](./itsmc-resync-servicenow.md)leírtak szerint szinkronizálja a ITSM-csatoló az új frissítési jogkivonat létrehozásához.

* **Hiba**: "nem sikerült létrehozni/frissíteni a munkaelemet a (z) {alertName} riasztáshoz. A (z) {connectionIdentifier} ITSM-csatoló nem létezik vagy törölték. "

    **OK**: ITSM-csatoló törölve.

    **Megoldás**: a ITSM-csatoló törölve lett, de még ITSM-műveleti csoportok vannak társítva. A probléma megoldásához 2 lehetőség közül választhat:
  * Ilyen műveleti csoportok keresése és letiltása vagy törlése
  * [Konfigurálja újra a műveleti csoportot](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) meglévő ITSM-csatoló használatára.
  * [Hozzon létre egy új ITSM-összekötőt](./itsmc-definition.md#create-an-itsm-connection) , és [konfigurálja újra a műveleti csoportot a használatára](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Gyakori felhasználói felületi hibák

* **Hiba**: "probléma történt. Nem lehetett lekérni a kapcsolat részleteit. " Ez a hiba akkor jelenik meg, amikor az ügyfél definiálja az ITSM műveleti csoportot.

    **OK**: Ez a hiba akkor jelenik meg, ha:
    * Az újonnan létrehozott ITSM-csatoló még befejezte a kezdeti szinkronizálást.
    * Az összekötő nincs megfelelően definiálva.

    **Megoldás**: 
    * Új ITSM-összekötő létrehozásakor ITSM-csatoló elindítja az információk szinkronizálását a ITSM rendszerből, például a munkaelemek sablonjait és a munkaelemeket. Az [itt](./itsmc-resync-servicenow.md)leírtak szerint szinkronizálja a ITSM-csatoló egy új frissítési jogkivonat létrehozásához.
    * Tekintse át a kapcsolat részleteit a ITSM-összekötőben az [itt](./itsmc-connections-servicenow.md#create-a-connection) leírtak szerint, és ellenőrizze, hogy a ITSM-összekötő sikeresen [szinkronizálható](./itsmc-resync-servicenow.md)-e.

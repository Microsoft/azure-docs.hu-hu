---
title: A listák kezelése az Azure Sentinelben REST API használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan kezelhető az Azure Sentinel-lista a listák és azok elemeinek létrehozásához, módosításához és törléséhez Log Analytics REST API használatával.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798465"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>A listák kezelése az Azure Sentinelben REST API használatával

> [!IMPORTANT]
> A lists szolgáltatás jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Az Azure Sentinel a Azure Monitor Log Analytics részeként készült, és lehetővé teszi Log Analytics "REST API használatát a listák kezelésére. Ebből a dokumentumból megtudhatja, hogyan hozhat létre, módosíthat és törölhet listaelemeket és azok elemeit a REST API használatával.  Az ily módon létrehozott listák az Azure Sentinel felhasználói felületén fognak megjelenni.

## <a name="common-uri-parameters"></a>Általános URI-paraméterek

Az összes List API-parancshoz a következő általános URI-paraméterek tartoznak:

| Name | In | Kötelező | Típus | Leírás |
|-|-|-|-|-|
| **SubscriptionId** | path | igen | GUID | Az Azure-előfizetés azonosítója |
| **ResourceGroupName** | path | igen | sztring | az előfizetéshez tartozó erőforráscsoport neve |
| **WorkspaceName** | path | igen | sztring | az Log Analytics munkaterület neve |
| **{watchlistAlias}** | path | igen | sztring | egy adott listának neve<br>\* Az összes lista beolvasása nem kötelező |
| **{watchlistItemId}** | path | igen * * | GUID | a listáról létrehozni, hozzáadni vagy törölni kívánt elem azonosítója<br>\*\* Csak a listához tartozó parancsokhoz szükséges |
| **{API-Version}** | lekérdezés | igen | sztring | a kérelem elvégzéséhez használt protokoll verziója. 2020. október 29-én a List API verziója *2019-01-01 – előzetes* verzió |
| **{bearerToken}** | engedélyezés | igen | jogkivonat | a tulajdonos engedélyezési jogkivonata |
|  

## <a name="retrieve-all-watchlists"></a>Az összes listának beolvasása

Ezzel a paranccsal a munkaterülethez társított összes listát lekérdezheti az elemek nélkül.

### <a name="request-uri"></a>Kérés URI-ja
(Az URI egyetlen sor, amely az egyszerű olvashatóság érdekében feltört)

| Metódus | Kérés URI-ja |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Válaszok

| Állapotkód | Választörzs | Leírás |
|-|-|-|
| 200/OK | Meglévő listák listája, vagy üres, ha nem található |  |
| 400/hibás kérelem |  | Helytelen formátumú kérelem szintaxisa, érvénytelen lekérdezési paraméter... |
|

## <a name="look-up-a-watchlist-by-name"></a>Listára való keresés név szerint

Ezzel a paranccsal egy adott, a munkaterülethez társított listát kérdezi le, az elemek nélkül.

### <a name="request-uri"></a>Kérés URI-ja
(Az URI egyetlen sor, amely az egyszerű olvashatóság érdekében feltört)

| Metódus | Kérés URI-ja |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Válaszok

| Állapotkód | Választörzs | Leírás |
|-|-|-|
| 200/OK | A kért listához |  |
| 400/hibás kérelem |  | Helytelen formátumú kérelem szintaxisa, érvénytelen lekérdezési paraméter... |
| 404/nem található |  | Nem található a kért nevű listával |
|

## <a name="create-a-watchlist"></a>Listának létrehozása

Ez a parancs létrehoz egy listát, és hozzáad elemeket. A rendszer két hívást kezdeményez a végpont létrehozásához a listához és annak elemeihez: az első létrehoz egy listát (szülő), a második pedig hozzáadja az elemeket.

### <a name="request-uri"></a>Kérés URI-ja
(Az URI egyetlen sor, amely az egyszerű olvashatóság érdekében feltört)

| Metódus | Kérés URI-ja |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>A kérés törzse

Az alábbi példa egy listáról szóló Create kérelem törzsét mutat be:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Válaszok

| Állapotkód | Választörzs | Leírás |
|-|-|-|
| 200/OK | A kérelem által létrehozott, elemek nélküli listás |  |
| 400/hibás kérelem |  | Helytelen formátumú kérelem szintaxisa, érvénytelen lekérdezési paraméter... |
| 409/ütközés |  | A művelet sikertelen volt, és létezik egy meglévő List ugyanazzal az aliassal. |
|

## <a name="delete-a-watchlist"></a>Listának törlése

Ez a parancs törli a listát és annak elemeit.

### <a name="request-uri"></a>Kérés URI-ja
(Az URI egyetlen sor, amely az egyszerű olvashatóság érdekében feltört)

| Metódus | Kérés URI-ja |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Válaszok

| Állapotkód | Választörzs | Leírás |
|-|-|-|
| 200/OK | Üres válasz törzse |  |
| 204/nincs tartalom | Üres válasz törzse | Nincs törölve |
| 400/hibás kérelem |  | Helytelen formátumú kérelem szintaxisa, érvénytelen lekérdezési paraméter... |
|

## <a name="add-or-update-a-watchlist-item"></a>Listához tartozó elem hozzáadása vagy frissítése

Ha a parancs egy meglévő *watchlisItemId* fut (GUID), akkor az elem a kérelem törzsében megadott adattal frissül. Ellenkező esetben új elem jön létre.

### <a name="request-uri"></a>Kérés URI-ja
(Az URI egyetlen sor, amely az egyszerű olvashatóság érdekében feltört)

| Metódus | Kérés URI-ja |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>A kérés törzse

Az alábbi példa egy listára vonatkozó, a feltételt hozzáadó/frissítő kérelemre vonatkozó kérelem törzse:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Válaszok

| Állapotkód | Választörzs | Leírás |
|-|-|-|
| 200/OK | A kérelem által létrehozott vagy frissített listához tartozó tétel |  |
| 400/hibás kérelem |  | Helytelen formátumú kérelem szintaxisa, érvénytelen lekérdezési paraméter... |
| 409/ütközés |  | A művelet sikertelen volt, és létezik egy meglévő List ugyanazzal az aliassal. |
|

## <a name="delete-a-watchlist-item"></a>Listához tartozó elemek törlése

Ez a parancs töröl egy meglévő listát.

### <a name="request-uri"></a>Kérés URI-ja
(Az URI egyetlen sor, amely az egyszerű olvashatóság érdekében feltört)

| Metódus | Kérés URI-ja |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Válaszok

| Állapotkód | Választörzs | Leírás |
|-|-|-|
| 200/OK | Üres válasz törzse |  |
| 204/nincs tartalom | Üres válasz törzse | Nincs törölve |
| 400/hibás kérelem |  | Helytelen formátumú kérelem szintaxisa, érvénytelen lekérdezési paraméter... |
|

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan kezelheti a listák és azok elemeit az Azure Sentinelben a Log Analytics API használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- További információ a [listáról](watchlists.md)
- Az [Azure Sentinel API](/rest/api/securityinsights/) egyéb használatának megismerése
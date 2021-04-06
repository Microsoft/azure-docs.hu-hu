---
title: Az Azure Sentinel UEBA-bővítések referenciája | Microsoft Docs
description: Ez a cikk az Azure Sentinel Entity Behavior Analytics által generált entitás-bővítéseket jeleníti meg.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901726"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Az Azure Sentinel UEBA gazdagítása – dokumentáció

Ezek a táblázatok felsorolják és leírják azokat az entitás-bővítéseket, amelyek felhasználhatók a biztonsági incidensek kivizsgálásának koncentrálására és élesítésére.

Az első két táblázat, a **felhasználói** elemzések és az **eszközök bepillantást nyerhet** Active Directory/Azure ad-ből és a Microsoft Threat Intelligence-forrásaiból származó entitásokra vonatkozó információkkal.

<a name="baseline-explained"></a>A többi táblázat a tevékenység-elemzési **táblázatok** alatt az Azure Sentinel entitások viselkedésének elemzésével létrehozott viselkedési profilok alapján tartalmazza az entitásokra vonatkozó információkat. A tevékenységek elemzése olyan alapkonfiguráción történik, amely minden használatkor dinamikusan le van fordítva. Minden tevékenységhez tartozik annak a meghatározott lookback-időtartama, amelyből a dinamikus alapterv származtatva van. Ez az időszak a tábla alapkonfiguráció [**oszlopában**](#activity-insights-tables) van megadva.

> [!NOTE] 
> A **dúsítás neve** mező mindhárom táblában két sornyi információt jelenít meg. Az első, **félkövérrel**, a dúsítás "rövid neve". A második *(dőlt és zárójelek között)* a [**viselkedési elemzési táblázatban**](identify-threats-with-entity-behavior-analytics.md#data-schema)tárolt dúsítás mező neve.

## <a name="user-insights-table"></a>Felhasználói adatáttekintési táblázat

| Dúsítás neve | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **Fiók megjelenített neve**<br>*(AccountDisplayName)* | A felhasználó fiókjának megjelenített neve. | Admin, Hayden Cook |
| **Fiók tartománya**<br>*(AccountDomain)* | A felhasználó fiókjának tartományneve. |  |
| **Fiók objektumának azonosítója**<br>*(AccountObjectID)* | A felhasználó fiók-objektumának azonosítója. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Blast sugár**<br>*(BlastRadius)* | A Blast sugár kiszámítása több tényező alapján történik: a felhasználó pozíciója a szervezeti fában, valamint a felhasználó Azure Active Directory szerepkörei és engedélyei. | Alacsony, közepes és magas |
| **Alvó fiók**<br>*(IsDormantAccount)* | A fiók nem lett használatban az elmúlt 180 napban. | Igaz, hamis |
| **Helyi rendszergazda**<br>*(IsLocalAdmin)* | A fiók helyi rendszergazdai jogosultságokkal rendelkezik. | Igaz, hamis |
| **Új fiók**<br>*(IsNewAccount)* | A fiók az elmúlt 30 napban lett létrehozva. | Igaz, hamis |
| **Helyszíni SID**<br>*(OnPremisesSID)* | A művelethez kapcsolódó felhasználó helyszíni biztonsági azonosítója. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Eszköz-áttekintési táblázat

| Dúsítás neve | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **Böngésző**<br>*Böngésző* | A műveletben használt böngésző. | Edge, Chrome |
| **Eszközcsalád**<br>*(DeviceFamily)* | A műveletben használt eszköz családja. | Windows |
| **Eszköz típusa**<br>*DeviceType* | A műveletben használt ügyfél-eszköz típusa | Asztali |
| **ISP**<br>*ISP* | A műveletben használt internetszolgáltató. |  |
| **Operációs rendszer**<br>*OperatingSystem* | A műveletben használt operációs rendszer. | Windows 10 |
| **Veszélyforrás Intel-kijelző leírása**<br>*(ThreatIntelIndicatorDescription)* | A megfigyelt fenyegetés mutatójának leírása, amely a műveletben használt IP-címről lett feloldva. | A gazdagép a botnet tagja: azorult |
| **Veszélyforrás Intel kijelző típusa**<br>*(ThreatIntelIndicatorType)* | A műveletben használt IP-címről feloldott veszélyforrás-jelző típusa. | Botnet, C2, CryptoMining, Darknet, DDoS, MaliciousUrl, malware, phishing, proxy, PUA, List |
| **Felhasználói ügynök**<br>*UserAgent* | A műveletben használt felhasználói ügynök. | Microsoft Azure Graph ügyféloldali kódtár 1,0,<br>Hencegés – CODEGEN/1.4.0.0/csharp,<br>EvoSTS |
| **Felhasználói ügynök családja**<br>*(UserAgentFamily)* | A műveletben használt felhasználói ügynök családja. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Tevékenység-összeírási táblázatok

#### <a name="action-performed"></a>Művelet elvégezve

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **A felhasználó első alkalommal végrehajtott művelete**<br>*(FirstTimeUserPerformedAction)* | 180 | A műveletet a felhasználó első alkalommal hajtotta végre. | Igaz, hamis |
| **A felhasználó által nem közösen végrehajtott művelet**<br>*(ActionUncommonlyPerformedByUser)* | 10 | A műveletet a felhasználó általában nem végzi el. | Igaz, hamis |
| **Nem gyakori műveletek a társak között**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | A művelet végrehajtása általában nem történik meg a felhasználó társai között. | Igaz, hamis |
| **Első művelet végrehajtása a bérlőben**<br>*(FirstTimeActionPerformedInTenant)* | 180 | A műveletet a szervezet minden felhasználója először hajtotta végre. | Igaz, hamis |
| **Nem közösen végrehajtott művelet a bérlőben**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | A művelet végrehajtása általában nem történik meg a szervezetben. | Igaz, hamis |
|

#### <a name="app-used"></a>Alkalmazás használatban

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **A felhasználó első alkalommal használt alkalmazás**<br>*(FirstTimeUserUsedApp)* | 180 | Az alkalmazást a felhasználó első alkalommal használta. | Igaz, hamis |
| **Az alkalmazást a felhasználó ritkán használja**<br>*(AppUncommonlyUsedByUser)* | 10 | A felhasználó általában nem használja az alkalmazást. | Igaz, hamis |
| **Az alkalmazás szokatlanul használatban van a társak között**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | Az alkalmazás használata általában nem használatos a felhasználó társai között. | Igaz, hamis |
| **Első alkalommal megfigyelt alkalmazás a bérlőben**<br>*(FirstTimeAppObservedInTenant)* | 180 | Az alkalmazást a szervezet első alkalommal észlelte. | Igaz, hamis |
| **Az alkalmazás nem általánosan használatban van a bérlőben**<br>*(AppUncommonlyUsedInTenant)* | 180 | Az alkalmazást általában nem használják a szervezeten belül. | Igaz, hamis |
| 

#### <a name="browser-used"></a>Használt böngésző

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **Első alkalommal, amikor a felhasználó csatlakozik a böngészőn keresztül**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | A felhasználó első alkalommal észlelte a böngészőt. | Igaz, hamis |
| **A böngészőt nem gyakran használják a felhasználó**<br>*(BrowserUncommonlyUsedByUser)* | 10 | A felhasználó nem használja gyakran a böngészőt. | Igaz, hamis |
| **A böngésző szokatlanul használatban van a társak között**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | A böngésző általában nem használatos a felhasználó társai között. | Igaz, hamis |
| **Első alkalommal a böngésző megfigyelt a bérlőben**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | A böngészőt a rendszer első alkalommal észlelte a szervezetben. | Igaz, hamis |
| **A böngésző szokatlanul használatban van a bérlőben**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | A böngészőt általában nem használják a szervezeten belül. | Igaz, hamis |
| 

#### <a name="country-connected-from"></a>A következő helyről csatlakozó ország:

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **Első alkalommal, amikor a felhasználó kapcsolódott az országból**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | Az IP-címről feloldott földrajzi hely a felhasználó általi első alkalommal kapcsolódott. | Igaz, hamis |
| **Az ország nem közösen csatlakozik a felhasználótól**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | A földrajzi helyet, az IP-címről feloldott módon, nem a felhasználótól gyakran csatlakoztatja. | Igaz, hamis |
| **Az ország szokatlanul kapcsolódott a partnerek közül**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | A földrajzi hely, az IP-címről feloldott módon, nem kapcsolódik gyakran a felhasználó társaihoz. | Igaz, hamis |
| **Első alkalommal, amikor az ország megfigyelt a bérlőben**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Az ország első alkalommal kapcsolódott a szervezeten belül bárkitől. | Igaz, hamis |
| **Az ország nem közösen csatlakozik a bérlőhöz**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | A földrajzi hely, az IP-cím alapján feloldva, nem kapcsolódik gyakran a szervezethez. | Igaz, hamis |
| 

#### <a name="device-used-to-connect"></a>A kapcsolódáshoz használt eszköz

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **Első alkalommal, amikor a felhasználó csatlakozik az eszközről**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | A felhasználó először csatlakoztatta a forrás eszközt a szolgáltatáshoz. | Igaz, hamis |
| **Az eszközt nem gyakran használják a felhasználó**<br>*(DeviceUncommonlyUsedByUser)* | 10 | A felhasználó nem használja általában az eszközt. | Igaz, hamis |
| **Az eszköz ritkán használatos a társak között**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | Az eszköz általában nem használatos a felhasználó társai között. | Igaz, hamis |
| **Első alkalommal megfigyelt eszköz a bérlőben**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Az eszközt a rendszer első alkalommal észlelte a szervezetben. | Igaz, hamis |
| **Az eszköz nem általánosan használatban van a bérlőben**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Az eszközt általában nem használják a szervezeten belül. | Igaz, hamis |
| 

#### <a name="other-device-related"></a>Az eszközhöz kapcsolódó egyéb

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **A felhasználó első bejelentkezése az eszközre**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | A célként megadott eszköz először kapcsolódott a felhasználóhoz. | Igaz, hamis |
| **Az eszköz családja szokatlanul használatban van a bérlőben**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | Az eszköz családja általában nem használatos a szervezetben. | Igaz, hamis |
| 

#### <a name="internet-service-provider-used-to-connect"></a>A kapcsolódáshoz használt internetszolgáltató

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **Első alkalommal, amikor a felhasználó az INTERNETSZOLGÁLTATÓn keresztül csatlakozik**<br>*(FirstTimeUserConnectedViaISP)* | 30 | A felhasználó első alkalommal észlelte az INTERNETSZOLGÁLTATÓt. | Igaz, hamis |
| **Az INTERNETSZOLGÁLTATÓt ritkán használják a felhasználó**<br>*(ISPUncommonlyUsedByUser)* | 10 | A felhasználó nem használja általában az INTERNETSZOLGÁLTATÓt. | Igaz, hamis |
| **Az INTERNETSZOLGÁLTATÓ szokatlanul használatban van a társak között**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | Az INTERNETSZOLGÁLTATÓ általában nem használatos a felhasználó társai között. | Igaz, hamis |
| **Első alkalommal, amikor az ISP-n keresztül csatlakozik a bérlőhöz**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | Az INTERNETSZOLGÁLTATÓt először észlelték a szervezeten belül. | Igaz, hamis |
| **Az INTERNETSZOLGÁLTATÓ használata nem gyakori a bérlőben**<br>*(ISPUncommonlyUsedInTenant)* | 30 | Az INTERNETSZOLGÁLTATÓt általában nem használják a szervezeten belül. | Igaz, hamis |
| 

#### <a name="resource-accessed"></a>Elért erőforrás

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **Első alkalommal, amikor a felhasználó hozzáfért az erőforráshoz**<br>*(FirstTimeUserAccessedResource)* | 180 | A felhasználó első alkalommal használta az erőforrást. | Igaz, hamis |
| **A felhasználó nem általánosan hozzáfért az erőforráshoz**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | A felhasználó általában nem fér hozzá az erőforráshoz. | Igaz, hamis |
| **Az erőforrás nem általánosan elérhető a társak között**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | Az erőforrás általában nem érhető el a felhasználó társai között. | Igaz, hamis |
| **Első alkalommal, amikor az erőforrás elérhető a bérlőben**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | Az erőforrást a szervezet minden felhasználója először használta. | Igaz, hamis |
| **Nem általánosan hozzáfért erőforrás a bérlőben**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | Az erőforrás általában nem érhető el a szervezeten belül. | Igaz, hamis |
| 

#### <a name="miscellaneous"></a>Különböző veszélyes anyagok és tárgyak

| Dúsítás neve | [Alapterv](#baseline-explained) (nap) | Leírás | Mintaérték |
| --- | --- | --- | --- |
| **A felhasználó legutóbb végrehajtott művelete**<br>*(LastTimeUserPerformedAction)* | 180 | A felhasználó legutóbbi végrehajtásakor ugyanezt a műveletet hajtotta végre. | <Timestamp> |
| **Korábban nem hajtottak végre hasonló műveletet**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | A felhasználó nem hajtott végre műveletet ugyanabban az erőforrás-szolgáltatóban. | Igaz, hamis |
| **Forrás IP-címe**<br>*(SourceIPLocation)* | *N/A* | Az ország a művelet forrás IP-címétől lett feloldva. | [Surrey, Anglia] |
| **Nem gyakori nagy mennyiségű művelet**<br>*(UncommonHighVolumeOfOperations)* | 7 | Egy felhasználó hasonló műveleteket hajtott végre ugyanazon a szolgáltatón belül | Igaz, hamis |
| **Szokatlan számú Azure AD feltételes hozzáférési hiba**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | A feltételes hozzáférés miatt szokatlan számú felhasználó nem tudott hitelesíteni | Igaz, hamis |
| **Szokatlan számú eszköz lett hozzáadva**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Egy felhasználó szokatlan számú eszközt adott hozzá. | Igaz, hamis |
| **Szokatlan számú eszköz törölve**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | A felhasználó szokatlan számú eszközt törölt. | Igaz, hamis |
| **A csoportba felvett felhasználók szokatlan száma**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | A felhasználók szokatlan számú felhasználót adtak hozzá egy csoporthoz. | Igaz, hamis |
|
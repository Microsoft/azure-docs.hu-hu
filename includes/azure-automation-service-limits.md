---
title: fájl belefoglalása
description: fájl belefoglalása
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 02/08/2021
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 3ee44509997a16fc7f06fd5a24e473d7011bde40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99974292"
---
#### <a name="process-automation"></a>Folyamatautomatizálás

| Erőforrás | Korlát |Jegyzetek|
| --- | --- |---|
| Azure Automation fiókban 30 másodpercenként elküldhető új feladatok maximális száma (nem ütemezett feladatok esetében) |100 |Ha eléri ezt a korlátot, a feladat létrehozására vonatkozó további kérések sikertelenek lesznek. Az ügyfél hibaüzenetet kap.|
| Az egyidejű futó feladatok maximális száma egy Automation-fiókon (nem ütemezett feladatok). |200 |Ha eléri ezt a korlátot, a feladat létrehozására vonatkozó további kérések sikertelenek lesznek. Az ügyfél hibaüzenetet kap.|
| A feladatok metaadatainak maximális tárolási mérete egy 30 napos időszakra | 10 GB (körülbelül 4 000 000 feladat)|Ha eléri ezt a korlátot, a feladat létrehozására vonatkozó további kérések sikertelenek lesznek. |
| Maximális feladatokhoz tartozó adatfolyam-korlát|1 MiB|Egyetlen Stream nem lehet nagyobb, mint 1 MiB.|
| Az Automation-fiókokban 30 másodpercenként importálható modulok maximális száma |5 ||
| Modul maximális mérete |100 MB ||
| Csomópont-konfigurációs fájl maximális mérete | 1 MB | Az állapot-konfigurációra vonatkozik |
| Feladatok futási ideje, ingyenes szintű |500 perc/előfizetés naptári havonta ||
| Maximálisan engedélyezett lemezterület a homokozóban<sup>1</sup> |1 GB |Csak az Azure-beli munkapéldányokra vonatkozik.|
| A sandbox<sup>1</sup> számára megadott maximális memória mennyisége |400 MB |Csak az Azure-beli munkapéldányokra vonatkozik.|
| A másodpercenként engedélyezett hálózati szoftvercsatornák maximális száma<sup>1</sup> |1,000 |Csak az Azure-beli munkapéldányokra vonatkozik.|
| Engedélyezett maximális futtatókörnyezet (runbook<sup>1)</sup> |3 óra |Csak az Azure-beli munkapéldányokra vonatkozik.|
| Az előfizetéshez tartozó Automation-fiókok maximális száma |Korlátlan ||
| Hibrid feldolgozói csoportok maximális száma Automation-fiókban|4,000||
|Egyetlen hibrid Runbook-feldolgozón futtatható egyidejű feladatok maximális száma|50 ||
| Runbook-feladatok maximális mérete   | 512 kilobájt||
| Maximális runbook paraméterek   | 50|Ha eléri a 50-paraméter korlátot, egy JSON-vagy XML-karakterláncot adhat át egy paraméternek, és elemezheti azt a runbook.|
| Webhook maximális hasznos adatának mérete |  512 kilobájt|
| A feladattal megőrzött napok maximális száma|30 nap|
| Maximális PowerShell-munkafolyamat állapotának mérete |5 MB| A PowerShell-munkafolyamat runbookok vonatkozik a munkafolyamat ellenőrzőpontja során.|

<sup>1</sup> A sandbox egy megosztott környezet, amelyet több feladat is használhat. Azokat a feladatokat, amelyek ugyanazt a Sandboxot használják, a sandbox erőforrás-korlátai kötik.

#### <a name="change-tracking-and-inventory"></a>Change Tracking és Inventory

A következő táblázat a nyomon követett elemek korlátozását mutatja gépenként a változások nyomon követéséhez.

| **Erőforrás** | **Korlátot**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Regisztrációs adatbázis|250||
|Windows-szoftver|250|Nem tartalmaz szoftverfrissítéseket.|
|Linux-csomagok|1 250||
|Szolgáltatások|250||
|Démon|250||

#### <a name="update-management"></a>Frissítéskezelés

A következő táblázat a Update Management korlátozásait mutatja be.

| **Erőforrás** | **Korlátot**| **Megjegyzések** |
|---|---|---|
|Gépek száma frissítési központi telepítéssel|1000||
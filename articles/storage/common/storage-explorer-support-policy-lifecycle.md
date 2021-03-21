---
title: Azure Storage Explorer támogatási életciklus | Microsoft Docs
description: A Azure Storage Explorer támogatási szabályzatának és életciklusának áttekintése
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: dc89631208c6a72c4b48d49260f584149d6c4260
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97679935"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Azure Storage Explorer támogatási életciklus és szabályzat

Ez a dokumentum a támogatási életciklusra és a Azure Storage Explorer szabályzatára vonatkozik.

## <a name="update-schedule-and-process"></a>Az ütemterv és a folyamat frissítése

A Azure Storage Explorer évente négy-hat alkalommal jelent meg. A Microsoft a kritikus fontosságú problémák megoldására is kihagyhatja ezt az ütemtervet.

Storage Explorer óránként ellenőrzi a frissítéseket, és letölti őket, amikor elérhetők. A felhasználó elfogadására van szükség, azonban a telepítési folyamat elindításához. Ha a felhasználók egy másik időpontban választanak a frissítésre, manuálisan is megtekinthetik a frissítést. Windows és Linux rendszeren a felhasználók a **Súgó** menü **frissítések keresése** pontjára kattintva érhetik el a frissítéseket. MacOS rendszeren a **frissítések** keresése az **alkalmazás menüben** található. Előfordulhat, hogy a felhasználók közvetlenül a [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) letöltési oldalon tölthetik le és telepítik a legújabb kiadást.

Javasoljuk, hogy mindig a Storage Explorer legújabb verzióit használják. Ha egy probléma megakadályozza a Storage Explorer legújabb verziójának frissítését, nyisson meg egy problémát a [githubon](https://github.com/microsoft/AzureStorageExplorer).

## <a name="support-lifecycle"></a>Támogatási életciklus

Storage Explorer a [modern életciklus-szabályzat](https://support.microsoft.com/help/30881/modern-lifecycle-policy)szabályozza. Várható, hogy a felhasználók naprakészen tartják Storage Explorer telepítését. Naprakészen tarthatja, hogy a felhasználók rendelkeznek a legújabb funkciókkal, a teljesítmény javításával, a biztonsággal és a szolgáltatás megbízhatóságával.

A 1.14.1 verziótól kezdődően a 12 hónaposnál nagyobb Storage Explorer kiadásokat a rendszer nem támogatja. A 1.14.1 előtti összes kiadás a 2021. július 14-én kezdődő támogatásnak minősül. A nem támogatott verziók már nem garantáltak, hogy teljes mértékben a tervezett és várt módon működjenek. Az összes kiadás listáját, a kiadási dátumát és a támogatási dátum végét lásd: [kiadások](#releases).

A 1.13.0 verziótól kezdődően az alkalmazáson belüli riasztások akkor jelennek meg, ha a verzió körülbelül egy hónappal távolabb van a támogatástól. A riasztás arra biztatja a felhasználókat, hogy frissítsenek a Storage Explorer legújabb verziójára. Ha egy verzió nem támogatott, az alkalmazáson belüli riasztás minden indításkor megjelenik.

## <a name="releases"></a>Kiadások

Ez a táblázat a Azure Storage Explorer minden kiadásának kiadási dátumát és a támogatási dátum végét ismerteti.

| Storage Explorer verziója  | Kiadási dátum       | A támogatási dátum vége |
|:-------------------------:|:------------------:|:-------------------:|
| v 1.17.0                   | December 15., 2020  | December 15., 2021   |
| v 1.16.0                   | November 10., 2020  | November 10., 2021   |
| v 1.15.1                   | Szeptember 2., 2020  | Szeptember 2., 2021   |
| v 1.15.0                   | Augusztus 27., 2020    | Augusztus 27., 2021     |
| v 1.14.2                   | Július 16., 2020      | Július 16., 2021       |
| v 1.14.1                   | Július 14., 2020      | Július 14., 2021       |
| v 1.14.0                   | Június 24., 2020      | Július 14., 2021       |
| v 1.13.1                   | 2020. május 18.       | Július 14., 2021       |
| v 1.13.0                   | 2020. május 1.        | Július 14., 2021       |
| v 1.12.0                   | Január 16., 2020   | Július 14., 2021       |
| v 1.11.2                   | December 17., 2019  | Július 14., 2021       |
| v 1.11.1                   | November 20., 2019  | Július 14., 2021       |
| v 1.11.0                   | 2019. november 4.   | Július 14., 2021       |
| v 1.10.1                   | Szeptember 19., 2019 | Július 14., 2021       |
| v 1.10.0                   | Szeptember 12., 2019 | Július 14., 2021       |
| v 1.9.0                    | 2019. július 1.       | Július 14., 2021       |
| v 1.8.1                    | Május 10., 2019       | Július 14., 2021       |
| v 1.8.0                    | 2019. május 2.        | Július 14., 2021       |
| v 1.7.0                    | 2019. március 5.      | Július 14., 2021       |
| v 1.6.2                    | 2019. január 8.    | Július 14., 2021       |
| v 1.6.1                    | December 18., 2018  | Július 14., 2021       |
| v 1.6.0                    | 2018. december 4.   | Július 14., 2021       |
| v 1.5.0                    | 2018. október 29.   | Július 14., 2021       |
| v 1.4.4                    | Október 15., 2018   | Július 14., 2021       |
| v 1.4.2                    | 24, 2018. szeptember | Július 14., 2021       |
| v 1.4.1                    | 2018. augusztus 28.    | Július 14., 2021       |
| v 1.3.1                    | 2018. július 11.      | Július 14., 2021       |
| v 1.2.0                    | 2018. június 12.      | Július 14., 2021       |
| v 1.1.0                    | 2018. május 9.        | Július 14., 2021       |
| v 1.0.0 (és régebbi)        | Április 16., 2018     | Július 14., 2021       |

---
title: Fenyegetések – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: A Microsoft Threat Modeling Tool veszélyforrások kategóriájának lapja, amely az összes elérhető fenyegetés kategóriáját tartalmazza.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727831"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Fenyegetések Microsoft Threat Modeling Tool

A Threat Modeling Tool a Microsoft biztonsági fejlesztési életciklus (SDL) alapvető eleme. Lehetővé teszi a szoftverfejlesztők számára, hogy korán azonosítsák és csökkentsék a potenciális biztonsági problémákat, amikor viszonylag könnyen és költséghatékonyan oldják meg a megoldást. Ennek eredményeképpen jelentős mértékben csökkenti a teljes fejlesztési költségeket. Azt is megtervezték, hogy az eszközt nem biztonsági szakértők is megalakították, így a fenyegetések modellezése minden fejlesztő számára egyszerűbb, mivel egyértelmű útmutatást nyújt a veszélyforrások létrehozásához és elemzéséhez.

> Látogasson el a **[Threat Modeling toolra](threat-modeling-tool.md)** , és kezdje el még ma!

A Threat Modeling Tool segít bizonyos kérdések megválaszolásában, például az alábbi lehetőségek közül:

* Hogyan változtathatja meg a támadó a hitelesítési adatvédelmet?
* Mi a hatása, ha egy támadó elolvashatja a felhasználói profilra vonatkozó adattartalmakat?
* Mi történik, ha a rendszer megtagadja a hozzáférést a felhasználói profil adatbázisához?

## <a name="stride-model"></a>LÉPÉSHOSSZ modell

A Microsoft a LÉPÉSHOSSZ modellt használja, amely különböző típusú fenyegetéseket kategorizál, és leegyszerűsíti az általános biztonsági beszélgetéseket.

| Kategória | Leírás |
| -------- | ----------- |
| **Identitáshamisítás** | A szolgáltatás illegálisan férhet hozzá, majd egy másik felhasználó hitelesítési információit, például a felhasználónevet és a jelszót használja. |
| **Illetéktelen adatmódosítás** | Az adatkezelés rosszindulatú módosítását foglalja magában. Ilyenek például az állandó adatmódosítások, mint az adatbázisokban tárolt jogosulatlan módosítások, valamint az adatváltozások, amelyek a két számítógép között egy nyitott hálózaton keresztül áramlanak, például az interneten |
| **Letagadhatóság** | Olyan felhasználókhoz társítva, akik megtagadják a műveletet más felek anélkül, hogy bármilyen módon bizonyítani tudják – például egy felhasználó szabálytalan műveletet hajt végre egy olyan rendszeren, amely nem rendelkezik a tiltott műveletek nyomon követésének lehetőségével. A nem megtagadási lehetőség arra utal, hogy a rendszer képes a megtagadási fenyegetések elhárítására. Előfordulhat például, hogy egy elem beszerzését végző felhasználónak be kell jelentkeznie az elemre a beérkezés után. A szállító ezt követően az aláírt nyugtát olyan bizonyítékként használhatja, amely szerint a felhasználó megkapta a csomagot |
| **Információfelfedés** | Olyan személyeknek szóló információk bevonása, akik nem jogosultak arra, hogy hozzáférjenek a szolgáltatáshoz, például a felhasználók számára, hogy nem férhetnek hozzá a fájlhoz, vagy hogy a behatoló képes legyen beolvasni az adatokat a két számítógép között. |
| **Szolgáltatásmegtagadás** | A szolgáltatásmegtagadási (DoS) támadások megtagadják a szolgáltatást az érvényes felhasználóknak – például úgy, hogy egy webkiszolgáló átmenetileg elérhetetlenné válik vagy használhatatlan. A rendszerek rendelkezésre állásának és megbízhatóságának javítása érdekében csak bizonyos típusú DoS-fenyegetések ellen kell védekezni |
| **Jogosultsági szint emelése** | A nem rendszerjogosultságú felhasználók emelt szintű hozzáférést nyernek, így elegendő hozzáférésük van a kompromisszumhoz, vagy megsemmisítik a teljes rendszerét. A jogosultsági fenyegetések magasabb szintre állítása olyan helyzetekben, amikor egy támadó ténylegesen behatolt a rendszervédelembe, és maga a megbízható rendszer részévé válik, a veszélyes helyzet valóban |

## <a name="next-steps"></a>Következő lépések

Folytassa **[Threat Modeling Tool enyhítésével](threat-modeling-tool-mitigations.md)** , hogy megismerje a fenyegetések enyhítésének különböző módszereit az Azure-ban.
---
title: Az eszköz frissítésének ismertetése IoT Hub importáláshoz | Microsoft Docs
description: Alapfogalmak az új frissítés importálásához a IoT Hub eszköz frissítése számára.
author: andbrown
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: c6c6707a74007898c3cd73250709f4df3fabb37d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663341"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Frissítések importálása a IoT Hub eszköz frissítéseibe
Ahhoz, hogy frissíteni lehessen a IoT Hub eszköz frissítését az eszközökön, először _importálnia_ kell ezt a frissítést az eszköz frissítési szolgáltatásában. Az alábbiakban áttekintheti azokat a fontos fogalmakat, amelyekkel megismerheti a frissítések importálásának idejét.

## <a name="import-manifest"></a>Jegyzék importálása

Az importálási jegyzékfájl egy olyan JSON-fájl, amely fontos információkat határoz meg az importált frissítésről. Az importálási folyamat részeként az importálási jegyzékfájlt és a hozzá tartozó frissítési fájlt (például egy belső vezérlőprogram-frissítési csomagot) is el kell küldenie. Az importálási jegyzékfájlban definiált metaadatok a frissítés betöltésére szolgálnak. A rendszer a központi telepítés időpontjában is használja a metaadatokat, például annak ellenőrzéséhez, hogy megfelelően telepítették-e a frissítést.

Az importálási jegyzékfájl több olyan elemet tartalmaz, amelyek fontos eszköz-frissítést jelentenek IoT Hub fogalmakhoz. Az alábbi fogalmakat az alábbiakban ismertetjük.

### <a name="update-identity-update-id"></a>Identitás frissítése (frissítési azonosító)

A frissítés identitása a frissítés egyedi termékazonosító jelöli. Az importált frissítés fontos tulajdonságait határozza meg. A frissítési identitás három részből áll:
* Szolgáltató: Ez a frissítéshez létrehozott vagy közvetlenül felelős entitás. Ez gyakran a vállalat neve lesz.
* Name: a frissítések osztályának azonosítója. Az osztály tetszőlegesen kiválasztható. Ez gyakran az eszköz vagy a modell neve lesz.
* Verzió: ez egy verziószám, amely megkülönbözteti ezt a frissítést másoktól, akik ugyanazzal a szolgáltatóval és névvel rendelkeznek. Ezt a verziót a IoT Hub szolgáltatáshoz tartozó frissítés használja, és előfordulhat, hogy az eszközön nem egyezik az adott szoftver egyik összetevőjének verziójával. 

### <a name="compatibility"></a>Kompatibilitás

A frissítések központi telepítésének egyszerűsítése érdekében a IoT Hub eszköz frissítése összehasonlítja az importálási jegyzékfájlban definiált frissítés kompatibilitási tulajdonságait, a megfelelő eszköz tulajdonságaival. Csak a megfelelő tulajdonságokkal rendelkező frissítések lesznek visszaadva és elérhetők a telepítéshez.

### <a name="installedcriteria"></a>InstalledCriteria

A InstalledCriteria az eszköz frissítési ügynöke használja annak megállapítására, hogy a frissítés telepítése sikeres volt-e.


## <a name="next-steps"></a>Következő lépések

Ha elkészült, próbálja ki az [importálási How-To útmutatót](./import-update.md), amely végigvezeti az importálási folyamat lépésein.



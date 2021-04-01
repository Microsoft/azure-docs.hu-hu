---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "83665948"
---
Az együttműködéshez a következő szerepköröket kell megadnia:

|Szerepkör|Funkciók|API-hozzáférés|API-engedélyek|
|--|--|--|--|
|Tulajdonos|Mind|Hitelesítési kulcs|Mind|
|Közreműködő|Csak az új tagok szerepkörökhöz való hozzáadásának lehetősége|Hitelesítési kulcs|Csak az új tagok szerepkörökhöz való hozzáadásának lehetősége|
|QnA Maker olvasás<br>olvasni|Exportálás/Letöltés<br>Tesztelés|Tulajdonosi jogkivonat|1. a KB API letöltése<br>2. a felhasználói API Tudásbázis listázása<br>3. a Tudásbázis részleteinek beolvasása<br>4. az átalakítások letöltése<br>Válasz készítése |
|QnA Maker szerkesztő<br>(olvasás/írás)|Exportálás/Letöltés<br>Tesztelés<br>Frissítés KB<br>KB exportálása<br>Importálás KB-ban<br>KB cseréje<br>Tudásbázis létrehozása|Tulajdonosi jogkivonat|1. TUDÁSBÁZIS-API létrehozása<br>2. a KB API frissítése<br>3. a KB API cseréje<br>4. változtatások cseréje<br>5. "Train API" [az új Service Model V5-ben]|
|Kognitív szolgáltatás felhasználója<br>(olvasási/írási/közzétételi)|Mind|Tulajdonosi jogkivonat|Mind|
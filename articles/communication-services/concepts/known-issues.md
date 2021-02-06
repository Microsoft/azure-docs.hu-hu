---
title: Azure kommunikációs szolgáltatások – ismert problémák
description: Ismerje meg az Azure kommunikációs szolgáltatások ismert problémáit
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628144"
---
# <a name="known-issues-azure-communication-services"></a>Ismert problémák: Azure kommunikációs szolgáltatások

Ez a cikk az Azure kommunikációs szolgáltatásokkal kapcsolatos ismert problémákról nyújt információkat.

## <a name="video-streaming-quality-on-chromeandroid"></a>Video streaming-minőség a Chrome/Android rendszeren 

A video streaming teljesítménye csökkenhet a Chrome Androidon.

### <a name="possible-causes"></a>Lehetséges okok
A távoli adatfolyamok minősége attól függ, hogy az adott stream elindításához használt ügyféloldali megjelenítő felbontása milyen. Távoli adatfolyamra való feliratkozáskor a fogadó a saját felbontását fogja meghatározni a küldő ügyféloldali megjelenítési méretei alapján.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>A Bluetooth Headset mikrofonja nem észlelhető

A Bluetooth-fejhallgató kommunikációs szolgáltatásokhoz való csatlakoztatásával kapcsolatos problémák merülhetnek fel.

### <a name="possible-causes"></a>Lehetséges okok
Nincs lehetőség a Bluetooth-mikrofon kiválasztására iOS rendszeren.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>A video-eszközök többszöri váltásakor előfordulhat, hogy a video streaming átmenetileg leáll

Előfordulhat, hogy a video-eszközök közötti váltás miatt a videó adatfolyama szüneteltethető, miközben a rendszer a kiválasztott eszközről szerzi be a streamet.

### <a name="possible-causes"></a>Lehetséges okok
A médiaadatfolyam-továbbítás és a mobileszközök közötti váltás számítási igényű. A gyakori váltás a teljesítmény romlását okozhatja. A fejlesztőknek javasoljuk, hogy egy másik eszköz elindítása előtt állítsanak le egy adatfolyamot.

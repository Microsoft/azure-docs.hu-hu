---
title: Azure kommunikációs szolgáltatások – gyakori kérdések/ismert problémák
description: További információ az Azure kommunikációs szolgáltatásokról
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e58335f1e266af651eb5867ca98e9ec979803b94
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655887"
---
# <a name="faq--known-issues"></a>GYAKORI kérdések/ismert problémák
Ez a cikk az Azure kommunikációs szolgáltatásokkal kapcsolatos ismert problémákról és gyakori kérdésekről nyújt információt.

## <a name="faq"></a>GYIK

### <a name="why-is-the-quality-of-my-video-degraded"></a>Miért csökkent a videó minősége?

A videó streamek minőségét a stream elindításához használt ügyféloldali megjelenítő mérete határozza meg. Távoli adatfolyamra való feliratkozáskor a fogadó a saját felbontását fogja meghatározni a küldő ügyféloldali megjelenítési méretei alapján.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Miért nem lehetséges a MIC/Speaker-eszközök enumerálása/kiválasztása a Safarion?

Az alkalmazások nem tudják enumerálni/kiválasztani a MIC/Speaker eszközöket (például Bluetooth) a Safari iOS/iPad szolgáltatásban. Ez az operációs rendszer korlátozása – mindig csak egy eszköz van.

MacOS-hez készült Safari esetében – az alkalmazás nem tudja enumerálni/kiválasztani a beszélőt a kommunikációs szolgáltatások Eszközkezelő – ezeket az operációs rendszer használatával kell kiválasztani. Ha MacOS rendszeren használja a Chrome-t, az alkalmazás a kommunikációs szolgáltatások Eszközkezelő segítségével enumerálhatja vagy kiválaszthatja az eszközöket.

## <a name="known-issues"></a>Ismert problémák

Ez a szakasz az Azure kommunikációs szolgáltatásokkal kapcsolatos ismert problémákról nyújt információkat.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>A video-eszközök többszöri váltásakor előfordulhat, hogy a video streaming átmenetileg leáll

Előfordulhat, hogy a video-eszközök közötti váltás miatt a videó adatfolyama szüneteltethető, miközben a rendszer a kiválasztott eszközről szerzi be a streamet.

#### <a name="possible-causes"></a>Lehetséges okok
A médiaadatfolyam-továbbítás és a mobileszközök közötti váltás számítási igényű. A gyakori váltás a teljesítmény romlását okozhatja. A fejlesztőknek javasoljuk, hogy egy másik eszköz elindítása előtt állítsanak le egy adatfolyamot.

---
title: Azure kommunikációs szolgáltatások – gyakori kérdések/ismert problémák
description: További információ az Azure kommunikációs szolgáltatásokról
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 2c6ac34d8daf00578cb1d03833a28eb8535708b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493645"
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

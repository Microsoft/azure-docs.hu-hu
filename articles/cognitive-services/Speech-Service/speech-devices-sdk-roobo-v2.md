---
title: Beszédfelismerési eszközök SDK Roobo intelligens hang dev Kit v2 – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Előfeltételek és utasítások a Speech Devices SDK első lépéseihez, Roobo Smart audio dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "80371583"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Eszköz: Roobo Smart audio dev Kit v2

Ez a cikk az Roobo Smart audio dev Kit2 eszközre vonatkozó információkat tartalmaz.

## <a name="set-up-the-development-kit"></a>A fejlesztői csomag beállítása

1. A fejlesztői készlet két Micro USB-összekötővel rendelkezik. A bal oldali összekötő a fejlesztői készlet bekapcsolására szolgál, és az alábbi képen Kiemelt teljesítményként jelenik meg. Az egyik az, hogy irányítsa, és a képen hibakeresést jelöl. 
    ![A fejlesztői csomag csatlakoztatása](media/speech-devices-sdk/roobo-v2-connections.png)
1. A Power port SZÁMÍTÓGÉPekhez vagy hálózati adapterhez való csatlakoztatásához használjon egy Micro USB-kábelt a fejlesztői készlethez. A felső táblán egy zöld energiaellátási kijelző jelenik meg.
1. A fejlesztői csomag vezérléséhez csatlakoztassa a hibakeresési portot egy számítógéphez egy második Micro USB-kábel használatával. A megbízható kommunikáció biztosítása érdekében elengedhetetlen a kiváló minőségű kábelek használata.
1. A fejlesztői csomag körkörösen való elosztása a fent látható felső korláttal rendelkező mikrofonokkal


## <a name="development-information"></a>Fejlesztési információk

További fejlesztési információk: [Roobo fejlesztői útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Hangrögzítés/lejátszás

A DDK2-alapú hangműveletek a következő módokon végezhetők el:
* Használjon ALSA nyílt forráskódú kódtárakat és alkalmazásaikat.
* Appmainprog felület használata az alkalmazások fejlesztéséhez. A DDK2 hanggal kapcsolatos szoftveres keretrendszer szabványos ALSA-keretrendszert használ, és libasound is használhat. Így közvetlenül a szoftvereket fejlesztheti. Így az ALSA feljegyzést és aplay közvetlenül rögzíthet és hanglejátszást készíthet.

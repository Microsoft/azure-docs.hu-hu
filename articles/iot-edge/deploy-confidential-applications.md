---
title: Bizalmas alkalmazások Azure IoT Edge modulként
description: A nyílt enklávé SDK-val és API-val bizalmas alkalmazások írhatók, és a bizalmas számítástechnika IoT Edge moduljaiként telepíthetők.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: f9dff1b4c6b2489edd3cd685e3546618961d9757
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487717"
---
# <a name="confidential-computing-at-the-edge"></a>Bizalmas számítástechnika az Edge-ben

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge támogatja a biztonságos enklávékban futó bizalmas alkalmazásokat az eszközön. A titkosítás biztonságos adatátvitelt biztosít az adatok átvitele közben vagy a nyugalmi állapotban, de az enklávék használat közben biztonságot biztosítanak az adatok és a munkaterhelések számára. IoT Edge támogatja a nyílt enklávét a bizalmas alkalmazások fejlesztésére szolgáló standardként.

A biztonság mindig is fontos szerepet játszik a eszközök internetes hálózata (IoT) számára, mivel gyakran a világ különböző részein a IoT-eszközök nem biztonságosak a privát létesítményben. Ez a kitettség olyan eszközöket tesz lehetővé, amelyek veszélyeztetik a hamisítást és a hamisítást, mert fizikailag elérhetők a hibás szereplők számára. IoT Edge eszközök még nagyobb szükségük van a megbízhatóságra és az integritásra, mivel lehetővé teszik a bizalmas számítási feladatok futtatását a peremhálózat szélén. A közös érzékelőkkel és működtetővel ellentétben ezek az intelligens peremhálózati eszközök olyan bizalmas munkaterheléseket is feltehetnek, amelyek korábban csak a védett felhőben vagy a helyszíni környezetekben futnak.

A [IoT Edge Security Manager](iot-edge-security-manager.md) a bizalmas számítástechnikai kérdés egyik részletét tárgyalja. A Security Manager egy hardveres biztonsági modult (HSM) használ a IoT Edge eszközön lévő identitási munkaterhelések és a folyamatban lévő folyamatok védelmére.

A bizalmas számítástechnika egy másik aspektusa védi a használatban lévő adatok védelmét. A *megbízható végrehajtási környezet* (Tee) egy biztonságos, elszigetelt környezet a processzoron, és más néven *enklávé*. A *bizalmas alkalmazások* olyan alkalmazások, amelyek enklávéban futnak. Az enklávék jellegéből adódóan a bizalmas alkalmazások a főprocesszoron vagy a TEE-on futó más alkalmazásokkal is védve vannak.

## <a name="confidential-applications-on-iot-edge"></a>Bizalmas alkalmazások IoT Edge

A bizalmas alkalmazások továbbítása és nyugalmi állapotban van titkosítva, és csak megbízható végrehajtási környezetben való futtatásra van visszafejtve. Ez a standard a IoT Edge modulként üzembe helyezett bizalmas alkalmazásokhoz is érvényes.

A fejlesztő létrehozza a bizalmas alkalmazást, és becsomagolja IoT Edge modulként. Az alkalmazás titkosítva van, mielőtt leküldi a tároló-beállításjegyzékbe. Az alkalmazás a IoT Edge telepítési folyamat során titkosítva marad, amíg a modul el nem indul a IoT Edge eszközön. Ha a bizalmas alkalmazás az eszköz PÓLÓján belül van, a rendszer visszafejti, és elindíthatja a végrehajtást.

![Diagram – a bizalmas alkalmazások a biztonságos enklávéba való üzembe helyezésig titkosítva vannak IoT Edge modulokon belül.](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

A IoT Edgeban található bizalmas alkalmazások az [Azure bizalmas számítástechnika](../confidential-computing/overview.md)logikai bővítménye. A biztonságos enklávékban futó munkaterhelések a felhőben is üzembe helyezhetők, hogy a biztonságos enklávékban fussanak a szélén.

## <a name="open-enclave"></a>Nyitott enklávé

Az [Open ENKLÁVÉ SDK](https://openenclave.io/sdk/) egy nyílt forráskódú projekt, amely segítségével a fejlesztők bizalmas alkalmazásokat hozhatnak létre több platform és környezet számára. Az Open enklávé SDK egy eszköz megbízható végrehajtási környezetén belül működik, míg a nyitott enklávé API a TEE és a nem TEE feldolgozási környezet közötti illesztőfelületként működik.

A nyitott enklávé több hardveres platformot is támogat. IoT Edge az enklávék támogatásához jelenleg a nyílt hordozható TEE operációs rendszer (OP-TEE OS) szükséges. További információ: [Open ENKLÁVÉ SDK for op-Tee os](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

A nyílt enklávé-tárház olyan mintákat is tartalmaz, amelyek segítségével a fejlesztők megkezdhetik az első lépéseket. További információért válasszon a bevezető cikkek közül:

* [Open enklávé SDK-minták készítése Linux rendszeren](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Open enklávé SDK-minták készítése Windows rendszeren](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardver

Jelenleg a [Scalys által biztosított TrustBox](https://scalys.com/trustbox-industrial/) az egyetlen eszköz, amely a gyártói szolgáltatási szerződésekkel támogatja a bizalmas alkalmazások IoT Edge modulként való üzembe helyezését. A TrustBox a TrustBox Edge-re épül, és a TrustBox EdgeXL-eszközökön egyaránt előre be van töltve az Open enklávé SDK-val és Azure IoT Edge.

További információkért lásd: [az Open enklávé első lépései a Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Fejlesztés és üzembe helyezés

Ha készen áll a bizalmas alkalmazás fejlesztésére és üzembe helyezésére, akkor a Visual Studio Code-hoz készült [Microsoft Open enklávé](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) bővítmény segíthet. A TrustBox modulok fejlesztéséhez használhatja a Linux vagy a Windows rendszerű fejlesztői gépet is.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezdheti el a bizalmas alkalmazások fejlesztését IoT Edge modulként a [Visual Studio Code-hoz készült Open enklávé bővítménnyel](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)

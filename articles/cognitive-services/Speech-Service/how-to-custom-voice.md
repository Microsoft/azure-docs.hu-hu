---
title: A szintézis javítása egyéni hangvezérelt szolgáltatással
titleSuffix: Azure Cognitive Services
description: Az egyéni hang olyan online eszközök összessége, amelyek lehetővé teszik a márka felismerhető, egyfajta hangjának létrehozását. A kezdéshez mindössze néhány hangfájlt és a hozzá tartozó átírásokat kell használnia. Az alábbi hivatkozásokat követve hozzon létre egy egyéni beszéd – szöveg élményt.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 4e487a3eab70e7d561468a8fac2103e177d2abc5
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524975"
---
# <a name="get-started-with-custom-voice"></a>Bevezetés a Custom Voice szolgáltatásba

Az [egyéni hang](https://aka.ms/customvoice) olyan online eszközök összessége, amelyek lehetővé teszik a márka felismerhető, egyfajta hangjának létrehozását. A kezdéshez mindössze néhány hangfájlt és a hozzá tartozó átírásokat kell használnia. Az alábbi hivatkozásokat követve megkezdheti az egyéni szöveg-beszéd élmény létrehozását.

## <a name="whats-in-custom-voice"></a>Mi az egyéni hang?

Az egyéni hang megkezdése előtt szüksége lesz egy Azure-fiókra és egy Speech Service-előfizetésre. Miután létrehozott egy fiókot, előkészítheti az adatait, betaníthatja és tesztelheti a modelleket, kiértékelheti a hangminőséget, és végül üzembe helyezheti az egyéni hangmodellt.

Az alábbi ábra az egyéni hangmodellek [Egyéni hangportál](https://aka.ms/customvoice)használatával történő létrehozásának lépéseit mutatja be. További információért tekintse meg a hivatkozásokat.

![Egyéni hangarchitektúra diagram](media/custom-voice/custom-voice-diagram.png)

1. [Előfizetés és hozzon létre egy projektet](#set-up-your-azure-account) – hozzon létre egy Azure-fiókot, és hozzon létre egy Speech Service-előfizetést. Ez az egyesített előfizetés hozzáférést biztosít a beszéd-szöveg, a szöveg és a beszéd fordításához, valamint az egyéni hangportálhoz. Ezután használja a Speech Service-előfizetését, és hozza létre az első egyéni hangprojektjét.

2. [Adatok feltöltése](how-to-custom-voice-create-voice.md#upload-your-datasets) – adatok feltöltése (hang és szöveg) az egyéni hangportál vagy az egyéni hangalapú API használatával. A portálon megvizsgálhatja és kiértékelheti a kiejtési pontszámokat és a jel-zaj arányt. További információ: az [adatok előkészítése egyéni hangra](how-to-custom-voice-prepare-data.md).

3. [A modell betanítása](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – egyéni szöveg-beszéd hangmodell létrehozásához használhatja adatait. A modelleket különböző nyelveken is betaníthatja. A képzés után tesztelje a modellt, és ha elégedett az eredménnyel, telepítheti a modellt.

4. [A modell üzembe helyezése](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – hozzon létre egy egyéni végpontot a szöveg-beszéd hangmodellhez, és használja a beszédfelismeréshez a termékeiben, eszközein és alkalmazásaiban.

## <a name="custom-neural-voices"></a>Egyéni neurális hangok

Az egyéni hang jelenleg a standard és a neurális szintet is támogatja. Az egyéni neurális hang lehetővé teszi, hogy a felhasználók magasabb színvonalú hangmodelleket hozzanak létre, miközben kevesebb adatra van szükségük, és olyan mértékeket biztosít, amelyek segítséget nyújtanak az AI felelősségteljes Javasoljuk, hogy az egyéni neurális hang használatával reálisabb hangokat fejlesszen a természetesebb társalgási felületek számára, és lehetővé tegye, hogy az ügyfelek és a végfelhasználók a legújabb szöveg-beszéd technológia előnyeit a felelős módon használják. [További információ az egyéni neurális hangokról](https://aka.ms/CNV-Transparency-Note). 

> [!NOTE]
> A Microsoft elkötelezettsége a felelős AI megtervezésének részeként korlátozott az egyéni neurális hang használata. Csak az alkalmazások felülvizsgálatát követően férhet hozzá a technológiához, és Ön kötelezte magát arra, hogy a felelős AI-alapelvekkel való összhangban használja azt. Tekintse meg [a szabályzatot a hozzáférés korlátozása](https://aka.ms/gating-overview) és [itt érvényes](https://aka.ms/customneural). Az egyéni hang standard és neurális verziója által támogatott [nyelvek](language-support.md#customization) és [régiók](regions.md#custom-voices) eltérőek. A Kezdés előtt tekintse meg a részleteket.  

## <a name="set-up-your-azure-account"></a>Azure-fiók beállítása

Egy beszédfelismerési szolgáltatás előfizetése szükséges ahhoz, hogy a Custom Speech portálon egyéni modellt hozzon létre. Kövesse ezeket az utasításokat egy Speech Service-előfizetés létrehozásához az Azure-ban. Ha nem rendelkezik Azure-fiókkal, regisztrálhat egy újat.  

Miután létrehozott egy Azure-fiókot és egy Speech Service-előfizetést, be kell jelentkeznie az egyéni hangportálra, és hozzá kell kötnie az előfizetését.

1. Szerezze be a beszédfelismerési szolgáltatás előfizetési kulcsát a Azure Portal.
2. Jelentkezzen be az [egyéni hang portálra](https://aka.ms/custom-voice).
3. Válassza ki az előfizetését, és hozzon létre egy beszédfelismerési projektet.
4. Ha egy másik beszédfelismerési előfizetésre szeretne váltani, használja a felső navigációs ablakában található fogaskerék ikont.

> [!NOTE]
> A szolgáltatás használata előtt létre kell hoznia egy F0 vagy egy S0 Speech Service-kulcsot az Azure-ban. Az egyéni neurális hang csak a S0 szintet támogatja. 

## <a name="how-to-create-a-project"></a>Projekt létrehozása

A tartalmak, például az adattípusok, modellek, tesztek és végpontok az egyéni hangportálon belüli **projektekben** vannak rendszerezve. Minden projekt egy adott országra/nyelvre és a létrehozni kívánt hang nemre vonatkozik. Létrehozhat például egy projektet a Call Center csevegési robotok számára, amelyek az angol nyelvet használják a Egyesült Államokban (en-US).

Az első projekt létrehozásához válassza a **szöveg – beszéd/egyéni hang** fület, majd kattintson az **új projekt** elemre. A projekt létrehozásához kövesse a varázsló utasításait. A projekt létrehozása után négy lap jelenik meg: **az adatkezelés**, a **képzés**, a **tesztelés** és az **üzembe helyezés**. Az egyes lapok használatának megismeréséhez használja a [következő lépések](#next-steps) hivatkozásait.

> [!IMPORTANT]
> Az [Egyéni hangportált](https://aka.ms/custom-voice) nemrég frissítették! Ha korábban létrehozott, modelleket, teszteket és közzétett végpontokat hozott létre a CRIS.ai-portálon vagy API-kkal, létre kell hoznia egy új projektet az új portálon a régi entitásokhoz való kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések

- [Egyéni hangalapú adatfeldolgozás előkészítése](how-to-custom-voice-prepare-data.md)
- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Útmutató: hangminták rögzítése](record-custom-voice-samples.md)
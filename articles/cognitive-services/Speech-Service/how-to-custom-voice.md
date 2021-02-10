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
ms.openlocfilehash: eff51c8568ce82c9d8d21bff7a2ba079c291679c
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007295"
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

A tartalmak, például az adattípusok, modellek, tesztek és végpontok az egyéni hangportálon belüli **projektekben** vannak rendszerezve. Minden projekt egy adott országra/nyelvre és a létrehozni kívánt hang nemre vonatkozik. Létrehozhat például egy projektet a Call Center csevegési robotok számára, amelyek az angol nyelvet használják a Egyesült Államok ("en-US").

Az első projekt létrehozásához válassza a **szöveg – beszéd/egyéni hang** fület, majd kattintson az **új projekt** elemre. A projekt létrehozásához kövesse a varázsló utasításait. A projekt létrehozása után négy lap jelenik meg: **az adatkezelés**, a **képzés**, a **tesztelés** és az **üzembe helyezés**. Az egyes lapok használatának megismeréséhez használja a [következő lépések](#next-steps) hivatkozásait.

> [!IMPORTANT]
> Az [Egyéni hangportált](https://aka.ms/custom-voice) nemrég frissítették! Ha korábban létrehozott, modelleket, teszteket és közzétett végpontokat hozott létre a CRIS.ai-portálon vagy API-kkal, létre kell hoznia egy új projektet az új portálon a régi entitásokhoz való kapcsolódáshoz.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Áttelepítés egyéni neurális hangra

Ha a nem neurális (vagy standard) egyéni hangvételt használja, vegye figyelembe, hogy az alábbi lépéseket követve azonnal áttelepíti az egyéni neurális hangra. Az egyéni neurális hangvételre való áttérés segít, hogy reálisabb hangokat fejlesszen még természetesebb felületi felületeknél, és lehetővé tegye, hogy az ügyfelek és a végfelhasználók a legújabb szöveg-beszéd technológia előnyeit a felelős módon hasznosítsák. 

1. Tekintse meg [a szabályzatot a hozzáférés korlátozása](https://aka.ms/gating-overview) és [itt érvényes](https://aka.ms/customneural). Vegye figyelembe, hogy az egyéni neurális hangszolgáltatáshoz való hozzáférésre a Microsoft saját belátása szerint jogosult a jogosultsági feltételek alapján. Az ügyfelek csak az alkalmazásuk felülvizsgálatát követően férhetnek hozzá a technológiához, és elkötelezték magukat, hogy a [felelős AI-alapelvekkel](https://microsoft.com/ai/responsible-ai) és a [viselkedési szabályzattal](https://aka.ms/custom-neural-code-of-conduct)összhangban használják azt. 
2. Az alkalmazás jóváhagyása után megkapja a "neurális" betanítási funkció elérését. Ügyeljen arra, hogy az [Egyéni hangportálra](https://speech.microsoft.com/customvoice) az alkalmazásban megadott Azure-előfizetéssel jelentkezzen be. 
    > [!IMPORTANT]
    > A hangtehetségek elleni védelemhez, valamint a hangmodellek jogosulatlan rögzítéssel vagy a hangtehetségek nyugtázása nélkül történő betanításának megakadályozásához az ügyfélnek fel kell töltenie egy rögzített nyilatkozatot a hangtehetséghez, amely a saját beleegyezik. A rögzítési parancsfájl előkészítésekor ügyeljen rá, hogy tartalmazza ezt a mondatot. "I [állapot az első és az utolsó név] tisztában vagyok azzal, hogy a hangfelvételeket [a vállalat neve] fogja használni a hang szintetikus verziójának létrehozásához és használatához."
    > Ezt a mondatot fel kell tölteni a **hangtehetség** lapra szóbeli beleegyező fájlként. A rendszer arra fogja használni, hogy a betanítási adatkészletek felvételeit ugyanazzal a személlyel végezze el, aki a beleegyezik.
3. Az egyéni neurális hangmodell létrehozása után telepítse a hangmodellt egy új végpontra. Ha új egyéni hangvégpontot szeretne létrehozni a neurális hangmodellel, ugorjon a **szöveg-beszéd > egyéni hang > üzembe helyezés** elemre. Válassza a **modell üzembe helyezése** lehetőséget, és adja meg az egyéni végpont **nevét** és **leírását** . Ezután válassza ki azt az egyéni neurális hangmodellt, amelyet hozzá szeretne rendelni ehhez a végponthoz, és erősítse meg a telepítést.  
4. Frissítse a kódot az alkalmazásaiban, ha új végpontot hozott létre egy új modellel. 

## <a name="next-steps"></a>Következő lépések

- [Egyéni hangalapú adatfeldolgozás előkészítése](how-to-custom-voice-prepare-data.md)
- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Útmutató: hangminták rögzítése](record-custom-voice-samples.md)

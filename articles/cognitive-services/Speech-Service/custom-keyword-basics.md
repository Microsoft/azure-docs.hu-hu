---
title: Kulcsszó-útmutató létrehozása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az eszköz mindig egy kulcsszót (vagy kifejezést) figyel. Ha a felhasználó a kulcsszót mondja, az eszköz az összes további hangot elküldi a felhőbe, amíg a felhasználó nem állítja a beszédet. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021949"
---
# <a name="get-started-with-custom-keyword"></a>Bevezetés az egyéni kulcsszavak használatába

Ebben a rövid útmutatóban megismerheti az egyéni kulcsszavakkal végzett munka alapjait a Speech Studio és a Speech SDK használatával. A kulcsszó egy szó vagy rövid kifejezés, amely lehetővé teszi, hogy a termék hangvezérelt legyen. Kulcsszavas modelleket hozhat létre a Speech Studióban, majd exportálhatja az alkalmazásaiban a Speech SDK-val használt modell-fájlt.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések egy beszédfelismerési előfizetést és a Speech SDK-t igénylik. Ha még nem rendelkezik előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free). Az SDK beszerzéséhez tekintse meg a platform [telepítési útmutatóját](quickstarts/setup-platform.md) .

## <a name="create-a-keyword-in-speech-studio"></a>Kulcsszó létrehozása a Speech Studióban

Egyéni kulcsszó használata előtt létre kell hoznia egy kulcsszót a [Speech Studio](https://aka.ms/sdsdk-speechportal) [Egyéni kulcsszavas](https://aka.ms/sdsdk-wakewordportal) oldalának használatával. A kulcsszó megadása után létrehoz egy `.table` fájlt, amelyet a SPEECH SDK-val használhat.

> [!IMPORTANT]
> Az egyéni kulcsszavas modellek és a létrejövő `.table` fájlok **csak** a Speech Studióban hozhatók létre.
> Nem hozhat létre egyéni kulcsszavakat az SDK-ból vagy REST-hívásokkal.

1. Lépjen a [Speech studióba](https://aka.ms/sdsdk-speechportal) , és **Jelentkezzen be** , vagy ha még nem rendelkezik beszédfelismerési előfizetéssel, válassza az [**előfizetés létrehozása**](https://go.microsoft.com/fwlink/?linkid=2086754)lehetőséget.

1. Az [egyéni kulcsszó](https://aka.ms/sdsdk-wakewordportal) lapon hozzon létre egy **új projektet**. 

1. Adjon meg egy **nevet**, egy opcionális **leírást**, és válassza ki a nyelvet. Nyelvenként egy projektre van szüksége, és a támogatás jelenleg a `en-US` nyelvre korlátozódik.

    ![A kulcsszavas projekt leírása](media/custom-keyword/custom-kws-portal-new-project.png)

1. Válassza ki a projektet a listából. 

    ![Válassza ki a kulcsszavas projektet](media/custom-keyword/custom-kws-portal-project-list.png)

1. Új kulcsszó-modell létrehozásához kattintson a **betanítási modell** elemre.

1. Adja meg a modell **nevét** , a választható **leírást**, valamint az Ön által választott **kulcsszót** , majd kattintson a **tovább** gombra. Tekintse meg a hatályos kulcsszó kiválasztására [vonatkozó útmutatást](./custom-keyword-overview.md#choose-an-effective-keyword) .

    ![Adja meg a kulcsszót](media/custom-keyword/custom-kws-portal-new-model.png)

1. A portál a kulcsszóhoz jelölt kiejtéseket hoz létre. Hallgassa meg az egyes jelölteket a lejátszás gombokra kattintva, és távolítsa el az összes helytelen kiejtés melletti ellenőrzéseket. Ha csak a jó kiejtéseket jelölte be, kattintson a **betanítás** gombra a Kulcsszóválasztó modell létrehozásának megkezdéséhez. 

    ![Képernyőkép, amely bemutatja, hol válassza ki a megfelelő pronounciations.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. A modell létrehozása akár harminc percet is igénybe vehet. A kulcsszó listája a **feldolgozástól** a **sikeres** modell befejeződése után módosul. Ezután letöltheti a fájlt.

    ![A kulcsszó áttekintése](media/custom-keyword/custom-kws-portal-download-model.png)

1. A letöltött fájl egy `.zip` Archívum. Bontsa ki az archívumot, és a kiterjesztéssel rendelkező fájl jelenik meg `.table` . Ezt a fájlt használja az SDK-val a következő szakaszban, ezért ügyeljen arra, hogy az elérési utat jegyezze fel. a fájl neve tükrözi a kulcsszava nevét, például az **eszköz aktiválása** elemnél a fájlnév `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Kulcsszavas modell használata az SDK-val

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

Tesztelje egyéni kulcsszavait a [Speech Devices SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)rövid útmutatójában.
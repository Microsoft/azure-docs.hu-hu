---
title: A hangsegéd alkalmazás konfigurálása az Azure Percept Studióban
description: A hangsegéd alkalmazás konfigurálása az Azure Percept Studióban
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e41dea3b47c608b9e82ac57fa1cfe5247ea6cc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099012"
---
# <a name="managing-your-voice-assistant"></a>A hangsegéd kezelése

Ez a cikk bemutatja, hogyan konfigurálhatja a hangsegéd-alkalmazáshoz tartozó kulcsszót és parancsokat az [Azure Percept Studióban](https://go.microsoft.com/fwlink/?linkid=2135819). A kulcsszó IoT Hub a portálon belüli konfigurálásával kapcsolatos útmutatásért tekintse meg ezt a [útmutató cikket](./how-to-configure-voice-assistant.md).

Ha még nem hozott létre hangsegéd alkalmazást, tekintse meg [a kód nélküli hangsegéd kiépítése az Azure Percept Studióval és az Azure Percept hanggal](./tutorial-no-code-speech.md)című témakört.

## <a name="keyword-configuration"></a>Kulcsszavas konfiguráció

A kulcsszó egy beszéd asszisztens aktiválásához használt szó vagy rövid kifejezés. Például a "Hey Cortana" a Cortana segéd kulcsszava. A Hangaktiválás lehetővé teszi, hogy a felhasználók a kulcsszóval teljesen megkezdhetik a termékkel való interakciót. Mivel a termék folyamatosan figyeli a kulcsszót, az összes hang helyileg lesz feldolgozva az eszközön, amíg az észlelés nem történik meg, hogy a felhasználói adatok a lehető legmagánjellegűek legyenek.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguráció a hangsegéd bemutató ablakában

1. Kattintson az **egyéni kulcsszó** melletti **módosítás** elemre a bemutató oldalon.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Képernyőfelvétel a Hospitality bemutató ablakáról.":::

    Ha nincs megnyitva a bemutató oldal, navigáljon az eszköz lapra (lásd alább), majd kattintson a **hangsegéd tesztelése** **elemre** a bemutatóhoz való hozzáféréshez.

1. Válasszon ki egy elérhető kulcsszót, majd kattintson a **Mentés** gombra a módosítások alkalmazásához.

1. Az Azure Percept hangeszköz három LED-jelzőfénye fényes kékre (nincs villogás) változik, ha a konfigurálás befejeződött, és a hangsegéd készen áll a használatra.

### <a name="configuration-within-the-device-page"></a>Az eszköz oldalán található konfiguráció

1. Az [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)áttekintés lapján kattintson az **eszközök** elemre a bal oldali menü ablaktáblán.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Képernyőkép az Azure Percept Studio áttekintő oldaláról a Kiemelt eszközökkel.":::

1. Válassza ki azt az eszközt, amelyre a hangsegéd alkalmazást telepítette.

1. Nyissa meg a **Speech (beszédfelismerés** ) lapot.

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Képernyőkép a peremhálózati eszköz oldaláról, a Speech (beszédfelismerés) lappal kiemelve.":::

1. Kattintson a **kulcsszó** melletti **módosítás** elemre.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Képernyőkép az elérhető beszédfelismerési megoldás műveleteiről.":::

1. Válasszon ki egy elérhető kulcsszót, majd kattintson a **Mentés** gombra a módosítások alkalmazásához.

1. Az Azure Percept hangeszköz három LED-jelzőfénye fényes kékre (nincs villogás) változik, ha a konfigurálás befejeződött, és a hangsegéd készen áll a használatra.

## <a name="create-a-custom-keyword"></a>Egyéni kulcsszó létrehozása

A [Speech Studio](https://speech.microsoft.com/)használatával létrehozhat egy egyéni kulcsszót a hangsegédhez. Akár 30 percet is igénybe vehet egy alapszintű egyéni kulcsszavas modell betanításához.

Az egyéni kulcsszó létrehozásával kapcsolatos útmutatásért kövesse a [Speech Studio dokumentációját](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) . A konfigurálást követően az új kulcsszó elérhető lesz a Project Santa Cruz portálon a hangsegéd alkalmazással való használatra.

## <a name="commands-configuration"></a>Parancsok konfigurálása

Az egyéni parancsok megkönnyítik a hangvezérelt hangvezérelt alkalmazások készítését a hang-első interakciós élményhez. Az egyéni parancsok a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez legmegfelelőbbek.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguráció a hangsegéd bemutató ablakában

1. Kattintson az **Egyéni parancs** melletti **módosítás** elemre a bemutató oldalon. Ha nincs megnyitva a bemutató oldal, navigáljon az eszköz lapra (lásd alább), majd kattintson a **hangsegéd tesztelése** **elemre** a bemutatóhoz való hozzáféréshez.

1. Válassza ki az elérhető egyéni parancsok egyikét, majd kattintson a **Mentés** gombra a módosítások alkalmazásához.

### <a name="configuration-within-the-device-page"></a>Az eszköz oldalán található konfiguráció

1. Az [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)áttekintés lapján kattintson az **eszközök** elemre a bal oldali menü ablaktáblán.

1. Válassza ki azt az eszközt, amelyre a hangsegéd alkalmazást telepítette.

1. Nyissa meg a **Speech (beszédfelismerés** ) lapot.

1. Kattintson a **parancs** melletti **módosítás** elemre.

1. Válassza ki az elérhető egyéni parancsok egyikét, majd kattintson a **Mentés** gombra a módosítások alkalmazásához.

## <a name="create-custom-commands"></a>Egyéni parancsok létrehozása

A [Speech Studio](https://speech.microsoft.com/)használatával egyéni parancsokat hozhat létre a hangsegéd végrehajtásához.

Az egyéni parancsok létrehozásával kapcsolatos útmutatásért kövesse a [Speech Studio dokumentációját](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-custom-commands-application) . A konfigurálást követően az új parancsok az Azure Percept Studióban lesznek elérhetők a hangsegéd alkalmazással való használatra.

## <a name="next-steps"></a>Következő lépések

A hangsegéd-alkalmazás létrehozása után próbáljon ki egy [kód nélküli megoldást](./tutorial-nocode-vision.md) az Azure Percept DK használatával.
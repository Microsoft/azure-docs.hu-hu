---
title: Az Azure Percept hangeszközének áttekintése
description: További információ az Azure Percept hangról
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 23fae249cfceec75af0b7c49a3875ab447ecbafd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564262"
---
# <a name="introduction-to-azure-percept-audio"></a>Az Azure Percept audio bemutatása

Az Azure Percept audio egy olyan kiegészítő eszköz, amely a Speech AI-képességeket hozzáadja az [Azure PERCEPT DK](./overview-azure-percept-dk.md)-hoz. Egy előre konfigurált hangprocesszort és egy négy mikrofonos lineáris tömböt tartalmaz, amely lehetővé teszi a hangparancsok használatát, a kulcsszavak beolvasását és az Azure Cognitive Services súgójának széles körű beszédfelismerését. Az Azure Percept DK, az [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)és más Azure Edge felügyeleti szolgáltatásokkal integráltan integrálható. Az Azure Percept audio megvásárolható a [Microsoft online áruházban](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Vásárlás most](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Azure Percept – hangösszetevők

Az Azure Percept audio a következő főbb összetevőket tartalmazza:

- Termelésre kész Azure Percept hangeszköz (SoM) négy mikrofonos lineáris tömbvel és hangfeldolgozással a XMOS codec használatával
- Fejlesztő (a fejlesztők számára): 2x gomb, 3x LED, Micro USB és 3,5 mm audio jack
- Szükséges kábelek: FPC kábel, USB Micro Type-B – USB-A
- Üdvözlőlap
- Mechanikus csatlakoztatási tábla integrált 80/20 1010 sorozatú csatlakoztatással

## <a name="compute-capabilities"></a>Számítási képességek 

Az Azure Percept audio továbbítja a hangbemenetet az Azure Percept DK-szállítói tábla PROCESSZORán futó Speech stacken, amely hibrid Edge-Felhőbeli módon történik. Ezért az Azure Percept hangjának olyan operációs rendszerrel kell rendelkeznie, amely támogatja a Speech stacket, hogy végre lehessen hajtani. 

A hang feldolgozása a következőképpen történik: 

- Azure Percept hang: rögzíti és átalakítja a hanganyagot, és elküldi azt a DK és a audio jack számára.

- Azure Percept DK: a Speech stack a távolsági kialakítást és visszhangot hajt végre, és feldolgozza a bejövő hangot a beszédre való optimalizáláshoz. A feldolgozás után kulcsszavas bepecsételés végezhető.

- Felhő: feldolgozza a természetes nyelvi parancsokat és kifejezéseket, a kulcsszavas ellenőrzést és az átképzést. 

- Offline: Ha az eszköz offline állapotban van, a rendszer felismeri a kulcsszót, és rögzíti az internetkapcsolat állapotát telemetria. A kulcsszó-észlelés megnövekedett hamis elfogadási aránya megfigyelhető, mert a felhőben nem hajtható végre a kulcsszó-ellenőrzés. 

## <a name="getting-started"></a>Első lépések

- [Az Azure Percept DK összeállítása](./quickstart-percept-dk-unboxing.md)
- [Az Azure Percept-hangeszköz csatlakoztatása a fejlesztői készlet](./quickstart-percept-audio-setup.md)
- [Az Azure Percept DK telepítési felületének befejezése](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Kód nélküli prototípus készítése

Hozzon létre egy [kód nélküli beszédfelismerési megoldást](./tutorial-no-code-speech.md) az Azure [Percept Studióban](https://go.microsoft.com/fwlink/?linkid=2135819) az Azure Percept hangsegéd-sablonjaival a vendéglátás, az egészségügy, a leltár és a gépjármű-forgatókönyvek használatához.

### <a name="manage-your-no-code-speech-solution"></a>A kód nélküli beszédfelismerési megoldás kezelése

- [A hangsegéd konfigurálása az Azure Percept Studióban](./how-to-manage-voice-assistant.md)
- [A hangsegéd konfigurálása az IOT hub-ban](./how-to-configure-voice-assistant.md)
- [Azure Percept – hanghibák elhárítása](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>További technikai információk

- [Azure Percept audio Adatlap](./azure-percept-audio-datasheet.md)
- [Gomb és a LED viselkedése](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Percept hangeszköz vásárlása a Microsoft online áruházból](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

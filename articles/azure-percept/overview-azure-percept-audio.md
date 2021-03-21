---
title: Az Azure Percept hangeszközének áttekintése
description: További információ az Azure Percept hangról
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 9ff0cb8e2417ed08ed4c2061674cc6932b511aed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595906"
---
# <a name="introduction-to-azure-percept-audio"></a>Az Azure Percept audio bemutatása

Az Azure Percept audio egy kiegészítő eszköz, amely a Speech AI-képességeket hozzáadja az Azure Percept DK-hoz. Egy előre konfigurált hangprocesszort és egy négy mikrofonos lineáris tömböt tartalmaz, amely lehetővé teszi a hangutasítások alkalmazását, a kulcsszavak beolvasását és az Azure-Cognitive Services használó helyi figyelőket. Az Azure Percept audio szolgáltatása lehetővé teszi, hogy az eszközök gyártói az Azure Percept DK-t az új, intelligens hangvezérelt eszközökre is kiterjessze. Az Azure Percept DK, az Azure Percept Studio és más Azure Edge felügyeleti szolgáltatásokkal integráltan integrálható. A [Microsoft online áruházban](https://go.microsoft.com/fwlink/p/?LinkId=2155270)megvásárolható.

> [!div class="nextstepaction"]
> [Vásárlás](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

<!---
:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Azure Percept Audio device.":::
--->
</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Azure Percept – hangösszetevők

Az Azure Percept audio a következő főbb összetevőket tartalmazza:

- Éles üzemre kész Azure Percept hangeszköz (SoM), négy mikrofonos lineáris tömbvel, és egy XMOS-kodek által végzett hangfeldolgozással
- Fejlesztői (a fejlesztők számára) táblája (tartalmazza a 2x gombokat, a 3x LED-eket, a Micro USB-t és a 3,5 mm-es hangcsatlakozót)
- Szükséges kábelek: FPC kábel, USB Micro Type-B – USB-A
- Üdvözlőlap
- Mechanikus csatlakoztatási tábla integrált 80/20 1010 sorozatú csatlakoztatással

## <a name="compute-capabilities"></a>Számítási képességek 

Az Azure Percept audio továbbítja a hangbemenetet az Azure Percept DK szolgáltatói tábláján futó Speech stacken a hibrid Edge-Cloud módon. Ezért az Azure Percept hangjának olyan operációs rendszerrel kell rendelkeznie, amely támogatja a Speech stacket, hogy végre lehessen hajtani. 

A feldolgozás a következőképpen történik: 

- Azure Percept hang: rögzíti és átalakítja a hanganyagot, és elküldi azt a DK és a audio jack számára.

- Azure Percept DK: a Speech stack a távolsági kialakítást és visszhangot hajt végre, és feldolgozza a bejövő hangot a beszédre való optimalizáláshoz. Ezután végrehajtja a bepecsételés kulcsszót.

- Felhő: feldolgozza a természetes nyelvi parancsokat és kifejezéseket, a kulcsszavas ellenőrzést és az átképzést. 

- Offline: Ha az eszköz offline állapotban van, a rendszer felismeri a kulcsszót, és rögzíti az internetkapcsolat állapotát telemetria. A kulcsszó-észlelés megnövekedett hamis elfogadási aránya megfigyelhető, mert a felhőben nem hajtható végre a kulcsszó-ellenőrzés. 

## <a name="getting-started"></a>Első lépések

- [Az Azure Percept DK összeállítása](./quickstart-percept-dk-unboxing.md)
- [Az Azure Percept DK telepítési felületének befejezése](./quickstart-percept-dk-set-up.md)
- [Az Azure Percept-hangeszköz csatlakoztatása a fejlesztői készlet](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>Kód nélküli prototípus készítése

Hozzon létre egy [kód nélküli beszédfelismerési megoldást](./tutorial-no-code-speech.md) az Azure [Percept Studióban](https://go.microsoft.com/fwlink/?linkid=2135819) az Azure Percept hangsegéd-sablonjaival a vendéglátás, az egészségügy, a leltár és a gépjármű-forgatókönyvek használatához.

### <a name="manage-your-no-code-speech-solution"></a>A kód nélküli beszédfelismerési megoldás kezelése

- [A hangsegéd konfigurálása az IOT hub-ban](./how-to-manage-voice-assistant.md)
- [A hangsegéd konfigurálása az Azure Percept Studióban](./how-to-configure-voice-assistant.md)
- [Azure Percept – hanghibák elhárítása](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>További technikai információk

- [Azure Percept audio Adatlap](./azure-percept-audio-datasheet.md)
- [Gomb és a LED viselkedése](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Percept hangeszköz vásárlása a Microsoft online áruházból](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

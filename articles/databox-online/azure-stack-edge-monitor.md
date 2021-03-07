---
title: A Azure Stack Edge Pro-eszköz monitorozása | Microsoft Docs
description: Ismerteti, hogyan használható a Azure Portal és a helyi webes felhasználói felület a Azure Stack Edge Pro figyelésére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: aae64cad3603725a4062d5afb42df974bbf8ac40
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438786"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>A Azure Stack Edge Pro figyelése

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

Ez a cikk ismerteti, hogyan figyelheti Azure Stack Edge Pro-t. Az eszköz figyeléséhez Azure Portal vagy a helyi webes KEZELŐFELÜLETet használhatja. A Azure Portal használatával megtekintheti az eszközök eseményeit, konfigurálhatja és kezelheti a riasztásokat, és megtekintheti a metrikákat. A fizikai eszköz helyi webes felhasználói felületének használatával megtekintheti a különböző eszközök összetevőinek hardveres állapotát.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Az eszközök eseményeinek és a kapcsolódó riasztások megtekintése
> * Eszköz-összetevők hardveres állapotának megtekintése
> * Az eszköz kapacitásának és tranzakciós metrikáinak megtekintése

## <a name="view-device-events"></a>Eszköz eseményeinek megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Hardver állapotának megtekintése

Hajtsa végre a következő lépéseket a helyi webes felhasználói felületen az eszközösszetevők hardverállapotának megtekintéséhez.

1. Csatlakozzon az eszköz helyi webes felhasználói felületéhez.
2. Válassza a **karbantartás > hardver állapota** lehetőséget. Megtekintheti az egyes eszközösszetevők állapotát.

    ![Hardver állapotának megtekintése](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Metrikák megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Mérőszámok az eszközön

Ez a szakasz az eszköz figyelési mérőszámait ismerteti. A metrikák a következőket tehetik:

* Kapacitási mérőszámok. A kapacitás metrikái az eszköz kapacitásával kapcsolatosak.

* Tranzakciós metrikák. A tranzakció metrikái az olvasási és írási műveletekhez kapcsolódnak az Azure Storage-hoz.

* Peremhálózati számítási mérőszámok. A peremhálózati számítási mérőszámok az eszköz peremhálózati számítási feladataihoz kapcsolódnak.

A metrikák teljes listája a következő táblázatban látható:

|Kapacitásmetrikák                     |Description  |
|-------------------------------------|-------------|
|**Rendelkezésre álló kapacitás**               | Az eszközre írható adatméretet jelöli. Más szóval ez a metrika az eszközön elérhető kapacitás. <br></br>Az eszköz kapacitása az eszközön és a felhőben található másolattal rendelkező fájlok helyi másolatának törlésével szabadítható fel.        |
|**Teljes kapacitás**                   | Az eszközön lévő összes bájtra vonatkozik, amely a helyi gyorsítótár teljes méreteként is hivatkozik. <br></br> Most már megnövelheti egy meglévő virtuális eszköz kapacitását egy adatlemez hozzáadásával. Adjon hozzá egy adatlemezt a virtuális gép hypervisor-felügyeletén keresztül, majd indítsa újra a virtuális gépet. Az átjáró eszköz helyi tárolóhelye ki lesz bővítve az újonnan hozzáadott adatlemezre. <br></br>További információkért keresse [fel a Hyper-V virtuális gép merevlemezének hozzáadása](https://www.youtube.com/watch?v=EWdqUw9tTe4)című témakört. |

|Tranzakciómetrikák              | Description         |
|-------------------------------------|---------|
|**Feltöltött Felhőbeli bájtok (eszköz)**    | Az eszközön található összes megosztáson feltöltött bájtok összege        |
|**Felhőbeli feltöltött bájtok (megosztás)**     | Egy megosztáson feltöltött bájtok száma. Ez a metrika a következő lehet: <br></br> AVG, amely (a megosztás/megosztások száma alapján feltöltött bájtok összege),  <br></br>Max, amely a megosztásból feltöltött bájtok maximális számát adja meg. <br></br>Minimum, amely a megosztásból feltöltött bájtok minimális száma      |
|**Felhőbeli letöltési teljesítmény (megosztás)**| Egy megosztáson letöltött bájtok száma. Ez a metrika a következő lehet: <br></br> Az AVG, amely (az összes olvasott vagy letöltött bájt összege egy megosztásba/megosztások száma) <br></br> Max, amely a megosztásból letöltött bájtok maximális számát adja meg.<br></br> és a min, amely a megosztásból letöltött bájtok minimális száma  |
|**Felhőbeli olvasási sebesség**            | A felhőből beolvasott összes bájt összege az eszközön lévő összes megosztáson keresztül     |
|**Felhőbeli feltöltési sebesség**          | A felhőbe írt összes bájt összege az eszközön található összes megosztáson keresztül     |
|**Felhőbeli feltöltési sebesség (megosztás)**  | Egy megosztásból/megosztásból a felhőbe írt összes bájt összege átlagos, maximális és minimális      |
|**Olvasási sebesség (hálózat)**           | Magában foglalja a felhőből beolvasott bájtok rendszerhálózati átviteli sebességét. Ez a nézet tartalmazhat olyan, a megosztásokra nem korlátozott adatforgalomat. <br></br>A felosztás megjeleníti az eszközön lévő összes hálózati adapter forgalmát, beleértve a nem csatlakoztatott vagy engedélyezett adaptereket is.      |
|**Írási átviteli sebesség (hálózat)**       | Magában foglalja a felhőbe írt összes bájt rendszerhálózati átviteli sebességét. Ez a nézet tartalmazhat olyan, a megosztásokra nem korlátozott adatforgalomat. <br></br>A felosztás megjeleníti az eszközön lévő összes hálózati adapter forgalmát, beleértve a nem csatlakoztatott vagy engedélyezett adaptereket is.          |

| Edge számítási metrikák              | Description         |
|-------------------------------------|---------|
|**Edge-számítás – memóriahasználat**      |           |
|**Edge-számítás – százalékos CPU**    |         |

## <a name="next-steps"></a>Következő lépések

További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](azure-stack-edge-manage-bandwidth-schedules.md) talál.
Ismerje meg, hogyan [kezelheti az eszköz-riasztási értesítéseket](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).

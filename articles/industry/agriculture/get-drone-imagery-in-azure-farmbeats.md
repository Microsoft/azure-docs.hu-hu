---
title: Drónképek lekérése
description: Ez a cikk azt ismerteti, hogyan kérhető le a-től származó drone-képek a partnerektől.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 18932d2ddb60242b4d7874dddf0349a62cd5c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177606"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Drone-képek beszerzése a drone-partnerektől

Ez a cikk azt ismerteti, hogyan vihet be orthomosaic-adatokból a drone-képekből származó partnereket az Azure FarmBeats Datahub. Az orthomosaic egy olyan légi ábrát vagy képet, amely geometriailag korrigálva van, és a drone által összegyűjtött adatokból van fűzve.

Jelenleg a következő képkeresési partnerek támogatottak.

  ![FarmBeats drone-képekkel rendelkező partnerek](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

A drone-képek Azure FarmBeats-mel való integrálása segít a farmon végzett orthomosaic adatok beszerzésében a datahub. Miután az elérhetővé válik, megtekintheti a FarmBeats-gyorsító szolgáltatásban. Az adatok az adatfúzióhoz, valamint a mesterséges intelligencia és a gépi tanulási modellek létrehozásához használhatók.

## <a name="before-you-begin"></a>Előkészületek

  - Győződjön meg arról, hogy telepítette az Azure FarmBeats-t. További információ a FarmBeats telepítéséről: az [Azure FarmBeats telepítése](install-azure-farmbeats.md).
  - Győződjön meg arról, hogy rendelkezik a farmhoz, amelyhez a FarmBeats-rendszeren definiált drone-képeket szeretné megadni.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>A FarmBeats-vel való drone-képek integrációjának engedélyezése

Adja meg az alábbi információkat az eszköz szolgáltatójának az FarmBeats való integráció engedélyezéséhez:
 - API-végpont
 - Bérlőazonosító
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs

Hajtsa végre az alábbi lépéseket.

1. Töltse le a [szkriptet](https://aka.ms/farmbeatspartnerscript), és bontsa ki a helyi meghajtóra. A zip-fájlban két fájl található.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és nyissa meg az Azure Cloud Shellt. Ez a lehetőség a portál jobb felső sarkában található eszköztáron érhető el.

    ![Azure Cloud Shell megnyitása a portál jobb felső sávján](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Győződjön meg arról, hogy a környezet **PowerShell**-re van beállítva.

    ![PowerShell-beállítás](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Töltse fel a Cloud Shell-példány 1. lépésében letöltött két fájlt.

    ![Fájlok feltöltése](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Nyissa meg azt a könyvtárat, ahová a fájlokat feltöltötte. Alapértelmezés szerint a rendszer feltölti őket a kezdőkönyvtár alá a Felhasználónév alatt.
6. Futtassa a következő parancsfájlt:

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. A képernyőn megjelenő utasításokat követve rögzítheti az API-végpont, a bérlői azonosító, az ügyfél-azonosító, az ügyfél titkos kulcsa és a EventHub kapcsolódási karakterláncának értékét.

    Miután megírta a szükséges hitelesítő adatokat a partner drone-szoftveres rendszerébe, importálhatja az összes farmot a FarmBeats rendszerből. Ezután a farm részleteit követve elvégezheti a repülési útvonal megtervezését és a drone-képek gyűjtését.

    Ha a nyers lemezképeket a drone-szolgáltatók szoftvere dolgozza fel, a drone szoftverrendszer feltölti a varrattal ellátható orthomosaic és más feldolgozott képeket a datahub.

## <a name="view-drone-imagery"></a>A drone-képek megtekintése

Az FarmBeats datahub való elküldését követően a FarmBeats Datahub API-k használatával kérdezheti le a jelenet-áruházat.

Azt is megteheti, hogy megtekinti a legújabb drone-rendszerképet a **Farm részletek** lapján. A rendszerkép megtekintéséhez kövesse a lépéseket.

1. Válassza ki azt a farmot, amelyhez a képeket feltöltötte. Megjelenik a **Farm** részletei lap.
2. Görgessen le a legújabb **precíziós térképek** szakaszhoz.
3. Tekintse meg a képet a **drone-képek** szakaszban.

    ![A drone képének szakasza](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>A drone-képek letöltése

Amikor kiválasztja a drone-képek szakaszt, megnyílik egy előugró ablak, amely a drone-orthomosaic nagy felbontású képét jeleníti meg.

![Nagy felbontású orthomosaic](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Az összes drone-Térkép megtekintése

A drone-szolgáltató által feltöltött fájlok és rendszerképek megjelennek a **Maps** szakaszban. Válassza ki a **Maps** szakaszt, majd a szűrés **Farm** alapján lehetőséget, és válassza ki a megfelelő fájlokat a megtekintéshez és a letöltéshez.

  ![Maps szakasz](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja a FarmBeats Datahub [API-kat](rest-api-in-azure-farmbeats.md) a drone-képek beszerzéséhez.

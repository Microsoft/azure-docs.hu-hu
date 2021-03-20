---
title: Egyéni szimulált eszköz létrehozása – Azure | Microsoft Docs
description: Ebben az oktatóanyagban a szimulációkban való használathoz létrehozhat egy egyéni szimulált eszközt az Eszközszimuláció használatával.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 1fe86aef832223a7485036343b4b12d8bb526e06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852387"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Oktatóanyag: Egyéni szimulált eszköz létrehozása

Ebben az oktatóanyagban a szimulációkban való használathoz létrehozhat egy egyéni szimulált eszközt az Eszközszimuláció használatával. Az Eszközszimuláció megismeréséhez használhatja az egyik mellékelt szimulált mintaeszközt. Emellett egyéni szimulált eszközöket is létrehozhat a cikkben ismertetett módon. További testreszabási lehetőségekért tekintse meg a [speciális eszközmodell létrehozását](iot-accelerators-device-simulation-advanced-device.md) ismertető cikket.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Szimulált eszközmodellek listájának megtekintése
> * Egyéni szimulált eszköz létrehozása
> * Eszközmodell klónozása
> * Eszközmodellek törlése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell az Eszközszimuláció egy üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem telepítette az eszköz-szimulációt, tekintse meg a következőt: [eszköz-szimulációs telepítés](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) a githubon.

## <a name="view-your-device-models"></a>Eszközmodellek megtekintése

A menüsávon válassza az **Eszközmodellek** gombot. Az **Eszközmodellek** lap felsorolja az Eszközszimuláció ezen példányában található összes elérhető eszközmodellt:

![Eszközmodellek](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Eszközmodell létrehozása

Kattintson az **+ Eszközmodellek hozzáadása** elemre a lap jobb felső sarkában:

![Eszközmodell hozzáadása](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

Ebben az oktatóanyagban létrehozhat egy szimulált hűtőt, amely hőmérséklet- és páratartalom-adatokat küld.

Adja meg az alábbi adatokat az űrlapon:

| Beállítás             | Érték                                                |
| ------------------- | ---------------------------------------------------- |
| Eszközmodell neve   | Hűtő                                         |
| Modell leírása   | Hőmérséklet- és páratartalom-érzékelőkkel rendelkező hűtő |
| Verzió             | 1.0                                                  |

> [!NOTE]
> Az eszközmodell nevének egyedinek kell lennie.

Kattintson az **+ Adatpont hozzáadása** elemre a hőmérséklet- és páratartalom-adatpontok hozzáadásához az alábbi értékekkel:

| Adatpont          | Működés        | Minimális érték | Maximális érték | Unit (Egység) |
| ------------------- | --------------- | --------- | --------- | ---- |
| Hőmérséklet         | Véletlenszerű          | -50       | 100       | F    |
| Páratartalom            | Véletlenszerű          | 0         | 100       | %    |

Kattintson a **Mentés** gombra az eszközmodell mentéséhez.

![Eszközmodell létrehozása](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

A hűtő ezzel szerepel az eszközmodellek listájában. Lehetséges, hogy a **Tovább** gombra kell kattintania, hogy egy másik lapra navigálva láthassa a hűtőt.

## <a name="clone-a-device-model"></a>Eszközmodell klónozása

Az eszközmodellek klónozásával létrehozhatja egy meglévő eszközmodell másolatát. Ezután az igényeinek megfelelően szerkesztheti a másolatot. A klónozással időt takaríthat meg, amikor hasonló eszközmodelleket kell létrehoznia.

Egy eszközmodell klónozásához jelölje be a modell mellett található jelölőnégyzetet, majd kattintson a **Klónozás** gombra a műveletsávon:

![Képernyőkép a kiválasztott modell és a klónozás gomb kiemeléséhez.](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Eszközmodellek törlése

Bármelyik egyéni eszközmodellt törölheti. Egy eszközmodell törléséhez jelölje be a modell mellett található jelölőnégyzetet, majd kattintson a **Törlés** gombra a műveletsávon:

![Eszközmodell törlése](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte, hogyan hozhat létre, klónozhat és törölhet egyéni eszközmodelleket. Az eszközmodellekkel kapcsolatos további információkért tekintse meg az alábbi útmutatót:

> [!div class="nextstepaction"]
> [Speciális eszközmodell létrehozása](iot-accelerators-device-simulation-advanced-device.md)
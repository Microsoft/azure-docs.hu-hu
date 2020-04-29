---
title: Új IoT-szimuláció létrehozása – Azure | Microsoft Docs
description: Ebben az oktatóanyagban létrehozhat és futtathat egy új szimulációt a Device Simulation használatával.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 09a6920e0d3a50da1bdacbf2bc7a80396c885897
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "61448564"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Oktatóanyag: IoT-eszközszimuláció üzembe helyezése és futtatása

Ebben az oktatóanyagban létrehozhat és futtathat egy új IoT-szimulációt az Eszközszimuláció, illetve egy vagy több szimulált eszköz használatával.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Az összes aktív és korábbi szimuláció megtekintése
> * Új szimuláció létrehozása és futtatása
> * Szimuláció mérőszámainak megtekintése
> * Szimuláció leállítása

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell az Eszközszimuláció üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe az Eszközszimulációt, végezze el az [Azure-beli IoT-eszközszimuláció üzembe helyezését és futtatását](quickstart-device-simulation-deploy.md) ismertető rövid útmutatóban leírt lépéseket.

## <a name="open-device-simulation"></a>Eszközszimuláció megnyitása

Az Eszközszimuláció böngészőben történő futtatásához először lépjen a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) területre. 

A rendszer arra kérheti, hogy lépjen be Azure-előfizetésének hitelesítő adataival.

Ezután kattintson az **Indítás** elemre a [gyorskonfigurálás](quickstart-device-simulation-deploy.md) során üzembe helyezett Device Simulation csempéjén.

## <a name="view-simulations"></a>Szimulációk megtekintése

A menüsávon válassza a **Szimulációk** gombot. A **Szimulációk** lap információkat jelenít meg az összes elérhető szimulációval kapcsolatban. Ezen a lapon láthatja a jelenleg futó és a korábban futtatott szimulációkat. Egy korábbi szimuláció újbóli futtatásához kattintson a szimuláció csempéjére a szimuláció részleteinek megnyitásához:

![Szimulációk](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Szimuláció létrehozása

Szimuláció létrehozásához kattintson az **+ Új szimuláció** elemre a jobb felső sarokban.

Egy szimuláció egy vagy több eszközmodellből áll. Az eszközmodell határozza meg a viselkedést, a telemetriát és a szimulálandó eszköz üzenetformátumát.

Eszközmodell hozzáadásához kattintson az **+ Eszköztípus hozzáadása** gombra, és válassza ki az eszközmodellt a listából. Egy szimuláció több eszközmodellel is rendelkezhet. Az egyes eszközmodellek különböző eszközszámmal és üzenetgyakorisággal rendelkezhetnek.

Amikor az új szimuláció űrlapja elkészült, kattintson a **Szimuláció indítása** elemre a szimuláció elindításához.

A szimuláció konfigurálása és elindítása a szimulált eszközök számától függően eltarthat néhány percig:

![Új szimuláció](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Szimuláció leállítása

Amikor a **Szimuláció indítása** gombra kattint, megjelenik a szimuláció részleteinek lapja. Ez a részleteket tartalmazó lap a szimuláció IoT Hubból származó statisztikáit és mérőszámait mutatja. Ezt a részleteket tartalmazó lapot elérheti úgy is, ha a szimuláció csempéjére kattint a **Szimulációk** lapon.

Egy szimuláció leállításához kattintson a **Szimuláció leállítása** elemre a jobb felső műveletsávon.

![Szimuláció leállítása](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan hozhat létre, futtathat és állíthat le egy szimulációt. Emellett azt is megismerte, hogyan tekintheti meg egy szimuláció részleteit. A szimulációk futtatásával kapcsolatos további információkért folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Egyéni szimulált eszköz létrehozása](iot-accelerators-device-simulation-create-custom-device.md)

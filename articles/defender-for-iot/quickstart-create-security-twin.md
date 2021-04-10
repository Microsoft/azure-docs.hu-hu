---
title: 'Gyors útmutató: biztonsági modul létrehozása – Twin'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre a Defender for IoT modul Twin-et az Azure Defender for IoT használatával.
ms.topic: quickstart
ms.date: 1/21/2021
ms.openlocfilehash: 2ee88bd23b7d125ef9244f8ff630ee5eb8cdd015
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782673"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Gyors útmutató: azureiotsecurity-modul létrehozása – Twin

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egyedi _azureiotsecurity_ -modult az új eszközökhöz, vagy létrehozhat egy batch-modult az összes eszközhöz egy IoT hub.

## <a name="prerequisites"></a>Előfeltételek

Nincsenek

## <a name="understanding-azureiotsecurity-module-twins"></a>Az ikrek azureiotsecurity-moduljának ismertetése

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is.

A IoT Defender teljes körű integrációt biztosít a meglévő IoT-eszközkezelés platformmal, így kezelheti az eszköz biztonsági állapotát, és használhatja a meglévő eszköz-vezérlési képességeket.
A IoT-integrációs Defender a IoT Hub Twin mechanizmus használatával érhető el.

További információ az IoT Hub Azure-beli modulbeli ikrek általános fogalmáról: [IoT hub modul ikrek](../iot-hub/iot-hub-devguide-module-twins.md) .

A IoT Defender a modul Twin mechanizmusát használja, és minden eszközhöz fenntart egy _azureiotsecurity_ nevű biztonsági modult.

A Defender-IoT-Micro-Agent Twin az eszközök biztonságára vonatkozó összes információt tartalmazza.

Ahhoz, hogy a Defender teljes mértékben használhassa a IoT funkciókat, létre kell hoznia, konfigurálnia és használniuk kell ezt a Defender-IoT-Micro-Agent ikreket a szolgáltatás minden eszközén.

## <a name="create-azureiotsecurity-module-twin"></a>Twin azureiotsecurity-modul létrehozása

a _azureiotsecurity_ modul ikrek két módon hozhatók létre:

1. [Modul Batch-parancsfájl](https://aka.ms/iot-security-github-create-module) – a automatikusan létrehozza a modult az új eszközök vagy eszközök számára az alapértelmezett konfiguráció használata nélkül.
1. Manuálisan szerkessze az egyes modulokat külön-külön az egyes eszközökhöz tartozó konfigurációkkal.

>[!NOTE]
> A Batch metódus használata nem írja felül az ikrek meglévő azureiotsecurity-modulját. A Batch metódus csak az ikrek új modulját hozza létre olyan eszközökhöz, amelyek még nem rendelkeznek különálló biztonsági modullal.

Tekintse meg az [ügynök konfigurációja](how-to-agent-configuration.md) című témakört, amelyből megtudhatja, hogyan módosíthatja vagy módosíthatja a meglévő modulok konfigurációját.

Új _azureiotsecurity_ -modul manuális létrehozása egy eszközhöz:

1. A IoT Hubban keresse meg és válassza ki azt az eszközt, amelyhez külön biztonsági modult szeretne létrehozni.

1. Válassza ki az eszközt, majd **adja hozzá a modul identitását**.

1. A **modul identitásának neve** mezőbe írja be a **azureiotsecurity** nevet.

1. Kattintson a **Mentés** gombra.

## <a name="verify-creation-of-a-module-twin"></a>Modul létrehozásának ellenőrzése

Annak ellenőrzése, hogy van-e különálló biztonsági modul egy adott eszközhöz:

1. Az Azure-IoT Hub válassza a **IoT eszközök** lehetőséget a **felfedezők** menüből.

1. Adja meg az eszköz AZONOSÍTÓját, vagy válasszon ki egy beállítást a **lekérdezési eszköz mezőben** , majd válassza az **eszközök lekérdezése** lehetőséget.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Eszközök lekérdezése":::

1. Jelölje ki az eszközt, vagy kattintson duplán az eszköz részletei lap megnyitásához.

1. Válassza a **modul identitások** menüt, és erősítse meg a **azureiotsecurity** modul létezését az eszközhöz társított modul-identitások listájában.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Eszközhöz társított modulok":::

Ha többet szeretne megtudni az IoT-modulhoz készült Defender tulajdonságainak testreszabásáról, tekintse meg az [ügynök konfigurálása](how-to-agent-configuration.md)című témakört.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan vizsgálhatja meg a biztonsági javaslatokat...

> [!div class="nextstepaction"]
> [Biztonsági javaslatok vizsgálata](quickstart-investigate-security-recommendations.md)
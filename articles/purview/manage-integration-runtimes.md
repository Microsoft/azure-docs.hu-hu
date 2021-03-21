---
title: Integrációs modulok létrehozása és kezelése
description: Ez a cikk az integrációs moduloknak az Azure hatáskörébe való létrehozásához és kezeléséhez szükséges lépéseket ismerteti.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980745"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul létrehozása és kezelése

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy saját üzemeltetésű integrációs modult (az adatforrásokat az Azure hatáskörébe).

> [!NOTE]
> A hatáskörébe Integration Runtime nem osztható meg egy Azure szinapszis Analytics-vagy Azure Data Factory-Integration Runtime ugyanazon a gépen. Külön gépre kell telepíteni.

## <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása

1. A hatáskörébe Studio kezdőlapján válassza a **felügyeleti központ** lehetőséget a bal oldali navigációs ablaktáblán.

2. A bal oldali ablaktábla **források és vizsgálat** területén válassza az **integrációs** modulok lehetőséget, majd válassza az **+ új** lehetőséget.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Kattintson az IR elemre.":::

3. Az **Integration Runtime telepítése** lapon válassza a **saját** üzemeltetésű Self-Hosted IR létrehozása lehetőséget, majd kattintson a **Folytatás** gombra.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Hozzon létre egy új.":::

4. Adja meg az IR nevét, majd válassza a létrehozás lehetőséget.

5. A **Integration Runtime beállítások** lapon kövesse a **manuális telepítés** szakaszban található lépéseket. Le kell töltenie az integrációs modult a letöltési helyről egy olyan virtuális gépre vagy gépre, amelyen futtatni szeretné.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="kulcs lekérése":::

   - Másolja és illessze be a hitelesítési kulcsot.

   - Töltse le a saját üzemeltetésű integrációs modult [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) egy helyi Windows-gépen. Indítsa el a telepítőt.

   - A **Integration Runtime (helyi) regisztrálása** lapon illessze be a korábban mentett két kulcs egyikét, és válassza a **regisztráció** lehetőséget.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="bemeneti kulcs.":::

   - Az **új Integration Runtime (helyi) csomópont** lapon válassza a **Befejezés** lehetőséget.

6. A saját üzemeltetésű integrációs modul sikeres regisztrálását követően a következő ablak jelenik meg:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="sikeresen regisztrálva.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul kezelése

A saját **üzemeltetésű** integrációs modult úgy szerkesztheti, ha a felügyeleti központban lévő **Integration Runtimes (integrációs** modulok) lehetőségre kattint, majd kiválasztja az IR-t, majd a Szerkesztés gombra kattint. Most már frissítheti a leírást, átmásolhatja a kulcsot, vagy újragenerálhatja az új kulcsokat.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="az IR szerkesztése.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="az IR-adatok szerkesztése.":::

A saját üzemeltetésű integrációs modult úgy törölheti, ha a felügyeleti központban lévő **Integration Runtimes** elemre navigál, majd KIVÁLASZTJA az IR-t, majd a **Törlés** gombra kattint. Az IR törlése után a rajta futó folyamatos vizsgálatok sikertelenek lesznek.

## <a name="next-steps"></a>Következő lépések

[Hogyan észlelhetők a vizsgálatok során az eltávolított eszközök?](concept-detect-deleted-assets.md)

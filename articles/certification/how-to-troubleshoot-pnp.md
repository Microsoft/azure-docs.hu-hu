---
title: IoT-eszköz Plug and Play hibaelhárítása
description: Útmutató az IoT-eszközt minősítő partnerek számára javasolt hibaelhárítási Plug and Play lépésekhez.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559189"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>IoT-tanúsítványprojekt Plug and Play hibaelhárítása

Az IoT Plug and Play-tanúsítási projekt Csatlakozás & tesztelési fázisában előfordulhatnak olyan forgatókönyvek, amelyek megakadályozzák az Azure for IoT tanúsítványszolgáltatás (AICS) tesztelésének sikeres futtatását.

## <a name="prerequisites"></a>Előfeltételek

- Be kell jelentkezve, és rendelkezik egy, az eszközhöz létrehozott projekttel az [Azure Certified Device Portalon.](https://certify.azure.com) További információért tekintse meg az [oktatóanyagot.](tutorial-01-creating-your-project.md)

## <a name="when-aics-tests-arent-passing"></a>Ha az AICS-tesztek nem haladnak át

Az AICS-teszt több okból is előfordulhat, hogy nem megy át. Kövesse az alábbi lépéseket a gyakori problémák ellenőrzéséhez és az eszköz hibaelhárításához.

1. Ellenőrizze, hogy az eszközkód be-e ékezeti a modellazonosító hasznos adatát a DPS-kiépítés során. Az AICS-nek ez a követelménye az eszköz érvényesítéséhez.
1. A korábbi tesztek telemetriai naplóit a gombra kattintva megtekintheti, hogy mi okozza a `View Logs` teszt sikertelenségét. A tesztüzenetek és a nyers adatok is elérhetők áttekintésre.  

    ![Tesztadatok áttekintése](./media/images/review-logs.png)

1. Bizonyos esetekben, ahol a naplók jelzik, próbálja meg újraindítani az eszközt, és újraindítani az eszköz `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` kiépítési folyamatát.
1. Ha az automatizált tesztek továbbra is sikertelenek, akkor helyettesítheti `request a manual review` az eredményeket. Ez manuális érvényesítési **kérést indít** el az Azure Certified Device csapatával.  

    ![Manuális felülvizsgálat kérése](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>Ha a "Figyelmeztetésekkel átkért" üzenet jelenik meg

A tesztek futtatásakor, ha az eredményt kapja, az azt jelenti, hogy nem érkezett meg telemetria a `Passed with warnings` tesztelési időszak alatt. Ennek oka lehet a telemetria hosszabb időintervallumokban való függősége, vagy olyan külső eseményindítók, amelyek nem voltak elérhetők. Ezután beküldheti az eszközt felülvizsgálatra, amelynek során a  felülvizsgáló csapat megállapítja, hogy szükség lesz-e manuális ellenőrzésre a jövőben.

## <a name="when-you-need-help-with-the-model-repository"></a>Ha segítségre van szüksége a modell adattárával

A modelltárhoz Plug and Play IoT-hibákkal kapcsolatban tekintse meg a Docs eszközmodell-adattárra vonatkozó [útmutatóját.](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository)

## <a name="next-steps"></a>Következő lépések

Reméljük, hogy ez az útmutató segít abban, hogy folytatja az IoT Plug and Play minősítését! Miután az AICS-t továbbadta, továbbléphet az oktatóanyagainkra, és elküldheti és közzéteheti az eszközét.

- [Oktatóanyag: Az eszköz tesztelése](tutorial-03-testing-your-device.md)

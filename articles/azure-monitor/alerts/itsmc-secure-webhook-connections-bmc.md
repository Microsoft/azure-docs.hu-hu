---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitorban – konfiguráció BMC-vel
description: Ebből a cikkből megtudhatja, hogyan csatlakoztatható a ITSM-termékek és-szolgáltatások a BMC-vel a Azure Monitor biztonságos exportálásával.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041705"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>A BMC Helix összekapcsolása Azure Monitor

A következő szakaszokban részletesen ismertetjük a BMC Helix-termék és az Azure-beli biztonságos exportálás összekapcsolását.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesítette a következő előfeltételeket:

* Az Azure AD regisztrálva van.
* A BMC Helix multi-Cloud Service Management támogatott verziója (19,08-es vagy újabb verzió).

## <a name="configure-the-bmc-helix-connection"></a>A BMC Helix-kapcsolatok konfigurálása

1. A következő eljárást használhatja a BMC Helix-környezetben a biztonságos exportálás URI-azonosítójának lekéréséhez:

   1. Jelentkezzen be az Integration studióba.
   1. Keresse meg a **create incidenst az Azure-riasztások** folyamatában.
   1. Másolja a webhook URL-címét.
   
   ![Képernyőfelvétel a webhook U R L-ről az Integration Studióban.](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Kövesse az utasításokat a verziónak megfelelően:
   * [A 20,02-es verzióra való előre összeépített integráció engedélyezése a Azure monitor](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [A 19,11-es verzióra való előre összeépített integráció engedélyezése a Azure monitor](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. A BMC Helix-kapcsolat konfigurációjának részeként nyissa meg az integrációs BMC-példányt, és kövesse az alábbi utasításokat:

   1. Válassza a **katalógus** lehetőséget.
   2. Válassza az **Azure-riasztások** lehetőséget.
   3. Válassza az **Összekötők** lehetőséget.
   4. Válassza a **konfiguráció** lehetőséget.
   5. Jelölje be az **új kapcsolatok** konfigurációjának hozzáadása elemet.
   6. Adja meg a konfigurációs szakasz információit:
      - **Név**: hozza létre a sajátját.
      - **Engedélyezési típus**: **nincs**
      - **Leírás**: hozza létre a sajátját.
      - **Webhely**: **felhő**
      - **Példányok száma**: **2**, az alapértelmezett érték.
      - **Ellenőrzés**: a használat engedélyezéséhez alapértelmezés szerint ki van választva.
      - Az Azure-bérlő azonosítója és az Azure-alkalmazás azonosítója a korábban megadott alkalmazásból származik.

![A BMC-konfigurációt megjelenítő képernyőkép.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)

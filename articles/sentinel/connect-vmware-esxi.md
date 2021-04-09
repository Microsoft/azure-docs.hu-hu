---
title: VMware ESXi-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a VMware ESXi adatösszekötőt az ESXi-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti az ESXi-adatlapokat a munkafüzetekben, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700869"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>A VMware ESXi összekötése az Azure Sentinelrel

> [!IMPORTANT]
> Az VMware ESXi-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a VMware ESXi berendezés az Azure Sentinelhez. A VMware ESXi adatösszekötő lehetővé teszi, hogy könnyedén beolvassa a VMware ESXi naplókat az Azure Sentinelbe, így jobban betekintést nyerhet a szervezet ESXi-tevékenységeibe, és javíthatja a biztonsági üzemeltetési képességeit. A VMware ESXi és az Azure Sentinel közötti integráció a telepített Log Analytics ügynökkel rendelkező syslog-kiszolgáló használatát teszi lehetővé. Egy Kusto függvényen alapuló, egyéni kialakítású naplózási elemzőt is használ.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

- A VMware ESXi-megoldást úgy kell konfigurálni, hogy a syslog használatával exportálja a naplókat.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Naplók elküldése a syslog-ügynöknek VMware ESXi  

Konfigurálja a VMware ESXit, hogy a syslog-ügynökön keresztül továbbítsa a syslog-üzeneteket az Azure Sentinel-munkaterületre.
3. Kövesse a **VMWare ESXi** oldalon megjelenő utasításokat.


1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a **VMWare ESXi (előzetes verzió)** összekötőt, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **VMWare ESXi** -összekötő lapon megjelenő utasításokat:

    1. A Linux-ügynök telepítése és előkészítése

        - Válasszon egy Azure Linux rendszerű virtuális gépet vagy egy nem Azure-beli Linux-gépet (fizikai vagy virtuális).

    1. A gyűjteni kívánt naplók konfigurálása

        - Válassza ki a létesítményeket és a megszakításokat a **munkaterület-ügynökök konfigurációjában**.

    1. A VMware ESXi konfigurálása és összekapcsolása

        - Kövesse ezeket az utasításokat az VMware ESXi a syslog továbbítására való konfigurálásához:
            - [VMware ESXi 3,5 és 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 és újabb](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            A távoli kiszolgáló esetében használja a Linux-ügynököt futtató Linux-gép IP-címét.

## <a name="validate-connectivity-and-find-your-data"></a>Kapcsolat ellenőrzése és az adataik megkeresése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek az Azure Sentinelben. 

A sikeres kapcsolatok létrejötte után az adat megjelenik **a naplókban, a** *naplózás kezelése* szakaszban a *syslog* táblában.

Ez az adatösszekötő egy Kusto függvény alapján egy elemzőtől függ, hogy a várt módon működjön-e. [Kövesse az alábbi lépéseket](https://aka.ms/sentinel-vmwareesxi-parser) a **VMwareESXi** Kusto függvény alias létrehozásához. Ezután beírhatja az `VMwareESXi` Azure Sentinel bármely lekérdezési ablakát az adatlekérdezéshez.

Néhány hasznos lekérdezési minta a Connector lap **következő lépések** lapján található.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakozhat VMware ESXi az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

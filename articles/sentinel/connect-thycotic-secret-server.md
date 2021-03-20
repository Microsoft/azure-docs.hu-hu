---
title: A Thycotic titkos kiszolgáló összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Thycotic Secret Server adatösszekötőt a Thycotic titkos kiszolgálói naplók Azure Sentinelbe való lekéréséhez. A Thycotic megtekintheti a munkafüzetekben tárolt titkos kiszolgálókat, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567937"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>A Thycotic titkos kiszolgálójának összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> A Thycotic titkos kiszolgáló összekötője jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Thycotic titkos kiszolgálói berendezés az Azure Sentinelhez. A Thycotic titkos kiszolgáló adatösszekötője lehetővé teszi, hogy könnyedén csatlakoztassa a Thycotic Secret Server-naplókat az Azure Sentinel használatával, így megtekintheti az adatait a munkafüzetekben, felhasználhatja egyéni riasztások létrehozásához és a vizsgálat javítására. A Thycotic és az Azure Sentinel közötti integráció a CEF-adatösszekötő használatával biztosítja a titkos kiszolgáló syslog-üzeneteinek megfelelő elemzését és megjelenítését.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz.

- A Thycotic titkos kiszolgálót úgy kell konfigurálni, hogy a syslog használatával exportálja a naplókat.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Thycotic titkos kiszolgálói naplók küldése az Azure Sentinelnek

A naplók Azure Sentinelbe való beszerzéséhez konfigurálja a Thycotic titkos kiszolgálóját, hogy a syslog-üzeneteket CEF formátumban küldje el a Linux-alapú naplózási kiszolgálónak (rsyslog vagy syslog-ng futtatásával). Ezen a kiszolgálón telepítve lesz a Log Analytics ügynök, és az ügynök továbbítja a naplókat az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a **Thycotic titkos kiszolgáló (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az **utasítások** lapon a **konfiguráció**:

    1. **1. Linux syslog-ügynök konfigurációja** – ezt a lépést akkor hajtsa végre, ha még nem fut a naplózási továbbító, vagy ha még egy másikra van szüksége. További részletekért és magyarázatért tekintse meg az [1. lépés: a naplózási továbbító üzembe helyezése](connect-cef-agent.md) az Azure Sentinel dokumentációjában.

    1. A **2. résznél. Common Event Format (CEF) naplók továbbítása a syslog-ügynökhöz** – kövesse a Thycotic utasításait a [titkos kiszolgáló konfigurálásához](https://thy.center/ss/link/syslog). Ennek a konfigurációnak a következő elemeket kell tartalmaznia:
        - Napló célhelye – a naplófájl-továbbító kiszolgáló állomásneve és/vagy IP-címe
        - Protokoll és port – **TCP 514** (ha ez nem ajánlott, ne feledje, hogy a párhuzamos módosítást a naplófájl-továbbító kiszolgálón a syslog démonon végezze el)
        - Napló formátuma – CEF
        - Naplózási típusok – az összes elérhető

    1. **3. alatt. Csatlakozás ellenőrzése** – az adatok betöltésének ellenőrzéséhez másolja a parancsot az összekötő lapra, és futtassa azt a napló-továbbítón. Részletesebb útmutatást és magyarázatot a [3. lépés: a kapcsolat ellenőrzése](connect-cef-verify.md) az Azure Sentinel dokumentációjában talál.

        Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen az **Azure Sentinel** szakaszban, a *CommonSecurityLog* táblában.

Ha Log Analytics szeretné lekérdezni a titkos Thycotic, másolja a következőt a lekérdezési ablakba, és alkalmazzon más szűrőket a választott módon:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Néhány hasznos munkafüzet és lekérdezési minta esetén tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Thycotic titkos kiszolgáló az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
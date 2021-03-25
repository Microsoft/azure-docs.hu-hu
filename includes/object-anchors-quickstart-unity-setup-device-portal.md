---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104091"
---
### <a name="set-up-the-windows-device-portal"></a>A Windows-eszközök portáljának beállítása

Ha Wi-Fi-kapcsolaton keresztül szeretne csatlakozni a HoloLens, kövesse az alábbi lépéseket:

1. Először [kapcsolódjon a HoloLens a Wi-Fi-](/hololens/hololens-network)hez.

2. Ezután szerezze be az IP-címet az eszközön a **beállítások > hálózati & Internet > Wi-Fi > speciális beállítások** elemre.

3. A SZÁMÍTÓGÉPén található webböngészőből nyissa meg a következőt: `https://<YOUR_HOLOLENS_IP_ADDRESS>` . A böngésző a következő üzenetet jeleníti meg: "probléma merült fel a webhely biztonsági tanúsítványával". Ez az üzenet azért fordul elő, mert az eszköz-portál számára kiállított tanúsítvány önaláírt tanúsítvány. Figyelmen kívül hagyhatja a tanúsítvány hibáját, és folytathatja a folytatást.

A Windows-eszközök portál beállításával kapcsolatos további [információkért tekintse meg a következőt](/windows/mixed-reality/using-the-windows-device-portal) :.
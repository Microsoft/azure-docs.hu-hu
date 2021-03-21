---
title: Defender IoT Micro Agent hibaelhárítás (előzetes verzió)
titleSuffix: Azure Defender for IoT
description: Megtudhatja, hogyan kezelheti a váratlan vagy megmagyarázhatatlan hibákat.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 07198a5d0ef5d0a6c9eed97523c61826e451b7f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124176"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender IoT Micro Agent hibaelhárítás (előzetes verzió)

Ha nem várt vagy megmagyarázhatatlan hibát észlel, az alábbi hibaelhárítási módszerekkel próbálja megoldani a problémákat. Igény szerint segítséget kaphat az Azure Defender for IoT termékért felelős csapatának is.   

## <a name="service-status"></a>Szolgáltatásállapot 

A szolgáltatás állapotának megtekintése: 

1. Futtassa a következő parancsot:

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Ellenőrizze, hogy a szolgáltatás stabil-e `active` , és hogy a folyamat ideje megfelelő-e.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Győződjön meg arról, hogy a szolgáltatás stabil, és ellenőrizze, hogy az aktív-e, és hogy az üzemidő megfelelő-e.":::

Ha a szolgáltatás a következő módon van felsorolva `inactive` , a szolgáltatás elindításához használja az alábbi parancsot:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Tudni fogja, hogy a szolgáltatás összeomlik, ha a folyamat ideje túl rövid. A probléma megoldásához át kell tekintenie a naplókat.

## <a name="review-logs"></a>Naplók áttekintése 

A következő parancs használatával ellenőrizheti, hogy a Defender IoT Micro Agent szolgáltatás rendszergazdai jogosultságokkal fut-e.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Győződjön meg arról, hogy a IoT Micro Agent szolgáltatáshoz tartozó Defender rendszergazdai jogosultságokkal fut.":::

A naplók megtekintéséhez használja a következő parancsot:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

A szolgáltatás újraindításához használja a következő parancsot: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [szolgáltatás támogatását és a nyugdíjazást](edge-security-module-deprecation.md).

---
title: Defender IoT Micro Agent hibaelhárítás (előzetes verzió)
description: Megtudhatja, hogyan kezelheti a váratlan vagy megmagyarázhatatlan hibákat.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011419"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender IoT Micro Agent hibaelhárítás (előzetes verzió)

Ha váratlan hiba fordul elő, a probléma megoldására tett kísérlet során a következő hibaelhárítási módszereket használhatja. Igény szerint segítséget kaphat az Azure Defender for IoT termékért felelős csapatának is.   

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

Tudni fogja, hogy a szolgáltatás összeomlik, ha a folyamat ideje kevesebb, mint 2 perc. A probléma megoldásához [át kell tekintenie a naplókat](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>A Micro Agent gyökérszintű jogosultságainak ellenőrzése

A következő parancs használatával ellenőrizheti, hogy a Defender IoT Micro Agent szolgáltatás rendszergazdai jogosultságokkal fut-e.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Győződjön meg arról, hogy a IoT Micro Agent szolgáltatáshoz tartozó Defender rendszergazdai jogosultságokkal fut.":::
## <a name="review-the-logs"></a>A naplók áttekintése 

A naplók áttekintéséhez használja a következő parancsot:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Gyors napló áttekintése

Ha a mikro-ügynök futtatása során probléma lép fel, a Micro agentet ideiglenes állapotba futtathatja, amely lehetővé teszi a naplók megtekintését a következő parancs használatával:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Indítsa újra a szolgáltatást

A szolgáltatás újraindításához használja a következő parancsot: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [szolgáltatás támogatását és a nyugdíjazást](edge-security-module-deprecation.md).

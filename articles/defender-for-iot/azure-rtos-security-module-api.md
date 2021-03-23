---
title: Defender-IoT-Micro-Agent az Azure RTOS API-hoz
description: Az Azure RTOS készült Defender-IoT-Micro-Agent hivatkozási API-ját.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: e7000a7e6d8ba332432f1ececa12bd9543e9e4a7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779392"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-IoT-Micro-Agent Azure RTOS API-hoz (előzetes verzió)

Ez az API kizárólag az Azure RTOS készült Defender-IoT-Micro-Agent használatával használható. További forrásokért tekintse meg a [Defender-IoT-Micro-Agent for Azure RTOS GitHub-erőforrást](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>A Defender-IoT-Micro-Agent engedélyezése az Azure RTOS

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Leírás

Ez a rutin lehetővé teszi az Azure IoT Defender-IoT-Micro-Agent alrendszer használatát. A belső állapotú gép felügyeli a biztonsági események gyűjteményét, és elküldi őket az Azure IoT Hubba. Az adatgyűjtés kezeléséhez csak egy NX_AZURE_IOT_SECURITY_MODULE példány szükséges és szükséges.

### <a name="parameters"></a>Paraméterek

| Név | Leírás |
|---------|---------|
| nx_azure_iot_ptr [in]    | Egy mutató a következőre: `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Visszatérési értékek

|Visszatérési értékek  |Leírás |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Az Azure IoT biztonsági moduljának engedélyezése sikeresen megtörtént.     |
|NX_AZURE_IOT_FAILURE   |  Belső hiba miatt nem sikerült engedélyezni az Azure IoT biztonsági modulját.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  A biztonsági modulnak érvényes #NX_AZURE_IOT példányt kell megadnia.      |

### <a name="allowed-from"></a>Engedélyezett

Szálak

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Az Azure IoT Defender letiltása – IoT-Micro-Agent

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Leírás

Ez a rutin letiltja az Azure IoT Defender-IoT-Micro-Agent alrendszert.

### <a name="parameters"></a>Paraméterek

| Név | Leírás |
|---------|---------|
| nx_azure_iot_ptr [in]    | Egy mutató a következőre: `NX_AZURE_IOT` . Ha NULL az egyedi példány, le van tiltva. |

### <a name="return-values"></a>Visszatérési értékek

|Visszatérési értékek  |Leírás |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Sikeres, ha az Azure IoT biztonsági moduljának letiltása sikeresen megtörtént.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Az Azure IoT Hub példány eltér az egyedi összetett példánytól.       |
|NX_AZURE_IOT_FAILURE    |  Belső hiba miatt nem sikerült letiltani az Azure IoT biztonsági modulját.       |

### <a name="allowed-from"></a>Engedélyezett

Szálak


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure RTOS Defender-IoT-Micro-Agent használatáról, tekintse meg a következő cikkeket:

- Tekintse át a Defender for IoT RTOS Defender – IoT-Micro-Agent [áttekintése című témakört](iot-security-azure-rtos.md).

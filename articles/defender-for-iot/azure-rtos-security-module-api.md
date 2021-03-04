---
title: Az Azure RTOS biztonsági moduljának API-ja
description: Az Azure RTOS biztonsági moduljának hivatkozási API-ját.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120062"
---
# <a name="security-module-for-azure-rtos-api"></a>Az Azure RTOS biztonsági moduljának API-ja 

Ez az API kizárólag az Azure RTOS biztonsági moduljának használatára szolgál. További forrásokért tekintse meg az [Azure RTOS GitHub-erőforrás biztonsági modulját](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-security-module-for-azure-rtos"></a>Az Azure RTOS biztonsági moduljának engedélyezése

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Leírás

Ez a rutin engedélyezi az Azure IoT biztonsági moduljának alrendszerét. A belső állapotú gép felügyeli a biztonsági események gyűjteményét, és elküldi őket az Azure IoT Hubba. Az adatgyűjtés kezeléséhez csak egy NX_AZURE_IOT_SECURITY_MODULE példány szükséges és szükséges.

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

## <a name="disable-azure-iot-security-module"></a>Az Azure IoT biztonsági modul letiltása

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototype

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Leírás

Ez a rutin letiltja az Azure IoT biztonsági moduljának alrendszerét.

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

Ha többet szeretne megtudni az Azure RTOS biztonsági moduljának használatáról, tekintse meg a következő cikkeket:

- Tekintse át a Defender for IoT RTOS biztonsági moduljának [áttekintését](iot-security-azure-rtos.md).
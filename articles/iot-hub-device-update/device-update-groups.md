---
title: Az Azure IoT Hub Device groups eszköz frissítésének ismertetése | Microsoft Docs
description: Az erőforráscsoportok használatának ismertetése.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679500"
---
# <a name="device-groups"></a>Device groups

Az eszközcsoport egy eszköz gyűjteménye. Az erőforráscsoportok segítségével számos eszközre méretezheti a központi telepítéseket. Minden eszköz egyszerre csak egy eszközcsoport tagja.
Dönthet úgy, hogy több erőforráscsoportot is létrehoz az eszközök rendszerezéséhez. Előfordulhat például, hogy a contoso a tesztelési laboratóriumban lévő eszközök "bevezetési" eszközét használja, és az operatív központban lévő eszközökhöz tartozó "kiértékelés" eszközcsoport. A contoso továbbá úgy is dönthet, hogy a földrajzi régiók alapján csoportosítja a termelési eszközeiket, hogy az eszközöket olyan ütemterv szerint frissítse, amely a regionális időzónákkal összhangban van. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Az eszközök csoportos létrehozásához használt Twin címke használata

A Device Twin címkék lehetővé teszik a felhasználók számára az eszközök csoportosítását. Az eszközöknek rendelkezniük kell egy ADUGroup kulccsal és egy értékkel a saját eszközén, hogy lehetővé tegyék a csoportok csoportosítását.

### <a name="device-twin-tag-format"></a>Eszköz kettős címke formátuma

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Kategorizálatlan eszközcsoport

A Uncategorized egy foglalt szó, amely a következő eszközök csoportosítására szolgál:
- Ne rendelkezzen a ADUGroup Device Twin címkével.
- A ADUGroup Device Twin címkével rendelkeznek, de a csoport nem jön létre ezzel a névvel.

Tegyük fel például, hogy az eszközök az eszköz két címkével rendelkeznek:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Alább láthatók azok az eszközök és a lehetséges csoportok, amelyek létrehozhatók.

|Eszköz |Group  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Group2|
|Device4    |Nem kategorizált|



## <a name="next-steps"></a>Következő lépések

[Eszközcsoport létrehozása](./create-update-group.md)

---
author: baanders
description: fájl belefoglalása az Azure digitális Twins telepítőjének lépéseinek áttekintéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560806"
---
Az új Azure Digital Twins-példány teljes beállítása két részből áll:
1. **A példány létrehozása**
2. **Felhasználói hozzáférési engedélyek beállítása**: az Azure-felhasználóknak rendelkezniük kell az Azure Digital Twins- *adattulajdonosi* szerepkörrel az Azure digitális Twins-példányon, hogy kezelni tudja az IT-t és annak adatait. Ebben a lépésben az Azure-előfizetéshez tartozó tulajdonos/rendszergazda rendeli hozzá ezt a szerepkört az Azure Digital Twins-példányt kezelő személyhez. Ez lehet önmaga vagy valaki más a szervezetben.
 
>[!NOTE]
>Ezeknek a műveleteknek a célja, hogy egy felhasználó elvégezze az Azure-előfizetéshez tartozó erőforrások és felhasználói hozzáférés felügyeletét. Bár egyes lépések alacsonyabb engedélyekkel is végezhetők, az ezekkel az engedélyekkel rendelkező személyekkel való együttműködéshez teljes mértékben be kell állítania egy használható példányt. Tekintse meg a további információkat az [*Előfeltételek: szükséges engedélyek*](#prerequisites-permission-requirements) szakaszban alább.
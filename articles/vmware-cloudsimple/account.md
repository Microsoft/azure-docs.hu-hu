---
title: Fiókkezelés – Azure VMware-megoldás a CloudSimple-portálon
description: Ismerteti, hogyan kezelheti a fiókokat az Azure VMware-megoldásban a CloudSimple-portálon
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4c26d5accce77ce6fd8c9b6c2b519b93f95013ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895172"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Fiókok kezelése az Azure VMware-megoldásban a CloudSimple-portálon

A CloudSimple szolgáltatás létrehozásakor létrehoz egy fiókot a CloudSimple. A fiók társítva van az Azure-előfizetéséhez, ahol a szolgáltatás található. Az előfizetéshez tartozó tulajdonosi és közreműködői szerepkörökkel rendelkező felhasználók hozzáférhetnek a CloudSimple-portálhoz. A CloudSimple szolgáltatáshoz társított Azure-előfizetés azonosítója és a bérlő azonosítója a fiókok oldalon található.

A CloudSimple-portál fiókjainak kezeléséhez [nyissa meg a portált](access-cloudsimple-portal.md) , és válassza a **fiók** lehetőséget az oldalsó menüben.

Válassza az **Összefoglalás** lehetőséget a vállalat CloudSimple-konfigurációjával kapcsolatos információk megtekintéséhez. Megjelenik a felhő-konfiguráció jelenlegi kapacitása, beleértve a privát felhők számát, a teljes tárterületet, a vSphere, a csomópontok számát és a számítási magok számát. Ha az aktuális konfiguráció nem felel meg az összes igénynek, a rendszer hivatkozást tartalmaz a további csomópontok megvásárlásához.

## <a name="email-alerts"></a>E-mailes riasztások

Bármely olyan személy e-mail-címét felveheti, akit értesíteni szeretne a saját Felhőbeli konfiguráció változásairól.

1. A **további e-mail-riasztások** területen kattintson az **új hozzáadása** lehetőségre.
2. Adja meg az e-mail-címet.
3. Nyomja le az ENTER billentyűt.  

Egy bejegyzés eltávolításához kattintson az **X** gombra.

## <a name="cloudsimple-operator-access"></a>CloudSimple-kezelő hozzáférése

Az operátori hozzáférés beállítása lehetővé teszi, hogy a CloudSimple segítséget nyújtson a CloudSimple-portálra való bejelentkezést lehetővé tevő támogatási szakembernek.  A beállítás alapértelmezés szerint engedélyezve van. A támogatási mérnök által az ügyfél-fiókba való bejelentkezéskor végrehajtott összes művelet rögzítve van, és elérhetővé válik a **tevékenység**  >  **naplózása** lapon végzett felülvizsgálathoz.

Kattintson a **CloudSimple operátor hozzáférés engedélyezve** váltógomb lehetőségre a hozzáférés be-és kikapcsolása.

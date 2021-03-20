---
title: Azure VMware-megoldás CloudSimple szerint – CloudSimple Node kvóta
description: Ismerteti a CloudSimple-csomópontok kvótáit, valamint a kvóta növelésének kérését
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 83dc9e26e03eb955d88340d1ed21084d4e685ed8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77019638"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple-kvóta korlátai

Négy csomópont a vásárláshoz elérhető alapértelmezett mennyiség, ha az előfizetés engedélyezve van a CloudSimple szolgáltatáshoz.  A Azure Portal bármely [csomópont-típusát](cloudsimple-node.md) megvásárolhatja.  Egy privát felhő létrehozásához legalább három csomópontnak kell futnia ugyanazon SKU-ban.  Ha megvásárolta a csomópontokat, a további csomópontok megvásárlásakor hibaüzenet jelenhet meg.

## <a name="quota-increase"></a>Kvótanövelés

Egy támogatási kérelem elküldésével növelheti a csomópont kvótáját. A szolgáltatási operatív csapat értékeli a kérést, és együttműködik Önnel a csomópont-kvóta növeléséhez.  Új jegy megnyitásakor válassza ki a következő beállításokat:

* Probléma típusa: **technikai**
* Előfizetés: **az előfizetés azonosítója**
* Szolgáltatás típusa: **VMware-megoldás CloudSimple szerint**
* Probléma típusa: **dedikált csomópontok kvótája**
* Probléma altípusa: **a dedikált csomópontok kvótájának növelésére**
* Subject: **kvóta növekedése**

A támogatási jegy részleteiben adja meg a szükséges számú csomópontot és a csomópont-SKU-t.

* Csomópont SKU
* Azon további csomópontok száma, amelyekhez a kvóta növelését kéri

## <a name="next-steps"></a>Következő lépések

* [Csomópontok vásárlása](create-nodes.md)
* [A CloudSimple-csomópontok áttekintése](cloudsimple-node.md)

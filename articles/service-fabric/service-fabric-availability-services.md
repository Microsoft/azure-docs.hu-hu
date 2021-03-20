---
title: Service Fabric szolgáltatások rendelkezésre állása
description: A szolgáltatás hibakeresését, feladatátvételét és helyreállítását ismerteti egy Azure Service Fabric alkalmazásban.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018902"
---
# <a name="availability-of-service-fabric-services"></a>Service Fabric szolgáltatások rendelkezésre állása
Ez a cikk áttekintést nyújt arról, hogyan kezeli az Azure Service Fabric egy szolgáltatás rendelkezésre állását.

## <a name="availability-of-service-fabric-stateless-services"></a>Service Fabric állapot nélküli szolgáltatások rendelkezésre állása
Service Fabric szolgáltatások lehetnek állapot-nyilvántartó vagy állapot nélküliek. Az állapot nélküli szolgáltatás olyan alkalmazás-szolgáltatás, amely nem rendelkezik olyan [helyi állapottal](service-fabric-concepts-state.md) , amely nem igényel jelentős rendelkezésre állást vagy megbízhatóságot.

Állapot nélküli szolgáltatás létrehozásához definiálni kell egy `InstanceCount` . A példányszám határozza meg az állapot nélküli szolgáltatás alkalmazás-logikájának azon példányainak számát, amelyeknek futniuk kell a fürtben. A példányok számának növelésével az állapot nélküli szolgáltatások horizontális felskálázásának ajánlott módja.

Ha egy állapot nélküli névvel ellátott szolgáltatás egy példánya meghibásodik, egy új példány jön létre egy jogosult csomóponton a fürtben. Előfordulhat például, hogy egy állapot nélküli szolgáltatási példány meghiúsul a Csomópont1, és újra létre kell hozni a Csomópont5 számítógépre.

## <a name="availability-of-service-fabric-stateful-services"></a>Service Fabric állapot-nyilvántartó szolgáltatások rendelkezésre állása
Az állapot-nyilvántartó szolgáltatáshoz társítva van egy állapot. Service Fabric egy állapot-nyilvántartó szolgáltatás replikák készletként van modellezve. Minden replika a szolgáltatás kódjának futó példánya. A replika a szolgáltatás állapotának másolatát is tartalmazhatja. Az olvasási és írási műveleteket egy, az *elsődlegesnek* nevezett replikán hajtja végre. Az írási műveletek állapotának módosításait a rendszer az *aktív formátumú másodlagos zónák* nevű replikakészlet más replikáinak *replikálja* , és alkalmazza. 

Csak egy elsődleges replika lehet, de több aktív másodlagos replika is lehet. Az aktív másodlagos replikák száma konfigurálható, és a replikák nagyobb száma több párhuzamos szoftver és hardverhiba esetén is elfogadható.

Ha az elsődleges replika leáll, Service Fabric teszi az új elsődleges replikát az egyik aktív másodlagos replikára. Ez az aktív másodlagos replika már rendelkezik az állapot frissített verziójával a *replikáláson* keresztül, és folytathatja a további írási/olvasási műveletek feldolgozását. Ez a folyamat *újrakonfigurálás* néven ismert, és az [újrakonfigurálási](service-fabric-concepts-reconfiguration.md) cikkben további útmutatást talál.

A replika fogalma elsődleges vagy aktív másodlagos, azaz *replika szerepkör*. Ezeket a replikákat a [replikák és példányok](service-fabric-concepts-replica-lifecycle.md) című cikkben találja. 

## <a name="next-steps"></a>Következő lépések
Service Fabric fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Service Fabric szolgáltatások skálázása](service-fabric-concepts-scalability.md)
- [Service Fabric szolgáltatások particionálása](service-fabric-concepts-partitioning.md)
- [Állapot meghatározása és kezelése](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)


---
title: Állapot kezelése az Azure Service Fabric Services szolgáltatásban
description: Az Azure Service Fabric állapotának megismerése, beleértve a szolgáltatási állapot definiálásának és kezelésének módját Service Fabric-szolgáltatásokban.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614570"
---
# <a name="service-state"></a>Szolgáltatás állapota
A **szolgáltatás állapota** arra a memóriára vagy lemezen tárolt adatra vonatkozik, amelyet a szolgáltatásnak működnie kell. Ez magában foglalja például a szolgáltatás által beolvasott és írt adatstruktúrákat és a tagok változóit. A szolgáltatás tervezésének módjától függően előfordulhat, hogy a lemezen tárolt fájlokat vagy más erőforrásokat is tartalmazhat. Például az a fájl, amelyet az adatbázis az adattárolók és a tranzakciós naplók tárolására fog használni.

Példaként vegyünk egy számológépet. Az alapszintű számológép-szolgáltatás két számot vesz igénybe, és visszaadja az összeget. A számítás végrehajtásához nem tartozik tag változó vagy egyéb információ.

Most gondolja át ugyanezt a számológépet, de egy további módszer a kiszámított utolsó összeg tárolásához és visszaküldéséhez. Ez a szolgáltatás most állapot-nyilvántartó. Állapot-nyilvántartó azt jelenti, hogy olyan állapotot tartalmaz, amelyet a rendszer ír, amikor új összeget számít ki, és beolvassa azt, amikor azt kéri, hogy adja vissza az utolsó számított összeget.

Az Azure Service Fabric az első szolgáltatást állapot nélküli szolgáltatásnak nevezzük. A második szolgáltatást állapot-nyilvántartó szolgáltatásnak nevezzük.

## <a name="storing-service-state"></a>Szolgáltatás állapotának tárolása
Az állapot lehet külsőleg, vagy az állapotot manipuláló kóddal együtt. A externalizációja általában egy külső adatbázis vagy más, a hálózaton keresztül különböző gépeken futó, vagy az ugyanazon a gépen található folyamaton kívüli adattár használatával végezhető el. A számológép példájában az adattár lehet az Azure Table Store SQL-adatbázisa vagy-példánya. Az összeg kiszámításához szükséges minden kérelem egy frissítést hajt végre az adatokon, és a szolgáltatásnak küldött kérések visszaadja az értéket az áruházból beolvasott aktuális értékből. 

Az állapot az állapotot kezelő kóddal együtt is elhelyezhető. A Service Fabric állapot-nyilvántartó szolgáltatásai általában a modell használatával készültek. Service Fabric biztosítja az infrastruktúrát annak biztosítására, hogy ez az állapot nagyfokú rendelkezésre állású, konzisztens és tartós legyen, és hogy a szolgáltatások így könnyen méretezhetők legyenek.

## <a name="next-steps"></a>Következő lépések
Service Fabric fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Service Fabric szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Service Fabric szolgáltatások skálázhatósága](service-fabric-concepts-scalability.md)
* [Service Fabric szolgáltatások particionálása](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)

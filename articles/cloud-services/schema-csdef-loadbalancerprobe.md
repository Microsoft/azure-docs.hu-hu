---
title: Azure Cloud Services (klasszikus) def. LoadBalancerProbe séma | Microsoft Docs
description: Az ügyfél által definiált LoadBalancerProbe egy végpontok állapotának mintavétele a szerepkör példányaiban. A szolgáltatás definíciós fájljában a webes vagy feldolgozói szerepköröket kombinálja.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1fbbb49918ed83bd38e0c1f879ed31386cfb756b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934121"
---
# <a name="azure-cloud-services-classic-definition-loadbalancerprobe-schema"></a>Azure Cloud Services (klasszikus) definíciós LoadBalancerProbe séma

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A terheléselosztó mintavétele az UDP-végpontok és-végpontok ügyfél által meghatározott állapotának mintavétele a szerepkör példányaiban. A `LoadBalancerProbe` nem önálló elem; a szolgáltatás definíciós fájljában a webes szerepkörrel vagy feldolgozói szerepkörrel együtt. A- `LoadBalancerProbe` t több szerepkör is használhatja.

A szolgáltatás definíciós fájljának alapértelmezett kiterjesztése. csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Terheléselosztó-mintavétel funkciója
A Azure Load Balancer feladata a bejövő forgalom útválasztása a szerepkör példányaira. A terheléselosztó meghatározza, hogy mely példányok fogadhatnak forgalmat az egyes példányok rendszeres vizsgálatával az adott példány állapotának megállapítása érdekében. A Load Balancer percenként többször többször is ellenőrzi az összes példányt. Két különböző lehetőség van a példány állapotának biztosítására a terheléselosztó számára – ez az alapértelmezett Load Balancer-mintavétel vagy egy egyéni terheléselosztó-mintavétel, amely a LoadBalancerProbe a. csdef fájlban való definiálásával valósítható meg.

Az alapértelmezett Load Balancer-mintavétel a virtuális gépen belül használja a vendég ügynököt, amely csak akkor figyeli és válaszol a HTTP 200 OK-válaszra, ha a példány kész állapotban van (például ha a példány nem a foglalt, az újrahasznosítás, a Leállítás stb. állapotú). Ha a vendég ügynök nem tud válaszolni a HTTP 200 OK értékre, akkor a Azure Load Balancer nem válaszol, és nem küld forgalmat erre a példányra. A Azure Load Balancer továbbra is Pingeli a példányt, és ha a vendég ügynök válaszol a HTTP 200-re, akkor a Azure Load Balancer újraküldi a forgalmat erre a példányra. Webes szerepkör használata esetén a webhely kódja általában olyan w3wp.exe fut, amelyet az Azure-háló vagy a vendég ügynök nem figyel, ami w3wp.exe (például:) hibákat eredményez. HTTP 500-válaszok) nem jelent jelentést a vendég ügynöknek, és a terheléselosztó nem tudja, hogy az adott példány elfogyjon a rotációs állapotból.

Az egyéni Load Balancer-mintavétel felülbírálja a vendég ügynök alapértelmezett mintavételét, és lehetővé teszi saját egyéni logikájának létrehozását a szerepkör-példány állapotának meghatározásához. A terheléselosztó rendszeresen mintavételt végez a végponton (alapértelmezés szerint 15 másodpercenként), a példány pedig rotációs állapotba kerül, ha a TCP ACK vagy HTTP 200 az időtúllépési időszakon belül válaszol (az alapértelmezett érték 31 másodperc). Ez hasznos lehet a saját logikájának megvalósításához, hogy eltávolítsa a példányokat a terheléselosztó forgása alól, például ha nem 200 állapotot ad vissza, ha a példány meghaladja a 90%-os CPU-t. Az w3wp.exet használó webes szerepkörök esetében ez azt is jelenti, hogy automatikusan figyeli a webhelyét, mert a webhely kódjában fellépő hibák nem 200 állapotot adnak vissza a terheléselosztó-mintavételbe. Ha nem határoz meg LoadBalancerProbe a. csdef fájlban, akkor a rendszer az alapértelmezett terheléselosztó-viselkedést (a korábban leírt módon) használja.

Ha egyéni terheléselosztó-mintavételt használ, meg kell győződnie arról, hogy a logikája figyelembe veszi a RoleEnvironment. OnStop metódust. Az alapértelmezett Load Balancer-mintavétel használatakor a rendszer elveszi a példányt a OnStop meghívása előtt, de az egyéni terheléselosztó mintavétele továbbra is visszatérhet a 200-es OK értékre a OnStop esemény során. Ha a OnStop eseményt használja a gyorsítótár kiürítéséhez, a szolgáltatás leállításához, vagy más módon olyan módosításokat hajt végre, amelyek hatással lehetnek a szolgáltatás futásidejű viselkedésére, akkor biztosítania kell, hogy az egyéni Load Balancer-mintavételi logika eltávolítja a példányt a rotációból.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Alapszintű szolgáltatás-definíciós séma a terheléselosztó mintavételéhez
 A terheléselosztó-mintavételt tartalmazó szolgáltatási definíciós fájl alapformátuma a következő.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Séma elemei
A `LoadBalancerProbes` szolgáltatás definíciós fájljának eleme a következő elemeket tartalmazza:

- [LoadBalancerProbes elem](#LoadBalancerProbes)
- [LoadBalancerProbe elem](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a> LoadBalancerProbes elem
Az `LoadBalancerProbes` elem a Load Balancer-vizsgálatok gyűjteményét írja le. Ez az elem a [LoadBalancerProbe elem](#LoadBalancerProbe)szülő eleme. 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a> LoadBalancerProbe elem
Az `LoadBalancerProbe` elem a modell állapotának mintavételét határozza meg. Több Load Balancer-mintavételt is meghatározhat. 

A következő táblázat a elem attribútumait ismerteti `LoadBalancerProbe` :

|Attribútum|Típus|Leírás|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Kötelező. A terheléselosztó mintavételének neve. A névnek egyedinek kell lennie.|
| `protocol`          | `string` | Kötelező. Megadja a végpont protokollját. A lehetséges értékek: `http` és `tcp`. Ha `tcp` meg van adva, egy fogadott ACK-t kell megadnia a mintavétel sikerességéhez. Ha meg `http` van adva, a mintavétel sikeres végrehajtásához a megadott URI-azonosítótól 200 OK-válasz szükséges.|
| `path`              | `string` | A virtuális gép állapotának kéréséhez használt URI. `path` kötelező, ha a értéke `protocol` `http` . Ellenkező esetben nem engedélyezett.<br /><br /> Nincs alapértelmezett érték.|
| `port`              | `integer` | Választható. A mintavétel kommunikációs portja. Ez bármely végpont esetében opcionális, mivel a mintavételhez ugyanazt a portot fogja használni a rendszer. Egy másik portot is beállíthat a szondázás számára. A lehetséges értékek tartománya 1 és 65535 között van, beleértve a következőt:.<br /><br /> Az alapértelmezett értéket a végpont állítja be.|
| `intervalInSeconds` | `integer` | Választható. Az az időtartam (másodpercben), ameddig a végpontot a rendszer állapotának meghatározásához gyakran kell megkeresni. Az intervallum általában valamivel kevesebb, mint a lefoglalt időtúllépési időszak (másodpercben), amely két teljes mintavételt tesz lehetővé, mielőtt a példány elforgatása megtörténjen.<br /><br /> Az alapértelmezett érték 15, a minimális érték 5.|
| `timeoutInSeconds`  | `integer` | Választható. Az időtúllépési időtartam (másodpercben), amely arra a mintavételi időszakra vonatkozik, ahol nincs válasz, a rendszer a végpont felé irányuló további forgalom leállítását eredményezi. Ez az érték lehetővé teszi, hogy a végpontok gyorsabban vagy lassabban legyenek, mint az Azure-ban használt tipikus időpontok (amelyek az alapértelmezettek).<br /><br /> Az alapértelmezett érték 31, a minimális érték 11.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
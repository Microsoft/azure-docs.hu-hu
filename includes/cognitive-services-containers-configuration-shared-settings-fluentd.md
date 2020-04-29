---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67712501"
---
A Fluent egy nyílt forráskódú adatgyűjtő az egyesített naplózáshoz. A `Fluentd` beállítások kezelik a tároló kapcsolódását egy [Fluent](https://www.fluentd.org) -kiszolgálóval. A tároló tartalmaz egy olyan, a rendszer által használt naplózási szolgáltatót, amely lehetővé teszi a tároló számára a naplók írását, és opcionálisan metrikus adatokat egy Fluent-kiszolgáló számára.

A következő táblázat a `Fluentd` szakasz által támogatott konfigurációs beállításokat ismerteti.

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Host` | Sztring | A Fluent-kiszolgáló IP-címe vagy DNS-állomásneve. |
| `Port` | Egész szám | A Fluent-kiszolgáló portja.<br/> Az alapértelmezett érték a 24224. |
| `HeartbeatMs` | Egész szám | A szívverési időköz (ezredmásodpercben). Ha az intervallum lejárta előtt nem küldték el az esemény forgalmát, a rendszer szívverést továbbít a Fluent-kiszolgálónak. Az alapértelmezett érték 60000 ezredmásodperc (1 perc). |
| `SendBufferSize` | Egész szám | A küldési műveletek számára lefoglalt hálózati puffer mérete (bájt). Az alapértelmezett érték 32768 bájt (32 kilobájt). |
| `TlsConnectionEstablishmentTimeoutMs` | Egész szám | Az SSL/TLS-kapcsolatnak a Fluent-kiszolgálóval való létesítéséhez szükséges időkorlát ezredmásodpercben. Az alapértelmezett érték 10000 ezredmásodperc (10 másodperc).<br/> Ha `UseTLS` a értéke false (hamis), akkor a rendszer figyelmen kívül hagyja ezt az értéket. |
| `UseTLS` | Logikai | Azt jelzi, hogy a tárolónak SSL/TLS protokollt kell használnia a Fluent-kiszolgálóval való kommunikációhoz. Az alapértelmezett érték a hamis. |
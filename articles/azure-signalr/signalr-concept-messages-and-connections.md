---
title: Üzenetek és kapcsolatok az Azure Signaler szolgáltatásban
description: Az Azure Signaler szolgáltatás üzeneteivel és kapcsolataival kapcsolatos főbb fogalmak áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491945"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Üzenetek és kapcsolatok az Azure Signaler szolgáltatásban

Az Azure Signaler szolgáltatás számlázási modellje a kapcsolatok számától és az üzenetek számától függ. Ez a cikk azt ismerteti, hogyan történik az üzenetek és a kapcsolatok meghatározása, és hogyan számítható ki a számlázás.


## <a name="message-formats"></a>Üzenetek formátuma 

Az Azure Signaler szolgáltatás ugyanazokat a formátumokat támogatja, mint a ASP.NET Core-jelző: [JSON](https://www.json.org/) és a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Üzenet mérete

Az Azure Signaler szolgáltatáshoz nem tartozik méretkorlát az üzeneteknél.

A nagyméretű üzenetek felosztása kisebb üzenetekre történik, amelyek nem több mint 2 KB-ot kapnak, és külön továbbításra kerülnek. Az SDK-k az üzenetek felosztását és összeállítását kezelik. Nincs szükség fejlesztői erőfeszítésekre.

A nagyméretű üzenetek negatív hatással vannak az üzenetkezelés teljesítményére. Ha lehetséges, használjon kisebb üzeneteket, és tesztelje, hogy az egyes használati esetek optimális mérete milyen.

## <a name="how-messages-are-counted-for-billing"></a>Az üzenetek számlázásának számítása

A számlázáshoz csak az Azure Signaler szolgáltatás kimenő üzenetei számítanak. Az ügyfelek és a kiszolgálók közötti ping üzenetek figyelmen kívül lesznek hagyva.

A 2 KB-nál nagyobb üzenetek több, egyenként 2 KB-os üzenetnek számítanak. A Azure Portal üzenet száma diagramja minden egyes 100 üzenettel frissül.

Tegyük fel például, hogy rendelkezik egy alkalmazás-kiszolgálóval és három ügyféllel:

Az App Server 1 KB-os üzenetet küld az összes csatlakoztatott ügyfélnek, az App Servertől a szolgáltatásnak küldött üzenet az ingyenes bejövő üzenetnek minősül. Csak a szolgáltatásból küldött három üzenet lesz kiszámlázva kimenő üzenetként.

Az A ügyfél 1 KB-os üzenetet küld egy másik "B" ügyfélnek, anélkül, hogy az App Serveren kellene haladnia. Az A ügyfél és a szolgáltatás közötti üzenet ingyenes bejövő üzenetet küld. A szolgáltatásból a B ügyfélnek küldött üzenet számlázása kimenő üzenetként történik.

Ha három ügyféllel és egy alkalmazás-kiszolgálóval rendelkezik. Egy ügyfél 4 KB-os üzenetet küld, amely lehetővé teszi a kiszolgáló számára az összes ügyfél számára történő szórást. A számlázott üzenetek száma nyolc: egy üzenet a szolgáltatásból az alkalmazáskiszolgáló felé, valamint három üzenet a szolgáltatástól az ügyfeleknek. Minden üzenet 2 2 KB-os üzenetnek számít.

## <a name="how-connections-are-counted"></a>A kapcsolatok számlálása

Kiszolgálói kapcsolatok és ügyfélkapcsolatok érhetők el az Azure Signaler szolgáltatással. Alapértelmezés szerint minden alkalmazáskiszolgáló öt kezdeti kapcsolattal kezdődik, és minden ügyfélnek van egy ügyfélkapcsolata.

Tegyük fel például, hogy két alkalmazás-kiszolgálóval rendelkezik, és öt hubokat határoz meg a kódban. A kiszolgáló kapcsolatának száma 50:2 alkalmazás-kiszolgáló * 5 hub * 5 kapcsolat/központ.

A Azure Portalban látható kapcsolatok száma magában foglalja a kiszolgálói kapcsolatokat, az ügyfélkapcsolatokat, a diagnosztikai kapcsolatokat és az élő nyomkövetési kapcsolatokat. A kapcsolatok típusai a következő listában vannak meghatározva:

- **Kiszolgáló kapcsolata**: összekapcsolja az Azure signaler szolgáltatást és az App Servert.
- **Ügyfélkapcsolat**: az Azure signaler szolgáltatás és az ügyfélalkalmazás összekapcsolása.
- **Diagnosztikai kapcsolatok**: olyan speciális Ügyfélkapcsolat, amely részletesebb naplót eredményezhet, ami hatással lehet A teljesítményre. Ezt a fajta ügyfelet hibaelhárításra tervezték.
- **Élő nyomkövetési kapcsolat**: az élő nyomkövetési végponthoz csatlakozik, és az Azure signaler szolgáltatás élő nyomkövetését fogadja. 
 
Figyelje meg, hogy az élő nyomkövetési kapcsolat nem ügyfél-kapcsolatként vagy kiszolgálói kapcsolatként számít. 

A ASP.NET-jelző eltérő módon számítja ki a kiszolgáló kapcsolatait. Az Ön által meghatározott hubok mellett egy alapértelmezett hubot is tartalmaz. Alapértelmezés szerint minden alkalmazás-kiszolgálónak öt további kezdeti kiszolgálói kapcsolatra van szüksége. Az alapértelmezett hub kezdeti kapcsolatok száma más hubokkal konzisztens marad.

A szolgáltatás és az alkalmazáskiszolgáló folyamatosan szinkronizálja a kapcsolat állapotát, és a kiszolgáló kapcsolatainak beállításával jobb teljesítményt és a szolgáltatás stabilitását teszi elérhetővé.  Így előfordulhat, hogy a kiszolgáló-kapcsolatok száma időről időre változik.

## <a name="how-inboundoutbound-traffic-is-counted"></a>A bejövő/kimenő forgalom számításának módja

A szolgáltatásba küldött üzenet bejövő üzenet. A szolgáltatásból küldött üzenet kimenő üzenet. A forgalmat bájtban számítjuk ki.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Összesítési típusok a Azure Monitorban](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [ASP.NET Core jelző konfigurálása](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

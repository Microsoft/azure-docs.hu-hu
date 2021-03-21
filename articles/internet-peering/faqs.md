---
title: Internetes társközi – gyakori kérdések
titleSuffix: Azure
description: Internetes társközi – gyakori kérdések
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75775497"
---
# <a name="internet-peering---faqs"></a>Internetes társközi – gyakori kérdések

Az alábbi információkat általános kérdésekre is áttekintheti.

**Mi a különbség az internetes társ-és a társ-szolgáltatási szolgáltatás között?**

Az egyenrangú szolgáltatás olyan szolgáltatás, amely nagyvállalati szintű nyilvános IP-kapcsolatot biztosít a Microsoft vállalatának ügyfelei számára. A nagyvállalati szintű internet olyan internetszolgáltatókon keresztül kapcsolódik a hálózathoz, amelyek nagy átviteli sebességű kapcsolattal rendelkeznek a Microsofttal, és redundanciát biztosítanak. Emellett a felhasználói forgalom a legközelebbi Microsoft Edge-re való késésre van optimalizálva. A társ-létesítési szolgáltatás a partneri szolgáltatóval való egyenrangú kapcsolaton alapul. A partnerrel való társítási kapcsolatnak az Exchange-társítással nem rendelkezőnek kell lennie. A közvetlen társításnak helyi és földrajzi redundanciával kell rendelkeznie.

**Mi az örökölt peering?**

A Azure PowerShell használatával beállított egyenrangú kapcsolatok Azure-erőforrásként kezelhetők. A múltban beállított összekapcsolási kapcsolatokat a rendszer olyan örökölt társításként tárolja, amelyet az Azure-erőforrásként való átalakításra is választhat.

**New-AzPeeringDirectConnectionObject neve esetén milyen IP-címek vannak megadva a Microsoft és a társ-eszközök számára?**

New-AzPeeringDirectConnectionObject parancsmag meghívásakor az a/31 (a. b. c. d/31) vagy a/30 (a. b. c. d/30) címnek van megadva. A rendszer az első IP-címet (a. b. c. d + 0) adja meg a társ eszközének és második IP-címének (a. b. c. d + 1) a Microsoft-eszköz számára.

**Mi a MaxPrefixesAdvertisedIPv4 és a MaxPrefixesAdvertisedIPv6 paraméter a New-AzPeeringDirectConnectionObject parancsmagban?**

A MaxPrefixesAdvertisedIPv4 és a MaxPrefixesAdvertisedIPv6 paraméterek azon IPv4-és IPv6-előtagok maximális számát jelölik, amelyeknek a társának el kívánja fogadni a Microsoftot. Ezeket a paramétereket bármikor módosíthatja.
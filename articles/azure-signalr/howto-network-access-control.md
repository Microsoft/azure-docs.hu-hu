---
title: A hálózati hozzáférés-vezérlés konfigurálása
titleSuffix: Azure SignalR Service
description: Konfigurálja a hálózati hozzáférés-vezérlést az Azure Signaler szolgáltatáshoz.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143220"
---
# <a name="configure-network-access-control"></a>A hálózati hozzáférés-vezérlés konfigurálása

Az Azure Signaler szolgáltatás lehetővé teszi a szolgáltatási végponthoz való hozzáférés szintjének védelmét és felügyeletét, a kérelem típusa és a felhasznált hálózatok részhalmaza alapján. A hálózati szabályok konfigurálásakor csak a megadott hálózatokon adatokat kérő alkalmazások férhetnek hozzá az Azure Signaler szolgáltatáshoz.

Az Azure Signaler szolgáltatás egy nyilvános végponttal rendelkezik, amely az interneten keresztül érhető el. [Saját végpontokat is létrehozhat az Azure signaler szolgáltatáshoz](howto-private-endpoints.md). A privát végpont egy magánhálózati IP-címet rendel a VNet az Azure Signaler szolgáltatáshoz, és a VNet és az Azure Signaler szolgáltatás közötti összes forgalmat privát kapcsolaton keresztül biztosítja. Az Azure Signaler szolgáltatás hálózati hozzáférés-vezérlése a nyilvános végpontok és a magánhálózati végpontok hozzáférés-vezérlését is lehetővé teszi.

Lehetőség van arra is, hogy engedélyezze vagy megtagadja a nyilvános végpontok és az egyes magánhálózati végpontok bizonyos típusú kérelmeit. Letilthatja például a nyilvános végpont összes [kiszolgálói kapcsolatát](signalr-concept-internals.md#server-connections) , és győződjön meg arról, hogy csak egy adott VNet származnak.

Egy olyan alkalmazás, amely hozzáfér egy Azure Signaler szolgáltatáshoz, ha a hálózati hozzáférés-vezérlési szabályok érvényben maradnak, a kérelemhez megfelelő engedély szükséges.

## <a name="scenario-a---no-public-traffic"></a>A forgatókönyv – nincs nyilvános forgalom

Az összes nyilvános forgalom teljes megtagadásához először konfigurálja a nyilvános hálózati szabályt úgy, hogy ne engedélyezze a kérelem típusát. Ezután olyan szabályokat kell konfigurálnia, amelyek hozzáférést biztosítanak az adott virtuális hálózatok érkező forgalomhoz. Ez a konfiguráció lehetővé teszi az alkalmazások biztonságos hálózati határának kiépítését.

## <a name="scenario-b---only-client-connections-from-public-network"></a>B forgatókönyv – csak a nyilvános hálózatról érkező ügyfélkapcsolatok

Ebben a forgatókönyvben a nyilvános hálózati szabályt úgy is beállíthatja, hogy csak a nyilvános hálózatról érkező [ügyfélkapcsolatokat](signalr-concept-internals.md#client-connections) engedélyezze. Ezután konfigurálhat magánhálózati szabályokat úgy, hogy az adott VNet származó más típusú kérelmeket is engedélyezzen. Ez a konfiguráció elrejti az alkalmazás-kiszolgálókat a nyilvános hálózatról, és biztonságos kapcsolatot létesít az alkalmazás-kiszolgálók és az Azure Signaler szolgáltatás között.

## <a name="managing-network-access-control"></a>A hálózati hozzáférés-vezérlés kezelése

Az Azure Signaler szolgáltatás hálózati hozzáférés-vezérlését az Azure Portal segítségével kezelheti.

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt Azure-jelző szolgáltatást.

1. Kattintson a **hálózati hozzáférés-vezérlés** beállítások menüre.

    ![Hálózati ACL a portálon](media/howto-network-access-control/portal.png)

1. Az alapértelmezett művelet szerkesztéséhez kapcsolja be az **Engedélyezés/megtagadás** gombot.

    > [!TIP]
    > Az alapértelmezett művelet az a művelet, amelyet akkor teszünk, ha nincs ACL-szabály egyezése. Ha például az alapértelmezett művelet meg van **tagadva**, akkor a rendszer megtagadja az alább nem explicit módon jóváhagyott kérelmeket.

1. A nyilvános hálózati szabály szerkesztéséhez válassza a **nyilvános hálózatban** lévő engedélyezett típusú kérelmek lehetőséget.

    ![Nyilvános hálózati hozzáférés-vezérlési lista szerkesztése a portálon ](media/howto-network-access-control/portal-public-network.png)

1. A magánhálózati végpontok hálózati szabályainak szerkesztéséhez válassza az egyes sorokban a **magánhálózati végponti kapcsolatok** alatt található kérelmek megengedett típusai lehetőséget.

    ![Privát végponti ACL szerkesztése a portálon ](media/howto-network-access-control/portal-private-endpoint.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

További információ az [Azure Private linkről](../private-link/private-link-overview.md).
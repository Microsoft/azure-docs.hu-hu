---
title: Események küldése a Auth0-ből az Azure-ba a Azure Event Grid használatával
description: A Auth0 és az Azure-szolgáltatások közötti események befejezése Azure Event Grid használatával.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077863"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Azure Event Grid integrálása a Auth0

Ez a cikk a Auth0 és az Azure-fiókok összekapcsolását ismerteti Event Grid partneri témakör létrehozásával.

A Auth0 által támogatott események teljes listájáért tekintse meg a Auth0-események [típusának kódját](https://auth0.com/docs/logs/references/log-event-type-codes) .

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Események küldése a Auth0-ből Azure Event Grid
Auth0-események küldése az Azure-ba:

1. Event Grid erőforrás-szolgáltató engedélyezése
1. Auth0-partneri témakör beállítása a Auth0-irányítópulton
1. A partner témakör aktiválása az Azure-ban
1. Előfizetés Auth0 származó eseményekre

További információ ezekről a fogalmakról: Event Grid [fogalmak](concepts.md).

### <a name="enable-event-grid-resource-provider"></a>Event Grid erőforrás-szolgáltató engedélyezése
Ha korábban már használta a Event Gridt, regisztrálnia kell a Event Grid erőforrás-szolgáltatót. Ha korábban már használta a Event Gridt, ugorjon a következő szakaszra.

Az Azure Portalon:
1. A bal oldali menüben válassza az előfizetések lehetőséget.
1. Válassza ki a használni kívánt előfizetést Event Grid
1. A bal oldali menüben, a beállítások területen válassza az erőforrás-szolgáltatók elemet.
1. A Microsoft. EventGrid keresése
1. Regisztráció kiválasztása
1. Frissítéssel ellenőrizze, hogy az állapot regisztrálva van-e

### <a name="set-up-an-auth0-partner-topic"></a>Auth0-partneri témakör beállítása
Az integrációs folyamat részeként be kell állítania a Auth0-t az esemény forrásaként való használatra (ez a lépés a [Auth0 irányítópulton](https://manage.auth0.com/)történik).

1. Jelentkezzen be a [Auth0-irányítópultra](https://manage.auth0.com/).
1. Navigáljon a naplók > streamek elemre.
1. Kattintson a + stream létrehozása lehetőségre.
1. Válassza a Azure Event Grid lehetőséget, és adjon meg egy egyedi nevet az új adatfolyamnak.
1. Az eseményforrás létrehozásához adja meg az Azure-előfizetés AZONOSÍTÓját, az Azure-régiót és az erőforráscsoport nevét. 
1. Kattintson a Mentés gombra.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Auth0-partneri témakör aktiválása az Azure-ban
Az Azure-beli Auth0-témakör aktiválása lehetővé teszi, hogy az események a Auth0-ből az Azure-ba folynak.

1. Jelentkezzen be az Azure Portalra.
1. Keressen `Partner Topics` a lap tetején, és kattintson a szolgáltatások lehetőségre `Event Grid Partner Topics` .
1. Kattintson arra a témakörre, amely megfelel a Auth0-irányítópulton létrehozott adatfolyamnak.
1. Erősítse meg, hogy a `Source` mező megfelel a Auth0-fióknak.
1. Kattintson az aktiválás gombra.

### <a name="subscribe-to-auth0-events"></a>Feliratkozás Auth0-eseményekre

#### <a name="create-an-event-handler"></a>Eseménykezelő létrehozása
A partneri témakör teszteléséhez szüksége lesz egy eseménykezelőre. Nyissa meg az Azure-előfizetését, és hozzon létre egy olyan szolgáltatást, amely [eseménykezelőként](event-handlers.md) , például egy [Azure-függvényként](custom-event-to-function.md)támogatott.

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Előfizetés a Auth0-partneri témakörre
Az Auth0-partneri témakörre való feliratkozás lehetővé teszi, hogy Event Grid, ahol szeretné, hogy a Auth0-események elindulnak.

1. A Auth0-integráció partneri témakör paneljén válassza az + esemény-előfizetés lehetőséget a felső részen.
1. Az esemény-előfizetés létrehozása lapon:
    1. Adja meg az esemény-előfizetés nevét.
    1. Válassza ki a végpont típusához létrehozott Azure-szolgáltatást vagy webhookot.
    1. Kövesse az adott szolgáltatásra vonatkozó utasításokat.
    1. Kattintson a Létrehozás gombra.

## <a name="testing"></a>Tesztelés
A Auth0-partneri témakörnek az Azure-nal való integrálásának használatra késznek kell lennie.

### <a name="verify-the-integration"></a>Az integráció ellenőrzése
Annak ellenőrzése, hogy az integráció a várt módon működik-e:

1. Jelentkezzen be a Auth0-irányítópultra.
1. Navigáljon a naplók > streamek elemre.
1. Kattintson a Event Grid streamre.
1. A streamben kattintson a Health (állapot) fülre. Az adatfolyamnak aktívnak kell lennie, és mindaddig, amíg nem lát hibát, a stream működik.

Próbáljon meg [egy olyan Auth0 műveletet meghívni, amely egy eseményt indít el közzétételre](https://auth0.com/docs/logs/references/log-event-type-codes) az események folyamata során.

## <a name="delivery-attempts-and-retries"></a>Kézbesítési kísérletek és újrapróbálkozások
A Auth0-események továbbítása az Azure-ba egy streaming mechanizmus használatával történik. Minden eseményt a rendszer a Auth0-ben indít el. Ha Event Grid nem tudja fogadni az eseményt, a Auth0 legfeljebb háromszor próbálkozik az esemény kézbesítésével. Ellenkező esetben a Auth0 naplózza a hibát a rendszerbe történő kézbesítéshez.

## <a name="next-steps"></a>Következő lépések

- [Auth0-partneri témakör](auth0-overview.md)
- [A partneri témakörök áttekintése](partner-events-overview.md)
- [Legyen Event Grid partner](partner-onboarding-overview.md)
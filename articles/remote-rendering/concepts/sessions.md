---
title: Remote Rendering-munkamenetek
description: Leírja, hogy mi a távoli renderelési munkamenet?
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: a74fae74a2d0ebbb71d65420475e5772e44a8d84
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507093"
---
# <a name="remote-rendering-sessions"></a>Remote Rendering-munkamenetek

Az Azure távoli renderelés (ARR) esetében a *munkamenet* kulcsfontosságú fogalom. Ez a cikk azt ismerteti, hogy pontosan milyen a munkamenet.

## <a name="overview"></a>Áttekintés

Az Azure-alapú távoli renderelés összetett renderelési feladatok felhőbe szervezésével működik. Ezek a renderelési feladatok nem teljesíthetők egyetlen kiszolgálóval sem, mivel a legtöbb felhőalapú kiszolgálónak nincsenek GPU-k. Az érintett adatok mennyisége és az eredményeknek az interaktív képarányok alapján történő létrehozásával kapcsolatos követelmények miatt az a feladata, hogy a kiszolgáló milyen módon kezelje a felhasználói kéréseket, és nem lehet átadni egy másik, menet közbeni gépre, ahogy az a leggyakoribb webes adatforgalom esetében is lehetséges.

Ez azt jelenti, hogy az Azure Remote rendering használatakor a szükséges hardver-képességekkel rendelkező felhőalapú kiszolgálót kizárólag a renderelési kérelmek kezelésére kell fenntartani. A *munkamenetek* a kiszolgálóval való interakció során felmerülő mindenre vonatkoznak. A szolgáltatás a használatra kész gép lefoglalására (*bérletére*) vonatkozó kezdeti kéréssel kezdődik, és folytatja a modellek betöltésére és módosítására vonatkozó összes parancsot, és a bérletnek a felhőalapú kiszolgálón való kiadása után fejeződik be.

## <a name="managing-sessions"></a>Munkamenetek kezelése

A munkamenetek kezelésének és kezelésének több módja is van. A munkamenetek [felügyeleti Rest APIon](../how-tos/session-rest-api.md)keresztüli létrehozása, frissítése és leállítása egymástól független módon történik. A C# és a C++ nyelvben ezek a műveletek az osztályok és a használatával érhetők el `AzureFrontend` `AzureSession` . Az Unity-alkalmazások esetében az összetevő további segédprogram-funkciókat is biztosít `ARRServiceUnity` .

Ha aktív munkamenethez *kapcsolódott* , akkor a rendszer az osztályon keresztül teszi elérhetővé a műveleteket, például a [modellek betöltését](models.md) és a jelenettel való interakciót `AzureSession` .

### <a name="managing-multiple-sessions-simultaneously"></a>Egyszerre több munkamenet kezelése

Egyetlen eszközről nem lehet teljes mértékben *csatlakozni* több munkamenethez. Azonban létrehozhat, megfigyelheti és leállíthat annyi munkamenetet, amennyit csak szeretne egyetlen alkalmazásból. Mindaddig, amíg az alkalmazás nem a munkamenethez való kapcsolódásra szolgál, nem szükséges olyan eszközön futtatni, mint a HoloLens 2. Ilyen implementáció esetén előfordulhat, hogy egy központi mechanizmus segítségével szeretné vezérelni a munkameneteket. Például létrehozhat egy webalkalmazást, ahol több tabletta és HoloLenses is bejelentkezhet. Ezután az alkalmazás megjelenítheti a tabletták beállításait, például azt, hogy melyik CAD-modellt szeretné megjeleníteni. Ha a felhasználó kiválaszt egy választ, a rendszer ezeket az információkat közli az összes HoloLenses, hogy közös felhasználói élményt hozzon létre.

## <a name="session-phases"></a>Munkamenet-fázisok

Minden munkamenet több fázison megy keresztül.

### <a name="session-startup"></a>Munkamenet indítása

Ha az ARR-t arra kéri, hogy [hozzon létre egy új munkamenetet](../how-tos/session-rest-api.md#create-a-session), akkor az első dolog, ha egy munkamenet [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)-t ad vissza. Ez az UUID lehetővé teszi a munkamenet adatainak lekérdezését. Az UUID-t és a munkamenet alapszintű információit 30 napig őrzi meg a rendszer, így a munkamenet leállítása után is lekérdezheti az adatokat. Ezen a ponton a **munkamenet-állapotot** a rendszer az **induláskor**fogja jelenteni.

Ezt követően az Azure Remote rendering megpróbál olyan kiszolgálót találni, amely képes a munkamenet üzemeltetésére. A keresésnek két paramétere van. Először csak a [régiójában](../reference/regions.md)lévő kiszolgálókat fogja fenntartani. Ennek oka, hogy a régiók közötti hálózati késés túl magas lehet a tisztességes felhasználói élmény garantálása érdekében. A második tényező a megadott kívánt *méret* . Az egyes régiókban korlátozott számú kiszolgáló érhető el, amelyek megfelelnek a standard vagy a [*prémium*](../reference/vm-sizes.md) [*szintű*](../reference/vm-sizes.md) kérésnek. Ennek következtében, ha a kért méret összes kiszolgálója jelenleg használatban van a régiójában, a munkamenet létrehozása sikertelen lesz. A hiba oka lehet a [lekérdezés](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Ha *standard szintű* kiszolgáló-méretet kér, és a kérés magas kereslet miatt meghiúsul, a nem jelenti azt, hogy a *prémium* szintű kiszolgáló igénylése is sikertelen lesz. Ha így van, akkor kipróbálhatja a *prémium* szintű kiszolgáló méretének visszaesését.

Ha a szolgáltatás megfelelő kiszolgálót talál, a megfelelő virtuális gépet (VM) át kell másolnia egy Azure távoli renderelési gazdagépre való bekapcsolásához. Ez a folyamat több percig is eltarthat. Ezt követően a virtuális gép elindult, és a **munkamenet állapota** **készre**vált.

Ezen a ponton a kiszolgáló kizárólag a bemenetre vár. Ez azt is megteheti, hogy a szolgáltatásért díjat számítunk fel.

### <a name="connecting-to-a-session"></a>Csatlakozás egy munkamenethez

Ha a munkamenet *elkészült*, *csatlakozhat* hozzá. A csatlakozás közben az eszköz parancsokat tud küldeni a modellek betöltéséhez és módosításához. Minden ARR-gazdagép egyszerre csak egy ügyféleszközök kiszolgálására szolgál, így amikor egy ügyfél egy munkamenethez csatlakozik, kizárólagos felügyeletet gyakorol a renderelt tartalom felett. Ez azt is jelenti, hogy a renderelési teljesítmény soha nem változik a vezérlőn kívüli okokból.

> [!IMPORTANT]
> Bár csak egy ügyfél tud *csatlakozni* egy munkamenethez, a munkamenetek alapvető információi, például a jelenlegi állapotuk, csatlakozás nélkül kérhetők le.

Amíg az eszköz egy munkamenethez csatlakozik, a más eszközök csatlakozási próbálkozásai sikertelenek lesznek. Ha azonban a csatlakoztatott eszköz kapcsolata megszakad, akár önként, akár valamilyen hiba miatt, a munkamenet egy másik csatlakozási kérelmet fog fogadni. A rendszer elveti az összes korábbi állapotot (a betöltött modelleket és az ilyeneket), így a következő csatlakoztatási eszköz tiszta lappal rendelkezik. Így a munkamenetek többször is felhasználhatók a különböző eszközökön, és előfordulhat, hogy a végfelhasználók a legtöbb esetben el tudják rejteni a munkamenet indítási terhelését.

> [!IMPORTANT]
> A távoli kiszolgáló soha nem változtatja meg az ügyféloldali adat állapotát. Az ügyfélalkalmazás minden adatmutációt (például átalakítási frissítéseket és betöltési kérelmeket) kell végrehajtania. Minden művelet azonnal frissíti az ügyfél állapotát.

### <a name="session-end"></a>Munkamenet vége

Új munkamenet igénylése esetén a *címbérlet maximális idejét*kell megadnia, jellemzően az egy és nyolc óra közötti tartományban. Ez az az időtartam, ameddig a gazdagép el fogja fogadni a bemenetet.

A munkamenet befejezésének két rendszeres oka van. Manuálisan kérheti le a munkamenet leállítását vagy a címbérlet maximális idejét. A gazdagépre irányuló aktív kapcsolatok mindkét esetben azonnal le vannak zárva, és a szolgáltatás le van állítva a kiszolgálón. A kiszolgálót ezután visszaadja az Azure-készletnek, és más célra is visszaigénylést kaphat. A munkamenet leállítása nem vonható vissza vagy szakítható meg. A munkamenet- **állapot** leállítási munkameneten való lekérdezése vagy **leállt** vagy **lejárt**, attól függően, hogy manuálisan leállították-e, vagy elérte a címbérlet maximális idejét.

Néhány hiba miatt előfordulhat, hogy egy munkamenet le is állítható.

A munkamenet leállítása után minden esetben nem számítunk fel díjat.

> [!WARNING]
> Azt jelzi, hogy egy munkamenethez kapcsolódik-e, és hogy mennyi ideig, nem befolyásolja a számlázást. A szolgáltatásért fizetendő díj a *munkamenet időtartamától*függ, ami azt jelenti, hogy a kiszolgáló csak Ön számára van fenntartva, és a kért hardver-képességek (a [lefoglalt méret](../reference/vm-sizes.md)). Ha elindít egy munkamenetet, öt percig kapcsolódjon, majd ne állítsa le a munkamenetet, hogy az a bérlet lejárta előtt is fusson, a teljes munkamenet címbérletének idejére. Ezzel szemben a *címbérlet maximális időtartama* többnyire biztonsági háló. Nem számít, hogy a munkamenetet nyolc órás bérlettel kéri-e, majd csak öt percig használja, ha ezt követően manuálisan leállítja a munkamenetet.

#### <a name="extend-a-sessions-lease-time"></a>Munkamenet címbérleti idejének meghosszabbítása

Kiterjesztheti egy aktív munkamenet [címbérleti idejét](../how-tos/session-rest-api.md#update-a-session) , ha kiderül, hogy továbbra is szüksége van rá.

## <a name="example-code"></a>Mintakód

Az alábbi kód a munkamenet indításának egyszerű megvalósítását, a *készenléti* állapotra, a csatlakozásra, majd a leválasztásra és a Leállítás visszakapcsolására való várakozást mutatja be.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Több `AzureFrontend` és `AzureSession` példány is kezelhető, módosítható és lekérdezhető a kódból. Egyszerre azonban csak egyetlen eszköz csatlakozhat egyszerre `AzureSession` .

A virtuális gép élettartama nincs a `AzureFrontend` példányhoz vagy a `AzureSession` példányhoz kötve. `AzureSession.StopAsync` a munkamenet leállítására kell hívni.

Az állandó munkamenet-azonosító lekérdezhető helyileg a-n keresztül `AzureSession.SessionUUID()` és gyorsítótárazva. Ezzel az AZONOSÍTÓval az alkalmazás meghívja `AzureFrontend.OpenSession` a kötést az adott munkamenethez.

Ha a `AzureSession.IsConnected` értéke TRUE (igaz), a `AzureSession.Actions` egy példányát adja vissza `RemoteManager` , amely a [modellek betöltésére](models.md), az [entitások](entities.md)manipulálására és a megjelenített jelenet [adatainak lekérdezésére](../overview/features/spatial-queries.md) szolgáló függvényeket tartalmazza.

## <a name="next-steps"></a>További lépések

* [Entitások](entities.md)
* [Modellek](models.md)

---
title: Az első megbízható szolgáltatás létrehozása javában
description: Bevezetés egy Microsoft Azure Service Fabric alkalmazás létrehozására az állapot nélküli és állapot-nyilvántartó szolgáltatásokkal Java-ban.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-javai
ms.openlocfilehash: f67957d711958febdb01dfad0b3c44a92cb0bcfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535236"
---
# <a name="get-started-with-reliable-services-in-java"></a>Ismerkedés a Reliable Services Javával
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)
>
>

Ez a cikk ismerteti az Azure Service Fabric Reliable Services alapismereteket, és végigvezeti a Java-ban írt egyszerű, megbízható szolgáltatásalkalmazás létrehozásán és üzembe helyezésén. 

## <a name="installation-and-setup"></a>Telepítés és beállítás
Mielőtt elkezdené, győződjön meg arról, hogy a Service Fabric fejlesztői környezet be van állítva a gépen.
Ha be kell állítania, ugorjon az [Ismerkedés a Mac gépen](service-fabric-get-started-mac.md) vagy az [első lépések a Linuxon](service-fabric-get-started-linux.md)című lépésre.

## <a name="basic-concepts"></a>Alapfogalmak
A Reliable Services megkezdéséhez csak néhány alapvető fogalmat kell megismernie:

* **Szolgáltatás típusa**: Ez a szolgáltatás implementálása. A nevet a megjelenő osztály határozza meg, amely kiterjeszti, `StatelessService` valamint az ott használt egyéb kódokat vagy függőségeket, valamint a nevét és verziószámát.
* **Nevesített Service-példány**: a szolgáltatás futtatásához a szolgáltatás típusának elnevezett példányait kell létrehoznia, hasonlóan ahhoz, mint egy adott típusú objektum példányának létrehozásakor. A szolgáltatás példányai valójában az Ön által írt szolgáltatási osztály példányai.
* **Service Host**: a létrehozott elnevezett szolgáltatás-példányokat a gazdagépen belül kell futtatni. A szolgáltatás gazdagépe csak egy folyamat, ahol a szolgáltatás példányai futhatnak.
* **Szolgáltatás regisztrálása**: a regisztráció minden együtt jár. A szolgáltatási típust regisztrálni kell a Service Host Service Fabric futtatókörnyezetében, hogy a Service Fabric példányokat hozzon létre a futtatásához.  

## <a name="create-a-stateless-service"></a>Állapot nélküli szolgáltatás létrehozása
Először hozzon létre egy Service Fabric alkalmazást. A Linux rendszerhez készült Service Fabric SDK tartalmaz egy Yeoman-generátort, amely állapot nélküli szolgáltatással biztosítja az állványzatot egy Service Fabric alkalmazás számára. Először futtassa a következő Yeoman-parancsot:

```bash
$ yo azuresfjava
```

A **megbízható állapot nélküli szolgáltatás** létrehozásához kövesse az utasításokat. Ebben az oktatóanyagban nevezze el a "HelloWorldApplication" alkalmazást és a "HelloWorld" szolgáltatást. Az eredmény a és a címtárakat is tartalmazza `HelloWorldApplication` `HelloWorld` .

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Szolgáltatás regisztrációja
A szolgáltatások típusait regisztrálni kell a Service Fabric futtatókörnyezetben. A szolgáltatás típusa a és a által `ServiceManifest.xml` megvalósított szolgáltatási osztályban van definiálva `StatelessService` . A szolgáltatás regisztrációja a folyamat fő belépési pontján történik. Ebben a példában a folyamat fő belépési pontja a következő `HelloWorldServiceHost.java` :

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>A szolgáltatás implementálása

Nyissa meg a **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService. Java** programot. Ez az osztály határozza meg a szolgáltatás típusát, és futtathat bármilyen kódot. A Service API két belépési pontot biztosít a kódhoz:

* Egy nyílt végű belépési pont metódus, amelynek a neve `runAsync()` , ahol megkezdheti a munkaterhelések végrehajtását, beleértve a hosszú ideig futó számítási munkaterheléseket is.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Egy kommunikációs belépési pont, ahol lehetőség van a kommunikációs verembe való csatlakoztatásra. Itt kezdheti meg a felhasználóktól és más szolgáltatásokból érkező kérések fogadását.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Ez az oktatóanyag a `runAsync()` belépési pont metódusára koncentrál. Itt azonnal megkezdheti a kód futtatását.

### <a name="runasync"></a>RunAsync
A platform meghívja ezt a módszert, ha a szolgáltatás egy példánya el van helyezve, és készen áll a végrehajtásra. Állapot nélküli szolgáltatás esetén ez azt jelenti, hogy a szolgáltatás példányának megnyitásakor. A rendszer lemondási tokent biztosít annak koordinálására, hogy mikor kell lezárni a szolgáltatási példányt. Service Fabric a szolgáltatási példányok nyitott/záró ciklusa többször is megjelenhet a szolgáltatás teljes élettartama során. Ez különböző okok miatt fordulhat elő, többek között:

* A rendszer áthelyezi a szolgáltatási példányokat az erőforrás-kiegyensúlyozáshoz.
* Hibák fordulnak elő a kódban.
* Az alkalmazás vagy a rendszer frissítve van.
* A mögöttes hardver kiesést tapasztal.

Ezt a koordinálást a Service Fabric felügyeli, hogy a szolgáltatás legyen elérhető és megfelelően kiegyensúlyozott.

`runAsync()` ne tiltsa le a szinkron módon. A runAsync implementációjának CompletableFuture kell visszaadnia, hogy a futtatókörnyezet továbbra is folytatódjon. Ha a munkaterhelésnek egy hosszú ideig futó feladatot kell megvalósítani, amelyet a CompletableFuture belül kell végrehajtani.

#### <a name="cancellation"></a>Lemondás
A számítási feladatok megszakítása a megadott lemondási token által koordinált együttműködési erőfeszítés. A rendszer megvárja a feladat befejezését (sikeres befejezéssel, megszakítással vagy hibával), mielőtt továbblép. Fontos, hogy tiszteletben tartsák a lemondási jogkivonatot, fejezze be a munkát, és `runAsync()` a lehető leggyorsabban lépjen ki, amikor a rendszer a lemondást kéri. Az alábbi példa bemutatja, hogyan kezelheti a lemondási eseményt:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

Ebben az állapot nélküli szolgáltatási példában a Count egy helyi változóban van tárolva. Azonban mivel ez egy állapot nélküli szolgáltatás, a tárolt érték csak a szolgáltatási példányának aktuális életciklusában létezik. A szolgáltatás áthelyezése vagy újraindítása után az érték elvész.

## <a name="create-a-stateful-service"></a>Állapot-nyilvántartó szolgáltatás létrehozása
Service Fabric bevezet egy új, állapot-nyilvántartó jellegű szolgáltatást. Egy állapot-nyilvántartó szolgáltatás a szolgáltatáson belül megbízhatóan kezelheti az állapotot, és az azt használó kóddal együtt. Az állapotot Service Fabric a rendszer, anélkül, hogy az állapotot külső tárolóra kell megőrizni.

Ha a számláló értékét állapot nélkül szeretné átalakítani a nagyfokú rendelkezésre állásra és a tartósra, még akkor is, ha a szolgáltatás áthelyezi vagy újraindul, szüksége van egy állapot-nyilvántartó szolgáltatásra.

A HelloWorld-alkalmazással megegyező könyvtárban új szolgáltatást is hozzáadhat a parancs futtatásával `yo azuresfjava:AddService` . Válassza ki a "megbízható állapot-nyilvántartó szolgáltatást" a keretrendszer számára, és nevezze el a "HelloWorldStateful" szolgáltatást. 

Az alkalmazásnak most két szolgáltatással kell rendelkeznie: az állapot nélküli szolgáltatás HelloWorld és az állapot-nyilvántartó szolgáltatás HelloWorldStateful.

Az állapot-nyilvántartó szolgáltatás ugyanolyan belépési pontokkal rendelkezik, mint az állapot nélküli szolgáltatás. A fő különbség egy olyan szolgáltató elérhetősége, amely megbízhatóan képes tárolni az állapotot. Service Fabric tartalmaz egy megbízható gyűjtemények nevű állami szolgáltatói implementációt, amely lehetővé teszi a replikált adatstruktúrák létrehozását a megbízható állami kezelőn keresztül. Az állapot-nyilvántartó megbízható szolgáltatás alapértelmezés szerint ezt az állapotot használja.

Nyissa meg a HelloWorldStateful. Java-t a **HelloWorldStateful-> src-** ben, amely a következő RunAsync módszert tartalmazza:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` hasonló állapotú és állapot nélküli szolgáltatásokban működik. Egy állapot-nyilvántartó szolgáltatásban azonban a platform további feladatokat hajt végre az Ön nevében a végrehajtás előtt `RunAsync()` . Ez a munka magában foglalja annak biztosítását, hogy a megbízható állapot-kezelő és a megbízható gyűjtemények használatra készek legyenek.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Megbízható gyűjtemények és a megbízható State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

A [ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) egy olyan szótári implementáció, amellyel megbízhatóan tárolhatja az állapotot a szolgáltatásban. A Service Fabric és a megbízható HashMaps használatával közvetlenül a szolgáltatásban tárolhatja az adatait anélkül, hogy külső állandó tárolóra lenne szükség. A megbízható HashMaps az adatvédelmet kiválóan elérhetővé teszik. A Service Fabric a szolgáltatás több *replikájának* létrehozásával és kezelésével hajtja végre. Emellett olyan API-t is biztosít, amely ellátja a replikák kezelésének bonyolultságát és az állapotukat.

A megbízható gyűjtemények bármilyen Java-típust tárolhatnak, beleértve az egyéni típusokat is, néhány figyelmeztetéssel:

* A Service Fabric az állapotot a csomópontok közötti *replikálással* , a megbízható HashMap pedig a helyi lemezre tárolja az egyes replikákat. Ez azt jelenti, hogy a megbízható HashMaps tárolt összes elemnek *szerializálható* kell lennie. 
* Az objektumok a magas rendelkezésre állás érdekében replikálódnak, amikor megbízható HashMaps tranzakciókat véglegesít. A megbízható HashMaps tárolt objektumokat a szolgáltatás helyi memóriájában tárolja. Ez azt jelenti, hogy van egy helyi hivatkozása az objektumra.
  
   Fontos, hogy ne módosítsa az objektumok helyi példányait anélkül, hogy frissítési műveletet hajt végre a tranzakcióban található megbízható gyűjteményen. Ennek az az oka, hogy az objektumok helyi példányainak módosításait a rendszer nem replikálja automatikusan. Újra be kell szúrni az objektumot a szótárba, vagy a *frissítési* módszerek egyikét kell használnia a szótárban.

A megbízható State Manager megbízható HashMaps kezel. A megbízható állapot-kezelőt megkérheti, hogy név szerint bármikor és a szolgáltatás bármely pontjára megkérdezzen egy megbízható gyűjteményt. A megbízható State Manager gondoskodik arról, hogy a hivatkozás visszakerüljön. Nem javasoljuk, hogy mentse a megbízható gyűjteményi példányokra mutató hivatkozásokat az osztály tag változói vagy tulajdonságai között. Különös figyelmet kell fordítani annak biztosítására, hogy a hivatkozás a szolgáltatás életciklusa során mindig egy példányra legyen beállítva. A megbízható állapot-kezelő kezeli ezt a munkát, és ismétlődő látogatásokra van optimalizálva.


### <a name="transactional-and-asynchronous-operations"></a>Tranzakciós és aszinkron műveletek
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

A megbízható HashMaps végzett műveletek aszinkronak. Ennek az az oka, hogy az írási műveletek megbízható gyűjteményekkel végeznek I/O-műveleteket a lemezre való replikáláshoz és az adatok megőrzéséhez.

A megbízható HashMap műveletek *tranzakciós* adatok, így az állapot konzisztens marad több megbízható HashMaps és művelet között. Előfordulhat például, hogy egy megbízható szótárból munkaelemet kap, végrehajt egy műveletet, és egy másik megbízható HashMap menti az eredményt egy tranzakción belül. Ezt atomi műveletként kezeli a rendszer, és garantálja, hogy a teljes művelet sikeres lesz, vagy a teljes művelet visszaállítja a műveletet. Ha hiba lép fel az elem kivonása után, de az eredmény mentése előtt, a teljes tranzakció vissza lesz állítva, és az elem a várólistán marad a feldolgozáshoz.


## <a name="build-the-application"></a>Az alkalmazás létrehozása

A Yeoman állványzat tartalmaz egy gradle-szkriptet az alkalmazás és a bash-parancsfájlok létrehozásához és az alkalmazás eltávolításához. Az alkalmazás futtatásához először hozza létre az alkalmazást a gradle:

```bash
$ gradle
```

Ez létrehoz egy Service Fabric alkalmazáscsomag, amely Service Fabric CLI használatával telepíthető.

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

Az alkalmazást a létrehozása után telepítheti a helyi fürtben.

1. Csatlakozzon a helyi Service Fabric-fürthöz.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Futtassa a sablonban megadott telepítési szkriptet az alkalmazáscsomagnak a fürt lemezképtárolójába való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

    ```bash
    ./install.sh
    ```

A kész alkalmazás a többi Service Fabric-alkalmazással azonos módon telepíthető. Részletesebb útmutatást talál a [Service Fabric-alkalmazás kezelése a Service Fabric parancssori felülettel](service-fabric-application-lifecycle-sfctl.md) című dokumentációban.

Ezen parancsok paraméterezése megtalálható az alkalmazáscsomagon belül, a generált jegyzékfájlokban.

Az alkalmazás telepítése után nyisson meg egy böngészőt, és keresse fel a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)t a `http://localhost:19080/Explorer` URL-címen. Bontsa ki az **Alkalmazások** csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés az alkalmazás típusához, és egy másik a típus első példányához.

> [!IMPORTANT]
> Ha az alkalmazást egy biztonságos Linux-fürtön szeretné üzembe helyezni az Azure-ban, konfigurálnia kell egy tanúsítványt, hogy érvényesítse az alkalmazást a Service Fabric futtatókörnyezettel. Ezzel lehetővé teszi, hogy a Reliable Services szolgáltatásai kommunikáljanak a mögöttes Service Fabric Runtime API-kkal. További információ: [Reliable Services alkalmazás konfigurálása Linux-fürtökön való futtatásra](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Következő lépések

* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)

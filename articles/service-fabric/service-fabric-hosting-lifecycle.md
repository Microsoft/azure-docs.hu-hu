---
title: Az Azure Service Fabric az aktiválási és inaktiválási életciklust üzemelteti
description: Ismerje meg az alkalmazások életciklusát és a csomóponton lévő szervizcsomagot.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831822"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Azure Service Fabric üzemeltetési életciklus

Ez a cikk áttekintést nyújt az Azure Service Fabricban előforduló eseményekről, amikor egy alkalmazás aktiválva van egy csomóponton. A viselkedést befolyásoló különböző fürtkonfiguráció.

Mielőtt továbblépne, tekintse át az [alkalmazás modellben Service Fabricban][a1]ismertetett fogalmakat és kapcsolatokat. 

> [!NOTE]
> Ez a cikk speciális módszerekkel kapcsolatos terminológiát használ. Hacsak másként nincs jelezve:
>
> - A *replika* egy állapot-nyilvántartó szolgáltatás replikáját és egy állapot nélküli szolgáltatás egy példányát jelenti.
> - A *CodePackage* egy ServiceHost-folyamatnak megfelelőként kezeli a rendszer, amely regisztrálja a ServiceType. Az adott ServiceType tartozó szolgáltatások replikáit tárolja.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>ApplicationPackage vagy szervizcsomag aktiválása

ApplicationPackage vagy szervizcsomag aktiválása:

1. Töltse le a ApplicationPackage (például *ApplicationManifest.xml*).
2. Környezet beállítása az alkalmazáshoz. A lépések közé tartozik például a felhasználók létrehozása.
3. Az alkalmazás az inaktiváláshoz való nyomon követésének megkezdése.
4. Töltse le a szervizcsomagot (például *ServiceManifest.xml*, kód, konfigurációs fájlok és adatcsomagok).
5. Környezet beállítása a szervizcsomaghoz. A lépések közé tartozik például a tűzfal beállítása és portok lefoglalása a végpontokhoz.
6. Az inaktiváláshoz szükséges szervizcsomagok nyomon követésének megkezdése.
7. Indítsa el a CodePackages SetupEntryPoint, és várjon, amíg befejeződik.
8. Indítsa el a CodePackages MainEntryPoint.

### <a name="servicetype-blocklisting"></a>ServiceType blocklisting
`ServiceTypeDisableFailureThreshold` meghatározza a megengedett aktiválási, letöltési és CodePackage hibák számát. A küszöbérték elérésekor a ServiceType a blocklisting ütemezve van. Az első aktiválási hiba, a letöltési hiba vagy a CodePackage összeomlása a ServiceType blocklisting ütemezhet. 

A `ServiceTypeDisableGraceInterval` konfiguráció meghatározza a türelmi intervallumot, mielőtt a ServiceType blocklisted a csomóponton. Ahogy a folyamat lejátssza, az aktiválás, a letöltés és a CodePackage újraindítása párhuzamosan próbálkozik. Az újrapróbálkozás például azt jelenti, hogy a CodePackage az összeomlás után újraindul, vagy a Service Fabric megkísérli a csomagok újbóli letöltését.

Ha a ServiceType blocklisted, a következő állapot jelenik meg: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

A ServiceType engedélyezve van a csomóponton, ha a következő események bármelyike előfordul:
- Az aktiválás sikeres. Vagy ha ez nem sikerül, az `ActivationMaxFailureCount` újrapróbálkozások maximális száma eléri.
- A letöltés sikeres. Vagy ha ez nem sikerül, az `DeploymentMaxFailureCount` újrapróbálkozások maximális száma eléri.
- Az összeomlást okozó CodePackage elindul, és sikeresen regisztrálja a ServiceType.

`ActivationMaxFailureCount``DeploymentMaxFailureCount`a kísérletek maximális száma Service Fabric a csomóponton lévő alkalmazások aktiválását vagy letöltését teszi elérhetővé. A maximális érték elérésekor a Service Fabric engedélyezi a ServiceType aktiválását. 

Ezek a küszöbértékek lehetővé teszi, hogy a szolgáltatás egy másik lehetőséget biztosítson az aktiválásra. Ha az aktiválás sikeres, a rendszer automatikusan meggyógyítja a problémát. 

Egy újonnan elhelyezett vagy aktivált replika új aktiválási vagy letöltési műveletet indíthat el. Ez a művelet vagy sikeres lesz, vagy elindítja a ServiceType blocklisting.

> [!NOTE]
> A ServiceType nem regisztráló összeomlási CodePackage nem befolyásolja a ServiceType. Csak a replikát tároló összeomlik CodePackage befolyásolja a ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage összeomlása
Ha egy CodePackage összeomlik, Service Fabric egy leállítási használatával indítja el újra. A rendszer attól függetlenül alkalmazza a leállítási, hogy a CodePackage regisztrálva van-e a Service Fabric futtatókörnyezettel.

A leállítási értéke `Min(RetryTime, ActivationMaxRetryInterval)` . Az érték a következő konfigurációs beállítás alapján állandó, lineáris vagy exponenciális értékek szerint növekszik `ActivationRetryBackoffExponentiationBase` :

- **Állandó**: Ha `ActivationRetryBackoffExponentiationBase == 0` , akkor `RetryTime = ActivationRetryBackoffInterval` .
- **Lineáris**: Ha  `ActivationRetryBackoffExponentiationBase == 0` , akkor `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` , ahol a `ContinuousFailureCount` CodePackage-összeomlások vagy sikertelen aktiválások száma.
- **Exponenciális**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
A viselkedést az értékek módosításával szabályozhatja. Ha például több gyors újraindítási kísérletet szeretne, a lineáris értékeket a értékre való beállítással és a értékre való `ActivationRetryBackoffExponentiationBase` `0` beállítással használhatja `ActivationRetryBackoffInterval` `10` . Tehát ha egy CodePackage összeomlik, a kezdési időköz 10 másodperc után fog megjelenni. Ha a csomag továbbra is összeomlik, a leállítási 20 másodperc, 30 másodperc, 40 másodperc és így tovább változik, egészen addig, amíg a CodePackage-aktiválás sikeres vagy a CodePackage inaktiválva van. 
    
A Service Fabric biztonsági mentésének maximális ideje (azaz vár), miután a hibát a következő szabályozza: `ActivationMaxRetryInterval` .
    
Ha a CodePackage összeomlik, és biztonsági másolatot készít, a által megadott időszakra kell maradnia `CodePackageContinuousExitFailureResetInterval` . Az intervallum után Service Fabric a CodePackage kifogástalannak tekinti. Service Fabric ezután felülírja az előző hiba állapotára vonatkozó jelentést az OK gombra, és visszaállítja a következőt: `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>A CodePackage nem regisztrálja a ServiceType
Ha egy CodePackage marad, és a rendszer egy ServiceType regisztrál, de nem, a Service Fabric figyelmeztetési állapotra vonatkozó jelentést generál a után `ServiceTypeRegistrationTimeout` . A jelentés azt jelzi, hogy a ServiceType nincs regisztrálva a várt időtartamon belül.

### <a name="activation-failure"></a>Aktiválási hiba
Ha a Service Fabric hibát észlel az aktiválás során, mindig lineáris leállítási használ, mivel a CodePackage összeomlik. Az aktiválási művelet az újrapróbálkozások után az alábbi időközönként ad lehetőséget: (0 + 10 + 20 + 30 + 40) = 100 másodperc. (Az első újrapróbálkozás azonnali.) A folyamat után az aktiválás nem próbálkozik újra.
    
A maximális aktiválási leállítási lehet `ActivationMaxRetryInterval` . Az újrapróbálkozás lehet `ActivationMaxFailureCount` .

### <a name="download-failure"></a>Letöltési hiba
Service Fabric mindig lineáris leállítási használ, ha hibát talál a letöltés során. Az aktiválási művelet az újrapróbálkozások után az alábbi időközönként ad lehetőséget: (0 + 10 + 20 + 30 + 40) = 100 másodperc. (Az első újrapróbálkozás azonnali.) A folyamat után a letöltés nem próbálkozik újra. 

A letöltés lineáris leállítási egyenlő a következővel: `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . A maximális leállítási lehet `DeploymentMaxRetryInterval` . Az aktiválásokhoz hasonlóan a letöltési műveletek is újra megpróbálkoznak a `ActivationMaxFailureCount` korláttal.

> [!NOTE]
> Mielőtt módosítaná ezeket a beállításokat, vegye figyelembe az alábbi példákat:
>
>* Ha a CodePackage összeomlik és biztonsági mentést végez, a ServiceType le lesz tiltva. Ha azonban az aktiválási konfiguráció gyors újraindítást tartalmaz, akkor a CodePackage néhány alkalommal elérheti, mielőtt a ServiceType ténylegesen blocklisted. 
>
>    Tegyük fel például, hogy a CodePackage bekövetkezik, regisztrálja a ServiceType Service Fabric, majd összeomlik. Ebben az esetben az üzemeltetési típus regisztrálását követően a `ServiceTypeDisableGraceInterval` rendszer megszakítja az időszakot. Ez a folyamat csak akkor ismételhető, ha a CodePackage az adott időszaknál nagyobb értékre áll vissza `ServiceTypeDisableGraceInterval` . Ezt követően a rendszer a ServiceType blocklisted a csomóponton. A ServiceType blocklisting a vártnál több időt vehet igénybe.
>
>* Aktiválások esetén, amikor egy Service Fabric rendszernek replikát kell elhelyeznie egy csomóponton, az újrakonfigurálási ügynök az alkalmazás aktiválására kéri az üzemeltetési alrendszert. 15 másodpercenként újrapróbálkozik az aktiválási kéréssel. (Az időtartamot a `RAPMessageRetryInterval` konfigurációs beállítás szabályozza.) Service Fabric nem tudja, hogy a ServiceType meg lett blocklisted, kivéve, ha a gazdagépen az aktiválási művelet hosszabb ideig tart, mint az újrapróbálkozási időköz és a `ServiceTypeDisableGraceInterval` . 
>
>    Tegyük fel például, hogy a fürt értéke 5, a értéke pedig `ActivationMaxFailureCount` `ActivationRetryBackoffInterval` 1 másodperc. Ebben az esetben az aktiválási művelet a (0 + 1 + 2 + 3 + 4) = 10 másodperces intervallumok után adja meg a műveletet. (Az első újrapróbálkozás azonnali.) Ha ezt a sorozatot futtatja, a tárhely újrapróbálkozik. Az aktiválási művelet befejeződik, és 15 másodperc elteltével nem próbálkozik újra. 
>
>    A Service Fabric 15 másodpercen belül kimerítette az összes engedélyezett újrapróbálkozást. Így az újrakonfigurálási ügynök minden újrapróbálkozása egy új aktiválási műveletet hoz létre az üzemeltetési alrendszerben, és a minta megismétli. Ennek eredményeképpen a ServiceType soha nem blocklisted a csomóponton. Mivel a ServiceType nem lesz blocklisted a csomóponton, a replika nem helyezhető át és nem próbálkozik egy másik csomóponton.
> 

## <a name="deactivation"></a>Inaktiválása

Ha egy csomóponton aktiválják a szervizcsomagot, az Inaktiválás nyomon követhető. Az Inaktiválás két módon működik:

- **Rendszeres inaktiválás**: `DeactivationScanInterval` a rendszer minden esetben ellenőrzi, hogy a rendszer *nem* adott-e meg replikát, és az inaktiválásra jelöltként jelöli meg őket.
- **ReplicaClose inaktiválása**: Ha egy replika be van zárva, a deaktiváló egy `DecrementUsageCount` . A darabszám 0, ha a szervizcsomag nem tartalmaz replikát, így a szervizcsomag az inaktiválásra jelölt.

Az aktiválási mód határozza meg, hogy mikor legyen ütemezve a jelöltek inaktiválásra. Megosztott módban az inaktiválásra jelölt pályázók a következő után lesznek ütemezve `DeactivationGraceInterval` . Exkluzív módban a rendszer a után ütemezi őket `ExclusiveModeDeactivationGraceInterval` . Ha az ilyen időpontok között új replika érkezik, a rendszer megszakítja az inaktiválást. 

További információ: [exkluzív mód és megosztott mód][a2].

### <a name="periodic-deactivation"></a>Rendszeres inaktiválás
Íme néhány példa az időszakos inaktiválásra:

* **1. példa**: tegyük fel, hogy a Deactivator elindít egy vizsgálatot a T időpontban (a `DeactivationScanInterval` ). A következő vizsgálat a következő időpontban fog megjelenni: 2T. Tegyük fel, hogy a szervizcsomag aktiválása a T + 1 időpontban történt. Ez a szervizcsomag nem üzemeltet replikát, ezért inaktiválva kell lennie. 

    Ha az inaktiválásra jelöltnek kell lennie, a szervizcsomagnak legalább T-nál nem kell replikát üzemeltetni. Az inaktiválásra jogosult a következő helyen: 2T + 1. Így a 2T-vizsgálat nem azonosítja ezt a szervizcsomagot inaktiválásra jelöltként. 

    A következő inaktiválási ciklusban a 3T ezt a szervizcsomagot fogja ütemezni az inaktiváláshoz, mert a csomag már nem replika állapotban van a (T) időpontra vonatkozóan.  

* **2. példa**: tegyük fel, hogy egy szervizcsomag a t-1 időpontban aktiválódik, és a deélénkítő elindít egy vizsgálatot a t-ben. A szervizcsomag nem üzemeltet replikát. A következő vizsgálatban a 2T a rendszer az inaktiválásra jelöltként azonosítja a szervizcsomagot, így az inaktiválásra lesz ütemezve.  

* **3. példa**: tegyük fel, hogy egy szervizcsomag a t-1 időpontban aktiválódik, és a deaktiváló elindít egy vizsgálatot a t-ben. A szervizcsomag még nem üzemeltet replikát. Most a T + 1 helyen egy replika kerül elhelyezésre. Ez azt jelenti, hogy az üzemeltetéshez egy `IncrementUsageCount` replika jön létre. 

    A 2T esetében ez a szervizcsomag nem lesz ütemezve inaktiválásra. Mivel a csomag tartalmaz egy replikát, az Inaktiválás a ReplicaClose logikát követi, ahogy a cikk következő szakasza is ismerteti.

* **4. példa**: tegyük fel, hogy a SZERVIZCSOMAG 10 GB. Mivel a csomag nagyméretű, időbe telik a letöltés a csomóponton. Ha egy alkalmazás aktiválva van, a deélénkítő nyomon követi a életciklusát. Ha a értéke `DeactivationScanInterval` kis értékre van állítva, előfordulhat, hogy a szervizcsomag nem rendelkezik a letöltendő idő miatti aktiválással a csomóponton. A probléma megoldásához [töltse le a szervizcsomagot előre a csomópontra][p1] , vagy növelje a következőt: `DeactivationScanInterval` . 

> [!NOTE]
> A szervizcsomagok bárhol inaktiválva lehetnek ( `DeactivationScanInterval` – 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose inaktiválása

> [!NOTE]
> Ez a szakasz a következő konfigurációs beállításokra hivatkozik:
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: az az idő, amelyet a szervizcsomag egy másik replika üzemeltetésére adott, ha már futtatott egy replikát. 
> - **DeactivationScanInterval**: egy olyan replika üzemeltetéséhez szükséges minimális idő, amely egy replikát *üzemeltet, azaz ha még nem* használta a replikát.
>

A rendszer megtartja a szervizcsomagok által birtokolt replikák számát. Ha a szervizcsomag egy replikát tárol, és a replika le van zárva vagy leállt, a rendszer a gazdagépet kapja `DecrementUsageCount` . Egy replika megnyitásakor a rendszer a gazdagépet kapja `IncrementUsageCount` . 

Az érték csökkentése azt jelzi, hogy a szervizcsomag által üzemeltetett replikák száma egy replikával csökkent. Ha a szám 0-ra csökken, a szervizcsomag inaktiválásra van ütemezve. Az az idő, amely után a rendszer inaktiválja a következőt: `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Tegyük fel például, hogy a csökkenés a T értéknél történik, és a szervizcsomag a 2T + X () időpontban inaktiválásra van ütemezve `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . Ez idő alatt `IncrementUsage` a rendszer megszakítja az inaktiválást, ha a gazdagép egy replikát hoz létre.

### <a name="ctrl--c"></a>Ctrl + C
Ha a szervizcsomag átadja a `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` -t, és továbbra sem replikát üzemeltet, az inaktiválást nem lehet megszakítani. A CodePackages egy CTRL + C kezelőt kap. Így most az inaktiválási folyamatnak be kell fejeznie a folyamatot. 

Ebben az időszakban, ha az azonos szervizcsomaggal rendelkező új replikát próbál meg elhelyezni, a művelet sikertelen lesz, mert a folyamat nem tud inaktiválásról aktiválásra áttérni.

## <a name="cluster-configurations"></a>Fürtkonfigurációk

Ez a szakasz felsorolja azokat a konfigurációkat, amelyek az aktiválást és az inaktiválást befolyásoló alapértelmezett beállításokkal rendelkeznek.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: default: 1. A hibák számának küszöbértéke; Ha elérte ezt a küszöbértéket, a FailoverManager értesíti a szolgáltatás típusát a csomóponton, és egy másik csomópontot próbál meg elhelyezésre.
- **ServiceTypeDisableGraceInterval**: alapértelmezett: 30 másodperc. Az az időtartam, amely után a szolgáltatástípus le lehet tiltani.
- **ServiceTypeRegistrationTimeout**: Default: 300 másodperc. A ServiceType Service Fabric való regisztrálásának időtúllépése.

### <a name="activation"></a>Aktiválás
- **ActivationRetryBackoffInterval**: alapértelmezett: 10 másodperc. Leállítási időköz minden aktiválási hiba esetén.
- **ActivationMaxFailureCount**: alapértelmezett: 20. A maximális darabszám, amely után a rendszer újrapróbálkozik a sikertelen aktiválás előtt. 
- **ActivationRetryBackoffExponentiationBase**: Default: 1,5.
- **ActivationMaxRetryInterval**: Default: 3 600 másodperc. Az aktiválás maximális leállítási újrapróbálkozási időköze a hibák után.
- **CodePackageContinuousExitFailureResetInterval**: Default: 300 másodperc. Időtúllépési időköz a CodePackage folyamatos kilépési hibáinak számának alaphelyzetbe állításához.

### <a name="download"></a>Letöltés
- **DeploymentRetryBackoffInterval**: default: 10. Az üzembe helyezési hiba leállítási időköze.
- **DeploymentMaxRetryInterval**: Default: 3 600 másodperc. A központi telepítés maximális leállítási időköze a hibák után.
- **DeploymentMaxFailureCount**: alapértelmezett: 20. Az alkalmazás központi telepítése újra próbálkozik az `DeploymentMaxFailureCount` adott alkalmazásnak a csomóponton való üzembe helyezésének sikertelensége előtt.

### <a name="deactivation"></a>Inaktiválása
- **DeactivationScanInterval**: Default: 600 másodperc. A szervizcsomag tárolására szolgáló minimális idő, ha még soha nem üzemeltetett replikát (azaz ha nincs használatban).
- **DeactivationGraceInterval**: Default: 60 másodperc. Egy *megosztott* négyszínes modellben a szervizcsomaghoz adott idő, amely egy újabb replikát futtat, miután már üzemeltetett replikát.
- **ExclusiveModeDeactivationGraceInterval**: alapértelmezett: 1 másodperc. Egy *kizárólagos* folyamatmodell esetében a szervizcsomaghoz adott idő egy újabb replika futtatására szolgál, miután már futtatta a replikát.

## <a name="next-steps"></a>Következő lépések

- [Alkalmazás becsomagolása][a3] és üzembe helyezése készen áll a telepítésre.
- [Alkalmazások telepítése és eltávolítása][a4] a PowerShellben.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode

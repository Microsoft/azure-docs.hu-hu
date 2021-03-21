---
title: Az Azure Service Fabric konfigurálása Reliable Services
description: Tudnivalók az állapot-nyilvántartó Reliable Services Azure Service Fabric-alkalmazásokban való konfigurálásáról globálisan és egyetlen szolgáltatáshoz.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.custom: devx-track-csharp
ms.openlocfilehash: cda0a9f988afae58a60bff051885a5eec8afe434
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023584"
---
# <a name="configure-stateful-reliable-services"></a>Állapot-nyilvántartó megbízható szolgáltatások konfigurálása
A megbízható szolgáltatások két konfigurációs beállítással rendelkeznek. Az egyik készlet globális a fürt összes megbízható szolgáltatásához, míg a másik készlet egy adott megbízható szolgáltatásra jellemző.

## <a name="global-configuration"></a>Globális konfiguráció
A globális megbízható szolgáltatás konfigurációja a fürt jegyzékfájljában van megadva a KtlLogger szakaszban. Lehetővé teszi a megosztott napló helyének és méretének, valamint a naplózó által használt globális memória-korlátok konfigurálását. A fürt jegyzékfájlja egyetlen XML-fájl, amely a fürt összes csomópontjára és szolgáltatására vonatkozó beállításokat és konfigurációkat tartalmazza. A fájl neve általában ClusterManifest.xml. A fürthöz tartozó jegyzékfájlt a Get-ServiceFabricClusterManifest PowerShell-paranccsal tekintheti meg.

### <a name="configuration-names"></a>Konfigurációs nevek
| Name | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobájtban |8388608 |A naplózó írási puffer memória-készletéhez tartozó kernel módban foglalható KB-os minimális szám. Ez a memória-készlet az állapotadatok gyorsítótárazásához használatos a lemezre írás előtt. |
| WriteBufferMemoryPoolMaximumInKB |Kilobájtban |Korlátlan |Az a maximális méret, ameddig a naplózó írási puffer memória-készlete növekedni tud. |
| SharedLogId |GUID |"" |Meghatározza a fürtben lévő összes megbízható szolgáltatás által használt alapértelmezett megosztott naplófájl azonosítására szolgáló egyedi GUID azonosítót, amely nem adja meg a SharedLogId a szolgáltatási specifikus konfigurációban. Ha a SharedLogId meg van adva, akkor a SharedLogPath is meg kell adni. |
| SharedLogPath |Teljes elérési út neve |"" |Megadja a teljes elérési utat, ahol a fürt minden olyan csomópontján lévő megbízható szolgáltatás által használt megosztott naplófájl, amely nem adja meg a SharedLogPath a szolgáltatási specifikus konfigurációban. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId is meg kell adni. |
| SharedLogSizeInMB |Megabájtban |8192 |Megadja, hogy hány MB lemezterületet kell statikusan lefoglalni a megosztott napló számára. Az értéknek 2048 vagy nagyobbnak kell lennie. |

Az Azure ARM-ban vagy a helyszíni JSON-sablonban az alábbi példa azt mutatja be, hogyan változtatható meg a megosztott tranzakciónapló, amelyet a rendszer az állapot-nyilvántartó szolgáltatások megbízható gyűjteményei számára hozott létre.

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="sample-local-developer-cluster-manifest-section"></a>Minta a helyi fejlesztői fürt jegyzékfájljának szakasza
Ha ezt a helyi fejlesztési környezetben szeretné módosítani, szerkesztenie kell a helyi clustermanifest.xml fájlt.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Megjegyzések
A naplózó olyan globális készletet tartalmaz, amely a nem lapozható kernel-memóriából van lefoglalva, amely az állapotadatok gyorsítótárazásához szükséges összes megbízható szolgáltatás számára elérhető a megbízható szolgáltatás másodpéldányához társított dedikált naplóba való írás előtt. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és a WriteBufferMemoryPoolMaximumInKB beállítások vezérlik. A WriteBufferMemoryPoolMinimumInKB megadja a memória-készlet kezdeti méretét, valamint azt a legkisebb méretet, ameddig a memória-készlet csökkenhet. A WriteBufferMemoryPoolMaximumInKB a legnagyobb méret, amellyel a memória-készlet növekedni lehet. Minden megnyitott megbízható szolgáltatás replikája növelheti a memória-készlet méretét a rendszer által meghatározott WriteBufferMemoryPoolMaximumInKB értékkel. Ha a rendelkezésre álló memória több memóriát is igénybe vesz, mint amennyi elérhető, a memóriára vonatkozó kérelmek késleltetve lesznek, amíg a memória elérhetővé válik. Ezért ha az írási pufferbeli memória készlete túl kicsi egy adott konfigurációhoz, akkor a teljesítmény romolhat.

A SharedLogId és a SharedLogPath beállításokat a rendszer mindig együtt használja a fürt összes csomópontjának alapértelmezett megosztott naplójának GUID azonosítójának és helyének definiálásához. Az alapértelmezett megosztott napló minden olyan megbízható szolgáltatáshoz használatos, amely nem határozza meg az adott szolgáltatáshoz tartozó settings.xml beállításait. A legjobb teljesítmény érdekében a megosztott naplófájlokat olyan lemezekre kell helyezni, amelyek kizárólag a megosztott naplófájlhoz használatosak a versengés csökkentése érdekében.

A SharedLogSizeInMB meghatározza, hogy a rendszer mennyi lemezterületet szabadítson fel az alapértelmezett megosztott bejelentkezéshez az összes csomóponton.  A SharedLogId és a SharedLogPath nem kell megadni a SharedLogSizeInMB megadása érdekében.

## <a name="service-specific-configuration"></a>Szolgáltatás-specifikus konfiguráció
Az állapot-nyilvántartó Reliable Services alapértelmezett konfigurációit a konfigurációs csomag (konfiguráció) vagy a szolgáltatás implementálása (kód) használatával módosíthatja.

* **Konfiguráció – a** konfigurációs csomagon keresztüli konfigurálás a Microsoft Visual Studio-csomag gyökerében létrehozott Settings.xml fájl módosításával történik az alkalmazás minden szolgáltatásának konfigurációs mappájában.
* A **programkódon** keresztüli konfigurálást úgy érheti el, hogy létrehoz egy ReliableStateManager egy ReliableStateManagerConfiguration objektum használatával a megfelelő beállításokkal.

Alapértelmezés szerint az Azure Service Fabric Runtime az előre definiált szakaszok nevét keresi a Settings.xml fájlban, és a konfigurációs értékeket használja az alapul szolgáló futtatókörnyezet-összetevők létrehozásakor.

> [!NOTE]
> Ne törölje a Visual Studio-megoldásban létrehozott Settings.xml fájl következő konfigurációjának szakaszait, hacsak **nem** tervezi a szolgáltatás programkódon keresztüli konfigurálását.
> A konfigurációs csomag vagy a szakasz nevének átnevezéséhez meg kell változtatni a ReliableStateManager konfigurálásához szükséges programkódot.
> 
> 

### <a name="replicator-security-configuration"></a>Replikátor biztonsági konfigurációja
A replikátor biztonsági beállításai a replikálás során használt kommunikációs csatorna biztonságossá tételére szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem fogják látni egymás replikációs forgalmát, így biztosítva, hogy a kiemelten elérhető adatok is biztonságosak legyenek. Alapértelmezés szerint a biztonsági konfiguráció üres szakasza megakadályozza a replikálás biztonságát.

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM formátumúnak kell lenniük. Ha többet szeretne megtudni a Linux-tanúsítványok kereséséről és konfigurálásáról, tekintse meg [a tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakört. 
> 
> 

### <a name="default-section-name"></a>Alapértelmezett szakasz neve
ReplicatorSecurityConfig

> [!NOTE]
> A szakasz nevének módosításához felülbírálja a replicatorSecuritySectionName paramétert a ReliableStateManagerConfiguration konstruktorban a szolgáltatáshoz tartozó ReliableStateManager létrehozásakor.
> 
> 

### <a name="replicator-configuration"></a>Replikátor konfigurációja
A replikátor-konfigurációk olyan replikátort állítanak be, amely felelős az állapot-nyilvántartó megbízható szolgáltatás állapotának nagyfokú megbízhatóságához azáltal, hogy helyileg replikálja és megőrzi az állapotot.
Az alapértelmezett konfigurációt a Visual Studio-sablon hozza létre, és ennek elegendőnek kell lennie. Ez a szakasz a replikátor finomhangolásához elérhető további konfigurációkról beszél.

### <a name="default-section-name"></a>Alapértelmezett szakasz neve
ReplicatorConfig

> [!NOTE]
> A szakasz nevének módosításához felülbírálja a replicatorSettingsSectionName paramétert a ReliableStateManagerConfiguration konstruktorban a szolgáltatáshoz tartozó ReliableStateManager létrehozásakor.
> 
> 

### <a name="configuration-names"></a>Konfigurációs nevek
| Name | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Másodperc |0,015 |Az az időszak, ameddig a másodlagos megvárja a műveletet a művelet fogadása után, mielőtt visszaküldi a nyugtát az elsődlegesnek. Az ezen az intervallumon belül feldolgozott műveletekhez küldendő összes más nyugtát egyetlen válaszként kell elküldeni. |
| ReplicatorEndpoint |N/A |Nincs alapértelmezett – kötelező paraméter |Az az IP-cím és port, amelyet az elsődleges/másodlagos replikátor a replikakészlet más replikákkal való kommunikációhoz fog használni. Ennek a szolgáltatás jegyzékfájljában a TCP-erőforrás végpontra kell hivatkoznia. A szolgáltatási jegyzékfájlban található végponti erőforrások definiálásával kapcsolatos további információkért tekintse meg a [szolgáltatás jegyzékfájljának erőforrásai](service-fabric-service-manifest-resources.md) című témakört. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |8192 |Az elsődleges várólistában lévő műveletek maximális száma. Egy művelet akkor szabadítható fel, ha az elsődleges replikátor nyugtát kap az összes másodlagos replikáló közül. Ennek az értéknek nagyobbnak kell lennie, mint 64, és a 2 hatványa. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A műveletek maximális száma a másodlagos várólistában. A művelet a kitartás után az állapotának nagyfokú rendelkezésre állása után szabadítható fel. Ennek az értéknek nagyobbnak kell lennie, mint 64, és a 2 hatványa. |
| CheckpointThresholdInMB |MB |50 |A naplófájl azon területének mennyisége, amely után az állapot ellenőrzőpontra kerül. |
| MaxRecordSizeInKB |KB |1024 |A replikációs rekord legnagyobb mérete, amelyet a replikátor írhat a naplóba. Ennek az értéknek a 4 és 16 közötti többszörösének kell lennie. |
| MinLogSizeInMB |MB |0 (rendszer által meghatározott) |A tranzakciós napló minimális mérete A napló nem engedheti meg a kivágást a beállítás alatti méretre. a 0 érték azt jelzi, hogy a replikátor meg fogja határozni a minimális napló méretét. Ennek az értéknek a növelése növeli a részleges másolatok és a növekményes biztonsági mentések lehetőségét, mivel a rendszer lerövidíti a kapcsolódó naplófájlok számát. |
| TruncationThresholdFactor |Szempont |2 |Meghatározza, hogy a napló milyen méretben fog kiváltani. A csonkítás küszöbértékét a MinLogSizeInMB a TruncationThresholdFactor szorzata határozza meg. A TruncationThresholdFactor nagyobbnak kell lennie, mint 1. A MinLogSizeInMB * TruncationThresholdFactor kisebbnek kell lennie, mint a MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Szempont |4 |Meghatározza, hogy a napló milyen méretben fog megjelenni, a replika pedig szabályozás alatt áll. A szabályozás küszöbértékét (MB-ban) a következő határozza meg: Max ((MinLogSizeInMB * ThrottlingThresholdFactor), (CheckpointThresholdInMB * ThrottlingThresholdFactor)). A szabályozási küszöbértéknek (MB) nagyobbnak kell lennie, mint a csonkítás küszöbértéke (MB). A csonkítás küszöbértékének (MB) kisebbnek kell lennie, mint MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Egy adott biztonsági mentési naplóban található biztonsági mentési naplók maximális összesített mérete (MB). A növekményes biztonsági mentési kérelmek sikertelenek lesznek, ha a növekményes biztonsági mentés olyan biztonsági mentési naplót fog eredményezni, amely a felhalmozott biztonsági mentési naplókat okozta, mivel a megfelelő teljes biztonsági mentés nagyobb ennél a méretnél. Ilyen esetekben a felhasználónak teljes biztonsági mentést kell készítenie. |
| SharedLogId |GUID |"" |Meghatározza a replikával használt megosztott naplófájl azonosításához használandó egyedi GUID azonosítót. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogId meg van adva, akkor a SharedLogPath is meg kell adni. |
| SharedLogPath |Teljes elérési út neve |"" |Meghatározza azt a teljes elérési utat, ahol a replika megosztott naplófájlja létrejön. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId is meg kell adni. |
| SlowApiMonitoringDuration |Másodperc |300 |Beállítja a felügyelt API-hívások figyelési intervallumát. Példa: a felhasználó által megadott biztonsági mentési visszahívás funkció. Az intervallum letelte után a rendszer figyelmeztetési állapotjelentést küld a Health Managernek. |
| LogTruncationIntervalSeconds |Másodperc |0 |Konfigurálható időköz, amelyen a naplózási csonkítás minden replikán megkezdődik. A naplófájlok a naplózási méret helyett az idő függvényében is használhatók. Ez a beállítás kényszeríti a törölt bejegyzések törlését is a megbízható szótárban. Ezért használható a törölt elemek időben történő törlésének biztosítására. |
| EnableStableReads |Logikai |Hamis |A stabil olvasások engedélyezése korlátozza a másodlagos replikákat, hogy olyan értékeket adjanak vissza, amelyek kvórum-nyugtázva. |

### <a name="sample-configuration-via-code"></a>Minta konfiguráció kód használatával
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Minta konfigurációs fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Megjegyzések
A BatchAcknowledgementInterval a replikációs késést vezérli. A (z) "0" érték a lehető legalacsonyabb késést eredményezi az átviteli sebességnél (mivel több nyugtázási üzenetet kell elküldeni és feldolgozni, ami kevesebb nyugtát tartalmaz).
Minél nagyobb a BatchAcknowledgementInterval, annál nagyobb a replikálás teljes átviteli sebessége, a magasabb működési késés díja. Ez közvetlenül a tranzakciós véglegesítés késésével van lefordítva.

A CheckpointThresholdInMB értéke szabályozza azt a lemezterületet, amelyet a replikátor az állapotadatok tárolására használhat a replika dedikált naplófájljában. Ha az alapértelmezettnél nagyobb értéket ad meg, akkor gyorsabb újrakonfigurálási időt eredményezhet, amikor új replikát adnak hozzá a készlethez. Ennek oka a részleges állapot-átvitel, amely a naplóban lévő műveletek több előzményének rendelkezésre állása miatt zajlik. Ez egy összeomlás után is növelheti a replika helyreállítási idejét.

A MaxRecordSizeInKB beállítás határozza meg egy olyan rekord maximális méretét, amelyet a replikátor a naplófájlba tud írni. A legtöbb esetben az alapértelmezett 1024-KB-os rekord mérete optimális. Ha azonban a szolgáltatás nagyobb adatelemeket okoz az állapotadatok részévé tételéhez, akkor előfordulhat, hogy ezt az értéket növelni kell. A MaxRecordSizeInKB kisebb, mint 1024, mivel a kisebb rekordok csak a kisebb rekordokhoz szükséges területet használják. Várható, hogy ezt az értéket csak ritka esetekben kell módosítani.

A SharedLogId és a SharedLogPath beállításait mindig együtt használja a rendszer, hogy a szolgáltatás a csomópont alapértelmezett megosztott naplójából külön megosztott naplót használjon. A legjobb hatékonyság érdekében a lehető legtöbb szolgáltatásnak ugyanazt a megosztott naplót kell megadnia. A megosztott naplófájlokat a kizárólag a megosztott naplófájlhoz használt lemezekre kell helyezni a fej mozgásának csökkentése érdekében. Várható, hogy ezt az értéket csak ritka esetekben kell módosítani.

## <a name="next-steps"></a>Következő lépések
* [A Service Fabric-alkalmazás hibakeresése a Visual Studióban](service-fabric-debugging-your-application.md)
* [Reliable Services fejlesztői referenciája](/previous-versions/azure/dn706529(v=azure.100))

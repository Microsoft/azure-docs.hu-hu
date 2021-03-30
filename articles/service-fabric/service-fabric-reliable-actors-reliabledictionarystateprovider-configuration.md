---
title: ReliableDictionaryActorStateProvider beállításainak módosítása
description: Ismerje meg, hogyan konfigurálhatja az Azure-Service Fabric állapot-nyilvántartó ReliableDictionaryActorStateProvider típust.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609740"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Reliable Actors konfigurálása – ReliableDictionaryActorStateProvider
A ReliableDictionaryActorStateProvider alapértelmezett konfigurációját módosíthatja úgy, hogy módosítja a Visual Studio-csomag gyökerében létrehozott settings.xml fájlt a megadott szereplő konfigurációs mappájában.

Az Azure Service Fabric Runtime a settings.xml fájlban előre definiált szakaszokat keres, és a konfigurációs értékeket a mögöttes futásidejű összetevők létrehozásakor használja fel.

> [!NOTE]
> A Visual Studio-megoldásban létrehozott settings.xml fájlban **ne** törölje vagy módosítsa a következő konfigurációk szakaszának nevét.
> 
> 

Vannak olyan globális beállítások is, amelyek befolyásolják a ReliableDictionaryActorStateProvider konfigurációját.

## <a name="global-configuration"></a>Globális konfiguráció
A globális konfiguráció a fürt jegyzékfájljában van megadva a fürt KtlLogger szakaszában. Lehetővé teszi a megosztott napló helyének és méretének, valamint a naplózó által használt globális memória-korlátok konfigurálását. Vegye figyelembe, hogy a ReliableDictionaryActorStateProvider és a megbízható állapot-nyilvántartó szolgáltatásokat használó összes szolgáltatásra hatással van a fürt jegyzékének változása.

A fürt jegyzékfájlja egyetlen XML-fájl, amely a fürt összes csomópontjára és szolgáltatására vonatkozó beállításokat és konfigurációkat tartalmazza. A fájl neve általában ClusterManifest.xml. A fürthöz tartozó jegyzékfájlt a Get-ServiceFabricClusterManifest PowerShell-paranccsal tekintheti meg.

### <a name="configuration-names"></a>Konfigurációs nevek
| Name | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobájtban |8388608 |A naplózó írási puffer memória-készletéhez tartozó kernel módban foglalható KB-os minimális szám. Ez a memória-készlet az állapotadatok gyorsítótárazásához használatos a lemezre írás előtt. |
| WriteBufferMemoryPoolMaximumInKB |Kilobájtban |Korlátlan |Az a maximális méret, ameddig a naplózó írási puffer memória-készlete növekedni tud. |
| SharedLogId |GUID |"" |Meghatározza a fürtben lévő összes megbízható szolgáltatás által használt alapértelmezett megosztott naplófájl azonosítására szolgáló egyedi GUID azonosítót, amely nem adja meg a SharedLogId a szolgáltatási specifikus konfigurációban. Ha a SharedLogId meg van adva, akkor a SharedLogPath is meg kell adni. |
| SharedLogPath |Teljes elérési út neve |"" |Megadja a teljes elérési utat, ahol a fürt minden olyan csomópontján lévő megbízható szolgáltatás által használt megosztott naplófájl, amely nem adja meg a SharedLogPath a szolgáltatási specifikus konfigurációban. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId is meg kell adni. |
| SharedLogSizeInMB |Megabájtban |8192 |Megadja, hogy hány MB lemezterületet kell statikusan lefoglalni a megosztott napló számára. Az értéknek 2048 vagy nagyobbnak kell lennie. |

### <a name="sample-cluster-manifest-section"></a>Példa a fürt jegyzékfájljának szakaszára
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Megjegyzések
A naplózó olyan globális készletet tartalmaz, amely a nem lapozható kernel-memóriából van lefoglalva, amely az állapotadatok gyorsítótárazásához szükséges összes megbízható szolgáltatás számára elérhető a megbízható szolgáltatás másodpéldányához társított dedikált naplóba való írás előtt. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és a WriteBufferMemoryPoolMaximumInKB beállítások vezérlik. A WriteBufferMemoryPoolMinimumInKB megadja a memória-készlet kezdeti méretét, valamint azt a legkisebb méretet, ameddig a memória-készlet csökkenhet. A WriteBufferMemoryPoolMaximumInKB a legnagyobb méret, amellyel a memória-készlet növekedni lehet. Minden megnyitott megbízható szolgáltatás replikája növelheti a memória-készlet méretét a rendszer által meghatározott WriteBufferMemoryPoolMaximumInKB értékkel. Ha a rendelkezésre álló memória több memóriát is igénybe vesz, mint amennyi elérhető, a memóriára vonatkozó kérelmek késleltetve lesznek, amíg a memória elérhetővé válik. Ezért ha az írási pufferbeli memória készlete túl kicsi egy adott konfigurációhoz, akkor a teljesítmény romolhat.

A SharedLogId és a SharedLogPath beállításokat a rendszer mindig együtt használja a fürt összes csomópontjának alapértelmezett megosztott naplójának GUID azonosítójának és helyének definiálásához. Az alapértelmezett megosztott napló minden olyan megbízható szolgáltatáshoz használatos, amely nem határozza meg az adott szolgáltatáshoz tartozó settings.xml beállításait. A legjobb teljesítmény érdekében a megosztott naplófájlokat olyan lemezekre kell helyezni, amelyek kizárólag a megosztott naplófájlhoz használatosak a versengés csökkentése érdekében.

A SharedLogSizeInMB meghatározza, hogy a rendszer mennyi lemezterületet szabadítson fel az alapértelmezett megosztott bejelentkezéshez az összes csomóponton.  A SharedLogId és a SharedLogPath nem kell megadni a SharedLogSizeInMB megadása érdekében.

## <a name="replicator-security-configuration"></a>Replikátor biztonsági konfigurációja
A replikátor biztonsági beállításai a replikálás során használt kommunikációs csatorna biztonságossá tételére szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem látják egymás replikációs forgalmát, és gondoskodnak arról, hogy a rendelkezésre álló adatok biztonságban legyenek.
Alapértelmezés szerint a biztonsági konfiguráció üres szakasza megakadályozza a replikálás biztonságát.

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM formátumúnak kell lenniük. Ha többet szeretne megtudni a Linux-tanúsítványok kereséséről és konfigurálásáról, tekintse meg [a tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakört. 
> 

### <a name="section-name"></a>Szakasz neve
&lt;ActorName &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikátor konfigurációja
A replikátor-konfigurációk olyan replikátor konfigurálására szolgálnak, amelynek feladata, hogy a nagyköveti állapot szolgáltatói állapotát az állapot helyi replikálásával és megtartásával megbízhatóan konfigurálja.
Az alapértelmezett konfigurációt a Visual Studio-sablon hozza létre, és ennek elegendőnek kell lennie. Ez a szakasz a replikátor finomhangolásához elérhető további konfigurációkról beszél.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Másodperc |0,015 |Az az időszak, ameddig a másodlagos megvárja a műveletet a művelet fogadása után, mielőtt visszaküldi a nyugtát az elsődlegesnek. Az ezen az intervallumon belül feldolgozott műveletekhez küldendő összes más nyugtát egyetlen válaszként kell elküldeni. |
| ReplicatorEndpoint |N/A |Nincs alapértelmezett – kötelező paraméter |Az az IP-cím és port, amelyet az elsődleges/másodlagos replikátor a replikakészlet más replikákkal való kommunikációhoz fog használni. Ennek a szolgáltatás jegyzékfájljában a TCP-erőforrás végpontra kell hivatkoznia. A szolgáltatási jegyzékfájlban található végponti erőforrások definiálásával kapcsolatos további információkért tekintse meg a [szolgáltatás jegyzékfájljának erőforrásai](service-fabric-service-manifest-resources.md) című témakört. |
| MaxReplicationMessageSize |Bájt |50 MB |Egyetlen üzenetben továbbítható replikációs adatok maximális mérete. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |8192 |Az elsődleges várólistában lévő műveletek maximális száma. Egy művelet akkor szabadítható fel, ha az elsődleges replikátor nyugtát kap az összes másodlagos replikáló közül. Ennek az értéknek nagyobbnak kell lennie, mint 64, és a 2 hatványa. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A műveletek maximális száma a másodlagos várólistában. A művelet a kitartás után az állapotának nagyfokú rendelkezésre állása után szabadítható fel. Ennek az értéknek nagyobbnak kell lennie, mint 64, és a 2 hatványa. |
| CheckpointThresholdInMB |MB |200 |A naplófájl azon területének mennyisége, amely után az állapot ellenőrzőpontra kerül. |
| MaxRecordSizeInKB |KB |1024 |A replikációs rekord legnagyobb mérete, amelyet a replikátor írhat a naplóba. Ennek az értéknek a 4 és 16 közötti többszörösének kell lennie. |
| OptimizeLogForLowerDiskUsage |Logikai |true |Igaz értéke esetén a rendszer úgy konfigurálja a naplót, hogy a replika dedikált naplófájlját egy NTFS ritka fájl használatával hozza létre. Ez csökkenti a fájl tényleges lemezterület-felhasználását. Hamis érték esetén a fájl rögzített foglalásokkal jön létre, amelyek biztosítják a legjobb írási teljesítményt. |
| SharedLogId |guid |"" |Meghatározza a replikával használt megosztott naplófájl azonosításához használandó egyedi GUID azonosítót. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogId meg van adva, akkor a SharedLogPath is meg kell adni. |
| SharedLogPath |Teljes elérési út neve |"" |Meghatározza azt a teljes elérési utat, ahol a replika megosztott naplófájlja létrejön. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId is meg kell adni. |

## <a name="sample-configuration-file"></a>Minta konfigurációs fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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

## <a name="remarks"></a>Megjegyzések
A BatchAcknowledgementInterval paraméter a replikációs késést vezérli. A (z) "0" érték a lehető legalacsonyabb késést eredményezi az átviteli sebességnél (mivel több nyugtázási üzenetet kell elküldeni és feldolgozni, ami kevesebb nyugtát tartalmaz).
Minél nagyobb a BatchAcknowledgementInterval, annál nagyobb a replikálás teljes átviteli sebessége, a magasabb működési késés díja. Ez közvetlenül a tranzakciós véglegesítés késésével van lefordítva.

A CheckpointThresholdInMB paraméter azt a lemezterületet határozza meg, amelyet a replikátor a replika dedikált naplófájljában tárolt állapotadatok tárolására használhat. Ha az alapértelmezettnél nagyobb értéket ad meg, akkor gyorsabb újrakonfigurálási időt eredményezhet, amikor új replikát adnak hozzá a készlethez. Ennek oka a részleges állapot-átvitel, amely a naplóban lévő műveletek több előzményének rendelkezésre állása miatt zajlik. Ez egy összeomlás után is növelheti a replika helyreállítási idejét.

Ha a OptimizeForLowerDiskUsage értéke TRUE (igaz) értékre van állítva, a naplófájl mérete túl lesz kiépítve, így az aktív replikák több állapotinformációkat is tárolhatnak a naplófájlokban, míg az inaktív replikák kevesebb lemezterületet fognak használni. Ez lehetővé teszi, hogy több replikát működtessen egy csomóponton. Ha a OptimizeForLowerDiskUsage hamis értékre állítja, az állapotadatok gyorsabban íródnak a naplófájlba.

A MaxRecordSizeInKB beállítás határozza meg egy olyan rekord maximális méretét, amelyet a replikátor a naplófájlba tud írni. A legtöbb esetben az alapértelmezett 1024-KB-os rekord mérete optimális. Ha azonban a szolgáltatás nagyobb adatelemeket okoz az állapotadatok részévé tételéhez, akkor előfordulhat, hogy ezt az értéket növelni kell. A MaxRecordSizeInKB kisebb, mint 1024, mivel a kisebb rekordok csak a kisebb rekordokhoz szükséges területet használják. Azt várjuk, hogy ezt az értéket csak ritka esetekben kell módosítani.

A SharedLogId és a SharedLogPath beállításait mindig együtt használja a rendszer, hogy a szolgáltatás a csomópont alapértelmezett megosztott naplójából külön megosztott naplót használjon. A legjobb hatékonyság érdekében a lehető legtöbb szolgáltatásnak ugyanazt a megosztott naplót kell megadnia. A megosztott naplófájlokat a kizárólag a megosztott naplófájlhoz használt lemezekre kell helyezni a fej mozgásának csökkentése érdekében. Várható, hogy ezeket az értékeket ritkán kell módosítani.


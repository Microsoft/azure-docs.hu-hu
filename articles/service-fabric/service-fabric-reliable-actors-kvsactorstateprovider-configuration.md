---
title: KVSActorStateProvider beállításainak módosítása
description: Ismerje meg, hogyan konfigurálhatja az Azure-Service Fabric állapot-nyilvántartó KVSActorStateProvider típust.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609774"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Reliable Actors konfigurálása – KVSActorStateProvider
A KVSActorStateProvider alapértelmezett konfigurációját módosíthatja úgy, hogy módosítja a Microsoft Visual Studio-csomag gyökerében létrehozott settings.xml fájlt a megadott szereplő konfigurációs mappájában.

Az Azure Service Fabric Runtime a settings.xml fájlban előre definiált szakaszokat keres, és a konfigurációs értékeket a mögöttes futásidejű összetevők létrehozásakor használja fel.

> [!NOTE]
> A Visual Studio-megoldásban létrehozott settings.xml fájlban **ne** törölje vagy módosítsa a következő konfigurációk szakaszának nevét.
> 
> 

## <a name="replicator-security-configuration"></a>Replikátor biztonsági konfigurációja
A replikátor biztonsági beállításai a replikálás során használt kommunikációs csatorna biztonságossá tételére szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem látják egymás replikációs forgalmát, így biztosítva, hogy a kiemelten elérhető adatok is biztonságosak legyenek.
Alapértelmezés szerint a biztonsági konfiguráció üres szakasza megakadályozza a replikálás biztonságát.

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM formátumúnak kell lenniük. Ha többet szeretne megtudni a Linux-tanúsítványok kereséséről és konfigurálásáról, tekintse meg [a tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakört. 
> 

### <a name="section-name"></a>Szakasz neve
&lt;ActorName &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikátor konfigurációja
A replikátor-konfigurációk olyan replikátort állítanak be, amely felelős a szereplők állami szolgáltatói állapotának nagyfokú megbízhatóságának kialakításához.
Az alapértelmezett konfigurációt a Visual Studio-sablon hozza létre, és ennek elegendőnek kell lennie. Ez a szakasz a replikátor finomhangolásához elérhető további konfigurációkról beszél.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Másodperc |0,015 |Az az időszak, ameddig a másodlagos megvárja a műveletet a művelet fogadása után, mielőtt visszaküldi a nyugtát az elsődlegesnek. Az ezen az intervallumon belül feldolgozott műveletekhez küldendő összes más nyugtát egyetlen válaszként kell elküldeni. |
| ReplicatorEndpoint |N/A |Nincs alapértelmezett – kötelező paraméter |Az az IP-cím és port, amelyet az elsődleges/másodlagos replikátor a replikakészlet más replikákkal való kommunikációhoz fog használni. Ennek a szolgáltatás jegyzékfájljában a TCP-erőforrás végpontra kell hivatkoznia. A szolgáltatási jegyzékfájlban található végponti erőforrások definiálásával kapcsolatos további információkért tekintse meg a [szolgáltatás jegyzékfájljának erőforrásai](service-fabric-service-manifest-resources.md) című témakört. |
| RetryInterval |Másodperc |5 |Az az időtartam, amely után a replikátor újraküld egy üzenetet, ha nem kap nyugtát egy művelethez. |
| MaxReplicationMessageSize |Bájt |50 MB |Egyetlen üzenetben továbbítható replikációs adatok maximális mérete. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |1024 |Az elsődleges várólistában lévő műveletek maximális száma. Egy művelet akkor szabadítható fel, ha az elsődleges replikátor nyugtát kap az összes másodlagos replikáló közül. Ennek az értéknek nagyobbnak kell lennie, mint 64, és a 2 hatványa. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |2048 |A műveletek maximális száma a másodlagos várólistában. A művelet a kitartás után az állapotának nagyfokú rendelkezésre állása után szabadítható fel. Ennek az értéknek nagyobbnak kell lennie, mint 64, és a 2 hatványa. |

## <a name="store-configuration"></a>Tároló konfigurációja
Az áruházi konfigurációk a replikált állapot megőrzésére szolgáló helyi tároló konfigurálására szolgálnak.
Az alapértelmezett konfigurációt a Visual Studio-sablon hozza létre, és ennek elegendőnek kell lennie. Ez a szakasz a helyi tároló finomhangolásához elérhető további konfigurációkról beszél.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName &gt; ServiceLocalStoreConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Ezredmásodpercben |200 |Beállítja a tartós helyi tárolók végrehajtásának maximális időtartamát. |
| MaxVerPages |Lapok száma |16384 |A helyi tároló adatbázisában lévő verziók maximális száma. Meghatározza a függőben lévő tranzakciók maximális számát. |

## <a name="sample-configuration-file"></a>Minta konfigurációs fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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


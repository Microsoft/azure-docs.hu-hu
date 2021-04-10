---
title: Windows rendszerű virtuális gépek időszinkronizálása az Azure-ban
description: Windows rendszerű virtuális gépek időszinkronizálása.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 85abc77757d31f3b1054a0670ea3f65a4fcb2e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555958"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Windows rendszerű virtuális gépek időszinkronizálása az Azure-ban

Az időszinkronizálás fontos a biztonság és az események korrelációs használata esetén. Időnként az elosztott tranzakciók megvalósítására használják. A több számítógépes rendszer közötti idő-pontosság a szinkronizáláson keresztül érhető el. A szinkronizálás több dologra is hatással lehet, többek között a újraindításra és a hálózati forgalomra az idő forrása és a számítógép lekérése között. 

Az Azure-t mostantól a Windows Server 2016-et futtató infrastruktúra támogatja. A Windows Server 2016 továbbfejlesztett algoritmusokkal rendelkezik, amelyek az időpontot és a feltételt használják a helyi óra és az UTC közötti szinkronizáláshoz.  A Windows Server 2016 továbbfejlesztette a VMICTimeSync szolgáltatást, amely azt szabályozza, hogy a virtuális gépek hogyan szinkronizálják a gazdagépet a pontos időpontig. A javításokban pontosabb kezdeti idő van a virtuális gépek indításakor vagy a virtuális gépek visszaállításakor, valamint a Windows Time (W32Time) szolgáltatásban megadott minták késési késleltetésének javítása. 


>[!NOTE]
>A Windows időszolgáltatásának gyors áttekintéséhez tekintse meg ezt a [magas szintű áttekintő videót](https://aka.ms/WS2016TimeVideo).
>
> További információ: [a Windows Server 2016 pontos ideje](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Áttekintés

A számítógép órájának pontossága a számítógép órájának az egyezményes világidő (UTC) időszabványhoz való zárásának mérésére szolgál. Az UTC-t egy, a pontos Atomic-órákat tartalmazó többnemzetiségű minta határozza meg, amely csak 300 év egy másodpercében lehet kikapcsolható. Az UTC közvetlen olvasása azonban speciális hardvert igényel. Ehelyett az időkiszolgálókat a rendszer az UTC szerint szinkronizálja, és más számítógépekről éri el a méretezhetőség és a robusztusság biztosítása érdekében. Minden számítógépen van olyan időszinkronizálási szolgáltatás, amely tudja, hogy milyen időkiszolgálókat használ, és rendszeres időközönként ellenőrzi, hogy a számítógép óráját javítani kell-e, és szükség esetén módosítani kell-e az időt. 

Az Azure-gazdagépek szinkronizálása a Microsoft által a Microsoft által birtokolt stratum 1-eszközökről, a GPS-antennák használatával történik. Az Azure-beli virtuális gépek vagy a gazdagéptől függenek, hogy a pontos időt (*gazda időt*) a virtuális gépre irányítják, vagy a virtuális gép közvetlenül lekérheti az időt egy időkiszolgálóról, vagy mindkettő kombinációját. 

A virtuális gép és a gazdagép közötti interakció is hatással lehet az órára. A [karbantartás](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)során a virtuális gépek legfeljebb 30 másodpercig szünetelnek. Például a karbantartás megkezdése előtt a virtuális gép órája a 10:00:00-as és 28 másodperces időtartamot jeleníti meg. A virtuális gép újraindítása után a virtuális gép órája továbbra is a 10:00:00-as értéket fogja megjeleníteni, ami 28 másodperc lenne. Ennek kiválasztásához a VMICTimeSync szolgáltatás figyeli, hogy mi történik a gazdagépen, és felszólítja a virtuális gépeken végrehajtott módosítások elvégzésére a kompenzálás érdekében.

A VMICTimeSync szolgáltatás akár minta-, akár szinkronizálási módban működik, és csak az órát fogja befolyásolni. A W32Time futtatását igénylő mintavételi módban a VMICTimeSync szolgáltatás 5 másodpercenként lekérdezi a gazdagépet, és időmintákat biztosít a W32Time. Körülbelül 30 másodpercenként a W32Time szolgáltatás a legkésőbbi időpontot használja, és a vendég órája befolyásolja. A szinkronizálási mód akkor aktiválódik, ha a vendég újraindult, vagy ha a vendég órája 5 másodpercnél több időt vesz igénybe a gazdagép órája mögött. Azokban az esetekben, amikor a W32Time szolgáltatás megfelelően fut, az utóbbi esetben soha nem fordulhat elő.

Az időszinkronizálás nem működik, mert a virtuális gépen lévő idő felhalmozza a hibákat. Ha csak egy virtuális gép van, akkor előfordulhat, hogy a hatás nem jelentős, kivéve, ha a munkaterhelés nagyon pontos időmérést igényel. A legtöbb esetben azonban több, egymással összekapcsolt virtuális géppel is rendelkezünk, amelyek időt használnak a tranzakciók nyomon követésére, és az idő a teljes telepítés során konzisztensnek kell lennie. Ha a virtuális gépek közötti idő eltér, a következő hatások jelenhetnek meg:

- A hitelesítés sikertelen lesz. A biztonsági protokollok, például a Kerberos vagy a tanúsítványtól függő technológiák támaszkodnak a rendszerek közötti konzisztens időszakra. 
- Nagyon nehéz megállapítani, hogy mi történt a rendszeren, ha a naplók (vagy más adat) nem fogadnak el időt. Ugyanez az esemény a különböző időpontokban történt, így megnehezíti a korrelációt.
- Ha az óra ki van kapcsolva, a számlázás helytelenül számítható ki.

A Windows központi telepítések legjobb eredményei a Windows Server 2016 operációs rendszer használatával érhetők el, amely biztosítja, hogy az időszinkronizálás legújabb fejlesztései is használhatók legyenek.

## <a name="configuration-options"></a>Beállítási lehetőségek

Az Azure-ban üzemeltetett Windows-alapú virtuális gépek időszinkronizálásának konfigurálására három lehetőség áll rendelkezésre:

- A gazdagép ideje és time.windows.com. Ez az Azure Marketplace-lemezképekben használt alapértelmezett konfiguráció.
- Csak gazdagép.
- Használjon egy másik, külső időkiszolgálót vagy anélkül, hogy a gazdagépi időt használja.


### <a name="use-the-default"></a>Alapértelmezett érték használata

Alapértelmezés szerint a Windows operációs rendszerhez készült virtuálisgép-lemezképek két forrásból való szinkronizálásra vannak konfigurálva a W32Time: 

- Az NTP-szolgáltató, amely információt kap a time.windows.com.
- A VMICTimeSync szolgáltatás, amely a gazdagépi idő és a virtuális gépek közötti kommunikációra szolgál, és a virtuális gép karbantartás utáni szüneteltetése után végez javítást. Az Azure-gazdagépek a Microsoft által birtokolt stratum 1 eszközöket használják a pontos idő megtartására.

a W32Time a következő prioritási sorrendben szeretné előnyben részesíteni az időszolgáltatót: stratum szint, root delay, root diszperzió, Time eltolás. A legtöbb esetben az Azure-beli virtuális gépeken a W32Time a kiértékelés miatt előnyben részesítették, hogy mindkét időforrást összehasonlítani kellene. 

Tartományhoz csatlakozó gépek esetén a tartomány maga is időszinkronizálási hierarchiát hoz létre, de az erdő gyökerének el kell érnie az időt, és a következő szempontokat továbbra is igaz értékre kell állítani.


### <a name="host-only"></a>Csak gazdagép 

Mivel a time.windows.com egy nyilvános NTP-kiszolgáló, a szinkronizálási idő megköveteli az interneten keresztüli adatforgalmat, a csomagok késése pedig negatívan befolyásolhatja az idő szinkronizálásának minőségét. A time.windows.com eltávolítása a csak gazdagépre történő szinkronizálásra való áttéréssel időnként javíthatja az idő szinkronizálásának eredményeit.

Ha az alapértelmezett konfiguráció használatával időt vesz igénybe, a csak a gazdagépre való áttéréskor érdemes váltani. Próbálja ki a gazdagépek szinkronizálását, és ellenőrizze, hogy ez javítaná-e a virtuális gépen futó idő szinkronizálását. 

### <a name="external-time-server"></a>Külső idő kiszolgálója

Ha bizonyos időszinkronizálási követelményekkel rendelkezik, akkor a külső időkiszolgálók is használhatók. A külső időkiszolgálók meghatározott időt biztosíthatnak, ami hasznos lehet a tesztelési forgatókönyvekhez, így biztosítva a nem Microsoft-adatközpontokban üzemeltetett gépekkel való időbeli egységességet, illetve a LEAP másodpercek speciális módon történő kezelését.

A külső kiszolgálókat a VMICTimeSync szolgáltatással és a VMICTimeProvider kombinálva az alapértelmezett konfigurációhoz hasonló eredményeket adhat meg. 

## <a name="check-your-configuration"></a>Konfiguráció ellenõrzése


Ellenőrizze, hogy az NTP-kiszolgáló időszolgáltatója a explicit NTP-kiszolgálók (NTP) vagy a tartomány időszinkronizálása (NT5DS) használatára van-e konfigurálva.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Ha a virtuális gép NTP-t használ, a következő kimenet jelenik meg:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Ha szeretné megtekinteni, hogy az NTP-kiszolgáló milyen időpontot használ, egy rendszergazda jogú parancssorba írja be a következőt:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Ha a virtuális gép az alapértelmezettet használja, a kimenet a következőképpen fog kinézni:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Itt láthatja, hogy jelenleg milyen időszolgáltatót használ.

```
w32tm /query /source
```


Itt látható a kimenet, és mit jelent:
    
- **Time.Windows.com** – az alapértelmezett konfigurációban a w32time a Time.Windows.com-től származó időt is igénybe veheti. Az időszinkronizálási minőség függ az internetkapcsolattól, és a csomagok késése is befolyásolja. Ez a normál kimenet a fizikai gépen.
- **VM IC-idő szinkronizációs szolgáltatója**  – a virtuális gép szinkronizálja az időt a gazdagépről. Ez az a szokásos kimenet, amelyet az Azure-on futó virtuális gépeken fog kapni. 
- *A tartományi kiszolgáló* – az aktuális gép egy tartományban van, és a tartomány meghatározza az időszinkronizálási hierarchiát.
- *Egy másik kiszolgáló* -W32Time explicit módon úgy lett konfigurálva, hogy lekérje a másik kiszolgáló idejét. Az időszinkronizálás minősége ettől az időponttól függ a kiszolgáló minőségétől.
- A **helyi CMOS óra** – óra nincs szinkronizálva. Ezt a kimenetet akkor érheti el, ha a W32Time nem volt elég ideje elindítani az újraindítás után, vagy ha a konfigurált időforrások nem érhetők el.


## <a name="opt-in-for-host-only-time-sync"></a>Csak a gazdagépen történő szinkronizálás engedélyezése

Az Azure folyamatosan dolgozik a gazdagépek időszinkronizálásának javításán, és garantálja, hogy az összes időszinkronizálási infrastruktúra közös elhelyezésű a Microsoft által birtokolt adatközpontokban. Ha van olyan időszinkronizálási problémája, amely a time.windows.com elsődleges időforrásként való használatát részesíti előnyben, a következő parancsokkal engedélyezheti a gazdagépen lévő idő szinkronizálását.

A VMIC-szolgáltató megjelölése engedélyezettként. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Az NTP-szolgáltatót letiltottként kell megjelölnie.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Indítsa újra a W32Time szolgáltatást.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 és R2 rendszerű virtuális gépek 

A Windows Server 2012 és a Windows Server 2012 R2 különböző alapértelmezett beállításokkal rendelkezik az idő szinkronizálásához. A W32Time alapértelmezés szerint úgy van konfigurálva, hogy a szolgáltatás alacsony terhelést és a pontos időt részesíti előnyben. 

Ha a Windows Server 2012-es és 2012 R2-es verzióját szeretné áthelyezni a pontos időpontot előnyben részesített új alapértelmezett beállítások használatára, akkor a következő beállításokat alkalmazhatja.

Frissítse a W32Time lekérdezési és frissítési intervallumait a Windows Server 2016 beállításainak megfelelően.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Ahhoz, hogy a W32Time használni tudja az új lekérdezési időközöket, a NtpServers kell megjelölni. Ha a kiszolgálók megjegyzése 0x1 bitflag maszkkal történik, akkor ez felülbírálja ezt a mechanizmust, és a W32Time a SpecialPollInterval-t fogja használni. Győződjön meg arról, hogy a megadott NTP-kiszolgálók 0x8 jelzőt vagy nincs jelölőt használnak:

Győződjön meg arról, hogy a használt NTP-kiszolgálókhoz milyen jelzők vannak használatban.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozások az idő szinkronizálásával kapcsolatos további részletekre mutatnak:

- [A Windows időszolgáltatás eszközei és beállításai](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [A Windows Server 2016 fejlesztése ](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Pontos idő a Windows Server 2016-hoz](/windows-server/networking/windows-time-service/accurate-time)
- [Határ támogatása a Windows időszolgáltatásának magas pontosságú környezetekhez való konfigurálásához](/windows-server/networking/windows-time-service/support-boundary)

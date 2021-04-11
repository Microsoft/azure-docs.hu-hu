---
title: A Azure Site Recovery Process Server hibáinak megoldása
description: Ez a cikk a Azure Site Recovery Process Serverrel kapcsolatos hibák elhárítását ismerteti
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.openlocfilehash: c6696fb395aee7284f1354269cd4cc3167ff3198
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581809"
---
# <a name="troubleshoot-the-process-server"></a>A folyamat kiszolgálójának hibáinak megoldása

Az [site Recovery](site-recovery-overview.md) Process Server akkor használatos, amikor a helyszíni VMWare virtuális gépek és fizikai kiszolgálók esetében a vész-helyreállítást állítja be az Azure-ba. Ez a cikk a Process Serverrel kapcsolatos hibák elhárítását ismerteti, beleértve a replikálási és csatlakozási problémákat.

[További](vmware-physical-azure-config-process-server-overview.md) információ a Process Serverről.

## <a name="before-you-start"></a>Előkészületek

A hibaelhárítás megkezdése előtt:

1. Győződjön meg arról, hogy megérti a [folyamat-kiszolgálók figyelését](vmware-physical-azure-monitor-process-server.md).
2. Tekintse át az alábbi ajánlott eljárásokat.
3. Ügyeljen arra, hogy kövesse a [kapacitással kapcsolatos szempontokat](site-recovery-plan-capacity-vmware.md#capacity-considerations), és használjon méretezési útmutatót a [konfigurációs kiszolgálóhoz](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) vagy az [önálló folyamat-kiszolgálókhoz](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Ajánlott eljárások a Process Server üzembe helyezéséhez

A folyamat-kiszolgálók optimális teljesítményéhez számos általános ajánlott eljárást összegezünk.

**Ajánlott eljárás** | **Részletek**
--- |---
**Használat** | Győződjön meg arról, hogy a konfigurációs kiszolgáló/önálló folyamat kiszolgálója csak a kívánt célra szolgál. Ne futtasson semmi mást a gépen.
**IP-cím** | Győződjön meg arról, hogy a Process Server statikus IPv4-címe van, és nincs konfigurálva a NAT.
**Memória/CPU-használat szabályozása** |A processzor és a memória kihasználtsága 70% alatt marad.
**Szabad terület biztosítása** | A szabad terület a folyamat kiszolgálójának gyorsítótár-lemezterületére utal. Az Azure-ba való feltöltés előtt a replikálási szolgáltatás a gyorsítótárban tárolódik.<br/><br/> Tartsa meg a szabad területet 25% felett. Ha a művelet 20% alá esik, a rendszer a folyamat-kiszolgálóhoz társított replikált gépek esetében a replikálást szabályozza.

## <a name="check-process-server-health"></a>A Process Server állapotának keresése

A hibaelhárítás első lépése a Process Server állapotának és állapotának a megkeresése. Ehhez tekintse át az összes riasztást, ellenőrizze, hogy futnak-e a szükséges szolgáltatások, és ellenőrizze, hogy van-e szívverés a folyamat-kiszolgálóról. Ezek a lépések az alábbi ábrán vannak összefoglalva, amelyet a lépések végrehajtásához szükséges eljárások követnek.

![A Process Server Health hibáinak megoldása](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1. lépés: a Process Server állapotával kapcsolatos riasztások hibáinak megoldása

A Process Server számos rendszerállapot-riasztást generál. Ezeket a riasztásokat és az ajánlott műveleteket az alábbi táblázat foglalja össze.

**Riasztástípus** | **Hiba** | **Hibaelhárítás**
--- | --- | --- 
![Kifogástalan][green] | Nincsenek  | A Process Server csatlakoztatva van és kifogástalan állapotú.
![Figyelmeztetés][yellow] | A megadott szolgáltatások nem futnak. | 1. Győződjön meg arról, hogy a szolgáltatások futnak.<br/> 2. Ha a szolgáltatások a várt módon futnak, kövesse az alábbi utasításokat a [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication).
![Figyelmeztetés][yellow]  | A CPU-kihasználtság > 80%-ot az elmúlt 15 percben. | 1. ne adjon hozzá új gépeket.<br/>2. Győződjön meg arról, hogy a folyamat-kiszolgálót használó virtuális gépek száma a [meghatározott határértékekhez](site-recovery-plan-capacity-vmware.md#capacity-considerations)igazodik, és vegye fontolóra egy [további folyamat-kiszolgáló](vmware-azure-set-up-process-server-scale.md)beállítását.<br/>3. kövesse az alábbi utasításokat a [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication).
![Kritikus][red] |  A CPU-kihasználtság > 95%-ot az elmúlt 15 percben. | 1. ne adjon hozzá új gépeket.<br/>2. Győződjön meg arról, hogy a folyamat-kiszolgálót használó virtuális gépek száma a [meghatározott határértékekhez](site-recovery-plan-capacity-vmware.md#capacity-considerations)igazodik, és vegye fontolóra egy [további folyamat-kiszolgáló](vmware-azure-set-up-process-server-scale.md)beállítását.<br/>3. kövesse az alábbi utasításokat a [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication).<br/> 4. Ha a probléma továbbra is fennáll, futtassa a [Deployment Planner](./site-recovery-deployment-planner.md) a VMware/fizikai kiszolgáló replikálásához.
![Figyelmeztetés][yellow] | Memóriahasználat > 80% az elmúlt 15 percben. |  1. ne adjon hozzá új gépeket.<br/>2. Győződjön meg arról, hogy a folyamat-kiszolgálót használó virtuális gépek száma a [meghatározott határértékekhez](site-recovery-plan-capacity-vmware.md#capacity-considerations)igazodik, és vegye fontolóra egy [további folyamat-kiszolgáló](vmware-azure-set-up-process-server-scale.md)beállítását.<br/>3. kövesse a figyelmeztetéssel kapcsolatos utasításokat.<br/> 4. Ha a probléma továbbra is fennáll, kövesse az alábbi utasításokat a [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication).
![Kritikus][red] | Memóriahasználat > 95% az elmúlt 15 percben. | 1. ne adjon hozzá új gépeket, és vegye fontolóra egy [további folyamat-kiszolgáló](vmware-azure-set-up-process-server-scale.md)beállítását.<br/> 2. kövesse a figyelmeztetéssel kapcsolatos utasításokat.<br/> 3.4. Ha a probléma továbbra is fennáll, kövesse az alábbi utasításokat a [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication).<br/> 4. Ha a probléma továbbra is fennáll, futtassa a VMware/fizikai kiszolgáló replikációs problémáinak [Deployment Plannerát](./site-recovery-deployment-planner.md) .
![Figyelmeztetés][yellow] | A gyorsítótár-mappa szabad területe < 30% az elmúlt 15 percben. | 1. ne vegyen fel új gépeket, és vegye fontolóra egy [további folyamat-kiszolgáló](vmware-azure-set-up-process-server-scale.md)beállítását.<br/>2. Győződjön meg arról, hogy a Process Servert használó virtuális gépek száma igazodik az [irányelvekhez](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. kövesse az alábbi utasításokat a [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication).
![Kritikus][red] |  Szabad terület < 25% az elmúlt 15 percben | 1. kövesse a probléma figyelmeztetéséhez tartozó utasításokat.<br/> 2.3. A [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication)kövesse az alábbi utasításokat.<br/> 3. Ha a probléma továbbra is fennáll, futtassa a [Deployment Planner](./site-recovery-deployment-planner.md) a VMware/fizikai kiszolgáló replikálásához.
![Kritikus][red] | A folyamat kiszolgálójának nincs szívverése 15 percig vagy többet. A tmansvs szolgáltatás nem kommunikál a konfigurációs kiszolgálóval. | 1.) győződjön meg arról, hogy a Process Server működik-e.<br/> 2. Győződjön meg arról, hogy a tmassvc fut a Process Serveren.<br/> 3. kövesse az alábbi utasításokat a [kapcsolódási és replikálási problémák elhárításához](#check-connectivity-and-replication).


![Tábla kulcsa](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2. lépés: a Process Server-szolgáltatások keresése

A folyamat-kiszolgálón futó szolgáltatásokat a következő táblázat foglalja össze. A Process Server üzembe helyezésének módjától függően a szolgáltatások kisebb eltéréseket mutatnak. 

A Microsoft Azure Recovery Services ügynök (obengine) kivételével az összes szolgáltatás esetében győződjön meg arról, hogy a StartType **automatikus** vagy **automatikus (Késleltetett indítás)** értékre van beállítva.
 
**Üzembe helyezés** | **Futó szolgáltatások**
--- | ---
**Kiszolgáló feldolgozása a konfigurációs kiszolgálón** | ProcessServer; ProcessServerMonitor; cxprocessserver Inmage-PushInstall; Napló feltöltési szolgáltatása (LogUpload); Inmage Scout Application szolgáltatás; Microsoft Azure Recovery Services ügynök (obengine); Inmage Scout VX-ügynök – Sentinel/Outpost (svagents); tmansvc World Wide Web közzétételi szolgáltatás (W3SVC); MySQL Microsoft Azure Site Recovery szolgáltatás (DRA)
**Önálló kiszolgálóként futtató kiszolgáló feldolgozása** | ProcessServer; ProcessServerMonitor; cxprocessserver Inmage-PushInstall; Napló feltöltési szolgáltatása (LogUpload); Inmage Scout Application szolgáltatás; Microsoft Azure Recovery Services ügynök (obengine); Inmage Scout VX-ügynök – Sentinel/Outpost (svagents); tmansvc.
**Az Azure-ban üzembe helyezett Process Server feladat-visszavétel** | ProcessServer; ProcessServerMonitor; cxprocessserver Inmage-PushInstall; Napló feltöltési szolgáltatása (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>3. lépés: a folyamat kiszolgálójának szívverését vizsgálja

Ha a Process Server nem rendelkezik szívveréssel (hibakód: 806), tegye a következőket:

1. Ellenőrizze, hogy a Process Server virtuális gép működik-e.
2. Keresse meg ezeket a naplókat a hibákért.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log c\programdata\asr\home\svsystems\ monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>A kapcsolat és a replikálás ellenőrzése

 A kezdeti és a folyamatos replikálási hibákat gyakran a forrásoldali gépek és a Process Server, illetve a Process Server és az Azure közötti csatlakozási problémák okozzák. Ezek a lépések az alábbi ábrán vannak összefoglalva, amelyet a lépések végrehajtásához szükséges eljárások követnek.

![A kapcsolat és a replikálás hibaelhárításához szükséges lépéseket bemutató folyamatábra.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4. lépés: a forrásoldali gépen futó idő szinkronizálásának ellenőrzése

Győződjön meg arról, hogy a replikált gép rendszerdátuma és ideje szinkronban van. [További információ](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5. lépés: víruskereső szoftver keresése a forrásszámítógépen

Győződjön meg arról, hogy a replikált gépen nem található víruskereső szoftver, Site Recovery blokkolja. Ha ki kell zárnia Site Recovery a víruskereső programokból, tekintse át [ezt a cikket](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>6. lépés: a forrás-számítógép kapcsolatának ellenőrzése


1. Ha szükséges, telepítse a [Telnet-ügyfelet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) a forrásoldali gépre. Ne használja a ping parancsot.
2. A forrásoldali gépről Pingelje a HTTPS-porton futó Process Servert a Telnet használatával. Alapértelmezés szerint a 9443 a replikációs forgalom HTTPS-portja.

    `telnet <process server IP address> <port>`

3. Ellenőrizze, hogy a sikeres-e a kapcsolatok.


**Kapcsolódás** | **Részletek** | **Művelet**
--- | --- | ---
**Sikeres** | A Telnet egy üres képernyőt jelenít meg, és a Process Server elérhető. | Nincs szükség további műveletre.
**Sikertelen** | Nem lehet kapcsolatot létesíteni | Győződjön meg arról, hogy a (z) 9443 bejövő port engedélyezett a Process Serveren. Ha például egy peremhálózati hálózat vagy egy szűrt alhálózat van. A kapcsolat ismételt ellenőrzése.
**Részben sikeres** | Kapcsolódhat, de a forrásszámítógép jelentést küld arról, hogy a folyamat kiszolgálója nem érhető el. | Folytassa a következő hibaelhárítási eljárással.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7. lépés: nem elérhető Process Server-kiszolgáló hibáinak megoldása

Ha a Process Server nem érhető el a forrásoldali gépről, akkor a 78186-es hiba jelenik meg. Ha nem foglalkozik, ez a probléma az alkalmazás-konzisztens és az összeomlás-konzisztens helyreállítási pontok elérését eredményezi, és nem a várt módon jön létre.

A hibák elhárításához ellenőrizze, hogy a forrásszámítógép elérheti-e a folyamat kiszolgálójának IP-címét, majd futtassa a cxpsclient eszközt a forrásoldali gépen a végpontok közötti kapcsolat ellenőrzéséhez.


### <a name="check-the-ip-connection-on-the-process-server"></a>Az IP-kapcsolatok keresése a Process Serveren

Ha a Telnet sikeres, de a forrásszámítógép azt jelenti, hogy a folyamat kiszolgálója nem érhető el, ellenőrizze, hogy el tudja-e érni a folyamat kiszolgálójának IP-címét.

1. Egy böngészőben próbálja meg elérni az IP-https://<PS_IP>: <PS_Data_Port>/.
2. Ha ez az érték a HTTPS-tanúsítvány hibáját mutatja, ez normális. Ha figyelmen kívül hagyja a hibát, a 400 – hibás kérést kell megjelennie. Ez azt jelenti, hogy a kiszolgáló nem tudja kiszolgálni a böngésző kérését, és hogy a szabványos HTTPS-kapcsolat rendben van.
3. Ha ez az ellenőrzési folyamat nem működik, jegyezze fel a böngésző hibaüzenetét. Egy 407-es hiba például egy proxy hitelesítéssel kapcsolatos problémát jelez.

### <a name="check-the-connection-with-cxpsclient"></a>A cxpsclient-mel létesített kapcsolatok keresése

Emellett a cxpsclient eszközt is futtathatja a végpontok közötti kapcsolatok vizsgálatához.

1. Futtassa az eszközt az alábbiak szerint:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. A folyamat-kiszolgálón keresse meg a létrehozott naplókat ezekben a mappákban:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>A forrásként szolgáló virtuális gépek naplófájljainak keresése a feltöltési hibákhoz (78028-es hiba)

A forrásoldali gépekről a Process Service-be blokkolt adatfeltöltés miatti probléma az összeomlás-konzisztens és az alkalmazás-konzisztens helyreállítási pontokat is eredményezheti. 

1. A hálózati feltöltési hibák elhárításához a következő naplóban talál hibákat:

    C:\Program Files (x86) \Microsoft Azure-hely Recovery\agent\svagents *. log 

2. A cikkben található további eljárások segítségével megoldhatja az adatok feltöltésével kapcsolatos problémákat.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8. lépés: annak megállapítása, hogy a folyamat kiszolgálója az adattovábbítást

Győződjön meg arról, hogy a Process Server aktívan küldi az Azure-ba irányuló adatküldést.

  1. A Process Serveren nyissa meg a Feladatkezelő eszközt (nyomja le a CTRL + SHIFT + ESC billentyűkombinációt).
  2. Válassza a **teljesítmény** fület > **Open erőforrás-figyelő**.
  3. A **erőforrás-figyelő** lapon válassza a **hálózat** lapot. A **hálózati tevékenységgel rendelkező folyamatok** területen győződjön meg arról, hogy a cbengine.exe aktívan küld-e nagy mennyiségű adat.

       ![Képernyőfelvétel: nagy mennyiségű kötet a hálózati tevékenységgel rendelkező folyamatokban.](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Ha cbengine.exe nem küld nagy mennyiségű adatokat, hajtsa végre az alábbi részekben ismertetett lépéseket.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9. lépés: az Azure Blob Storage-hoz való kapcsolódási kiszolgáló keresése

1. A erőforrás-figyelő területen válassza a **cbengine.exe** lehetőséget.
2. A **TCP-kapcsolatok** területen ellenőrizze, hogy van-e kapcsolat a Process Server és az Azure Storage között.

  ![A cbengine.exe és az Azure Blob Storage URL-címéhez való kapcsolódást bemutató képernyőkép.](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Szolgáltatások keresése

Ha nincs kapcsolat a Process Serverrel az Azure Blob Storage URL-címével, ellenőrizze, hogy a szolgáltatások futnak-e.

1. A vezérlőpulton válassza a **szolgáltatások** lehetőséget.
2. Ellenőrizze, hogy a következő szolgáltatások futnak-e:

    - cxprocessserver
    - Inmage Scout VX-ügynök – Sentinel/Outpost
    - Microsoft Azure Recovery Services ügynök
    - Microsoft Azure Site Recovery szolgáltatás
    - tmansvc

3. Indítsa el vagy indítsa újra a nem futó szolgáltatásokat.
4. Ellenőrizze, hogy a Process Server csatlakoztatva van-e és elérhető-e. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10. lépés: a Process Server Azure nyilvános IP-címhez való kapcsolódásának keresése

1. A Process Server **%ProgramFiles%\Microsoft Azure Recovery Services Agent\Temp** nyissa meg a legújabb CBEngineCurr. errlog fájlt.
2. A fájlban keresse meg a **443**, vagy a karakterlánc- **kapcsolódási kísérlet sikertelen volt**.

  ![A temp mappában található hibák naplói](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Ha problémákat tapasztal, a CBEngineCurr. currLog fájlban található Azure nyilvános IP-címet a 443-es porton keresztül találja:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. A Process Server parancssorában a Telnet használatával Pingelje az Azure nyilvános IP-címét.
6. Ha nem tud kapcsolatot létesíteni, kövesse a következő eljárást.

## <a name="step-11-check-process-server-firewall-settings"></a>11. lépés: a Process Server tűzfal beállításainak megtekintése. 

Győződjön meg arról, hogy az IP-cím alapú tűzfal blokkolja-e a hozzáférést a folyamat kiszolgálóján.

1. IP-cím alapú tűzfalszabályok esetén:

    a) töltse le a [Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653)teljes listáját.

    b) adja hozzá az IP-címtartományt a tűzfal konfigurációjához, hogy a tűzfal engedélyezze a kommunikációt az Azure-ban (és az alapértelmezett HTTPS-porton, 443).

    c) engedélyezze az előfizetéshez tartozó Azure-régió, illetve az Azure West USA régiója számára az IP-címtartományok használatát (a hozzáférés-vezérléshez és az identitások kezeléséhez).

2. URL-alapú tűzfalak esetén adja hozzá a következő táblázatban felsorolt URL-címeket a tűzfal konfigurációjához.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12. lépés: a folyamat-kiszolgáló proxy beállításainak ellenőrzése 

1. Ha proxykiszolgálót használ, győződjön meg arról, hogy a proxykiszolgáló nevét feloldja a DNS-kiszolgáló. Tekintse meg a konfigurációs kiszolgáló beállításakor megadott értéket a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings** beállításkulcsban.
2. Győződjön meg arról, hogy a Azure Site Recovery ügynök ugyanazokat a beállításokat használja az adatküldéshez.

    a) keressen **Microsoft Azure Backup**.

    b) nyissa meg **Microsoft Azure Backup**, és válassza a **művelet**  >  **módosítása tulajdonságok** lehetőséget.

    c) a proxy **konfigurálása** lapon a proxy címnek meg kell egyeznie a beállításjegyzék-beállításokban megjelenő proxy-címtől. Ha nem, módosítsa azt ugyanarra a címekre.

## <a name="step-13-check-bandwidth"></a>13. lépés: a sávszélesség ellenõrzése

Növelje meg a folyamat-kiszolgáló és az Azure közötti sávszélességet, és győződjön meg arról, hogy a probléma továbbra is fennáll.


## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége, tegye fel kérdéseit a [Microsoft Q&a Azure site Recovery kérdését](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
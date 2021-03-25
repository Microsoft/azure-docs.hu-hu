---
title: Az Azure-hibák feladatátvételének elhárítása | Microsoft Docs
description: Ez a cikk az Azure-ban feladatátvétel során fellépő gyakori hibák elhárításának módszereit ismerteti
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 8fe21ce5b92d672a2e025e0b45b8cbaea5951a8b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043903"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>VMware virtuális gépről vagy fizikai gépről az Azure-ba irányuló feladatátvétel hibaelhárítása

A virtuális gépek Azure-ba történő feladatátvétele közben a következő hibák valamelyike jelenhet meg. A hibaelhárításhoz használja a leírt lépéseket az egyes hibák feltételéhez.

## <a name="failover-failed-with-error-id-28031"></a>A feladatátvétel a 28031-es AZONOSÍTÓJÚ hiba miatt meghiúsult

Site Recovery nem tudott létrehozni feladatátvételi virtuális gépet az Azure-ban. A következő okok egyike miatt fordulhat elő:

* Nincs elegendő kvóta a virtuális gép létrehozásához: a rendelkezésre álló kvóta az előfizetés-> használat + kvóták lehetőséggel ellenőrizhető. A kvóta növeléséhez [új támogatási kérést](https://aka.ms/getazuresupport) is megnyithat.

* Ugyanazon rendelkezésre állási csoportba tartozó különböző méretű családokból álló virtuális gépeket próbál feladatátvételre beállítani. Győződjön meg arról, hogy ugyanazon a rendelkezésre állási csoportba tartozó összes virtuális gép esetében azonos méretű családot választ. Módosítsa a méretet a virtuális gép számítási és hálózati beállításainak megadásával, majd próbálja megismételni a feladatátvételt.

* Az előfizetéshez olyan házirend van, amely megakadályozza a virtuális gép létrehozását. Módosítsa úgy a szabályzatot, hogy engedélyezze a virtuális gép létrehozását, majd próbálkozzon újra a feladatátvételsel.

## <a name="failover-failed-with-error-id-28092"></a>A feladatátvétel a 28092-es AZONOSÍTÓJÚ hiba miatt meghiúsult

Site Recovery nem tudott hálózati adaptert létrehozni a feladatátvételi virtuális géphez. Győződjön meg arról, hogy elegendő kvóta áll rendelkezésre a hálózati adapterek létrehozásához az előfizetésben. Az elérhető kvótát az előfizetés-> használat + kvóták című témakörben tekintheti meg. A kvóta növeléséhez [új támogatási kérést](https://aka.ms/getazuresupport) is megnyithat. Ha elegendő kvóta van, akkor lehet, hogy ez egy átmeneti probléma, próbálja megismételni a műveletet. Ha a probléma még az újrapróbálkozások után sem szűnik meg, akkor a dokumentum végén hagyjon megjegyzést.  

## <a name="failover-failed-with-error-id-70038"></a>A feladatátvétel a 70038-es AZONOSÍTÓJÚ hiba miatt meghiúsult

A Site Recovery nem tudott létrehozni egy feladatátvételt a klasszikus virtuális gépen az Azure-ban. Ez az alábbiak miatt fordulhat elő:

* A virtuális gép létrehozásához szükséges erőforrások egyike, például egy virtuális hálózat nem létezik. Hozza létre a virtuális hálózatot a virtuális gép számítási és hálózati beállításaiban megadott módon, vagy módosítsa a beállítást egy már létező virtuális hálózatra, majd próbálkozzon újra a feladatátvételsel.

## <a name="failover-failed-with-error-id-170010"></a>A feladatátvétel a 170010-es AZONOSÍTÓJÚ hiba miatt meghiúsult

Site Recovery nem tudott létrehozni feladatátvételi virtuális gépet az Azure-ban. Ez azért fordulhat elő, mert a hidratáció belső tevékenysége nem sikerült a helyszíni virtuális gép számára.

Ahhoz, hogy az Azure-ban bármilyen gépet felvigyen, az Azure-környezetnek a rendszerindítási indítási állapotban kell lennie, a DHCP pedig az autostart állapotban van. Így a hidratációs tevékenység a feladatátvétel időpontjában átalakítja az **ATAPI, a Intelide, a storflt, a VMBus és a storvsc illesztőprogramok** indítási típusát a rendszerindítás indítására. Emellett átalakítja a néhány szolgáltatás indítási típusát, például a DHCP-t az automatikus indításhoz. Ez a tevékenység környezet-specifikus problémák miatt sikertelen lehet. 

A **Windows vendég operációs rendszerhez** tartozó illesztőprogramok indítási típusának manuális módosításához kövesse az alábbi lépéseket:

1. [Töltse le](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) a nem hidratációs szkriptet, és futtassa a következő módon. Ez a szkript ellenőrzi, hogy a virtuális gép igényel-e hidratációt.

    `.\Script-no-hydration.ps1`

    A következő eredményt adja, ha hidratálás szükséges:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    Abban az esetben, ha a virtuális gép nem tesz eleget a hidratáció követelményének, a szkript megadja, hogy "Ez a rendszer megfelel a nem-hidratálás követelménynek". Ebben az esetben az Azure-ban és a virtuális gépen nem szükséges az összes illesztőprogram és szolgáltatás az állapotban.

2. Futtassa a nem hidratálás-set parancsfájlt az alábbiak szerint, ha a virtuális gép nem felel meg a nem-hidratáció követelménynek.

    `.\Script-no-hydration.ps1 -set`
    
    Ez a művelet átalakítja az illesztőprogramok indítási típusát, és az alábbihoz hasonló eredményt ad:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nem lehet csatlakozni/RDP/SSH-kapcsolatot létesíteni a feladatátvételi virtuális géppel a virtuális gép szürke kikapcsolási gombja miatt

Az RDP-problémákról részletes hibaelhárítási utasításokért tekintse meg a dokumentációt [itt](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).

Az SSH-problémákról részletes hibaelhárítási utasításokért tekintse meg a dokumentációt [itt](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).

Ha az Azure-ban a feladatátvételen átesett virtuális gépen a **Connect (csatlakozás** ) gomb szürkén jelenik meg, és nem csatlakozik az Azure-hoz az expressz útvonalon vagy a helyek közötti VPN-kapcsolaton keresztül, akkor

1. Nyissa meg a **virtuális gép**  >  **hálózatkezelését**, és kattintson a szükséges hálózati adapter nevére.  ![A képernyőfelvételen a hálózati csatoló nevét tartalmazó virtuális gép hálózatkezelési lapja látható.](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigáljon az **IP-konfigurációk** elemre, majd kattintson a szükséges IP-konfiguráció név mezőjére. ![Képernyőfelvétel: a hálózati adapter I p-konfigurációk lapja, amely a kiválasztott I P konfiguráció nevét tartalmazza.](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. A nyilvános IP-cím engedélyezéséhez kattintson az **Engedélyezés** gombra. ![IP-cím engedélyezése](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Kattintson a **kötelező beállítások konfigurálása**  >  **új létrehozása** lehetőségre. ![Új létrehozása](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Adja meg a nyilvános címek nevét, válassza ki az **SKU** és a **hozzárendelés** alapértelmezett beállításait, majd kattintson az **OK** gombra.
6. Most a módosítások mentéséhez kattintson a **Mentés** gombra.
7. Zárja be a paneleket, és navigáljon a virtuális gép **Áttekintés** szakaszához a kapcsolódáshoz/RDP-hez.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nem lehet csatlakozni/RDP/SSH-VM-csatlakozás gomb elérhető

Ha az Azure-ban a feladatátvételen átesett virtuális gépen a **Csatlakozás** gomb elérhető (nem szürkén jelenik meg), akkor ellenőrizze a **rendszerindítási diagnosztikát** a virtuális gépen, és ellenőrizze a hibákat a [jelen cikkben](/troubleshoot/azure/virtual-machines/boot-diagnostics)leírtak szerint.

1. Ha a virtuális gép nem indult el, próbálja meg a feladatátvételt egy régebbi helyreállítási pontra.
2. Ha a virtuális gépen belüli alkalmazás nem működik, próbálja meg végrehajtani a feladatátvételt egy alkalmazás-konzisztens helyreállítási pontra.
3. Ha a virtuális gép tartományhoz van csatlakoztatva, győződjön meg arról, hogy a tartományvezérlő pontosan működik. Ezt az alábbi lépések végrehajtásával teheti meg:

    a. Hozzon létre egy új virtuális gépet ugyanabban a hálózatban.

    b.  Győződjön meg arról, hogy képes csatlakozni ugyanahhoz a tartományhoz, amelyen a feladatátvételt végző virtuális gép várhatóan megjelenik.

    c. Ha a tartományvezérlő **nem** működik megfelelően, próbálja meg bejelentkezni a feladatátvételi virtuális gépre a helyi rendszergazdai fiók használatával.
4. Ha egyéni DNS-kiszolgálót használ, ellenőrizze, hogy elérhető-e. Ezt az alábbi lépések végrehajtásával teheti meg:

    a. Hozzon létre egy új virtuális gépet ugyanabban a hálózatban, és

    b. Ellenőrizze, hogy a virtuális gép képes-e a névfeloldásra az egyéni DNS-kiszolgáló használatával

>[!Note]
>A rendszerindítási Diagnosztikatól eltérő beállítások engedélyezéséhez a feladatátvétel előtt telepíteni kell az Azure virtuálisgép-ügynököt a virtuális gépre.

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>A soros konzol nem nyitható meg egy UEFI-alapú gép Azure-ba történő feladatátvétele után

Ha RDP használatával tud csatlakozni a géphez, de nem tudja megnyitni a soros konzolt, kövesse az alábbi lépéseket:

* Ha a gép operációs rendszere Red hat vagy Oracle Linux 7. */8.0, futtassa a következő parancsot a feladatátvételi Azure-beli virtuális gépen rendszergazdai jogosultságokkal. Indítsa újra a virtuális gépet a parancs után.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* Ha a gép operációs rendszere CentOS 7. *, futtassa a következő parancsot a feladatátvételi Azure-beli virtuális gépen rendszergazdai jogosultságokkal. Indítsa újra a virtuális gépet a parancs után.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>Váratlan leállítási üzenet (eseményazonosító 6008)

Ha egy Windows rendszerű virtuális gépet egy feladatátvétel után indít el, ha a helyreállított virtuális gépen váratlan leállítási üzenet jelenik meg, az azt jelzi, hogy a virtuális gép leállítási állapota nem lett rögzítve a feladatátvételhez használt helyreállítási pontban. Ez akkor történik meg, amikor egy olyan pontra állít helyre, amikor a virtuális gép nem lett teljesen leállítva.

Ez általában nem okoz problémát, és általában figyelmen kívül hagyható a nem tervezett feladatátvételek esetében. Ha a feladatátvételt tervezték, győződjön meg arról, hogy a virtuális gép megfelelően le van állítva a feladatátvétel előtt, és elegendő időt biztosít a helyszíni folyamatban lévő replikációs információknak az Azure-ba való elküldésekor. Ezután használja a [feladatátvételi képernyőn](site-recovery-failover.md#run-a-failover) a **legújabb** lehetőséget, hogy a rendszer az Azure-ban lévő összes függőben lévő adatfeldolgozást egy helyreállítási pontba dolgozza fel, amelyet a rendszer a virtuális gép feladatátvételéhez használ.

## <a name="unable-to-select-the-datastore"></a>Nem lehet kijelölni az adattárt

Ez a probléma akkor jelenik meg, ha nem tudja megtekinteni az adattárolót az Azure-ban a portálon, amikor a feladatátvételt észlelő virtuális gép ismételt védelemmel próbálkozik. Ennek az az oka, hogy a fő cél nem ismerhető fel olyan virtuális gépként, amelyet a vCenter a Azure Site Recoveryhoz adott hozzá.

A virtuális gépek ismételt védelemmel kapcsolatos további információkért lásd: [a gépek ismételt védetté és visszavétele a helyszíni helyre az Azure-ba történő feladatátvétel után](vmware-azure-reprotect.md).

A hiba megoldása érdekében:

Hozza létre manuálisan a fő célt a forrást kezelő vCenter. Az adattár a következő vCenter-felderítési és-frissítési háló műveletei után lesz elérhető.

> [!Note]
> 
> A felderítési és a frissítési háló műveletei akár 30 percet is igénybe vehetnek. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>A Linux Master Target regisztrációja a CS hibával meghiúsul TLS-hiba 35 

A Azure Site Recovery fő cél regisztrációja a konfigurációs kiszolgálóval meghiúsul, mert a hitelesített proxy engedélyezve van a fő célhelyen. 
 
Ezt a hibát a következő karakterláncok jelzik a telepítési naplóban: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

A hiba megoldása érdekében:
 
1. A konfigurációs kiszolgáló virtuális gépen nyisson meg egy parancssort, és ellenőrizze a proxy beállításait a következő parancsokkal:

    Cat/etc/Environment echo $http _proxy echo $https _proxy 

2. Ha az előző parancsok kimenete azt mutatja, hogy a http_proxy vagy https_proxy beállítások vannak meghatározva, használja a következő módszerek egyikét a fő célként megadott kommunikáció feloldásához a konfigurációs kiszolgálóval:
   
   - Töltse le a [PsExec eszközt](/sysinternals/downloads/psexec).
   - Az eszközzel elérheti a rendszer felhasználói környezetét, és meghatározhatja, hogy a proxy címe konfigurálva van-e. 
   - Ha a proxy konfigurálva van, nyissa meg az IE-t a rendszer felhasználói környezetében a PsExec eszköz használatával.
  
     **PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Ellenőrizze, hogy a fő célkiszolgáló tud-e kommunikálni a konfigurációs kiszolgálóval:
  
     - Módosítsa a proxybeállításokat az Internet Explorerben, hogy a fő célkiszolgáló IP-címét kihagyja a proxyn keresztül.   
     Vagy
     - Tiltsa le a proxyt a fő célkiszolgálón. 


## <a name="next-steps"></a>Következő lépések
- [Windows rendszerű virtuális gép RDP-kapcsolatának](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection) hibáinak megoldása
- Linux rendszerű [virtuális géppel létesített SSH-kapcsolatok](/troubleshoot/azure/virtual-machines/detailed-troubleshoot-ssh-connection) hibáinak megoldása

Ha további segítségre van szüksége, tegye közzé a lekérdezését a [Microsoft Q&egy kérdés oldalát, site Recovery](/answers/topics/azure-site-recovery.html) vagy hagyjon megjegyzést a dokumentum végén. Van egy aktív közösségünk, amelynek segítenie kell a segítségét.
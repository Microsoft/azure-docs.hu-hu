---
title: Azure Migrate replikációs berendezés
description: Ismerje meg az Azure Migrate VMware-migráláshoz szükséges replikációs berendezést.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 5f63b033c3995932662fc9b68c1397bf57b0326e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714973"
---
# <a name="replication-appliance"></a>Replikációs berendezés

Ez a cikk a [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz VMware virtuális gépek, fizikai gépek és magán-/nyilvános felhőbeli virtuális gépek Azure-ba történő ügynökalapú migrálása során használt replikációs berendezést ismerteti. 


## <a name="overview"></a>Áttekintés

A replikációs berendezés a VMware virtuális gépek vagy fizikai kiszolgálók ügynökalapú migrálásának beállításakor lesz üzembe állítva. Egyetlen helyszíni gépként, VMware virtuális gépként vagy fizikai kiszolgálóként van üzembe helyezni. A következőt futtatja:

- **Replikációs berendezés:** A replikációs berendezés koordinálja a kommunikációt, és kezeli az adatreplikációt az Azure-ba replikált helyszíni VMware virtuális gépek és fizikai kiszolgálók számára.
- **Folyamatkiszolgáló:** A folyamatkiszolgáló, amely alapértelmezés szerint telepítve van a replikációs berendezésen, és a következőket teszi:
    - **Replikációs átjáró:** Replikációs átjáróként működik. Replikációs adatokat fogad a replikációra engedélyezett gépekről. Gyorsítótárazást, tömörítést és titkosítást használva optimalizálja a replikációs adatokat, és elküldi az Azure-nak.
    - **Ügynök telepítője:** Végrehajtja a mobilitási szolgáltatás leküldéses telepítését. Ennek a szolgáltatásnak telepítve kell lennie és futnia kell minden olyan helyszíni gépen, amely áttelepítéshez replikálni szeretné.

## <a name="appliance-deployment"></a>Berendezés üzembe helyezése

**Alkalmazási cél** | **Részletek**
--- |  ---
**VMware virtuálisgép-ügynökalapú migrálás** | Töltse le az OVA-sablont a Azure Migrate hubról, majd importálja a vCenter Server a berendezés virtuális gépének létrehozásához.
**Fizikaigép-ügynök alapú áttelepítés** | Ha nem VMware-infrastruktúrával, vagy ha nem tud VMware virtuális gépet létrehozni OVA-sablonnal, töltse le a szoftvertelepítőt a Azure Migrate Hubról, és futtassa a berendezés gépének beállításához.

> [!NOTE]
> Ha az üzembe helyezést a Azure Government használja a telepítőfájlt a replikációs berendezés üzembe helyezéséhez.

## <a name="appliance-requirements"></a>Berendezéskövetelmények

Amikor beállít egy replikációs berendezést a Azure Migrate Hubban megadott OVA-sablonnal, a berendezés a Windows Server 2016 rendszert futtatja, és megfelel a támogatási követelményeknek. Ha manuálisan beállít egy replikációs berendezést egy fizikai kiszolgálón, győződjön meg arról, hogy az megfelel a követelményeknek.

**Összetevő** | **Követelmény**
--- | ---
 | **VMware virtuálisgép-berendezés**
PowerCLI | Ha a replikációs berendezés VMware virtuális gépen fut, a [PowerCLI 6.0-s](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) verzióját kell telepíteni.
Hálózati adapter típusa | VMXNET3 (ha a berendezés VMware virtuális gép)
 | **Hardverbeállítások**
Processzormagok | 8
RAM | 16 GB
Lemezek száma | Három: Az operációsrendszer-lemez, a folyamatkiszolgáló gyorsítótárlemeze és a megőrzési meghajtó.
Szabad lemezterület (gyorsítótár) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
**Szoftverbeállítások** |
Operációs rendszer | Windows Server 2016 vagy Windows Server 2012 R2
Licenc | A berendezéshez Egy Windows Server 2016 értékelési licenc is jár, amely 180 napig érvényes.<br/><br/> Ha a kiértékelési időszak hamarosan lejár, javasoljuk, hogy töltsön le és helyezzen üzembe egy új berendezést, vagy aktiválja a berendezés virtuális gépének operációsrendszer-licencét.
Operációs rendszer területi beállítása | Angol (en-us)
TLS | Engedélyezni kell a TLS 1.2-t.
.NET-keretrendszer | .NET-keretrendszer 4.6-os vagy újabb rendszernek kell telepítve lennie a gépen (erős titkosítás engedélyezése esetén).
MySQL | A MySQL-t telepíteni kell a berendezésre.<br/> Telepíteni kell a MySQL-t. Telepíthet manuálisan, vagy Site Recovery a berendezés üzembe helyezése során.
Egyéb alkalmazások | Ne futtason más alkalmazásokat a replikációs berendezésen.
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssorhoz való hozzáférés megakadályozása. <br> – A beállításjegyzék szerkesztőeszközéhez való hozzáférés megakadályozása. <br> – A fájlmellékletek megbízhatósági logikája. <br> – Kapcsolja be a szkriptvégrehajtást. <br> [További információ](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | – Nincs meglévő alapértelmezett webhely <br> – A 443-as porton nem figyel egy meglévő webhely vagy alkalmazás sem <br>- Névtelen  [hitelesítés engedélyezése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - [FastCGI-beállítás](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) engedélyezése
**Hálózati beállítások** |
IP-cím típusa | Statikus
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)
Hálózati adapter típusa | VMXNET3

## <a name="mysql-installation"></a>A MySQL telepítése 

A MySQL-t telepíteni kell a replikációs berendezés gépére. Az alábbi módszerek egyikével telepíthető.

**Metódus** | **Részletek**
--- | ---
Manuális letöltés és telepítés | Töltse le a MySQL & a C:\Temp\ASRSetup mappába, majd telepítse manuálisan.<br/> A berendezés beállításakor a MySQL már telepítettként fog mutatni.
Online letöltés nélkül | Helyezze a MySQL telepítőalkalmazást a C:\Temp\ASRSetup mappába. Amikor telepíti a berendezést, és kiválasztja a MySQL letöltését és telepítését, a telepítő a hozzáadott telepítőt fogja használni.
Letöltés és telepítés a Azure Migrate | Amikor telepíti a berendezést, és a rendszer kéri a MySQL-t, válassza a **Letöltés és telepítés lehetőséget.**

## <a name="url-access"></a>URL-hozzáférés

A replikációs berendezésnek hozzá kell férni ezekhez az URL-címekhez az Azure nyilvános felhőben.

**URL-cím** | **Részletek**
--- | ---
\*.backup.windowsazure.com | Replikált adatátvitelhez és koordinációhoz használatos
\*.store.core.windows.net | Replikált adatátvitelhez és koordinációhoz használatos
\*.blob.core.windows.net | A replikált adatokat tároló tárfiók elérésére használatos
\*.hypervrecoverymanager.windowsazure.com | Replikációkezelési műveletekhez és koordinációhoz használatos
https:\//management.azure.com | Replikációkezelési műveletekhez és koordinációhoz használatos
*.services.visualstudio.com | Naplózási célokra használható (nem kötelező)
time.windows.com | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | A berendezés beállításának hozzá kell férni ezekhez az URL-címekhez. Hozzáférés-vezérlésre és identitáskezelésre használhatók Azure Active Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | A MySQL letöltésének befejezéséhez. Néhány régióban előfordulhat, hogy a letöltés a CDN URL-címére lesz átirányítva. Szükség esetén győződjön meg arról, hogy a CDN URL-címe is engedélyezve van.


## <a name="azure-government-url-access"></a>Azure Government URL-cím elérése

A replikációs berendezésnek hozzá kell férni ezekhez az URL-címekhez a Azure Government.

**URL-cím** | **Részletek**
--- | ---
\*.backup.windowsazure.us | Replikált adatátvitelhez és koordinációhoz használatos
\*.store.core.windows.net | Replikált adatátvitelhez és koordinációhoz használatos
\*.blob.core.windows.net | A replikált adatokat tároló tárfiók elérésére használatos
\*.hypervrecoverymanager.windowsazure.us | Replikációkezelési műveletekhez és koordinációhoz használatos
https:\//management.usgovcloudapi.net | Replikációkezelési műveletekhez és koordinációhoz használatos
*.services.visualstudio.com | Naplózási célokra használható (nem kötelező)
time.nist.gov | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | A berendezés OVA-val való beállításához hozzá kell férni ezekhez az URL-címekhez. Hozzáférés-vezérlésre és identitáskezelésre használhatók a Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | A MySQL letöltésének befejezéséhez. Néhány régióban előfordulhat, hogy a rendszer átirányítja a letöltést a CDN URL-címére. Szükség esetén győződjön meg arról, hogy a CDN URL-címe is engedélyezve van.  

>[!Note]
>
> Ha a Migrate projekt privát végpontkapcsolattal rendelkezik, a következő URL-címekhez kell hozzáférnie privát kapcsolati hozzáférésen keresztül és felett:   
> - *.blob.core.windows.com – A replikált adatokat tároló tárfiók elérése. Ez nem kötelező, és nem szükséges, ha a tárfiókhoz privát végpont van csatlakoztatva. 
> - https: \/ /management.azure.com replikációkezelési műveletekhez és koordinációhoz. 
>- https:\//login.microsoftonline.com <br/>https:\//login.windows.net <br/> https: \/ /www.live.com _és_ <br/> https: \/ /www.microsoft.com hozzáférés-vezérléshez és identitáskezeléshez Azure Active Directory

## <a name="port-access"></a>Port elérése

**Eszköz** | **Kapcsolat**
--- | ---
Virtuális gépek | A Mobility szolgáltatás virtuális gépeken futó virtuális gépek a 443-as bejövő HTTPS-porton kommunikálnak a helyszíni replikációs beberendezéssel (konfigurációs kiszolgálóval) a replikáció kezelése érdekében.<br/><br/> A virtuális gépek a 9443-as bejövő HTTPS-porton keresztül küldik el a (konfigurációs kiszolgálón futó) folyamatkiszolgálónak a replikációs adatokat. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés a 443-as kimenő HTTPS-porton keresztül vezényli a replikációt az Azure-ral.
Folyamatkiszolgáló | A folyamatkiszolgáló fogadja a replikációs adatokat, optimalizálja és titkosítja őket, majd a 443-as kimenő porton keresztül elküldi őket az Azure Storage-ba.<br/> Alapértelmezés szerint a folyamatkiszolgáló a replikációs berendezésen fut.


## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi a virtuális gép replikációját, megkezdődik a kezdeti replikáció az Azure Storage-ba a megadott replikációs szabályzat használatával. 
2. A forgalom az Azure Storage nyilvános végpontjaira replikálódik az interneten keresztül. A helyszíni helyről az Azure-ba nem lehet replikálni a forgalmat egy helyszíni virtuális magánhálózaton (VPN) keresztül.
3. A kezdeti replikáció befejeződése után megkezdődik a változásreplikáció. A rendszer naplózza a gép nyomon követését.
4. A kommunikáció a következőképpen történik:
    - A virtuális gépek a replikációs berendezéssel a 443-as bejövő HTTPS-porton kommunikálnak a replikáció kezelése érdekében.
    - A replikációs berendezés a 443-as kimenő HTTPS-porton keresztül vezényli a replikációt az Azure-ral.
    - A virtuális gépek a 9443-as bejövő HTTPS-porton keresztül küldik el a (replikációs berendezésen futó) folyamatkiszolgálónak a replikációs adatokat. Ez a port módosítható.
    - A folyamatkiszolgáló fogadja a replikációs adatokat, optimalizálja és titkosítja őket, majd a 443-as kimenő porton keresztül elküldi őket az Azure Storage-nak.
5. A replikációs adatnaplók először egy gyorsítótár-tárfiókba belépnek az Azure-ban. A rendszer feldolgozja ezeket a naplókat, és az adatokat egy Azure-beli felügyelt lemezen tárolja.

![A replikációs folyamat architektúráját bemutató ábra.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Berendezésfrissítések

A berendezést a rendszer manuálisan frissíti a Azure Migrate hubról. Javasoljuk, hogy mindig a legújabb verziót futtassa.

1. A Azure Migrate > kiszolgálók > Azure Migrate: Server Assessment, Infrastruktúra-kiszolgálók területén válassza a **Konfigurációs kiszolgálók lehetőséget.**
2. A **Konfigurációs kiszolgálók** részen megjelenik egy hivatkozás az Ügynök **verziója** között, ha a replikációs berendezés új verziója érhető el. 
3. Töltse le a telepítőt a replikációs berendezés gépére, és telepítse a frissítést. A telepítő észleli a berendezésen futó aktuális verziót.
 
## <a name="next-steps"></a>Következő lépések

- [Ismerje meg,](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) hogyan állíthatja be a replikációs berendezést ügynökalapú VMware virtuális gépek migráláshoz.
- [Ismerje meg,](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) hogyan állíthatja be a replikációs berendezést fizikai kiszolgálókhoz.

---
title: Azure Migrate replikációs berendezés
description: Ismerje meg az ügynök-alapú VMWare-áttelepítés Azure Migrate replikációs berendezését.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754096"
---
# <a name="replication-appliance"></a>Replikációs berendezés

Ez a cikk a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz által használt replikációs készüléket ismerteti, ha VMWare virtuális gépeket, fizikai gépeket és magán-vagy nyilvános Felhőbeli virtuális gépeket telepít át az Azure-ba az ügynök-alapú áttelepítés használatával. 


## <a name="overview"></a>Áttekintés

A rendszer üzembe helyezi a replikációs készüléket a VMware virtuális gépek vagy fizikai kiszolgálók ügynök-alapú áttelepítésének beállításakor. A rendszer egyetlen helyszíni gépként van üzembe helyezve VMware virtuális gépként vagy fizikai kiszolgálóként. A következőket futtatja:

- **Replikációs berendezés**: a replikációs berendezés koordinálja a kommunikációt, és felügyeli az adatreplikációt a helyszíni VMWare virtuális gépek és az Azure-ba replikált fizikai kiszolgálók esetében.
- **Process Server**: a folyamat kiszolgálója, amely alapértelmezés szerint telepítve van a replikációs berendezésen, és a következő műveleteket végzi el:
    - **Replikációs átjáró**: replikációs átjáróként működik. Replikációs adatok fogadása a replikációhoz engedélyezett gépekről. A replikációs adatokat a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és elküldi az Azure-nak.
    - **Ügynök telepítője**: a mobilitási szolgáltatás leküldéses telepítését végzi. Ezt a szolgáltatást minden olyan helyszíni gépen telepíteni és futtatni kell, amelyet át szeretne replikálni az áttelepítéshez.

## <a name="appliance-deployment"></a>Berendezések üzembe helyezése

**Alkalmazási cél** | **Részletek**
--- |  ---
**VMware VM-ügynök alapú áttelepítés** | A Azure Migrate központból letöltheti a petesejtek sablonját, és importálhatja a vCenter Serverba a készülék virtuális gépe létrehozásához.
**Fizikai gépi ügynök alapú áttelepítés** | Ha nem rendelkezik VMware-infrastruktúrával, vagy ha nem hoz létre VMware-alapú virtuális gépet egy PETESEJT-sablonnal, letölt egy szoftvert a Azure Migrate központból, és futtathatja a készülék számítógépének beállításához.

> [!NOTE]
> Ha Azure Government telepíti a rendszert, a telepítési fájllal telepítheti a replikációs berendezést.

## <a name="appliance-requirements"></a>Készülékre vonatkozó követelmények

Amikor beállítja a replikációs berendezést az Azure Migrate központban megadott petesejtek sablonnal, a készülék futtatja a Windows Server 2016-et, és megfelel a támogatási követelményeknek. Ha a replikációs készüléket manuálisan állítja be egy fizikai kiszolgálón, akkor ellenőrizze, hogy az megfelel-e a követelményeknek.

**Összetevő** | **Követelmény**
--- | ---
 | **VMware VM-készülék**
PowerCLI | A [PowerCLI 6,0-es verziójának](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) telepítve kell lennie, ha a replikációs berendezés VMWare virtuális gépen fut.
Hálózati adapter típusa | VMXNET3 (ha a készülék VMware virtuális gép)
 | **Hardverbeállítások**
Processzormagok | 8
RAM | 16 GB
Lemezek száma | Három: az operációsrendszer-lemez, a Process Server cache lemez és a megőrzési meghajtó.
Szabad lemezterület (gyorsítótár) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
**Szoftverbeállítások** |
Operációs rendszer | Windows Server 2016 vagy Windows Server 2012 R2
Licenc | A készülékhez tartozik egy Windows Server 2016 próbaverziós licenc, amely 180 napig érvényes.<br/><br/> Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.
Operációs rendszer területi beállítása | Angol (en-us)
TLS | A TLS 1,2-et engedélyezni kell.
.NET-keretrendszer | A .NET-keretrendszer 4,6-es vagy újabb verziójának telepítve kell lennie a gépen (erős kriptográfiai támogatással.
MySQL | A MySQL-t telepíteni kell a készülékre.<br/> Telepíteni kell a MySQL-t. Manuálisan is telepítheti, vagy Site Recovery telepítheti a készülék telepítése során.
Egyéb alkalmazások | Ne futtasson más alkalmazásokat a replikációs berendezésen.
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssor elérésének tiltása. <br> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br> – A fájlmellékletek megbízhatósági logikája. <br> – A parancsfájlok végrehajtásának bekapcsolása. <br> [További információ](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | – Nincs előre meglévő alapértelmezett webhely <br> – Nincs már meglévő webhely/alkalmazás a 443-es porton <br>– [Névtelen hitelesítés](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) engedélyezése <br> – [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -beállítás engedélyezése
**Hálózati beállítások** |
IP-cím típusa | Statikus
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)
Hálózati adapter típusa | VMXNET3

## <a name="mysql-installation"></a>MySQL-telepítés 

A MySQL-t telepíteni kell a replikációs berendezést futtató gépre. Ezt a metódusok egyikével telepítheti.

**Metódus** | **Részletek**
--- | ---
Manuális letöltés és telepítés | Töltse le a MySQL-alkalmazást & helyezze a mappába a C:\Temp\ASRSetup, majd telepítse manuálisan.<br/> A készülék a MySQL beállítása után már telepítve lesz.
Online letöltés nélkül | Helyezze a MySQL Installer alkalmazást a C:\Temp\ASRSetup. mappába. Ha telepíti a készüléket, és rákattint a MySQL letöltésére és telepítésére, a telepítő a hozzáadott telepítőt fogja használni.
Letöltés és telepítés Azure Migrate | Ha telepíti a készüléket, és a rendszer kéri a MySQL-t, válassza a **letöltés és telepítés** lehetőséget.

## <a name="url-access"></a>URL-hozzáférés

A replikációs berendezésnek hozzá kell férnie ezekhez az URL-címekhez az Azure nyilvános felhőben.

**URL-cím** | **Részletek**
--- | ---
\*.backup.windowsazure.com | A replikált adatátvitelhez és a koordinációhoz használatos
\*.store.core.windows.net | A replikált adatátvitelhez és a koordinációhoz használatos
\*.blob.core.windows.net | A replikált adattárban tárolt Storage-fiók elérésére szolgál
\*.hypervrecoverymanager.windowsazure.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
https:\//management.azure.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
*.services.visualstudio.com | Telemetria célra használatos (opcionális)
time.windows.com | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.Live.com <br/> https: \/ /Graph.Windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.Live.com <br/> https: \/ /www.microsoft.com  | A készülék beállításához hozzá kell férnie az URL-címekhez. A hozzáférés-vezérléshez és az identitások kezeléséhez használják Azure Active Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | A MySQL letöltésének befejezéséhez. Néhány régióban a letöltés átirányítva lesz a CDN URL-címére. Szükség esetén győződjön meg arról, hogy a CDN URL-címe is engedélyezett.


## <a name="azure-government-url-access"></a>URL-hozzáférés Azure Government

A replikációs berendezésnek hozzá kell férnie Azure Government-beli URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
\*. backup.windowsazure.us | A replikált adatátvitelhez és a koordinációhoz használatos
\*.store.core.windows.net | A replikált adatátvitelhez és a koordinációhoz használatos
\*.blob.core.windows.net | A replikált adattárban tárolt Storage-fiók elérésére szolgál
\*. hypervrecoverymanager.windowsazure.us | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
https:\//management.usgovcloudapi.net | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
*.services.visualstudio.com | Telemetria célra használatos (opcionális)
time.nist.gov | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.Live.com <br/> https: \/ /Graph.Windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.Live.com <br/> https: \/ /www.microsoft.com  | A készüléknek a PETESEJTekkel való beállításához hozzáféréssel kell rendelkeznie az URL-címekhez. A hozzáférés-vezérléshez és az identitáskezeléshoz használják Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | A MySQL letöltésének befejezéséhez. Néhány régióban a letöltés átirányítva lesz a CDN URL-címére. Szükség esetén győződjön meg arról, hogy a CDN URL-címe is engedélyezett.

## <a name="port-access"></a>Port-hozzáférés

**Eszköz** | **Kapcsolat**
--- | ---
Virtuális gépek | A virtuális gépeken futó mobilitási szolgáltatás a replikációs felügyelet érdekében a HTTPS 443 bejövő porton keresztül kommunikál a helyszíni replikációs berendezéssel (konfigurációs kiszolgálóval).<br/><br/> A virtuális gépek replikációs adatküldést küldenek a folyamat-kiszolgálónak (amely a konfigurációs kiszolgáló gépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
Folyamatkiszolgáló | A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.<br/> Alapértelmezés szerint a Process Server fut a replikációs berendezésen.


## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi a replikációt egy virtuális géphez, a kezdeti replikáció az Azure Storage-ba megkezdődik a megadott replikációs házirend használatával. 
2. A forgalom az Azure Storage nyilvános végpontjait az interneten keresztül replikálja. A helyek közötti virtuális magánhálózati (VPN) kapcsolaton keresztüli replikálása nem támogatott a helyszíni helyről az Azure-ba.
3. A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A rendszer naplózza a gép nyomon követett módosításait.
4. A kommunikáció a következőképpen történik:
    - A virtuális gépek a replikálás kezeléséhez a HTTPS 443 bejövő porton keresztül kommunikálnak a replikációs berendezéssel.
    - A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
    - A virtuális gépek replikációs adatküldést küldenek a (replikációs berendezésen futó) folyamat-kiszolgálónak a HTTPS 9443 bejövő porton. Ez a port módosítható.
    - A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.
5. A replikációs adatnaplók először egy gyorsítótárbeli Storage-fiókba helyezik az Azure-ban. A rendszer feldolgozza ezeket a naplókat, és az adattárolást egy Azure-beli felügyelt lemez tárolja.

![A diagram a replikálási folyamat architektúráját mutatja.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Berendezések frissítése

A készüléket manuálisan frissítheti az Azure Migrate hub-ból. Javasoljuk, hogy mindig futtassa a legújabb verziót.

1. Azure Migrate > kiszolgálók > Azure Migrate: kiszolgáló-értékelés, infrastruktúra-kiszolgálók, kattintson a **konfigurációs kiszolgálók** elemre.
2. A **konfigurációs kiszolgálók** területen megjelenik egy hivatkozás az **ügynök verziójában** , ha a replikációs berendezés új verziója érhető el. 
3. Töltse le a telepítőt a replikációs berendezést végző gépre, és telepítse a frissítést. A telepítő észleli a készüléken futó aktuális verziót.
 
## <a name="next-steps"></a>Következő lépések

- [Ismerje meg, hogyan](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) állíthatja be a replikációs berendezést az ügynök-alapú VMWare virtuális gépek áttelepítéséhez.
- [Ismerje meg, hogyan](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) állíthatja be a replikációs berendezést a fizikai kiszolgálók számára.

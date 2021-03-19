---
title: IP-címzés beállítása a feladatátvétel után egy másodlagos helyre Azure Site Recovery
description: Ismerteti, hogyan lehet IP-címzést beállítani a virtuális gépekhez való csatlakozáshoz egy másodlagos helyszíni helyen a vész-helyreállítás és a Azure Site Recovery használatával történő feladatátvétel után.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 43942c20a353ff69383f3e721679e4c95ab9d230
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87495943"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>IP-címzés beállítása másodlagos helyszíni helyhez való kapcsolódáshoz a feladatátvétel után

Ha System Center Virtual Machine Manager-(VMM-) felhőkben lévő Hyper-V virtuális gépek feladatátvételét egy másodlagos helyre hajtja végre, akkor a replika virtuális gépekhez csatlakoznia kell. Ez a cikk segítséget nyújt ennek elvégzésében. 

## <a name="connection-options"></a>Kapcsolatok beállításai

A feladatátvételt követően több módon is kezelheti a replika virtuális gépek IP-címzését: 

- **Azonos IP-cím megőrzése feladatátvétel után**: ebben a forgatókönyvben a REPLIKÁLt virtuális gépnek ugyanaz az IP-címe, mint az elsődleges virtuális gép. Ez leegyszerűsíti a hálózattal kapcsolatos problémákat a feladatátvétel után, de némi infrastrukturális munkát igényel.
- **Másik IP-cím használata feladatátvétel után**: ebben a forgatókönyvben a virtuális gép új IP-címet kap a feladatátvétel után. 
 

## <a name="retain-the-ip-address"></a>Az IP-cím megőrzése

Ha meg szeretné őrizni az elsődleges hely IP-címeit, a másodlagos helyre történő feladatátvétel után a következőket teheti:

- Helyezzen üzembe egy kifeszített alhálózatot az elsődleges és a másodlagos helyek között.
- Végezzen el egy teljes alhálózat-feladatátvételt az elsődlegesről a másodlagos helyre. Az IP-címek új helyének jelzéséhez frissítenie kell az útvonalakat.


### <a name="deploy-a-stretched-subnet"></a>Kiterjesztett alhálózat üzembe helyezése

A kiterjesztett konfigurációban az alhálózat egyszerre érhető el az elsődleges és a másodlagos helyeken is. Egy kihelyezett alhálózat esetében, amikor a gépet és annak IP-címét (3. réteg) a másodlagos helyre helyezi, a hálózat automatikusan átirányítja a forgalmat az új helyre. 

- A 2. rétegbeli (adatkapcsolati réteg) perspektívából olyan hálózati berendezések szükségesek, amelyek kezelhetik a kihelyezett VLAN-t.
- A VLAN nyújtásával a lehetséges tartalék tartomány mindkét helyre kiterjed. Ez egyetlen meghibásodási pont lesz. Nem valószínű, hogy ilyen esetben előfordulhat, hogy nem tudja elkülöníteni az incidenseket, például a szórási vihart. 


### <a name="fail-over-a-subnet"></a>Alhálózat feladatátvétele

A teljes alhálózat feladatátvételével megszerezheti a kiterjesztett alhálózat előnyeit anélkül, hogy ténylegesen el kellene terjeszteni. Ebben a megoldásban egy alhálózat elérhető a forrás-vagy célhelyen, de egyszerre nem.

- Az IP-címtartomány feladatátvétel esetén történő fenntartásához programozott módon megszervezheti, hogy az útválasztó infrastruktúra az alhálózatokat egyik helyről a másikra helyezze át.
- Feladatátvétel esetén az alhálózatok a társított virtuális gépekkel együtt mozognak.
- Ennek a megközelítésnek a fő hátránya, hogy meghibásodás esetén át kell helyeznie a teljes alhálózatot.

#### <a name="example"></a>Példa

Íme egy példa a teljes alhálózat feladatátvételre. 

- A feladatátvétel előtt az elsődleges hely a 192.168.1.0/24 alhálózaton futó alkalmazásokkal rendelkezik.
- A feladatátvétel során az ebben az alhálózatban lévő összes virtuális gép átveszi a feladatokat a másodlagos helyre, és megtartja az IP-címüket. 
- Az összes hely közötti útvonalakat úgy kell módosítani, hogy az tükrözze azt a tényt, hogy a 192.168.1.0/24 alhálózaton lévő összes virtuális gép most már át lett helyezve a másodlagos helyre.

Az alábbi ábrák a feladatátvétel előtt és után ábrázolják az alhálózatokat.


**Feladatátvétel előtt**

![Az alhálózatokat bemutató diagram a feladatátvétel előtt.](./media/hyper-v-vmm-networking/network-design2.png)

**Feladatátvétel után**

![A feladatátvételt követő alhálózatokat bemutató diagram.](./media/hyper-v-vmm-networking/network-design3.png)

A feladatátvételt követően a Site Recovery IP-címet foglal le a virtuális gép minden hálózati adapteréhez. A cím a megfelelő hálózat statikus IP-címkészlet alapján van lefoglalva minden virtuálisgép-példányhoz.

- Ha a másodlagos helyen lévő IP-címkészlet megegyezik a forrás helyével, a Site Recovery a replika virtuális géphez ugyanazt az IP-címet (a forrás virtuális gép) foglalja le. Az IP-cím le van foglalva a VMM-ben, de nincs beállítva feladatátvételi IP-címként a Hyper-V-gazdagépen. A Hyper-v-gazdagép feladatátvételi IP-címe közvetlenül a feladatátvétel előtt van beállítva.
- Ha ugyanaz az IP-cím nem érhető el, Site Recovery lefoglal egy másik elérhető IP-címet a készletből.
- Ha a virtuális gépek DHCP-t használnak, Site Recovery nem kezeli az IP-címeket. Ellenőriznie kell, hogy a másodlagos helyen lévő DHCP-kiszolgáló a forrás helyével megegyező tartományból tud-e címeket kiosztani.

### <a name="validate-the-ip-address"></a>Az IP-cím ellenőrzése

A virtuális gép védelmének engedélyezése után a következő minta parancsfájl segítségével ellenőrizheti a virtuális géphez rendelt címeket. Ez az IP-cím a feladatátvételi IP-címként van beállítva, és a virtuális géphez a feladatátvételkor van hozzárendelve:

```powershell
$vm = Get-SCVirtualMachine -Name <VM_NAME>
$na = $vm[0].VirtualNetworkAdapters>
$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
$ip.address
```

## <a name="use-a-different-ip-address"></a>Másik IP-cím használata

Ebben az esetben a feladatátvétel alatt álló virtuális gépek IP-címei módosulnak. A megoldás hátránya a szükséges karbantartás.  Előfordulhat, hogy frissíteni kell a DNS-és a gyorsítótár-bejegyzéseket. Ez állásidőt eredményezhet, amely a következőképpen enyhíthető:

- Használjon kis TTL-értékeket az intranetes alkalmazásokhoz.
- A DNS-kiszolgáló időben történő frissítéséhez használja a következő parancsfájlt egy Site Recovery helyreállítási tervben. Dinamikus DNS-regisztráció használata esetén nincs szükség a parancsfájlra.

    ```powershell
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Példa 

Ebben a példában különböző IP-címek találhatók az elsődleges és másodlagos helyeken, és létezik egy harmadik hely, ahonnan az elsődleges vagy a helyreállítási helyen futó alkalmazások elérhetők.

- A feladatátvétel előtt az alkalmazások az elsődleges helyen 192.168.1.0/24 alhálózatot üzemeltetnek.
- A feladatátvételt követően az alkalmazások a másodlagos helyen lévő 172.16.1.0/24 alhálózaton vannak konfigurálva.
- Mindhárom hely elérheti egymást.
- A feladatátvételt követően a rendszer visszaállítja az alkalmazásokat a helyreállítási alhálózatban.
- Ebben a forgatókönyvben nem szükséges a teljes alhálózat feladatátvétele, és nincs szükség módosításra a VPN-vagy hálózati útvonalak újrakonfigurálásához. A feladatátvételt és néhány DNS-frissítést, győződjön meg arról, hogy az alkalmazások továbbra is elérhetők maradnak.
- Ha a DNS a dinamikus frissítések engedélyezésére van konfigurálva, akkor a virtuális gépek az új IP-cím használatával regisztrálják magukat, amikor a feladatátvétel után indulnak.

**Feladatátvétel előtt**

![A feladatátvétel előtt különböző IP-címeket bemutató diagram.](./media/hyper-v-vmm-networking/network-design10.png)

**Feladatátvétel után**

![A feladatátvételt követő különböző IP-címeket bemutató diagram.](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Következő lépések

[Feladatátvétel futtatása](hyper-v-vmm-failover-failback.md)


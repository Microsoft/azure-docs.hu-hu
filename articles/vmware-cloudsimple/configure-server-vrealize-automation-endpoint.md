---
title: Azure VMware Solutions (AVS) – vCenter beállítása egy AVS privát felhőben a vRealize Automation szolgáltatáshoz
description: Leírja, hogyan állíthat be VMware vCenter-kiszolgálót az AVS Private-felhőben a VMware vRealize Automation-végpontként
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 41106498594ac05b944323e5f5e63de739aedf37
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024840"
---
# <a name="set-up-vcenter-on-your-avs-private-cloud-for-vmware-vrealize-automation"></a>VCenter beállítása az AVS Private Cloud-on a VMware vRealize Automation szolgáltatáshoz

A VMware vCenter-kiszolgálót beállíthatja az AVS Private-felhőben a VMware vRealize Automation végpontjának.

## <a name="before-you-begin"></a>Előzetes teendők

Végezze el ezeket a feladatokat a vCenter-kiszolgáló konfigurálása előtt:

* Konfiguráljon egy [helyek közötti VPN-kapcsolatot](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni környezet és az AVS Private Cloud között.
* [Konfigurálja a helyi DNS-kérések DNS-továbbítását](on-premises-dns-setup.md) az AVS Private Cloud DNS-kiszolgálóira.
* A következő táblázatban felsorolt engedélyek készletével hozzon létre egy [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a vRealize Automation-IaaS létrehozásához.

| Attribútum értéke | Engedély |
------------ | ------------- |  
| Datastore |  Tárterület foglalása <br> Adattár tallózása |
| Adattár-fürt | Adattár-fürt konfigurálása |
| Mappa | Mappa létrehozása <br>Mappa törlése |
| Globális |  Egyéni attribútumok kezelése<br>Egyéni attribútum beállítása |
| Network (Hálózat) | Hálózat kiosztása |
| Engedélyek | Engedélyek módosítása |
| Erőforrás | Virtuális gép erőforrás-készlethez rendelése<br>Virtuális gép migrálása<br>Migrálás virtuális gépen |
| Virtuális gépek leltározása |  Létrehozás meglévőből<br>Új létrehozása<br>Áthelyezés<br>Eltávolítás | 
| Virtuális gép interakciója |  CD-adathordozó konfigurálása<br>Konzol interakciója<br>Eszköz csatlakoztatása<br>Kikapcsolás<br>Bekapcsolás<br>Alaphelyzetbe állítás<br>Felfüggesztheti<br>Eszközök telepítése | 
| Virtuális gép konfigurációja |  Meglévő lemez hozzáadása<br>Új lemez hozzáadása<br>Hozzáadás vagy eltávolítás<br>Lemez eltávolítása<br>Extra szintű<br>CPU-szám módosítása<br>Erőforrás módosítása<br>Virtuális lemez kiterjesztése<br>Lemez Change Tracking<br>Memória<br>Eszközbeállítások módosítása<br>Átnevezés<br>Jegyzet beállítása (5,0-es és újabb verziók)<br>Beállítások<br>Swapfile elhelyezése |
| Kiépítés |  Testreszabás<br>Sablon klónozása<br>Virtuális gép klónozása<br>Sablon üzembe helyezése<br>Testreszabási specifikációk olvasása |
| Virtuális gép állapota | Pillanatkép létrehozása<br>Pillanatkép eltávolítása<br>Helyreállítás pillanatképre |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>A vRealize Automation telepítése a helyszíni környezetben

1. Jelentkezzen be a vRealize Automation IaaS-kiszolgáló berendezésbe az AVS-támogatást létrehozó IaaS-rendszergazdaként.
2. Telepítsen egy vSphere-ügynököt a vRealize Automation-végponthoz.
    1. Nyissa meg a https://*VRA-URL*: 5480/Installer címet, ahol a *VRA-URL-* cím az a URL-cím, amelyet a vRealize Automation felügyeleti felhasználói felületének eléréséhez használ.
    2. A telepítő letöltéséhez kattintson a **IaaS-telepítőre** .<br>
    A telepítési fájl elnevezési konvenciója setup_*VRA-URL-* @5480.exe.
    3. Futtassa a telepítőt. Az üdvözlő képernyőn kattintson a **tovább**gombra.
    4. Fogadja el a LICENCSZERZŐDÉSt, és kattintson a **tovább**gombra.
    5. Adja meg a bejelentkezési adatokat, kattintson a **tanúsítvány elfogadása**elemre, majd kattintson a **tovább**gombra.
    ![vRA hitelesítő adatai](media/configure-vra-endpoint-login.png)
    6. Válassza az **egyéni telepítési** és **proxy ügynökök** lehetőséget, majd kattintson a **tovább**gombra.
    ![vRA telepítési típusa](media/configure-vra-endpoint-install-type.png)
    7. Adja meg a IaaS-kiszolgáló bejelentkezési adatait, és kattintson a **tovább**gombra. Ha Active Directory használ, írja be a felhasználónevet **tartomány \ felhasználó** formátumban. Ellenkező esetben használja **user@domain** formátumot.
    ![vRA bejelentkezési adatai](media/configure-vra-endpoint-account.png)
    8. A proxybeállítások esetében adja meg a **vSphere** értéket az **ügynök típusaként**. Adja meg az ügynök nevét.
    9. Adja meg a IaaS-kiszolgáló teljes tartománynevét a **Manager Service Host** és a **Model Manager webszolgáltatás gazdagép** mezőiben. Kattintson a **test (tesztelés** ) elemre a minden FQDN-értékhez tartozó kapcsolatok teszteléséhez. Ha a teszt sikertelen, módosítsa a DNS-beállításokat úgy, hogy az IaaS-kiszolgáló állomásneve fel legyen oldva.
    10. Adja meg a vCenter-kiszolgáló végpontjának nevét az AVS Private Cloud számára. Jegyezze fel a nevet a konfigurációs folyamat későbbi részében való használatra.

        ![vRA-telepítési proxy](media/configure-vra-endpoint-proxy.png)

    11. Kattintson a **Tovább** gombra.
    12. Kattintson az **Install** (Telepítés) gombra.

## <a name="configure-the-vsphere-agent"></a>A vSphere-ügynök konfigurálása

1. Nyissa meg a https://*VRA-URL*/vcac, és jelentkezzen be **ConfigurationAdmin**néven.
2. Válassza az **infrastruktúra** > **végpontok** > **végpontok**lehetőséget.
3. Válassza az **új** > **virtuális** > **vSphere**lehetőséget.
4. Adja meg az előző eljárásban megadott vSphere-végpont nevét.
5. A **cím**mezőbe írja be az AVS Private Cloud vCenter Server URL-címét a https://*vCenter-FQDN*/SDK formátumban, ahol a *vCenter-FQDN* a vCenter-kiszolgáló neve.
6. Adja meg a vRealize Automation IaaS rendszergazda felhasználójának hitelesítő adatait, amelyet az AVS-támogatás hozott létre.
7. A felhasználói hitelesítő adatok ellenőrzéséhez kattintson a **Kapcsolódás tesztelése** gombra. Ha a teszt sikertelen, ellenőrizze az URL-címet, a fiók adatait és a [végpont nevét](#verify-the-endpoint-name) , majd a tesztet.
8. Sikeres tesztelés után kattintson az **OK** gombra az vSphere-végpont létrehozásához.
    ![vRA-végponti konfigurációs hozzáférési](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>A végpont nevének ellenőrzése

A megfelelő vCenter-kiszolgálói végpont nevének azonosításához tegye a következőket:

1. Nyisson meg egy parancssort a IaaS készüléken.
2. Módosítsa a könyvtárat a C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, ahol az *ügynök neve* az vCenter-kiszolgálói végponthoz rendelt név.
3. Futtassa a következő parancsot.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

A kimenet az alábbihoz hasonló. A `managementEndpointName` mező értéke a végpont neve.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```

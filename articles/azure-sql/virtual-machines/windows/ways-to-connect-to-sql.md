---
title: Kapcsolódás SQL Server virtuális géphez (Resource Manager) | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a SQL Server virtuális géphez az Azure-ban. Ez a témakör a klasszikus telepítési modellt használja. A forgatókönyvek a hálózati konfigurációtól és az ügyfél helyétől függően eltérőek.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37cb8f5a2ff0916f53ae50f5750664204ab1ba75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737489"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Kapcsolódás SQL Server Azure-beli virtuális géphez
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Áttekintés

Ez a témakör azt ismerteti, hogyan lehet csatlakozni az SQL-hez az Azure-beli virtuális gépen (VM). Néhány [általános kapcsolódási forgatókönyvet](#connection-scenarios) tárgyal, majd [a kapcsolódási beállítások módosításához a portálon lépéseket](#change)tartalmaz. Ha a portálon kívülről kell a kapcsolatot elhárítani vagy konfigurálnia, tekintse meg a témakör végén található [manuális konfigurálást](#manual) . 

Ha inkább a kiépítéssel és a kapcsolattal kapcsolatos teljes áttekintést szeretne látni, tekintse [meg a SQL Server virtuális gép](create-sql-vm-portal.md)üzembe helyezése az Azure-ban című témakört.

## <a name="connection-scenarios"></a>Kapcsolatok forgatókönyvei

Az ügyfél SQL Server VMhoz való kapcsolódásának módja az ügyfél helyétől és a hálózati konfigurációtól függően eltérő.

Ha kiépít egy SQL Server VM a Azure Portalban, lehetősége van megadnia az **SQL-kapcsolat** típusát.

![Nyilvános SQL-kapcsolati beállítás a kiépítés során](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

A kapcsolódási lehetőségek a következők:

| Beállítás | Leírás |
|---|---|
| **Nyilvános** | Kapcsolódjon SQL Server az interneten keresztül. |
| **Személyes** | Kapcsolódjon SQL Server ugyanahhoz a virtuális hálózathoz. |
| **Helyi** | Kapcsolódjon SQL Server helyileg ugyanazon a virtuális gépen. | 

A következő részek részletesebben ismertetik a **nyilvános** és a **magánjellegű** beállításokat.

## <a name="connect-to-sql-server-over-the-internet"></a>Kapcsolódás SQL Server az interneten keresztül

Ha az internetről szeretne csatlakozni a SQL Server adatbázis-motorhoz, válassza a **nyilvános** lehetőséget a portálon az **SQL-kapcsolat** típusaként a kiépítés során. A portál automatikusan a következő lépéseket hajtja végre:

* Engedélyezi a SQL Server TCP/IP protokollját.
* Egy tűzfalszabály konfigurálásával megnyitja a SQL Server TCP-portot (alapértelmezés szerint 1433).
* A nyilvános hozzáféréshez szükséges SQL Server hitelesítés engedélyezése.
* Konfigurálja a hálózati biztonsági csoportot a virtuális gépen a SQL Server porton lévő összes TCP-forgalomra.

> [!IMPORTANT]
> A SQL Server Developer és az expressz kiadások virtuálisgép-rendszerképei nem engedélyezik automatikusan a TCP/IP protokollt. Fejlesztői és expressz kiadások esetén a virtuális gép létrehozása után a [TCP/IP protokoll manuális engedélyezéséhez](#manualtcp) SQL Server konfigurációkezelőt kell használnia.

Bármely internet-hozzáféréssel rendelkező ügyfél csatlakozhat a SQL Server példányhoz a virtuális gép nyilvános IP-címének vagy az IP-címhez rendelt bármely DNS-címkének a megadásával. Ha a SQL Server port 1433, nem kell megadnia azt a kapcsolódási karakterláncban. A következő kapcsolati karakterlánc egy SQL-hitelesítést használó SQL virtuális géphez csatlakozik `sqlvmlabel.eastus.cloudapp.azure.com` (a nyilvános IP-címet is használhatja).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Bár ez a karakterlánc lehetővé teszi az ügyfelekhez való kapcsolódást az interneten keresztül, ez nem jelenti azt, hogy bárki csatlakozhat a SQL Server-példányhoz. Az ügyfeleken kívül a megfelelő felhasználónevet és jelszót kell használniuk. A további biztonság érdekében azonban elkerülheti a jól ismert 1433-as portot is. Ha például az 1500-es port figyelésére, valamint a megfelelő tűzfal-és hálózati biztonsági csoportokra vonatkozó szabályok megadására vonatkozó SQL Server konfigurálását választotta, akkor a portszámnak a kiszolgálónévhez való hozzáfűzésével kapcsolódhat. Az alábbi példa az előzőt változtatja meg az **1500**-as számú egyéni portszám hozzáadásával a kiszolgálónévhez:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Amikor interneten keresztül kérdez le SQL Servert a virtuális gépen, az Azure-adatközpontból érkező összes kimenő adatra a kimenő adatforgalomra vonatkozó normál [díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)vonatkozik.

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Kapcsolódás SQL Server virtuális hálózaton belül

Ha a portálon az **SQL-kapcsolati** típushoz **privát** lehetőséget választ, az Azure a legtöbb olyan beállítást konfigurálja, amely azonos a **nyilvános** értékkel. Az egyetlen különbség, hogy nincs olyan hálózati biztonsági csoportra vonatkozó szabály, amely engedélyezi a SQL Server porton kívüli forgalom használatát (alapértelmezés szerint 1433).

> [!IMPORTANT]
> A SQL Server Developer és az expressz kiadások virtuálisgép-rendszerképei nem engedélyezik automatikusan a TCP/IP protokollt. Fejlesztői és expressz kiadások esetén a virtuális gép létrehozása után a [TCP/IP protokoll manuális engedélyezéséhez](#manualtcp) SQL Server konfigurációkezelőt kell használnia.

A magánhálózati kapcsolatot gyakran használják egy [virtuális hálózattal](../../../virtual-network/virtual-networks-overview.md)együtt, amely számos forgatókönyvet tesz lehetővé. Ugyanazon a virtuális hálózaton is csatlakoztathatók a virtuális gépek, még akkor is, ha ezek a virtuális gépek különböző erőforráscsoportok alatt találhatók. A helyek közötti [VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md)használatával olyan hibrid architektúrát is létrehozhat, amely a helyszíni hálózatokkal és számítógépekkel összekapcsolja a virtuális gépeket.

A virtuális hálózatok lehetővé teszik az Azure-beli virtuális gépek tartományhoz való csatlakoztatását is. Ez az egyetlen módszer a Windows-hitelesítés használatára a SQL Server. A többi csatlakoztatási forgatókönyvhöz felhasználónevek és jelszavak szükségesek az SQL-hitelesítéshez.

Feltételezve, hogy konfigurálta a DNS-t a virtuális hálózaton, csatlakozhat a SQL Server-példányhoz úgy, hogy megadja a SQL Server VM számítógép nevét a kapcsolati karakterláncban. Az alábbi példa azt is feltételezi, hogy a Windows-hitelesítés konfigurálva van, és hogy a felhasználó hozzáférést kapott a SQL Server-példányhoz.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> SQL-kapcsolat beállításainak módosítása

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A Azure Portal a SQL Server virtuális gép csatlakozási beállításait módosíthatja.

1. A Azure Portal válassza az SQL-alapú **virtuális gépek** elemet.

2. Válassza ki a SQL Server VM.

3. A **Beállítások** területen válassza a **Biztonság** elemet.

4. Módosítsa az **SQL-kapcsolat szintjét** a kötelező beállításra. Ezt a körzetet igény szerint módosíthatja a SQL Server portot vagy az SQL-hitelesítési beállításokat.

   ![SQL-kapcsolat módosítása](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Várjon néhány percet, amíg a frissítés befejeződik.

   ![SQL virtuális gép frissítési értesítése](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> A TCP/IP engedélyezése fejlesztőknek és expressz kiadásoknak

SQL Server kapcsolódási beállítások módosításakor az Azure nem engedélyezi automatikusan a TCP/IP protokollt SQL Server Developer és Express kiadásokhoz. Az alábbi lépések ismertetik, hogyan lehet manuálisan engedélyezni a TCP/IP protokollt, hogy távolról is csatlakozhasson IP-címmel.

Először kapcsolódjon a SQL Server virtuális géphez a távoli asztal használatával.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Ezután engedélyezze a TCP/IP protokollt **SQL Server konfigurációkezelő**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Csatlakozás SSMS segítségével

Az alábbi lépések bemutatják, hogyan hozhat létre opcionális DNS-címkét az Azure-beli virtuális géphez, majd hogyan csatlakozhat a SQL Server Management Studiohoz (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Manuális konfigurálás és hibaelhárítás

Bár a portál lehetőséget biztosít a kapcsolatok automatikus konfigurálására, hasznos tudni, hogyan konfigurálhatja a kapcsolatot manuálisan. A követelmények megismerése is segítséget nyújthat a hibaelhárításban.

A következő táblázat az Azure-beli virtuális gépen SQL Serverhoz való kapcsolódás követelményeit sorolja fel.

| Követelmény | Leírás |
|---|---|
| [SQL Server hitelesítési mód engedélyezése](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | SQL Server hitelesítés szükséges ahhoz, hogy távolról csatlakozhasson a virtuális GÉPHEZ, hacsak nem konfigurálta a Active Directoryt egy virtuális hálózaton. |
| [SQL-bejelentkezés létrehozása](/sql/relational-databases/security/authentication-access/create-a-login) | SQL-hitelesítés használata esetén olyan felhasználónévvel és jelszóval rendelkező SQL-bejelentkezésre van szükség, amely a céladatbázis engedélyeivel is rendelkezik. |
| [TCP/IP protokoll engedélyezése](#manualtcp) | A SQL Servernak engedélyeznie kell a TCP protokollon keresztüli kapcsolatokat. |
| [Tűzfalszabály engedélyezése a SQL Server porthoz](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A virtuális gépen lévő tűzfalnak engedélyeznie kell a bejövő forgalmat a SQL Server porton (alapértelmezett 1433). |
| [Hálózati biztonsági csoportra vonatkozó szabály létrehozása a 1433-as TCP-hez](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Ha az interneten keresztül szeretne csatlakozni, engedélyeznie kell a virtuális gép számára a SQL Server port forgalmának fogadását (alapértelmezett 1433). A helyi és a virtuális hálózati kapcsolatok esetében nincs szükség erre. Ez az egyetlen szükséges lépés a Azure Portalban. |

> [!TIP]
> Az előző táblázatban szereplő lépések a Kapcsolódás a portálon való konfigurálásakor lesznek végrehajtva. Ezekkel a lépésekkel megerősítheti a konfigurációt, vagy manuálisan állíthatja be a kapcsolatot SQL Server számára.

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tekinteni a telepítési utasításokat ezen csatlakozási lépések mellett, tekintse [meg a SQL Server virtuális gép üzembe helyezése az Azure](create-sql-vm-portal.md)-ban című témakört.

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md)szolgáltatásban.
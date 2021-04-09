---
title: 'Gyors útmutató: linuxos SQL Server VM létrehozása az Azure-ban'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Linux rendszerű SQL Server 2017-virtuálisgépet az Azure Portalon.
services: virtual-machines-linux
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fd5c289f2b441b5862d863d9a390a1cd054acbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790151"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>SQL Server-t futtató linuxos virtuális gép kiépítése a Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

Ebben a rövid útmutatóban a Azure Portal használatával hozzon létre egy Linux rendszerű virtuális gépet, amelyen telepítve van a SQL Server 2017-es verzió. A következőket sajátíthatja el: 


* [SQL Server-t futtató linuxos virtuális gép létrehozása a katalógusból](#create)
* [Csatlakozás az új virtuális géphez ssh használatával](#connect)
* [Az SA-jelszó módosítása](#password)
* [Konfigurálás távoli kapcsolatokhoz](#remote)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> Linux rendszerű, telepített SQL Serverrel rendelkező virtuális gép létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. Az **erőforrás létrehozása** panelen válassza a **számítás** lehetőséget.

1. Válassza az **összes** megjelenítése lehetőséget a **Kiemelt** fejléc mellett.

   ![Az összes virtuálisgép-rendszerkép megjelenítése](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. A keresőmezőbe írja be a **SQL Server 2019** kifejezést, majd a keresés indításához válassza az **ENTER billentyűt** .

1. Korlátozza a keresési eredményeket az **operációs rendszer**  >  **RedHat** kiválasztásával.

    ![SQL Server 2019 virtuálisgép-lemezképek keresési szűrője](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Válasszon ki egy SQL Server 2019 Linux-rendszerképet a keresési eredmények közül. Ez az oktatóanyag **SQL Server 2019**-et használ a RHEL74-on.

   > [!TIP]
   > A fejlesztői kiadással tesztelheti vagy fejlesztheti a vállalati kiadás szolgáltatásait, de nem SQL Server licencelési költségeket. Csak a Linux rendszerű virtuális gép futtatásával járó költségeket kell kifizetnie.

1. Válassza a **Létrehozás** lehetőséget. 


### <a name="set-up-your-linux-vm"></a>Linux rendszerű virtuális gép beállítása

1. Az **alapvető beállítások** lapon válassza ki az **előfizetést** és az **erőforráscsoportot**. 

    ![Alapvető beállítások ablak](./media/sql-vm-create-portal-quickstart/basics.png)

1. A **virtuális gép neve** mezőbe írja be az új linuxos virtuális gép nevét.
1. Ezután írja be vagy válassza ki a következő értékeket:
   * **Régió**: válassza ki az Ön számára legmegfelelőbb Azure-régiót.
   * **Rendelkezésre állási lehetőségek**: válassza ki az alkalmazásaihoz és adataihoz leginkább megfelelő rendelkezésre állási és redundancia-beállítást.
   * **Méret módosítása**: válassza ezt a lehetőséget a gép méretének kiválasztásához, és ha elkészült, válassza a **kiválasztás** elemet. További információ a virtuális gépek méretéről: virtuálisgép- [méretek](../../../virtual-machines/sizes.md).

     ![Virtuális gép méretének kiválasztása](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > A fejlesztéshez és a működéshez való teszteléshez használjon **DS2** vagy magasabb virtuálisgép-méretet. A teljesítményteszteléshez használjon **DS13** vagy nagyobb méretet.

   * **Hitelesítés típusa**: válassza az **SSH nyilvános kulcs** lehetőséget.

     > [!Note]
     > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Username (Felhasználónév**): adja meg a virtuális gép rendszergazdájának nevét.
   * **Nyilvános SSH-kulcs**: adja meg az RSA nyilvános kulcsát.
   * **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése** lehetőséget, és válassza ki az **SSH (22)** portot a **nyilvános bejövő portok kiválasztása** listában. Ebben a rövid útmutatóban ez a lépés szükséges a SQL Server konfigurációjának összekapcsolásához és befejezéséhez. Ha távolról szeretne csatlakozni a SQL Serverhoz, akkor a virtuális gép létrehozása után manuálisan engedélyeznie kell a forgalmat a Microsoft SQL Server által használt alapértelmezett portra (1433).

     ![Bejövő portok](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Végezze el a kívánt módosításokat a következő lapokon található beállításokban, vagy tartsa meg az alapértelmezett beállításokat.
    * **Lemezek**
    * **Hálózat**
    * **Felügyelet**
    * **Vendégkonfiguráció**
    * **Címkék**

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. A **felülvizsgálat + létrehozás** ablaktáblán válassza a **Létrehozás** lehetőséget.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Kapcsolódás a Linux rendszerű virtuális géphez

Ha már megnyitott egy BASH-parancssort, csatlakozzon az Azure-beli virtuális géphez az **ssh** paranccsal. A következő parancsban helyettesítse be a Linux rendszerű virtuális gép felhasználónevét és IP-címét a csatlakozáshoz.

```bash
ssh azureadmin@40.55.55.555
```

A virtuális gép IP-címét az Azure Portalon találhatja meg.

![IP-cím az Azure Portalon](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Ha Windows rendszert használ, és nem rendelkezik BASH-rendszerhéjral, telepítsen egy SSH-ügyfelet, például a PuTTY-t.

1. [Töltse le és telepítse a PuTTY-t](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Futtassa a PuTTY-t.

1. A PuTTY konfigurációs képernyőjén adja meg a virtuális gép nyilvános IP-címét.

1. Válassza a **Megnyitás** lehetőséget, majd adja meg a felhasználónevet és a jelszót az üzenetekben.

A Linux rendszerű virtuális gépekhez való csatlakozásról további információt a [Linux rendszerű virtuális gép az Azure-ban a Portal használatával történő létrehozását](../../../virtual-machines/linux/quick-create-portal.md) ismertető cikkben talál.

> [!NOTE]
> Ha a kiszolgáló gazdagép-kulcsát nem gyorsítótárazza a beállításjegyzékben, a következő lehetőségek közül választhat: Putty biztonsági riasztás. Ha megbízik a gazdagépen, válassza az **Igen** lehetőséget a kulcs a PuTTY gyorsítótárba való felvételéhez és a csatlakozás folytatásához. Ha csak egyszer szeretné csatlakoztatni a csatlakozást, anélkül, hogy a kulcsot a gyorsítótárba venné, válassza a **nem** lehetőséget. Ha nem bízik meg a gazdagépen, kattintson a **Mégse** gombra a kapcsolat megszakításához.

## <a name="change-the-sa-password"></a><a id="password"></a> Az SA-jelszó módosítása

Az új virtuális gép egy véletlenszerű SA-jelszóval telepíti az SQL Servert. A jelszó alaphelyzetbe állítása, mielőtt a SA-bejelentkezéssel csatlakozik SQL Serverhoz.

1. A Linux rendszerű virtuális géphez való csatlakozás után nyisson meg egy új parancsterminált.

1. Módosítsa az SA-jelszót az alábbi parancsokkal:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Adjon meg egy új SA-jelszót, és erősítse azt meg, amikor a rendszer erre kéri.

1. Indítsa újra az SQL Server szolgáltatást.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Eszközök hozzáadása az elérési úthoz (nem kötelező)

Alapértelmezés szerint a rendszer több SQL Server-[csomagot](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) is telepít, köztük az SQL Server parancssori eszközcsomagját. Az eszközcsomag tartalmazza az **sqlcmd** és **bcp** eszközt. Kényelmesebb, ha az eszközök elérési útját (`/opt/mssql-tools/bin/`) hozzáadja a **PATH** környezeti változóhoz.

1. A következő parancsok futtatásával módosítsa a **PATH** értékeit a bejelentkezési munkamenetek és az interaktív/nem bejelentkezési munkamenetekre vonatkozóan egyaránt:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Konfigurálás távoli kapcsolatokhoz

Ha távolról kell csatlakoznia az Azure-beli virtuális gépen futó SQL Serverhez, ahhoz konfigurálnia kell egy bejövő szabályt a hálózat biztonsági csoportjában. Ez a szabály engedélyezi a forgalmat azon a porton keresztül, amelyet az SQL Server figyel (ez alapértelmezés szerint az 1433-as). A következő lépések bemutatják, hogyan végezheti el ezt a lépést az Azure Portal használatával.

> [!TIP]
> Ha a kiépítés során kiválasztotta az **MS SQL (1433)** bejövő portot, a rendszer már elvégezte ezeket a módosításokat. Továbbléphet a következő szakaszra, amely a tűzfal konfigurálásával foglalkozik.

1. A portálon válassza a **Virtuális gépek** elemet, és válassza ki az SQL Server-t tartalmazó virtuális gépet.
1. A bal oldali navigációs ablaktábla **Beállítások** területén válassza a **hálózatkezelés** lehetőséget.
1. A hálózatkezelés ablakban válassza a bejövő **port hozzáadása** a **bejövő portra vonatkozó szabályok** lehetőséget.

   ![Bejövőport-szabályok](./media/sql-vm-create-portal-quickstart/networking.png)

1. A **Szolgáltatás** listában válassza ki az **MS SQL** lehetőséget.

    ![MS SQL biztonságicsoport-szabálya](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. Kattintson az **OK** gombra a virtuális gép szabályának mentéséhez.

### <a name="open-the-firewall-on-rhel"></a>Tűzfal megnyitása az RHEL-alapú virtuális gépen

Ez az oktatóanyag egy Red Hat Enterprise Linux (RHEL) rendszerű virtuális gép létrehozását ismertette. Ha távolról szeretne csatlakozni egy RHEL rendszerű virtuális géphez, akkor meg kell nyitnia a Linux tűzfalán az 1433-as portot.

1. [Csatlakozzon](#connect) az RHEL rendszerű virtuális géphez.

1. A BASH-parancssorban futtassa a következő parancsokat:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Következő lépések

Most, hogy van egy SQL Server 2017-es virtuális gépe az Azure-ban, helyileg csatlakozhat az **sqlcmd** használatával, és Transact-SQL-lekérdezéseket futtathat.

Ha az Azure-beli virtuális gépet a távoli SQL Server kapcsolatokhoz konfigurálta, akkor távolról is csatlakozhat. A Windows rendszerről egy Linuxon futó SQL Serverhez való csatlakozásra láthat egy példát a [Linuxon futó SQL Serverhez egy Windowson futó SSMS használatával történő csatlakozást](/sql/linux/sql-server-linux-develop-use-ssms) ismertető cikkben is. A Visual Studio Code használatával való csatlakozás részleteiről további információt a [Transact-SQL-szkriptek SQL Serverhez a Visual Studio Code használatával történő létrehozását és futtatását](/sql/linux/sql-server-linux-develop-use-vscode) ismertető cikkben talál.

További általános információ a SQL Server on Linuxről: a [Linuxon futó SQL Server 2017 áttekintése](/sql/linux/sql-server-linux-overview). További információkat a Linux rendszerű SQL Server 2017-es virtuális gépek használatáról az [SQL Server 2017-es Azure-beli virtuális gépek áttekintésében](sql-server-on-linux-vm-what-is-iaas-overview.md) talál.
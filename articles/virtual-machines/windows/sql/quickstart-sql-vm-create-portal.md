---
title: SQL Servert futtató, Windows rendszerű virtuális gép létrehozása a portálon | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows rendszerű SQL Server 2017 virtuális gépet az Azure Portalon.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bbf127f105f0ef22a23f00541396c2ac66b0e5f9
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828365"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Gyors útmutató: SQL Server 2017-et futtató, Windows rendszerű virtuális gép létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Ez a rövid útmutató részletesen bemutatja egy SQL Servert futtató virtuális gép létrehozását az Azure Portalon.


  > [!TIP]
  > - Ez a rövid útmutató bemutatja az SQL virtuális gépek gyors kiépítését és az azokhoz való csatlakozást. Az SQL virtuális gépek többi kiépítési lehetőségéről további információért tekintse meg a [Windows SQL Server virtuális gépek Azure Portalon való kiépítésének útmutatóját](virtual-machines-windows-portal-sql-server-provision.md).
  > - Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Azure-előfizetés beszerzése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a id="select"></a> SQL Server virtuálisgép-rendszerkép kiválasztása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók használatával.

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az *Azure SQL* kifejezést a keresőmezőbe.
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. További információkat az **SQL Virtual Machines** csempén a **Részletek megjelenítése** lehetőség kiválasztásával tekinthet meg.
1. Válassza ki **az ingyenes SQL Server licencet: SQL Server 2017 fejlesztő a Windows Server 2016 @ no__t-0 képen a legördülő menüből.

   ![Új keresési ablak](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Kattintson a **Létrehozás** gombra.

   ![Új keresési ablak](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a id="configure"></a> Az alapvető adatok megadása

Az **alapvető beállítások** lapon adja meg a következő információkat:

1. A Project Details ( **projekt részletei** ) szakaszban válassza ki az Azure-előfizetését, majd válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához. A név mezőbe írja be a _SQLVM-RG_ nevet.

   ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. A **példány részletei**területen:
    1. Írja be a SQLVM **nevet a virtuális gépnek**. 
    1. Válasszon egy helyet a **régió**számára. 
    1. Ebben a rövid útmutatóban hagyja, hogy a **rendelkezésre állási beállítások** ne legyenek _infrastruktúra_-redundancia megadása. További információ a rendelkezésre állási lehetőségekről: [rendelkezésre állás](../../windows/availability.md). 
    1. A **rendszerkép** listában válassza _az ingyenes SQL Server licenc: SQL Server 2017 fejlesztői Windows Server 2016_rendszeren. 
    1. A virtuális gép méretének **módosításához** válassza az **a2** alapszintű ajánlat lehetőséget. Ne felejtse el megtisztítani az erőforrásokat, ha elkészült velük, hogy elkerülje a váratlan költségeket. 

   ![Példány részletei](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. A **rendszergazdai fiók**területen adjon meg egy felhasználónevet, például az _Azureus_ és egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Rendszergazdai fiók](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. A **bejövő portszabályok**területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** lehetőséget a legördülő menüből. 

   ![Bejövőport-szabályok](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server beállításai

A **SQL Server beállítások** lapon adja meg a következő beállításokat:

1. A **Biztonság & hálózatkezelés**területen válassza a _nyilvános (Internet_) lehetőséget az **SQL** -kapcsolathoz, `1401` és módosítsa a portot úgy, hogy ne használjon jól ismert portszámot a nyilvános forgatókönyvben. 
1. Az **SQL-hitelesítés**területen válassza az **Engedélyezés**lehetőséget. Az SQL-bejelentkezés ugyanarra a felhasználónévre és jelszóra van állítva, amelyet a virtuális géphez konfigurált. [**Azure Key Vault integrációhoz**](virtual-machines-windows-ps-sql-keyvault.md)használja az alapértelmezett beállítást. A **tárolási konfiguráció** nem érhető el az alapszintű SQL Server VM lemezképhez, de további információkat talál a [tárolási konfigurációban](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)található egyéb rendszerképek elérhető lehetőségeiről.  

   ![Az SQL Server biztonsági beállításai](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. Szükség esetén módosítsa az egyéb beállításokat, majd válassza a **felülvizsgálat + létrehozás**elemet. 

   ![Felülvizsgálat + létrehozás](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Az SQL Servert futtató virtuális gép létrehozása

A **felülvizsgálat + létrehozás** lapon tekintse át az összegzést, és válassza a **Létrehozás** lehetőséget a virtuális géphez megadott SQL Server, erőforráscsoport és erőforrások létrehozásához.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit. Az üzembe helyezés több percet is igénybe vehet. 

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

1. A portálon keresse meg a SQL Server VM **nyilvános IP-címét** a virtuális gép tulajdonságainak **Áttekintés** szakaszában.

1. Az internethez csatlakozó másik számítógépen nyissa meg [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. A **Kapcsolódás a kiszolgálóhoz** vagy a **Kapcsolódás az adatbázismotorhoz** párbeszédpanelen szerkessze a **Kiszolgáló neve** értéket. Adja meg a virtuális gép nyilvános IP-címét. A név után írjon egy vesszőt, és adja meg az egyéni portot (**1401**), amelyet korábban az új virtuális gép konfigurálásakor beállítottunk. Például: `11.22.33.444,1401`.

1. A **Hitelesítés** mezőben válassza az **SQL Server-hitelesítés** lehetőséget.

1. A **Bejelentkezés** szövegmezőbe írjon be egy érvényes SQL-bejelentkezési nevet.

1. A **Jelszó** szövegmezőbe írja be a bejelentkezési jelszót.

1. Kattintson a **Csatlakozás** gombra.

    ![ssms connect](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Távoli bejelentkezés a virtuális gépre

A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szükséges, hogy az SQL virtuális gép folyamatosan fusson, a szükségtelen költségeket elkerülendő leállíthatja az épp használaton kívüli gépet. Emellett véglegesen törölheti a virtuális géppel társított erőforrásokat, ha törli a társított erőforráscsoportot a portálon. Ez véglegesen törli magát a virtuális gépet is, ezért ezt a parancsot körültekintően alkalmazza. További információk: [Azure-erőforrások kezelése a portálon keresztül](../../../azure-resource-manager/manage-resource-groups-portal.md).


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy SQL Server 2017 virtuális gépet a Azure Portalban. Az adatok az új SQL Serverre való migrálásával kapcsolatos további információkért lásd a következő cikket.

> [!div class="nextstepaction"]
> [Adatbázisok migrálása SQL virtuális gépekre](virtual-machines-windows-migrate-sql.md)

---
title: SQL Server létrehozása Windows rendszerű virtuális gépen a Azure Portalban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows rendszerű virtuális gépet SQL Server 2017-mel a Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 335a63faa440e057c282f992b67b301289a7a4bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356956"
---
# <a name="quickstart-create-sql-server-2017-on-a-windows-virtual-machine-in-the-azure-portal"></a>Rövid útmutató: SQL Server 2017 létrehozása Windows rendszerű virtuális gépen a Azure Portal

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Windows](sql-vm-create-portal-quickstart.md)
> * [Linux](../linux/sql-vm-create-portal-quickstart.md)

Ez a rövid útmutató végigvezeti SQL Server virtuális gép (VM) létrehozásán a Azure Portalban.


  > [!TIP]
  > - Ez a rövid útmutató bemutatja az SQL virtuális gépek gyors kiépítését és az azokhoz való csatlakozást. További információ az SQL-alapú virtuális gépek egyéb kiépítési lehetőségeiről: [kiépítési útmutató SQL Server Windows rendszerű virtuális gépen a Azure Portal](create-sql-vm-portal.md).
  > - Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Azure-előfizetés beszerzése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> SQL Server virtuálisgép-rendszerkép kiválasztása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a fiókjával.

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az *Azure SQL* kifejezést a keresőmezőbe.
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. További információkat az **SQL Virtual Machines** csempén a **Részletek megjelenítése** lehetőség kiválasztásával tekinthet meg.
1. Válassza ki az **ingyenes SQL Server licencet: SQL Server 2017 fejlesztő a Windows Server 2016** rendszerképet a legördülő menüből.

   ![Képernyőfelvétel: az ingyenes SQL Server licenc kiválasztása: SQL Server 2017 Developer a Windows Server 2016-rendszerképben.](./media/sql-vm-create-portal-quickstart/select-sql-2017-vm-image.png)

1. Válassza a **Létrehozás** lehetőséget.

   ![Új keresési ablak](./media/sql-vm-create-portal-quickstart/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Az alapvető adatok megadása

Az **alapvető beállítások** lapon adja meg a következő információkat:

1. A **Project Details (projekt részletei** ) szakaszban válassza ki az Azure-előfizetését, majd válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához. A név mezőbe írja be a _SQLVM-RG_ nevet.

   ![Előfizetés](./media/sql-vm-create-portal-quickstart/basics-project-details.png)

1. A **példány részletei** területen:
    1. Írja  be a SQLVM **nevet a virtuális gépnek**. 
    1. Válasszon egy helyet a **régió** számára. 
    1. Ebben a rövid útmutatóban hagyja, hogy a **rendelkezésre állási beállítások** ne legyenek _infrastruktúra-redundancia_ megadása. További információ a rendelkezésre állási lehetőségekről: [rendelkezésre állás](../../../virtual-machines/availability.md). 
    1. A **rendszerkép** listában válassza az _ingyenes SQL Server licenc: SQL Server 2017 Developer elemet a Windows Server 2016-on_. 
    1. A virtuális gép **méretének** **módosításához** válassza az **a2 alapszintű** ajánlat lehetőséget. Ne felejtse el megtisztítani az erőforrásokat, ha elkészült velük, hogy elkerülje a váratlan költségeket. 

   ![Példány adatai](./media/sql-vm-create-portal-quickstart/basics-instance-details.png)

1. A **rendszergazdai fiók** területen adjon meg egy felhasználónevet, például az _Azureus_ és egy jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Rendszergazdai fiók](./media/sql-vm-create-portal-quickstart/basics-administrator-account.png)

1. A **bejövő portszabályok** területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** lehetőséget a legördülő menüből. 

   ![Bejövőport-szabályok](./media/sql-vm-create-portal-quickstart/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server beállításai

A **SQL Server beállítások** lapon adja meg a következő beállításokat:

1. A **biztonság & hálózatkezelés** területen válassza a _nyilvános (Internet_) lehetőséget az **SQL-kapcsolathoz** , és módosítsa a portot úgy, `1401` hogy ne használjon jól ismert portszámot a nyilvános forgatókönyvben. 
1. Az **SQL-hitelesítés** területen válassza az **Engedélyezés** lehetőséget. Az SQL-bejelentkezési hitelesítő adatok ugyanarra a felhasználónévre és jelszóra vannak beállítva, amelyet a virtuális géphez konfigurált. [**Azure Key Vault integrációhoz**](azure-key-vault-integration-configure.md)használja az alapértelmezett beállítást. A **tárolási konfiguráció** nem érhető el az alapszintű SQL Server VM lemezképhez, de további információkat talál az egyéb rendszerképek elérhető lehetőségeiről a [tárolási konfigurációban](storage-configuration.md#new-vms).  

   ![Az SQL Server biztonsági beállításai](./media/sql-vm-create-portal-quickstart/sql-server-settings.png)


1. Szükség esetén módosítsa az egyéb beállításokat, majd válassza a **felülvizsgálat + létrehozás** elemet. 

   ![Ellenőrzés és létrehozás](./media/sql-vm-create-portal-quickstart/review-create.png)


## <a name="create-the-sql-server-vm"></a>Az SQL Servert futtató virtuális gép létrehozása

A **felülvizsgálat + létrehozás** lapon tekintse át az összegzést, és válassza a  **Létrehozás** lehetőséget a virtuális géphez megadott SQL Server, erőforráscsoport és erőforrások létrehozásához.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit. Az üzembe helyezés több percet is igénybe vehet. 

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

1. A portálon keresse meg a SQL Server VM **nyilvános IP-címét** a virtuális gép tulajdonságainak **Áttekintés** szakaszában.

1. Az internethez csatlakozó másik számítógépen nyissa meg [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. A **Kapcsolódás a kiszolgálóhoz** vagy a **Kapcsolódás az adatbázismotorhoz** párbeszédpanelen szerkessze a **Kiszolgáló neve** értéket. Adja meg a virtuális gép nyilvános IP-címét. Ezután adjon hozzá egy vesszőt, és adja hozzá az új virtuális gép konfigurálásakor megadott egyéni portot (**1401**). Például: `11.22.33.444,1401`.

1. A **Hitelesítés** mezőben válassza az **SQL Server-hitelesítés** lehetőséget.

1. A **Bejelentkezés** szövegmezőbe írjon be egy érvényes SQL-bejelentkezési nevet.

1. A **Jelszó** szövegmezőbe írja be a bejelentkezési jelszót.

1. Válassza a **Kapcsolódás** lehetőséget.

    ![ssms connect](./media/sql-vm-create-portal-quickstart/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Távoli bejelentkezés a virtuális gépre

A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szükséges, hogy az SQL virtuális gép folyamatosan fusson, a szükségtelen költségeket elkerülendő leállíthatja az épp használaton kívüli gépet. Emellett véglegesen törölheti a virtuális géppel társított erőforrásokat, ha törli a társított erőforráscsoportot a portálon. Ez véglegesen törli magát a virtuális gépet is, ezért ezt a parancsot körültekintően alkalmazza. További információk: [Azure-erőforrások kezelése a portálon keresztül](../../../azure-resource-manager/management/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy SQL Server 2017 virtuális gépet a Azure Portalban. Az adatok az új SQL Serverre való migrálásával kapcsolatos további információkért lásd a következő cikket.

> [!div class="nextstepaction"]
> [Adatbázisok migrálása SQL virtuális gépekre](migrate-to-vm-from-sql-server.md)
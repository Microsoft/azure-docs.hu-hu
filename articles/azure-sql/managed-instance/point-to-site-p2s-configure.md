---
title: Pont – hely kapcsolat konfigurálása a SSMS használatával
titleSuffix: Azure SQL Managed Instance
description: Kapcsolódjon az Azure SQL felügyelt példányához SQL Server Management Studio (SSMS) használatával pont – hely kapcsolattal egy helyszíni ügyfélszámítógépről.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788366"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Gyors útmutató: pont – hely kapcsolat konfigurálása az Azure SQL felügyelt példányához a helyszíni környezetből
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a rövid útmutató azt ismerteti, hogyan csatlakozhat az Azure SQL felügyelt példányához [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával egy helyszíni ügyfélszámítógépről pont – hely kapcsolaton keresztül. További információ a pont – hely kapcsolatokról: [Tudnivalók a pont – hely típusú VPN-ről](../../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- A  [felügyelt példány létrehozása](instance-create-quickstart.md) kiindulási pontként létrehozott erőforrásokat használja.
- A PowerShell 5,1 és Azure PowerShell 1.4.0 vagy újabb verzió szükséges a helyszíni ügyfélszámítógépen. Ha szükséges, tekintse meg a [Azure PowerShell modul telepítésére](/powershell/azure/install-az-ps#install-the-azure-powershell-module)vonatkozó utasításokat.
- A [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) legújabb verzióját igényli a helyszíni ügyfélszámítógépen.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>VPN-átjáró csatlakoztatása egy virtuális hálózathoz

1. Nyissa meg a PowerShellt a helyszíni ügyfélszámítógépen.

2. Másolja ezt a PowerShell-szkriptet. Ez a parancsfájl egy VPN-átjárót csatol a [felügyelt példány létrehozása](instance-create-quickstart.md) rövid útmutatójában létrehozott SQL felügyelt példány virtuális hálózathoz. Ez a szkript a Azure PowerShell az modult használja, és Windows-vagy Linux-alapú gazdagépek esetén a következő műveleteket végzi el:

   - Tanúsítványokat hoz létre és telepít egy ügyfélszámítógépen
   - Kiszámítja a jövőbeli VPN Gateway alhálózati IP-címtartományt.
   - Az átjáró-alhálózat létrehozása
   - Központilag telepíti a VPN-átjárót a VPN-alhálózathoz csatoló Azure Resource Manager sablont.

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Illessze be a szkriptet a PowerShell-ablakba, és adja meg a szükséges paramétereket. A, a és a értékének `<subscriptionId>` `<resourceGroup>` `<virtualNetworkName>` egyeznie kell azzal, amelyet a [felügyelt példányok létrehozása](instance-create-quickstart.md) című rövid útmutatóban használt. A értéke lehet `<certificateNamePrefix>` tetszőleges sztring.

4. Futtassa a PowerShell-szkriptet.

> [!IMPORTANT]
> Ne folytassa, amíg a PowerShell-parancsfájl be nem fejeződik.

## <a name="create-a-vpn-connection"></a>VPN-kapcsolat létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg azt az erőforráscsoportot, amelyben létrehozta a virtuális hálózati átjárót, majd nyissa meg a virtuális hálózati átjáró erőforrását.
3. Válassza a **pont – hely konfiguráció** lehetőséget, majd válassza a **VPN-ügyfél letöltése** lehetőséget.

    ![VPN-ügyfél letöltése](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. A helyszíni ügyfélszámítógépen bontsa ki a fájlokat a zip-fájlból, majd nyissa meg a mappát a kibontott fájlokkal.
5. Nyissa meg a **WindowsAmd64** mappát, és nyissa meg a **VpnClientSetupAmd64.exe** fájlt.
6. Ha a Windows által **védett számítógép** -üzenet jelenik meg, kattintson a **További információ** , majd a **Futtatás amúgy** elemre.

    ![VPN-ügyfél telepítése](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. A Felhasználói fiókok felügyelete párbeszédpanelen kattintson az **Igen** gombra a folytatáshoz.
8. A virtuális hálózatra hivatkozó párbeszédpanelen válassza az **Igen** lehetőséget a VPN-ügyfél telepítéséhez a virtuális hálózathoz.

## <a name="connect-to-the-vpn-connection"></a>Csatlakozás a VPN-kapcsolathoz

1. Nyissa meg a **VPN** -t a **hálózati & interneten** a helyi ügyfélszámítógépen, és válassza ki az SQL felügyelt példányának virtuális hálózatát, hogy kapcsolatot létesítsen ehhez a VNet. A következő képen a VNet neve **MyNewVNet**.

    ![VPN-kapcsolat](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Válassza a **Kapcsolódás** lehetőséget.
3. A párbeszédpanelen válassza a **kapcsolat** lehetőséget.

    ![Képernyőfelvétel: a csatlakozási gomb kiemelése.](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Ha a rendszer arra kéri, hogy a Csatlakozáskezelő emelt szintű jogosultságokkal rendelkezzen az útválasztási táblázat frissítéséhez, válassza a **Folytatás** lehetőséget.
5. A folytatáshoz a felhasználói fiókok felügyelete párbeszédpanelen válassza az **Igen** lehetőséget.

   Létrehozott egy VPN-kapcsolatát az SQL felügyelt példányának VNet.

    ![Képernyőfelvétel: a kapcsolat létrehozásakor kiemeli a csatlakoztatott üzenetet.](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Csatlakozás SSMS segítségével

1. A helyszíni ügyfélszámítógépen nyissa meg a SQL Server Management Studio.
2. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a felügyelt példány teljes **állomásnevét** a **kiszolgáló neve** mezőben.
3. Válassza ki **SQL Server hitelesítést**, adja meg felhasználónevét és jelszavát, majd válassza a **kapcsolat** lehetőséget.

    ![SSMS-kapcsolat](./media/point-to-site-p2s-configure/ssms-connect.png)  

A csatlakozási lehetőség után megtekintheti a rendszer és a felhasználói adatbázisokat az adatbázisok csomópontban. A biztonság, a kiszolgálói objektumok, a replikálás, a felügyelet, a SQL Server Agent és a XEvent Profiler-csomópontok különböző objektumait is megtekintheti.

## <a name="next-steps"></a>Következő lépések

- Az Azure-beli virtuális gépekről történő kapcsolódást bemutató rövid útmutató: [pont – hely kapcsolat konfigurálása](point-to-site-p2s-configure.md).
- Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Ha egy meglévő SQL Server adatbázist szeretne visszaállítani a helyszíni rendszerből egy felügyelt példányra, a [Azure Database Migration Service áttelepítéshez](../../dms/tutorial-sql-server-to-managed-instance.md) vagy a [T-SQL Restore parancs](restore-sample-database-quickstart.md) használatával állíthatja vissza az adatbázis biztonságimásolat-fájljából.
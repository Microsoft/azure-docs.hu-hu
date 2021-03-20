---
title: Azure-beli virtuális gép kapcsolatának konfigurálása
titleSuffix: Azure SQL Managed Instance
description: Kapcsolódjon az Azure SQL felügyelt példányához SQL Server Management Studio használatával egy Azure-beli virtuális gépről.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91620256"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Gyors útmutató: Azure-beli virtuális gép konfigurálása a felügyelt Azure SQL-példányhoz való kapcsolódáshoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy Azure-beli virtuális gépet a felügyelt Azure SQL-példányhoz való kapcsolódáshoz SQL Server Management Studio (SSMS) használatával. 


A pont – hely kapcsolat használatával történő kapcsolódást bemutató rövid útmutatóban tekintse meg a [pont – hely kapcsolat konfigurálása](point-to-site-p2s-configure.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a [felügyelt példány létrehozása](instance-create-quickstart.md) kiindulási pontként létrehozott erőforrásokat használja.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Új alhálózat VNet létrehozása

A következő lépések új alhálózatot hoznak létre az SQL felügyelt példányának VNet, így egy Azure-beli virtuális gép csatlakozhat a felügyelt példányhoz. Az SQL felügyelt példányának alhálózata felügyelt példányokhoz van hozzárendelve. Ebben az alhálózatban nem hozhatók létre más erőforrások, például az Azure Virtual machines.

1. Nyissa meg a [felügyelt példány létrehozása](instance-create-quickstart.md) rövid útmutatójában létrehozott felügyelt példányhoz tartozó erőforráscsoportot. Válassza ki a felügyelt példány virtuális hálózatát.

   ![SQL felügyelt példány erőforrásai](./media/connect-vm-instance-configure/resources.png)

2. Válassza az **alhálózatok** lehetőséget, majd válassza az **+ alhálózat** lehetőséget egy új alhálózat létrehozásához.

   ![SQL felügyelt példány alhálózatai](./media/connect-vm-instance-configure/subnets.png)

3. Töltse ki az űrlapot a táblázatban található információk alapján:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- |
   | **Név** | Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Címtartomány (CIDR-blokk)** | Érvényes tartomány | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Hálózati biztonsági csoport** | Nincsenek | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Útvonaltábla** | Nincsenek | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Szolgáltatásvégpontok** | 0 kijelölve | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Alhálózat delegálása** | Nincsenek | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|

   ![Új SQL felügyelt példány alhálózata ügyfél virtuális géphez](./media/connect-vm-instance-configure/new-subnet.png)

4. Kattintson az **OK** gombra a további alhálózat létrehozásához az SQL felügyelt példányának VNet.

## <a name="create-a-vm-in-the-new-subnet"></a>Virtuális gép létrehozása az új alhálózatban 

A következő lépések bemutatják, hogyan hozhat létre virtuális gépet az új alhálózatban az SQL felügyelt példányhoz való kapcsolódáshoz.

## <a name="prepare-the-azure-virtual-machine"></a>Az Azure-beli virtuális gép előkészítése

Mivel az SQL felügyelt példánya a privát virtuális hálózatba kerül, létre kell hoznia egy Azure-beli virtuális gépet egy telepített SQL Client eszközzel, például SQL Server Management Studio vagy Azure Data Studio. Ez az eszköz lehetővé teszi az SQL felügyelt példányhoz való kapcsolódást és a lekérdezések végrehajtását. Ez a rövid útmutató az SQL Server Management Studio eszközt használja.

Az ügyfél virtuális gépnek az összes szükséges eszközzel való létrehozásának legegyszerűbb módja a Azure Resource Manager-sablonok használata.

1. Győződjön meg arról, hogy be van jelentkezve a Azure Portal egy másik böngésző lapon. Ezután válassza a következő gombot egy ügyfél virtuális gép létrehozásához és a SQL Server Management Studio telepítéséhez:

   [![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. Töltse ki az űrlapot az alábbi táblázatban szereplő információk alapján:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- |
   | **Előfizetés** | Érvényes előfizetés | Olyan előfizetésnek kell lennie, amelyben jogosult új erőforrások létrehozására. |
   | **Erőforráscsoport** |Az [SQL felügyelt példányának létrehozása](instance-create-quickstart.md) rövid útmutatójában megadott erőforráscsoport|Ez az erőforráscsoport a VNet létezésének egyike.|
   | **Hely** | Az erőforráscsoport helye | Ez az érték a kiválasztott erőforráscsoport alapján van feltöltve. |
   | **Virtuális gép neve**  | Bármely érvényes név | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   |**Rendszergazdai Felhasználónév**|Bármilyen érvényes Felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.<br>Ezt a felhasználónevet akkor használja, amikor [csatlakozik a virtuális géphez](#connect-to-the-virtual-machine).|
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Ezt a jelszót bármikor használhatja [a virtuális géphez való kapcsolódáskor](#connect-to-the-virtual-machine).|
   | **Virtuális gép mérete** | Bármilyen érvényes méret | A **Standard_B2s** ebben a sablonban szereplő alapértelmezett értéke elegendő ehhez a rövid útmutatóhoz. |
   | **Hely**|[resourceGroup (). location].| Ne módosítsa ezt az értéket. |
   | **Virtual Network neve**|Az a virtuális hálózat, amelyben létrehozta a felügyelt példányt|
   | **Alhálózat neve**|Az előző eljárásban létrehozott alhálózat neve| Ne válassza ki azt az alhálózatot, amelyben a felügyelt példányt létrehozta.|
   | **összetevők helye** | [központi telepítés (). properties. templateLink. URI] | Ne módosítsa ezt az értéket. |
   | **összetevők helye sas-token** | Hagyja üresen | Ne módosítsa ezt az értéket. |

   ![ügyféloldali virtuális gép létrehozása](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Ha az [SQL felügyelt példányának létrehozásakor](instance-create-quickstart.md)a javasolt VNet nevet és az alapértelmezett alhálózatot használta, nem kell módosítania az utolsó két paramétert. Ellenkező esetben módosítsa ezeket az értékeket a hálózati környezet beállításakor megadott értékekre.

3. Jelölje be az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet.
4. Válassza a **vásárlás** lehetőséget az Azure-beli virtuális gép üzembe helyezéséhez a hálózaton.
5. A telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

> [!IMPORTANT]
> Ha a virtuális gép létrehozása után körülbelül 15 percet vesz igénybe, hogy időt adjon a létrehozás utáni parancsfájloknak SQL Server Management Studio telepítéséhez.

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A következő lépések bemutatják, hogyan csatlakozhat az újonnan létrehozott virtuális géphez Távoli asztal kapcsolat használatával.

1. Ha az üzembe helyezés elkészült, lépjen a virtuális gép erőforráshoz.

    ![Képernyőfelvétel: a kiválasztott virtuális gép áttekintő lapját tartalmazó Azure Portal és a Kiemelt kapcsolat.](./media/connect-vm-instance-configure/vm.png)  

2. Válassza a **Kapcsolódás** lehetőséget.

   A virtuális gép nyilvános IP-címét és portszámát tartalmazó RDP protokoll fájl (. rdp fájl) űrlap jelenik meg.

   ![RDP-űrlap](./media/connect-vm-instance-configure/rdp.png)  

3. Válassza az **RDP-fájl letöltése** lehetőséget.

   > [!NOTE]
   > Az SSH használatával is csatlakozhat a virtuális géphez.

4. Zárjuk be a **Kapcsolódás virtuális géphez** űrlapot.
5. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz.
6. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Mac számítógépen szükség van egy RDP-ügyfélre, például a Mac App Store áruházból [Távoli asztal-ügyfélre](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) .

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson **az OK gombra**.

8. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás folytatásához.

A Kiszolgálókezelő irányítópultján csatlakozik a virtuális géphez.

## <a name="connect-to-sql-managed-instance"></a>Csatlakozás felügyelt SQL-példányhoz 

1. A virtuális gépen nyissa meg a SQL Server Management Studio.

   Néhány percet is igénybe vehet, ahogy a konfigurációjának befejezéséhez szükséges, mivel ez az első alkalom, hogy a SSMS elindult.
2. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a felügyelt példány teljes **állomásnevét** a **kiszolgáló neve** mezőben. Válassza ki **SQL Server hitelesítést**, adja meg felhasználónevét és jelszavát, majd válassza a **kapcsolat** lehetőséget.

    ![SSMS-kapcsolat](./media/connect-vm-instance-configure/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat a Databases (Adatbázisok) csomóponton, valamint különféle objektumokat a Security (Biztonság), Server Objects (Kiszolgálóobjektumok), Replication (Replikáció), Management (Felügyelet), SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="next-steps"></a>Következő lépések

- A pont – hely kapcsolattal rendelkező helyszíni ügyfélszámítógépekről történő kapcsolódást bemutató rövid útmutató: [pont – hely kapcsolat konfigurálása](point-to-site-p2s-configure.md).
- Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Ha egy meglévő SQL Server adatbázist szeretne visszaállítani a helyszíni rendszerből egy felügyelt példányra, a [Azure Database Migration Service áttelepítéshez](../../dms/tutorial-sql-server-to-managed-instance.md) vagy a [T-SQL Restore parancs](restore-sample-database-quickstart.md) használatával állíthatja vissza az adatbázis biztonságimásolat-fájljából.

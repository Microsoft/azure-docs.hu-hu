---
title: SAP-megoldások Azure Monitor üzembe helyezése a Azure Portal
description: SAP-megoldások Azure Monitor üzembe helyezése a Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 02c0801aa0425db96a1e6f71f248c795e81b5ddf
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554059"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>SAP-megoldások Azure Monitor üzembe helyezése Azure Portal

Az SAP-megoldások erőforrásainak Azure Monitor a [Azure Portal](https://azure.microsoft.com/features/azure-portal)használatával hozhatók létre. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a Azure Monitor SAP-megoldások üzembe helyezéséhez és a szolgáltatók konfigurálásához.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen

## <a name="create-monitoring-resource"></a>Figyelési erőforrás létrehozása

1. Válassza ki Azure Monitor az **Azure Marketplace**-en elérhető **SAP-megoldásokhoz** .

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="A rendszerkép az Azure Marketplace-en elérhető SAP-megoldások Azure Monitor kiválasztását mutatja be." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Az **alapvető beállítások** lapon adja meg a szükséges értékeket. Ha alkalmazható, használhat meglévő Log Analytics munkaterületet.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="A Azure Portal konfigurációs beállításainak megjelenítése." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. A virtuális hálózat kiválasztásakor győződjön meg arról, hogy a figyelni kívánt rendszerek elérhetők a VNET belülről. 

   > [!IMPORTANT]
   > Ha a **megosztás** lehetőséget választja a Microsofttal való adatmegosztáshoz, a támogatási csapatunk további támogatást biztosít.

## <a name="configure-providers"></a>Szolgáltatók konfigurálása

### <a name="sap-hana-provider"></a>SAP HANA szolgáltató 

1. Válassza a **szolgáltató** lapot a konfigurálni kívánt szolgáltatók hozzáadásához. A figyelési erőforrás telepítése után több szolgáltatót is hozzáadhat egy másik után, vagy hozzáadhatja őket. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Megjeleníti a szolgáltató lapot, és további szolgáltatókat adhat hozzá a Azure Monitor SAP-megoldásokhoz." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Válassza a **szolgáltató hozzáadása** lehetőséget, majd a legördülő menüből válassza a **SAP HANA** lehetőséget. 

   > [!IMPORTANT]
   > Győződjön meg arról, hogy SAP HANA szolgáltató konfigurálva van a SAP HANA "Master" csomóponthoz.

3. Adja meg a HANA-kiszolgáló magánhálózati IP-címét.

4. Adja meg a használni kívánt adatbázis-bérlő nevét. Bármelyik bérlőt kiválaszthatja, de javasoljuk a **SYSTEMDB** használatát, mivel a figyelési területek szélesebb körét teszi lehetővé. 

5. Adja meg a HANA-adatbázishoz tartozó SQL-portszámot. A portszámnak a ( **[3]**[  +  **instance #]**  +  **[13]** formátumban kell lennie. Például 30013. 

6. Adja meg a használni kívánt adatbázis-felhasználónevet. Győződjön meg arról, hogy az adatbázis-felhasználónak van hozzárendelve a **figyelés** és a **katalógus olvasási** szerepköre. 

7. Ha elkészült, válassza a **szolgáltató hozzáadása** elemet. Továbbra is szükség esetén további szolgáltatókat adhat hozzá, vagy a telepítés befejezéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="A szolgáltatói információk hozzáadásakor a konfigurációs beállítások képe." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Magas rendelkezésre állású fürt (pacemaker) szolgáltatója

1. Válassza ki a **magas rendelkezésre állású fürtöt (pacemaker)** a legördülő menüből. 

   > [!IMPORTANT]
   > A magas rendelkezésre állású fürt (pacemaker) szolgáltatójának konfigurálásához győződjön meg arról, hogy a ha_cluster_provider telepítve van az egyes csomópontokon. További információ: [Ha a fürt exportőre](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Adja meg a Prometheus-végpontot a formájában http://IP:9664/metrics . 
 
3. Adja meg a rendszerazonosító (SID), az állomásnév és a fürt nevét.

4. Ha elkészült, válassza a **szolgáltató hozzáadása** elemet. Továbbra is szükség esetén további szolgáltatókat adhat hozzá, vagy a telepítés befejezéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="A rendszerkép a HA-fürt pacemaker-szolgáltatójához kapcsolódó beállításokat jeleníti meg." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>Operációs rendszer (Linux) szolgáltatója 

1. Válassza az operációs rendszer (Linux) lehetőséget a legördülő menüből 

>[!IMPORTANT]
> Az operációs rendszer (Linux) szolgáltatójának konfigurálásához győződjön meg arról, hogy a Node_Exporter legújabb verziója telepítve van minden olyan gazdagépen (BareMetal vagy virtuális gépen), amelyet figyelni szeretne. Használja ezt a [link] ( https://prometheus.io/download/#node_exporter) a legújabb verzió megkereséséhez. További információ: [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Adjon meg egy nevet, amely a BareMetal-példány azonosítója lesz.
3. Adja meg a Node exportőr végpontot a formájában http://IP:9100/metrics .

>[!IMPORTANT]
> Használja a Linux-gazdagép magánhálózati IP-címét. Győződjön meg arról, hogy a gazdagép és az AMS erőforrás ugyanabban a VNET van. 

>[!Note]
> A tűzfal "9100" portját meg kell nyitni a Linux-gazdagépen.
>Ha a tűzfal-cmd: Firewall-cmd--Permanent--Add-port = 9100/TCP tűzfal-cmd--reload a UFW használata esetén: UFW Allow 9100/TCP UFW reload

>[!Tip]
> Ha a Linux-gazdagép egy Azure-beli virtuális gép, ügyeljen arra, hogy az összes megfelelő NSG engedélyezze a bejövő forgalmat a 9100-as porton a forrásként.
 
5. Ha elkészült, válassza a **szolgáltató hozzáadása** elemet. Továbbra is szükség esetén további szolgáltatókat adhat hozzá, vagy a telepítés befejezéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget   . 


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server szolgáltató

1. A Microsoft SQL Server-szolgáltató hozzáadása előtt futtassa a következő parancsfájlt SQL Server Management Studio egy olyan felhasználó létrehozásához, amely a szolgáltató konfigurálásához szükséges megfelelő engedélyekkel rendelkezik.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Válassza a **szolgáltató hozzáadása** lehetőséget, majd a legördülő menüből válassza a **Microsoft SQL Server** lehetőséget. 

3. Töltse ki a mezőket a Microsoft SQL Serverhoz tartozó adatok használatával. 

4. Ha elkészült, válassza a **szolgáltató hozzáadása** elemet. Továbbra is szükség esetén további szolgáltatókat adhat hozzá, vagy a telepítés befejezéséhez válassza a **felülvizsgálat + létrehozás** lehetőséget.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="A rendszerkép a Microsoft SQL Server-szolgáltató hozzáadásával kapcsolatos információkat jeleníti meg." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Következő lépések

További információ az [SAP-megoldások Azure monitor](azure-monitor-overview.md)

---
title: Sap Azure Monitor megoldások üzembe helyezése a Azure Portal
description: Sap Azure Monitor megoldások üzembe helyezése a Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 096f0425a6893d68341b97c821481fa0adf2f95c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375270"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>A Azure Monitor for SAP Solutions üzembe helyezése Azure Portal

Azure Monitor for SAP Solutions erőforrások a következővel [Azure Portal:](https://azure.microsoft.com/features/azure-portal). Ez a módszer egy böngészőalapú felhasználói felületet biztosít a Azure Monitor for SAP Solutions és konfigurálja a szolgáltatókat.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen

## <a name="create-monitoring-resource"></a>Monitorozási erőforrás létrehozása

1. Válassza **Azure Monitor for SAP Solutions** lehetőséget **a Azure Marketplace** közül.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Az SAP-megoldásokhoz Azure Monitor ajánlat kiválasztását bemutató kép Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Az Alapvető **beállítások lapon** adja meg a szükséges értékeket. Ha lehetséges, használhat egy meglévő Log Analytics-munkaterületet.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="A konfigurációs Azure Portal megjelenítése." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Virtuális hálózat kiválasztásakor győződjön meg arról, hogy a figyelni kívánt rendszerek elérhetőek a virtuális hálózaton belülről. 

   > [!IMPORTANT]
   > Ha a **Megosztás** lehetőséget választjuk a Microsofttal való adatmegosztáshoz, a támogatási csapatunk további támogatást nyújthat.

## <a name="configure-providers"></a>Szolgáltatók konfigurálása

### <a name="sap-hana-provider"></a>SAP HANA szolgáltató 

1. Válassza **a Szolgáltató** lapot a konfigurálni kívánt szolgáltatók hozzáadásához. Több szolgáltatót is hozzáadhat, vagy hozzáadhatja őket a figyelési erőforrás üzembe helyezése után. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="A szolgáltató lapon további szolgáltatókat adhat hozzá a Azure Monitor for SAP Solutions." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Válassza **a Szolgáltató hozzáadása lehetőséget,** **SAP HANA** a legördülő menüből. 

   > [!IMPORTANT]
   > Győződjön meg SAP HANA, hogy a szolgáltató konfigurálva van SAP HANA fő csomóponthoz.

3. Adja meg a HANA-kiszolgáló magánhálózati IP-címét.

4. Adja meg a használni kívánt adatbázis-bérlő nevét. Bármelyik bérlőt választhatja, javasoljuk a **SYSTEMDB használatát,** mivel az szélesebb körű figyelési területet tesz lehetővé. 

5. Adja meg a HANA-adatbázishoz társított SQL-portszámot. A portszám formátumának a következőnek kell lennie: **[3]**  +  **[instance#]**  +  **[13]**. Például: 30013. 

6. Adja meg a használni kívánt adatbázis-felhasználónevet. Győződjön meg arról, hogy az adatbázis-felhasználó rendelkezik hozzárendelt **figyelési** **és katalógus-olvasási szerepkörökkel.** 

7. Ha elkészült, válassza a **Szolgáltató hozzáadása lehetőséget.** Folytassa szükség szerint további szolgáltatók hozzáadását, vagy válassza az **Áttekintés + létrehozás** lehetőséget az üzembe helyezés befejezéséhez.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="A szolgáltatói adatok hozzáadásakor rendelkezésre álló konfigurációs beállítások képe." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Magas rendelkezésre állású fürtszolgáltató (Pacemaker)

1. A legördülő menüben válassza a Magas rendelkezésre állású fürt **(Pacemaker)** lehetőséget. 

   > [!IMPORTANT]
   > A magas rendelkezésre állású fürt (Pacemaker) szolgáltatójának konfigurálása érdekében győződjön meg arról, hogy a ha_cluster_provider minden csomóponton telepítve van. További információ: [HA-fürtök halmaza](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Adja meg a Prometheus végpontot a következő formában: http://IP:9664/metrics . 
 
3. Adja meg a rendszerazonosítót (SID), az állomásnevet és a fürt nevét.

4. Ha elkészült, válassza a **Szolgáltató hozzáadása lehetőséget.** Folytassa szükség szerint további szolgáltatók hozzáadását, vagy válassza az **Áttekintés + létrehozás** lehetőséget az üzembe helyezés befejezéséhez.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="A képen a HA-fürt Pacemaker szolgáltatójához kapcsolódó lehetőségek láthatóak." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>Operációsrendszer-szolgáltató (Linux) 

1. Válassza az Operációs rendszer (Linux) lehetőséget a legördülő menüből 

>[!IMPORTANT]
> Az operációs rendszer (Linux) szolgáltatójának konfiguráláskor győződjön meg arról, hogy a Node_Exporter legújabb verziója telepítve van minden monitorni kívánt gazdagépen (BareMetal vagy VM). Használja ezt a [hivatkozást] ( https://prometheus.io/download/#node_exporter) a legújabb verzió megkereshez. További információ: [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Adja meg a nevet, amely az BareMetal-példány azonosítója lesz.
3. Adja meg a csomópont-végpontot a következő formában: http://IP:9100/metrics .

>[!IMPORTANT]
> Használja a Linux-gazdagép magánhálózati IP-címét. Győződjön meg arról, hogy a gazdagép és az AMS erőforrás ugyanazon a virtuális hálózaton található. 

>[!Note]
> A "9100" tűzfalportot meg kell nyitni a Linux-gazdagépen.
>Firewall-cmd használata esetén: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload Ha az ufw: ufw allow 9100/tcp ufw reload

>[!Tip]
> Ha a linuxos gazdagép Egy Azure-beli virtuális gép, győződjön meg arról, hogy az összes vonatkozó NSG engedélyezi a bejövő forgalmat a 9100-as porton a "VirtualNetwork" forrásként.
 
5. Ha elkészült, válassza a **Szolgáltató hozzáadása lehetőséget.** Folytassa szükség szerint további szolgáltatók hozzáadását, vagy válassza az **Áttekintés + létrehozás** lehetőséget az üzembe helyezés   befejezéséhez. 


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server szolgáltató

1. A Microsoft SQL Server hozzáadása előtt futtassa a következő szkriptet a SQL Server Management Studio a szolgáltató konfiguráláshoz szükséges engedélyekkel rendelkező felhasználó létrehozásához.

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

2. Válassza **a Szolgáltató hozzáadása lehetőséget,** **Microsoft SQL Server** a legördülő menüből. 

3. Töltse ki a mezőket a saját Microsoft SQL Server. 

4. Ha elkészült, válassza a **Szolgáltató hozzáadása lehetőséget.** Folytassa szükség szerint további szolgáltatók hozzáadását, vagy válassza az **Áttekintés + létrehozás** lehetőséget az üzembe helyezés befejezéséhez.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="A képen a szolgáltató hozzáadásával kapcsolatos Microsoft SQL Server látható." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="sap-netweaver-provider"></a>SAP NetWeaver szolgáltató

#### <a name="pre-requisites-for-adding-netweaver-provider"></a>A NetWeaver szolgáltató hozzáadásának előfeltételei

Az "SAP start service" számos szolgáltatást nyújt, beleértve az SAP-rendszer monitorozását. Az "SAPControl" egy SOAP webszolgáltatás-felület, amely ezeket a képességeket teszi elérhetővé. Ez az SAPControl webszolgáltatási felület megkülönbözteti a védett és a nem [védett](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) webszolgáltatás-metódusokat. Bizonyos metrikák lekérésének lehetővé tennie bizonyos metódusok védmét. Az aktuális kiadáshoz szükséges metódusok vélmének feloldása érdekében kövesse az alábbi lépéseket az egyes SAP-rendszerekhez:

1. SAP GUI-kapcsolat megnyitása az SAP-kiszolgálóhoz
2. Bejelentkezés rendszergazdai fiókkal
3. RZ10 tranzakció végrehajtása
4. Válassza ki a megfelelő profilt (DEFAULT. PFL)
5. Válassza a Kiterjesztett karbantartás lehetőséget, majd kattintson a Módosítás gombra. 
6. Módosítsa a "service/protectedwebmethods" érintett paraméter értékét "SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList" értékre az ajánlott beállításra, és kattintson a Másolás gombra.
7. Vissza profil mentése és >kiválasztása
8. A paraméter érvénybe lépő újraindítási rendszere

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>NetWeaver szolgáltató telepítése a Azure Portal
1.  Győződjön meg arról, hogy az előfeltételként szükséges lépések befejeződtek, és a kiszolgáló újraindult
2.  A Azure Portal AMS alatt válassza a Szolgáltató hozzáadása, majd az SAP NetWeaver lehetőséget a legördülő menüből.
3.  Adja meg az SAP-rendszer és az altartomány állomásnevét (ha van)
4.  Adja meg a megadott állomásnévnek megfelelő példányszámot 
5.  Adja meg a rendszerazonosítót (SID)
6.  Ha elkészült, válassza a Szolgáltató hozzáadása lehetőséget.
7.  Folytassa szükség szerint további szolgáltatók hozzáadását, vagy válassza az Áttekintés + létrehozás lehetőséget az üzembe helyezés befejezéséhez

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

## <a name="next-steps"></a>Következő lépések

További információ [a](azure-monitor-overview.md) Azure Monitor for SAP Solutions

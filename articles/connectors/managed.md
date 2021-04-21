---
title: Felügyelt összekötők Azure Logic Apps
description: A Microsoft által felügyelt eseményindítók és műveletek használatával olyan automatizált munkafolyamatokat hozhat létre, amelyek más alkalmazásokat, adatokat, szolgáltatásokat és rendszereket integrálnak Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796926"
---
# <a name="managed-connectors-for-logic-apps"></a>Felügyelt összekötők Logic Apps

[A felügyelt összekötők](apis-list.md) olyan más szolgáltatások és rendszerek elérésére kínálnak módokat, [](built-in.md) amelyekben nem érhetők el beépített eseményindítók és műveletek. Ezekkel az eseményindítókval és műveletekkel olyan munkafolyamatokat hozhat létre, amelyek adatokat, alkalmazásokat, felhőalapú szolgáltatásokat és helyszíni rendszereket integrálnak. A beépített eseményindítókhoz és műveletekhez képest ezek az összekötők általában egy adott szolgáltatáshoz vagy rendszerhez kötődnek, például az Azure Blob Storage-hoz, az Office 365-höz, az SQL-hez, a Salesforce-hoz vagy az SFTP-kiszolgálókhoz. A Microsoft által felügyelt és az Azure-ban üzemeltetett felügyelt összekötők általában megkövetelik, hogy először kapcsolatot hozzon létre a munkafolyamatból, és hitelesítse az identitását. Az ismétlődésalapú és a webhook-alapú eseményindítók is elérhetők, ezért ha ismétlődésalapú eseményindítót használ, tekintse át az Ismétlődés [viselkedésének áttekintése témakört.](apis-list.md#recurrence-behavior)

Néhány szolgáltatáshoz, rendszerhez és protokollhoz (például Azure Service Bus, Azure Functions, SQL, AS2 stb.) a Logic Apps is biztosít beépített verziókat. A szám és a tartomány attól függően változik, hogy több-bérlős logikai alkalmazást vagy egybérlős logikai alkalmazást hoz létre. Néhány esetben egy beépített és egy felügyelt összekötő-verzió is elérhető. A legtöbb esetben a beépített verzió jobb teljesítményt, képességeket, díjszabást stb. nyújt. Ha például [B2B-üzeneteket](../logic-apps/logic-apps-enterprise-integration-as2.md)cserél az AS2 protokollal, válassza ki a beépített verziót, kivéve, ha nyomkövetési képességekre van szüksége, amelyek csak az (elavult) felügyelt összekötő verziójában érhetők el.

A felügyelt összekötők Logic Apps több alkategóriához is tartoznak. Az SAP-összekötő például vállalati [](#enterprise-connectors) és helyszíni [összekötő is.](#on-premises-connectors)

* [A standard szintű összekötők](#standard-connectors) olyan szolgáltatásokhoz biztosítanak hozzáférést, mint például a Azure Blob Storage, az Office 365, a SharePoint, a Salesforce, Power BI, a OneDrive stb.
* [A helyszíni összekötők](#on-premises-connectors) hozzáférést biztosítanak olyan helyszíni rendszerekhez, mint a SQL Server, a SharePoint Server, az SAP, Oracle DB, a fájlmegosztások stb.
* [](#integration-account-connectors) Az integrációsfiók-összekötők segítségével XML-fájlokat alakíthatja át és érvényesítheti, egyszerű fájlokat kódolhatja és dekódolhatja, valamint az AS2, EDIFACT és X12 protokollokkal feldolgozhatja a vállalat közötti (B2B) üzeneteket. 

## <a name="standard-connectors"></a>Szokványos összekötők

Azure Logic Apps ezeket a népszerű Standard összekötőket biztosítja az automatizált munkafolyamatok ezen szolgáltatások és rendszerek használatával történő építéséhez. Egyes Standard összekötők helyszíni [rendszereket és integrációs](#on-premises-connectors) [fiókokat is támogatnak.](#integration-account-connectors)

Egyes Logic Apps Standard összekötők támogatják [a helyszíni](#on-premises-connectors) rendszereket vagy az [integrációs fiókokat.](#integration-account-connectors)

:::row:::
    :::column:::
        [![Azure Service Bus összekötő ikonja a Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        A Logic Apps leggyakrabban használt összekötőjével aszinkron üzeneteket, munkameneteket és témakör-előfizetéseket kezelhet.
    :::column-end:::
    :::column:::
        [![SQL Server összekötő ikonja a Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Csatlakozhat a SQL Server helyszíni vagy felhőbeli Azure SQL Database, így kezelheti a rekordokat, tárolt eljárásokat futtathat vagy lekérdezéseket hajthat végre.
    :::column-end:::
    :::column:::
        [![Az Azure Blog Storage felügyelt összekötő ikonja a Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blog Storage**][azure-blob-storage-doc]
        \
        \
        Csatlakozzon Azure Storage-fiókjához a blobtartalom létrehozásához és kezeléséhez.
    :::column-end:::
    :::column:::
        [![Office 365 Outlook által felügyelt összekötő ikon a Logic Apps][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Kapcsolódhat munkahelyi vagy iskolai e-mail-fiókjához, így e-maileket, feladatokat, naptáreseményeket és értekezleteket, névjegyeket, kéréseket és sok más feladatot hozhat létre és kezelhet.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![STFP-SSH által felügyelt összekötő ikon a Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Az SSH használatával csatlakozhat az interneten elérhető SFTP-kiszolgálókhoz, és elérheti az azokon lévő mappákat és fájlokat.
    :::column-end:::
    :::column:::
        [![SharePoint Online által felügyelt összekötő ikon a Logic Apps][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Csatlakozhat a SharePoint Online-hoz a fájlok, mellékletek, mappák stb. kezeléséhez.
    :::column-end:::
    :::column:::
        [![Az Azure Queues felügyelt összekötő ikonja a Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure Queues**][azure-queues-doc]
        \
        \
        Csatlakozzon Azure Storage-fiókjához, hogy üzenetsorokat és üzeneteket hoz létre és kezeljen.
    :::column-end:::
    :::column:::
        [![Felügyelt FTP-összekötő ikon a Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**Ftp**][ftp-doc]
        \
        \
        Csatlakozzon az internetről elérhető FTP-kiszolgálókhoz, hogy használva tudja a fájlokat és mappákat.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![A fájlrendszer által felügyelt összekötő ikonja a Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Fájlrendszer**][file-system-doc]
        \
        \
        Csatlakozzon a helyszíni fájlmegosztáshoz a fájlok létrehozásához és kezeléséhez.
    :::column-end:::
    :::column:::
        [![Azure Event Hubs összekötő ikonja a Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        Az Event Hubokon keresztül eseményeket használhat fel és tehet közzé. Az Event Hubs szolgáltatással például lekérheti a logikai alkalmazások kimenetét, majd elküldheti a kimenetet valamely valós idejű elemzési szolgáltatónak.
    :::column-end:::
    :::column:::
        [![Azure Event Grid összekötő ikonja a Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Egy erőforrás által közzétett események Event Grid, például amikor az Azure-erőforrások vagy külső erőforrások változnak.
    :::column-end:::
    :::column:::
        [![A Salesforce által felügyelt összekötő ikonja a Logic Apps][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Kapcsolódhat Salesforce-fiókjához, így például rekordokat, feladatokat, objektumokat és más elemeket hozhat létre és kezelhet.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>Helyszíni összekötők

Mielőtt kapcsolatot hoz létre egy helyszíni rendszerrel, először le kell töltenie, telepítenie és be kell állítania egy helyszíni [adatátjárót.][gateway-doc] Ez az átjáró biztonságos kommunikációs csatornát biztosít anélkül, hogy beállítanának egy szükséges hálózati infrastruktúrát. 

Az alábbi összekötők néhány gyakran használt [Standard](#standard-connectors) összekötő, Logic Apps biztosít a helyszíni rendszerekben található adatok és erőforrások eléréséhez. A helyszíni összekötők listájáért lásd: [Támogatott adatforrások.](../logic-apps/logic-apps-gateway-connection.md#supported-connections)

:::row:::
    :::column:::
        [![A Biztalk Server helyszíni összekötő ikonja a Logic Apps][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![A fájlrendszer helyszíni összekötő ikonja a Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Fájlrendszer**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Az IBM Db2 helyszíni összekötő ikonja a Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Az IBM Informix helyszíni összekötő ikonja a Logic Apps][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Helyszíni MySQL-összekötő ikon a Logic Apps][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB összekötő ikonja a Logic Apps][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Helyszíni PostgreSQL-összekötő ikon a Logic Apps][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**Postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![A SharePoint Server helyszíni összekötő ikonja a Logic Apps][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint-kiszolgáló**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server összekötő ikonja a Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Helyszíni Teradata-összekötő ikon a Logic Apps][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői

Az integrációs fiókok összekötői kifejezetten támogatják a vállalat közötti [(B2B)](../logic-apps/logic-apps-enterprise-integration-overview.md) kommunikációs forgatókönyveket a Azure Logic Apps. Miután [létrehozott](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) egy integrációs fiókot, és meghatározta a B2B-munkatermékeket, például kereskedelmi partnereket, egyezményeket, térképeket és sémákat, integrációs fiókok összekötőivel kódolhatja és dekódolhatja az üzeneteket, átalakíthatja a tartalmat stb.

Ha például Microsoft-összekötőt BizTalk Server, létrehozhat egy kapcsolatot a munkafolyamatból a helyszíni BizTalk Server [összekötővel.](#on-premises-connectors) Ezután bizTalk-hez hasonló műveleteket végezhet vagy bővíthet a munkafolyamatban ezekkel az integrációsfiók-összekötőkvel.

> [!NOTE]
> Az integrációs fiók összekötőinek használata előtt össze kell kapcsolnunk a logikai alkalmazást [egy integrációs fiókkal.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)


:::row:::
    :::column:::
        [![AS2-dekódolási művelet ikon a Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2-dekódolás**][as2-doc]
    :::column-end:::
    :::column:::
        [![AS2 kódolási művelet ikonja a Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2-kódolás**][as2-doc]
    :::column-end:::
    :::column:::
        [![Az EDIFACT-dekódolási művelet ikonja a Logic Apps][edifact-icon]][edifact-decode-doc]
        \
        \
        [**EDIFACT-dekódolás**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Az EDIFACT kódolási művelet ikonja a Logic Apps][edifact-icon]][edifact-encode-doc]
        \
        \
        [**EDIFACT-kódolás**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Az egyenáras fájl dekódolása műveletikon a Logic Apps][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Egybesimított fájlok dekódolása**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Az egyenáras fájlkódolás műveletikonja a Logic Apps][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Egyszerű fájlkódolás**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Integrációs fiók műveletikonja a Logic Apps][integration-account-icon]][integration-account-doc]
        \
        \
        [**Integrációs fiók**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![A Liquid átalakítja a művelet ikonját a Logic Apps][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid-átalakítások**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![X12-dekódolási művelet ikonja a Logic Apps][x12-icon]][x12-decode-doc]
        \
        \
        [**X12-dekódolás**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 kódolási művelet ikonja a Logic Apps][x12-icon]][x12-encode-doc]
        \
        \
        [**X12-kódolás**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![XML-átalakítások műveletikonja a Logic Apps][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML-átalakítások**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![XML-érvényesítési művelet ikon a Logic Apps][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML-érvényesítés**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Vállalati összekötők

Az alábbi összekötők további költséggel biztosítanak hozzáférést a vállalati rendszerekhez:

:::row:::
    :::column:::
        [![IBM 3270 enterprise connector icon in Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270 vállalati** összekötő][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![IBM MQ enterprise connector icon in Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ Enterprise-összekötő**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP Enterprise-összekötő ikon a Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Enterprise-összekötő][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>ISE-összekötők

Integrációs szolgáltatási környezetben (ISE) ezek a felügyelt összekötők [ISE-verziókkal](apis-list.md#ise-and-connectors)is rendelkeznek, amelyek a több-bérlős verzióktól eltérő képességekkel rendelkeznek:

> [!NOTE]
> Az ISE-ban és az összekötőikben futó logikai alkalmazások , függetlenül attól, hogy hol futnak ezek az összekötők, a használatalapú díjszabási csomagtól függetlenül rögzített díjszabási csomagokat követnek. További információ: [Logic Apps díjszabási](../logic-apps/logic-apps-pricing.md) modell [és Logic Apps részletei.](https://azure.microsoft.com/pricing/details/logic-apps/)

:::row:::
    :::column:::
        [![AS2 ISE-összekötő ikon a Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2** Ise][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ISE-összekötő ikonja a Logic Apps][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** Ise][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage ISE-összekötő ikonja a Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage** Ise][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ISE-összekötő ikonja a Logic Apps][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** Ise][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ISE-összekötő ikonja a Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** Ise][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE-összekötő ikonja a Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** Ise][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Az Azure File Storage ISE-összekötő ikonja a Logic Apps][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure File Storage** Ise][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE-összekötő ikonja a Logic Apps][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** Ise][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor ISE-összekötő ikonja a Logic Apps][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Azure Monitor naplók** Ise][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus ISE-összekötő ikonja a Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** Ise][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE-összekötő ikonja a Logic Apps][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** Ise][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Az Azure Table Storage ISE-összekötő ikonja a Logic Apps][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Azure Table Storage** Ise][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Queues ISE-összekötő ikon a Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure Queues** Ise][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT ISE-összekötő ikon a Logic Apps][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** Ise][edifact-doc]
    :::column-end:::
    :::column:::
        [![Fájlrendszer ISE-összekötő ikonja a Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Fájlrendszer** Ise][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP ISE-összekötő ikon a Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** Ise][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![IBM 3270 ISE-összekötő ikon a Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** Ise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![IBM DB2 ISE-összekötő ikon a Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** Ise][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![IBM MQ ISE-összekötő ikon a Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** Ise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE-összekötő ikon a Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Ise][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE-összekötő ikon a Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** Ise][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE-összekötő ikon a Logic Apps][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** Ise][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE-összekötő ikonja a Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** Ise][sql-server-doc]
    :::column-end:::
    :::column:::
        [![X12 ISE-összekötő ikon a Logic Apps][x12-icon]][x12-doc]
        \
        \
        [**X12** Ise][x12-doc]
    :::column-end:::
:::row-end:::

További információt az alábbi témakörökben talál:

* [Azure-beli virtuális hálózati erőforrások elérése a Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md)
* [Csatlakozás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni API-k létrehozása, amelyek a Logic Apps](/logic-apps/logic-apps-create-api-app)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Automatizálási feladatok létrehozása és kezelése a felhőbeli és helyszíni infrastruktúrához"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Csatlakozás Azure Cosmos DB a dokumentumokhoz és a tárolt eljárásokhoz való hozzáféréshez"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Egy erőforrás által közzétett események Event Grid, például amikor az Azure-erőforrások vagy külső erőforrások változnak"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Csatlakozzon Azure Event Hubs alkalmazáshoz, így eseményeket fogadhat és küldhet a logikai alkalmazások és a Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Csatlakozzon Azure Storage-fiókjához, hogy fájlokat hoz létre, frissítsen, lekért és töröljön"
[azure-key-vault-doc]: /connectors/keyvault/ "Csatlakozzon a Azure Key Vault a titkos kulcsok és kulcsok kezeléséhez"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Lekérdezések futtatása Azure Monitor Log Analytics-munkaterületeken és Application Insights összetevőkön"
[azure-queues-doc]: /connectors/azurequeues/ "Csatlakozzon az Azure Storage-fiókjához, így üzenetsorokat és üzeneteket hozhat létre és kezelhet"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Csatlakozás Azure Synapse Analytics az adatok megtekintéséhez"
[azure-table-storage-doc]: /connectors/azuretables/ "Kapcsolódhat Azure Storage-fiókjához, így táblákat hozhat létre, frissíthet és lekérdezhet, és egyéb funkciókat is használhat"
[biztalk-server-doc]: /connectors/biztalk/ "Csatlakozzon a BizTalk Server, hogy BizTalk-alapú alkalmazásokat futtathat a Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftp-doc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[github-doc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Csatlakozik a Google Naptárhoz, és kezelheti a naptárat"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Csatlakozás a Google Táblázatokhoz a táblázatok módosításához"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "A Google Tasks szolgáltatáshoz csatlakozik a feladatok kezeléséhez"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Csatlakozás 3270 alkalmazáshoz IBM-nagyszámítógépen"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Csatlakozhat az IBM DB2-höz a felhőben vagy a helyszínen. Sorok frissítése, tábla lekérte és egyéb"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Csatlakozzon az Informixhez a felhőben vagy a helyszínen. Sor beolvasása, táblák listása és egyéb"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Csatlakozás az IBM MQ-hoz a helyszínen vagy az Azure-ban üzenetek küldése és fogadása"
[instagram-doc]: ./connectors-create-api-instagram.md "Csatlakozzon az Internethez. Események aktiválása vagy az eseményekre való felvonás"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[mysql-doc]: /connectors/mysql/ "Csatlakozás a helyszíni MySQL-adatbázishoz az adatok olvasása és írása érdekében"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Kapcsolódhat munkahelyi vagy iskolai fiókjához, így e-maileket küldhet és fogadhat, kezelheti naptárát és névjegyeit, és egyéb használhatja"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Kapcsolódjon személyes Microsoft OneDrive-fiókjához, hogy fájlokat tölthet fel, törölhet, listába sorolhat adatokat és egyéb adatokat"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Kapcsolódhat a vállalati Microsoft OneDrive-hoz, így fájlokat tölthet fel, törölhet, listathat adatokat és egyéb adatokat"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozzon egy Oracle-adatbázishoz, így hozzáadhat, beszúrhat, törölhet sorokat és egyéb adatokat"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Kapcsolódhat az Outlook-postaládához, így kezelheti az e-mailjeit, naptárát, névjegyeit és egyéb használhatja"
[postgre-sql-doc]: /connectors/postgresql/ "Csatlakozzon a PostgreSQL-adatbázishoz, hogy adatokat olvasson a táblákból"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Csatlakozzon a Salesforce-fiókjához. Fiókok, érdeklődők, lehetőségek és egyéb lehetőségek kezelése"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Csatlakozzon a SendGridhez. E-mail küldése és címzettlisták kezelése"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Csatlakozzon SFTP-fiókjához SSH használatával. Fájlok feltöltése, betöltése, törlése és egyéb"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Csatlakozzon a helyszíni SharePoint-kiszolgálóhoz. Dokumentumok kezelése, elemek listából és egyéb elemekből"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Csatlakozzon a SharePoint Online-hoz. Dokumentumok kezelése, elemek listából és egyéb elemekből"
[slack-doc]: ./connectors-create-api-slack.md "Csatlakozás a Slackhez és üzenetek küldése Slack-csatornákon"
[smtp-doc]: ./connectors-create-api-smtp.md "Csatlakozás SMTP-kiszolgálóhoz, és mellékleteket is küldő e-mail küldése"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Csatlakozás Azure SQL Database vagy SQL Server. SQL-adatbázistáblák bejegyzéseinek létrehozása, frissítése, lekérte és törlése"
[teradata-doc]: /connectors/teradata/ "Csatlakozás a Teradata-adatbázishoz az adatok táblákból való beolvasása érdekében"
[twilio-doc]: ./connectors-create-api-twilio.md "Csatlakozzon a Twilio-hoz. Üzenetek küldése és fogadása, elérhető számok le szolgáltatása, bejövő telefonszámok kezelése és egyéb lehetőségek"
[youtube-doc]: ./connectors-create-api-youtube.md "Csatlakozzon a YouTube-hoz. Videók és csatornák kezelése"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 protokollt használó üzenetek kódolása és dekódolás"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Az EDIFACT protokollt használva kódolja és dekódolja az üzeneteket"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Az EDIFACT protokollt (EDIFACT) használva dekódoló üzenetek"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Az EDIFACT protokollt használó üzenetek kódolása"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyenl fájlról"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyenl fájlról"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Integrációs fiók összetevő metaadatainak kezelése"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "JSON átalakítása Liquid-sablonokkal"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 protokollt használó üzenetek kódolása és dekódolás"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Az X12 protokollt (X12) használva dekódoló üzenetek"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Az X12 protokollt használó üzenetek kódolása"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-üzenetek átalakítása"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-üzenetek ellenőrzése"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 protokollt használó üzenetek kódolása és dekódolás"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Az EDIFACT protokollt használó üzenetek kódolása és dekódolás"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Az EDIFACT protokollt használó üzenetek dekódolva"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Az EDIFACT protokollt használó üzenetek kódolása"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyenl fájlról"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyenl fájlról"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Integrációs fiók összetevő metaadatainak kezelése"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "JSON átalakítása Liquid-sablonokkal"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 protokollt használó üzenetek kódolása és dekódolás"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Az X12 protokollt (X12) használva dekódoló üzenetek"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 protokollt használó üzenetek kódolása"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-üzenetek átalakítása"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-üzenetek ellenőrzése"

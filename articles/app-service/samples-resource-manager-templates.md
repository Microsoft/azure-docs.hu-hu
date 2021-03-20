---
title: Azure Resource Manager sablon mintái
description: A gyakori App Service forgatókönyvek némelyikéhez Azure Resource Manager sablonban találhat mintákat. Ismerje meg, hogyan automatizálhatja App Service telepítési vagy felügyeleti feladatait.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 08/26/2020
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 5129fccceb633991767cdd3b52bbb5b6af067270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891045"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service Azure Resource Manager sablonjai

Az alábbi táblázat a Azure App Service Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaz. Az alkalmazás-sablonok létrehozásakor előforduló gyakori hibák elkerülésével kapcsolatos javaslatokért tekintse [meg az alkalmazások Azure Resource Manager-sablonokkal való üzembe helyezésének útmutatását](deploy-resource-manager-template.md).

A App Services erőforrások JSON-szintaxisával és tulajdonságaival kapcsolatos további információkért lásd: [Microsoft. Web Resource types](/azure/templates/microsoft.web/allversions).

| Alkalmazás üzembe helyezése | Description |
|-|-|
| [App Service csomag és alapszintű Linux-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Üzembe helyez egy Linux rendszerhez konfigurált App Service alkalmazást. |
| [App Service csomag és alapszintű Windows-alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Központilag telepíti a Windows rendszerhez konfigurált App Service alkalmazást. |
| [GitHub-adattárhoz csatolt alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Üzembe helyez egy App Service alkalmazást, amely kódot húz le a GitHubról. |
| [Alkalmazás egyéni üzembe helyezési pontokkal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Üzembe helyez egy App Service alkalmazást egyéni üzembe helyezési pontokkal/környezetekkel. |
| [Alkalmazás privát végponttal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-webapp)| Üzembe helyez egy App Service alkalmazást egy privát végponttal. |
|**Alkalmazás konfigurálása**| **Leírás** |
| [Alkalmazás-tanúsítvány Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Üzembe helyez egy App Service alkalmazás-tanúsítványt egy Azure Key Vault titkos kulcsból, és a TLS/SSL-kötéshez használja azt. |
| [Alkalmazás egyéni tartománnyal és SSL-lel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Üzembe helyez egy App Service alkalmazást egy egyéni állomásnévvel, és beolvassa az alkalmazás tanúsítványát Key Vaultról a TLS/SSL-kötéshez. |
| [Alkalmazás GoLang bővítménnyel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Üzembe helyez egy App Service alkalmazást a Golang hely bővítménnyel. Ezután futtathatja a Golangben fejlesztett webalkalmazásokat az Azure-ban. |
| [Alkalmazás Java 8 és tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Üzembe helyez egy App Service alkalmazást a Java 8 és a Tomcat 8 használata esetén. Ezután Java-alkalmazásokat futtathat az Azure-ban. |
| [Alkalmazás regionális VNet-integrációval](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| Üzembe helyez egy App Service alkalmazást, amelyen engedélyezve van a regionális VNet-integráció. |
|**Alkalmazás védelme**| **Leírás** |
| [Az Azure Application Gateway integrált alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Üzembe helyez egy App Service alkalmazást és egy Application Gateway, és elkülöníti a forgalmat a szolgáltatási végpont és a hozzáférési korlátozások használatával. |
|**Linux-alkalmazás csatlakoztatott erőforrásokkal**| **Leírás** |
| [Alkalmazás Linuxon MySQL-vel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Központilag telepít egy App Service alkalmazást Linux rendszeren, Azure Database for MySQL. |
| [Alkalmazás Linuxon a PostgreSQL használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Központilag telepít egy App Service alkalmazást Linux rendszeren, Azure Database for PostgreSQL. |
|**Alkalmazás csatlakoztatott erőforrásokkal**| **Leírás** |
| [Alkalmazás MySQL-sel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Üzembe helyez egy App Service alkalmazást a Windows rendszeren Azure Database for MySQL. |
| [Alkalmazás PostgreSQL-sel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Üzembe helyez egy App Service alkalmazást a Windows rendszeren Azure Database for PostgreSQL. |
| [Alkalmazás Azure SQL Database-adatbázissal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Üzembe helyez egy App Service alkalmazást és egy adatbázist az alapszintű szolgáltatási szinten Azure SQL Databaseban. |
| [Alkalmazás blob Storage-kapcsolatban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Üzembe helyez egy App Service alkalmazást egy Azure Blob Storage-beli kapcsolatok karakterláncával. Ezután használhatja a blob Storage-t az alkalmazásból. |
| [Alkalmazás egy Azure cache for Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Üzembe helyez egy App Service alkalmazást a Redis készült Azure cache-sel. |
| [Alkalmazás egy háttér webapphoz csatlakoztatva](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection)| Két webalkalmazást (frontend és háttérrendszer) helyez üzembe a VNet-befecskendezéssel és privát végponttal együtt. |
|**App Service-környezet**| **Leírás** |
| [App Service Environment v2-környezet létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton. |
| [App Service Environment v2-környezet létrehozása ILB-címmel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Létrehoz egy App Service Environment v2-környezetet a virtuális hálózaton egy privát belső terheléselosztó címmel. |
| [Az alapértelmezett SSL-tanúsítvány konfigurálása egy ILB App Service-környezethez vagy egy ILB App Service Environment v2-környezethez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Az alapértelmezett TLS/SSL-tanúsítványt konfigurálja egy ILB App Service-környezethez vagy egy ILB App Service Environment v2-hez. |
| | |

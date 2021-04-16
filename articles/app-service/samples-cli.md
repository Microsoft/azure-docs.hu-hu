---
title: CLI-minták
description: Néhány gyakori forgatókönyvhöz azure CLI-App Service talál. Megtudhatja, hogyan automatizálhatja a App Service üzembe helyezési vagy felügyeleti feladatait.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 51cdd470c9ae970d5945f8d5399cea67a44037f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483384"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-minták Azure App Service

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| Script | Leírás |
|-|-|
|**Alkalmazás létrehozása**||
| [Alkalmazás létrehozása és fájlok üzembe helyezése FTP használatával](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és üzembe helyez rajta egy fájlt FTP használatával. |
| [Alkalmazás létrehozása és kód üzembe helyezése a GitHubról](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és üzembe helyez kódot egy nyilvános GitHub-adattárból. |
| [Alkalmazás létrehozása a GitHubról való folyamatos üzembe helyezéssel](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service, amely folyamatosan közzéten egy saját GitHub-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése helyi Git-adattárból](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és konfigurálja a kód leküldését egy helyi Git-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service egy üzembe helyezési pont segítségével a kód módosításainak előkészítéséhez. |
| [ASP.NET Core-alkalmazás létrehozása Docker-tárolóban](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service Linux rendszeren, és betölt egy Docker-rendszerképet a Docker Hub. |
| [Alkalmazás létrehozása és elérhetővé szolgáltatása privát végponttal](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást és egy privát végpontot |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány leképezés egy alkalmazásra](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és leképez rá egy egyéni tartománynevet. |
| [Egyéni TLS-/SSL-tanúsítvány kötése egy alkalmazáshoz](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és hozzáköti egy egyéni tartománynév TLS-/SSL-tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Alkalmazás manuális skálázható](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service, és skálázható 2 példányra. |
| [Alkalmazás globális méretezése magas rendelkezésre állású architektúrával](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Két különböző App Service hoz létre, és egyetlen végponton keresztül teszi őket elérhetővé a Azure Traffic Manager. |
|**Alkalmazás védelme**||
| [Integráció a Azure Application Gateway](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és integrálja azt Application Gateway szolgáltatásvégpont és hozzáférési korlátozások használatával. |
|**Az alkalmazás összekötése az erőforrásokkal**||
| [Alkalmazás csatlakoztatása SQL Database](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást és egy adatbázist a Azure SQL Database, majd hozzáadja az adatbázis kapcsolati sztringet az alkalmazás beállításaihoz. |
| [Alkalmazás csatlakoztatása tárfiókhoz](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service egy tárfiókot, majd hozzáadja a storage kapcsolati sztringet az alkalmazásbeállításokhoz. |
| [Alkalmazás csatlakoztatása Azure Cache for Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást és egy Azure Cache for Redis, majd hozzáadja a Redis-kapcsolat részleteit az alkalmazás beállításaihoz.) |
| [Alkalmazás csatlakoztatása Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service és egy Cosmos DB, majd hozzáadja Cosmos DB kapcsolati adatokat az alkalmazás beállításaihoz. |
|**Alkalmazás biztonsági mentése és visszaállítása**||
| [Alkalmazás biztonsági mentése](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és létrehoz hozzá egy egyszeres biztonsági mentést. |
| [Ütemezett biztonsági mentés létrehozása egy alkalmazáshoz](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és ütemezett biztonsági másolatot készít hozzá. |
| [Visszaállít egy alkalmazást egy biztonsági másolatból](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Visszaállít egy App Service biztonsági másolatból. |
|**Alkalmazás monitorozása**||
| [Alkalmazás figyelése webkiszolgáló-naplók használatával](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service, engedélyezi a naplózást, és letölti a naplókat a helyi gépre. |
| | |

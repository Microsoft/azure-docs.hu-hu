---
title: Azure Purview-fiók csatlakoztatása 
description: Azure Purview-fiók csatlakoztatása Synapse-munkaterülethez.
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ad9250d022f02db06c9c5251c1fccfbfb730af67
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567876"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Rövid útmutató: Azure Purview-fiók csatlakoztatása Synapse-munkaterülethez 


Ebben a rövid útmutatóban egy Azure Purview-fiókot fog regisztrálni egy Synapse-munkaterületre. Ez a kapcsolat lehetővé teszi az Azure Purview-eszközök felderítését és a Synapse-képességeken keresztüli interakciót. 

A Synapse-ban a következő feladatokat hajthatja végre: 
- A felső keresőmezővel kulcsszavak alapján keresheti meg a Purview-eszközöket 
- Az adatok metaadatok, leszármakozás, jegyzetek alapján való magyarázata 
- Az adatok csatlakoztatása a munkaterülethez csatolt szolgáltatásokkal vagy integrációs adatkészletekkel 
- Az adatkészletek elemzése a Synapse Apache Spark, Synapse SQL és Adatfolyam 

## <a name="prerequisites"></a>Előfeltételek 
- [Azure Purview-fiók](../../purview/create-catalog-portal.md) 
- [Synapse-munkaterület](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Bejelentkezés Synapse-munkaterületre 

A  [https://web.azuresynapse.net](https://web.azuresynapse.net) webhelyre lépve jelentkezzen be a munkaterületre. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Engedélyek az Azure Purview-fiókok csatlakoztatáshoz 

- Ha Azure Purview-fiókot szeretne csatlakoztatni egy Synapse-munkaterülethez, közreműködői szerepkörre van szüksége **a** Synapse-munkaterületen az Azure Portal IAM-ból, és hozzá kell férni az Azure Purview-fiókhoz. További részletekért lásd: [Azure Purview permissions (Azure Purview-engedélyek).](../../purview/catalog-permissions.md)

## <a name="connect-an-azure-purview-account"></a>Azure Purview-fiók csatlakoztatása  

- A Synapse-munkaterületen, a **Manage**  ->  **Azure Purview (Az Azure-beli végleges nézet kezelése) alatt található.** Válassza **a Csatlakozás egy Purview-fiókhoz lehetőséget.** 
- Választhat a **From Azure subscription (Azure-előfizetésből) vagy a** Enter manually **(Manuálisan) lehetőséget.** **Az Azure-előfizetésben** kiválaszthatja azt a fiókot, amelyhez hozzáféréssel rendelkezik. 
- Ha csatlakoztatva van, a Nézet fiók neve az **Azure Purview-fiók** lapon látható lesz. 
- Az adatok kereséséhez használhatja a Synapse-munkaterület tetején található keresősávot. 

## <a name="nextsteps"></a>Következő lépések 

[Az azure-beli Azure Synapse regisztrálása és beolvasása](../../purview/register-scan-azure-synapse-analytics.md)

[Adatok felfedezése, csatlakoztatása és feltárása Synapse-ban az Azure Purview segítségével](how-to-discover-connect-analyze-azure-purview.md)   

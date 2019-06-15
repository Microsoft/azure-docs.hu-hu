---
title: 'Szolgáltatások közötti hitelesítés: REST API-t Azure Data Lake Storage Gen1 az Azure Active Directory |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 REST API használatával az Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c48f7d7608b2b70f4ae41e2af5792cff72bb0dd2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195782"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 REST API használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Ebben a cikkben megismerkedhet a REST API használata a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 tennie. Végfelhasználói hitelesítés a Data Lake Storage Gen1 REST API használatával, lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 REST API-val](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure Active Directory "" webalkalmazás létrehozásához**. El kell végeznie a lépések [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés
Ebben a forgatókönyvben az alkalmazás maga biztosítja saját hitelesítő adatait a műveletek végrehajtásához. Ehhez adja ki az alábbi kódrészletben látható módon egy hasonló POST-kérelem: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A kérelem kimenete egy engedélyezési jogkivonatot tartalmaz (kimaradásával `access-token` alábbi kimenetben), hogy ezt követően adja át a REST API-hívások. A hitelesítési jogkivonat mentse egy szövegfájlba; szüksége lesz rá a Data Lake Storage Gen1 REST-hívások küldésekor.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Ez a cikk a **nem interaktív** módszert alkalmazza. További információk a nem interaktív (szolgáltatások közötti) hívásokról: [Szolgáltatások közötti hívások hitelesítő adatok használatával](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan service-to-service-hitelesítés használatára való hitelesítés a Data Lake Storage Gen1 REST API használatával. Most már megtekintheti a következő cikkek, amelyek a REST API segítségével dolgozhat a Data Lake Storage Gen1 beszélni.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 REST API használatával](data-lake-store-get-started-rest-api.md)
* [A Data Lake Storage Gen1 Adatműveletek – REST API használatával](data-lake-store-data-operations-rest-api.md)


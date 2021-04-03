---
title: Végfelhasználói hitelesítés – REST Data Lake Storage Gen1 – Azure
description: Ismerje meg, hogyan érheti el a végfelhasználói hitelesítést Azure Data Lake Storage Gen1 használatával Azure Active Directory használatával REST API
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103747"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Végfelhasználói hitelesítés Azure Data Lake Storage Gen1 használatával REST API
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ebből a cikkből megtudhatja, hogyan használható a REST API a végfelhasználói hitelesítés végrehajtásához Azure Data Lake Storage Gen1 használatával. A szolgáltatás és a szolgáltatás közötti hitelesítéshez REST API használatával Data Lake Storage Gen1 lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 használatával REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "natív" alkalmazást**. A Data Lake Storage Gen1 a Azure Active Directory használatával végre kell hajtania a [végfelhasználói hitelesítéshez](data-lake-store-end-user-authenticate-using-active-directory.md)szükséges lépéseket.

* **[curl](https://curl.haxx.se/)**. Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API hívásokat kezdeményezni egy Data Lake Storage Gen1-fiókkal.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
A végfelhasználói hitelesítés az ajánlott módszer, ha azt szeretné, hogy egy felhasználó az Azure AD használatával jelentkezzen be az alkalmazásba. Az alkalmazás képes hozzáférni az Azure-erőforrásokhoz a bejelentkezett felhasználóval megegyező hozzáférési szinttel. A felhasználónak rendszeresen meg kell adnia hitelesítő adatait ahhoz, hogy az alkalmazás fenntartsa a hozzáférést.

A végfelhasználói bejelentkezés eredménye az, hogy az alkalmazás hozzáférési jogkivonatot és frissítési jogkivonatot kap. A hozzáférési jogkivonat minden Data Lake Storage Gen1 vagy Data Lake Analyticshoz kapcsolódó kérelemhez csatolva lesz, és alapértelmezés szerint egy órára érvényes. A frissítési token új hozzáférési jogkivonat beszerzésére használható, és alapértelmezés szerint legfeljebb két hétig érvényes, ha rendszeresen használják. A végfelhasználói bejelentkezéshez két különböző megközelítés használható.

Ebben az esetben az alkalmazás bejelentkezésre kéri a felhasználót, és minden művelet a felhasználó kontextusában lesz végrehajtva. Hajtsa végre a következő lépéseket:

1. Az alkalmazáson keresztül irányítsa át a felhasználót az alábbi URL-címre:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> egy URL-címben való használatra kell kódolni. Tehát a esetében https://localhost használja a `https%3A%2F%2Flocalhost` következőt:)

    A jelen oktatóanyagban kicserélheti a fenti URL-ben szereplő helyőrző értékeket, és beillesztheti egy webböngésző címsorába. A rendszer átirányítja az Azure bejelentkezési azonosítójával történő hitelesítéshez. Miután sikeresen bejelentkezett, a válasz megjelenik a böngésző címsorában. A válasz az alábbi formátumban jelenik meg:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Rögzítse a válaszban szereplő engedélyezési kódot. Ebben az oktatóanyagban átmásolhatja az engedélyezési kódot a webböngésző címsorába, és átadhatja a POST kérelemben a jogkivonat-végpontnak, ahogy az a következő kódrészletben látható:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > Ebben az esetben a \<REDIRECT-URI> szükségtelen kódolást kell végezni.
   > 
   > 

3. A válasz egy JSON-objektum, amely egy hozzáférési jogkivonatot (például `"access_token": "<ACCESS_TOKEN>"` ) és egy frissítési tokent (például `"refresh_token": "<REFRESH_TOKEN>"` ) tartalmaz. Az alkalmazás a hozzáférési jogkivonatot használja a Azure Data Lake Storage Gen1hoz való hozzáféréshez és a frissítési tokenhez egy másik hozzáférési jogkivonat beszerzéséhez, amikor egy hozzáférési jogkivonat lejár.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Ha a hozzáférési jogkivonat lejár, új hozzáférési jogkivonatot kérhet a frissítési jogkivonat használatával, ahogy az alábbi kódrészletben is látható:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

További információk az interaktív felhasználói hitelesítéssel kapcsolatban: [Authorization code grant flow](/previous-versions/azure/dn645542(v=azure.100)) (Az engedélyezési kód engedélyezési folyamata).

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használható a szolgáltatások közötti hitelesítés a Azure Data Lake Storage Gen1 REST API használatával történő hitelesítéshez. A következő cikkekből megtudhatja, hogyan használhatja a REST API a Azure Data Lake Storage Gen1 használatához.

* [Fiókkezelés Data Lake Storage Gen1 a REST API használatával](data-lake-store-get-started-rest-api.md)
* [Az adatműveletek Data Lake Storage Gen1 a REST API használatával](data-lake-store-data-operations-rest-api.md)
---
title: Hitelesítés – Data Lake Storage Gen1 az Azure AD-vel
description: Megtudhatja, hogyan hitelesítheti Azure Data Lake Storage Gen1 a Azure Active Directory használatával.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106569"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Hitelesítés Azure Data Lake Storage Gen1 használatával Azure Active Directory

A Azure Data Lake Storage Gen1 Azure Active Directory használ a hitelesítéshez. Az Data Lake Storage Gen1-vel működő alkalmazások készítése előtt el kell döntenie, hogyan hitelesítheti az alkalmazást Azure Active Directory (Azure AD) használatával.

## <a name="authentication-options"></a>Hitelesítési lehetőségek

* **Végfelhasználói hitelesítés** – a végfelhasználó Azure-beli hitelesítő adatai a Data Lake Storage Gen1 használatával történő hitelesítéshez használhatók. Az Ön által létrehozott alkalmazás Data Lake Storage Gen1 a felhasználói hitelesítő adatok megadását kéri. Ennek eredményeképpen ez a hitelesítési mechanizmus *interaktív* , és az alkalmazás a bejelentkezett felhasználó környezetében fut. További információ és útmutatás: [Data Lake Storage Gen1 végfelhasználói hitelesítése](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Szolgáltatások közötti hitelesítés** – ezt a beállítást akkor használja, ha azt szeretné, hogy egy alkalmazás a Data Lake Storage Gen1 használatával hitelesítse magát. Ilyen esetekben létre kell hoznia egy Azure Active Directory (AD-) alkalmazást, és az Azure AD-alkalmazás kulcsát használva hitelesítenie kell magát a Data Lake Storage Gen1. Ennek eredményeképpen ez a hitelesítési mechanizmus *nem interaktív*. További információ és útmutatás: [szolgáltatások közötti hitelesítés Data Lake Storage Gen1hoz](data-lake-store-service-to-service-authenticate-using-active-directory.md).

A következő táblázat bemutatja, hogyan támogatott a végfelhasználók és a szolgáltatások közötti hitelesítési mechanizmusok Data Lake Storage Gen1. Így olvashatja el a táblázatot.

* A ✔ * jel azt jelzi, hogy a hitelesítési lehetőség támogatott, és hivatkozásokat tartalmaz egy cikkre, amely bemutatja, hogyan használható a hitelesítési lehetőség. 
* A ✔ szimbólum azt jelzi, hogy a hitelesítési beállítás támogatott. 
* Az üres cellák azt jelzik, hogy a hitelesítési beállítás nem támogatott.


|Használja ezt a hitelesítési lehetőséget a következővel:...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Végfelhasználó (MFA * * nélkül)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(elavult)     |    **[✔ *](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Végfelhasználó (MFA-val)                           |    **[✔ *](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔ *](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔ *](data-lake-store-get-started-cli-2.0.md)**      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Szolgáltatások közötti (az ügyfél kulcsa)         |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔ *](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔ *](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔ *](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Szolgáltatások közötti (ügyféltanúsítvány használatával) |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kattintson a <b>✔ \* </b> szimbólumra. Ez egy hivatkozás.</i><br>
<i>* * Az MFA a többtényezős hitelesítéshez</i>

Az Azure Active Directory hitelesítéshez való használatáról további információt a  [Azure Active Directory hitelesítési forgatókönyvei](../active-directory/develop/authentication-vs-authorization.md) című témakörben talál.

## <a name="next-steps"></a>Következő lépések

* [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
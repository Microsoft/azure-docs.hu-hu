---
title: Egyéni hitelesítésszolgáltatói tanúsítvány hozzáadása – Azure API Management | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá egyéni hitelesítésszolgáltatói tanúsítványt az Azure API Management. A tanúsítványok törlésére vonatkozó utasításokat is láthatja.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51719f23302bfaa036f99e88fcd440d97ca3bc02
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817809"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Egyéni hitelesítésszolgáltatói tanúsítvány hozzáadása az Azure API Management

Az Azure API Management hitelesítésszolgáltatói tanúsítványok telepítését teszi lehetővé a megbízható legfelső szintű és köztes tanúsítványtárolókban található számítógépen. Ezt a funkciót akkor kell használni, ha a szolgáltatások egyéni hitelesítésszolgáltatói tanúsítványt igényelnek.

Ez a cikk bemutatja, hogyan kezelheti egy Azure API Management-szolgáltatáspéldány CA-tanúsítványait a Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Hitelesítésszolgáltatói tanúsítvány feltöltése

![Hitelesítésszolgáltatói tanúsítványok hozzáadása](media/api-management-howto-ca-certificates/00.png)

Új hitelesítésszolgáltatói tanúsítvány feltöltéshez kövesse az alábbi lépéseket. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az új [szolgáltatáspéldány API Management oktatóanyagát.](get-started-create-service-instance.md)

1. Keresse meg az Azure API Management-szolgáltatáspéldányt a Azure Portal.

2. A **menüben válassza a HITELESÍTÉSSZOLGÁLTATÓi** tanúsítványok lehetőséget.

3. Kattintson a **+ Hozzáadás** gombra.  

    ![A hitelesítésszolgáltatói tanúsítvány hozzáadására vonatkozó + Hozzáadás gombot bemutató képernyőkép.](media/api-management-howto-ca-certificates/01.png)  

4. Keresse meg a tanúsítványt, és döntse el a tanúsítványtárolót. Csak a nyilvános kulcsra van szükség, ezért a jelszóra nincs szükség.

    ![A tanúsítvány tallózását bemutató képernyőkép.](media/api-management-howto-ca-certificates/02.png)  

5. Kattintson a **Mentés** gombra. A művelet eltarthat néhány percig.

    ![A tanúsítvány mentését bemutató képernyőkép.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Hitelesítésszolgáltatói tanúsítványt a `New-AzApiManagementSystemCertificate` PowerShell-paranccsal tölthet fel.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ügyfél-tanúsítvány törlése

Tanúsítvány törléséhez kattintson a helyi menü **... parancsára,** és válassza a **tanúsítvány melletti Törlés** lehetőséget.

![Hitelesítésszolgáltatói tanúsítványok törlése](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a

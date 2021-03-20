---
title: Egyéni HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány hozzáadása – Azure API Management | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá egyéni HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt az Azure API Managementban. A tanúsítványok törlésére vonatkozó utasításokat is megtekintheti.
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
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102037"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Egyéni HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány hozzáadása az Azure API Management

Az Azure API Management lehetővé teszi HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok telepítését a gépre a megbízható gyökér-és köztes tanúsítványtárolókban. Ezt a funkciót akkor kell használni, ha a szolgáltatások egyéni HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt igényelnek.

A cikk bemutatja, hogyan kezelheti a Azure Portal egy Azure API Management Service-példány HITELESÍTÉSSZOLGÁLTATÓI tanúsítványait.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Hitelesítésszolgáltatói tanúsítvány feltöltése

![HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok hozzáadása](media/api-management-howto-ca-certificates/00.png)

Az új HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltéséhez kövesse az alábbi lépéseket. Ha még nem hozott létre API Management Service-példányt, tekintse meg az [API Management Service-példány létrehozása](get-started-create-service-instance.md)című oktatóanyagot.

1. Navigáljon az Azure API Management Service-példányhoz a Azure Portal.

2. Válassza ki a **hitelesítésszolgáltatói tanúsítványok** lehetőséget a menüből.

3. Kattintson a **+ Hozzáadás** gombra.  

    ![A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány hozzáadására szolgáló + Hozzáadás gombot megjelenítő képernyőkép.](media/api-management-howto-ca-certificates/01.png)  

4. Keresse meg a tanúsítványt, és döntse el a tanúsítványtárolót. Csak a nyilvános kulcsra van szükség, ezért a jelszó megadása nem kötelező.

    ![Képernyőkép, amely bemutatja, hogyan böngészhet a tanúsítványban.](media/api-management-howto-ca-certificates/02.png)  

5. Kattintson a **Mentés** gombra. A művelet eltarthat néhány percig.

    ![A tanúsítvány mentését bemutató képernyőkép.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> A HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a PowerShell-paranccsal töltheti fel `New-AzApiManagementSystemCertificate` .

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ügyféltanúsítvány törlése

A tanúsítvány törléséhez kattintson a helyi menü **...** lehetőségre, és válassza a **Törlés** lehetőséget a tanúsítvány mellett.

![HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok törlése](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a

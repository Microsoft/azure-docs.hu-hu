---
title: Egyéni tartománynév beállítása saját üzemeltetésű Azure API Management átjáróhoz | Microsoft Docs
description: Ez a témakör az egyéni tartománynevek saját üzemeltetésű Azure API Management átjáróhoz való konfigurálásának lépéseit ismerteti.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490996"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Egyéni tartománynév beállítása saját üzemeltetésű átjáróhoz

Ha saját üzemeltetésű [Azure API Management-átjárót](self-hosted-gateway-overview.md)hoz létre, az nem rendelkezik állomásnévvel, és az IP-címe alapján kell hivatkozni rá. Ez a cikk bemutatja, hogyan képezhető le egy meglévő egyéni DNS-név (más néven állomásnév) egy saját üzemeltetésű átjáróra.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

-   Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Egy API Management példány. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
- Egy saját üzemeltetésű átjáró. További információ: [a saját üzemeltetésű átjáró kiépítése](api-management-howto-provision-self-hosted-gateway.md)
-   Az Ön vagy a szervezete tulajdonában álló egyéni tartománynév. Ez a témakör nem nyújt útmutatást az Egyéni tartománynév beszerzéséhez.
-   A DNS-kiszolgálón tárolt DNS-rekord, amely az egyéni tartománynevet a saját üzemeltetésű átjáró IP-címére képezi le. Ez a témakör nem nyújt útmutatást a DNS-rekordok üzemeltetéséhez.
-   Rendelkeznie kell egy nyilvános és titkos kulccsal rendelkező érvényes tanúsítvánnyal (. PFX). A tulajdonos vagy a tulajdonos alternatív neve (SAN) meg kell egyeznie a tartománynévvel (Ez lehetővé teszi, hogy az API Management-példány biztonságosan tegye elérhetővé a TLS protokollon keresztüli URL-címeket).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Egyéni tartományi tanúsítvány hozzáadása a API Management szolgáltatáshoz

Egyéni tartományi tanúsítvány hozzáadása (. PFX) fájlt a API Management példányára, vagy hivatkozzon a Azure Key Vault tárolt tanúsítványra. [Az Azure API Managementban az ügyféltanúsítvány-alapú hitelesítés használatával](api-management-howto-mutual-certificates.md)hajtsa végre a következő lépéseket: Secure backend Services.

> [!NOTE]
> Azt javasoljuk, hogy használjon Key Vault-tanúsítványt a saját üzemeltetésű átjáró tartományhoz.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Egyéni tartománynév beállítása a saját üzemeltetésű átjáróhoz a Azure Portal használatával

1. Válassza ki az **átjárókat** az **üzembe helyezés és az infrastruktúra** területen.
2. Válassza ki azt a saját üzemeltetésű átjárót, amelyhez konfigurálni kívánja a tartománynevet.
3. Válassza a **gazdagépek** lehetőséget a **Beállítások** területen.
4. Válassza a **+ Hozzáadás** lehetőséget
5. Adja meg az állomásnév erőforrás nevét a **név** mezőben.
6. Adja meg a tartomány nevét az **állomásnév** mezőben.
7. Válasszon ki egy tanúsítványt a **tanúsítvány** legördülő listából.
8. Jelölje be az **ügyféltanúsítvány egyeztetése** jelölőnégyzetet, ha az átjárón keresztül elérhető API-k bármelyike ügyféltanúsítvány-alapú hitelesítést használ.
    > [!WARNING]
    > Ezt a beállítást az átjáróhoz konfigurált összes tartománynév megosztja.
9. A **Hozzáadás** gombra kattintva rendelje hozzá az egyéni tartománynevet a kiválasztott saját üzemeltetésű átjáróhoz.

## <a name="next-steps"></a>Következő lépések

[A szolgáltatás frissítése és méretezése](upgrade-and-scale.md)

---
title: 'Azure AD-bérlő felhasználói VPN-kapcsolatokhoz: Azure AD-hitelesítés'
description: Az Azure Virtual WAN felhasználói VPN (pont – hely) használatával csatlakozhat a VNet az Azure AD-hitelesítés használatával
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91367852"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Azure Active Directory bérlő előkészítése a felhasználói VPN OpenVPN protokoll kapcsolataihoz

Ha a IKEv2 protokollon keresztül csatlakozik a virtuális hubhoz, használhat tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést. Az OpenVPN protokoll használatakor azonban Azure Active Directory hitelesítést is használhat. Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure AD-bérlőt a virtuális WAN-felhasználók VPN (pont – hely) számára az OpenVPN-hitelesítés használatával.

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN &reg; protokoll kapcsolatai esetén támogatott.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. az Azure AD-bérlő létrehozása

Ellenőrizze, hogy rendelkezik-e Azure AD-Bérlővel. Ha nem rendelkezik Azure AD-Bérlővel, létrehozhat egyet az [új bérlő létrehozása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) című cikk lépéseivel:

* Szervezet neve
* Kezdeti tartománynév

Példa:

   ![Új Azure AD-bérlő](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-bérlői felhasználók létrehozása

Ezután hozzon létre két felhasználói fiókot az újonnan létrehozott Azure AD-bérlőben, egy globális rendszergazdai fiókban és egy felhasználói fiókban. A felhasználói fiók használható az OpenVPN-hitelesítés tesztelésére, és a globális rendszergazdai fiók az Azure VPN-alkalmazás regisztrálásához való jóváhagyást fog használni. Miután létrehozott egy Azure AD-felhasználói fiókot, hozzárendelhet egy **címtárbeli szerepkört** a felhasználóhoz a rendszergazdai engedélyek delegálásához.

Az [ebben a cikkben](../active-directory/fundamentals/add-users-azure-active-directory.md) ismertetett lépések segítségével hozza létre az Azure ad-bérlőhöz tartozó két felhasználót. Ügyeljen arra, hogy a **címtárbeli szerepkört** a **globális rendszergazda** számára a létrehozott fiókok egyikén változtassa meg.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. adja meg az Azure VPN-alkalmazás regisztrálásának jóváhagyását

1. Jelentkezzen be az Azure Portalra a **globális rendszergazdai** szerepkörhöz rendelt felhasználóként.

2. Ezután adjon rendszergazdai jóváhagyást a szervezet számára, amely lehetővé teszi, hogy az Azure VPN-alkalmazás jelentkezzen be, és olvassa be a felhasználói profilokat. Másolja és illessze be a böngésző címsorába a telepítési helyére vonatkozó URL-címet:

    Nyilvános

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. Ha a rendszer kéri, válassza a **globális rendszergazdai** fiókot.

    ![Könyvtár azonosítója](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Ha a rendszer kéri, válassza az **elfogadás** lehetőséget.

    ![Képernyőfelvétel: a szervezete és további információi elfogadását kérő párbeszédpanel.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Az Azure AD-ben a **vállalati alkalmazásokban** most látnia kell az **Azure VPN** listáját.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Következő lépések

Ahhoz, hogy az Azure AD-hitelesítés használatával csatlakozhasson a virtuális hálózatokhoz, létre kell hoznia egy felhasználói VPN-konfigurációt, és hozzá kell rendelnie egy virtuális hubhoz. Lásd: [Az Azure ad-hitelesítés konfigurálása pont – hely kapcsolathoz az Azure-hoz](virtual-wan-point-to-site-azure-ad.md).

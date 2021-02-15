---
title: 'VPN Gateway: Azure AD-bérlő a P2S VPN-kapcsolatokhoz: Azure AD-hitelesítés'
description: Ismerje meg, hogyan állíthat be egy Azure AD-bérlőt a P2S Open VPN-hitelesítéshez.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: bff1eec0152ab0f57edd212adf6b14f7b588fb51
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390164"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory-bérlő létrehozása P2S OpenVPN-protokollkapcsolatokhoz

A VNet való csatlakozáskor tanúsítványalapú hitelesítést vagy RADIUS-hitelesítést használhat. Ha azonban a nyílt VPN protokollt használja, akkor Azure Active Directory hitelesítést is használhat. Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure AD-bérlőt a P2S Open VPN-hitelesítéshez.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. az Azure AD-bérlő ellenőrzése

Ellenőrizze, hogy rendelkezik-e Azure AD-Bérlővel. Ha nem rendelkezik Azure AD-Bérlővel, létrehozhat egyet az [új bérlő létrehozása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) című cikk lépéseivel:

* Szervezet neve
* Kezdeti tartománynév

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Új Azure AD-bérlő" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD-bérlői felhasználók létrehozása

Az Azure AD-bérlőnek a következő fiókokra van szüksége: egy globális rendszergazdai fiók és egy fő felhasználói fiók. A fő felhasználói fiókot a rendszer fő beágyazási fiókként (szolgáltatásfiók) használja. Azure AD-bérlői felhasználói fiók létrehozásakor a címtárbeli szerepkört a létrehozni kívánt felhasználó típusára kell beállítania.

Az Azure AD-bérlő legalább két felhasználójának létrehozásához használja a [felhasználók hozzáadása vagy törlése – Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) című témakör lépéseit. Ügyeljen arra, hogy a **címtárbeli szerepkört** a fióktípus létrehozásához módosítsa:

* Globális rendszergazda
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. az Azure AD-hitelesítés engedélyezése a VPN-átjárón

1. Keresse meg a hitelesítéshez használni kívánt címtár AZONOSÍTÓját. A Active Directory lap Tulajdonságok szakaszában szerepel.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="A könyvtár tulajdonságait megjelenítő képernyőkép" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Másolja ki a Címtár-azonosítót.

1. A **globális rendszergazdai** szerepkörhöz rendelt felhasználóként jelentkezzen be a Azure Portalba.

1. Ezután adja meg a rendszergazdai engedélyt. Másolja és illessze be a böngésző címsorába a telepítési helyére vonatkozó URL-címet:

   Nyilvános

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Microsoft Cloud Germany

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Ha olyan globális rendszergazdai fiókot használ, amely nem natív az Azure AD-bérlőn a beleegyezőség biztosításához, az URL-címben cserélje le az "általános" kifejezést az Azure AD-címtár azonosítójával. Előfordulhat, hogy a "Common" kifejezést a címtár-azonosítójával együtt kell lecserélnie bizonyos más esetekben is.
   >

1. Ha a rendszer kéri, válassza a **globális rendszergazdai** fiókot.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Fiók választása" border="false":::
1. Ha a rendszer kéri, válassza az **elfogadás** lehetőséget.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="A képernyőképen a szervezete által kért üzenet-engedélyek és az elfogadás lehetőség látható." border="false":::
1. Az Azure AD-ben a **vállalati alkalmazásokban** a felsorolt **Azure VPN** látható.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="A minden alkalmazás lapot megjelenítő képernyőkép." lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Ha még nem rendelkezik működő pont – hely környezettel, kövesse az utasításokat, és hozzon létre egyet. Lásd: [pont – hely típusú VPN létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) és konfigurálása pont – hely típusú VPN-átjáró létrehozásához és konfigurálásához.

    > [!IMPORTANT]
    > Az OpenVPN esetében az alapszintű SKU nem támogatott.

1. Engedélyezze az Azure AD-hitelesítést a VPN-átjárón, ha a **pont – hely konfigurációhoz** navigál, és az **OpenVPN (SSL)** lehetőséget **adja meg bújtatási típusként**. A **hitelesítési típusként** válassza a **Azure Active Directory** lehetőséget, majd adja meg a **Azure Active Directory** szakaszban található információkat.

   * **Bérlő:** TenantID az Azure AD-bérlőhöz ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Célközönség:** Az "Azure VPN" Azure AD Enterprise-alkalmazás ApplicationID ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **Kiállító**: a biztonságos jogkivonat-szolgáltatás URL-címe ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="SAzure VPN" border="false":::

   > [!NOTE]
   > Győződjön meg róla, hogy az érték végén szerepelnie kell egy záró perjelnek `AadIssuerUri` . Ellenkező esetben a kapcsolatok sikertelenek lehetnek.
   >

1. A **VPN-ügyfél letöltése** hivatkozásra kattintva hozza létre és töltse le a profilt.

1. Bontsa ki a letöltött zip-fájlt.

1. Tallózással keresse meg a kibontott "AzureVPN" mappát.

1. Jegyezze fel a "azurevpnconfig.xml" fájl helyét. A azurevpnconfig.xml a VPN-kapcsolat beállítását tartalmazza, és közvetlenül importálható az Azure VPN-ügyfélalkalmazás alkalmazásba. Ezt a fájlt az összes olyan felhasználó számára is terjesztheti, akiknek e-mailben vagy más módon kell csatlakozniuk. A felhasználónak érvényes Azure AD-beli hitelesítő adatokra lesz szüksége a sikeres kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések

Hozzon létre és konfiguráljon egy VPN-ügyféloldali profilt. Lásd: [VPN-ügyfél konfigurálása P2S VPN-kapcsolatokhoz](openvpn-azure-ad-client.md).

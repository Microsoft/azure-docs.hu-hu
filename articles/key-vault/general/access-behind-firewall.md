---
title: Hozzáférés Key Vault tűzfal mögött – Azure Key Vault | Microsoft Docs
description: Ismerje meg azokat a portokat, gazdagépeket vagy IP-címeket, amelyek lehetővé teszik a Key Vault-ügyfélalkalmazások tűzfal mögötti alkalmazását a kulcstartó eléréséhez.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: ca5842fb268c20f8ae58eb5f683229c4ae3919f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93289164"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Az Azure Key Vault elérése tűzfal mögött

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Milyen portokat, gazdagépeket vagy IP-címeket kell megnyitnia a Key Vault-ügyfélalkalmazás tűzfal mögötti engedélyezéséhez a Key Vault eléréséhez?

A kulcstároló eléréséhez a kulcstároló-ügyfélalkalmazásnak a különféle funkciók biztosításához képesnek kell lennie több végpont elérésére:

* Hitelesítés az Azure Active Directory (Azure AD) használatával.
* Az Azure Key Vault kezelése. Ide tartozik a hozzáférési szabályzatok létrehozása, olvasása, frissítése, törlése és beállítása az Azure Resource Manageren keresztül.
* A magában a Key Vaultban tárolt objektumok (kulcsok és titkos kulcsok) elérése és kezelése a Key Vault speciális végpontján (pl. `https://yourvaultname.vault.azure.net`) keresztül.  

A konfigurációtól és a környezettől függően lehetnek bizonyos eltérések.

## <a name="ports"></a>Portok

Mindhárom funkció (a hitelesítés, a felügyelet és az adatsíkhoz való hozzáférés) Key Vault felé irányuló összes forgalma a 443-as HTTPS-porton keresztül zajlik. A CRL használata esetén azonban alkalmanként HTTP-forgalom is előfordul (a 80-as porton keresztül). Az OCSP-t támogató ügyfelek nem érhetik el a CRL-t, de esetenként elérhetik [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl) .  

## <a name="authentication"></a>Hitelesítés

A Key Vault-ügyfélalkalmazásoknak a hitelesítés érdekében hozzá kell férniük az Azure Active Directory-végpontokhoz. A használt végpont függ az Azure AD bérlői konfigurációjától, valamint a név típusától (felhasználói név, szolgáltatásnév), illetve a fiók típusától (pl. Microsoft-fiók vagy munkahelyi/iskolai fiók).  

| Résztvevő típusa | Végpont:port |
| --- | --- |
| A Microsoft-fiókot használó felhasználó<br> (például: user@hotmail.com) |**Globális**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US government:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443<br><br> és <br>login.live.com:443 |
| Az Azure AD-vel munkahelyi vagy iskolai fiókot használó felhasználó vagy szolgáltatás (például user@contoso.com) |**Globális**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US government:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443 |
| Munkahelyi vagy iskolai fiókot és az Active Directory Federation Servicest (AD FS) vagy más összevont végpontot (például: user@contoso.com) használó felhasználó vagy szolgáltatás. |A munkahelyi vagy iskolai fiókhoz tartozó valamennyi végpont plusz az AD FS vagy más összevont végpontok |

Más összetett forgatókönyvek is előfordulhatnak. További információkért tekintse meg az [Azure Active Directory hitelesítési folyamatát](../../active-directory/develop/authentication-vs-authorization.md), az [alkalmazások Azure Active Directoryval való integrálását](../../active-directory/develop/active-directory-how-to-integrate.md) és [az Active Directory hitelesítési protokolljait ismertető cikket](/previous-versions/azure/dn151124(v=azure.100)).  

## <a name="key-vault-management"></a>A Key Vault felügyelete

A Key Vault felügyeletéhez (CRUD és hozzáférési házirend beállítása) a Key Vault-ügyfélalkalmazásnak el kell érnie az Azure Resource Manager-végpontot.  

| Művelet típusa | Végpont:port |
| --- | --- |
| A Key Vault vezérlési síkjával végzett műveletek<br> az Azure Resource Manager használatával |**Globális**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |
| Microsoft Graph API |**Globális**<br> graph.microsoft.com:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure US government:**<br> graph.microsoft.com:443<br><br> **Azure Germany:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Key Vault-műveletek

Az összes Key Vault-objektummal (kulcsok és titkos kulcsok) végzett felügyeleti és titkosítási művelethez a Key Vault-ügyfélnek el kell érnie a Key Vault-végpontot. A végpont DNS-utótagja a Key Vault helyétől függően eltérő. A Key Vault-végpont formátuma az alábbi táblázatban látható módon: *<tároló-neve>*.*<területspecifikus-dns-utótag>*.  

| Művelet típusa | Végpont:port |
| --- | --- |
| A kulcsokon végzett műveletek, beleértve a titkosítási műveleteket is; kulcsok és titkos létrehozása, olvasása, frissítése és törlése; címkék és egyéb attribútumok beállítása és olvasása kulcstároló-objektumokon (kulcsokon vagy titkokon) |**Globális**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Azure US government:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP-címtartományok

A Key Vault szolgáltatás egyéb Azure-erőforrásokat is használ, amilyen például a PaaS-infrastruktúra. Éppen ezért nem lehetséges megadni IP-címek meghatározott tartományát, amellyel a Key Vault szolgáltatás végpontjai egy adott időpontban rendelkeznek. Ha a tűzfal csak az IP-címtartományok használatát támogatja, tekintse meg Microsoft Azure Datacenter IP-címtartományok dokumentumait a következő helyen:
* [Nyilvános](https://www.microsoft.com/en-us/download/details.aspx?id=56519)
* [US Gov](https://www.microsoft.com/en-us/download/details.aspx?id=57063)
* [Németország](https://www.microsoft.com/en-us/download/details.aspx?id=57064)
* [Kína](https://www.microsoft.com/en-us/download/details.aspx?id=57062)

A Hitelesítés és identitás (Azure Active Directory) egy globális szolgáltatás, és előfordulhat, hogy értesítés nélkül ad át feladatot más régióknak vagy helyez át forgalmat. Ebben a forgatókönyvben minden, a [Hitelesítés és identitás – IP-címek](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) szakaszban felsorolt IP-tartományt hozzá kell adni a tűzfalhoz.

## <a name="next-steps"></a>Következő lépések

Ha kérdése van a Key Vaultval kapcsolatban, látogasson el a [Microsoft Q&a Azure Key Vault kérdéseit tartalmazó oldalra](/answers/topics/azure-key-vault.html).
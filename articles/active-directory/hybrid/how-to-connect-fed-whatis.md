---
title: Azure AD Connect és összevonás | Microsoft Docs
description: Ez a lap a Azure AD Connectt használó AD FS műveletekre vonatkozó dokumentáció központi helye.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a97142e0c512f4f95235ad08c94c852906d3efd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92095856"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect és összevonás
A Azure Active Directory (Azure AD) összekapcsolása lehetővé teszi a helyszíni Active Directory összevonási szolgáltatások (AD FS) (AD FS) és az Azure AD-vel való összevonás konfigurálását. Az összevonási bejelentkezés lehetővé teszi a felhasználók számára, hogy az Azure AD-alapú szolgáltatásokba jelentkezzenek be helyszíni jelszavával – és közben a vállalati hálózaton, anélkül, hogy újra meg kellene adniuk a jelszavukat. A AD FS összevonásával telepítheti AD FS új telepítését, vagy megadhat egy meglévő telepítést egy Windows Server 2012 R2-farmban.

Ez a témakör a Azure AD Connect összevonásával kapcsolatos funkciókról nyújt tájékoztatást. Felsorolja az összes kapcsolódó témakörre mutató hivatkozásokat. Azure AD Connectre mutató hivatkozásokat a helyszíni [identitások integrálása Azure Active Directory segítségével](whatis-hybrid-identity.md)című témakörben talál.

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: összevonási témakörök
| Témakör | Mit takar, és mikor érdemes beolvasni |
|:--- |:--- |
| **Felhasználói bejelentkezési beállítások Azure AD Connect** | |
| [Felhasználói bejelentkezési beállítások ismertetése](plan-connect-user-signin.md) |Ismerje meg a felhasználói bejelentkezési lehetőségek különböző beállításait, valamint azt, hogy ezek milyen hatással vannak az Azure bejelentkezési felhasználói felületére. |
| **AD FS telepítése Azure AD Connect használatával** | |
| [Előfeltételek](how-to-connect-install-custom.md#ad-fs-configuration-prerequisites) |Tekintse meg a sikeres AD FS telepítésének előfeltételeit Azure AD Connect használatával. |
| [AD FS Farm konfigurálása](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Új AD FS Farm telepítése Azure AD Connect használatával. |
| [Összevonása az Azure AD-vel alternatív bejelentkezési azonosító használatával](how-to-connect-fed-management.md#alternateid) | Összevonás konfigurálása alternatív bejelentkezési azonosító használatával  |
| **A AD FS konfigurációjának módosítása** | |
| [A megbízhatóság javítása](how-to-connect-fed-management.md#repairthetrust) |Javítsa ki az aktuális megbízhatósági kapcsolatot a helyszíni AD FS és az Microsoft 365/Azure között. |
| [Új AD FS-kiszolgáló hozzáadása](how-to-connect-fed-management.md#addadfsserver) |A kezdeti telepítés után kibonthat egy AD FS farmot egy további AD FS-kiszolgálóval. |
| [Új AD FS WAP-kiszolgáló hozzáadása](how-to-connect-fed-management.md#addwapserver) |A kezdeti telepítés után kibonthat egy AD FS farmot egy további webalkalmazás-proxy (WAP) kiszolgálóval. |
| [Új összevont tartomány hozzáadása](how-to-connect-fed-management.md#addfeddomain) |Adjon hozzá egy másik tartományt az Azure AD-vel való összevonáshoz. |
| [A TLS/SSL-tanúsítvány frissítése](how-to-connect-fed-ssl-update.md)| Egy AD FS Farm TLS/SSL-tanúsítványának frissítése. |
| [Microsoft 365 és az Azure AD összevonási tanúsítványainak megújítása](how-to-connect-fed-o365-certs.md)|Újítsa meg O365-tanúsítványát az Azure AD-vel.|
| **Egyéb összevonási konfiguráció** | |
| [Több Azure AD-példány összevonása egyetlen AD FS-példánnyal](how-to-connect-fed-single-adfs-multitenant-federation.md) | Több Azure AD-összevonása egyetlen AD FS farmtal| 
| [Egyéni vállalati embléma/illusztráció hozzáadása](how-to-connect-fed-management.md#customlogo) |Módosítsa a bejelentkezési élményt úgy, hogy megadja a AD FS bejelentkezési oldalán megjelenő egyéni emblémát. |
| [Bejelentkezési Leírás hozzáadása](how-to-connect-fed-management.md#addsignindescription) |Módosítsa a bejelentkezési leírást a AD FS bejelentkezési oldalára. |
| [AD FSi jogcím szabályainak módosítása](how-to-connect-fed-management.md#modclaims) |A Azure AD Connect szinkronizálási konfigurációnak megfelelő jogcímek módosítása vagy hozzáadása AD FS. |


## <a name="additional-resources"></a>További források
* [Egyesítő két Azure AD-t egyetlen AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Az AD FS üzembe helyezése az Azure-ban](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Magas rendelkezésre állású, több földrajzi AD FS üzembe helyezés az Azure-ban az Azure-ban Traffic Manager](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)
---
title: Az Azure AD Connect és összevonás |} A Microsoft Docs
description: Ezen a lapon a központi hely, az Azure AD Connect használata az AD FS-műveletek összes dokumentumaiból.
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
ms.topic: conceptual
origin.date: 10/09/2018
ms.date: 03/15/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67ae5d2661371c256f753d05eb496d2cd53a0017
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60350490"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect és összevonás
Az Azure Active Directory (Azure AD) Connect konfigurálását teszi lehetővé, összevonás a helyszíni Active Directory összevonási szolgáltatások (AD FS) és az Azure ad-ben. Az összevonási jelentkezzen be engedélyezheti a felhasználók jelentkezhetnek be, az Azure AD-alapú szolgáltatásokat a helyszíni jelszavak –, és a vállalati hálózaton, írja be újra a jelszavukat anélkül. Az összevonási beállítás és az AD FS használatával telepíthet egy új AD FS telepítése, vagy megadhat egy meglévő telepítését egy Windows Server 2012 R2-farmban.

Ez a témakör az otthoni funkciók összevonási kapcsolatos információk az Azure AD Connect. A lista felsorolja az összes kapcsolódó témakörökre mutató hivatkozásokat. Az Azure AD Connect hivatkozások: [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Az Azure AD Connect: összevonási kapcsolatos témakörök
| Témakör | Azt ismerteti, és mikor érdemes elolvasni |
|:--- |:--- |
| **A felhasználói bejelentkezés lehetőségei az Azure AD Connectben** | |
| [Felhasználói bejelentkezési lehetőségek ismertetése](plan-connect-user-signin.md) |Ismerje meg a különböző felhasználói bejelentkezés lehetőségei és azok az Azure bejelentkezési felhasználói élményt. |
| **Az AD FS telepítése az Azure AD Connect használatával** | |
| [Előfeltételek](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Tekintse meg az AD FS az Azure AD Connect használatával a sikeres telepítés előfeltételeit. |
| [Az AD FS-farm konfigurálása](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Egy új AD FS-farm telepítése az Azure AD Connect használatával. |
| [Az Azure AD-bA a másodlagos bejelentkezési Azonosítót összevonása](how-to-connect-fed-management.md#alternateid) | Alternatív bejelentkezési azonosítóval összevonás konfigurálása  |
| **Az AD FS-konfiguráció módosítása** | |
| [A bizalmi kapcsolat javítása](how-to-connect-fed-management.md#repairthetrust) |A jelenlegi bizalmi kapcsolat javítása között a helyszíni AD FS és az Office 365 vagy Azure. |
| [Új AD FS-kiszolgáló hozzáadása](how-to-connect-fed-management.md#addadfsserver) |Bontsa ki a további AD FS-kiszolgáló AD FS farmot kezdeti telepítés után. |
| [Új AD FS-WAP-kiszolgáló hozzáadása](how-to-connect-fed-management.md#addwapserver) |Bontsa ki az AD FS-farm további webalkalmazás-proxykiszolgálóként (WAP) kiszolgáló a kezdeti telepítés után. |
| [Új összevont tartomány hozzáadása](how-to-connect-fed-management.md#addfeddomain) |Adjon hozzá egy másik tartomány az Azure ad-ben. |
| [Az SSL-tanúsítvány frissítése](how-to-connect-fed-ssl-update.md)| Frissítse az AD FS-farm SSL-tanúsítványt. |
| [-Office 365 és az Azure AD összevonási tanúsítványainak megújítása](how-to-connect-fed-o365-certs.md)|Az Azure ad-vel az O365-tanúsítvány megújítása.|
| **Más összevonási konfiguráció** | |
| [Több Azure AD-példány összevonása egyetlen AD FS-példánnyal](how-to-connect-fed-single-adfs-multitenant-federation.md) | Egyetlen AD FS-farm több Azure AD összevonása| 
| [Adjon hozzá egy egyéni vállalati emblémát/ábra](how-to-connect-fed-management.md#customlogo) |A bejelentkezési élmény módosítsa úgy az egyéni embléma, amely az AD FS bejelentkezési oldal jelenik meg. |
| [A bejelentkezési leírásának hozzáadása](how-to-connect-fed-management.md#addsignindescription) |Módosítsa a bejelentkezés leírását az AD FS bejelentkezési oldalára. |
| [Az AD FS-jogcímszabályok módosítása](how-to-connect-fed-management.md#modclaims) |Módosíthatja, vagy adjon hozzá szabályokat, amelyek megfelelnek az AD FS az Azure AD Connect szinkronizálási konfigurációjában. |


## <a name="additional-resources"></a>További források
* [Többtartományos két Azure ad-ben egyetlen AD FS-sel](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Az AD FS üzembe helyezése az Azure-ban](how-to-connect-fed-azure-adfs.md)
* [Magas rendelkezésre állás földrajzi határokon átívelő AD FS üzembe helyezése az Azure-ban Azure Traffic Managerrel](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

<!-- Update_Description: update metedata properties -->
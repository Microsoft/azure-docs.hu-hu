---
title: Azure AD Connect a Microsoft Cloud németországi adatközpontjában
description: Az Azure AD Connect a helyszíni címtárakat integrálja az Azure Active Directoryval. Ez lehetővé teszi az Azure AD-vel integrált Microsoft 365-, Azure-és SaaS-alkalmazások közös identitásának megadását.
keywords: Azure AD Connect bemutatása, Azure AD Connect áttekintése, mi az Azure AD Connect, active directory telepítése, Németország, Fekete-erdő
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246f8326f7112825de98ee824cf4bab7952a7878
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90014587"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect a Microsoft Cloud németországi adatközpontjában – nyilvános előzetes verzió
## <a name="introduction"></a>Bevezetés
Az Azure AD Connect szinkronizálást biztosít a helyszíni Active Directory és az Azure Active Directory között.
A [Microsoft Cloud németországi adatközpontja](https://azure.microsoft.com/global-infrastructure/germany/
) esetében az eljárások jelentős részét a kezelőnek kell elvégeznie. Microsoft Cloud Németország használatakor a következő információkat kell figyelembe vennie:

* A következő URL-címeket meg kell nyitni egy proxykiszolgálón ahhoz, hogy sikeres lehessen a szinkronizálás:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Visszavont tanúsítványok listái (CRL-ek)
* Az Azure AD címtárba való bejelentkezéskor az onmicrosoft.de tartományba tartozó fiókot kell használnia.

 
## <a name="download"></a>Letöltés
Az Azure AD Connect a portál Azure AD Connect paneljéről tölthető le.  Az Azure AD Connect panelt az alábbi útmutatás alapján keresheti meg.

### <a name="the-azure-ad-connect-blade"></a>Az Azure AD Connect panel
Miután bejelentkezett a Azure Portalba:

1. Válassza a Tallózás lehetőséget.
2. Válassza az Azure Active Directory elemet.
3. Ezután válassza az Azure AD Connect elemet.

Ezeket a részleteket fogja látni:

![Az Azure AD Connect panel](./media/reference-connect-germany/germany1.png)

A következő táblázat a panelen látható funkciókat ismerteti.

| Cím | Leírás |
| --- | --- |
| SZINKRONIZÁLÁS ÁLLAPOTA |Arról tájékoztatja, hogy a szinkronizálás engedélyezve van-e, vagy le van tiltva. |
| LEGUTÓBBI SZINKRONIZÁLÁS |A legutóbbi alkalom, amikor sikeresen befejeződött egy szinkronizálás. |
| ÖSSZEVONT TARTOMÁNYOK |Azt mutatja, hogy jelenleg hány összevont tartomány van konfigurálva. |

## <a name="installation"></a>Telepítés
Az Azure AD Connect telepítéséhez használhatja az [itt](how-to-connect-install-roadmap.md) található dokumentációt.

## <a name="advanced-features-and-additional-information"></a>Speciális szolgáltatások és további információk
További információ az egyéni beállításokról vagy a speciális konfigurációkról: [a helyszíni identitások integrálása a Azure Active Directory](whatis-hybrid-identity.md)használatával. Ez a lap a további útmutatással kapcsolatos információkat és hivatkozásokat ismerteti.


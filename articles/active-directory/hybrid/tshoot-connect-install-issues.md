---
title: A Azure AD Connect telepítési problémáinak elhárítása | Microsoft Docs "
description: Ez a témakör a Azure AD Connect telepítésével kapcsolatos hibák elhárításának lépéseit ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89275861"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Hibaelhárítás: Azure AD Connect telepítési problémák

## <a name="recommended-steps"></a>**Javasolt lépések**
Ellenőrizze, hogy a [Azure ad Connect telepítési típusa](./how-to-connect-install-select-installation.md) megfelelő-e. Ha teljesíti az expressz telepítés feltételeit, javasoljuk, hogy az expressz telepítéssel lépjen tovább. Az expressz telepítés a telepítés befejezéséhez minimálisan szükséges beállításokat biztosít, ezért a problémák kevésbé valószínűek. 

Ha azonban nem felel meg az expressz telepítési feltételeknek, és el kell végeznie az Egyéni telepítést, akkor itt talál néhány ajánlott eljárást, amelyekkel elkerülheti a gyakori problémákat. Az egyszerűség kedvéért csak a szelektív beállítások szerepelnek itt:

* Győződjön meg arról, hogy Ön rendszergazda azon a számítógépen, amelyre a HRE-kapcsolatot telepíti. Jelentkezzen be a gépre ugyanazzal a rendszergazdai hitelesítő adatokkal.

* Ha meglévő SQL Server kíván használni, a következő lapon minden beállítás alapértelmezett értékre kerül, kivéve a "meglévő SQL Server használata" lehetőséget. [További](./how-to-connect-install-custom.md) információ az egyéni telepítési beállítások használatáról. 

    ![Meglévő SQL Server használata](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* A következő lapon válassza az "új AD-fiók létrehozása" lehetőséget, hogy elkerülje a meglévő fiókkal kapcsolatos engedélyek megadását.

    ![AD Forest-fiók](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Gyakori problémák**

* [A helyszíni Active Directory kapcsolódási problémái](./reference-connect-adconnectivitytools.md).

* [Kapcsolódási problémák az online Azure Active Directory](./tshoot-connect-connectivity.md).

* [A helyszíni Active Directoryekkel kapcsolatos engedélyek](./how-to-connect-configure-ad-ds-connector-account.md).

## <a name="recommended-documents"></a>**Ajánlott dokumentumok**
* [Az Azure AD Connect előfeltételei](./how-to-connect-install-prerequisites.md)
* [Az Azure AD Connect telepítési típusának kiválasztása](./how-to-connect-install-select-installation.md)
* [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](./how-to-connect-install-express.md)
* [Az Azure AD Connect testreszabott telepítése](./how-to-connect-install-custom.md)
* [Azure AD Connect: frissítés egy előző verzióról a legújabbra](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: Mi az átmeneti kiszolgáló?](./plan-connect-topologies.md#staging-server)
* [Mi az az ADConnectivityTool PowerShell-modul?](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>Következő lépések
- [Azure ad Connect szinkronizálás](how-to-connect-sync-whatis.md).
- [Mi az a hibrid identitás?](whatis-hybrid-identity.md)
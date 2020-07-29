---
title: 'Hibrid identitás: a címtár-integrációs eszközök összehasonlítása | Microsoft Docs'
description: Ez az oldal egy átfogó táblázatot biztosít, amely összehasonlítja a címtár-integrációhoz használható különböző címtár-integrációs eszközöket.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80811104"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hibrid identitás: a címtár-integrációs eszközök összehasonlítása
Az évek során a címtár-integrációs eszközök bővültek és továbbfejlődtek.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) A FIM [és a](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) webkiszolgáló továbbra is támogatott, és elsődlegesen lehetővé teszi a helyszíni rendszerek közötti szinkronizálást.   A [FIM Windows Azure ad Connector](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) mind a FIM, mind a (z AD DS Azure ad Connect Azure ad Connect) rendszerben támogatott, de új központi telepítések esetén nem ajánlott – a helyszíni forrásokkal, például a megjegyzésekkel vagy az SAP HCM-vel rendelkező ügyfeleknek a Rendszerfelügyeleti webszolgáltatások használatával kell használniuk Active Directory tartományi szolgáltatások (AD DS).
- [Azure ad Connect szinkronizálás](how-to-connect-sync-whatis.md) magában foglalja a korábban az rsync-ben és a Azure ad-szinkronizáló-ben kiadott összetevőket és funkciókat a AD DS erdők és az Azure ad közötti szinkronizáláshoz.  
- [Azure ad Connect a Felhőbeli kiépítés](../cloud-provisioning/what-is-cloud-provisioning.md) egy új Microsoft-ügynök, amely a ad DSról az Azure ad-be való szinkronizálásra szolgál, és olyan forgatókönyvek esetében hasznos, mint például az egyesítés és a beszerzés, ahol a beszerzett vállalat ad-erdői el vannak különítve a fölérendelt vállalat

Ha többet szeretne megtudni a Azure AD Connect Sync és Azure AD Connect Cloud kiépítés közötti különbségekről, tekintse meg a következő cikket: [Mi az Azure ad Connect Cloud kiépítés?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).


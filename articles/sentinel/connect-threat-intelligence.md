---
title: A fenyegetési intelligencia-adatok összekapcsolásának az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan kapcsolódhat a veszélyforrások felderítéséhez az Azure Sentinel szolgáltatáshoz.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96121751"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Fenyegetésfelderítési szolgáltatók adatainak csatlakoztatása

> [!IMPORTANT]
> A veszélyforrások felderítésére szolgáló adatösszekötők az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhetők el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel segítségével importálhatja a szervezete által használt veszélyforrás-kijelzőket, ami növelheti a biztonsági elemzők számára az ismert fenyegetések felismerésének és rangsorolásának képességét. Az Azure Sentinel számos funkciója elérhetővé válik vagy bővül:

- Az **elemzések** tartalmazzák az ütemezett szabálygyűjtemény készletét, amely lehetővé teszi, hogy riasztásokat és incidenseket hozzon létre a fenyegetési mutatókon alapuló naplózási események egyezése alapján.

- A **munkafüzetek** összesített információkat biztosítanak az Azure sentinelbe importált veszélyforrási mutatókról, valamint az elemzési szabályok alapján létrehozott összes riasztásról, amelyek megfelelnek a fenyegetési mutatóknak.

- A **vadászati** lekérdezések lehetővé teszik a biztonsági nyomozók számára, hogy a gyakori vadászati forgatókönyvek kontextusában használják a veszélyforrások mutatóit.

- A **jegyzetfüzetek** a veszélyforrások kivizsgálásakor és a rosszindulatú viselkedések kivizsgálása során is használhatják a fenyegetéseket.

A veszélyforrások indikátorait az Azure Sentinel szolgáltatásba továbbíthatja a következő szakaszban felsorolt integrált veszélyforrások elleni hírszerzési platform (TIP) termékek egyikével, a TAXII-kiszolgálókhoz való csatlakozással vagy a [Microsoft Graph Security TIINDICATORS API](/graph/api/resources/tiindicator)-val való közvetlen integráció használatával.

## <a name="integrated-threat-intelligence-platform-products"></a>Integrált veszélyforrások elleni hírszerzési platform termékei

- [MICSKÓ nyílt forráskódú veszélyforrások elleni intelligencia platform](https://www.misp-project.org/)
    
    A MICSKÓ-példányokkal rendelkező ügyfelek számára a veszélyforrások Microsoft Graph biztonsági API-ra történő áttelepítéséhez a következő témakörben talál további információt: [micskó Microsoft Graph biztonsági parancsfájl](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    A ThreatStream-integrátor és-bővítmények letöltéséhez, valamint az ThreatStream Intelligence a Microsoft Graph Security API-hoz való csatlakoztatására vonatkozó utasításokért tekintse meg a [ThreatStream letöltések](https://ui.threatstream.com/downloads) lapot.

- [Palo Alto hálózatok MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Útmutatásért lásd: [IOCs küldése a Microsoft Graph Security API-nak a MineMeld használatával](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect platform](https://threatconnect.com/solution/)

    További információ: [ThreatConnect-integrációk](https://threatconnect.com/integrations/) és a Microsoft Graph Security API használata az oldalon.

- [EclecticIQ platform](https://www.eclecticiq.com/solutions)

- [ThreatQ fenyegetések intelligencia platform](https://www.threatq.com/)

    További információ és útmutatás: a [Microsoft Sentinel Connector a ThreatQ-integrációhoz](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview).

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Az Azure Sentinel összekapcsolhatók a veszélyforrások elleni intelligencia platformmal

### <a name="prerequisites"></a>Előfeltételek  

- A globális rendszergazda vagy a biztonsági rendszergazda Azure AD szerepköre, hogy engedélyeket adjon a tipp termékének vagy egyéni alkalmazásának, amely közvetlen integrációt használ a Microsoft Graph Security tiIndicators API-val.

- Olvasási és írási engedélyek az Azure Sentinel-munkaterülethez a veszélyforrások mutatóinak tárolásához.

### <a name="instructions"></a>Utasítások

1. Azure Active Directory [alkalmazás regisztrálása](/graph/auth-v2-service#1-register-your-app) az alkalmazás-azonosító, az alkalmazás titkos kódjának és a Azure Active Directory bérlői azonosító beszerzéséhez. Ezekre az értékekre akkor van szükség, amikor az integrált tipp termékét vagy alkalmazását a Microsoft Graph Security tiIndicators API-val való közvetlen integrációt használó alkalmazáshoz konfigurálja.

2. [API-engedélyek konfigurálása](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) a regisztrált alkalmazáshoz: adja hozzá a Microsoft Graph alkalmazás engedély **ThreatIndicators. ReadWrite. OwnedBy** a regisztrált alkalmazáshoz.

3. Kérje meg a Azure Active Directory bérlői rendszergazdát, hogy rendszergazdai jóváhagyást adjon a szervezete regisztrált alkalmazásának. A Azure Portal: **Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  **\<_app name_>**  >  **View API-engedélyekhez**  >  **rendszergazdai jóváhagyást \<_tenant name_> kell adni**.

4. Konfigurálja a tipp termékét vagy alkalmazását, amely közvetlen integrációt használ Microsoft Graph Security tiIndicators API-val a mutatók Azure Sentinelbe való küldéséhez az alábbiak megadásával:
    
    a. A regisztrált alkalmazás AZONOSÍTÓjának, titkos kódjának és bérlői AZONOSÍTÓjának értékei.
    
    b. A megcélzott termék esetében az Azure Sentinelt kell megadnia.
    
    c. A művelethez válassza a riasztást.

5. A Azure Portal navigáljon az **Azure Sentinel**-  >  **adatösszekötők** elemre, majd válassza ki a **Threat Intelligence Platforms (előzetes verzió)** összekötőt.

6. Válassza az **összekötő megnyitása lapot**, majd a **Csatlakozás** lehetőséget.

7. Az Azure sentinelbe importált veszélyforrások mutatóinak megtekintéséhez navigáljon az **Azure Sentinel-logs**  >  **SecurityInsights**, majd bontsa ki a **ThreatIntelligenceIndicator** csomópontot.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Az Azure Sentinel és a TAXII-kiszolgálók összekötése

### <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyek az Azure Sentinel-munkaterülethez a veszélyforrások mutatóinak tárolásához.

- TAXII 2,0-kiszolgáló URI-ja és a gyűjtemény azonosítója.

### <a name="instructions"></a>Utasítások

1. A Azure Portal navigáljon az **Azure Sentinel**  >  -**adatösszekötők** elemre, majd válassza ki a **Threat Intelligence-TAXII (előzetes verzió)** összekötőt.

2. Válassza az **összekötő megnyitása lapot**.

3. A szövegmezőben megadhatja a kötelező és a nem kötelező információkat.

4. Válassza a **Hozzáadás** lehetőséget a TAXII 2,0-kiszolgálóhoz való kapcsolódás engedélyezéséhez.

5. Ha további TAXII 2,0-kiszolgálóval rendelkezik: ismételje meg a 3. és a 4. lépést.

6. Az Azure sentinelbe importált veszélyforrások mutatóinak megtekintéséhez navigáljon az **Azure Sentinel-logs**  >  **SecurityInsights**, majd bontsa ki a **ThreatIntelligenceIndicator** csomópontot.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a veszélyforrások elleni intelligencia szolgáltatója az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket.

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).
---
title: Használja a kapcsolat diagnosztizálása funkciót a SSIS Integration Runtime-ban
description: A kapcsolódási problémák elhárítása a SSIS integrációs moduljában a kapcsolat diagnosztizálása funkció használatával.
ms.service: data-factory
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 06/07/2020
ms.openlocfilehash: 1fb9a0c77a7cdc286c7c206d6eb33e43917ee719
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361859"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Használja a kapcsolat diagnosztizálása funkciót a SSIS Integration Runtime-ban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A SSIS Integration Runtime-ban a SQL Server Integration Services (SSIS) csomagok végrehajtása közben csatlakozási problémák jelentkezhetnek. Ezek a problémák különösen akkor fordulnak elő, ha a SSIS Integration Runtime csatlakozik az Azure-beli virtuális hálózathoz.

Kapcsolódási problémák elhárítása a kapcsolat *diagnosztizálása* funkció használatával a kapcsolatok teszteléséhez. A funkció a Azure Data Factory portál monitoring SSIS Integration Runtime lapján található.

 ![Figyelő lap – kapcsolat diagnosztizálása](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Figyelő lap – tesztelési kapcsolatok](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

A következő fejezetekben megismerheti a kapcsolatok tesztelésekor előforduló leggyakoribb hibákat. A szakaszok az alábbiakat ismertetik:

- Hibakód
- Hibaüzenet
- A hiba lehetséges oka (i)
- Ajánlott megoldás (ok)

## <a name="error-code-invalidinput"></a>Hibakód: InvalidInput

- **Hibaüzenet**: "Ellenőrizze, hogy helyes-e a bevitele."
- **Lehetséges ok**: helytelen a bevitele.
- **Javaslat**: vizsgálja meg a bemenetet.

## <a name="error-code-firewallornetworkissue"></a>Hibakód: FirewallOrNetworkIssue

- **Hibaüzenet**: "Ellenőrizze, hogy a port nyitva van-e a tűzfalon/kiszolgálón/NSG, és hogy a hálózat stabil-e."
- **Lehetséges okok:**
  - A kiszolgáló nem nyitja meg a portot.
  - A hálózati biztonsági csoport elutasította a kimenő forgalmat a porton.
  - A NVA/Azure Firewall/helyszíni tűzfal nem nyitja meg a portot.
- **Javaslatok**
  - Nyissa meg a portot a kiszolgálón.
  - Frissítse a hálózati biztonsági csoportot, hogy engedélyezze a kimenő forgalmat a porton.
  - Nyissa meg a portot a NVA/Azure Firewall/helyszíni tűzfalon.

## <a name="error-code-misconfigureddnssettings"></a>Hibakód: MisconfiguredDnsSettings

- **Hibaüzenet**: "Ha saját DNS-kiszolgálót használ a Azure-SSIS IRhoz csatlakoztatott VNet, ellenőrizze, hogy képes-e az állomásnév feloldására."
- **Lehetséges okok:**
  -  Probléma van az egyéni DNS-sel.
  -  Nem teljes tartománynevet (FQDN) használ a saját gazdagép neveként.
- **Javaslatok**
  -  Javítsa ki az egyéni DNS-problémát annak biztosítására, hogy az képes legyen az állomásnév feloldására.
  -  Használja a teljes tartománynevet. A Azure-SSIS IR nem fűzi hozzá automatikusan a saját DNS-utótagját. Használja például a **<your_private_server>. contoso.com** **<your_private_server>** helyett.

## <a name="error-code-servernotallowremoteconnection"></a>Hibakód: ServerNotAllowRemoteConnection

- **Hibaüzenet**: "Ellenőrizze, hogy a kiszolgáló engedélyezi-e a távoli TCP-kapcsolatokat ezen a porton keresztül."
- **Lehetséges okok:**
  -  A kiszolgáló tűzfala nem engedélyezi a távoli TCP-kapcsolatokat.
  -  A kiszolgáló nem online állapotú.
- **Javaslatok**
  -  Távoli TCP-kapcsolatok engedélyezése a kiszolgálói tűzfalon.
  -  Indítsa el a kiszolgálót.
   
## <a name="error-code-misconfigurednsgsettings"></a>Hibakód: MisconfiguredNsgSettings

- **Hibaüzenet**: "Ellenőrizze, hogy a VNet NSG engedélyezi-e a kimenő forgalmat ezen a porton keresztül. Ha Azure ExpressRoute és UDR használ, ellenőrizze, hogy a port nyitva van-e a tűzfalon/kiszolgálón. "
- **Lehetséges okok:**
  -  A hálózati biztonsági csoport elutasította a kimenő forgalmat a porton.
  -  A NVA/Azure Firewall/helyszíni tűzfal nem nyitja meg a portot.
- **Ajánlás**
  -  Frissítse a hálózati biztonsági csoportot, hogy engedélyezze a kimenő forgalmat a porton.
  -  Nyissa meg a portot a NVA/Azure Firewall/helyszíni tűzfalon.

## <a name="error-code-genericissues"></a>Hibakód: GenericIssues

- **Hibaüzenet**: "a kapcsolódási teszt általános problémák miatt nem sikerült."
- **Lehetséges ok**: a tesztelési kapcsolatban általános átmeneti probléma merült fel.
- **Javaslat**: később próbálkozzon újra a tesztelési kapcsolatban. Ha az Újrapróbálkozás nem segít, lépjen kapcsolatba a Azure Data Factory támogatási csapatával.

## <a name="error-code-pspingexecutiontimeout"></a>Hibakód: PSPingExecutionTimeout

- **Hibaüzenet**: "a kapcsolat időtúllépésének tesztelése, próbálkozzon újra később."
- **Lehetséges ok**: a kapcsolat tesztelése időtúllépés miatt megszakadt.
- **Javaslat**: később próbálkozzon újra a tesztelési kapcsolatban. Ha az Újrapróbálkozás nem segít, lépjen kapcsolatba a Azure Data Factory támogatási csapatával.

## <a name="error-code-networkinstable"></a>Hibakód: NetworkInstable

- **Hibaüzenet**: "a hálózat instabilitása miatt szabálytalanul sikerült tesztelni a kapcsolódást."
- **Lehetséges ok**: átmeneti hálózati probléma.
- **Javaslat**: Győződjön meg arról, hogy a kiszolgáló vagy a tűzfal hálózata stabil-e.

## <a name="next-steps"></a>Következő lépések

- [SSIS-projekt üzembe helyezése az Azure-ban a SSMS használatával](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [SSIS-csomagok futtatása az Azure-ban a SSMS](/sql/integration-services/ssis-quickstart-run-ssms)
- [SSIS-csomagok ütemezett beosztása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)
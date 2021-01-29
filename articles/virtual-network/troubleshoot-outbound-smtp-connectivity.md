---
title: A kimenő SMTP-kapcsolatok hibáinak megoldása az Azure-ban | Microsoft Docs
description: Ismerje meg a javasolt módszert az e-mailek küldéséhez és az Azure-beli kimenő SMTP-kapcsolattal kapcsolatos problémák elhárításához.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: 518b8e33ac9adfdd1aa121e0cb8d1558545980e7
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054854"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>A kimenő SMTP-kapcsolati problémák elhárítása az Azure-ban

A (z) 2017. november 15-én kezdődően a virtuális gépről (például outlook.com és gmail.com) közvetlenül küldött kimenő e-mail-üzenetek csak bizonyos előfizetési típusok számára érhetők el az Azure-ban. A 25-ös TCP-portot használó kimenő SMTP-kapcsolatok blokkolva lettek. (A 25-ös portot főleg a nem hitelesített e-mailek kézbesítésére használják.)

Ez a viselkedési változás csak az 2017. november 15. után létrehozott előfizetésekre és központi telepítésekre vonatkozik.

## <a name="recommended-method-of-sending-email"></a>E-mailek küldésének ajánlott módja

Javasoljuk, hogy hitelesített SMTP Relay-szolgáltatásokat használjon az Azure-beli virtuális gépekről vagy Azure App Serviceról küldött e-mailek küldéséhez. (Ezek a továbbító szolgáltatások jellemzően a 587-es TCP-porton keresztül csatlakoznak, de más portokat is támogatnak.) Ezek a szolgáltatások az IP-címek és a tartományok hírnevének fenntartására szolgálnak, hogy a harmadik féltől származó e-mail-szolgáltatók elutasítsák az üzeneteket. A [SendGrid](https://sendgrid.com/partners/azure/) egy ilyen SMTP-továbbító szolgáltatás, de mások is vannak. Előfordulhat, hogy egy, a helyszínen futó biztonságos SMTP-továbbító szolgáltatást is tartalmaz, amelyet használhat.

Az e-mailes kézbesítési szolgáltatások használata nem korlátozódik az Azure-ban, az előfizetés típusától függetlenül.

## <a name="enterprise-agreement"></a>Nagyvállalati Szerződés

Nagyvállalati Szerződés Azure-felhasználók esetében a technikai képesség nem változik, hogy hitelesített továbbító használata nélkül küldjön e-mailt. Az új és a meglévő Nagyvállalati Szerződés felhasználók is kipróbálhatják a kimenő e-mailek küldését az Azure-beli virtuális gépekről az Azure-platform korlátozásai nélkül. Nincs garancia arra, hogy az e-mail-szolgáltatók elfogadják a bejövő e-maileket az adott felhasználótól. Az Azure platform azonban nem blokkolja a Nagyvállalati Szerződés-előfizetéseken belüli virtuális gépek kézbesítési kísérleteit. Az üzenetek kézbesítésével vagy a levélszemét szűrésével kapcsolatos olyan problémák megoldásához, amelyeket az adott szolgáltatók érintenek, közvetlenül az e-mail-szolgáltatókkal kell dolgoznia.

## <a name="pay-as-you-go"></a>Utólagos elszámolás

Ha az utólagos elszámolású előfizetéshez a 2017. november 15. előtt regisztrált, akkor a technikai képesség nem változik a kimenő e-mailek kézbesítésének kipróbálása során. Az Azure-beli virtuális gépekről a kimenő e-mailek kézbesítését továbbra is kipróbálhatja közvetlenül a külső e-mail-szolgáltatók számára, az Azure platform korlátozásai nélkül. Nem garantáljuk, hogy az e-mail-szolgáltatók elfogadják a bejövő e-maileket az adott felhasználótól. A felhasználóknak közvetlenül kell működniük az e-mail-szolgáltatókkal, hogy javítsák az üzenetek kézbesítésével vagy a levélszemét szűrésével kapcsolatos, bizonyos szolgáltatókra vonatkozó problémákat.

A 2017. november 15. után létrehozott utólagos elszámolású előfizetések esetében technikai korlátozások lesznek érvényesek, amelyek letiltják a közvetlenül az előfizetésen belüli virtuális gépekről küldött e-maileket. Ha közvetlenül az Azure-beli virtuális gépekről szeretne e-mailt küldeni a külső e-mail-szolgáltatók számára (hitelesített SMTP-továbbítás használata nélkül), és a fizetési előzményekkel rendelkező fiókkal rendelkezik, kérheti, hogy a rendszer eltávolítsa a korlátozást. Ezt a Azure Portal Azure Virtual Network erőforrásának **diagnosztika és megoldás** paneljének **kapcsolat** szakaszában teheti meg. Ha elfogadja a kérelmét, az előfizetése engedélyezve lesz, vagy a következő lépésekre vonatkozó utasításokat fogja kapni. 

Az utólagos elszámolású előfizetés után a rendszer leállítja és újraindítja a virtuális gépeket a Azure Portal az előfizetésben lévő összes virtuális gépet. A kivétel csak a kért előfizetésre vonatkozik, és csak az internethez közvetlenül átirányított VM-forgalomra érvényes.

> [!NOTE]
> A Microsoft fenntartja a jogot a kivételek visszavonására, ha megállapítják, hogy a szolgáltatási feltételek megsértése történt.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open licencprogram, oktatás, Azure diákoknak, Visual Studio és ingyenes próbaverzió

Ha a következő előfizetési típusok egyikét hozta létre a 2017. november 15. után, akkor technikai korlátozásokkal rendelkezik, amelyek letiltják az előfizetésben található virtuális gépekről küldött e-maileket közvetlenül az e-mail-szolgáltatók számára:
- MSDN
- Azure Pass
- Azure in Open
- Education
- Azure for Students
- Ingyenes próba
- Bármely Visual Studio-előfizetés  

A korlátozások a visszaélések megelőzése érdekében vannak érvényben. A korlátozások eltávolítására irányuló kérelmek nem lesznek megadva.

Ha ezeket az előfizetési típusokat használja, javasoljuk, hogy az SMTP Relay-szolgáltatásokat a jelen cikk korábbi részében ismertetett módon használja, vagy módosítsa az előfizetés típusát.

## <a name="cloud-solution-provider"></a>Felhőszolgáltató

Ha felhőalapú megoldás-szolgáltatón keresztül használja az Azure-erőforrásokat, akkor a Azure Portal egy virtuális hálózati erőforrásának **diagnosztizálás és megoldás** ablaktáblájának **kapcsolat** szakaszában távolítsa el a korlátozást. Ha elfogadja a kérelmét, az előfizetése engedélyezve lesz, vagy a következő lépésekre vonatkozó utasításokat fogja kapni.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus vagy Azure Sponsorship

A 2017. november 15. után létrehozott következő típusok előfizetései technikai korlátozásokat tartalmaznak, amelyek letiltják a közvetlenül az előfizetésen belüli virtuális gépekről küldött e-maileket:

- Microsoft Partner Network (MPN)
- BizSpark plusz
- Azure Sponsorship

Ha közvetlenül az Azure-beli virtuális gépekről szeretne e-mailt küldeni a külső e-mail-szolgáltatók számára (hitelesített SMTP-továbbítás használata nélkül), a következő probléma típusának használatával egy támogatási eset megnyitásával teheti meg a kérelmet: **technikai**  >  **Virtual Network** a  >  **kapcsolat**  >  **nem tud e-mailt küldeni (SMTP/port 25)**. Győződjön meg arról, hogy a központi telepítésnek miért kell közvetlenül a levelezési szolgáltatónak küldenie a leveleket a hitelesített továbbító használata helyett. A kérelmeket a Microsoft saját belátása szerint fogja felülvizsgálni és jóváhagyni. A rendszer csak a további csalás elleni ellenőrzések befejezését követően kap kérelmeket. 

Az előfizetés kivétele és a virtuális gépek leállítása és újraindítása után a Azure Portal az előfizetésben lévő összes virtuális gép mentesül. A kivétel csak a kért előfizetésre vonatkozik, és csak az internethez közvetlenül átirányított VM-forgalomra érvényes.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és kérje meg a probléma gyors megoldását. A probléma típusa: a **technikai**  >  **Virtual Network**  >  **kapcsolat**  >  **nem tud e-mailt küldeni (SMTP/port 25)**.

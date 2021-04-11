---
title: Partnerkapcsolat a Azure DDoS Protection standard csomaggal
description: A Azure DDoS Protection standard által engedélyezett partneri lehetőségek ismertetése.
ms.service: ddos-protection
documentationcenter: na
author: aletheatoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: 625f77a6d9dbeaf612df884434ab39b943587791
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103198"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Partnerkapcsolat a Azure DDoS Protection standard csomaggal
Ez a cikk a Azure DDoS Protection szabvány által engedélyezett partneri lehetőségeket ismerteti. Ez a cikk úgy lett kialakítva, hogy segítse a termék-kezelőket és az üzleti fejlesztési szerepköröket a befektetési útvonalak megismerésében, és betekintést nyújt a partneri értékek kiosztására.

## <a name="background"></a>Háttér
Az elosztott szolgáltatásmegtagadási (DDoS) támadások egyike a legfontosabb rendelkezésre állási és biztonsági szempontnak, amelyet az ügyfelek az alkalmazásaikat a felhőbe mozgatnak. A zsarolás és a hacktivista a DDoS-támadások mögötti gyakori motivációja, mivel ezek az esetek viszonylag könnyen és egyszerűen elindíthatók.

A Azure DDoS Protection a legkifinomultabb DDoS-fenyegetések elleni védelmet nyújt, és az Azure hálózatkezelésének globális skáláját hasznosítja. A szolgáltatás a virtuális hálózatokban üzembe helyezett alkalmazásokhoz és erőforrásokhoz nyújt továbbfejlesztett DDoS-elhárítási képességeket.

A technológiai partnerek natív módon védik az ügyfelek erőforrásait Azure DDoS Protection szabványokkal a DDoS-támadások miatti rendelkezésre állási és megbízhatósági probléma megoldásához.

## <a name="introduction-to-azure-ddos-protection-standard"></a>A Azure DDoS Protection standard bemutatása
A Azure DDoS Protection standard a 3. réteg és a 4. réteg DDoS-támadások elleni fejlett DDoS-elhárítási képességeket biztosít. A DDoS Protection standard szolgáltatás legfontosabb funkciói a következők:

### <a name="adaptive-real-time-tuning"></a>Adaptív valós idejű hangolás
Az Azure DDoS Protection standard minden védett alkalmazás esetében automatikusan behangolja a DDoS-mérséklési házirend küszöbértékeit az alkalmazás forgalmi profiljának mintái alapján. A szolgáltatás két bepillantással hajtja végre ezt a testreszabást:

- A 3. és a 4. rétegbeli ügyfélen belüli (/IP-) forgalom automatikus megtanulása.
- A téves pozitív érték minimalizálása, figyelembe véve, hogy az Azure mérete lehetővé teszi, hogy jelentős mennyiségű forgalmat vegyen fel.

![Adaptív valós idejű hangolás](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Támadási elemzések, telemetria, monitorozás és riasztások
Azure DDoS Protection a DDoS-támadások azonosítását és enyhítését felhasználói beavatkozás nélkül.

- Ha a védett erőforrás a Azure Security Center alatt lévő előfizetésben található, DDoS Protection a standard automatikusan riasztást küld a Security Center, amikor DDoS-támadás észlelhető, és a védett alkalmazással szembeni enyhítése történik.
- Azt is megteheti, hogy értesítést kap, ha a védett nyilvános IP-címek esetében aktív megoldás van érvényben, a metrikát a DDoS-támadás alatt is [konfigurálhatja](alerts.md) .
- Emellett dönthet úgy is, hogy riasztásokat hoz létre a többi DDoS mérőszámhoz, és [konfigurálja a támadási telemetria](telemetry.md) , hogy megértse a támadás mértékét, a forgalom eldobását, a támadási vektorokat, a legfontosabb közreműködőket és egyéb részleteket.

![DDoS-metrikák](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS gyors válasz (DRR)
DDoS Protection a standard szintű ügyfelek hozzáférhetnek a [gyors reagálású csapathoz](ddos-rapid-response.md) egy aktív támadás során. A DRR a támadás során és a támadás utáni elemzés során is segíthet.

### <a name="sla-guarantee-and-cost-protection"></a>SLA-garancia és Cost Protection
DDoS Protection standard szintű szolgáltatást a 99,99%-os SLA garantálja, a Cost Protection pedig erőforrás-krediteket biztosít a dokumentált támadás során felskálázáshoz. További információ: [Azure DDoS Protection SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)-ja.

## <a name="featured-partner-scenarios"></a>Kiemelt partneri forgatókönyvek
Az alábbi főbb előnyök a Azure DDoS Protection standard integrálásával származhatnak:

- A partnerek által kínált szolgáltatások (terheléselosztó, webalkalmazási tűzfal, tűzfal stb.) automatikusan védettek (fehér címkével) a háttérbeli Azure DDoS Protection szabvány szerint.
- A partnerek hozzáférhetnek Azure DDoS Protection standard szintű támadási elemzésekhez és telemetria, amelyeket a saját termékeik integrálására használhatnak, és egységes felhasználói élményt nyújtanak.  
- A-partnerek a DDoS-támogatással kapcsolatos problémák esetén még az Azure gyors reagálás hiányában is hozzáférhetnek a DDoS gyors reagáláshoz.
- A partnerek által védett alkalmazásokat a DDoS SLA-garanciával és a DDoS-támadások esetén a költséghatékonysággal támogatja.

## <a name="technical-integration-overview"></a>Technikai integráció – áttekintés
Azure DDoS Protection a szabványos partneri lehetőségeket Azure Portal, API-k és CLI/PS használatával lehet elérhetővé tenni.

### <a name="integrate-with-ddos-protection-standard"></a>Integrálás a DDoS Protection Standardmal
A következő lépések szükségesek ahhoz, hogy a partnerek a Azure DDoS Protection standard szintű integrációt konfigurálja:
1. Hozzon létre egy DDoS Protection csomagot a kívánt (partneri) előfizetésben. Részletes útmutatásért lásd: [a DDoS standard Protection-csomag létrehozása](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Egy adott bérlőhöz csak 1 DDoS Protection tervet kell létrehozni. 
2. Helyezzen üzembe egy szolgáltatást nyilvános végponttal (partner) előfizetésekben, például terheléselosztó, tűzfalak és webalkalmazási tűzfal. 
3. Engedélyezze a Azure DDoS Protection standard szolgáltatást azon szolgáltatás virtuális hálózatán, amely nyilvános végpontokkal rendelkezik az első lépésben létrehozott DDoS Protection terv használatával. A stpe részletes ismertetését lásd: a [DDoS standard Protection-csomag engedélyezése](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Miután a Azure DDoS Protection standard engedélyezve van egy virtuális hálózaton, a virtuális hálózaton belüli összes nyilvános IP-cím automatikusan védett lesz. Ezeknek a nyilvános IP-címeknek a forrása lehet az Azure-ban (ügyfél-előfizetés) vagy az Azure-on kívül is. 
4. Ha szeretné, integrálja Azure DDoS Protection standard telemetria és a támadási elemzést az alkalmazásra vonatkozó, az ügyfél felé irányuló irányítópulton. További információ a telemetria használatáról: [DDoS Protection telemetria megtekintése és konfigurálása](telemetry.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Előkészítési útmutatók és műszaki dokumentáció

- [Azure DDoS Protection termék lapja](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection dokumentáció](ddos-protection-overview.md)
- [Azure DDoS Protection API-hivatkozás](/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure Virtual Network API-dokumentáció](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Segítség kérése

- Ha kérdése van az alkalmazással, a szolgáltatással vagy a termék-integrációval kapcsolatban Azure DDoS Protection standard rendszerrel, akkor az [Azure biztonsági közössége](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)is elérhető.
- [Stack overflow](https://stackoverflow.com/tags/azure-ddos/)-beszélgetések követése.

### <a name="get-to-market"></a>Piacra jutás

- A Microsofttal való partneri kapcsolat elsődleges programja a [Microsoft Partner Network](https://partner.microsoft.com/). – Microsoft Graph biztonsági integrációk az [MPN független szoftvergyártó (ISV)](https://partner.microsoft.com/saas-solution-guide) nyomon követéséhez tartoznak.
- A Microsoft [intelligens biztonsági társítás](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) a program kifejezetten a Microsoft biztonsági partnerei számára a biztonsági termékek gazdagítása és a Microsoft biztonsági termékeibe való integrációjuk javítása érdekében.

## <a name="next-steps"></a>Következő lépések
Meglévő partner-integrációk megtekintése:

- [Barracuda WAF-szolgáltatás](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF a Radware-ből](https://www.radware.com/resources/microsoft-azure/)

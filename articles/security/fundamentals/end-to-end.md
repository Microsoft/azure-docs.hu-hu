---
title: Végpontok közötti biztonság az Azure-ban | Microsoft Docs
description: A cikk az Azure-szolgáltatások olyan térképét nyújtja, amely segít a Felhőbeli erőforrások biztonságában és védelmében, valamint a fenyegetések észlelésében és kivizsgálásában.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310342"
---
# <a name="end-to-end-security-in-azure"></a>Végpontok közötti biztonság az Azure-ban
Az Azure az alkalmazások és szolgáltatások használatának egyik legjobb oka az, hogy kiaknázza a biztonsági eszközök és képességek széles körét. Ezek az eszközök és képességek segítik a biztonságos megoldások létrehozását a biztonságos Azure platformon. Microsoft Azure biztosítja az ügyféladatok titkosságát, integritását és rendelkezésre állását, ugyanakkor lehetővé teszi az átlátható elszámoltathatóságot is.

Az alábbi ábra és dokumentáció bemutatja az Azure-beli biztonsági szolgáltatásokat. Ezek a biztonsági szolgáltatások segítenek megfelelni az üzleti igényeknek, és védik a felhasználókat, eszközöket, erőforrásokat, az alkalmazásokat és a felhőben lévő alkalmazásokat.

## <a name="microsoft-security-services-map"></a>Microsoft biztonsági szolgáltatások térképe

A biztonsági szolgáltatások térképe az általuk védett erőforrások (oszlop) alapján szervez szolgáltatásokat. A diagram a szolgáltatásokat a következő kategóriákba (sorokba) is csoportosítja:

- Biztonság és védelem – olyan szolgáltatások, amelyek lehetővé teszik az identitások, a gazdagépek, a hálózatok és az adatok többrétegű, részletes stratégiájának megvalósítását. A biztonsági szolgáltatások és képességek ezen gyűjteménye lehetővé teszi az Azure-környezetekben rejlő biztonsági helyzetek megismerését és javítását.
- Fenyegetések észlelése – a gyanús tevékenységeket azonosító és a fenyegetés enyhítését megkönnyítő szolgáltatások.
- Vizsgálat és reagálás – olyan szolgáltatások, amelyek lekérik a naplózási adataikat, így felmérhető egy gyanús tevékenység, és reagálhat rájuk.

A diagram magában foglalja az Azure Security benchmark programot, amely az Azure-ban használt szolgáltatások biztonságossá tételéhez használható, nagy hatású biztonsági javaslatok gyűjteménye.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Az Azure-ban elérhető végpontok közötti biztonsági szolgáltatásokat bemutató ábra." border="false":::

## <a name="security-controls-and-baselines"></a>Biztonsági vezérlők és alaptervek
Az [Azure Security benchmark](../benchmarks/introduction.md) program olyan nagy teljesítményű biztonsági javaslatokat tartalmaz, amelyek segítségével biztonságossá teheti az Azure-ban használt szolgáltatásokat:

- Biztonsági vezérlők – ezek a javaslatok általánosan érvényesek az Azure-bérlőre és az Azure-szolgáltatásokra. Mindegyik javaslat azonosítja azon érdekelt felek listáját, akik általában részt vesznek a teljesítményteszt megtervezésében, jóváhagyásában vagy megvalósításában.
- Szolgáltatás alapkonfigurációi – ezek a vezérlők az egyes Azure-szolgáltatásokra vonatkoznak, hogy javaslatokat szolgáltassanak a szolgáltatás biztonsági beállításairól.

## <a name="secure-and-protect"></a>Biztonság és védelem

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="A Felhőbeli erőforrások biztonságossá tételét és védelmét segítő Azure-szolgáltatásokat bemutató ábra." border="false":::

| Szolgáltatás | Leírás |
|------|--------|
| [Azure Security Center](../../security-center/security-center-introduction.md)| Egy egységes infrastruktúra-biztonsági felügyeleti rendszer, amely erősíti az adatközpontok biztonsági állapotát, és komplex veszélyforrások elleni védelmet biztosít a felhőben lévő hibrid számítási feladatokhoz – akár az Azure-ban, akár nem –, akár a helyszínen is. |
| **Identitás- &nbsp; & &nbsp; hozzáférés &nbsp; kezelése** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| A Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása.  |
|  | A [feltételes hozzáférés](../../active-directory/conditional-access/overview.md) az az eszköz, amelyet az Azure ad az identitási jelek összekapcsolására, a döntések meghozatalára és a szervezeti házirendek betartatására használ. |
|  | A [tartományi szolgáltatások](../../active-directory-domain-services/overview.md) az Azure ad által a felügyelt tartományi szolgáltatások, például a tartományhoz való csatlakozás, a csoportházirend, a Lightweight Directory Access Protocol (LDAP) és a KERBEROS/NTLM hitelesítés biztosítására használt eszköz. |
|  | A [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) olyan szolgáltatás az Azure ad-ben, amely lehetővé teszi a szervezet fontos erőforrásaihoz való hozzáférés kezelését, vezérlését és figyelését. |
|  | A [többtényezős hitelesítés](../../active-directory/authentication/concept-mfa-howitworks.md) az Azure ad által használt eszköz, amely a hitelesítés második formáját igényli a hozzáférés biztosításához az adatkezeléshez és az alkalmazásokhoz. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Olyan eszköz, amely lehetővé teszi, hogy a szervezetek automatizálják az identitás-alapú kockázatok észlelését és elhárítását, a portálon tárolt adatokkal kapcsolatos kockázatokat, valamint a kockázatkezelési adatok harmadik féltől származó eszközökre való exportálását további elemzés céljából. |
| **Infrastruktúra- &nbsp; & &nbsp; hálózat** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Egy virtuális hálózati átjáró, amely titkosított forgalmat küld egy Azure-beli virtuális hálózat és egy helyszíni hely között a nyilvános interneten keresztül, valamint titkosított adatforgalmat küld az Azure virtuális hálózatok között a Microsoft hálózatán keresztül. |
| [Azure DDoS Protection Standard](../../ddos-protection/ddos-protection-overview.md) | A DDoS-támadások elleni védelem érdekében fejlett DDoS-kockázatcsökkentő funkciókat biztosít. A rendszer automatikusan hangolja az adott Azure-erőforrások védelmére egy virtuális hálózaton. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Globális, méretezhető belépési pont, amely a Microsoft globális peremhálózati hálózatát használja gyors, biztonságos és széles körben méretezhető webalkalmazások létrehozásához. |
| [Azure Firewall](../../firewall/overview.md) | Felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely megvédi az Azure Virtual Network-erőforrásait. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Biztonságos titkos kulcsok tárolása tokenekhez, jelszavakhoz, tanúsítványokhoz, API-kulcsokhoz és egyéb titkokhoz. Key Vault is használható az adattitkosításhoz használt titkosítási kulcsok létrehozására és szabályozására. |
| [Key Vault felügyelt HSM (előzetes verzió)](../../key-vault/managed-hsm/overview.md) | Teljes körűen felügyelt, magas rendelkezésre állású, egybérlős, szabványoknak megfelelő felhőalapú szolgáltatás, amely lehetővé teszi a felhőalapú alkalmazások titkosítási kulcsainak védelmét az FIPS 140-2 3. szintű hitelesített HSM használatával. |
| [Azure Private Link](../../private-link/private-link-overview.md) | Lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat privát végpontján keresztül. |
| [Azure Application Gateway](../../application-gateway/overview.md) | Egy speciális webes forgalmi terheléselosztó, amely lehetővé teszi a webes alkalmazások forgalmának kezelését. Application Gateway a HTTP-kérések további attribútumain alapuló útválasztási döntéseket hozhat létre, például az URI elérési út vagy a gazdagép fejléceit. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | Teljes körűen felügyelt nagyvállalati üzenetkezelés az üzenetsor-kezelés és a közzétételi – előfizetés témakörökben. Service Bus az alkalmazások és szolgáltatások egymástól való elválasztására szolgál. |
| [Webalkalmazási tűzfal](../../web-application-firewall/overview.md) | A webalkalmazások központosított védelmét nyújtja a gyakori biztonsági rések és sebezhetőségek ellen. A WAF az Azure Application Gateway és az Azure bejárati ajtaján is üzembe helyezhető. |
| **Adat& alkalmazás** |  |
| [Azure Backup](../../backup/backup-overview.md) | Az egyszerű, biztonságos és költséghatékony megoldásokat kínál az adatok biztonsági mentésére és helyreállítására a Microsoft Azure felhőből. |
| [Azure-Storage Service Encryption](../../storage/common/storage-service-encryption.md) | A automatikusan titkosítja az adataikat a tárolás előtt, és a lekéréskor automatikusan visszafejti azokat. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | Felhőalapú megoldás, amely lehetővé teszi a szervezetek számára a dokumentumok és e-mailek felderítését, besorolását és védelemmel való ellátását a címkék alkalmazásával. |
| [API Management](../../api-management/api-management-key-concepts.md) | Konzisztens és modern API-átjárókat hozhat létre a meglévő háttér-szolgáltatásokhoz. |
| [Azure Confidential Computing](../../confidential-computing/overview.md) | Lehetővé teszi a bizalmas adatok elkülönítését a felhőben történő feldolgozás közben. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | A fejlesztési projektek a biztonsági és irányítási technológiák, az üzemeltetési gyakorlatok és a megfelelőségi szabályzatok több rétegét is kihasználhatják az Azure DevOps való tárolás során. |
| **Ügyfél-hozzáférés** |  |
| [Külső Azure AD-identitások](../../active-directory/external-identities/compare-with-b2c.md) | Az Azure AD külső identitásokkal lehetővé teheti, hogy a szervezeten kívüli személyek hozzáférjenek az alkalmazásokhoz és az erőforrásokhoz, miközben a felhasználó bármilyen identitással bejelentkezhetnek. |
|  | Az alkalmazásokat és erőforrásokat külső felhasználókkal is megoszthatja az [Azure ad B2B](../../active-directory/external-identities/what-is-b2b.md) együttműködés segítségével. |
|  | [Azure ad B2C](../../active-directory-b2c/overview.md) lehetővé teszi a napi több millió felhasználó és több milliárd hitelesítés támogatását, a fenyegetések figyelését és automatikus kezelését, például a szolgáltatásmegtagadási, a jelszó-vagy a találgatásos támadásokat. |

## <a name="detect-threats"></a>Fenyegetésészlelés

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="A fenyegetéseket észlelő Azure-szolgáltatásokat bemutató ábra." border="false":::

| Szolgáltatás | Leírás |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Fejlett, intelligens, Azure-és hibrid erőforrások és számítási feladatok. A Security Center található Azure Defender-irányítópulton megtekinthetik és vezérelhetik a környezet felhőalapú munkaterhelés-védelmi funkcióit. |
| [Azure Sentinel](../../sentinel/overview.md) | Méretezhető, Felhőbeli natív, biztonsági információkkal kapcsolatos esemény-felügyeleti (SIEM) és biztonsági előkészítési automatikus válasz (felszárnyalás) megoldás. A Sentinel intelligens biztonsági elemzési és fenyegetési intelligenciát biztosít a vállalaton belül, így egyetlen megoldást kínál a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a veszélyforrásokra adott válaszokra. |
| **Identitás- &nbsp; & &nbsp; hozzáférés &nbsp; kezelése** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | Az egységes pre-és post-szerződésszegés nagyvállalati védelmi csomag, amely natív módon koordinálja a végpontok, identitások, e-mailek és alkalmazások között, hogy integrált védelmet biztosítson a kifinomult támadásokkal szemben. |
|  | A [Microsoft Defender for Endpoint](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) egy vállalati végponti biztonsági platform, amelynek célja, hogy segítse a vállalati hálózatokat a fejlett fenyegetések megelőzésére, észlelésére, kivizsgálására és reagálására. |
|  | A [Microsoft Defender for Identity](https://docs.microsoft.com/defender-for-identity/what-is) egy felhőalapú biztonsági megoldás, amely lehetővé teszi a helyszíni Active Directory jelek azonosítását, észlelését és kivizsgálását, valamint a szervezete által irányított, rosszindulatú belső műveleteket. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Kétféle automatizált értesítő e-mailt küld a felhasználói kockázatok és kockázatok észlelésének kezeléséhez: a veszélyeztetett felhasználók észlelt e-maileket és heti kivonatoló e-maileket. |
| **Infrastruktúra & hálózat** |  |
| [Azure Defender IoT-hez](../../defender-for-iot/overview.md) | Egységes biztonsági megoldás a IoT/OT-eszközök, biztonsági rések és fenyegetések azonosításához. Lehetővé teszi a teljes IoT/OT-környezet védelmét, függetlenül attól, hogy védenie kell-e a meglévő IoT/OT-eszközöket, vagy a biztonságot új IoT-innovációk alapján kell felépíteni. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Eszközöket biztosít az Azure-beli virtuális hálózatban lévő erőforrások figyelésére, diagnosztizálására, megtekintésére, valamint a naplók engedélyezésére vagy letiltására. Network Watcher úgy van kialakítva, hogy figyelje és javítsa a IaaS-termékek hálózati állapotát, beleértve a virtuális gépeket, a virtuális hálózatokat, az Application Gateway-t és a terheléselosztó szolgáltatást. |
| [Naplózás Azure Policy](../../governance/policy/overview.md) | Segít a szervezeti szabványok betartatásában és a megfelelőségi követelmények kiértékelésében. A Azure Policy tevékenység-naplókat használ, amelyek automatikusan engedélyezve vannak az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célhelyek és más hasznos elemek belefoglalására. |
| **Adat& alkalmazás** |  |
| [Azure Defender tárolóregisztrációs adatbázisokhoz](../../security-center/defender-for-container-registries-introduction.md) | A biztonsági rések képolvasó segítségével beolvashatja a rendszerképeket a Azure Resource Manager-alapú Azure Container Registry-jegyzékekben, és mélyebb láthatóságot biztosíthat a lemezképek biztonsági rései számára. |
| [Azure Defender Kuberneteshez](../../security-center/defender-for-kubernetes-introduction.md) | A fürt szintű veszélyforrások elleni védelmet biztosít az AK által felügyelt szolgáltatások figyelésével az Azure Kubernetes Service (ak) által lekért naplókon keresztül. |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | Egy felhőalapú hozzáférés biztonsági közvetítője (CASB), amely több felhőkön működik. Részletes láthatóságot, az adatátvitel szabályozását, valamint olyan kifinomult elemzési lehetőségeket kínál, melyekkel azonosíthatja és elháríthatja a kiberfenyegetéseket az összes felhőalapú szolgáltatásában. |

## <a name="investigate-and-respond"></a>Vizsgálat és válaszintézkedések

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Az Azure-szolgáltatásokat bemutató ábra, amely segítséget nyújt a fenyegetések kivizsgálásában és megválaszolásában." border="false":::

| Szolgáltatás | Leírás |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Hatékony keresési és lekérdezési eszközök a szervezet adatforrásaiban a biztonsági fenyegetések elleni vadászathoz. |
| [Azure &nbsp; monitor &nbsp; &nbsp; -naplók és- &nbsp; metrikák](../../azure-monitor/overview.md) | Átfogó megoldást nyújt a Felhőbeli és a helyszíni környezetek telemetria gyűjtésére, elemzésére és üzemeltetésére. A Azure Monitor különböző forrásokból származó [adatokat gyűjt és összesít](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) egy közös adatplatformba, ahol elemzésre, vizualizációra és riasztásra is használható. |
| **Identitás- &nbsp; & &nbsp; hozzáférés &nbsp; kezelése** |  |
| [Azure &nbsp; ad &nbsp; &nbsp; -jelentések és- &nbsp; figyelés](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | Az [Azure ad-jelentések](../../active-directory/reports-monitoring/overview-reports.md) átfogó képet nyújtanak a környezettel kapcsolatos tevékenységekről. |
|  | Az [Azure ad-figyelés](../../active-directory/reports-monitoring/overview-monitoring.md) lehetővé teszi, hogy az Azure ad-tevékenység naplóit különböző végpontokra irányítsa.|
| [Azure AD PIM naplózási előzményei](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Megjeleníti az összes szerepkör-hozzárendelést és aktiválást az elmúlt 30 napban az összes Kiemelt szerepkör esetében. |
| **Adat& alkalmazás** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Eszközöket biztosít a felhőalapú környezetében zajló események mélyebb megismeréséhez. |

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [megosztott felelősségét a felhőben](shared-responsibility.md).

- Ismerje [meg az Azure-felhő elkülönítési lehetőségeit](isolation-choices.md) mind a kártékony, mind a nem rosszindulatú felhasználókkal szemben.
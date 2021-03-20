---
title: Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások – Microsoft Azure
description: Ez a cikk az adatbiztonságra és-titkosításra vonatkozó ajánlott eljárásokat ismerteti az Azure beépített képességeinek használatával.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: 414445f90c3be4c55166f6a0ecead25074d6ed74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98874113"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Az Azure adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárásai
Ez a cikk az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárásokat ismerteti.

Az ajánlott eljárások a vélemények konszenzusán alapulnak, és a jelenlegi Azure platform-képességekkel és-szolgáltatásokkal működnek. A vélemények és technológiák idővel változnak, és ez a cikk rendszeresen frissül, hogy tükrözze ezeket a módosításokat.

## <a name="protect-data"></a>Adatok védelme
A felhőben tárolt adatainak védelme érdekében figyelembe kell vennie azokat a lehetséges állapotokat, amelyekben az adatai megjelenhetnek, és hogy milyen vezérlők érhetők el az adott állapotban. Az Azure-Adatbiztonság és-titkosítás ajánlott eljárásai a következő adatállapotokhoz kapcsolódnak:

- Nyugalmi állapotban: Ez magában foglalja az összes olyan adattárolási objektumot, tárolót és típust, amelyek statikusan léteznek fizikai adathordozón, akár mágneses, akár optikai lemez esetén.
- Tranzitban: Ha az adatátvitelt az összetevők, a helyszínek vagy a programok között végzik, az átvitel zajlik. Ilyenek például a hálózaton keresztüli átvitel (a helyszíniről a felhőbe és fordítva, beleértve a hibrid kapcsolatokat, például a ExpressRoute), vagy egy bemeneti/kimeneti folyamat során.

## <a name="choose-a-key-management-solution"></a>Kulcskezelő megoldás kiválasztása

A kulcsok védelme elengedhetetlen a felhőben tárolt adatok védelméhez.

Az [Azure Key Vault](../../key-vault/general/overview.md) segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

A Key Vault használatával több biztonságos tárolót is létrehozhat, amelyek neve: Vaults. Ezeket a tárolókat a HSM támogatja. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. A Azure Key Vault képes kezelni Transport Layer Security (TLS) tanúsítványok kérését és megújítását. Hatékony megoldást kínál a tanúsítványok életciklusának kezelésére.

Azure Key Vault úgy lett kialakítva, hogy támogassa az alkalmazás kulcsait és titkos kulcsait. A Key Vault nem a felhasználói jelszavak tárolására szolgál.

Az alábbiakban a Key Vault használatára vonatkozó ajánlott biztonsági eljárásokat ismertetjük.

**Ajánlott eljárás**: a felhasználókhoz, csoportokhoz és alkalmazásokhoz való hozzáférés biztosítása egy adott hatókörben.   
**Részletek**: az Azure RBAC előre definiált szerepköreinek használata. Ha például hozzáférést szeretne biztosítani egy felhasználónak a kulcstartók felügyeletéhez, akkor az előre definiált szerepkört [Key Vault](../../role-based-access-control/built-in-roles.md) a felhasználóhoz egy adott hatókörben. A hatókör ebben az esetben egy előfizetés, egy erőforráscsoport vagy csak egy adott kulcstartó. Ha az előre definiált szerepkörök nem felelnek meg az igényeinek, [saját szerepköröket is meghatározhat](../../role-based-access-control/custom-roles.md).

**Ajánlott eljárás**: annak szabályozása, hogy mely felhasználók férhetnek hozzá.   
**Részletek**: a kulcstartóhoz való hozzáférés két különálló felületen, a felügyeleti síkon és az adatsíkon keresztül szabályozható. A felügyeleti sík és az adatsík hozzáférés-vezérlése egymástól függetlenül működik.

Az Azure RBAC segítségével szabályozhatja, hogy mely felhasználók férhetnek hozzá. Ha például egy kulcstartóban szeretné megadni az alkalmazáshoz a kulcsok használatát, csak a Key Vault hozzáférési házirendjének használatával kell megadnia az adatsíkok hozzáférési engedélyeit, és ehhez az alkalmazáshoz nincs szükség felügyeleti síkon való hozzáférésre. Ha azt szeretné, hogy a felhasználók beolvassák a tár tulajdonságait és címkéit, de nem rendelkeznek hozzáféréssel a kulcsokhoz, a titkokhoz vagy a tanúsítványokhoz, a felhasználó számára az Azure RBAC használatával is megadhatja az olvasási hozzáférést, és nincs szükség az adatsíkon való hozzáférésre.

**Ajánlott eljárás**: tanúsítványok tárolása a kulcstartóban. A tanúsítványok nagy értékűek. Rossz kezekben az alkalmazás biztonsága vagy az adatai biztonsága is sérülhet.   
**Részletek**: Azure Resource Manager a virtuális gépek üzembe helyezésekor biztonságosan telepíthet Azure Key Vault Azure-beli virtuális gépeken tárolt tanúsítványokat. Ha a kulcstartóhoz a megfelelő hozzáférési szabályzatokat állítja be, azt is szabályozhatja, hogy ki férhet hozzá a tanúsítványhoz. Egy másik előnye, hogy az összes tanúsítvány egy helyen kezelhető az Azure Key Vaultban. További információért lásd: [tanúsítványok központi telepítése virtuális gépekre az ügyfél által felügyelt Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault) .

**Ajánlott eljárás**: Ügyeljen arra, hogy helyreállítsa a kulcstartók vagy kulcstartó-objektumok törlését.   
**Részletek**: a kulcstartók vagy a Key Vault-objektumok törlése véletlen vagy rosszindulatú lehet. Engedélyezze a Key Vault visszaállítható törlés funkcióját és a végleges törlés elleni védelmet, elsősorban az olyan kulcsokhoz, amelyek az inaktív adatok védelmére vannak használva. Az ilyen kulcsok törlése az adatvesztéssel egyenértékű, ezért fontos, hogy a törölt kulcstartókat és kulcstartó-objektumokat szükség esetén vissza tudja állítani. Rendszeresen Key Vault helyreállítási műveleteket.

> [!NOTE]
> Ha a felhasználó közreműködői engedélyekkel (Azure RBAC) rendelkezik egy kulcstároló-felügyeleti síkon, akkor a Key Vault hozzáférési szabályzatának beállításával hozzáférést biztosíthatnak az adatsíkon. Azt javasoljuk, hogy szigorúan szabályozzák, hogy kik férjenek hozzá a kulcstartóhoz, így biztosítható, hogy csak a jogosult személyek férhessenek hozzá és kezelhetik a kulcstartókat, a kulcsokat, a titkokat és a tanúsítványokat.
>
>

## <a name="manage-with-secure-workstations"></a>Felügyelet biztonságos munkaállomásokkal

> [!NOTE]
> Az előfizetés rendszergazdájának vagy tulajdonosának biztonságos hozzáférési munkaállomást vagy emelt szintű hozzáférési munkaállomást kell használnia.
>
>

Mivel a támadások túlnyomó többsége a végfelhasználót célozza meg, a végpont a támadás egyik elsődleges pontja lesz. A végpontot veszélyeztető támadók a felhasználó hitelesítő adataival férhetnek hozzá a szervezet adataihoz. A legtöbb végponti támadás kihasználja azt a tényt, hogy a felhasználók a helyi munkaállomáson rendszergazdák.

**Ajánlott eljárás**: biztonságos felügyeleti munkaállomás használata a bizalmas fiókok, a feladatok és az adatok védelméhez.   
**Részletek**: az emelt [szintű hozzáférési munkaállomás](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) használatával csökkentheti a munkaállomásokon a támadási felületet. Ezek a biztonságos felügyeleti munkaállomások segíthetnek csökkenteni a támadások némelyikét, és biztosítják, hogy az adatai biztonságban legyenek.

**Ajánlott eljárás**: az Endpoint Protection biztosítása.   
**Részletek**: biztonsági szabályzatok betartatása minden olyan eszközön, amely az adatok felhasználására szolgál, függetlenül az adatok helyétől (Felhőbeli vagy helyszíni).

## <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

Az inaktív [adatok titkosítása](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) az adatvédelem, a megfelelőség és az adatok szuverenitásának kötelező lépése.

**Ajánlott eljárás**: a lemezes titkosítás alkalmazása az adatai védelmére.   
**Részletek**: [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md)használata. Lehetővé teszi a rendszergazdáknak a Windows-és Linux-IaaS VM-lemezek titkosítását. A lemezes titkosítás ötvözi az iparági szabványnak megfelelő Windows BitLocker szolgáltatást és a Linux dm-crypt funkciót, hogy mennyiségi titkosítást biztosítson az operációs rendszer és az adatlemezek számára.

Az Azure Storage és a Azure SQL Database alapértelmezés szerint titkosítja az inaktív adatok mennyiségét, és számos szolgáltatás lehetőséget biztosít a titkosításra. Az Azure Key Vault használatával szabályozhatja az adatokat elérő és titkosító kulcsokat. [További információért lásd az Azure erőforrás-szolgáltatók titkosítási modell támogatását](encryption-atrest.md#azure-resource-providers-encryption-model-support)ismertető témakört.

**Ajánlott eljárások**: titkosítás használata a jogosulatlan adathozzáféréssel kapcsolatos kockázatok enyhítéséhez.   
**Részletek**: Titkosítsa a meghajtókat, mielőtt bizalmas adatokat írna rájuk.

Azok a szervezetek, amelyek nem kényszerítik az adattitkosítást, jobban ki vannak téve az adatok bizalmasságával kapcsolatos problémáknak. Előfordulhat például, hogy a jogosulatlan vagy a szélhámos felhasználók ellopják a feltört fiókokban lévő adatvesztést, vagy jogosulatlan hozzáférést szereznek az adatkódoláshoz. A vállalatoknak azt is bizonyítaniuk kell, hogy szorgalmasak, és a megfelelő biztonsági ellenőrzésekkel javítják az adatbiztonságot az iparági előírásoknak való megfelelés érdekében.

## <a name="protect-data-in-transit"></a>Az átvitel alatt álló adatok védelme

Az átvitel alatt álló adatok védelmének fontos szerepet kell kapnia az adatvédelmi stratégiában. Mivel az adatok oda-vissza mozognak számos hely között, általában azt javasoljuk, hogy mindig használjon SSL/TLS protokollt a különböző helyek közötti adatcserékhez. Bizonyos körülmények között érdemes lehet a helyszíni és a felhőbeli infrastruktúra közötti teljes kommunikációs csatornát elkülöníteni egy VPN használatával.

A helyszíni infrastruktúra és az Azure között mozgó adatok esetében célszerű megfontolni a megfelelő védelmi lehetőségek, például a HTTPS vagy a VPN használatát. Amikor titkosított forgalmat küld egy Azure-beli virtuális hálózat és egy helyszíni hely között a nyilvános interneten keresztül, használja az [Azure VPN Gateway](../../vpn-gateway/index.yml)-t.

Az alábbiakban az Azure VPN Gateway, az SSL/TLS és a HTTPS használatára vonatkozó ajánlott eljárásokat ismertetjük.

**Ajánlott eljárás**: a helyszínen lévő több munkaállomás és egy Azure-beli virtuális hálózat közötti biztonságos hozzáférés.   
**Részletek**: [helyek közötti VPN](../../vpn-gateway/tutorial-site-to-site-portal.md)használata.

**Ajánlott eljárás**: biztonságos hozzáférés a helyszínen található egyes munkaállomásokról egy Azure-beli virtuális hálózatra.   
**Részletek**: [pont – hely típusú VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)használata.

**Ajánlott eljárás**: nagyobb adatkészletek mozgatása dedikált, nagy sebességű WAN-kapcsolaton keresztül.   
**Részletek**: a [ExpressRoute](../../expressroute/expressroute-introduction.md)használata. Ha az ExpressRoute használata mellett dönt, az adatokat az alkalmazás szintjén is titkosíthatja az SSL/TLS vagy más protokoll használatával, így fokozhatja a védelmet.

**Ajánlott eljárás**: az Azure Storage szolgáltatással való interakció a Azure Portalon keresztül.   
**Részletek**: minden tranzakció HTTPS-kapcsolaton keresztül történik. A [storage REST API](/rest/api/storageservices/) a HTTPS protokollon keresztül is használhatja az [Azure Storage](https://azure.microsoft.com/services/storage/)szolgáltatással való kommunikációhoz.

Azok a szervezetek, amelyek nem védik az adatátviteli adatvédelmet, érzékenyebbek az [ember általi támadásokra, a](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)) [lehallgatás](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))és a munkamenet-eltérítésre. Ezek a támadások a bizalmas adatokhoz való hozzáférés első lépései lehetnek.

## <a name="secure-email-documents-and-sensitive-data"></a>Biztonságos e-mailek, dokumentumok és bizalmas adatok

A vállalaton kívül megosztott e-maileket, dokumentumokat és bizalmas adatokat szeretne vezérelni és biztonságossá tenni. Az [Azure Information Protection](/azure/information-protection/) olyan felhőalapú megoldás, amely segítségével a szervezetek osztályozhatják, címkézhetik és védelemmel láthatják el a dokumentumaikat és az e-mailjeiket. Ezt automatikusan elvégezheti a rendszergazdák, akik szabályokat és feltételeket határoznak meg manuálisan a felhasználóktól, vagy egy olyan kombinációt, amelyben a felhasználók javaslatokat kapnak.

A besorolás minden esetben azonosítható, függetlenül attól, hogy hol tárolják az adatok tárolási helyét vagy közösen a megosztást. A címkékhez vizuális jelölések (például fejlécek, láblécek vagy vízjelek) is tartoznak. A rendszer tiszta szöveges formátumban adja hozzá a metaadatokat a fájlokhoz és e-mail-fejlécekhez. A tiszta szöveg biztosítja, hogy más szolgáltatások, például az adatvesztés megelőzésére szolgáló megoldások azonosítják a besorolást, és megtehetik a megfelelő lépéseket.

A védelmi technológia az Azure Rights Management (Azure RMS) szolgáltatást használja. Ez a technológia integrálva van más Microsoft Cloud Services és alkalmazások, például a Microsoft 365 és a Azure Active Directory szolgáltatással. A védelmi technológia titkosítási, identitáskezelési és engedélyezési házirendeket használ. A Azure RMS által alkalmazott védelem a dokumentumok és e-mailek, a helytől függetlenül marad – a szervezeten, hálózatokon, fájlkiszolgálókon és alkalmazásokon belül vagy kívül.

Ez az adatvédelmi megoldás megtartja az adatok felügyeletét, még akkor is, ha más személyekkel van megosztva. A Azure RMS is használhatja saját üzletági alkalmazásaival és adatvédelmi megoldásaival a szoftvergyártók számára, függetlenül attól, hogy ezek az alkalmazások és megoldások a helyszínen vagy a felhőben vannak.

A következő megoldást javasoljuk:

- [Azure Information Protection üzembe helyezése](/azure/information-protection/deployment-roadmap) a szervezet számára.
- Az üzleti igényeknek megfelelő címkéket alkalmazhat. Tegyük fel például, hogy egy "szigorúan bizalmas" nevű címkét alkalmaz minden olyan dokumentumra és e-mailre, amely szigorúan titkos adatot tartalmaz az adatok besorolásához és védelméhez. Ezt követően csak a jogosult felhasználók férhetnek hozzá ezekhez az értékekhez a megadott korlátozásokkal.
- Konfigurálja a [Azure RMS használati naplózását](/azure/information-protection/log-analyze-usage) , hogy nyomon követheti, hogyan használja a szervezet a védelmi szolgáltatást.

Az [adatbesorolásra](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és a fájlokra vonatkozó védelem gyenge szervezetei hajlamosabbak az adatszivárgásra vagy az adatvisszaélésre. A megfelelő fájlkezelővel elemezheti az adatfolyamatokat, hogy betekintést nyerjen a vállalatba, azonosítsa a kockázatos viselkedéseket és javítsa a szükséges intézkedéseket, nyomon követheti a dokumentumokhoz való hozzáférést stb.

## <a name="next-steps"></a>Következő lépések

Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági eljárásaival és modelljeivel](best-practices-and-patterns.md) foglalkozó témakörben talál.

Az Azure-biztonsággal és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általános információk az alábbi forrásokból érhetők el:
* Az [Azure Security csapatának blogja](/archive/blogs/azuresecurity/) – naprakész információk az Azure Security legújabb frissítéseiről
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – a Microsoft biztonsági rései, például az Azure-nal kapcsolatos problémák, jelentések vagy e-mailen keresztül secure@microsoft.com
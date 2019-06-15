---
title: Adatbiztonság és titkosítás ajánlott eljárások – Microsoft Azure
description: Ez a cikk az adatok biztonsági védelmének bevált gyakorlata készletét nyújtja, és az Azure-szolgáltatások beépített titkosítás segítségével.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 9955450b468ef38ba456d7ee73d9681de677494d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190709"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Az Azure Adatbiztonság és titkosítás ajánlott eljárások
Ez a cikk ismerteti az ajánlott eljárások az Adatbiztonság és titkosítás.

Az ajánlott eljárások a vélemény konszenzus alapulnak, és dolgozhat az aktuális Azure platform olyan képességeit, és a szolgáltatáskészletek. Vélemények és technológiák az idő előrehaladtával változik, és ez a cikk a változások követése érdekében rendszeresen frissül.

## <a name="protect-data"></a>Adatok védelme
A felhőbeli adatok védelme érdekében szüksége fiók számára a lehetséges állapotok, amelyben az adatok akkor fordulhat elő, és milyen vezérlők érhetők el az állapotban. Ajánlott eljárások az Azure Adatbiztonság és titkosítás a következő adatok állapotok vonatkoznak:

- Inaktív állapotban: Ez magában foglalja az összes adatokat tároló objektumokat, tárolók, és statikusan fizikai adathordozón, létezik-e mágneses típusait vagy optikai lemez.
- Az átvitel során: Adatátvitel összetevők, helyeket vagy programok között, amikor, az átvitel során. Példák átvitel a hálózaton keresztül (a helyszínről a felhőbe, és fordítva, beleértve a hibrid kapcsolatok, például az ExpressRoute), egy service bus és a egy bemeneti/kimeneti folyamat során.

## <a name="choose-a-key-management-solution"></a>Kulcs megoldás választása

A kulcsok védelme létfontosságú a felhőbeli adatok védelmére.

[Az Azure Key Vault](../key-vault/key-vault-overview.md) segítségével megakadályozhatja a kriptográfiai kulcsokat és titkos kódok, amelyek felhőalapú alkalmazásokat és szolgáltatásokat használja. A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők létrehozhatják a kulcsokat és fejlesztéshez és teszteléshez percek alatt, és ezután telepítheti át őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

A Key Vault használatával hozzon létre több biztonságos tároló, amelyeket kulcstartóknak nevezünk. Ezeket a tárolókat hardveres biztonsági modulok élvezik. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. Kulcstartók is szabályozhatja, és a hozzáférés naplózása a bennük tárolt tartalomhoz. Az Azure Key Vault kérelmezését és megújítását a Transport Layer Security (TLS) tanúsítványok képes kezelni. Biztosítja a funkciók egy hatékony megoldás, a tanúsítványok életciklusának kezelésére.

Az Azure Key Vault támogatja az alkalmazások kulcsainak és titkos kulcsok célja. A Key Vault nem célja a felhasználói jelszavak egy tároló lehet.

Az alábbiakban ajánlott biztonsági eljárások a Key Vault használatával.

**Ajánlott eljárás**: Hozzáférés engedélyezése a felhasználók, csoportok és alkalmazások egy adott hatókörnél.   
**Részletes**: Az RBAC a előre definiált szerepkörök használhatók. Ha például hozzáférést biztosítani egy felhasználónak kulcstartóit kezeljék, rendelne az előre meghatározott szerepkör [Key Vault-közreműködő](../role-based-access-control/built-in-roles.md) ehhez a felhasználóhoz egy adott hatókörnél. A hatókör ebben az esetben lenne egy előfizetés, erőforráscsoport vagy egyszerűen egy adott kulcstartó. Ha az előre definiált szerepkörök nem az igényeinek, [saját szerepkörök definiálása a](../role-based-access-control/custom-roles.md).

**Ajánlott eljárás**: Vezérlő a felhasználók milyen hozzáférést szeretne biztosítani.   
**Részletes**: A kulcstartó-hozzáférés vezérlése két külön felületen, a felügyeleti síkon és az adatsíkon keresztül történik. A felügyeleti sík és az adatsík hozzáférés-vezérlése egymástól függetlenül működik.

RBAC használata a felhasználók milyen hozzáférést szeretne biztosítani a vezérlőbe. Például ha szeretne hozzáférést biztosítani egy alkalmazás-kulcsok használata a key vaultban, csak kell biztosítania az adatsík hozzáférés engedélyei kulcstároló hozzáférési szabályzatainak használatával, és nem felügyeleti síkú hozzáférésre van szükség az alkalmazás. Ezzel szemben ha egy felhasználó a tudják olvasni a tároló tulajdonságait és címkék, de nem rendelkezik a kulcsok, titkos kódok és tanúsítványok elérését, a felhasználó olvasási hozzáférést biztosíthat az RBAC használatával, és nem érhető el az adatsík nem szükséges.

**Ajánlott eljárás**: Tanúsítványok Store tárol a kulcstárolóban. A magas érték vannak a tanúsítványai. Rossz kezekbe kerülnek az alkalmazás biztonsági vagy az adatok biztonsága érdekében csorbát szenvedhetnek.   
**Részletes**: Az Azure Resource Manager biztonságosan telepítheti a virtuális gépek telepítésekor a rendszer Azure virtuális gépek az Azure Key vaultban tárolt tanúsítványok. A kulcstartó megfelelő hozzáférési szabályzatok beállításával, is szabályozhatja a hozzáférést a tanúsítvány fogadására jogosultak. Egy másik előnye, hogy Ön kezelése egy helyen az Azure Key Vaultban a összes tanúsítványt. Lásd: [üzembe helyezése tanúsítványok virtuális gépekhez ügyfél által felügyelt Key vaultból](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) további információt.

**Ajánlott eljárás**: Győződjön meg arról, hogy helyre tudja állítani a törlés kulcstartók vagy a key vault-objektumokon.   
**Részletes**: Tárolók kulcs törlése vagy a key vault-objektumokon nem szándékos vagy rosszindulatú lehet. A helyreállítható Törlés engedélyezése és a végleges törlése a védelmi funkciók Key vault, különösen az inaktív adatok titkosításához használt kulcsokat. Ezek a kulcsok törlését megegyezik az adatvesztést, így a törölt tároló helyreállítása és tároló objektumokat, ha szükséges. A Key Vault helyreállítási műveleteket rendszeresen gyakorolja.

> [!NOTE]
> Ha egy felhasználónak közreműködői engedélye (RBAC), a kulcstartó felügyeleti síkjához van, akkor is hozzáférést magukat az adatsík kulcstartó-hozzáférési házirend beállításával. Azt javasoljuk, hogy Ön szorosan közreműködői hozzáférés szabályozásához a kulcstartókhoz, győződjön meg arról, hogy csak az arra jogosult személyek eléréséhez, és a kulcstartók, kulcsok, titkos kódok és tanúsítványok kezelése.
>
>

## <a name="manage-with-secure-workstations"></a>A biztonságos munkaállomások kezelése

> [!NOTE]
> Az előfizetés-rendszergazda vagy a tulajdonos egy biztonságos hozzáférési szintű munkaállomásokat és a egy emelt hozzáférési szintű munkaállomásokat kell használnia.
>
>

A támadások többsége a végfelhasználó célozza meg, mert a végpont válik a támadás elsődleges pontok egyikéhez. A támadó károsan befolyásolja az endpoint a felhasználó hitelesítő adatainak használatával a munkahelyi adatokhoz való hozzáférést. A legtöbb végponthoz támadások a tény, hogy a felhasználók a saját helyi munkaállomások rendszergazdái is kihasználhatja.

**Ajánlott eljárás**: A biztonságos felügyeleti munkaállomás használatával érzékeny fiókok, feladatok és az adatok védelme.   
**Részletes**: Használja a [emelt hozzáférési szintű munkaállomásokat](https://technet.microsoft.com/library/mt634654.aspx) csökkenteni a támadási felületet a munkaállomásokat. Ezek a biztonságos felügyeleti munkaállomások segítségével néhány ezeket a támadásokat, és ellenőrizze, hogy az adatok biztonságosabb.

**Ajánlott eljárás**: Győződjön meg, hogy az endpoint protection.   
**Részletes**: Biztonsági szabályzatokat érvényesítsen az adatokat, függetlenül az adatok helyétől (felhőbeli vagy helyszíni) által használt összes eszközön.

## <a name="protect-data-at-rest"></a>Inaktív adatok védelme

[Adattitkosítás inaktív](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés az adatok adatvédelmi, megfelelőségi és az adatok elkülönítése felé.

**Ajánlott eljárás**: Az adatok védelme érdekében a lemeztitkosítás alkalmazása.   
**Részletes**: Használat [az Azure Disk Encryption](azure-security-disk-encryption.md). Ez lehetővé teszi, hogy a rendszergazdák számára, hogy a Windows és Linux rendszerű IaaS VM-lemezek titkosítása. Lemeztitkosítás egyesíti a szabványos Windows BitLocker funkcióval, és a Linux dm-crypt funkciót biztosít az operációs rendszer és az adatlemezek kötettitkosítását.

Az Azure Storage és Azure SQL Database titkosítja az inaktív adatok alapértelmezés szerint, és sok szolgáltatás ajánlat titkosítási beállítás. Az Azure Key Vault segítségével az adatok titkosításához használt kulcsok feletti. Lásd: [Azure-erőforrás szolgáltatókat titkosítás modell támogatása további](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Ajánlott eljárások**: Titkosítás használatával kapcsolatos adatokhoz való illetéktelen hozzáférés kockázatok csökkentése érdekében.   
**Részletes**: A meghajtók titkosítását bizalmas adatokat írni őket.

Szervezetek számára, amelyek nem kényszerítenek adattitkosítás több jelennek meg a bizalmas adatokhoz problémákat. Például jogosulatlan vagy rosszindulatú felhasználók előfordulhat, hogy a sérült biztonságú fiókok adatait, illetve a tiszta formátumban kódolt adatok jogosulatlan elérésére. Vállalatok is igazolnia kell, hogy azok a szokott, és a megfelelő biztonsági vezérlők iparági előírásoknak való megfelelés érdekében az adatok biztonságának növelése érdekében.

## <a name="protect-data-in-transit"></a>Adatok védelmére átvitel

Az átvitt adatok védelme a data protection stratégia nagyon fontos részét kell lennie. Adatok áthelyezése számos helyről oda-vissza van, mert általában javasoljuk, hogy mindig használjon az SSL/TLS-protokollok különböző helyek közötti exchange-adatok. Bizonyos esetekben érdemes elkülöníteni a teljes kommunikációs csatornát, a helyszíni és a felhő között infrastruktúra közötti VPN használatával.

Adatok áthelyezése a helyszíni infrastruktúra és az Azure között fontolja meg a megfelelő védelmi módszerek, például a HTTPS- vagy VPN. Azure-beli virtuális hálózathoz és a egy helyszíni hely közötti titkosított forgalmat küld a nyilvános interneten keresztül, amikor [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/).

Az alábbiakban gyakorlati SSL/TLS, HTTPS és az Azure VPN Gateway eljárásokkal.

**Ajánlott eljárás**: Biztonságos hozzáférés több munkaállomásokon található a helyszínről az Azure virtual Networkhöz.   
**Részletes**: Használat [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Ajánlott eljárás**: Biztonságos hozzáférés egy egyéni munkaállomásról található helyszíni az Azure-beli virtuális hálózathoz.   
**Részletes**: Használat [pont – hely VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

**Ajánlott eljárás**: Helyezze át a nagyobb adatkészletek egy dedikált nagy sebességű WAN-kapcsolaton keresztül.   
**Részletes**: Használat [ExpressRoute](../expressroute/expressroute-introduction.md). Ha az ExpressRoute használatát választja, a titkosításhoz is az adatok az alkalmazás szintjén használatával [SSL/TLS](https://support.microsoft.com/kb/257591) vagy egyéb protokollok felvett Protection.

**Ajánlott eljárás**: Az Azure Storage segítségével kezelheti az Azure Portalon keresztül.   
**Részletes**: Összes tranzakció HTTPS-kapcsolaton keresztül történik. Is [Storage REST API-val](https://msdn.microsoft.com/library/azure/dd179355.aspx) kommunikálni HTTPS-kapcsolaton keresztül [Azure Storage](https://azure.microsoft.com/services/storage/).

Szervezetek számára, amelyek az adatok védelmére átvitel sikertelen érzékenyebbek [man-in-the-middle támadások](https://technet.microsoft.com/library/gg195821.aspx), [lehallgatást](https://technet.microsoft.com/library/gg195641.aspx), és a munkamenet-eltérítés. Ezeket a támadásokat lehet az első lépés a bizalmas adatok elérésekor.

## <a name="secure-email-documents-and-sensitive-data"></a>Biztonságos e-mail, dokumentumok és bizalmas adatok

Szeretné szabályozása és e-mailek, dokumentumok és a vállalati kívül megosztott bizalmas adatok biztonságos. [Az Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) egy felhőalapú megoldás, amely segítségével a szervezetek, címkézését, és a dokumentumokat és e-mailek védelmére. Ezt megteheti automatikusan rendszergazdák által meghatározott szabályokkal és feltételekkel, manuálisan a felhasználók, és a egy kombinációját, ahol a felhasználók javaslatok.

Besorolás mindig azonosítható lesz minden alkalommal, függetlenül az adatok tárolására, vagy kivel osztják meg azokat. A címkék vizuális jelölését, például az élőfej, élőláb vagy vízjel tartalmazza. Metaadatokat a fájlokhoz és e-mail-fejlécekhez szövegként kerül. A tiszta szöveges biztosítja, hogy más szolgáltatások, például a megoldások megakadályozni az adatvesztést, a besorolást, és megteheti a szükséges lépéseket.

A védelmi technológia az Azure Rights Management (Azure RMS). Ez a technológia más Microsoft-felhőszolgáltatásokkal és az alkalmazások, például az Office 365 és az Azure Active Directory van integrálva. A védelmi technológia titkosítási, identitáskezelési és engedélyezési házirendeket használ. Az Azure RMS keresztül alkalmazza a rendszer mindig fennmarad a dokumentumok és e-mailek védelme a helytől függetlenül – a szervezet, hálózatokon, fájlkiszolgálókon és alkalmazások kívül vagy belül egyaránt.

Ezen adatvédelmi megoldást megőrzi az adatok feletti akkor is, ha azokat megosztotta másokkal. Azure RMS-t a saját üzleti alkalmazásokkal és adatvédelmi megoldásaival, szoftverforgalmazóktól származó használhatja-e ezek az alkalmazások és megoldások a helyszíni vagy a felhőben.

Azt javasoljuk, hogy Ön:

- [Azure Information Protection üzembe](https://docs.microsoft.com/azure/information-protection/deployment-roadmap) a szervezet számára.
- A alkalmazni címkék, amelyek az üzleti követelményeinek megfelelően. Példa: A alkalmazni "szigorúan bizalmas" dokumentumai és e-mailek besorolására és védelmére ezeket az adatokat, top-secret adatokat tartalmazó címke. Csak a jogosult felhasználók is ezek az adatok az Ön által megadott megadott korlátozások nélkül is hozzáférhet.
- Konfigurálása [az Azure RMS használatnaplózási](https://docs.microsoft.com/azure/information-protection/log-analyze-usage) , hogy figyelhesse a szervezet hogyan használja a védelmi szolgáltatást.

Szervezetek számára, amelyek a gyenge [adatbesorolás](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és lehet, hogy a fájlok védelmének érzékenyebb az adatszivárgás vagy adatok való visszaélés. A megfelelő fájlok védelmének elemezheti az adatfolyamok betekintést az üzleti, kockázatos viselkedés észlelésére és a javító intézkedések megtételére, dokumentum-hozzáférés nyomon követésére és így tovább.

## <a name="next-steps"></a>További lépések

Lásd: [Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md) további ajánlott biztonsági eljárások szeretne használni, amikor a tervezése, telepítése, és a felhőalapú megoldások kezelése az Azure használatával.

Az alábbi forrásanyagokat biztosít az Azure biztonsági és a kapcsolódó Microsoft-szolgáltatások kapcsolatos általános információk érhetők el:
* [Az Azure Security csapat blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – az Azure Security legújabb naprakész információk
* [A Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – Ha a jelenteni lehet a Microsoft biztonsági réseket, beleértve a problémák az Azure-ral, vagy az e-mailen keresztül secure@microsoft.com

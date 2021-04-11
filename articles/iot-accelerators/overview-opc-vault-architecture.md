---
title: OPC Vault-architektúra – Azure | Microsoft Docs
description: OPC-tár tanúsítványkezelő szolgáltatás architektúrája
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: eb558d967ad657d14158684fba92b13979ea5fe2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646802"
---
# <a name="opc-vault-architecture"></a>OPC-tár architektúrája

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Ez a cikk áttekintést nyújt az OPC Vault-szolgáltatásról és az OPC-tároló IoT Edge modulról.

Az OPC UA-alkalmazások alkalmazás-példány-tanúsítványokat használnak az alkalmazás szintű biztonság biztosításához. A biztonságos kapcsolatok aszimmetrikus titkosítással lettek létrehozva, amelyek esetében az alkalmazás tanúsítványai biztosítják a nyilvános és a titkos kulcspár használatát. A tanúsítványok önaláírtak vagy hitelesítésszolgáltatók (CA) által is aláírva.

Az OPC UA-alkalmazások listáját az általa megbízhatónak ítélt alkalmazásokat képviselő megbízható tanúsítványok listája tartalmazza. Ezeket a tanúsítványokat egy HITELESÍTÉSSZOLGÁLTATÓ önaláírta vagy aláírhatja, vagy lehet egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ vagy egy alárendelt HITELESÍTÉSSZOLGÁLTATÓ is. Ha egy megbízható tanúsítvány egy nagyobb tanúsítványlánc részét képezi, az alkalmazás megbízik az összes olyan tanúsítványban, amely a megbízható tanúsítványok listáján a tanúsítványhoz csatlakozik. Ez akkor igaz, ha a teljes tanúsítványláncot ellenőrizni lehet.

A megbízható önaláírt tanúsítványok és a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megbízhatósága közötti legnagyobb különbség a megbízhatóság üzembe helyezéséhez és karbantartásához szükséges telepítési erőfeszítés. A vállalatra vonatkozó HITELESÍTÉSSZOLGÁLTATÓ üzemeltetése is további erőfeszítést igényel. 

Ha az önaláírt tanúsítványok megbízhatóságát szeretné terjeszteni több kiszolgáló számára egyetlen ügyfélalkalmazás használatával, az összes kiszolgálói alkalmazás tanúsítványát telepítenie kell az ügyfélalkalmazás megbízhatósági listájára. Emellett az ügyfélalkalmazás tanúsítványát is telepítenie kell az összes kiszolgálói alkalmazás megbízhatósági listájára. Ez a felügyeleti erőfeszítés meglehetősen nagy terhet jelent, és akkor is növekszik, ha a tanúsítványok élettartamát kell megfontolnia, és meg kell újítania a tanúsítványokat.

A vállalati HITELESÍTÉSSZOLGÁLTATÓ használata nagy mértékben egyszerűsítheti a megbízhatóság kezelését több kiszolgálóval és ügyféllel. Ebben az esetben a rendszergazda egyszer létrehoz egy HITELESÍTÉSSZOLGÁLTATÓI aláírású alkalmazás példányának tanúsítványát minden egyes használt ügyfél és kiszolgáló esetében. Emellett a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány minden alkalmazás-megbízhatósági listában telepítve van minden kiszolgálón és ügyfélen. Ezzel a módszerrel csak a lejárt tanúsítványokat kell megújítani, és lecserélni az érintett alkalmazásokra.

Az Azure Industrial IoT OPC UA tanúsítványkezelő szolgáltatása segítségével kezelheti az OPC UA-alkalmazások vállalati szintű HITELESÍTÉSSZOLGÁLTATÓit. Ez a szolgáltatás az OPC Vault-szolgáltatáson alapul. Az OPC-tároló egy olyan szolgáltatást biztosít, amely egy biztonságos felhőben üzemelteti a vállalatra jellemző HITELESÍTÉSSZOLGÁLTATÓT. Ezt a megoldást a Azure Active Directory (Azure AD) által védett szolgáltatások, Azure Key Vault a hardveres biztonsági modulokkal (HSM), a Azure Cosmos DBekkel és opcionálisan IoT Hub alkalmazás-tárolóként.

Az OPC-tár szolgáltatás úgy van kialakítva, hogy támogassa a szerepköralapú munkafolyamatot, ahol a biztonsági rendszergazdák és a jóváhagyók a Azure Key Vault jóváhagyására vagy elutasítására vonatkozó jogosultságokkal rendelkeznek.

A meglévő OPC UA-megoldásokkal való kompatibilitás érdekében a szolgáltatások közé tartozik az OPC-tár webszolgáltatás által támogatott peremhálózati moduljának támogatása. Ezzel implementálja az **OPC ua globális felderítési kiszolgáló és a Tanúsítványkezelő** felületet, a tanúsítványok és a megbízhatósági kapcsolatok listáját a specifikáció 12. részének megfelelően. 


## <a name="architecture"></a>Architektúra

Az architektúra az OPC-tár szolgáltatáson alapul, és egy OPC-tároló IoT Edge modul a gyári hálózathoz és egy webes minta UX a munkafolyamat vezérléséhez:

![OPC Vault-architektúra ábrája](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC Vault-szolgáltatás

Az OPC-tár szolgáltatás a következő felületekből áll, hogy megvalósítsa a munkafolyamatot az OPC UA-alkalmazások számára a vállalati HITELESÍTÉSSZOLGÁLTATÓ terjesztéséhez és kezeléséhez.

### <a name="application"></a>Alkalmazás 
- Az OPC UA-alkalmazás lehet egy kiszolgáló vagy egy ügyfél, vagy mindkettő. Az OPC-tároló ebben az esetben alkalmazás-regisztrációs szolgáltatóként szolgál. 
- Az alkalmazások regisztrálására, frissítésére és regisztrációjának megszüntetésére szolgáló alapvető műveletek mellett a keresési kifejezésekkel rendelkező alkalmazások keresése és lekérdezése is elérhető. 
- A tanúsítványkérelem érvényes alkalmazásra hivatkozik, hogy feldolgozza a kérést, és az összes OPC UA-specifikus bővítménnyel kiállítson egy aláírt tanúsítványt. 
- Az Application Service-t a Azure Cosmos DB adatbázisa támogatja.

### <a name="certificate-group"></a>Tanúsítvány csoport
- A tanúsítvány-csoport egy olyan entitás, amely egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT vagy egy alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt tárol, beleértve a tanúsítványok aláírására szolgáló titkos kulcsot is. 
- Az RSA-kulcs hossza, az SHA-2 kivonat hossza, valamint az élettartamok mind a kiállító HITELESÍTÉSSZOLGÁLTATÓ, mind az aláírt alkalmazás tanúsítványainak esetében konfigurálhatók. 
- A CA-tanúsítványokat a Azure Key Vault-ban tárolja, és a FIPS 140-2 2. szintű HSM-mel támogatott. A titkos kulcs soha nem hagyja el a biztonságos tárterületet, mert az aláírást egy, az Azure AD által védett Key Vault művelet hajtja végre. 
- Az idő múlásával megújíthatja a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat, és a Key Vault előzmények miatt megmaradnak a biztonságos tárolóban. 
- Az egyes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok visszavonási listáját Key Vault titkos kulcsként is tárolja a rendszer. Egy alkalmazás regisztrációjának törlésekor az alkalmazás tanúsítványát a rendszer a visszavont tanúsítványok listáján (CRL) is visszavonja a rendszergazda.
- Visszavonhatja az önálló tanúsítványokat és a kötegelt tanúsítványokat is.

### <a name="certificate-request"></a>Tanúsítványkérelem
A tanúsítványkérelem egy új kulcspár vagy egy aláírt tanúsítvány előállítására szolgáló munkafolyamatot valósít meg egy OPC UA-alkalmazáshoz tartozó tanúsítvány-aláírási kérelem (CSR) használatával. 
- A kérést egy olyan adatbázisban tárolja a rendszer, amely a kapcsolódó információkat, például a tulajdonost vagy a CSR-t, valamint az OPC UA-alkalmazásra mutató hivatkozást tartalmaz. 
- A szolgáltatásban lévő üzleti logika érvényesíti a kérelmet az alkalmazás-adatbázisban tárolt információkkal. Az adatbázisban szereplő alkalmazás-URI-nak például meg kell egyeznie az alkalmazás URI azonosítóval a CSR-ben.
- Az aláírási jogokkal rendelkező biztonsági rendszergazda (azaz a jóváhagyó szerepkör) jóváhagyja vagy elutasítja a kérelmet. Ha a kérést jóváhagyják, egy új kulcspár vagy aláírt tanúsítvány (vagy mindkettő) jön létre. Az új titkos kulcsot a rendszer biztonságosan tárolja Key Vaultban, és az új aláírt nyilvános tanúsítvány tárolása a tanúsítványkérelem adatbázisában történik.
- A kérelmező lekérdezheti a kérés állapotát, amíg jóvá nem hagyta vagy vissza nem vonja. Ha a kérést jóváhagyták, a titkos kulcs és a tanúsítvány az OPC UA-alkalmazás tanúsítványtárolójában tölthető le és telepíthető.
- A kérelmező mostantól elfogadhatja a szükségtelen információk törlésére vonatkozó kérelmet a kérelem-adatbázisból. 

Egy aláírt tanúsítvány élettartama során előfordulhat, hogy egy alkalmazás törölve lett, vagy a kulcs biztonsága sérült lehet. Ilyen esetben a CA-kezelő a következőket teheti:
- Töröl egy alkalmazást, amely az alkalmazás összes függőben lévő és jóváhagyott tanúsítványkérelem-kérelmét is törli. 
- Csak egyetlen tanúsítványkérelem törlése, ha csak egy kulcs van megújítva vagy sérült.

A rendszer megszakította a megsérült jóváhagyott és elfogadott tanúsítványkérelmek törlését.

A kezelők rendszeresen megújítják a kiállító HITELESÍTÉSSZOLGÁLTATÓ CRL-t. A megújítás időpontjában a rendszer az összes törölt tanúsítványkérelmet visszavonja, és a tanúsítvány sorozatszámait hozzáadja a CRL-visszavonási listához. Visszavont tanúsítványkérelmek vannak megjelölve visszavont tanúsítványként. Sürgős eseményekben az egytanúsítványos kérelmek is visszavonhatók.

Végezetül a frissített CRL-ket elérhetővé kell tenni a résztvevő OPC UA-ügyfelek és-kiszolgálók számára.

## <a name="opc-vault-iot-edge-module"></a>OPC-tároló IoT Edge modulja
A gyári hálózat globális felderítési kiszolgálójának támogatásához az OPC-tároló modult a peremhálózat szélén helyezheti üzembe. Futtassa helyi .NET Core-alkalmazásként, vagy indítsa el egy Docker-tárolóban. Vegye figyelembe, hogy a jelenlegi OPC UA .NET Standard szintű Auth2 érték-hitelesítési támogatás hiánya miatt az OPC-tároló peremhálózati moduljának funkciói az olvasói szerepkörre korlátozódnak. Egy felhasználót nem lehet megszemélyesíteni az Edge-modulból a szolgáltatásba az OPC UA GDS standard interfész használatával.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC Vault architektúráját, a következőket teheti:

> [!div class="nextstepaction"]
> [OPC-tároló létrehozása és üzembe helyezése](howto-opc-vault-deploy.md)

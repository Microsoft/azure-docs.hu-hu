---
title: Az OPC Vault Certificate Management szolgáltatás biztonságos futtatása – Azure | Microsoft Docs
description: Útmutatás az OPC-tár tanúsítványkezelő szolgáltatásának biztonságos futtatásához az Azure-ban, valamint a további megfontolandó biztonsági irányelvek felülvizsgálata.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f54467c03a67797d52ac499ca9ba455c0f75e240
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92071455"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Az OPC Vault tanúsítványkezelő szolgáltatás biztonságos futtatása

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Ez a cikk azt ismerteti, hogyan futtatható az OPC-tár tanúsítványkezelő szolgáltatás az Azure-ban, és hogyan tekintheti át az egyéb biztonsági irányelveket.

## <a name="roles"></a>Szerepkörök

### <a name="trusted-and-authorized-roles"></a>Megbízható és meghatalmazott szerepkörök

Az OPC-tár szolgáltatás lehetővé teszi, hogy a különböző szerepkörök hozzáférjenek a szolgáltatás különböző részeihez.

> [!IMPORTANT]
> Az üzembe helyezés során a parancsfájl csak azt a felhasználót adja meg, aki az üzembe helyezési parancsfájlt felhasználóként futtatja az összes szerepkörhöz. Éles környezetben tekintse át ezt a szerepkör-hozzárendelést, és végezze el újra a megfelelő konfigurálást az alábbi irányelveket követve. Ehhez a feladathoz a szerepkörök és szolgáltatások manuális hozzárendelése szükséges a Azure Active Directory (Azure AD) Enterprise Applications portálon.

### <a name="certificate-management-service-roles"></a>Tanúsítványkezelő szolgáltatás szerepkörei

Az OPC-tár webszolgáltatás a következő szerepköröket határozza meg:

- **Olvasó**: alapértelmezés szerint a bérlő bármely hitelesített felhasználója rendelkezik olvasási hozzáféréssel. 
  - Olvasási hozzáférés az alkalmazásokhoz és tanúsítványkérelmek. Listázhatja és lekérdezheti az alkalmazásokat és a tanúsítványokra vonatkozó kérelmeket. Az eszköz felderítési információi és a nyilvános tanúsítványok olvasási hozzáféréssel is elérhetők.
- **Író**: az író szerepkör hozzá van rendelve egy felhasználóhoz az írási engedélyek bizonyos feladatokhoz való hozzáadásához. 
  - Olvasási/írási hozzáférés az alkalmazásokhoz és a tanúsítványokhoz. Regisztrálhatja, frissítheti és törölheti az alkalmazásokat. Létrehozhat tanúsítvány-kérelmeket, és megszerezheti a jóváhagyott titkos kulcsokat és tanúsítványokat. A titkos kulcsokat is törölhet.
- **Jóváhagyó**: a jóváhagyó szerepkört egy felhasználó rendeli hozzá a tanúsítványkérelmek jóváhagyásához vagy elutasításához. A szerepkör nem tartalmaz más szerepkört.
  - Az OPC-tár Service API-hoz való hozzáférés jóváhagyói szerepkörén kívül a felhasználónak is rendelkeznie kell a kulcs aláírási engedéllyel a Azure Key Vault, hogy képes legyen aláírni a tanúsítványokat.
  - Az író és a jóváhagyó szerepkört különböző felhasználókhoz kell rendelni.
  - A jóváhagyó fő szerepe a tanúsítványkérelmek létrehozásának és elutasításának jóváhagyása.
- **Rendszergazda**: a rendszergazda szerepkör hozzá van rendelve egy felhasználóhoz a Tanúsítványsablonok kezeléséhez. A szerepkör nem támogatja a jóváhagyó szerepkört, de magában foglalja az író szerepkört is.
  - A rendszergazda felügyelheti a tanúsítvány-csoportokat, módosíthatja a konfigurációt, és visszavonhatja az alkalmazás tanúsítványait egy új visszavont tanúsítványok listájának (CRL) kiállításával.
  - Ideális esetben az író, a jóváhagyó és a rendszergazdai szerepkörök különböző felhasználókhoz vannak rendelve. A további biztonság érdekében a jóváhagyó vagy a rendszergazdai szerepkörrel rendelkező felhasználónak kulcs-aláírási engedéllyel kell rendelkeznie a Key Vaultban, a tanúsítványok kiállításához vagy egy kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújításához.
  - A Service-felügyeleti szerepkörön kívül a szerepkör magában foglalja a következőt:
    - A HITELESÍTÉSSZOLGÁLTATÓ biztonsági eljárásai megvalósításának kezeléséért.
    - A tanúsítványok létrehozásának, visszavonásának és felfüggesztésének kezelése. 
    - Titkosítási kulcs életciklus-kezelése (például a kiállító HITELESÍTÉSSZOLGÁLTATÓI kulcsok megújítása).
    - A HITELESÍTÉSSZOLGÁLTATÓT működtető szolgáltatások telepítése, konfigurálása és karbantartása.
    - A szolgáltatások napi működése. 
    - A HITELESÍTÉSSZOLGÁLTATÓ és az adatbázis biztonsági mentése és helyreállítása.

### <a name="other-role-assignments"></a>Egyéb szerepkör-hozzárendelések

A szolgáltatás futtatásakor vegye figyelembe a következő szerepköröket is:

- A tanúsítvány-beszerzési szerződés üzleti tulajdonosa a külső legfelső szintű hitelesítésszolgáltatóval (például ha a tulajdonos tanúsítványokat vásárol egy külső HITELESÍTÉSSZOLGÁLTATÓTÓL, vagy olyan HITELESÍTÉSSZOLGÁLTATÓT üzemeltet, amely egy külső HITELESÍTÉSSZOLGÁLTATÓhoz van rendelve).
- A hitelesítésszolgáltató fejlesztése és érvényesítése.
- A naplózási rekordok áttekintése.
- A HITELESÍTÉSSZOLGÁLTATÓ támogatását vagy a fizikai és a Felhőbeli létesítmények kezelését segítő személyzet, de nem közvetlenül megbízható a HITELESÍTÉSSZOLGÁLTATÓI műveletek elvégzéséhez, a *jogosult* szerepkörben vannak. A jogosult szerepkörrel rendelkező személyek készletét is dokumentálni kell.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Tekintse át a megbízható és a jogosultsággal rendelkező szerepkörök negyedéves tagságát

Legalább negyedévente tekintse át a megbízható és meghatalmazott szerepkörök tagságát. Győződjön meg arról, hogy az egyes szerepkörökben lévő személyek (manuális folyamatok esetében) vagy szolgáltatás-identitások (automatizált folyamatok esetében) minimálisan megmaradnak.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Szerepkör elkülönítése a tanúsítvány kérelmezője és a jóváhagyó között

A tanúsítvány-kiállítási folyamatnak ki kell kényszeríteni a szerepkör elkülönítését a tanúsítvány kérelmezője és a tanúsítvány-jóváhagyó szerepkörök (személyek vagy automatizált rendszerek) között. A tanúsítvány kiállítását jóvá kell hagynia egy tanúsítvány-jóváhagyó szerepkörrel, amely ellenőrzi, hogy a tanúsítvány kérelmezője jogosult-e tanúsítványok beszerzésére. A tanúsítvány-jóváhagyó szerepkörrel rendelkező személyeknek formálisan jogosult személynek kell lenniük.

### <a name="restrict-assignment-of-privileged-roles"></a>Kiemelt szerepkörök hozzárendelésének korlátozása

Korlátozni kell a Kiemelt szerepkörök hozzárendelését, például a rendszergazdák és a jóváhagyó csoportok tagságának engedélyezését a jogosult személyek korlátozott csoportjára. Az emelt szintű szerepkör módosításainak 24 órán belül vissza kell vonnia a hozzáférést. Végül pedig negyedévente tekintse át a Kiemelt szerepkör-hozzárendeléseket, és távolítsa el a szükségtelen vagy lejárt hozzárendeléseket.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>A Kiemelt szerepköröknek kétfaktoros hitelesítést kell használniuk

A többtényezős hitelesítést (más néven kétfaktoros hitelesítést) a jóváhagyó és a rendszergazdák interaktív bejelentkezéséhez használhatja a szolgáltatásnak.

## <a name="certificate-service-operation-guidelines"></a>A Certificate Service üzemeltetési iránymutatásai

### <a name="operational-contacts"></a>Operatív névjegyek

A Certificate Service-nek naprakész biztonsági válaszszabály szükséges a fájlra vonatkozóan, amely részletes működési incidensi válaszokat tartalmaz.

### <a name="security-updates"></a>Biztonsági frissítések

Minden rendszert folyamatosan figyelni és frissíteni kell a legújabb biztonsági frissítésekkel.

> [!IMPORTANT]
> Az OPC-tároló szolgáltatás GitHub-tárháza folyamatosan frissül a biztonsági javításokkal. Figyelje ezeket a frissítéseket, és rendszeres időközönként alkalmazza őket a szolgáltatásra.

### <a name="security-monitoring"></a>A biztonság monitorozása

Előfizetés a megfelelő biztonsági figyelésre vagy megvalósításra. Tegyük fel például, hogy előfizet egy központi figyelési megoldásra (például Azure Security Center vagy Microsoft 365 figyelési megoldásra), és konfigurálja megfelelően annak biztosításához, hogy a biztonsági események továbbítva legyenek a figyelési megoldásnak.

> [!IMPORTANT]
> Alapértelmezés szerint az OPC Vault szolgáltatás üzembe helyezése az [Azure Application Insights](../azure-monitor/app/devops.md) figyelési megoldásként történik. A [Azure Security Centerhoz](https://azure.microsoft.com/services/security-center/) hasonló biztonsági megoldás hozzáadása kifejezetten ajánlott.

### <a name="assess-the-security-of-open-source-software-components"></a>A nyílt forráskódú szoftverek összetevőinek biztonságának felmérése

A terméken vagy szolgáltatáson belül használt összes nyílt forráskódú összetevőnek szabadnak kell lennie a mérsékelt vagy nagyobb biztonsági sebezhetőségtől.

> [!IMPORTANT]
> A folyamatos integrációs buildek során az OPC-tár szolgáltatás GitHub-tárháza megvizsgálja a biztonsági rések összes összetevőjét. Figyelje ezeket a frissítéseket a GitHubon, és rendszeres időközönként alkalmazza őket a szolgáltatásra.

### <a name="maintain-an-inventory"></a>Leltár karbantartása

Az összes üzemi gazdagép (beleértve az állandó virtuális gépeket is), az eszközök, az összes belső IP-címtartomány, a VIP-cím és a nyilvános DNS-tartománynevek készletének karbantartása. Amikor rendszer-, eszköz-IP-címet, VIP-t vagy nyilvános DNS-tartományt ad hozzá vagy távolít el, 30 napon belül frissítenie kell a leltárt.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Az Azure OPC-tároló alapértelmezett üzemi telepítésének leltára 

Az Azure-ban:
- **App Service terv**: app Service-csomag a Service hosts szolgáltatáshoz. Alapértelmezett S1.
- **App Service** a Service-hez: az OPC Vault Service Host.
- **App Service** a minta alkalmazáshoz: az OPC-tár minta Application Host.
- **Key Vault standard**: a webszolgáltatásokhoz tartozó titkok és Azure Cosmos db kulcsok tárolása.
- **Key Vault Premium**: a kiállító hitelesítésszolgáltatói kulcsok, az aláírási szolgáltatás, valamint a tár konfigurációja és az alkalmazás titkos kulcsai tárolásának üzemeltetése.
- **Azure Cosmos db**: az alkalmazás-és tanúsítványkérelmek adatbázisa. 
- **Application Insights**: (nem kötelező) figyelési megoldás webszolgáltatásokhoz és alkalmazásokhoz.
- **Azure ad-alkalmazás regisztrálása**: a minta alkalmazás, a szolgáltatás és a peremhálózati modul regisztrálása.

A Cloud Services esetében dokumentálni kell a szolgáltatás üzembe helyezéséhez használt összes állomásnevet, erőforráscsoportot, erőforrás-nevet, előfizetési azonosítót és bérlői azonosítót. 

Azure IoT Edge vagy helyi IoT Edge-kiszolgálón:
- **OPC-tároló IoT Edge modul**: a Factory Network OPC ua globális felderítési kiszolgáló támogatásához. 

A IoT Edge eszközök esetében dokumentálni kell a gazdagépeket és az IP-címeket. 

### <a name="document-the-certification-authorities-cas"></a>A hitelesítésszolgáltatók dokumentálása (CAs)

A HITELESÍTÉSSZOLGÁLTATÓI hierarchia dokumentációjában az összes működő hitelesítésszolgáltatónak szerepelnie kell. Ebbe beletartozik az összes kapcsolódó alárendelt hitelesítésszolgáltató, szülő hitelesítésszolgáltató és legfelső szintű hitelesítésszolgáltató is, még akkor is, ha azokat nem a szolgáltatás kezeli. A formális dokumentáció helyett megadhatja az összes nem lejárt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány teljes készletét.

> [!NOTE]
> Az OPC-tár minta alkalmazása támogatja a szolgáltatásban a dokumentációban felhasznált és előállított összes tanúsítvány letöltését.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>A kiállított tanúsítványok dokumentálása minden hitelesítésszolgáltató (CAs) szerint

Adja meg az elmúlt 12 hónapban kiállított összes tanúsítvány teljes készletét.

> [!NOTE]
> Az OPC-tár minta alkalmazása támogatja a szolgáltatásban a dokumentációban felhasznált és előállított összes tanúsítvány letöltését.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>A titkosítási kulcsok biztonságos törlésére szolgáló szabványos működési eljárás dokumentálása

A HITELESÍTÉSSZOLGÁLTATÓ élettartama során a kulcsok törlése csak ritkán történhet meg. Ezért nincs olyan felhasználó, akinek nincs Key Vault tanúsítvány-törlési jogosultsága, és miért nincsenek elérhető API-k egy kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány törléséhez. A hitelesítésszolgáltató titkosítási kulcsainak biztonságos törléséhez szükséges manuális szabványos működési eljárás csak a Azure Portal Key Vault közvetlen elérésével érhető el. Key Vault is törölheti a tanúsítványt. Az azonnali törlés érdekében tiltsa le a [Key Vault Soft delete](../key-vault/general/soft-delete-overview.md) funkciót.

## <a name="certificates"></a>Tanúsítványok

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>A tanúsítványoknak meg kell felelniük a minimális tanúsítvány-profilnak

Az OPC-tár szolgáltatás egy online HITELESÍTÉSSZOLGÁLTATÓ, amely a végfelhasználói tanúsítványokat ad ki az előfizetőknek. Az OPC-tár webszolgáltatás az alapértelmezett implementációban követi ezeket az irányelveket.

- Az összes tanúsítványnak tartalmaznia kell a következő X. 509 mezőket az alább megadott módon:
  - A Version mező tartalmának v3-nek kell lennie. 
  - A serialNumber mező tartalmának legalább 8 bájtnak kell lennie, amely egy FIPS (Federal Information Processing Standards) 140 jóváhagyott véletlenszám-generátort tartalmaz.<br>
    > [!IMPORTANT]
    > Az OPC-tároló sorozatszáma alapértelmezés szerint 20 bájt, és az operációs rendszer kriptográfiai véletlenszám-létrehozója alapján lesz beszerezve. A véletlenszám-generátor a Windows-eszközökön jóváhagyott FIPS 140, de nem Linux rendszeren. Ezt a Linux virtuális gépeket vagy linuxos Docker-tárolókat használó szolgáltatások központi telepítésének kiválasztásakor érdemes figyelembe venni, amelyeken a mögöttes technológiai OpenSSL nem engedélyezett az FIPS 140-ben.
  - A issuerUniqueID és a subjectUniqueID mező nem lehet jelen.
  - A végfelhasználói tanúsítványokat az IETF RFC 5280-es verziójának megfelelően meg kell határozni az alapszintű megkötések bővítménnyel.
  - A kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz a pathLenConstraint mezőt 0-ra kell állítani. 
  - A kibővített kulcshasználat bővítménynek jelen kell lennie, és tartalmaznia kell a kibővített kulcshasználat objektum-azonosítók (OID) minimális készletét. Nem adható meg az anyExtendedKeyUsage OID (2.5.29.37.0). 
  - A CRL terjesztési pont (CDP) bővítménynek jelen kell lennie a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványban.<br>
    > [!IMPORTANT]
    > A CDP-bővítmény megtalálható az OPC-tároló HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokban. Az OPC UA-eszközök azonban egyéni metódusokat használnak a visszavonási listák terjesztésére.
  - A szolgáltatói információ-hozzáférési bővítménynek szerepelnie kell az előfizetői tanúsítványokban.<br>
    > [!IMPORTANT]
    > A szolgáltatói információ-hozzáférési bővítmény az OPC-tároló előfizetői tanúsítványokban van jelen. Az OPC UA-eszközök azonban egyéni módszereket használnak a kiállító HITELESÍTÉSSZOLGÁLTATÓ adatainak terjesztésére.
- A jóváhagyott aszimmetrikus algoritmusokat, a kulcs hosszát, a kivonatoló függvényeket és a kitöltési módot kell használni.
  - Az RSA és az SHA-2 az egyetlen támogatott algoritmus.
  - Az RSA használható titkosításhoz, kulcscserehez és aláíráshoz.
  - Az RSA-titkosításnak csak a OAEP, az RSA-KEM vagy az RSA-PSS kitöltési módot kell használnia.
  - A 2048 bitenél nagyobb vagy azzal egyenlő kulcs hosszúsága kötelező.
  - Használja az SHA-2 kivonatoló algoritmust (SHA256, SHA384 és SHA512).
  - A 20 évnél nagyobb vagy azzal megegyező élettartamú RSA legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI kulcsok esetében 4096 bit vagy nagyobb értéknek kell lennie.
  - Az RSA kiállító CA kulcsainak legalább 2048 bitenek kell lenniük. Ha a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány lejárati dátuma 2030, akkor a CA-kulcsnak 4096 bit vagy nagyobbnak kell lennie.
- Tanúsítvány élettartama
  - Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok: a legfelső szintű hitelesítésszolgáltatók maximális tanúsítványának érvényességi időtartama nem haladhatja meg a 25 évet.
  - Alárendelt HITELESÍTÉSSZOLGÁLTATÓ vagy online kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványai: a tanúsítvány maximális érvényességi időtartama az online hitelesítésszolgáltatók számára, és csak az előfizetői tanúsítványok kiadása nem haladhatja meg a 6 évet. Ezen hitelesítésszolgáltatók esetében a kapcsolódó privát aláírási kulcs nem használható 3 évnél hosszabb ideig az új tanúsítványok kiállításához.<br>
    > [!IMPORTANT]
    > A kiállítói tanúsítvány, ahogy az alapértelmezett OPC-tárolóban a külső legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ nélkül lett létrehozva, a rendszer úgy kezeli, mint egy online alhálózatot, a megfelelő követelményekkel és élettartamokkal. A rendszer az alapértelmezett élettartamot 5 évre állítja be, amelynek a kulcsa a 2048-nál nagyobb vagy azzal egyenlő.
  - Minden aszimmetrikus kulcsnak legfeljebb 5 éves élettartammal kell rendelkeznie, és ajánlott 1 éves élettartama.<br>
    > [!IMPORTANT]
    > Alapértelmezés szerint az OPC-tárolóban kiadott alkalmazás-tanúsítványok élettartama 2 év, és minden évben le kell cserélni. 
  - A tanúsítvány megújításakor a rendszer új kulccsal újítja meg.
- OPC UA-specifikus bővítmények az Application instance-tanúsítványokban
  - Az subjectAltName bővítmény tartalmazza az alkalmazás URI-ját és a gazdagépeket. Ilyenek lehetnek a teljes tartománynevek, az IPv4-és az IPv6-címek is.
  - A kulcshasználat magában foglalja a digitalSignature, a letagadhatatlanság, a keyEncipherment és a dataEncipherment.
  - A extendedKeyUsage tartalmazza a serverAuth és a clientAuth.
  - Az authorityKeyIdentifier az aláírt tanúsítványokban van megadva.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>A HITELESÍTÉSSZOLGÁLTATÓI kulcsoknak és tanúsítványoknak meg kell felelniük a minimális követelményeknek

- **Titkos kulcsok**: az RSA-kulcsoknak legalább 2048 bitesnek kell lenniük. Ha a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány lejárati dátuma 2030, akkor a CA-kulcsnak 4096 bit vagy nagyobbnak kell lennie.
- **Élettartam**: a tanúsítvány maximális érvényességi időtartama a online hitelesítésszolgáltatók számára, és csak az előfizetői tanúsítványok kiadása nem haladhatja meg a 6 évet. Ezen hitelesítésszolgáltatók esetében a kapcsolódó privát aláírási kulcs nem használható 3 évnél hosszabb ideig az új tanúsítványok kiállításához.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>A HITELESÍTÉSSZOLGÁLTATÓI kulcsokat hardveres biztonsági modulok védik

A OpcVault Azure Key Vault Premiumot használ, és a kulcsokat a FIPS 140-2 2. szintű hardveres biztonsági modul (HSM) védi. 

A Key Vault által használt kriptográfiai modulok (HSM vagy szoftverek) FIPS-hitelesítéssel rendelkeznek. A HSM által védettként létrehozott vagy importált kulcsok a HSM-ben lesznek feldolgozva, amely a 2. szintű FIPS 140-2-es szintre van érvényesítve. A szoftveresen védettként létrehozott vagy importált kulcsok feldolgozása az 1. szintű FIPS 140-2-es szintre ellenőrzött titkosítási modulokon belül történik.

## <a name="operational-practices"></a>Működési eljárások

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>A tanúsítványigénylés szabványos működési PKI-eljárásainak dokumentálása és karbantartása

Dokumentálja és karbantartsa a hitelesítésszolgáltatók tanúsítványait, például a következőket: 
- Az előfizető azonosítása és hitelesítése. 
- A tanúsítványkérelem feldolgozásának és ellenőrzésének módja (ha van ilyen) a tanúsítvány-megújítási és-újragenerálási kérelmek feldolgozásának módját is beleértve. 
- A kiállított tanúsítványok az előfizetők számára való terjesztése. 

Az OPC-tár Service SOP-t az [OPC-tároló architektúrájában](overview-opc-vault-architecture.md) és [az OPC-tár tanúsítvány-szolgáltatásának kezelésében](howto-opc-vault-manage.md)ismertetjük. Az eljárások a "OPC Unified Architecture Specification 12. rész: felderítés és globális szolgáltatások" című részben olvashatók.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>A tanúsítványok visszavonására szolgáló szabványos PKI-eljárások dokumentálása és karbantartása

A tanúsítvány-visszavonási folyamat az [OPC-tároló architektúrájában](overview-opc-vault-architecture.md) és [az OPC-tár tanúsítvány-szolgáltatásának kezelése](howto-opc-vault-manage.md)című témakörben található.
    
### <a name="document-ca-key-generation-ceremony"></a>A HITELESÍTÉSSZOLGÁLTATÓI kulcs létrehozási ünnepségének dokumentálása 

A kiállító HITELESÍTÉSSZOLGÁLTATÓ kulcsának létrehozása az OPC-tárolói szolgáltatásban egyszerűsített, a Azure Key Vault biztonságos tárterülete miatt. További információ: [az OPC-tár tanúsítvány-szolgáltatásának kezelése](howto-opc-vault-manage.md).

Ha azonban külső legfelső szintű hitelesítésszolgáltatót használ, a HITELESÍTÉSSZOLGÁLTATÓI kulcs generálási ünnepségének meg kell felelnie az alábbi követelményeknek.

A HITELESÍTÉSSZOLGÁLTATÓI kulcs generálási ünnepségét olyan dokumentált parancsfájlon kell végrehajtani, amely legalább a következő elemeket tartalmazza: 
- A szerepkörök és a résztvevői felelősségek meghatározása.
- Jóváhagyás a HITELESÍTÉSSZOLGÁLTATÓI kulcs generálási ünnepségének végrehajtásához.
- Az ünnepséghez szükséges titkosítási hardverek és aktiválási anyagok.
- Hardver-előkészítés (beleértve az eszköz/konfigurációs adatok frissítését és kijelentkezését).
- Operációs rendszer telepítése.
- A HITELESÍTÉSSZOLGÁLTATÓI kulcs generálási ünnepsége során végrehajtott konkrét lépések, például: 
  - HITELESÍTÉSSZOLGÁLTATÓI alkalmazások telepítése és konfigurálása.
  - HITELESÍTÉSSZOLGÁLTATÓI kulcs létrehozása.
  - HITELESÍTÉSSZOLGÁLTATÓI kulcs biztonsági mentése.
  - HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány aláírása.
  - Aláírt kulcsok importálása a szolgáltatás védett HSM-ben.
  - HITELESÍTÉSSZOLGÁLTATÓ rendszerleállása.
  - A tároláshoz szükséges anyagok előkészítése.


## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC-tároló biztonságos kezelését, a következőket teheti:

> [!div class="nextstepaction"]
> [Az OPC UA-eszközök biztonságossá tétele az OPC-tárolóval](howto-opc-vault-secure.md)
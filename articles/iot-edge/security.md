---
title: Biztonsági keretrendszer – Azure IoT Edge | Microsoft Docs
description: Ismerje meg a Azure IoT Edge fejlesztéséhez használt biztonsági, hitelesítési és engedélyezési szabványokat, és figyelembe kell venni a megoldás megtervezése során
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ff154eee6c6174a8f1a3aa7bea37ef62273bb1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489502"
---
# <a name="security-standards-for-azure-iot-edge"></a>A Azure IoT Edge biztonsági előírásai

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Azure IoT Edge az adatai és az elemzések intelligens peremhálózatba való áthelyezésekor rejlő kockázatokat kezeli. A IoT Edge biztonsági szabványok rugalmasságot biztosítanak a különböző üzembe helyezési forgatókönyvek számára az összes Azure-szolgáltatástól várt védelemmel.

IoT Edge a hardver különböző modelljein és típusain fut, több operációs rendszert támogat, és különböző üzembe helyezési forgatókönyvekre vonatkozik. Ahelyett, hogy konkrét megoldásokat kínál konkrét forgatókönyvekhez, IoT Edge egy bővíthető biztonsági keretrendszer, amely a méretezéshez tervezett, jól megalapozott alapelveken alapul. Az üzembe helyezési forgatókönyv kockázata számos tényezőtől függ, többek között:

* Megoldás tulajdonjoga
* Központi telepítés földrajza
* Adatérzékenység
* Adatvédelem
* Alkalmazás függőlegesen
* Szabályozási követelmények

Ez a cikk áttekintést nyújt a IoT Edge biztonsági keretrendszerről. További információ: [az intelligens peremhálózat biztonságossá tétele](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Szabványok

A szabványok megkönnyítik az alapos vizsgálatot és a könnyű megvalósítást, mindkettő a biztonság szempontjából fémjelzi. A biztonsági megoldásnak a kiértékelés alatt meg kell adniuk a megbízhatóságot, és nem szabad az üzembe helyezés akadálya. A Azure IoT Edge biztonságossá tételének kialakítása az idő és az iparág által bizonyítottan jól bevált biztonsági protokollokon alapul.

## <a name="authentication"></a>Hitelesítés

IoT-megoldás üzembe helyezésekor tudnia kell, hogy csak a megbízható szereplők, eszközök és modulok férhetnek hozzá a megoldáshoz. A tanúsítványalapú hitelesítés a Azure IoT Edge platform hitelesítésének elsődleges mechanizmusa. Ez a mechanizmus a nyilvános kulcsokra épülő infrastruktúrát (PKiX) az Internet Engineering Task Force (IETF) által szabályozott szabványok összessége alapján származtatja.

Az Azure IoT Edge eszközzel kommunikáló összes eszköznek, modulnak és résztvevőnek egyedi tanúsítvány-identitással kell rendelkeznie. Ez az útmutató arra vonatkozik, hogy az interakciók fizikai vagy hálózati kapcsolaton keresztül vannak-e. Nem minden forgatókönyv vagy összetevő használhat tanúsítvány alapú hitelesítést, így a biztonsági keretrendszer bővíthetősége biztonságos alternatívákat biztosít.

További információ: [Azure IoT Edge tanúsítvány használata](iot-edge-certs.md).

## <a name="authorization"></a>Engedélyezés

A legalacsonyabb jogosultság elve azt jelenti, hogy a rendszer felhasználóinak és összetevőinek csak a szerepkörök végrehajtásához szükséges erőforrások és adatok minimális készletéhez kell hozzáféréssel rendelkezniük. Az eszközök, modulok és színészek csak a jogosultsági hatókörén belüli erőforrásokhoz és információkhoz férnek hozzá, és csak akkor, ha az architektúra engedélyezve van. Bizonyos engedélyek megfelelő jogosultságokkal konfigurálhatók, mások pedig építészeti szempontból kényszerítve vannak. Előfordulhat például, hogy egyes modulok engedélyt kapnak az Azure IoT Hubhoz való kapcsolódásra. Azonban nincs ok arra, hogy az egyik IoT Edge eszközhöz tartozó modul egy másik IoT Edge eszközben is hozzáférhessen egy modulhoz.

Más engedélyezési sémák közé tartozik a tanúsítvány-aláírási jogosultságok és a szerepköralapú hozzáférés-vezérlés (RBAC).

## <a name="attestation"></a>Igazolás

Az igazolás biztosítja a szoftveres BITS integritását, ami fontos a kártevők észleléséhez és megelőzéséhez. A Azure IoT Edge biztonsági keretrendszer három fő kategória alapján osztályozza az igazolást:

* Statikus igazolás
* Futtatókörnyezeti igazolás
* Szoftver igazolása

### <a name="static-attestation"></a>Statikus igazolás

A statikus igazolás ellenőrzi az eszközön lévő összes szoftver integritását az áramellátás során, beleértve az operációs rendszert, az összes futtatókörnyezetet és a konfigurációs információt. Mivel a kikapcsolás során statikus igazolás történik, gyakran biztonságos rendszerindítás néven is ismert. A IoT Edge eszközök biztonsági keretrendszere kiterjed a gyártókra, és olyan biztonságos hardver-képességeket tartalmaz, amelyek biztosítják a statikus igazolási folyamatokat. Ezek a folyamatok a biztonságos rendszerindítást és a biztonságos belső vezérlőprogram frissítését tartalmazzák. A szilícium-szállítókkal folytatott szoros együttműködés során a felesleges belső vezérlőprogram-rétegek elkerülhetők, így a fenyegetések felszínét csökkentik.

### <a name="runtime-attestation"></a>Futtatókörnyezeti igazolás

Miután egy rendszer elvégezte a biztonságos rendszerindítást, a jól megtervezett rendszereknek fel kell ismerniük a kártevők beadására tett kísérleteket és a megfelelő ellenintézkedéseket. A kártevők elleni támadások a rendszerek portjait és felületeit célozzák meg. Ha a rosszindulatú szereplők fizikailag férnek hozzá egy eszközhöz, az eszköz maga is meghamisíthatja az eszközt, vagy használhatja a csatornákon keresztüli támadásokat, hogy hozzáférjenek. Az ilyen malcontent, függetlenül attól, hogy a kártevők és a jogosulatlan módosítások nem észlelhetők-e statikus igazolással, mert a rendszer a rendszerindítási folyamat után fecskendez. Az eszköz hardveres súgója által kínált vagy kényszerített ellenintézkedések az ilyen fenyegetések kivédéséhez. A IoT Edge biztonsági keretrendszere explicit módon olyan bővítményeket hív meg, amelyek a futásidejű fenyegetések elleni küzdelemre irányulnak.  

### <a name="software-attestation"></a>Szoftver igazolása

Az összes kifogástalan állapotú rendszernek, beleértve az intelligens peremhálózati rendszereket, javításokat és frissítéseket kell használnia. A biztonsági frissítések fontosak a frissítési folyamatokhoz, ellenkező esetben lehetséges veszélyforrások lehetnek. A IoT Edge biztonsági keretrendszere a mért és aláírt csomagok alapján kéri a frissítéseket a csomagok forrásának integritásának és hitelesítésének biztosítására. Ez a szabvány az összes operációs rendszerre és alkalmazás szoftveres BITS-re vonatkozik.

## <a name="hardware-root-of-trust"></a>A hardver gyökerének megbízhatósága

Számos intelligens peremhálózati eszközhöz, különösen azokhoz az eszközökhöz, amelyekhez a potenciális kártékony szereplők fizikailag hozzáférhetnek, a hardveres biztonság a védelem utolsó védelmi lehetősége. Az ilyen üzembe helyezések esetében elengedhetetlen a jogosulatlanul védett hardverek használata. Azure IoT Edge arra ösztönzi a biztonságos szilícium-gyártókat, hogy a különböző kockázati profilok és üzembe helyezési forgatókönyvek esetében különböző típusú hardveres megbízhatósági kapcsolatot nyújtsanak A hardveres megbízhatóság a közös biztonsági protokollok (például a platformmegbízhatósági modul (ISO/IEC 11889) és a Trusted Computing Group eszköz-azonosító összeállítási motorja (DICE) esetében lehet. A biztonságos enklávé-technológiák, például a TrustZones és a Software Guard Extensions (SGX ENKLÁVÉHOZ) is biztosítanak hardveres megbízhatóságot.

## <a name="certification"></a>Tanúsítvány

Annak érdekében, hogy az ügyfelek tájékozott döntéseket hozhassanak Azure IoT Edge eszközeinek beszerzése során az üzembe helyezéshez, a IoT Edge keretrendszer tartalmaz minősítési követelményeket. Ezeknek a követelményeknek a megalapozása a biztonsági jogcímek és a biztonság megvalósításának érvényesítésére vonatkozó minősítések. A biztonsági jogcímek tanúsítványa például azt jelenti, hogy a IoT Edge eszköz biztonságos hardvert használ, amely képes a rendszerindítási támadásokra. Az érvényesítési tanúsítvány azt jelenti, hogy a biztonságos hardver megfelelően lett megvalósítva, hogy felkínálja ezt az értéket az eszközön. Az egyszerűség elve miatt a keretrendszer minimálisan igyekszik megtartani a minősítés terheit.

## <a name="extensibility"></a>Bővíthetőség

A IoT technológia különböző típusú üzleti átalakításokat vezet be, a biztonságnak párhuzamosan kell fejlődni az új forgatókönyvek kezeléséhez. A Azure IoT Edge biztonsági keretrendszer egy olyan szilárd alaptal kezdődik, amelyen a bővíthetővé válik a különböző dimenziók között:

* Az első féltől származó biztonsági szolgáltatások, például az Azure IoT Hub eszköz kiépítési szolgáltatása.
* Harmadik féltől származó szolgáltatások, például a felügyelt biztonsági szolgáltatások a különböző alkalmazás-vertikális eszközökhöz (például az ipari vagy az egészségügyi szolgáltatáshoz) vagy a technológiai fókuszhoz (például a Mesh hálózatokban vagy a Silicon Hardware igazolási szolgáltatásokban) a partnerek gazdag hálózatán keresztül.
* A régebbi rendszerek olyan alternatív biztonsági stratégiákkal való utólagos átalakítást foglalnak magukban, mint például a hitelesítéshez és az identitáskezeléshoz szükséges tanúsítványokon kívüli biztonságos technológia használata.
* Biztonságos hardver a feltörekvő biztonságos hardveres technológiák és a szilícium-partnerek hozzájárulásainak bevezetéséhez.

Az intelligens peremhálózat biztonságossá tétele érdekében a IoT védelmének közös érdeke a nyílt Közösségtől származó együttműködésen alapuló hozzájárulásokat igényel. Ezek a hozzájárulások biztonságos technológiák vagy szolgáltatások formájában lehetnek. A Azure IoT Edge biztonsági keretrendszer egy stabil alapot biztosít a biztonság számára, amely a maximális lefedettség érdekében bővíthető, és az Azure-felhővel megegyező megbízhatóságot és integritást biztosít az intelligens környezetekben.  

## <a name="next-steps"></a>Következő lépések

További információ a Azure IoT Edge az [intelligens peremhálózat biztonságossá](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)tételéről.

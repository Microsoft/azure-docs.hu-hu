---
title: Edge Secured-Core minősítési követelmények
description: Edge Secured-Core minősítési program követelményei
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166904"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Azure Certified Device-Edge Secured-Core (előzetes verzió) #

## <a name="edge-secured-core-certification-requirements"></a>Edge Secured-Core minősítési követelmények ##

Ez a dokumentum az adott eszközre vonatkozó olyan képességeket és követelményeket ismerteti, amelyek megfelelnek az Azure IoT Device Catalog-ban az Edge Secured-Core címkével rendelkező eszköz minősítésének és listázásának.

### <a name="program-purpose"></a>Program célja ###
Az Edge Secured-Core egy növekményes tanúsítvány az Azure Certified eszköz programjában, amely teljes operációs rendszert, például linuxos vagy Windows 10 IoT futtató IoT eszközöket biztosít. Ez a program lehetővé teszi, hogy az eszközök partnereink a biztonsági feltételek további készletével megkülönböztessék eszközeiket. Az ehhez a feltételhez tartozó eszközök engedélyezik az alábbi ígéreteket:
1. Hardveres eszköz identitása 
2. Képes a rendszerintegritás kényszerítésére 
3. Naprakész marad, és távolról kezelhető
4. Biztosítja a REST-alapú védelmet
5. Adatátviteli védelmet biztosít
6. Beépített biztonsági ügynök és megerősítés
### <a name="requirements"></a>Követelmények ###

---
|Name|SecuredCore. beépített. biztonság|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja, hogy az eszközök jelentést készítsenek a biztonsági információkról és az eseményekről, ha adatokat küldenek az Azure Defendernek a IoT.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés |Az eszköznek biztonsági naplókat és riasztásokat kell előállítania. Az eszköz naplózza és riasztásokat küld Azure Security Center.<ol><li>Biztonsági ügynök letöltése és üzembe helyezése a GitHubról</li><li>Riasztási üzenet ellenőrzése az Azure Defender for IoT.</li></ol>|
|Források|[Azure docs IoT Defender a IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Name|SecuredCore. encryption. Storage|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy a bizalmas adatok titkosítva maradhatnak-e nem felejtő tárolón.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy a tárolási titkosítás engedélyezve legyen, és az alapértelmezett algoritmus XTS-AES legyen, és a kulcs hossza 128 bit vagy nagyobb.|
|Források||

---
|Name|SecuredCore. Hardware. SecureEnclave|
|:---|:---|
|Állapot|Választható|
|Leírás|A teszt célja, hogy ellenőrizze a biztonságos enklávé létezését, és hogy az enklávé elérhető legyen egy biztonságos ügynökből.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy az Azure biztonsági ügynök kommunikálni tudjon a biztonságos enklávéval|
|Források|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore. Hardware. Identity|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy az eszköz azonosítása a hardverben gyökerezik-e.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy az eszköz rendelkezik a TPM-mel, és hogy az IoT Hub a TPM-kiterjesztési kulcs használatával kiépíthető.|
|Források|[Automatikus kiépítés beállítása DPS-vel](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore. Update|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy az eszköz képes-e a belső vezérlőprogram és a szoftverek fogadására és frissítésére.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|A partner megerősíti, hogy a Microsoft Update, az Azure-eszköz frissítése vagy más jóváhagyott szolgáltatások segítségével frissítést tudott küldeni az eszközre.|
|Források|[IoT Hub eszköz frissítése](../iot-hub-device-update/index.yml)|

---
|Name|SecuredCore.Manageability.Configszülő|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy az eszközök támogatják-e a távoli biztonsági felügyeletet.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy az eszköz támogassa a távolról felügyelhető és kifejezetten biztonsági konfigurációkat. És a rendszer visszaküldi az állapotot IoT Hub/Azure Defender for IoT.|
|Források||

---
|Name|SecuredCore. kezelhetőség. alaphelyzetbe állítás|
|:---|:---|
|Állapot|Kötelező|
|Leírás|Ennek a tesztnek az az oka, hogy az eszközt két használati esettel érvényesíti: a) az Alaphelyzetbe állítás lehetősége (felhasználói adatok eltávolítása, felhasználói konfigurációk eltávolítása), b) az eszköz utolsó ismert jó állapotának visszaállítása a problémát okozó frissítés esetén.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszköz, amelyet az eszközkészlet és a beküldött dokumentáció kombinációja alapján kell érvényesíteni, hogy az eszköz támogatja ezt a funkciót. Az eszköz gyártója eldöntheti, hogy implementálja-e ezeket a képességeket a távoli visszaállítás vagy csak a helyi visszaállítás támogatásához.|
|Források||

---
|Name|SecuredCore. Updates. időtartam|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A szabályzat célja annak biztosítása, hogy az eszköz biztonságban maradjon.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Kézi|
|Érvényesítés|Kötelezettségvállalás arról, hogy az eszközök minősítése az eszközöknek a beküldés napjától számítva 60 hónapig tart. A vásárló és az eszközök számára elérhető specifikációknak bizonyos módon meg kell adniuk a szoftver frissítésének időtartamát.|
|Források||

---
|Name|SecuredCore. Policy. vuln. közzétételi|
|:---|:---|
|Állapot|Kötelező|
|Leírás|Ennek a szabályzatnak a célja annak biztosítása, hogy a termék biztonsági rései jelentéseinek gyűjtésére és terjesztésére szolgáló mechanizmust biztosítson.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Kézi|
|Érvényesítés|A hitelesített eszközökre vonatkozó sebezhetőségi jelentések küldésének és fogadásának folyamatáról a dokumentációban olvashat.|
|Források||

---
|Name|SecuredCore. Policy. vuln. javítások|
|:---|:---|
|Állapot|Kötelező|
|Leírás|Ennek a szabályzatnak a célja annak biztosítása, hogy a magas/kritikus biztonsági rések (az CVSS 3,0-et használva) a javítás elérhetővé tételének 180 napján belül legyenek kezelve.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Kézi|
|Érvényesítés|A hitelesített eszközökre vonatkozó sebezhetőségi jelentések küldésének és fogadásának folyamatáról a dokumentációban olvashat.|
|Források||


---
|Name|SecuredCore. encryption. TLS|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja a szükséges TLS-verziók és a titkosítási csomagok támogatásának ellenőrzése.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy az eszköz támogassa a 1,2-es minimális TLS-verziót, és támogatja a következő szükséges TLS titkosítási csomagokat.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Források| [TLS-támogatás a IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [TLS titkosítási csomagok a Windows 10 rendszerben](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Name|SecuredCore. Protection. SignedUpdates|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy a frissítéseket alá kell-e írni.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészleten keresztül érvényesíteni kívánt eszköz, amely biztosítja, hogy az operációs rendszer, az illesztőprogramok, az alkalmazás szoftverei, a kódtárak, a csomagok és a belső vezérlőprogram frissítései ne legyenek alkalmazva, ha megfelelően alá van írva és érvényesítve.
|Források||

---
|Name|SecuredCore. firmware. SecureBoot|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja az eszköz rendszerindítási integritásának ellenőrzése.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy a rendszer minden alkalommal érvényesítse a belső vezérlőprogram és a kernel aláírásait. <ul><li>UEFI: a biztonságos rendszerindítás engedélyezve van</li><li>Uboot: a hitelesített rendszerindítás engedélyezve van</li></ul>|
|Források||

---
|Name|SecuredCore. Protection. CodeIntegrity|
|:---|:---|
|Állapot|Kötelező|
|Leírás|Ennek a tesztnek a célja annak ellenőrzése, hogy a kód integritása elérhető-e az eszközön.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy a kód integritása engedélyezve legyen. </br> Windows: HVCI </br> Linux: DM-valóság és IMA|
|Források||

---
|Name|SecuredCore. Protection. NetworkServices|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy a hálózatról bejövő adatokat fogadó alkalmazások nem emelt szintű jogosultságokkal futnak-e.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz annak biztosítása érdekében, hogy a hálózati kapcsolatokat elfogadó szolgáltatások ne legyenek rendszer-vagy gyökérszintű jogosultságokkal.|
|Források||

---
|Name|SecuredCore. Protection. alaptervek|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy a rendszer megfelel-e az alapkonfiguráció biztonsági beállításainak.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy a Defender IOT rendszerkonfigurációi teljesítménymutatókat futtasson.|
|Források| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore. firmware. Protection|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak biztosítása, hogy az eszköz megfelelően enyhítse a belső vezérlőprogram biztonsági fenyegetéseket.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely megerősíti, hogy a rendszer a következő módszerek egyikével védi a belső vezérlőprogram biztonsági veszélyforrásait: <ul><li>DRTM + UEFI felügyeleti mód enyhítése</li><li>DRTM + UEFI felügyeleti mód megerősítése</li><li>Jóváhagyott FW, amely a SRTM + futásidejű belső vezérlőprogram megerősítését végzi</li></ul> |
|Források| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore. firmware. igazolás|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak biztosítása, hogy az eszköz képes legyen távolról tanúsítani az Microsoft Azure igazolási szolgáltatást.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy a platform rendszerindítási naplói és a rendszerindítási tevékenységek mérései összegyűjthetők és távolról hitelesítve legyenek az Microsoft Azure igazolási szolgáltatáshoz.|
|Források| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Name|SecuredCore. Hardware. MemoryProtection|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy a DMA nincs-e engedélyezve a külsőleg elérhető portokon.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Ha a DMA-kompatibilis külső portok léteznek az eszközön, az eszközkészlet ellenőrzi, hogy a IOMMU vagy a SMMU engedélyezve van-e és konfigurálva van-e a portok számára.|
|Források||

---
|Name|SecuredCore. Protection. debug|
|:---|:---|
|Állapot|Kötelező|
|Leírás|A teszt célja annak ellenőrzése, hogy a hibakeresési funkció le van-e tiltva az eszközön.|
|Cél rendelkezésre állása|2021|
|Érvényesség|Bármely eszköz|
|Operációs rendszer|Független|
|Érvényesítés típusa|Manuális/eszközök|
|Érvényesítés|Az eszközkészlettel érvényesíteni kívánt eszköz, amely biztosítja, hogy a hibakeresési funkció engedélyezze az engedélyezést.|
|További források||

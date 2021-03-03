---
title: Fejlett többlépcsős támadások észlelése az Azure Sentinelben
description: Az Azure Sentinel fúziós technológiájának használatával csökkentheti a riasztások fáradtságát, és a fejlett többlépcsős támadási észlelésen alapuló, gyakorlati incidenseket hozhat létre.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724285"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Fejlett többlépcsős támadások észlelése az Azure Sentinelben

> [!IMPORTANT]
> Bizonyos fúziós észlelések (lásd alább láthatók) jelenleg **előzetes** verzióban érhetők el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A gépi tanuláson alapuló fúziós technológiával az Azure Sentinel képes automatikusan észlelni a többlépcsős támadásokat a rendellenes viselkedések és a gyanús tevékenységek kombinációinak azonosításával, amelyek a kill-Chain különböző szakaszaiban figyelhetők meg. Ezen észlelések alapján az Azure Sentinel olyan incidenseket hoz létre, amelyeket egyébként nehéz lenne megfogni. Ezek az incidensek két vagy több riasztást vagy tevékenységet tartalmaznak. A tervezés szerint ezek az incidensek kis mennyiségű, magas megbízhatóságú és nagy súlyosságú.

A környezethez testre szabott, ez az észlelési technológia nem csak a hamis pozitív arányt csökkenti, de a támadásokat a korlátozott vagy hiányzó információk alapján is észleli.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Fejlett többfázisú támadásészlelés konfigurálása

Ez az észlelés alapértelmezés szerint engedélyezve van az Azure Sentinelben. Az alábbi utasításokat követve ellenőrizhető az állapot, vagy letiltható abban az esetben, ha alternatív megoldást használ a több riasztáson alapuló incidensek létrehozására:

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Navigáljon az **Azure Sentinel**  >  **Configuration**  >  **Analytics szolgáltatáshoz**

1. Válassza az **aktív szabályok** lehetőséget, majd a **Name (név** ) oszlopban keresse meg a **speciális többlépcsős támadás észlelése** elemet a **fúziós** szabály típusának szűrésével. Ellenőrizze az **állapot** oszlopot, és erősítse meg, hogy az észlelés engedélyezve van vagy le van tiltva.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. Az állapot módosításához válassza ki ezt a bejegyzést, és a **speciális többlépcsős támadás-észlelés** panelen válassza a **Szerkesztés** lehetőséget.

1. A **szabály létrehozása varázsló** panelen az állapot módosítása automatikusan ki lesz választva, ezért válassza a **Tovább: Áttekintés**, majd a **Mentés** lehetőséget. 

 Mivel a **fúziós** szabály típusa csak egy olyan szabályt tartalmaz, amely nem módosítható, a szabály sablonjai nem alkalmazhatók ennél a szabálytípusnél.

> [!NOTE]
> Az Azure Sentinel jelenleg 30 napos korábbi adatmennyiséget használ a gépi tanulási rendszerek betanításához. Ezeket az adategységeket a rendszer mindig a Microsoft kulcsaival titkosítja, ahogy az a Machine learning-folyamaton halad át. A betanítási adatait azonban nem titkosítja az [ügyfél által felügyelt kulcsokkal (CMK)](customer-managed-keys.md) , ha engedélyezte a CMK használatát az Azure Sentinel-munkaterületen. A fúzió letiltásához navigáljon az **Azure Sentinel** \> **Configuration** \> **Analytics \> aktív szabályok \> speciális többlépcsős támadás észlelése** elemre, és az **állapot** oszlopban válassza a **Letiltás lehetőséget.**

## <a name="attack-detection-scenarios"></a>Támadás észlelésének forgatókönyvei

A következő szakasz felsorolja a korrelációs forgatókönyvek típusait, a fenyegetések besorolása szerint csoportosítva, hogy az Azure Sentinel a fúziós technológia használatát vizsgálja.

A fentiekben leírtaknak megfelelően a Fusion a különböző termékekből származó több biztonsági riasztást használ a fejlett többlépcsős támadások észleléséhez, a sikeres fúziós észlelések **fúziós incidensként** jelennek meg az Azure Sentinel **incidensek** lapján, nem pedig **riasztásként** a **naplók** **biztonsági riasztások** táblájában.

A fúziós támadásokkal kapcsolatos észlelési forgatókönyvek engedélyezéséhez a felsorolt adatforrásokat a társított Azure Sentinel-adatösszekötők használatával kell bevenni.

> [!NOTE]
> Ezen forgatókönyvek némelyike **előzetes** verzióban érhető el. A rendszer ezt jelzi.

## <a name="compute-resource-abuse"></a>Számítási erőforrásokkal való visszaélés

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Több virtuális gépre vonatkozó létrehozási tevékenység a gyanús Azure Active Directory bejelentkezést követően
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hatás 

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), erőforrás-eltérítés (T1496)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy rendellenes számú virtuális gép lett létrehozva egyetlen munkamenetben egy Azure AD-fiókba való gyanús bejelentkezést követően. Ez a típusú riasztás nagy fokú megbízhatósággal jelzi, hogy a fúziós incidens leírásában feljegyzett fiókot feltörték, és az új virtuális gépek jogosulatlan célra való létrehozására használták, például kriptográfiai adatbányászati műveletek futtatásával. A gyanús Azure AD-bejelentkezési riasztások és a több virtuális gépre vonatkozó létrehozási tevékenységek riasztásai a következők:

- **Nem lehet olyan szokatlan helyre utazni, amely több VM-létrehozási tevékenységhez vezet**

- **Bejelentkezési esemény egy ismeretlen helyről, amely több VM-létrehozási tevékenységhez vezet**

- **Bejelentkezési esemény egy fertőzött eszközről, amely több VM-létrehozási tevékenységhez vezet**

- **Bejelentkezési esemény egy névtelen IP-címről, amely több VM-létrehozási tevékenységhez vezet**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal több VM-létrehozási tevékenységhez**

## <a name="credential-harvesting-new-threat-classification"></a>Hitelesítő adatok betakarítása (új veszélyforrások besorolása)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Rosszindulatú hitelesítő adatok ellopására szolgáló eszköz végrehajtása a gyanús bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hitelesítő adatokhoz való hozzáférés

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), operációs rendszer hitelesítő adatainak kiírása (T1003)

**Adatösszekötő-források:** Azure Active Directory Identity Protection, a Microsoft Defender for Endpoint

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy egy ismert hitelesítő adatok ellopására szolgáló eszköz egy gyanús Azure AD-bejelentkezés után lett végrehajtva. Ez magas megbízhatóságot biztosít arra vonatkozóan, hogy a riasztás leírásában feltüntetett felhasználói fiók biztonsága megsérült, és lehet, hogy sikeresen használt egy olyan eszközt, mint a **Mimikatz** , hogy begyűjtse a hitelesítő adatokat, például a kulcsokat, a szöveges jelszavakat és/vagy jelszavak kivonatait a rendszerből. A begyűjtött hitelesítő adatok lehetővé tehetik a támadók számára a bizalmas adatok elérését, a jogosultságok kiterjesztését és/vagy a hálózaton keresztüli későbbi áthelyezését. A gyanús Azure AD-bejelentkezési riasztások a rosszindulatú hitelesítő adatok ellopása eszközzel kapcsolatos riasztásai a következők:

- **Nem lehet utazni a rosszindulatú hitelesítő adatok ellopása eszköz végrehajtásához vezető atipikus helyszínekre**

- **Bejelentkezési esemény ismeretlen helyről, amely rosszindulatú hitelesítő adatok ellopására szolgáló eszköz végrehajtásához vezet**

- **Bejelentkezési esemény egy fertőzött eszközről, amely rosszindulatú hitelesítőadat-lopási eszköz futtatását eredményezi**

- **Bejelentkezési esemény egy olyan névtelen IP-címről, amely rosszindulatú hitelesítő adatok ellopására szolgáló eszköz végrehajtásához vezet**

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek rosszindulatú hitelesítő adatok ellopására szolgáló eszköz végrehajtásához vezetnek**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Gyanús hitelesítő adatok ellopása tevékenység a gyanús bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hitelesítő adatokhoz való hozzáférés

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), hitelesítő adatok a jelszavak tárolói (T1555), az operációs rendszer hitelesítő adatainak kiírása (T1003)

**Adatösszekötő-források:** Azure Active Directory Identity Protection, a Microsoft Defender for Endpoint

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy a hitelesítő adatok ellopásához kapcsolódó tevékenység a gyanús Azure AD-bejelentkezést követően történt. Ez nagy megbízhatósággal jelzi, hogy a riasztás leírásában feltüntetett felhasználói fiók biztonsága sérült, és a hitelesítő adatok (például kulcsok, egyszerű szöveges jelszavak, jelszó-kivonatok stb.) lopására szolgál. A lopott hitelesítő adatokkal a támadók hozzáférhetnek a bizalmas adatokhoz, megadhatják a jogosultságokat, és/vagy oldalirányban áthelyezhetik azokat a hálózaton keresztül. A gyanús Azure AD bejelentkezési riasztások és a hitelesítő adatok ellopása tevékenység riasztásai a következők:

- **Nem lehet utazni a gyanús hitelesítő adatok ellopását eredményező atipikus helyszínekre**

- **Bejelentkezési esemény egy ismeretlen helyről, amely a hitelesítő adatok ellopására irányuló tevékenységet eredményez**

- **Bejelentkezési esemény egy fertőzött eszközről, amely gyanús hitelesítő adatok ellopásának tevékenységét eredményezi**

- **Bejelentkezési esemény egy névtelen IP-címről, amely gyanús hitelesítő adatok ellopására irányuló tevékenységet eredményez**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal, ami gyanús hitelesítő adatok ellopását eredményezi.**

## <a name="crypto-mining-new-threat-classification"></a>Titkosítás – adatbányászat (új veszélyforrások besorolása)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Kriptográfia – a gyanús bejelentkezést követő adatbányászati tevékenység

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hitelesítő adatokhoz való hozzáférés

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), erőforrás-eltérítés (T1496)

**Adatösszekötő-források:** Azure Active Directory Identity Protection, Azure Defender (Azure Security Center)

**Leírás:** Az ilyen típusú fúziós incidensek olyan kriptográfiai adatbányászati tevékenységeket jeleznek, amelyek egy Azure AD-fiókba gyanús bejelentkezéssel kapcsolatosak. Ez nagy megbízhatóságot jelent, hogy a riasztás leírásában feltüntetett felhasználói fiók biztonsága sérült, és a környezet erőforrásainak az enyém titkosítási pénznemre való eltérítésére szolgál. Ez megéhezheti a számítási teljesítmény erőforrásait, és/vagy jelentősen nagyobb, mint várt Felhőbeli használati számlákat eredményezhet. A gyanús Azure AD bejelentkezési riasztások és a kriptográfiai adatbányászati tevékenység riasztása a következők:  

- **Nem lehet utazni a titkosítási tevékenységekhez vezető atipikus helyszínekre**

- **Bejelentkezési esemény egy ismeretlen helyről, amely kriptográfiai adatbányászati tevékenységet eredményez**

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely kriptográfiai adatbányászati tevékenységet eredményez**

- **Bejelentkezési esemény egy névtelen IP-címről, amely kriptográfiai adatbányászati tevékenységet eredményez**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal a kriptográfiai adatbányászati tevékenységhez**

## <a name="data-exfiltration"></a>Adatkiszivárgás

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Office 365 postaláda kiszűrése gyanús Azure AD-bejelentkezés után

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, kiszűrése, gyűjtemény

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), E-mail gyűjtemény (T1114), automatizált kiszűrése (T1020)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy egy gyanús bejövő továbbítási szabályt állítottak be egy felhasználó postaládájába egy, az Azure AD-fiókba gyanús bejelentkezést követően. Ez a jelzés magas megbízhatóságot biztosít ahhoz, hogy a felhasználói fiók (a fúziós incidens leírásában szerepel) biztonságban legyen, és hogy a rendszer a szervezet hálózatáról származó adatokat exfiltrate, ha a postaláda-továbbítási szabályt a valódi felhasználó tudta nélkül engedélyezte. A gyanús Azure AD-bejelentkezési riasztások és az Office 365 postaláda-kiszűrése riasztásai a következők:

- **Az Office 365 postaláda-kiszűrése vezető szokatlan helyre nem lehet utazni**

- **Bejelentkezési esemény egy ismeretlen helyről, amely az Office 365 postaláda-kiszűrése vezet**

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely az Office 365 postaláda-kiszűrése vezet**

- **Bejelentkezési esemény egy névtelen IP-címről, amely az Office 365 postaláda-kiszűrése vezet**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal az Office 365 postaláda kiszűrése**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>A tömeges fájl letöltése a gyanús Azure AD-bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, kiszűrése

**Mitre ATT&CK technikák:** Érvényes fiók (T1078)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy a felhasználó rendellenes számú fájlt töltött le egy, az Azure AD-fiókba gyanús bejelentkezést követően. Ez a jelzés magas megbízhatóságot biztosít ahhoz, hogy a fúziós incidens leírásában feljegyzett fiókot feltörték, és a rendszer felhasználta az adatok exfiltrate a szervezet hálózatáról. A gyanús Azure AD-bejelentkezések riasztásai a tömeges fájlok letöltési riasztásával:  

- **Nem lehet utazni a tömeges fájl letöltését eredményező szokatlan helyre**

- **Bejelentkezési esemény egy ismeretlen helyről, amely a Mass file letöltéséhez vezet**

- **Bejelentkezési esemény egy fertőzött eszközről, amely a Mass file letöltését eredményezi**

- **Bejelentkezési esemény a tömeges fájl letöltését eredményező névtelen IP-címről**

- **Bejelentkezési esemény a felhasználótól a fájlok letöltéséhez szükséges kiszivárgott hitelesítő adatokkal**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Tömeges fájlmegosztás a gyanús Azure AD-bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, kiszűrése

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), kiszűrése over Web Service (T1567)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy egy adott küszöbértéket meghaladó számú fájl megosztva lett másokkal egy Azure AD-fiókba gyanús bejelentkezést követően. Ez a jelzés magas megbízhatóságot biztosít ahhoz, hogy a fúziós incidensek leírásában feljegyzett fiókot feltörték, és a rendszer a szervezete hálózatáról származó adatokat exfiltrate, például dokumentumokat, táblázatokat stb., jogosulatlan felhasználókkal, rosszindulatú célra. A gyanús Azure AD bejelentkezési riasztások és a tömeges fájlmegosztás riasztásai a következők:  

- **Nem lehet olyan szokatlan helyre utazni, amely a tömeges fájlmegosztást eredményezi**

- **Bejelentkezési esemény egy ismeretlen helyről, amely a tömeges fájlmegosztást eredményezi**

- **Bejelentkezési esemény a tömeges fájlmegosztást eredményező fertőzött eszközről**

- **Bejelentkezési esemény egy tömeges fájlmegosztást eredményező névtelen IP-címről**

- **Bejelentkezési esemény a felhasználótól a tömeges fájlmegosztást eredményező kiszivárgott hitelesítő adatokkal**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Gyanús beérkezett fájlok kezelésére vonatkozó szabályok a gyanús Azure AD-bejelentkezést követően
Ez a forgatókönyv két fenyegetési besoroláshoz tartozik a listában: az **kiszűrése** és az **oldalirányú mozgást**. Az érthetőség kedvéért mindkét szakaszban megjelenik.

Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, oldalirányú mozgás, kiszűrése

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), belső célzott adathalászat (T1534)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy az Azure AD-fiókba gyanús bejelentkezést követően a rendszer rendellenes beérkezett fájlok szabályait állította be a felhasználó postaládájába. Ez nagy megbízhatósággal jelzi, hogy a fúziós incidens leírásában feljegyzett fiók biztonsága megsérült, és a felhasználó e-mail-címére vonatkozó szabályok rosszindulatú célokra való kezelésére használták. Lehetséges, hogy egy támadó megpróbálta exfiltrate az adatok a szervezet hálózatáról. Azt is megteheti, hogy a támadó e-maileket próbál előállítani a szervezeten belülről (a külső forrásokból érkező e-mailekre irányuló adathalászat-észlelési mechanizmusok megkerülése) abból a célból, hogy a későbbiekben a további felhasználói és/vagy emelt szintű fiókokhoz való hozzáféréshez hozzáférjen. A gyanús Azure AD-bejelentkezési riasztások a gyanús beérkezett fájlok kezelésére vonatkozó szabályainak riasztásai a következők:  

- **Nem lehet olyan szokatlan helyre utazni, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény egy ismeretlen helyről, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény olyan fertőzött eszközről, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény olyan névtelen IP-címről, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek gyanús beérkezett fájlok manipulációs szabályához vezetnek**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Több Power BI jelentés-megosztási tevékenység a gyanús Azure AD-bejelentkezést követően 
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, kiszűrése 

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), kiszűrése over Web Service (T1567)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy rendellenes számú Power BI jelentést osztottak meg egyetlen munkamenetben az Azure AD-fiókba való gyanús bejelentkezést követően. Ez a jelzés magas megbízhatóságot biztosít ahhoz, hogy a fúziós incidens leírásában feljegyzett fiókot feltörték, és a rendszer a szervezet hálózatáról származó adatok exfiltrate a jogosulatlan felhasználóktól származó Power BI jelentések rosszindulatú célból történő megosztásával. A gyanús Azure AD-bejelentkezési riasztások és a több Power BI jelentéskészítő tevékenységekkel kapcsolatos permutációk a következők:  

- **Nem lehet olyan szokatlan helyre utazni, amely több Power BI jelentéskészítési tevékenységhez vezet**

- **Bejelentkezési esemény ismeretlen helyről, amely több Power BI jelentéskészítési tevékenységhez vezet**

- **Bejelentkezési esemény egy fertőzött eszközről, amely több Power BI jelentéskészítési tevékenységhez vezet**

- **Bejelentkezési esemény egy névtelen IP-címről, amely több Power BI jelentéskészítési tevékenységhez vezet**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal, amelyek több Power BI jelentéskészítési tevékenységhez vezetnek**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Gyanús Power BI jelentés megosztása a gyanús Azure AD-bejelentkezés után
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, kiszűrése 

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), kiszűrése over Web Service (T1567)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy egy gyanús Power BI jelentéskészítő tevékenység gyanús bejelentkezést követően történt egy Azure AD-fiókban. A megosztási tevékenység gyanúsként lett azonosítva, mert az Power BI jelentés természetes nyelvi feldolgozással azonosított bizalmas adatokat foglalt magukban, és mivel egy külső e-mail-címmel lett megosztva, amelyet a weben közzétettek, vagy amelyeket egy külső előfizetett e-mail-címre küldtek el. Ez a riasztás nagy megbízhatósággal jelzi, hogy a fúziós incidensek leírásában feljegyzett fiók biztonsága sérül, és a szervezet bizalmas adatainak exfiltrate való megosztásával a jogosulatlan felhasználókkal való Power BI jelentések rosszindulatú céllal való megosztása történik. A gyanús Azure AD bejelentkezési riasztások a gyanús Power BI jelentés megosztásával kapcsolatos permutációi a következők:  

- **Nem lehet olyan szokatlan helyre utazni, amely gyanús Power BI jelentés megosztására vezet**

- **Bejelentkezési esemény ismeretlen helyről, amely gyanús Power BI jelentés megosztására vezet**

- **Bejelentkezési esemény olyan fertőzött eszközről, amely gyanús Power BI jelentés megosztásához vezet**

- **Bejelentkezési esemény olyan névtelen IP-címről, amely gyanús Power BI jelentés megosztásához vezet**

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek gyanús Power BI jelentés megosztásához vezetnek**

## <a name="data-destruction"></a>Adatmegsemmisítés

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>A tömeges fájl törlése a gyanús Azure AD-bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hatás

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), adatmegsemmisítés (T1485)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy rendellenes számú egyedi fájlt töröltek egy Azure AD-fiókba való gyanús bejelentkezés után. Ez azt jelzi, hogy a fúziós incidens leírásában feljegyzett fiók biztonsága sérült, és az adatok rosszindulatú céllal történő megsemmisítésére használták. A gyanús Azure AD-bejelentkezések riasztásai a Mass file törlési riasztással a következők:  

- **Nem lehet olyan szokatlan helyre utazni, amely a tömeges fájl törlését eredményezi**

- **Bejelentkezési esemény egy ismeretlen helyről, amely a tömeges fájlok törlését eredményezi**

- **Bejelentkezési esemény egy fertőzött eszközről, amely a tömeges fájl törlését eredményezi**

- **Bejelentkezési esemény a tömeges fájlok törlését eredményező névtelen IP-címről**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal, amelyek a tömeges fájlok törlését eredményezik**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Gyanús e-mail törlési tevékenység gyanús Azure AD-bejelentkezést követően
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hatás 

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), adatmegsemmisítés (T1485)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy rendellenes számú e-mailt töröltek egyetlen munkamenetben az Azure AD-fiókba való gyanús bejelentkezést követően. Ez azt jelzi, hogy a fúziós incidens leírásában feljegyzett fiók biztonsága megsérült, és az adatok rosszindulatú célra való megsemmisítésére használták, például a szervezet károsodásával vagy a levélszeméttel kapcsolatos e-mail-tevékenységek elrejtésével. A gyanús Azure AD bejelentkezési riasztások és a gyanús e-mail törlési tevékenység riasztásai a következők:   

- **Nem lehet olyan szokatlan helyre utazni, amely gyanús e-mail törlési tevékenységet eredményez**

- **Bejelentkezési esemény egy ismeretlen helyről, amely gyanús e-mail törlési tevékenységet eredményez**

- **Bejelentkezési esemény egy fertőzött eszközről, amely gyanús e-mail törlési tevékenységet eredményez**

- **Bejelentkezési esemény olyan névtelen IP-címről, amely gyanús e-mail törlési tevékenységet eredményez**

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek gyanús e-mail törlési tevékenységet eredményeznek**

## <a name="denial-of-service"></a>Szolgáltatásmegtagadás

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Több virtuális gép törlése tevékenységek a gyanús Azure AD-bejelentkezés után
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hatás

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), végpont-megtagadási szolgáltatás (T1499)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy rendellenes számú virtuális gépet töröltek egyetlen munkamenetben egy Azure AD-fiókba való gyanús bejelentkezést követően. Ez a jelzés magas megbízhatóságot biztosít ahhoz, hogy a fúziós incidens leírásában feljegyzett fiókot feltörték, és a szervezet felhőalapú környezetének megszakadására vagy megsemmisítésére használták. A gyanús Azure AD-bejelentkezési riasztások és a több virtuális gépre vonatkozó törlési tevékenységek riasztása a következők:  

- **Nem lehet olyan szokatlan helyre utazni, amely több virtuális gép törlési tevékenységét eredményezi**

- **Bejelentkezési esemény egy ismeretlen helyről, amely több virtuális gép törlési tevékenységét eredményezi**

- **Bejelentkezési esemény egy fertőzött eszközről, amely több virtuális gép törlési tevékenységét eredményezi**

- **Bejelentkezési esemény egy névtelen IP-címről, amely több virtuális gép törlési tevékenységét eredményezi**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal, amelyek több virtuális gép törlési tevékenységét eredményezik**

## <a name="lateral-movement"></a>Oldalirányú mozgás

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Office 365 megszemélyesítés a gyanús Azure AD-bejelentkezés után

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, oldalirányú mozgás

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), belső célzott adathalászat (T1534)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy rendellenes számú megszemélyesítési művelet történt egy Azure AD-fiókból érkező gyanús bejelentkezés után. Bizonyos szoftverekben lehetőség van arra, hogy a felhasználók megszemélyesítsék más felhasználókat. Az e-mail szolgáltatások például lehetővé teszik, hogy a felhasználók más felhasználók számára is engedélyezzék az e-mailek küldését a nevükben. Ez a riasztás nagyobb megbízhatóságot jelez, hogy a fúziós incidens leírásában feljegyzett fiók biztonsága sérül, és a megszemélyesítési tevékenységek rosszindulatú célra való elvégzésére szolgál, például adathalászó e-mailek küldése a kártevők terjesztéséhez vagy az oldalirányú mozgáshoz. A gyanús Azure AD-bejelentkezési riasztások és az Office 365 megszemélyesítési riasztások a következők:  

- **Nem lehet utazni egy szokatlan helyre, amely az Office 365 megszemélyesítést eredményezi**

- **Bejelentkezési esemény egy ismeretlen helyről, amely az Office 365 megszemélyesítéshez vezet**

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely az Office 365 megszemélyesítést vezeti**

- **Bejelentkezési esemény egy névtelen IP-címről, amely az Office 365 megszemélyesítést vezeti**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal az Office 365 megszemélyesítéshez vezető módon**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Gyanús beérkezett fájlok kezelésére vonatkozó szabályok a gyanús Azure AD-bejelentkezést követően
Ez a forgatókönyv két fenyegetési besoroláshoz tartozik a következő listában: **oldalirányú mozgás** és **az adatkiszűrése**. Az érthetőség kedvéért mindkét szakaszban megjelenik.

Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, oldalirányú mozgás, kiszűrése

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), belső célzott adathalászat (T1534), automatizált kiszűrése (T1020)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy az Azure AD-fiókba gyanús bejelentkezést követően a rendszer rendellenes beérkezett fájlok szabályait állította be a felhasználó postaládájába. Ez a jelzés magas megbízhatóságot biztosít ahhoz, hogy a fúziós incidensek leírásában feljegyzett fiók biztonsága megsérül, és a felhasználó e-mailes beérkező szabályainak rosszindulatú célokra való kezelésére használták. Lehetséges, hogy egy támadó megpróbálta exfiltrate az adatok a szervezet hálózatáról. Azt is megteheti, hogy a támadó e-maileket próbál előállítani a szervezeten belülről (a külső forrásokból érkező e-mailekre irányuló adathalászat-észlelési mechanizmusok megkerülése) abból a célból, hogy a későbbiekben a további felhasználói és/vagy emelt szintű fiókokhoz való hozzáféréshez hozzáférjen. A gyanús Azure AD-bejelentkezési riasztások a gyanús beérkezett fájlok kezelésére vonatkozó szabályainak riasztásai a következők:

- **Nem lehet olyan szokatlan helyre utazni, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény egy ismeretlen helyről, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény olyan fertőzött eszközről, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény olyan névtelen IP-címről, amely gyanús beérkezett fájlok manipulációs szabályához vezet**

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek gyanús beérkezett fájlok manipulációs szabályához vezetnek**

## <a name="malicious-administrative-activity"></a>Rosszindulatú felügyeleti tevékenység

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Gyanús Felhőbeli alkalmazás rendszergazdai tevékenysége gyanús Azure AD-bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, adatmegőrzés, védelmi adócsalás, oldalirányú mozgás, gyűjtés, kiszűrése és hatás

**Mitre ATT&CK technikák:** N/A

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy rendellenes számú rendszergazdai tevékenységet hajtottak végre egy adott munkamenetben egy gyanús Azure AD-bejelentkezést követően ugyanabból a fiókból. Ez azt jelzi, hogy a fúziós incidens leírásában feljegyzett fiók biztonsága megsérült, és a rendszer felhasználta a rosszindulatú szándékú jogosulatlan adminisztratív műveletek elvégzésére. Ez azt is jelenti, hogy egy rendszergazdai jogosultságokkal rendelkező fiók biztonsága sérült. A gyanús Azure AD bejelentkezési riasztások és a gyanús Cloud app felügyeleti tevékenység riasztásai a következők:  

- **Nem lehet olyan szokatlan helyre utazni, amely gyanús Cloud app felügyeleti tevékenységhez vezet**

- **Bejelentkezési esemény olyan ismeretlen helyről, amely gyanús Cloud app felügyeleti tevékenységhez vezet**

- **Bejelentkezési esemény olyan fertőzött eszközről, amely gyanús Cloud app rendszergazdai tevékenységet vezet**

- **Bejelentkezési esemény olyan névtelen IP-címről, amely gyanús Cloud app felügyeleti tevékenységhez vezet**

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek gyanús Cloud app felügyeleti tevékenységet vezetnek**

## <a name="malicious-execution-with-legitimate-process"></a>Rosszindulatú végrehajtás legitim folyamattal

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A PowerShell gyanús hálózati kapcsolatban tartott, majd a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom következik.
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Végrehajtási

**Mitre ATT&CK technikák:** Parancs-és parancsfájl-értelmező (T1059)

**Adatösszekötő-források:** Microsoft Defender for Endpoint (korábban Microsoft Defender komplex veszélyforrások elleni védelem vagy MDATP), Palo Alto Networks 

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy egy PowerShell-paranccsal kezdeményezték a kimenő kapcsolódási kérelmeket, és ezt követően a rendszer rendellenes bejövő tevékenységeket észlelt a Palo Alto Networks tűzfalon. Ez azt jelzi, hogy egy támadó valószínűleg hozzáfért a hálózathoz, és rosszindulatú műveleteket próbál végrehajtani. Az ezt a mintát követő PowerShell-kapcsolati kísérletek jelezhetik a kártevők parancs-és vezérlési tevékenységeket, a további kártevők letöltésére vonatkozó kéréseket, illetve a távoli interaktív hozzáférést létrehozó támadót. Ahogy az összes "élő a szárazföldön" támadás is, ez a tevékenység a PowerShell jogos használata lehet. Azonban a PowerShell-parancs végrehajtása, amelyet a gyanús bejövő tűzfal tevékenység követ, növeli a PowerShell rosszindulatú módon való használatának megbízhatóságát, és további vizsgálatot kell végeznie. A Palo Alto-naplókban az Azure Sentinel a [veszélyforrások naplóira](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)koncentrál, és a forgalom gyanúsnak minősül, ha a fenyegetések engedélyezettek (gyanús adatok, fájlok, árvizek, csomagok, vizsgálatok, kémprogramok, URL-címek, vírusok, sebezhetőségek, Futótűz-vírusok, erdőtüzek). A további riasztási részletekért tekintse meg a fúziós incidens leírásában szereplő, a [fenyegetés/tartalom típusnak](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) megfelelő Palo Alto Threat log hivatkozást is.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Gyanús távoli WMI-végrehajtás, amelyet a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom követ
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Végrehajtás, felderítés

**Mitre ATT&CK technikák:** Windows Management Instrumentation (T1047)

**Adatösszekötő-források:** Microsoft Defender for Endpoint (korábban MDATP), Palo Alto Networks 

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy a Windows Management Interface (WMI) parancsok távolról lettek végrehajtva egy rendszeren, és ezt követően a rendszer gyanús bejövő tevékenységet észlelt a Palo Alto Networks tűzfalon. Ez azt jelzi, hogy egy támadó elérheti a hálózatát, és megpróbálkozik a későbbi áthelyezéssel, a jogosultságok kiterjesztésével és/vagy a kártékony hasznos adatok végrehajtásával. Ahogy az összes "a szárazföldön él" támadásokkal szemben, ez a tevékenység a WMI jogos használata lehet. Azonban a távoli WMI-parancs végrehajtása, amelyet a gyanús bejövő tűzfalszabályok követnek, növeli a WMI rosszindulatú módon való használatának megbízhatóságát, és további vizsgálatot kell végeznie. A Palo Alto-naplókban az Azure Sentinel a [veszélyforrások naplóira](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)koncentrál, és a forgalom gyanúsnak minősül, ha a fenyegetések engedélyezettek (gyanús adatok, fájlok, árvizek, csomagok, vizsgálatok, kémprogramok, URL-címek, vírusok, sebezhetőségek, Futótűz-vírusok, erdőtüzek). A további riasztási részletekért tekintse meg a fúziós incidens leírásában szereplő, a [fenyegetés/tartalom típusnak](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) megfelelő Palo Alto Threat log hivatkozást is.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Gyanús PowerShell-parancssor a gyanús bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, végrehajtás

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), parancs és parancsfájl-értelmező (T1059)

**Adatösszekötő-források:** Azure Active Directory Identity Protection, Microsoft Defender for Endpoint (korábban MDATP)

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy a felhasználó az Azure AD-fiókba gyanús bejelentkezést követően potenciálisan rosszindulatú PowerShell-parancsokat hajtott végre. Ez nagy megbízhatósággal jelzi, hogy a riasztás leírásában feljegyzett fiók biztonsága sérül, és további kártékony műveletekre került sor. A támadók gyakran használják a PowerShellt arra, hogy a lemezen lévő összetevők elhagyása nélkül hajtsák végre a kártékony adattartalmakat, így elkerülhetők a lemezes biztonsági mechanizmusok, például a víruskeresők észlelése. A gyanús Azure AD bejelentkezési riasztások és a gyanús PowerShell-parancsok riasztásai a következők:

- **A gyanús PowerShell-parancssorhoz vezető atipikus helyszínekre nem lehet utazni**

- **Bejelentkezési esemény olyan ismeretlen helyről, amely gyanús PowerShell-parancssorhoz vezet**

- **Bejelentkezési esemény egy gyanús PowerShell-parancssort eredményező fertőzött eszközről**

- **Bejelentkezési esemény egy olyan névtelen IP-címről, amely gyanús PowerShell-parancssorhoz vezet**

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal a gyanús PowerShell-parancssorhoz**

## <a name="malware-c2-or-download"></a>Kártevő C2 vagy Letöltés

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Hálózati kérelem a TOR névtelenítésével szolgáltatáshoz, amelyet a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom követ.
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Parancs és vezérlés

**Mitre ATT&CK technikák:** Titkosított csatorna (T1573), proxy (T1090)

**Adatösszekötő-források:** Microsoft Defender for Endpoint (korábban MDATP), Palo Alto Networks 

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy kimenő kapcsolódási kérelem érkezett a TOR névtelenítésével szolgáltatáshoz, és ezt követően a rendszer rendellenes bejövő tevékenységeket észlelt a Palo Alto Networks tűzfalon. Ez azt jelzi, hogy egy támadó valószínűleg hozzáfért a hálózathoz, és megpróbálja elrejteni a műveleteit és szándékait. Az ezzel a mintázattal rendelkező TOR-hálózattal létesített kapcsolatok a kártevők és a vezérlési tevékenységek, valamint a további kártevők letöltésére vonatkozó kérések, illetve a távoli interaktív hozzáférést létrehozó támadók. A Palo Alto-naplókban az Azure Sentinel a [veszélyforrások naplóira](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)koncentrál, és a forgalom gyanúsnak minősül, ha a fenyegetések engedélyezettek (gyanús adatok, fájlok, árvizek, csomagok, vizsgálatok, kémprogramok, URL-címek, vírusok, sebezhetőségek, Futótűz-vírusok, erdőtüzek). A további riasztási részletekért tekintse meg a fúziós incidens leírásában szereplő, a [fenyegetés/tartalom típusnak](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) megfelelő Palo Alto Threat log hivatkozást is.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Kimenő kapcsolat az IP-címmel a jogosulatlan hozzáférési kísérletek előzményeivel, amelyet a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom követ.
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Parancs és vezérlés

**Mitre ATT&CK technikák:** Nem alkalmazható

**Adatösszekötő-források:** Microsoft Defender for Endpoint (korábban MDATP), Palo Alto Networks 

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy egy, a jogosulatlan hozzáférési kísérletek előzményeivel rendelkező IP-címhez való kimenő kapcsolat létrejött, és ezt követően a rendszer rendellenes tevékenységeket észlelt a Palo Alto Networks tűzfalon. Ez azt jelzi, hogy egy támadó valószínűleg hozzáfért a hálózathoz. Az ezt a mintát követő kapcsolódási kísérletek a kártevők és a vezérlési tevékenységek, valamint a további kártevők letöltésére vonatkozó kérések, illetve a távoli interaktív hozzáférést létrehozó támadók számára is megadhatók. A Palo Alto-naplókban az Azure Sentinel a [veszélyforrások naplóira](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)koncentrál, és a forgalom gyanúsnak minősül, ha a fenyegetések engedélyezettek (gyanús adatok, fájlok, árvizek, csomagok, vizsgálatok, kémprogramok, URL-címek, vírusok, sebezhetőségek, Futótűz-vírusok, erdőtüzek). A további riasztási részletekért tekintse meg a fúziós incidens leírásában szereplő, a [fenyegetés/tartalom típusnak](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) megfelelő Palo Alto Threat log hivatkozást is.

## <a name="ransomware"></a>Zsarolóprogramok

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Ransomware-végrehajtás a gyanús Azure AD-bejelentkezést követően

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, hatás

**Mitre ATT&CK technikák:** Érvényes fiók (T1078), a hatáshoz titkosított adatforgalom (T1486)

**Adatösszekötő-források:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy a rendellenes felhasználói viselkedés azt jelzi, hogy az Azure AD-fiókba gyanús bejelentkezést követően ransomware támadás észlelhető. Ez a jelzés magas megbízhatóságot biztosít ahhoz, hogy a fúziós incidens leírásában feljegyzett fiókot feltörték, és az adatok titkosítására használták az adatok tulajdonosának érvényesítése vagy az adatok tulajdonosának hozzáférésének megtagadása céljából. A gyanús Azure AD bejelentkezési riasztások és a ransomware-végrehajtási riasztások a következők:  

- **Nem lehet utazni a Cloud app ransomware vezető szokatlan helyre**

- **Bejelentkezési esemény ismeretlen helyről, amely a Cloud app ransomware vezető**

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely ransomware vezet a Cloud app szolgáltatásban**

- **Bejelentkezési esemény egy névtelen IP-címről, amely a Cloud app ransomware vezet**

- **Bejelentkezési esemény felhasználótól kiszivárgott hitelesítő adatokkal, amelyek a Cloud app ransomware vezetnek**

## <a name="remote-exploitation"></a>Távoli kiaknázás

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A támadási keretrendszer feltételezett használata, amelyet a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom követ.
Ez a forgatókönyv jelenleg **előzetes** verzióban érhető el.

**Mitre ATT&CK-taktika:** Kezdeti hozzáférés, végrehajtás, oldalirányú mozgás, jogosultság-eszkaláció

**Mitre ATT&CK technikák:** Public-Facing alkalmazás (T1190) kiaknázása, az ügyfél-végrehajtás (T1203) kiaknázása, a távoli szolgáltatások kiaknázása (T1210), a jogosultságok eszkalációjának kiaknázása (T1068)

**Adatösszekötő-források:** Microsoft Defender for Endpoint (korábban MDATP), Palo Alto Networks 

**Leírás:** Az ilyen típusú fúziós incidensek azt jelzik, hogy nem szabványos, a támadási keretrendszerek (például Metasploit) használatára hasonlítanak a protokollok, és ezt követően a rendszer gyanús bejövő tevékenységet észlelt a Palo Alto Networks tűzfalon. Ez lehet az első jele annak, hogy egy támadó kihasználta a szolgáltatást, hogy hozzáférjen a hálózati erőforrásokhoz, vagy hogy a támadó már elérte a hozzáférést, és megkísérli az elérhető rendszerek/szolgáltatások további kiaknázását a későbbiekben és/vagy a jogosultságok kiterjesztésével. A Palo Alto-naplókban az Azure Sentinel a [veszélyforrások naplóira](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)koncentrál, és a forgalom gyanúsnak minősül, ha a fenyegetések engedélyezettek (gyanús adatok, fájlok, árvizek, csomagok, vizsgálatok, kémprogramok, URL-címek, vírusok, sebezhetőségek, Futótűz-vírusok, erdőtüzek). A további riasztási részletekért tekintse meg a fúziós incidens leírásában szereplő, a [fenyegetés/tartalom típusnak](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) megfelelő Palo Alto Threat log hivatkozást is.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a fejlett többlépcsős támadások észlelését, az alábbi rövid útmutatóból megtudhatja, hogyan tekintheti meg az adatait és a potenciális fenyegetéseket: Ismerkedés [Az Azure Sentinel szolgáltatással](quickstart-get-visibility.md).

Ha készen áll az Ön számára létrehozott incidensek vizsgálatára, tekintse meg a következő oktatóanyagot: [incidensek vizsgálata az Azure Sentinel](tutorial-investigate-cases.md)használatával.


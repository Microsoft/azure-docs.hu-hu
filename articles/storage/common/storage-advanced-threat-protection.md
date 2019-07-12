---
title: Advanced Threat Protection for Azure Storage
description: Állítsa be az Azure Storage komplex veszélyforrások elleni védelem fióktevékenység rendellenességek észlelése és csupán a vélhetően kárt okozó kísérleteket hozzáférjen a fiókjához.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 8cea4b3fb78f3430fdd92e40552d687501af4be8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621963"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection for Azure Storage

Az Azure Storage-hoz nyújtott komplex veszélyforrások elleni védelem egy további biztonságiintelligencia-réteget ad, amely észleli a tárfiókok elérésére és felhasználására tett szokatlan és feltehetően ártalmas kísérleteket. A védelmi réteget teszi cím fenyegetések szakértői biztonsági szakértelem vagy biztonság monitorozási rendszerek felügyelete nélkül. 

Biztonsági riasztások aktiválódnak rendellenességeket a tevékenység esetén.  Ezek a biztonsági riasztások integrálva vannak az [az Azure Security Center](https://azure.microsoft.com/services/security-center/), és az előfizetés adminisztrátorainak, gyanús tevékenységeket és a problémák kivizsgálásához és elhárításához fenyegetésekkel kapcsolatos javaslatok részleteit tartalmazó e-mailen keresztül is kapnak.

> [!NOTE]
> * Komplex veszélyforrások elleni védelem az Azure Storage jelenleg csak az a Blob storage.
> * A díjszabás részleteiért, beleértve a 30 napos ingyenes próbaverziót, tekintse meg a [az Azure Security Center díjszabását ismertető lapon]( https://azure.microsoft.com/pricing/details/security-center/).
> * Az Azure storage szolgáltatás ATP jelenleg nem érhető el az Azure government és szuverén felhő-régiók.

Komplex veszélyforrások elleni védelem az Azure Storage-diagnosztikai naplók, olvasási, írási és törlési kérelmek a Blob storage, a fenyegetésészlelés fogadnak. A komplex veszélyforrások elleni védelem riasztások vizsgálatához megtekintheti a Storage Analytics-naplózás használata a kapcsolódó tárolási tevékenység. További információkért lásd: hogyan [Storage Analytics naplózási](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Állítsa be a komplex veszélyforrások elleni védelem 

### <a name="using-the-portal"></a>A portál használata

1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com/).

2. Keresse meg a védeni kívánt Azure Storage-fiók-konfigurációs oldalához. Az a **beállítások** lapon jelölje be **komplex veszélyforrások elleni védelem**.

3. Az a **komplex veszélyforrások elleni védelem** konfigurálása panel
    * Kapcsolja be **ON** speciális *veszélyforrások elleni védelem*
    * Kattintson a **mentése** mentse az új vagy frissített komplex veszélyforrások elleni védelem szabályzatot. (A képen árak például csak célokat.)

![Azure Storage komplex veszélyforrások elleni védelem bekapcsolása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Az Azure Security Center használatával
A Standard szintű szolgáltatásra való előfizetéskor az Azure Security Centerben, komplex veszélyforrások elleni védelem a van beállítva a storage-fiókok. További információ: [a Security Center Standard csomagra váltásával fokozott biztonságot érhet](https://docs.microsoft.com/azure/security-center/security-center-pricing). (A képen árak például csak célokat.)

![Az ASC a standard csomag](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használatával

Az Azure Resource Manager-sablon üzembe helyezéséhez a komplex veszélyforrások elleni védelem az Azure Storage-fiók használata engedélyezett.
További információkért lásd: [komplex veszélyforrások elleni védelem tárfiók](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Az Azure Policy használata

Az Azure Policy használatával komplex veszélyforrások elleni védelem engedélyezése egy adott előfizetés vagy az erőforrás-csoportban tárfiókok között.

1. Az Azure delek **szabályzat – definíciók** lapot.

1. Keresse meg a **üzembe helyezése komplex veszélyforrások elleni védelem a Storage-fiókok** házirend.

     ![Keresés a házirend](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Válasszon ki egy Azure előfizetést vagy az erőforrást.

    ![Előfizetés vagy csoport kiválasztása](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. A szabályzat hozzárendelése.

    ![Definíciók szabályzatról](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>A REST API használata
Rest API-parancsok segítségével létrehozása, frissítése és az adott tárfiókhoz a komplex veszélyforrások elleni védelem beállítás.

* [Komplex veszélyforrások elleni védelem – létrehozása](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Komplex veszélyforrások elleni védelem – beolvasása](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

A következő PowerShell-parancsmagokat használja:

  * [A komplex veszélyforrások elleni védelem engedélyezése](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Első komplex veszélyforrások elleni védelem](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [A komplex veszélyforrások elleni védelem letiltása](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Ismerje meg a biztonsági anomáliák kezelésére

Tárolási tevékenységével kapcsolatos szabálytalanságokat fordulhat elő, ha e-mail-értesítést a gyanús biztonsági eseményről információkat kap. Az esemény részleteinek a következők:

* Az anomáliadetektálási jellege
* A tárfiók neve
* Az esemény időpontja
* A tárolási típust
* A lehetséges okok 
* A kivizsgálás szükséges lépéseit
* Hibaelhárítási lépések


Az e-mailben is kitérnek a lehetséges okok és ajánlott műveletek vizsgálata és enyhítése érdekében a potenciális fenyegetést.

![Az Azure Storage komplex veszélyforrások elleni védelem riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Tekintse át, és az aktuális biztonsági riasztások kezelése az Azure Security Center [biztonsági riasztások csempe](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Részletek és az aktuális fenyegetések kivizsgálása és a jövőbeli fenyegetésekkel szembeni címzés műveletek egy meghatározott riasztásra kattintva biztosít.

![Az Azure Storage komplex veszélyforrások elleni védelem riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Védelmi figyelmeztetések

Szokatlan és vélhetően kárt okozó kísérleteket elérni vagy kiaknázni a storage-fiókok által előállított riasztások. Ezeket az eseményeket is aktiválhatja a következő riasztásokat:

### <a name="anomalous-access-pattern-alerts"></a>Rendellenes hozzáférési minta riasztások

* **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha valaki használta a storage-fiók egy szokatlan földrajzi helyről.
A lehetséges okok:
   * A támadó érte el a storage-fiók
   * Egy jogosult felhasználó fért hozzá a tárfiók egy új helyről
 
* **Alkalmazás Anomáliadetektálási**: Ez a riasztás azt jelzi, hogy szokatlan alkalmazáshoz fért hozzá ezt a tárfiókot. A lehetséges okok:
   * A támadó fért hozzá a tárfiók, egy új alkalmazással.
   * Egy jogosult felhasználónak a tárfiók eléréséhez egy új alkalmazást böngésző használatban van.

* **Névtelen hozzáférés**: Ez a riasztás jelzi, hogy lett-e ezt a fiókot ehhez a fiókhoz a legutóbbi hozzáférés minta névtelenül elérhető (azaz minden hitelesítés nélkül), amely nem a várt képest.
A lehetséges okok:
   * A támadó bejutott nyilvános olvasási hozzáférés egy tárolóba.
   * Egy jogosult felhasználó vagy alkalmazás használatban van egy tárolót nyilvános olvasási hozzáférést.

* **Tor Anomáliadetektálási**: Ez a riasztás azt jelzi, hogy ez a fiók rendelkezik hozzáfértek sikeresen egy aktív kilépési csomópontján Tor (anonymizing proxy) néven ismert IP-címek. Ez a riasztás súlyosságát is figyelembe veszi (ha vannak) használt hitelesítés típusa, és hogy a Ez az első esetben az ilyen hozzáférésre.
A lehetséges okok:
   * A támadó fért hozzá a tárfiók, Tor használatával.
   * Egy jogosult felhasználó fért hozzá a tárfiók, Tor használatával.


### <a name="anomalous-extractupload-alerts"></a>Rendellenes kinyerési feltölti riasztások

* **Adatok kiszűrése**: Ez a riasztás azt jelzi, hogy egy szokatlanul nagy mennyiségű adat lehívása a storage-tárolót a közelmúltbeli tevékenység képest. A lehetséges okok:
   * A támadó rendelkezik egy nagy mennyiségű adatot kinyert egy tárolót. (Például: adatok kiszűrése vagy illetéktelen behatolás, adatok jogosulatlan átvitelét)
   * Egy jogosult felhasználó vagy alkalmazás rendelkezik kinyert egy szokatlan adatmennyiség egy tárolót. (Például: karbantartási tevékenységek)

* **Váratlan delete**: Ez a riasztás azt jelzi, hogy egy vagy több váratlan törlési műveletek történt-e a storage-fiókban, ehhez a fiókhoz a közelmúltbeli tevékenység képest. A lehetséges okok:
   * A támadó tárfiókban lévő adatok törölve van.
   * Egy jogosult felhasználó hajtott végre egy szokatlan törlése.

* **Töltse fel az Azure Cloud Service-csomag**: Ez a riasztás azt jelzi, hogy egy Azure Cloud Service-csomag (.cspkg fájl) fel van töltve a tárfiókhoz szokatlan módon, ehhez a fiókhoz a közelmúltbeli tevékenység képest. A lehetséges okok: 
   * A támadó előkészítése meg a központi telepítése egy Azure-felhőszolgáltatás az Ön tárfiókjából rosszindulatú kódot.
   * Hiteles felhasználó rendelkezik lett egy megbízható szolgáltatás üzembe helyezésének előkészítése.

### <a name="suspicious-storage-activities-alerts"></a>A storage gyanús tevékenységek riasztások

* **Hozzáférési engedély módosítása**: Ez a riasztás azt jelzi, hogy módosult-e a hozzáférési engedélyeket a storage-tároló szokatlan módon. A lehetséges okok: 
   * A támadó megváltozott tároló gyengíthetik a biztonsági engedélyeket.
   * Egy jogosult felhasználó megváltozott a tároló engedélyeit.

* **Hálózatfelügyeleti eléréséhez**: Ez a riasztás azt jelzi, hogy a tárfiók hozzáférési engedélyek ellenőrzése után szokatlan módon, ehhez a fiókhoz a közelmúltbeli tevékenység képest. A lehetséges okok: 
   * A támadó egy jövőbeli támadások végzett felderítés.
   * Egy jogosult felhasználó hajtott végre karbantartási a tárfiókban.

* **Adatfeltárás**: Ez a riasztás azt jelzi, hogy BLOB vagy egy tárfiókban lévő tárolók lettek besorolva szokatlan módon, ehhez a fiókhoz a közelmúltbeli tevékenység képest. A lehetséges okok: 
   * A támadó egy jövőbeli támadások végzett felderítés.
   * Egy jogosult felhasználó vagy alkalmazáslogika rendelkezik megvizsgálta a tárfiókban lévő adatokat.






## <a name="next-steps"></a>További lépések

* Tudjon meg többet [naplók az Azure Storage-fiókok](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Tudjon meg többet [Azure Security Centerben](../../security-center/security-center-intro.md)

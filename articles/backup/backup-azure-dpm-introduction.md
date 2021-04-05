---
title: A DPM-kiszolgáló előkészítése a munkaterhelések biztonsági mentésére
description: Ebből a cikkből megtudhatja, hogyan készítse elő a System Center Data Protection Manager (DPM) biztonsági mentését az Azure-ba a Azure Backup szolgáltatás használatával.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 823b23d99959df5f2eed20cf4136254e1702fe89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98985631"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>A számítási feladatok biztonsági mentésének előkészítése az Azure-ba a System Center DPM

Ez a cikk azt ismerteti, hogyan lehet előkészíteni a System Center Data Protection Manager (DPM) Azure-ra történő biztonsági mentését az Azure Backup szolgáltatás használatával.

A cikk a következőket tartalmazza:

- A DPM Azure Backup-vel való üzembe helyezésének áttekintése.
- Az Azure Backup és a DPM használatának előfeltételei és korlátozásai.
- Az Azure előkészítésének lépései, beleértve a Recovery Services backup-tároló beállítását, valamint a tár Azure Storage-típusának igény szerinti módosítását.
- A DPM-kiszolgáló előkészítésének lépései, beleértve a tár hitelesítő adatainak letöltését, a Azure Backup ügynök telepítését és a DPM-kiszolgáló regisztrálását a tárolóban.
- Hibaelhárítási tippek gyakori hibákhoz.

## <a name="why-back-up-dpm-to-azure"></a>Miért érdemes biztonsági mentést készíteni a DPM az Azure-ba?

A [System Center DPM](/system-center/dpm/dpm-overview) biztonsági mentést készít a fájlokról és az alkalmazásokról. A DPM az alábbiak szerint kommunikál a Azure Backupekkel:

- **Fizikai kiszolgálón vagy helyszíni virtuális gépen futó DPM** – az adatok biztonsági mentését az Azure Backup-tárolójába, a lemez és a szalagos biztonsági mentés mellett is elvégezheti.
- **Azure-beli virtuális gépen futtatott DPM** – a System Center 2012 R2 és a 3. frissítéssel, illetve az újabb verziókkal a DPM üzembe helyezhető egy Azure-beli virtuális gépen. Biztonsági mentést készíthet a virtuális géphez csatolt Azure-lemezekre, vagy a Azure Backup használatával biztonsági mentést készíthet az adatbiztonsági mentési tárba.

A DPM-kiszolgálók Azure-ba történő biztonsági mentésének üzleti előnyei a következők:

- A helyszíni DPM a Azure Backup a hosszú távú szalagos üzembe helyezés alternatíváját biztosítja.
- Az Azure-beli virtuális gépeken futó DPM Azure Backup lehetővé teszi a tárolók kiszervezését az Azure-lemezről. A régebbi adattárolók tárolása lehetővé teszi, hogy az új lemezekre való adattárolással bővítse vállalkozását.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

**Beállítás** | **Követelmény**
--- | ---
DPM egy Azure-beli virtuális gépen | System Center 2012 R2, DPM 2012 R2 3. vagy újabb kumulatív frissítéssel.
DPM fizikai kiszolgálón | System Center 2012 SP1 vagy újabb; System Center 2012 R2.
DPM Hyper-V rendszerű virtuális gépen | System Center 2012 SP1 vagy újabb; System Center 2012 R2.
DPM VMware virtuális gépen | System Center 2012 R2 az 5. vagy újabb kumulatív frissítéssel.
Összetevők | A DPM-kiszolgálónak telepítve kell lennie a Windows PowerShell és a .NET-keretrendszer 4,5-es frissítésének.
Támogatott alkalmazások | [Ismerje meg](/system-center/dpm/dpm-protection-matrix) , hogy milyen DPM tud biztonsági másolatot készíteni.
Támogatott fájltípusok | Ezek a fájltípusok biztonsági mentést készíthetnek Azure Backup használatával:<br> <li>Titkosított (csak teljes biztonsági mentés)<li> Tömörített (növekményes biztonsági másolatok támogatottak) <li> Ritka (növekményes biztonsági másolatok támogatottak)<li> Tömörített és ritka (ritkán kezelt)
Nem támogatott fájltípusok | <li>Kis-és nagybetűket megkülönböztető fájlrendszerrel rendelkező kiszolgálók<li> rögzített hivatkozások (kihagyva)<li> újraelemzési pontok (kihagyva)<li> titkosított és tömörített (kihagyva)<li> titkosított és ritka (kihagyva)<li> Tömörített Stream<li> adatfolyam elemzése
Helyi tárhely | Minden olyan gépnek, amelyről biztonsági másolatot szeretne készíteni, rendelkeznie kell helyi szabad tárterülettel, amely a biztonsági mentés alatt álló adatmennyiség legalább 5%-a. Például a 100 GB adat biztonsági mentéséhez legalább 5 GB szabad terület szükséges a semmiből.
Tár tárolója | A Azure Backup-tárolóba felhasználható adatmennyiségnek nincs korlátja, de az adatforrások (például virtuális gépek vagy adatbázisok) mérete nem haladhatja meg a 54 400 GB-ot.
Azure ExpressRoute | Az Azure ExpressRoute-on keresztül biztonsági mentést készíthet az adatairól a nyilvános (a régi áramkörökhöz elérhető) és a Microsoft-partnerek számára. A privát kapcsolaton keresztüli biztonsági mentés nem támogatott.<br/><br/> **Nyilvános társítás esetén**: a következő tartományokhoz/címekhez való hozzáférés biztosítása:<br/><br/> URLs<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>IP-címek<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/>**A Microsoft-partnerekkel** válassza ki a következő szolgáltatásokat/régiókat és a vonatkozó közösségi értékeket:<br/><br/>-Azure Active Directory (12076:5060)<br/><br/>-Microsoft Azure régió (az Recovery Services-tároló helyének megfelelően)<br/><br/>– Azure Storage (az Recovery Services-tároló helyének megfelelően)<br/><br/>További információ: ExpressRoute- [útválasztási követelmények](../expressroute/expressroute-routing.md).<br/><br/>**Megjegyzés**: a nyilvános társítás az új áramköröknél elavult.
Az Azure Backup ügynöke | Ha a DPM a System Center 2012 SP1 rendszeren fut, telepítse a 2. vagy újabb kumulatív frissítést a DPM SP1 verzióhoz. Ez az ügynök telepítéséhez szükséges.<br/><br/> Ez a cikk bemutatja, hogyan helyezheti üzembe a Azure Backup-ügynök legújabb verzióját, más néven a Microsoft Azure Recovery Service (MARS) ügynököt. Ha a korábbi verziót telepítette, frissítsen a legújabb verzióra, és győződjön meg arról, hogy a biztonsági mentés a várt módon működik-e.

Mielőtt elkezdené, szüksége lesz egy Azure-fiókra, amelyen engedélyezve van a Azure Backup funkció. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ a [Azure Backup díjszabásáról](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Tárolási beállítások módosítása

Választhat a földrajzilag redundáns tárolás és a helyileg redundáns tárolás között.

- Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik.
- Ha a tároló elsődleges biztonsági másolata, hagyja a beállítást a Geo-redundáns tárolás beállításnál. Ha olyan olcsóbb lehetőséget szeretne, amely nem annyira tartós, a következő eljárással konfigurálhatja a helyileg redundáns tárolást.
- Tudjon meg többet az [Azure Storage](../storage/common/storage-redundancy.md)szolgáltatásról, valamint a [geo-redundáns](../storage/common/storage-redundancy.md#geo-redundant-storage), [helyileg redundáns](../storage/common/storage-redundancy.md#locally-redundant-storage) és [zónákra](../storage/common/storage-redundancy.md#zone-redundant-storage) redundáns tárolási lehetőségekről.
- Módosítsa a tárolási beállításokat a kezdeti biztonsági mentés előtt. Ha már készített biztonsági másolatot egy elemről, a tárolási beállítások módosítása előtt állítsa le a biztonsági mentést a tárolóban.

A tárreplikációs beállítás szerkesztése:

1. Nyissa meg a tároló irányítópultját.

2. A **kezelés** területen válassza a **biztonsági mentési infrastruktúra** elemet.

3. A **biztonsági mentés konfigurálása** menüben válassza ki a tárolóhoz tartozó tárolási beállítást.

    ![A Backup-tárolók listája](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>A tároló hitelesítő adatainak letöltése

A tár hitelesítő adatait használja a DPM-kiszolgáló a tárolóban való regisztrálásához.

- A tároló hitelesítőadat-fájlja a portál által az egyes Backup-tárolókhoz létrehozott tanúsítvány.
- A portál ezután feltölti a nyilvános kulcsot az Access Control Service (ACS) szolgáltatásba.
- A gép regisztrációs munkafolyamata során a tanúsítvány titkos kulcsa elérhetővé válik a felhasználó számára, amely hitelesíti a számítógépet.
- A hitelesítés alapján a Azure Backup szolgáltatás adatokat küld az azonosított tárba.

### <a name="best-practices-for-vault-credentials"></a>Ajánlott eljárások a tár hitelesítő adataihoz

A hitelesítő adatok beszerzéséhez töltse le a tároló hitelesítőadat-fájlját egy biztonságos csatornán keresztül a Azure Portal:

- A tároló hitelesítő adatait csak a regisztrációs munkafolyamat során használja a rendszer.
- Az Ön felelőssége, hogy a tár hitelesítő adatait tartalmazó fájl biztonságos legyen, és ne veszélyeztesse.
  - Ha a hitelesítő adatok ellenőrzése megszakad, a tároló hitelesítő adataival más gépek is regisztrálhatók a tárba.
  - A biztonsági mentési adatai azonban az Ön tulajdonában lévő jelszóval vannak titkosítva, így a meglévő biztonsági mentési adatai nem lehetnek biztonságban.
- Győződjön meg arról, hogy a fájl egy olyan helyen lett mentve, amely a DPM-kiszolgálóról érhető el. Ha egy fájlmegosztási vagy SMB-fájlban van tárolva, ellenőrizze a hozzáférési engedélyeket.
- A tároló hitelesítő adatai 48 óra után lejárnak. Az új tár hitelesítő adatait igény szerint többször is letöltheti. A regisztrációs munkafolyamat során azonban csak a legújabb tár hitelesítőadat-fájl használható.
- A Azure Backup szolgáltatás nem ismeri a tanúsítvány titkos kulcsát, és a titkos kulcs nem érhető el a portálon vagy a szolgáltatásban.

Töltse le a tároló hitelesítő adatait tartalmazó fájlt egy helyi gépre a következő módon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg azt a tárolót, amelyben regisztrálni szeretné a DPM-kiszolgálót.
3. A **Beállítások** területen válassza a **Tulajdonságok** lehetőséget.

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. A **Tulajdonságok**  >  **biztonsági másolatának hitelesítő adatai** területen válassza a **Letöltés** lehetőséget. A portál a tár hitelesítő adatait tartalmazó fájlt a tár neve és az aktuális dátum kombinációja alapján hozza létre, és letölthetővé teszi.

    ![Hitelesítő adatok letöltése](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Válassza a **Mentés** lehetőséget a tár hitelesítő adatainak a mappába való letöltéséhez, vagy a **Mentés másként lehetőségre** , és adjon meg egy helyet. A fájl generálása akár egy percet is igénybe vesz.

## <a name="install-the-backup-agent"></a>A biztonságimásolat-készítő ügynök telepítése

A Azure Backup által készített biztonsági mentést végző számítógépeken telepítve kell lennie a biztonsági mentési ügynöknek (más néven Microsoft Azure Recovery Service-(MARS-) ügynöknek). Telepítse az ügynököt a DPM-kiszolgálóra a következőképpen:

1. Nyissa meg azt a tárolót, amelyre regisztrálni szeretné a DPM-kiszolgálót.
2. A **Beállítások** területen válassza a **Tulajdonságok** lehetőséget.

    ![Tár beállításainak megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. A **Tulajdonságok** lapon töltse le a Azure Backup-ügynököt.

    ![Letöltés](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. A letöltés után futtassa MARSAgentInstaller.exe. az ügynök telepítése a DPM-gépre.
5. Válassza ki az ügynök telepítési mappáját és gyorsítótárának mappáját. A gyorsítótár helyének szabad területének a biztonsági mentési adatmennyiség legalább 5%-ának kell lennie.
6. Ha proxykiszolgálót használ az internethez való csatlakozáshoz, a **proxy konfiguráció** képernyőjén adja meg a proxykiszolgáló részleteit. Ha hitelesített proxyt használ, a képernyőn adja meg a Felhasználónév és a jelszó részleteit.
7. A Azure Backup ügynök telepíti a .NET-keretrendszer 4,5-es és a Windows PowerShell-t (ha nincsenek telepítve) a telepítés befejezéséhez.
8. Az ügynök telepítése után **zárjuk be** az ablakot.

    ![Bezárás](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>A DPM-kiszolgáló regisztrálása a tárolóban

1. A DPM felügyeleti konzol > **felügyelet** területén válassza az **online** lehetőséget. Válassza a **Regisztráció** lehetőséget. Ekkor megnyílik a kiszolgáló regisztrálása varázsló.
2. A **proxy konfigurálása** területen szükség szerint határozza meg a proxybeállításokat.

    ![Proxy konfigurálása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. A **Backup-tárolóban** keresse meg és válassza ki a letöltött tároló hitelesítő adatait tartalmazó fájlt.

    ![Tároló hitelesítő adatai](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. A **szabályozás beállítása beállításnál** engedélyezheti a sávszélesség-szabályozást a biztonsági mentésekhez. Megadhatja a munkaórák és napok megadásának korlátait.

    ![Szabályozás beállítása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. A **helyreállítási mappa beállítása** területen válasszon egy helyet, amelyet az Adathelyreállítás során használhat.

    - Azure Backup ezt a helyet használja ideiglenes területként a helyreállított adatmennyiséghez.
    - Az Adathelyreállítás befejezése után Azure Backup törli az ezen a területen található adatvesztést.
    - A helynek elegendő lemezterülettel kell rendelkeznie ahhoz, hogy a helyreállítani kívánt elemeket párhuzamosan lehessen tárolni.

    ![Helyreállítási mappa beállítása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. A **titkosítási beállításnál** adja meg vagy adjon meg egy jelszót.

    - A hozzáférési kód a biztonsági másolatok felhőbe történő titkosítására szolgál.
    - Legalább 16 karaktert kell megadnia.
    - Mentse a fájlt biztonságos helyen, ezért a helyreállításhoz szükséges.

    ![Titkosítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Ön rendelkezik a titkosítási jelszóval, és a Microsoft nem rendelkezik a láthatósággal.
    > Ha a jelszó elvesztése vagy elvész, a Microsoft nem tud segítséget nyújtani a biztonsági mentési adatgyűjtés helyreállításához.

7. Válassza a **regisztráció** lehetőséget a DPM-kiszolgáló a tárolóhoz való regisztrálásához.

Miután a kiszolgáló regisztrálása sikeresen megtörtént a tárolóban, most már készen áll a Microsoft Azurere való biztonsági mentésre. A munkaterhelések Azure-ba történő biztonsági mentéséhez konfigurálnia kell a védelmi csoportot a DPM-konzolon. [További információ](/system-center/dpm/create-dpm-protection-groups) a védelmi csoportok üzembe helyezéséről.

## <a name="troubleshoot-vault-credentials"></a>Tár hitelesítő adatainak hibakeresése

### <a name="expiration-error"></a>Lejárati hiba

A tár hitelesítő adatainak fájlja csak 48 órán át érvényes (Miután letöltötte a portálról). Ha a képernyőn bármilyen hiba fordul elő (például "a tár hitelesítő adatainak megadott fájlja lejárt"), jelentkezzen be a Azure Portalba, majd töltse le újra a tároló hitelesítő adatait tartalmazó fájlt.

### <a name="access-error"></a>Hozzáférési hiba

Győződjön meg arról, hogy a tár hitelesítő adatainak fájlja olyan helyen érhető el, amelyet a telepítő alkalmazás érhet el. Ha a hozzáféréshez kapcsolódó hibák merülnek fel, másolja a tároló hitelesítő adatait tartalmazó fájlt egy ideiglenes helyre a számítógépen, majd próbálja megismételni a műveletet.

### <a name="invalid-credentials-error"></a>Érvénytelen hitelesítő adatokkal kapcsolatos hiba

Ha a tár hitelesítő adatai érvénytelenek (például "Érvénytelen tároló hitelesítő adatok"), akkor a fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatáshoz társított legújabb hitelesítő adatokkal.

- Próbálja megismételni a műveletet, miután letöltött egy új tároló hitelesítő adatait a portálról.
- Ez a hiba általában akkor látható, ha a tároló **hitelesítő adatainak letöltése** lehetőséget választja a Azure Portalban, kétszer a gyors egymásutánban. Ebben az esetben csak a második tár hitelesítő adatait tartalmazó fájl érvényes.

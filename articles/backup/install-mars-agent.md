---
title: Az Microsoft Azure Recovery Services- (MARS-) ügynök telepítése
description: Megtudhatja, hogyan telepítheti a Microsoft Azure Recovery Services- (MARS-) ügynököt a Windows rendszerű gépek biztonsági frissítéséhez.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 3ea48aaa6aad4a51463c4c028ead22f31163f810
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519221"
---
# <a name="install-the-azure-backup-mars-agent"></a>Az Azure Backup MARS-ügynök telepítése

Ez a cikk a Microsoft Azure Recovery Services- (MARS-) ügynök telepítését ismerteti. A MARS más néven Azure Backup ügynök.

## <a name="about-the-mars-agent"></a>A MARS-ügynökről

Azure Backup MARS-ügynökkel biztonsági mentése történik a helyszíni gépekről és az Azure-beli virtuális gépekről származó fájlokról, mappákról és rendszerállapotról. Ezek a biztonsági másolatok egy Recovery Services-tárolóban vannak tárolva az Azure-ban. Az ügynököt futtathatja:

* Közvetlenül a helyszíni Windows-gépeken. Ezek a gépek közvetlenül az Azure-beli Recovery Services-tárolókba tudnak biztonsági ásni.
* Olyan Azure-beli virtuális gépeken, amelyeken a Windows az Azure-beli virtuális gép biztonsági mentési bővítménye mellett fut. Az ügynök biztonsági mentése a virtuális gép adott fájljairól és mappáiról.
* Egy Microsoft Azure Backup (MABS) példányon vagy egy System Center Data Protection Manager (DPM) kiszolgálón. Ebben a forgatókönyvben a gépek és a számítási feladatok a MABS-be vagy az Data Protection Manager. Ezután a MABS Data Protection Manager MARS-ügynökkel biztonságimentet egy Azure-beli tárolóba.

A biztonsági mentéshez elérhető adatok az ügynök telepítésének helyétől függnek.

> [!NOTE]
> Az Azure-beli virtuális gépek biztonsági Azure Backup a virtuális gépen. Ez a metódus a teljes virtuális gép biztonsági adatait biztonságimenti. Ha a virtuális gépen található adott fájlokról és mappákról szeretne biztonsági mentéset, telepítse és használja a MARS-ügynököt a bővítmény mellett. További információ: [Az Azure-beli](backup-architecture.md#architecture-built-in-azure-vm-backup)virtuális gépek beépített biztonsági mentésének architektúrája.

![Biztonsági mentési folyamat lépései](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Előkészületek

* [Megtudhatja, Azure Backup MARS-ügynökkel](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)hogyan biztonságimenti a Windows rendszerű gépeket.
* Ismerje meg azt [a biztonsági mentési](backup-architecture.md#architecture-back-up-to-dpmmabs) architektúrát, amely a MARS-ügynököt egy másodlagos MABS- vagy Data Protection Manager futtatja.
* Tekintse [át, hogy mit támogat](backup-support-matrix-mars-agent.md) a marsi ügynök, és miről tud biztonságimentet.
* Győződjön meg arról, hogy van Azure-fiókja, ha egy kiszolgálóról vagy ügyfélről kell biztonságimentet kapnia az Azure-ba. Ha nincs fiókja, néhány perc alatt [](https://azure.microsoft.com/free/) létrehozhat egy ingyenes fiókot.
* Ellenőrizze az internet-hozzáférést a gépeken, amelyekről biztonsági elérést kíván kapni.
* Győződjön meg arról, hogy a MARS-ügynök telepítését és konfigurálást végző felhasználó helyi rendszergazdai jogosultságokkal rendelkezik a védeni szükséges kiszolgálón.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Tárolóreplikáció módosítása

Alapértelmezés szerint a tárolók georedundáns [tárolást (GRS) használnak.](../storage/common/storage-redundancy.md#geo-redundant-storage)

* Ha a tároló az elsődleges biztonsági mentési mechanizmus, javasoljuk, hogy használja a GRS-t.
* A helyileg redundáns [tárolás (LRS) használatával csökkentheti](../storage/common/storage-redundancy.md#locally-redundant-storage) az Azure-tárterület költségeit.

A tárreplikáció típusának módosítása:

1. Az új tárolóban válassza a **Beállítások szakasz Tulajdonságok** **szakaszát.**

1. A Tulajdonságok **lap** Biztonsági mentés **konfigurációja lapján válassza** a Frissítés **lehetőséget.**

1. Válassza ki a tárreplikáció típusát, majd válassza a **Mentés lehetőséget.**

    ![Biztonsági mentés konfigurációjának frissítése](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> A tároló replikációs típusát a tároló beállítása után nem módosíthatja, és biztonsági mentési elemeket tartalmaz. Ha ezt szeretné tenni, újra létre kell hoznia a tárolót.
>

### <a name="verify-internet-access"></a>Az internet-hozzáférés ellenőrzése

Ha a gépe korlátozott internetkapcsolattal rendelkezik, győződjön meg arról, hogy a gép vagy a proxy tűzfalbeállításai engedélyezik a következő URL-címeket és IP-címeket:

* URL-címek
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>A Azure ExpressRoute

Az adatok biztonsági biztonsági Azure ExpressRoute a nyilvános társviszony-létesítés (amely a régi kapcsolati körök számára érhető el) és a Microsoft társviszony-létesítés használatával. A privát társviszony-létesítésen keresztüli biztonsági mentés nem támogatott.

Nyilvános társviszony-létesítés használata esetén először győződjön meg a következő tartományokhoz és címekhez való hozzáférésről:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18

A Microsoft társviszony-létesítéshez válassza ki a következő szolgáltatásokat, régiókat és a kapcsolódó közösségi értékeket:

* Azure Active Directory (12076:5060)
* Azure-régió a helyreállítási tár helyének megfelelően
* Az Azure Storage a helyreállítási tár helyének megfelelően

További információ: [ExpressRoute útválasztási követelmények.](../expressroute/expressroute-routing.md)

> [!NOTE]
> A nyilvános társviszony-létesítés elavult az új kapcsolathálózatok számára.

A fenti URL-címek és IP-címek mind a HTTPS protokollt használják a 443-as porton.

### <a name="private-endpoints"></a>Privát végpontok

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>A MARS-ügynök letöltése

Töltse le a MARS-ügynököt, hogy telepítheti a gépekre, amelyekről biztonsági kíván biztonsági okat.

Ha már telepítette az ügynököt valamelyik gépre, győződjön meg arról, hogy az ügynök legújabb verzióját futtatja. Keresse meg a legújabb verziót a portálon, vagy keresse fel közvetlenül a [letöltést.](https://aka.ms/azurebackup_agent)

1. A tárolóban az Első **lépések alatt válassza** a Biztonsági mentés **lehetőséget.**

    ![A biztonsági mentési cél megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. A **Hol fut a számítási feladat?** alatt válassza a Helyszíni **lehetőséget.** Akkor is válassza ezt a lehetőséget, ha a MARS-ügynököt egy Azure-beli virtuális gépre szeretné telepíteni.
1. A **Miről szeretne biztonsági mentése?** alatt válassza a Fájlok és mappák **lehetőséget.** A Rendszerállapot lehetőséget **is választhatja.** Számos más lehetőség is elérhető, de ezek a lehetőségek csak másodlagos biztonsági mentési kiszolgáló futtatása esetén támogatottak. Válassza az **Infrastruktúra előkészítése lehetőséget.**

    ![Fájlok és mappák konfigurálása](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Az **infrastruktúra előkészítése esetében** a Recovery **Services-ügynök telepítése alatt** töltse le a MARS-ügynököt.

    ![Az infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. A letöltési menüben válassza a **Mentés lehetőséget.** Alapértelmezés szerint az *MARSagentinstaller.exe* fájlt a rendszer a Downloads mappába menti.

1. Válassza **a Már letöltött vagy a legújabb Recovery Services-ügynök használata** lehetőséget, majd töltse le a tároló hitelesítő adatait.

    ![A tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Kattintson a **Mentés** gombra. A rendszer letölti a fájlt a Letöltések mappába. Nem nyithatja meg a tároló hitelesítőadat-fájlját.

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Futtassa *aMARSagentinstaller.exe* fájlt a gépeken, amelyekről biztonsági mentéset kíván futtatni.
1. A MARS-ügynök telepítővarázsló a **Telepítési beállítások lehetőséget.** Itt válassza ki az ügynök telepítésének helyét, és válasszon egy helyet a gyorsítótár számára. Ezután kattintson a **Tovább** gombra.
   * Azure Backup a gyorsítótárat használja az adat-pillanatképek tárolására, mielőtt elküldi azokat az Azure-nak.
   * A gyorsítótár helyének legalább 5%-ában szabad területnek kell lennie a biztonsági adatokat tároló adatok méretének.

    ![Telepítési beállítások kiválasztása a MARS-ügynök telepítővarázsló](./media/backup-configure-vault/mars1.png)

1. A **Proxykonfiguráció** beállításnál adja meg, hogy a Windows rendszerű gépen futó ügynök hogyan csatlakozzon az internethez. Ezután kattintson a **Tovább** gombra.

   * Ha egyéni proxyt használ, adja meg a szükséges proxybeállításokat és hitelesítő adatokat.
   * Ne feledje, hogy az ügynöknek adott [URL-címekhez kell hozzáférnie.](#before-you-start)

    ![Internet-hozzáférés beállítása a MARS varázslóban](./media/backup-configure-vault/mars2.png)

1. A **Telepítés mezőben** tekintse át az előfeltételeket, majd válassza a **Telepítés lehetőséget.**
1. Az ügynök telepítése után válassza a **Folytatás a regisztrációhoz lehetőséget.**
1. A **Kiszolgáló regisztrálása varázsló** Tárolóazonosítása lapján keresse meg és válassza ki a letöltött  >  hitelesítőadat-fájlt. Ezután kattintson a **Tovább** gombra.

    ![Tároló hitelesítő adatainak hozzáadása a Kiszolgáló regisztrálása varázslóval](./media/backup-configure-vault/register1.png)

1. A **Titkosítási beállítás lapon** adjon meg egy jelszót, amely a gép biztonsági másolatai titkosítására és visszafejtéséhez lesz használva. [Az engedélyezett](backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-) jelszókarakterekkel kapcsolatos további információkért lásd itt.

    * Mentse a jelszót egy biztonságos helyre. A biztonsági másolat visszaállításához szüksége lesz rá.
    * Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem tud segíteni a biztonsági mentési adatok helyreállításában.

1. Válassza a **Befejezés** gombot. Az ügynök most már telepítve van, és a gép regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [lehet biztonságimentetni Windows rendszerű gépekről a Azure Backup MARS Azure Backup használatával](backup-windows-with-mars-agent.md)

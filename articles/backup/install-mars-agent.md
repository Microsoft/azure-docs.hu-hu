---
title: A Microsoft Azure Recovery Services-(MARS-) ügynök telepítése
description: Ismerje meg, hogyan telepítheti a Microsoft Azure Recovery Services-(MARS-) ügynököt a Windows rendszerű gépek biztonsági mentésére.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: acf38fdf338fcdd0cd7902d4295f0f03310543a8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986870"
---
# <a name="install-the-azure-backup-mars-agent"></a>A Azure Backup MARS-ügynök telepítése

Ez a cikk a Microsoft Azure Recovery Services (MARS) ügynök telepítését ismerteti. A MARSot Azure Backup ügynöknek is nevezzük.

## <a name="about-the-mars-agent"></a>Tudnivalók a MARS-ügynökről

A Azure Backup a MARS-ügynököt használja a fájlok, mappák és rendszerállapotok biztonsági mentésére a helyszíni gépekről és az Azure-beli virtuális gépekről. Ezeket a biztonsági másolatokat az Azure egy Recovery Services tárolójában tárolja. Az ügynököt az alábbiak szerint futtathatja:

* Közvetlenül a helyszíni Windows rendszerű gépeken. Ezek a gépek közvetlenül az Azure-beli Recovery Services-tárolóra tudnak biztonsági mentést készíteni.
* Olyan Azure-beli virtuális gépeken, amelyeken az Azure VM Backup bővítménnyel együtt futnak a Windows egymás mellett. Az ügynök biztonsági másolatot készít a virtuális gépen lévő meghatározott fájlokról és mappákról.
* Microsoft Azure Backup Server-(MABS-) példányon vagy System Center Data Protection Manager (DPM) kiszolgálón. Ebben a forgatókönyvben a gépek és a munkaterhelések biztonsági mentést készítenek a MABS vagy a Data Protection Manager. Ezután a MABS vagy a Data Protection Manager a MARS-ügynök használatával készít biztonsági másolatot az Azure-beli tárolóba.

A biztonsági mentéshez rendelkezésre álló adatok az ügynök telepítésének helyétől függenek.

> [!NOTE]
> Általában az Azure-beli virtuális gépek biztonsági mentését egy Azure Backup bővítmény használatával végezheti el a virtuális gépen. Ez a módszer biztonsági másolatot készít a teljes virtuális gépről. Ha a virtuális gépen lévő meghatározott fájlokról és mappákról szeretne biztonsági másolatot készíteni, telepítse és használja a MARS-ügynököt a bővítmény mellett. További információ: [beépített Azure-beli virtuális gépek biztonsági mentésének architektúrája](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Biztonsági mentési folyamat lépései](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Előkészületek

* Megtudhatja [, hogyan használja a Azure Backup a Mars-ügynököt a Windows rendszerű gépek biztonsági mentésére](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Ismerje meg a MARS-ügynököt egy másodlagos MABS vagy Data Protection Manager kiszolgálón futtató [biztonsági mentési architektúrát](backup-architecture.md#architecture-back-up-to-dpmmabs) .
* Tekintse át a támogatott és a MARS-ügynök által [használható biztonsági mentést](backup-support-matrix-mars-agent.md) .
* Győződjön meg arról, hogy rendelkezik Azure-fiókkal, ha biztonsági mentést szeretne készíteni egy kiszolgálóról vagy ügyfélről az Azure-ba. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
* Ellenőrizze az internet-hozzáférést azon gépeken, amelyekről biztonsági másolatot szeretne készíteni.
* Győződjön meg arról, hogy a MARS-ügynök telepítését és konfigurálását végző felhasználó helyi rendszergazdai jogosultságokkal rendelkezik a védett kiszolgálón.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Tárolási replikáció módosítása

Alapértelmezés szerint a [tárolók a Geo-redundáns tárolást (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)használják.

* Ha a tároló elsődleges biztonsági mentési mechanizmusa, javasoljuk, hogy használja a GRS.
* A [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) használatával csökkentheti az Azure Storage szolgáltatás költségeit.

A tárolási replikálás típusának módosítása:

1. Az új tárolóban válassza a **Tulajdonságok** lehetőséget a **Beállítások** szakaszban.

1. A **Tulajdonságok** lap **biztonsági mentés konfigurálása** területén válassza a **frissítés** lehetőséget.

1. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés** gombra.

    ![Biztonsági mentési konfiguráció frissítése](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> A tárolási replikálási típus nem módosítható a tároló beállítása és a biztonsági mentési elemek létrehozása után. Ha ezt szeretné tenni, újra létre kell hoznia a tárolót.
>

### <a name="verify-internet-access"></a>Internet-hozzáférés ellenőrzése

Ha a számítógépén korlátozott az Internet-hozzáférés, győződjön meg arról, hogy a számítógépen vagy a proxyn a tűzfalbeállítások a következő URL-címek és IP-címek használatát teszik lehetővé:

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

### <a name="use-azure-expressroute"></a>Az Azure ExpressRoute használata

Az Azure ExpressRoute-on keresztül történő biztonsági mentést a nyilvános peering (a régi áramkörök számára elérhető) és a Microsoft-partneri kapcsolat használatával végezheti el. A privát kapcsolaton keresztüli biztonsági mentés nem támogatott.

A nyilvános társítás használatához először ellenőrizze a következő tartományokhoz és címekhez való hozzáférést:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18

A Microsoft-társak használatához válassza ki a következő szolgáltatásokat, régiókat és releváns közösségi értékeket:

* Azure Active Directory (12076:5060)
* Azure-régió, az Recovery Services-tároló helyének megfelelően
* Az Azure Storage a Recovery Services-tároló helyének megfelelően

További információ: ExpressRoute- [útválasztási követelmények](../expressroute/expressroute-routing.md).

> [!NOTE]
> Az új áramkörök esetében a nyilvános társítás elavult.

Az előző URL-címek és IP-címek mindegyike a HTTPS protokollt használja a 443-es porton.

### <a name="private-endpoints"></a>Privát végpontok

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>A MARS-ügynök letöltése

Töltse le a MARS-ügynököt, és telepítse azokat a számítógépekre, amelyekről biztonsági másolatot szeretne készíteni.

Ha már telepítette az ügynököt valamelyik gépen, győződjön meg arról, hogy az ügynök legújabb verzióját futtatja. Keresse meg a legújabb verziót a portálon, vagy lépjen közvetlenül a [letöltésre](https://aka.ms/azurebackup_agent).

1. A tárolóban, a **első lépések** alatt válassza a **biztonsági mentés** elemet.

    ![A biztonsági mentési cél megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. A **hol fut a munkaterhelése?** területen válassza **a** helyszíni lehetőséget. Akkor is válassza ezt a lehetőséget, ha a MARS-ügynököt egy Azure-beli virtuális gépen szeretné telepíteni.
1. A **Miről szeretne biztonsági másolatot készíteni?** területen válassza a **fájlok és mappák** lehetőséget. A **rendszerállapot** lehetőséget is választhatja. Számos más lehetőség is elérhető, de ezek a beállítások csak akkor támogatottak, ha másodlagos biztonsági mentési kiszolgálót futtat. Válassza az **infrastruktúra előkészítése** lehetőséget.

    ![Fájlok és mappák konfigurálása](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Az **infrastruktúra előkészítése** érdekében a **Recovery Services ügynök telepítése** területen töltse le a Mars-ügynököt.

    ![Az infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. A letöltés menüben válassza a **Mentés** lehetőséget. Alapértelmezés szerint az *MARSagentinstaller.exe* fájlt a rendszer a Downloads mappába menti.

1. Válassza a **már letöltött vagy a legújabb Recovery Services ügynök használata** lehetőséget, majd töltse le a tároló hitelesítő adatait.

    ![A tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Kattintson a **Mentés** gombra. A rendszer letölti a fájlt a letöltések mappájába. A tár hitelesítő adatait tartalmazó fájl nem nyitható meg.

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Futtassa a *MARSagentinstaller.exe* fájlt azokon a számítógépeken, amelyekről biztonsági másolatot szeretne készíteni.
1. A MARS-ügynök telepítővarázslója területen válassza a **telepítési beállítások** lehetőséget. Ott válassza ki, hol szeretné telepíteni az ügynököt, és válasszon egy helyet a gyorsítótár számára. Ezután kattintson a **Tovább** gombra.
   * Azure Backup a gyorsítótár használatával tárolja az adatpillanatképeket, mielőtt elküldené őket az Azure-ba.
   * A gyorsítótár helyének legalább 5%-ának kell lennie a biztonsági mentéshez használt adatmennyiségnek.

    ![Válassza a telepítési beállítások elemet a MARS-ügynök telepítése varázslóban.](./media/backup-configure-vault/mars1.png)

1. A **proxy konfigurálása** lapon adhatja meg, hogy a Windows rendszerű gépen futó ügynök hogyan kapcsolódjon az internethez. Ezután kattintson a **Tovább** gombra.

   * Ha egyéni proxyt használ, meg kell adnia a szükséges proxybeállításokat és hitelesítő adatokat.
   * Ne feledje, hogy az ügynöknek hozzá kell férnie [bizonyos URL-címekhez](#before-you-start).

    ![Internet-hozzáférés beállítása a MARS varázslóban](./media/backup-configure-vault/mars2.png)

1. A **telepítéshez** tekintse át az előfeltételeket, majd válassza a **telepítés** lehetőséget.
1. Az ügynök telepítése után válassza a **Folytatás a regisztrációhoz** lehetőséget.
1. A **kiszolgáló regisztrálása varázsló** tárolójának  >  **azonosítása** lapon keresse meg és válassza ki a letöltött hitelesítő adatokat tartalmazó fájlt. Ezután kattintson a **Tovább** gombra.

    ![Tár hitelesítő adatainak hozzáadása a kiszolgáló regisztrálása varázsló használatával](./media/backup-configure-vault/register1.png)

1. A **titkosítási beállítások** lapon a számítógép biztonsági másolatainak titkosításához és visszafejtéséhez használt jelszót kell megadnia. Az engedélyezett hozzáférési kódok karakterekről további információt [itt talál](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase) .

    * Mentse a jelszót egy biztonságos helyen. Szüksége lesz rá a biztonsági másolat visszaállításához.
    * Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem tud segítséget nyújtani a biztonsági mentési adatok helyreállításához.

1. Válassza a **Befejezés** gombot. Az ügynök már telepítve van, és a számítógép regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="next-steps"></a>Következő lépések

[A Windows rendszerű gépek biztonsági mentésének ismertetése a Azure Backup Mars-ügynök használatával](backup-windows-with-mars-agent.md)

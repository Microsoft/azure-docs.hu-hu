---
title: MABS & System Center DPM támogatási mátrix
description: Ez a cikk Azure Backup támogatást összegzi, ha Microsoft Azure Backup Servert (MABS) vagy System Center DPM használ a helyszíni és az Azure-beli virtuális gépek erőforrásainak biztonsági mentésére.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609753"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Támogatási mátrix a Microsoft Azure Backup Server vagy System Center DPM való biztonsági mentéshez

A [Azure Backup szolgáltatás](backup-overview.md) segítségével biztonsági mentést készíthet a helyszíni gépekről és munkaterhelésekről, valamint az Azure-beli virtuális gépekről. Ez a cikk az Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection Manager (DPM) és a Azure Backup használatával ismerteti a gépek biztonsági mentésének támogatási beállításait és korlátozásait.

## <a name="about-dpmmabs"></a>Tudnivalók a DPM/MABS

A [System Center DPM](/system-center/dpm/dpm-overview) egy vállalati megoldás, amely a vállalati gépek és az adatmennyiségek biztonsági mentésének és helyreállításának konfigurálását, megkönnyítését és felügyeletét végzi. A [System Center](https://www.microsoft.com/system-center/pricing) programcsomag részét képezi.

A MABS egy olyan kiszolgálói termék, amely a rajtuk futó helyszíni fizikai kiszolgálók, virtuális gépek és alkalmazások biztonsági mentésére használható.

A MABS a System Center DPM alapul, és hasonló funkciókat biztosít néhány különbséggel:

- A MABS futtatásához nincs szükség System Center-licencre.
- A MABS és a DPM esetében az Azure hosszú távú biztonsági mentési tárolót biztosít. Emellett a DPM lehetővé teszi az adatok biztonsági mentését a szalagos hosszú távú tároláshoz. A MABS nem biztosítja ezt a funkciót.
- [Elsődleges DPM-kiszolgálóról biztonsági mentést készíthet egy másodlagos DPM-kiszolgálóval](/system-center/dpm/back-up-the-dpm-server). A másodlagos kiszolgáló biztosítja az elsődleges kiszolgáló adatbázisának és az elsődleges kiszolgálón tárolt adatforrás-replikáknak a védelmét. Az elsődleges kiszolgáló meghibásodása esetén a másodlagos kiszolgáló folytathatja az elsődleges kiszolgáló által védett munkaterhelések védelmét, amíg az elsődleges kiszolgáló ismét elérhető nem lesz.  A MABS nem biztosítja ezt a funkciót.

A MABS a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57520)töltheti le. Helyszíni vagy Azure-beli virtuális gépen is futtatható.

A DPM és a MABS számos alkalmazás, valamint a kiszolgáló-és ügyféloldali operációs rendszerek biztonsági mentését támogatja. Több biztonsági mentési forgatókönyvet biztosítanak:

- A számítógép szintjén biztonsági mentést készíthet a rendszerállapot vagy az operációs rendszer nélküli biztonsági mentés használatával.
- Biztonsági mentést készíthet bizonyos kötetekről, megosztásokról, mappákról és fájlokról.
- Az optimalizált, app-Aware beállítások használatával biztonsági mentést készíthet az egyes alkalmazásokról.

## <a name="dpmmabs-backup"></a>DPM/MABS biztonsági mentés

A DPM/MABS és a Azure Backup használatával történő biztonsági mentés a következőképpen működik:

1. A DPM/MABS védelmi ügynök telepítve van minden olyan gépen, amelyről biztonsági mentés készül.
1. A gépek és alkalmazások biztonsági mentése a helyi tárolóba történik a DPM/MABS.
1. A Microsoft Azure Recovery Services (MARS) ügynök telepítve van a DPM-kiszolgálón/MABS.
1. A MARS-ügynök biztonsági mentést készít a DPM-vagy MABS-lemezekről az Azure-beli biztonsági mentési Recovery Services-tárolóba Azure Backup használatával.

További információk:

- [További](backup-architecture.md#architecture-back-up-to-dpmmabs) információ a MABS architektúráról.
- [Tekintse át](backup-support-matrix-mars-agent.md) a Mars-ügynök által támogatott tudnivalókat.

## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Ügynök** | **Hely**
--- | --- | ---
**Helyszíni gépek/munkaterhelések biztonsági mentése** | A DPM/MABS védelmi ügynök azon gépeken fut, amelyekről biztonsági másolatot szeretne készíteni.<br/><br/> A MARS-ügynök a DPM/MABS-kiszolgálón.<br/> A szolgáltatás engedélyezéséhez szükséges Microsoft Azure Recovery Services ügynök vagy Azure Backup ügynök minimális verziója 2.0.8719.0.  | A DPM/MABS a helyszínen kell futnia.

## <a name="supported-deployments"></a>Támogatott központi telepítések

A DPM/MABS a következő táblázatban összefoglalt módon helyezhető üzembe.

**Üzembe helyezés** | **Támogatás** | **Részletek**
--- | --- | ---
**Helyszíni üzembe helyezés** | Fizikai kiszolgáló, de nem fizikai fürtben.<br/><br/>Hyper-V virtuális gép. A MABS különálló hypervisoron vagy fürtön is üzembe helyezheti vendégként. Nem helyezhető üzembe egy fürt vagy önálló hypervisor csomópontján. A Azure Backup Server dedikált, egycélú kiszolgálón való futtatásra lett tervezve.<br/><br/> Windows rendszerű virtuális gépként VMware-környezetben. | A helyszíni MABS-kiszolgálók nem védik az Azure-alapú munkaterheléseket. <br><br> További információ: [védelmi mátrix](backup-mabs-protection-matrix.md).
**Azure Stack virtuális gépként van üzembe helyezve** | Csak MABS | A DPM nem használható Azure Stack virtuális gépek biztonsági mentésére.
**Azure-beli virtuális gépként telepítve** | Az ezen a virtuális gépeken futó Azure-beli virtuális gépeket és munkaterheléseket védi | Az Azure-ban futó DPM/MABS nem tud biztonsági másolatot készíteni a helyszíni gépekről. Csak az Azure IaaS-alapú virtuális gépeken futó munkaterhelések elleni védelemre használható.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Támogatott MABS és DPM operációs rendszerek

Azure Backup a következő operációs rendszerek bármelyikét futtató DPM-vagy MABS-példányok biztonsági mentését végezheti el. Az operációs rendszernek a legújabb szervizcsomagokat és frissítéseket kell futtatnia.

**Forgatókönyv** | **DPM/MABS**
--- | ---
**MABS egy Azure-beli virtuális gépen** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Javasoljuk, hogy indítson el egy rendszerképet a piactéren.<br/><br/> Minimális Standard_A4_v2 négy maggal és 8 GB RAM-mal.
**DPM egy Azure-beli virtuális gépen** | System Center 2012 R2, 3. frissítéssel vagy újabb verzióval.<br/><br/> Windows operációs rendszer a [System Center által igényelt](/system-center/dpm/prepare-environment-for-dpm#dpm-server)módon.<br/><br/> Javasoljuk, hogy indítson el egy rendszerképet a piactéren.<br/><br/> Minimális Standard_A4_v2 négy maggal és 8 GB RAM-mal.
**Helyszíni MABS** |  MABS v3 és újabb verziók: Windows Server 2016 vagy Windows Server 2019
**Helyszíni DPM** | Fizikai kiszolgáló vagy Hyper-V virtuális gép: System Center 2012 SP1 vagy újabb.<br/><br/> VMware virtuális gép: System Center 2012 R2, 5. frissítéssel vagy újabb verzióval.

>[!NOTE]
>A Azure Backup Server telepítése nem támogatott a Windows Server Core vagy Microsoft Hyper-V Server rendszeren.

## <a name="management-support"></a>Kezelési támogatás

**Kérdés** | **Részletek**
--- | ---
**Telepítés** | Telepítse a DPM/MABS egyetlen célra szolgáló gépre.<br/><br/> Ne telepítse a DPM/MABS tartományvezérlőre, az Alkalmazáskiszolgáló szerepkör telepítését futtató gépre egy Microsoft Exchange Servert vagy System Center Operations Managert futtató gépen vagy egy fürtcsomóponton.<br/><br/> [Tekintse át az összes DPM rendszerkövetelményt](/system-center/dpm/prepare-environment-for-dpm#dpm-server).
**Tartomány** | A DPM/MABS tartományhoz kell csatlakoznia. Először telepítse, majd csatlakoztassa a DPM/MABS-t egy tartományhoz. A DPM/MABS új tartományba való áthelyezése az üzembe helyezést követően nem támogatott.
**Storage** | A modern Backup Storage (MBS) a DPM 2016/MABS v2 és újabb verziók esetében támogatott. A MABS v1 esetében nem érhető el.
**MABS-frissítés** | Közvetlenül telepítheti a MABS v3 verziót, vagy frissíthet a MABS v3 verzióra a MABS v2-ről. [További információk](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS áthelyezése** | Ha a MABS egy új kiszolgálóra helyezi át, a tárterület megőrzése mellett az MBS használata támogatott.<br/><br/> A kiszolgálónak ugyanazzal a névvel kell rendelkeznie, mint az eredetinek. A név nem módosítható, ha ugyanazt a tárolót szeretné megőrizni, és ugyanazt a MABS-adatbázist használja az adathelyreállítási pontok tárolásához.<br/><br/> Szüksége lesz egy biztonsági másolatra a MABS-adatbázisról, mert vissza kell állítania.

>[!NOTE]
>A DPM/MABS-kiszolgáló átnevezése nem támogatott.

## <a name="mabs-support-on-azure-stack"></a>MABS-támogatás Azure Stack

A MABS üzembe helyezhető egy Azure Stack virtuális gépen, így egyetlen helyről kezelheti Azure Stack virtuális gépek és munkaterhelések biztonsági mentését.

**Összetevő** | **Részletek**
--- | ---
**MABS Azure Stack virtuális gépen** | Legalább a2-es méret. Javasoljuk, hogy kezdjen egy Windows Server 2012 R2 vagy Windows Server 2016 rendszerképpel az Azure Marketplace-ről.<br/><br/> A MABS virtuális gépen ne telepítsen semmilyen mást.
**MABS-tároló** | Használjon különálló Storage-fiókot a MABS virtuális géphez. A MABS-on futó MARS-ügynöknek ideiglenes tárterületre van szüksége a gyorsítótár helyéhez, valamint a felhőből visszaállított adatok tárolására.
**MABS Storage-készlet** | Az MABS-tároló méretét a MABS virtuális géphez csatolt lemezek száma és mérete határozza meg. Minden Azure Stack virtuálisgép-méret maximális számú lemezzel rendelkezik. Az a2 például négy lemez.
**MABS megőrzése** | A helyi MABS-lemezeken több mint öt napig ne őrizzen meg biztonsági mentést.
**MABS vertikális felskálázás** | Az üzemelő példány vertikális felskálázásához növelheti a MABS virtuális gép méretét. Például megváltoztathatja az A – D sorozatot.<br/><br/> Azt is megteheti, hogy az Azure-ban rendszeresen biztonsági mentést készít az Azure-ba. Szükség esetén további MABS-kiszolgálókat is telepíthet.
**.NET-keretrendszer a MABS-on** | A MABS virtuális gépnek a .NET-keretrendszer 3,3 SP1 vagy újabb verziójára van szüksége.
**MABS tartomány** | A MABS virtuális gépnek csatlakoznia kell egy tartományhoz. A rendszergazdai jogosultsággal rendelkező tartományi felhasználóknak telepíteniük kell a MABS a virtuális gépre.
**Azure Stack virtuális gép biztonsági mentése** | Biztonsági mentést készíthet a fájlokról, mappákról és alkalmazásokról.
**Támogatott biztonsági mentés** | Ezeket az operációs rendszereket olyan virtuális gépek támogatják, amelyekről biztonsági másolatot szeretne készíteni:<br/><br/> Windows Server Semi-Annual Channel (Datacenter, Enterprise, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Azure Stack virtuális gépek SQL Server támogatása** | SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1 biztonsági mentése.<br/><br/> Adatbázis biztonsági mentése és helyreállítása.
**SharePoint-támogatás Azure Stack virtuális gépekhez** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Egy farm, adatbázis, előtér és webkiszolgáló biztonsági mentése és helyreállítása.
**A virtuális gépek biztonsági mentésének hálózati követelményei** | Azure Stack munkaterhelésben lévő összes virtuális gépnek ugyanahhoz a virtuális hálózathoz kell tartoznia, és ugyanahhoz az előfizetéshez kell tartoznia.

## <a name="dpmmabs-networking-support"></a>DPM/MABS hálózatkezelési támogatás

### <a name="url-access"></a>URL-hozzáférés

A DPM-kiszolgáló/MABS-kiszolgálónak hozzá kell férnie ezekhez az URL-címekhez és IP-címekhez:

* URL-címek
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-támogatás

Az Azure ExpressRoute-on keresztül biztonsági mentést készíthet az adatairól a nyilvános (a régi áramkörökhöz elérhető) és a Microsoft-partnerek számára. A privát kapcsolaton keresztüli biztonsági mentés nem támogatott.

Nyilvános társítás esetén: a következő tartományokhoz/címekhez való hozzáférés biztosítása:

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

A Microsoft-partnerekkel válassza ki a következő szolgáltatásokat/régiókat és a vonatkozó közösségi értékeket:

- Azure Active Directory (12076:5060)
- Microsoft Azure régió (az Recovery Services-tároló helyének megfelelően)
- Azure Storage (az Recovery Services-tároló helyének megfelelően)

További információkért tekintse meg a [ExpressRoute útválasztási követelményeit](../expressroute/expressroute-routing.md).

>[!NOTE]
>Az új áramkörök esetében a nyilvános társítás elavult.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-kapcsolat Azure Backup

A biztonsági mentések megfelelő működéséhez szükség van a Azure Backup szolgáltatáshoz való kapcsolódásra, az Azure-előfizetésnek pedig aktívnak kell lennie. A következő táblázat a viselkedést mutatja be, ha ez a két dolog nem fordul elő.

**MABS az Azure-ba** | **Előfizetés** | **Biztonsági mentés/visszaállítás**
--- | --- | ---
Csatlakozva | Aktív | Biztonsági mentés DPM-vagy MABS-lemezre.<br/><br/> Biztonsági mentés az Azure-ba.<br/><br/> Visszaállítás a lemezről.<br/><br/> Visszaállítás az Azure-ból.
Csatlakozva | Lejárt/kiépítés | Nincs biztonsági másolat a lemezre vagy az Azure-ra.<br/><br/> Ha az előfizetés lejárt, a lemezről vagy az Azure-ból állíthatja vissza.<br/><br/> Ha az előfizetés le van szerelve, a lemezről vagy az Azure-ból nem lehet visszaállítani. Az Azure helyreállítási pontok törlődnek.
Több mint 15 napja nincs kapcsolat | Aktív | Nincs biztonsági másolat a lemezre vagy az Azure-ra.<br/><br/> A lemezről vagy az Azure-ból is visszaállítható.
Több mint 15 napja nincs kapcsolat | Lejárt/kiépítés | Nincs biztonsági másolat a lemezre vagy az Azure-ra.<br/><br/> Ha az előfizetés lejárt, a lemezről vagy az Azure-ból állíthatja vissza.<br/><br/> Ha az előfizetés le van szerelve, a lemezről vagy az Azure-ból nem lehet visszaállítani. Az Azure helyreállítási pontok törlődnek.

## <a name="domain-and-domain-trusts-support"></a>Tartományi és tartományi megbízhatósági kapcsolatok támogatása

|Követelmény |Részletek |
|---------|---------|
|Tartomány    | A DPM/MABS-kiszolgálónak Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 tartományban kell lennie.        |
|Tartományi megbízhatóság   |  A DPM/MABS támogatja az erdők közötti adatvédelmet, ha a különálló erdők között erdőszintű kétirányú megbízhatósági kapcsolatot hoz létre.   <BR><BR>   A DPM/MABS képes a kiszolgálók és munkaállomások különböző tartományokban való ellátására, egy olyan erdőben, amely kétirányú megbízhatósági kapcsolattal rendelkezik a DPM/MABS-kiszolgáló tartományával. A munkacsoportokban vagy nem megbízható tartományokban található számítógépek elleni védelemért lásd: [munkacsoportokban és nem megbízható tartományokban lévő munkaterhelések biztonsági mentése és visszaállítása.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br><br> A Hyper-V kiszolgálófürtök biztonsági mentéséhez a MABS-kiszolgálóval megegyező tartományban vagy egy megbízható vagy gyermektartomány-tartományban kell lennie. A nem megbízható tartományokban vagy munkaterhelésekben lévő kiszolgálók és fürtök biztonsági mentését az NTLM, önálló kiszolgálóhoz használható tanúsítványalapú hitelesítés, illetve önálló fürthöz használható tanúsítványalapú hitelesítés használatával teheti meg.  |

## <a name="dpmmabs-storage-support"></a>DPM/MABS-tároló támogatása

A rendszer a DPM/MABS biztonsági mentést készít a helyi lemezes tárolóban.

Az USB-vagy cserélhető meghajtók nem támogatottak.

Az NTFS-tömörítés nem támogatott a DPM/MABS kötetek esetében.

A BitLocker csak a tároló lemezének hozzáadása után engedélyezhető. A Hozzáadás előtt ne engedélyezze a BitLockert.

A hálózatra csatlakoztatott tároló (NAS) nem támogatott a DPM-tárolóban való használathoz.

**Storage** | **Részletek**
--- | ---
**MBS** | A modern Backup Storage (MBS) a DPM 2016/MABS v2 és újabb verziók esetében támogatott. A MABS v1 esetében nem érhető el.
**MABS-tároló az Azure-beli virtuális gépen** | A rendszer az DPM/MABS virtuális géphez csatolt Azure-lemezeken tárolja az adattárolást, amelyeket a DPM/MABS kezel. A DPM-vagy MABS-tárolóhoz használható lemezek számát a virtuális gép mérete korlátozza.<br/><br/> A2 méretű VM: 4 lemez; A3 méretű VM: 8 lemez; A4-es virtuális gép: 16 lemez, az egyes lemezek maximális mérete 1 TB. Ez határozza meg a rendelkezésre álló teljes biztonsági mentési tár készletét.<br/><br/> Azon adatok mennyisége, amelyekről biztonsági másolatot készíthet, a csatolt lemezek számától és méretétől függ.
**MABS adatmegőrzés az Azure-beli virtuális gépen** | Javasoljuk, hogy a DPM/MABS Azure-lemezen egy napig őrizze meg az adatait, és a DPM/MABS biztonsági mentését a tárolóba a hosszú megőrzés érdekében. Így nagyobb mennyiségű adattal védhető a Azure Backup való kiszervezéssel.

### <a name="modern-backup-storage-mbs"></a>Modern biztonsági mentési tár (MBS)

A DPM 2016/MABS v2 (Windows Server 2016 rendszeren futó) és újabb verziók esetében kihasználhatja a modern Backup Storage (MBS) szolgáltatás előnyeit.

- Az MBS biztonsági mentéseit egy rugalmas fájlrendszerű (ReFS) lemezen tárolja a rendszer.
- Az MBS a ReFS blokkolja a klónozást a gyorsabb biztonsági mentéshez és a tárhely hatékonyabb kihasználásához.
- Amikor köteteket ad hozzá a helyi DPM-vagy MABS-tárolóhoz, a meghajtóbetűjelekkel konfigurálja azokat. Ezt követően különböző köteteken is konfigurálhatja a munkaterhelés-tárolót.
- Amikor védelmi csoportokat hoz létre az DPM/MABS való biztonsági mentéshez, válassza ki a használni kívánt meghajtót. Előfordulhat például, hogy a biztonsági másolatokat az SQL vagy más magas IOPS számítási feladatokhoz nagy teljesítményű meghajtón tárolja, és az alacsonyabb teljesítményű meghajtókon ritkábban készített munkaterheléseket tárol.

## <a name="supported-backups-to-mabs"></a>A MABS által támogatott biztonsági másolatok

A Azure Backup Server védelemmel ellátott különböző kiszolgálókról és munkaterhelésekről a [Azure Backup Server védelmi mátrixban](./backup-mabs-protection-matrix.md#protection-support-matrix)találhat további információt.

## <a name="supported-backups-to-dpm"></a>A DPM által támogatott biztonsági másolatok

A Data Protection Managerrel védhető különböző kiszolgálókkal és munkaterhelésekkel kapcsolatos információkért tekintse meg a [DPM biztonsági mentését](/system-center/dpm/dpm-protection-matrix)ismertető cikket.

- A DPM/MABS által készített fürtözött munkaterheléseknek ugyanabban a tartományban kell lenniük, mint a DPM/MABS, vagy egy alárendelt/megbízható tartományban.
- Az NTLM/tanúsítványalapú hitelesítés használatával biztonsági mentést készíthet a nem megbízható tartományokban vagy munkacsoportokban található adatvédelemről.

## <a name="deduplicated-volumes-support"></a>Deduplikált kötetek támogatása

>[!NOTE]
> A deduplikálás támogatása a MABS az operációs rendszer támogatásának függvénye.

### <a name="for-ntfs-volumes"></a>NTFS-kötetek esetén

| A védett kiszolgáló operációs rendszere  | A MABS-kiszolgáló operációs rendszere  | MABS verziója  | Deduplikációs támogatás |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| Windows Server 2019                       | Windows Server 2019                  | MABS v3            | Y                    |
| Windows Server 2016                       | Windows Server 2019                  | MABS v3            | Y                   |
| Windows Server 2012 R2                    | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2012                       | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2019                       | Windows Server 2016                  | MABS v3            | Y * *                  |
| Windows Server 2016                       | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012 R2                    | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012                       | Windows Server 2016                  | MABS v3            | Y                    |

- \* Ha a WS 2019-on futó MABS v3-as verzióval védett WS 2016 NTFS-kötetet, a helyreállítások befolyásolhatják. Kijavítottuk a helyreállítások nem deduplikált módon történő helyreállítását. Ha erre a javításra van szüksége a MABS v3 UR1-on, MABS-támogatást érhet el.
- \** Ha a WS 2016-es verzióban található MABS v3-as verzióval rendelkező WS 2019 NTFS deduplikált kötetet védi, a biztonsági mentések és a visszaállítások nem lesznek kiválasztva. Ez azt jelenti, hogy a biztonsági másolatok több helyet foglalnak el a MABS-kiszolgálón, mint az eredeti NTFS deduped kötet.

**Probléma**: Ha a védett kiszolgáló operációs rendszerét a windows Server 2016-ről a windows Server 2019-re frissíti, akkor a rendszer az NTFS deduplikált kötet biztonsági másolatát fogja érinteni a deduplikálás logikájának változásai miatt.

**Megkerülő megoldás**: Ha ezt a javítást szeretné elérni a MABS v3 UR1, akkor elérheti a MABS-támogatást.

### <a name="for-refs-volumes"></a>ReFS-kötetek esetében

>[!NOTE]
> Azonosítottank néhány problémát a deduplikált ReFS-kötetek biztonsági mentésével kapcsolatban. Dolgozunk a kijavításán, és ezt a szakaszt azonnal frissítjük, amint elérhetővé tettük a javítást. Addig is eltávolítjuk a deduplikált ReFS-kötetek biztonsági mentésének támogatását a MABS V3-ból.
>
> A MABS v3 UR1 és újabb verziója továbbra is támogatja a normál ReFS-kötetek védelmét és helyreállítását.

## <a name="next-steps"></a>Következő lépések

- [További](backup-architecture.md#architecture-back-up-to-dpmmabs) információ a MABS architektúráról.
- [Tekintse át](backup-support-matrix-mars-agent.md) a Mars-ügynök által támogatott tudnivalókat.
- [Állítson be](backup-azure-microsoft-azure-backup.md) egy MABS-kiszolgálót.
- [DPM beállítása](/system-center/dpm/install-dpm).

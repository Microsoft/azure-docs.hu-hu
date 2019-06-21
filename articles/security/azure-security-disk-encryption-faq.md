---
title: Gyakori kérdések – IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk a Microsoft Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek kapcsolatos gyakori kérdésekre adott válaszokat ismerteti.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 98acc7f6dd5ec7cf3702bbcbe60e2739732512e2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294912"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Az Azure Disk Encryption IaaS virtuális gépekhez – gyakori kérdések

Ez a cikk az Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek kapcsolatos gyakori kérdések (GYIK) választ ad. Ezzel a szolgáltatással kapcsolatos további információkért lásd: [Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Hol található az Azure Disk Encryption az általánosan (elérhetővé tétel GA)?

Az általános rendelkezésre állás érdekében minden nyilvános Azure-régióban az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Melyik felhasználó érhetők el az Azure Disk Encryption?

Az Azure Disk Encryption általánosan elérhető az Azure Resource Manager sablonok, az Azure PowerShell és Azure parancssori felület támogatja. A különböző felhasználói élmények rugalmasságot biztosítanak. Lemez titkosítása az IaaS virtuális gépek három különböző lehetőségek állnak rendelkezésre. A felhasználói élmény és lépésenkénti útmutatóját az Azure Disk Encryption elérhető további információkért lásd: [engedélyezése Azure Disk Encryption a Windows](azure-security-disk-encryption-windows.md) és [engedélyezése az Azure Disk Encryption for Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Az Azure Disk Encryption mennyibe?

Semmilyen díjat nem az Azure Disk Encryption Virtuálisgép-lemezek titkosítása, de számítunk fel díjat a társított Azure Key Vault használatát. Az Azure Key Vault költségek további információkért lásd: a [Key Vault díjszabását ismertető](https://azure.microsoft.com/pricing/details/key-vault/) lapot.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan lehet elindítani az Azure Disk Encryption használatával?

Első lépések, olvassa el a [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>A rendszerindító és az adatok kötetek az Azure Disk Encryption használatával titkosítsa?

Adatok és rendszerindító kötetek Igen, a Windows és Linux rendszerű IaaS virtuális gépek használatával titkosítsa. Windows-beli virtuális gépek az operációsrendszer-kötet titkosítása nélkül az adatok nem titkosíthatók. Linux rendszerű virtuális gépekhez legyen az operációs rendszer kötetének titkosításához először nélkül az adatmennyiség titkosíthatja. A titkosított operációsrendszer-kötet linuxhoz, miután letiltja a titkosítást az operációs rendszer kötet Linux rendszerű IaaS virtuális gépekhez nem támogatott. Linux rendszerű virtuális gépek méretezési csoportokban csak az adatmennyiség titkosíthatók.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Egy nem csatlakoztatott kötetet, az Azure Disk Encryption használatával titkosítsa?

Nem, az Azure Disk Encryption csak a csatlakoztatott kötetek titkosítja.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hogyan elforgatása titkok és titkosítási kulcsokat?

Titkos kulcsok rotálására, csak hívja ugyanazt a parancsot, amellyel eredetileg engedélyezi a lemeztitkosítást, adjon meg egy másik Key Vaultot. A kulcstitkosítási kulcs rotálása, ugyanazt a parancsot, amellyel eredetileg engedélyezi a lemeztitkosítást, adja meg az új kulcstitkosítási hívjuk. 

>[!WARNING]
> - Ha korábban már használt [az Azure Disk Encryption az Azure AD-alkalmazás](azure-security-disk-encryption-prerequisites-aad.md) a virtuális gép titkosítása az Azure AD hitelesítő adatok megadásával kell továbbra is használja ezt a beállítást a virtuális gép titkosítására. Nem használhat [az Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) a titkosított virtuális gépen, ez nem támogatott forgatókönyv, azaz a átváltani AAD-alkalmazás számára a titkosított virtuális gép nem támogatott még.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hogyan adja hozzá vagy távolítsa el a kulcsalapú titkosítás kulcsa, ha nem eredetileg szükségem?

Szeretne hozzáadni egy kulcsalapú titkosítás kulcsa, hívja meg a engedélyezése parancsot újra a kulcstitkosítási kulcs paraméter átadásával. Távolítsa el a kulcsalapú titkosítás kulcsa, hívja az enable parancsot újra a kulcstitkosítási kulcs paraméter nélkül.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Az Azure Disk Encryption lehetővé teszi, hogy a saját kulcs (használatának BYOK)?

Igen, a saját kulcs titkosítási kulcsok is megadhatja. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. További információ a fő titkosítási kulcsok esetében, tekintse meg [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Használható az Azure által létrehozott fő titkosítási kulcsot?

Igen, az Azure Key Vault segítségével hozzon létre egy kulcsalapú titkosítás kulcsa az Azure disk encryption használatra. Ezek a kulcsok számára biztosít védelmet az Azure Key Vaultban, azaz az Azure Disk Encryption key áruházban. A kulcsalapú titkosítás kulcsa további információkért lásd: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Használható egy helyszíni kulcskezelő szolgáltatás vagy a HSM titkosítási kulcsok védelme?

A helyszíni kulcskezelő szolgáltatás vagy a HSM nem használható az Azure Disk Encryption titkosítási kulcsok védelme. Az Azure Key Vault szolgáltatás segítségével csak biztosítja a titkosítási kulcsok védelmét. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Mik azok az Előfeltételek konfigurálása az Azure Disk Encryption?

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg a [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md) hozzon létre egy új kulcstartót cikket, vagy egy meglévő lemez titkosítási hozzáférés engedélyezése a titkosításhoz, és a kulcsok és titkok védelme key vault beállítása. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Mik azok az Előfeltételek konfigurálása az Azure Disk Encryption az Azure AD-alkalmazás (előző kiadás)?

Nincsenek az Azure Disk Encryption előfeltételeinek. Tekintse meg a [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites-aad.md) cikk létrehozása az Azure Active Directory-alkalmazás, hozzon létre egy új kulcstartót, vagy a titkosítási lemezelérést engedélyezheti a titkosítást egy meglévő key vault beállítása és titkos kulcsok védelme és kulcsok. A kulcstitkosítási kulcs támogatási példák a további információkért lásd: [áttekintése az Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Használja az Azure AD-alkalmazás (előző kiadás) továbbra is támogatott az Azure Disk Encryption szolgáltatást?
Igen. Az Azure AD-alkalmazás használatával lemeztitkosítás továbbra is támogatott. Azonban új virtuális gépek titkosításához javasoljuk az új módszer helyett az Azure AD-alkalmazás titkosítására használja. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>A titkosítás nélkül az Azure AD-alkalmazás Azure AD-alkalmazás a titkosított virtuális gépeket telepíthet át?
  Jelenleg nem áll rendelkezésre egy közvetlen áttelepítési útvonal az Azure AD-alkalmazás, a titkosítás nélkül az Azure AD-alkalmazás titkosított gépek. Ezenkívül nem áll rendelkezésre egy közvetlen útvonal, a titkosítás nélkül az Azure AD-alkalmazás a titkosítást, AD-alkalmazás. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Milyen az Azure PowerShell-verzió támogatja az Azure Disk Encryption?

Az Azure PowerShell SDK legújabb verziójának segítségével konfigurálhatja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases). Az Azure Disk Encryption van *nem* Azure SDK 1.1.0-s verzió által támogatott.

> [!NOTE]
> A Linux Azure lemez titkosítási előzetes bővítmény "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" elavult. Az Azure disk encryption előzetes kiadásban ez a bővítmény tettek közzé. A bővítmény előzetes verzióját a tesztelési vagy éles környezetben nem használjon.

> A különféle telepítési környezetekhez hasonlóan az Azure Resource Manager (ARM), ahol nincs szüksége az Azure disk encryption bővítményt a Linuxos IaaS virtuális gép titkosításának Linux rendszerű virtuális gép üzembe helyezéséhez, az Azure disk encryption támogatott éles bővítményt kell használnia" Microsoft.Azure.Security.AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Használhatom az Azure Disk Encryption saját egyéni Linux-rendszerképen?

Az egyéni Linux-rendszerképek az Azure Disk Encryption, nem lehet alkalmazni. Csak a katalógus típusú Linux-rendszerképeket emelte ki a korábban a támogatott disztribúciók esetében támogatottak. Egyéni Linux-rendszerképek jelenleg nem támogatottak.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Használhatom-e frissítéseket Linux Red Hat használó virtuális gép a yum használatával frissítést?

Igen, végezhet a yum használatával frissítése egy Red Hat Linux rendszerű virtuális gépen.  További információkért lásd: [tűzfal mögött található Linux-csomagkezelés](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Mi a javasolt az Azure disk encryption munkafolyamat Linux?

A következő munkafolyamatot javasoljuk, hogy a legjobb eredmények Linux rendszeren:
* Indítsa el a szükséges operációs rendszer disztribúció és verzió a megfelelő módosítás nélküli tőzsdei katalógus-rendszerképből
* Készítsen biztonsági másolatot minden olyan csatlakoztatott meghajtók, a rendszer titkosítja.  Ez vissza akár lehetővé teszi a helyreállítási Ha egy sikertelen, például ha a virtuális gép újraindul, titkosítási befejezése előtt.
* Titkosítása (is igénybe vehet néhány Virtuálisgép-jellemző és bármely más csatolt lemez mérete attól függően órákig vagy akár napokig)
* Testre szabhatja, és igény szerint adjon hozzá szoftvereket a lemezképet.

Ha ezt a munkafolyamatot nem lehetséges, a függő entitások [a Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE) a platform tárolási fiók réteg lehet a teljes lemeztitkosítás, dm-crypt használata helyett.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Mi az a lemez "Rendelkeznek BEk-kel kötet" vagy "/ mnt/azure_bek_disk"?

"Rendelkeznek BEk-kel kötet" Windows vagy a "/ mnt/azure_bek_disk" Linux olyan helyi kötet, amely biztonságosan tárolja a titkosítási kulcsok titkosított Azure IaaS virtuális gépekhez.
> [!NOTE]
> Nem törölheti vagy szerkesztheti a bármely tartalmát ezt a lemezt. Válassza le a lemez nem, mivel a titkosítási kulcs jelenlét szükséges az IaaS virtuális gép titkosítási műveleteket.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Milyen titkosítási módszert használ az Azure Disk Encryption?

A Windows, ADE használ a BitLocker AES256 titkosítási módszert (a Windows Server 2012 előtti verziókról AES256WithDiffuser). Linux rendszeren ADE használ az aes-xts-plain64 visszafejtése alapértelmezett egy 256 bites kötetfőkulccsal.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Ha EncryptFormatAll használja, és adja meg a minden kötet esetében, azt az adatot töröl már titkosított adatok meghajtókon?
Nem, adatokat a rendszer nem törli a már Azure Disk Encryption használatával titkosított meghajtók. Hogyan EncryptFormatAll nem újratitkosítása az operációs rendszer meghajtójának hasonlóan, nem fog újra már a titkosított adatok meghajtójának titkosításához. További információkért lásd: a [EncryptFormatAll feltételek](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>XFS fájlrendszer támogatott?
Lemez adattitkosítás csak a EncryptFormatAll XFS köteteket támogatja. Ez formázza újra a kötetet, bármely meglévő adatok törlése korábban már létezik. További információkért lásd: a [EncryptFormatAll feltételek](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Biztonsági mentése és visszaállítása egy titkosított virtuális gép? 

Az Azure Backup lehetővé teszi a biztonsági mentése és visszaállítása titkosított virtuális gép belül az azonos előfizetésben és régióban.  Útmutatásért lásd: [biztonsági mentése és visszaállítása titkosított virtuális gépek az Azure Backup szolgáltatással](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Egy titkosított virtuális gép helyreállítása egy másik régióban jelenleg nem támogatott.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Hol tudhatok meg kérdéseket tehet fel, vagy visszajelzést?

Kérdéseket tehet fel, vagy visszajelzést adhat a [az Azure Disk Encryption fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, további információt az Azure Disk Encryption kapcsolatos leggyakoribb kérdések. Ezzel a szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Disk Encryption áttekintése](azure-security-disk-encryption-overview.md)
- [Az Azure Security Centerben lemeztitkosítás alkalmazása](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-beli adat-titkosítás inaktív állapotban](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

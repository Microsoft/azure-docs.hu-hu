---
title: A felhasználó által a fájl szintjén elvégezhető műveletek szabályozása – Azure-fájlmegosztás
description: Ismerje meg, hogyan konfigurálhatja a Windows ACL-engedélyeket helyszíni AD DS hitelesítésre az Azure-fájlmegosztás számára. Lehetővé teszi, hogy kihasználhassa a részletes hozzáférés-vezérlés előnyeit.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 698b4ebedfc9b41e8c5732a0a81226a971d65585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470758"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Harmadik rész: a könyvtár-és a fájl szintű engedélyek konfigurálása SMB protokollon keresztül 

A cikk elkezdése előtt győződjön meg arról, hogy végrehajtotta az előző cikket, [rendeljen hozzá megosztási szintű engedélyeket egy identitáshoz](storage-files-identity-ad-ds-assign-permissions.md) , hogy megbizonyosodjon arról, hogy a megosztási szintű engedélyek érvényben vannak.

A megosztási szintű engedélyek Azure RBAC való hozzárendelését követően a részletes hozzáférés-vezérlés kihasználása érdekében konfigurálnia kell a megfelelő Windows-ACL-eket a gyökér, a könyvtár vagy a fájl szintjén. Tekintse át az Azure RBAC-megosztási szintű engedélyeit, amelyek meghatározzák, hogy a felhasználók hozzáférhetnek-e a megosztáshoz. Míg a Windows ACL-ek részletesebben működnek, hogy megtudja, milyen műveleteket végezhet a felhasználó a címtár vagy a fájl szintjén. A megosztási szint és a fájl/könyvtár szintű engedélyek akkor is érvénybe lépnek, ha egy felhasználó egy fájlhoz vagy könyvtárhoz próbál hozzáférni, tehát ha a kettő között különbség van, akkor csak a legszigorúbb korlátozás lesz érvényben. Ha például egy felhasználó rendelkezik írási/olvasási hozzáféréssel a fájl szintjén, de csak egy megosztási szinten olvas, akkor csak a fájlt tudja olvasni. Ugyanez igaz lehet, ha fordították, és a felhasználó olvasási/írási hozzáféréssel rendelkezik a megosztás szintjén, de csak a fájl szintjén olvashatja el.

## <a name="azure-rbac-permissions"></a>Azure RBAC engedélyek

A következő táblázat tartalmazza az ehhez a konfigurációhoz kapcsolódó Azure RBAC-engedélyeket:


| Beépített szerepkör  | NTFS-engedély  | Eredményül kapott hozzáférés  |
|---------|---------|---------|
|Storage-fájladatok SMB-megosztásának olvasója | Teljes hozzáférés, módosítás, olvasás, írás, végrehajtás | Olvasás és végrehajtás  |
|     |   Olvasás |     Olvasás  |
|Storage-fájladatok SMB-megosztásának közreműködője  |  Teljes hozzáférés    |  Módosítás, olvasás, írás, végrehajtás |
|     |  Módosítás         |  Módosítás    |
|     |  Olvasás és végrehajtás |  Olvasás és végrehajtás |
|     |  Olvasás           |  Olvasás    |
|     |  Írás          |  Írás   |
|Storage-fájladatok SMB-megosztásának emelt szintű közreműködője | Teljes hozzáférés  |  Módosítás, olvasás, írás, szerkesztés, végrehajtás |
|     |  Módosítás          |  Módosítás |
|     |  Olvasás és végrehajtás  |  Olvasás és végrehajtás |
|     |  Olvasás            |  Olvasás   |
|     |  Írás           |  Írás  |



## <a name="supported-permissions"></a>Támogatott engedélyek

Azure Files támogatja az alapszintű és a speciális Windows ACL-ek teljes készletét. Az Azure-fájlmegosztás könyvtárain és fájljain a Windows ACL-eket megtekintheti és konfigurálhatja úgy, hogy összekapcsolja a megosztást, majd a Windows fájlkezelővel, a Windows [icacls](/windows-server/administration/windows-commands/icacls) parancs futtatásával vagy a [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) parancs használatával. 

Az ACL-ek rendszergazdai jogosultságokkal való konfigurálásához a megosztást a tartományhoz csatlakoztatott virtuális gépen lévő Storage-fiók kulcsa használatával kell csatlakoztatnia. Kövesse a következő szakaszban található utasításokat egy Azure-fájlmegosztás parancssorból történő csatlakoztatásához és a Windows ACL-ek konfigurálásához.

A fájlmegosztás gyökérkönyvtárában a következő engedélyek szerepelnek:

- Builtin\rendszergazda: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT AUTHORITY\Authenticated-felhasználók: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (F)
- LÉTREHOZÓ TULAJDONOS: (OI) (CI) (IO) (F)

|Felhasználók|Definíció|
|---|---|
|BUILTIN\Rendszergazdák|Minden olyan felhasználó, aki a helyszíni AD DS környezet tartományi rendszergazdái.
|BUILTIN\Users|Beépített biztonsági csoport az AD-ben. Alapértelmezés szerint NT AUTHORITY\Authenticated-felhasználókat tartalmaz. A hagyományos fájlkiszolgálók esetében kiszolgálónként konfigurálhatja a tagság definícióját. Azure Files esetében nem létezik egy üzemeltetési kiszolgáló, ezért a BUILTIN\Users ugyanazokat a felhasználókat tartalmazza, mint az NT AUTHORITY\Authenticated-felhasználók.|
|NT AUTHORITY\SYSTEM|A fájlkiszolgáló operációs rendszerének szolgáltatási fiókja. Ez a szolgáltatásfiók Azure Files környezetben nem alkalmazható. A gyökérkönyvtárban található, hogy konzisztensek legyenek a Windows-fájlok kiszolgálói felületének hibrid forgatókönyvek esetén.|
|NT AUTHORITY\Authenticated-felhasználók|Az AD összes olyan felhasználója, aki érvényes Kerberos-jogkivonatot kaphat.|
|LÉTREHOZÓ TULAJDONOS|Minden objektumhoz tartozik tulajdonos az adott objektumhoz. Ha az objektumon a "létrehozó tulajdonos" hozzáférés-vezérlési listák vannak társítva, akkor az objektum tulajdonosának felhasználója rendelkezik az ACL által meghatározott objektum engedélyeivel.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Fájlmegosztás csatlakoztatása a parancssorból

`net use`Csatlakoztassa az Azure-fájlmegosztást a Windows parancs használatával. Ne felejtse el lecserélni az alábbi példában szereplő helyőrző értékeket a saját értékeire. A fájlmegosztás csatlakoztatásával kapcsolatos további információkért lásd: [Azure-fájlmegosztás használata a Windowsban](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Ha a Azure Fileshoz való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [a hibaelhárítási eszközt, amelyet Azure Files csatlakoztatási hibákhoz tettünk közzé a Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)rendszeren. Az 445-as port blokkolása esetén [útmutatást](./storage-files-faq.md#on-premises-access) is biztosítunk a forgatókönyvek megoldásához. 

## <a name="configure-windows-acls"></a>Windows ACL-ek konfigurálása

Miután a fájlmegosztás csatlakoztatva lett a Storage-fiók kulcsához, konfigurálnia kell a Windows ACL-eket (más néven NTFS-engedélyeket). A Windows ACL-eket Windows fájlkezelővel vagy icacls-vel is konfigurálhatja.

Ha a helyi fájlkiszolgálón lévő címtárakkal vagy fájlokkal rendelkezik a AD DS identitásokkal konfigurált Windows DACL-ekkel, akkor átAzure Files másolhatja az ACL-eket a hagyományos fájlmásolás-eszközökkel, például a Robocopy vagy az [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases)használatával. Ha a címtárakat és a fájlokat a rendszer a Azure File Syncon Azure Fileson, akkor az ACL-eket a rendszer a natív formában tartja át és őrzi meg.

### <a name="configure-windows-acls-with-icacls"></a>Windows ACL-ek konfigurálása icacls-val

A következő Windows-paranccsal teljes körű engedélyeket adhat meg a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

A Windows ACL-ek és a különböző típusú támogatott engedélyek beállításával kapcsolatos további információkért tekintse meg [az icacls parancssori útmutatója](/windows-server/administration/windows-commands/icacls)című témakört.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Windows-ACL-ek konfigurálása a Windows fájlkezelővel

A Windows fájlkezelővel teljes hozzáférést biztosíthat a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is. Ha nem tudja megfelelően betölteni az AD-tartomány adatait a Windows fájlkezelőben, ez valószínűleg a helyszíni Active Directory-környezet megbízhatósági beállításai miatt fordul elő. Az ügyfélszámítógép nem tudta elérni Azure Files hitelesítéshez regisztrált AD-tartományvezérlőt. Ebben az esetben a Windows ACL-ek configurating használjon icacls-ket.

1. Nyissa meg a Windows fájlkezelő alkalmazást, és kattintson a jobb gombbal a fájlra/könyvtárra, és válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Biztonság** fület.
1. Válassza a **Szerkesztés lehetőséget.** az engedélyek módosításához.
1. Módosíthatja a meglévő felhasználók engedélyeit, vagy kiválaszthatja a **Hozzáadás...** lehetőséget az új felhasználók engedélyeinek megadásához.
1. Az új felhasználók hozzáadására szolgáló kérdés ablakban adja meg azt a cél felhasználónevet, amely számára engedélyezni kívánja az engedélyek megadását az **adja meg a kijelölendő objektumok nevét** mezőbe, **majd válassza a Névellenőrzés lehetőséget** a MEGcélzott felhasználó teljes UPN-nevének megkereséséhez.
1.    Válassza az **OK** lehetőséget.
1.    A **Biztonság** lapon válassza ki az összes olyan engedélyt, amely számára engedélyezni szeretné az új felhasználót.
1.    Kattintson az **Alkalmaz** gombra.


## <a name="next-steps"></a>Következő lépések

Most, hogy a funkció engedélyezve és konfigurálva van, folytassa a következő cikkel, amelyben csatlakoztathatja az Azure-fájlmegosztást egy tartományhoz csatlakoztatott virtuális gépről.

[Negyedik rész: fájlmegosztási csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről](storage-files-identity-ad-ds-mount-file-share.md)

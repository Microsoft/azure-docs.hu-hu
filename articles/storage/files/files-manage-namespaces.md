---
title: Az DFS-N használata Azure Files
description: A DFS-N gyakori Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741777"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Elosztott fájlrendszerbeli névterek használata Azure Files
[Az elosztott fájlrendszerek](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)névterei ( más néven elosztott fájlrendszerbeli névterek vagy DFS-N) egy Windows Server kiszolgálói szerepkör, amely széles körben használatos az SMB-fájlmegosztások üzembe helyezésének és karbantartásának egyszerűsítésére éles környezetben. Az Elosztott fájlrendszerbeli névterek egy tárolónévtér-virtualizálási technológia, ami azt jelenti, hogy lehetővé teszi egy közvetett réteg beállítását a fájlmegosztások UNC elérési útja és maguk a tényleges fájlmegosztások között. Az elosztott fájlrendszerbeli névterek együttműködnek az SMB-fájlmegosztásokkal, függetlenek ezektől a fájlmegosztásoktól: használhatók helyszíni Windows-fájlkiszolgálón üzemeltetett SMB-megosztásokkal Azure File Sync-val vagy anélkül, közvetlenül az Azure-fájlmegosztásokkal, az Azure NetApp Files-ben vagy más külső ajánlatokban üzemeltetett SMB-fájlmegosztásokkal, sőt akár más felhőkben üzemeltetett fájlmegosztásokkal is. 

Az elosztott fájlrendszerbeli névterek lényegében egy felhasználóbarát UNC elérési út ( például ) és az SMB-megosztás (például vagy ) mögöttes UNC elérési útja közötti leképezést `\\contoso\shares\ProjectX` `\\Server01-Prod\ProjectX` `\\storageaccount.file.core.windows.net\projectx` biztosít. Amikor a végfelhasználó a fájlmegosztáshoz szeretne navigálni, beírja a felhasználóbarát UNC elérési utat, de az SMB-ügyfél hozzáfér a leképezés mögöttes SMB-útvonalhoz. Ezt az alapfogalmat kiterjesztheti egy meglévő fájlkiszolgáló nevének (például ) nevére `\\MyServer\ProjectX` is. Ezzel a képességgel a következő forgatókönyveket érheti el:

- Adja meg a logikai adatkészlet migrálásbiztos nevét. Ebben a példában egy ehhez hasonló leképezése `\\contoso\shares\Engineering` van, amely a következőre van leképezve: `\\OldServer\Engineering` . Miután befejezte az áttelepítést a Azure Files, módosíthatja a leképezést, hogy a felhasználóbarát UNC elérési út a(Azure Files) `\\storageaccount.file.core.windows.net\engineering` legyen. Amikor egy végfelhasználó hozzáfér a felhasználóbarát UNC elérési úthoz, a rendszer zökkenőmentesen átirányítja őket az Azure-fájlmegosztás elérési útjára.

- Hozzon létre egy közös nevet egy olyan logikai adatkészlethez, amely több kiszolgálóra van elosztva különböző fizikai helyeken, például Azure File Sync. Ebben a példában például egy név több UNC elérési útra van leképezve, például `\\contoso\shares\FileSyncExample` `\\FileSyncServer1\ExampleShare` , , `\\FileSyncServer2\DifferentShareName` `\\FileSyncServer3\ExampleShare` . Amikor a felhasználó hozzáfér a felhasználóbarát UNC-hez, kap egy listát a lehetséges UNC elérési utakról, és az Windows Server Active Directory (AD) helydefiníciói alapján kiválasztja a hozzájuk legközelebb eső elérési utat.

- Az adatok logikai készletének kiterjesztése méret, I/O vagy más skálázható küszöbértékek között. Ez gyakori a felhasználói könyvtárak esetében, ahol minden felhasználó saját mappát kap egy megosztáson, vagy ideiglenes megosztásokkal, ahol a felhasználók tetszőleges helyet kapnak az ideiglenes adatokkal kapcsolatos igények kezeléséhez. Az elosztott fájlrendszerbeli névterekkel több mappát fűzhet össze egy összetartó névtérbe. A leképezi például `\\contoso\shares\UserShares\user1` a következőt: `\\storageaccount.file.core.windows.net\user1` , `\\contoso\shares\UserShares\user2` leképezve a következőre: `\\storageaccount.file.core.windows.net\user2` , és így tovább.  

Az alábbi videóban egy példát láthat arra, hogyan használhatók az elosztott fájlrendszerbeli névterek a Azure Files üzemelő példányával.

[![A DFS-N beállításának bemutatója a Azure Files – kattintson a lejátszáshoz!](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Ugorjon a videóban 10:10-re az Elosztott fájlrendszerbeli névterek beállításának lépéseiért.

Ha már rendelkezik elosztott fájlrendszerbeli névtér használatával, nincs szükség különleges lépésekre a Azure Files és File Sync. Ha helyszíni azure-fájlmegosztáshoz fér hozzá, a szokásos hálózati szempontok érvényesek; További [Azure Files tekintse meg a hálózati szempontokat.](./storage-files-networking-overview.md)

## <a name="namespace-types"></a>Névtértípusok
Az Elosztott fájlrendszerbeli névterek két fő névtértípust létezik:

- **Tartományalapú névtér:** A saját tartományának Windows Server AD névtér. Az AD részeként üzemeltetett névterek UNC elérési útjuk lesz, amely tartalmazza a tartomány nevét ,például , ha a `\\contoso.com\shares\myshare` `contoso.com` tartománya . A tartományalapú névterek nagyobb méretkorlátokat és beépített redundanciát támogatnak az AD-en keresztül. A tartományalapú névterek nem lehet fürtözött erőforrás a feladatátvevő fürtön. 
- **Önálló névtér:** Egy különálló kiszolgálón üzemeltetett névtér, amely nem a kiszolgáló Windows Server AD. Az önálló névterek neve az önálló kiszolgáló neve (például ) alapján lesz `\\MyStandaloneServer\shares\myshare` elnevezve, ahol az önálló kiszolgáló neve `MyStandaloneServer` . Az önálló névterek alacsonyabb skálázási célokat támogatnak, mint a tartományalapú névterek, de fürtözött erőforrásként üzemeltetheti őket egy feladatátvevő fürtön.

## <a name="requirements"></a>Követelmények
Ha elosztott fájlrendszerbeli névtereket Azure Files és File Sync használni, a következő erőforrásokkal kell rendelkezik:

- Egy Active Directory tartomány. Ez bárhol üzemeltethető, például a helyszínen, egy Azure-beli virtuális gépen (VM), vagy akár egy másik felhőben is.
- Egy Windows Server, amely képes a névtérre. Az elosztott fájlrendszerbeli névterek gyakori telepítési mintája az Active Directory-tartományvezérlő használata a névterek gazdagépeként, a névterek azonban bármely olyan kiszolgálóról telepíthetők, amelyeken telepítve van az Elosztott fájlrendszerbeli névterek kiszolgálói szerepkör. Az Elosztott fájlrendszerbeli névterek a Windows Server összes támogatott verziójában elérhetők.
- Tartományhoz csatlakozott környezetben üzemeltetett SMB-fájlmegosztás, például egy tartományhoz csatlakozott tárfiókban üzemeltetett Azure-fájlmegosztás, vagy egy tartományhoz csatlakozott Windows-fájlkiszolgálón tárolt fájlmegosztás Azure File Sync. További információ a tárfiók tartományhoz való csatlakozásával kapcsolatban: [Identitásalapú hitelesítés.](storage-files-active-directory-overview.md) A Windows-fájlkiszolgálók ugyanúgy csatlakoznak a tartományhoz, függetlenül attól, hogy az adott Azure File Sync.
- Az elosztott fájlrendszerbeli névterekkel használni kívánt SMB-fájlmegosztásnak elérhetőnek kell lennie a helyszíni hálózatokról. Ez elsősorban az Azure-fájlmegosztások esetében jelent problémát, azonban technikailag az Azure-ban vagy bármely más felhőben üzemeltetett fájlmegosztásra vonatkozik. A hálózattal kapcsolatos további információkért lásd: Hálózati szempontok [közvetlen hozzáféréshez.](storage-files-networking-overview.md)

## <a name="install-the-dfs-namespaces-server-role"></a>Az Elosztott fájlrendszerbeli névterek kiszolgálói szerepkör telepítése
Ha már használ elosztott fájlrendszerbeli névtereket, vagy be szeretne állítani elosztott fájlrendszerbeli névtereket a tartományvezérlőn, nyugodtan kihagyhatja ezeket a lépéseket.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az Elosztott fájlrendszerbeli névterek kiszolgálói szerepkör telepítéséhez nyissa meg a Kiszolgálókezelőt a kiszolgálón. Válassza **a Kezelés,** majd a **Szerepkörök és szolgáltatások hozzáadása lehetőséget.** Az eredményül kapott varázsló végigvezeti az elosztott fájlrendszerbeli névterek futtatásához és kezeléséhez szükséges Windows-összetevők telepítésének folyamatán. 

A **telepítővarázsló** Telepítés típusa szakaszában  válassza a Szerepköralapú vagy szolgáltatásalapú telepítés választógombot, majd kattintson a Tovább **gombra.** A Kiszolgáló **kiválasztása szakaszban** válassza ki a kívánt kiszolgáló(k)t, amelyre telepíteni szeretné az Elosztott fájlrendszerbeli névterek kiszolgálói szerepkört, majd válassza a Tovább **lehetőséget.** 

A **Kiszolgálói szerepkörök szakaszban** jelölje ki és jelölje be az Elosztott fájlrendszerbeli **névterek szerepkört** a szerepkörlistában. Ezt a Fájl- és tárolási **szolgáltatások fájl-** és  >  **ISCSI-szolgáltatások alatt találja.** Amikor kiválasztja az Elosztott fájlrendszerbeli névterek kiszolgálói szerepkört, az esetlegesen szükséges támogató kiszolgálói szerepköröket vagy szolgáltatásokat is hozzáadhatja, amelyekre még nincs telepítve.

![Képernyőkép a **Szerepkörök és szolgáltatások hozzáadása** varázslóról, az **Elosztott fájlrendszerbeli névterek** szerepkör kijelölve.](./media/files-manage-namespaces/dfs-namespaces-install.png)

Miután ellenőrizte az **Elosztott** fájlrendszerbeli névterek  szerepkört, választhatja a  Tovább lehetőséget az összes további képernyőn, majd válassza a Telepítés lehetőséget, amint a varázsló engedélyezi a gombot. A telepítés befejezése után konfigurálhatja a névteret.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Egy emelt szintű PowerShell-munkamenetből (vagy PowerShell-parancsáttolás használatával) hajtsa végre a következő parancsokat.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Meglévő kiszolgálónevek átveszi a főkonszolidációval
Az elosztott fájlrendszerbeli névterek fontos használata, hogy átvesz egy meglévő kiszolgálónevet a fájlmegosztások fizikai elrendezésének újrarendezése érdekében. A modernizálási áttelepítés során például egyesíteni szeretné több régi fájlkiszolgáló fájlmegosztását egyetlen fájlkiszolgálón. A végfelhasználói jártasság és a dokumentum-összekapcsolás hagyományosan korlátozza a különálló fájlkiszolgálókról származó fájlmegosztások összevonásának képességét egyetlen gazdagépen, de az Elosztott fájlrendszerbeli névterek gyökérkonszolidáció szolgáltatása lehetővé teszi, hogy egyetlen kiszolgálót több kiszolgálónévre is felálljon, és a megfelelő megosztásnévre irányítható.

Bár számos adatközpont-migrálási forgatókönyv esetében hasznos, a gyökérkonszolidáció különösen hasznos a natív felhőbeli Azure-fájlmegosztások esetében, mert:

- Az Azure-fájlmegosztások nem teszik lehetővé a meglévő helyszíni kiszolgálónevek megtartását.
- Az Azure-fájlmegosztásokat a tárfiók teljes tartománynevével (FQDN) kell elérni. A tárfiókban hívott Azure-fájlmegosztás például mindig az UNC elérési úton `share` `storageaccount` keresztül érhető `\\storageaccount.file.core.windows.net\share` el. Ez zavaró lehet az olyan végfelhasználók számára, akik rövid nevet várnak (például `\\MyServer\share`) vagy egy név, amely a szervezet tartománynevének altartománya (például `\\MyServer.contoso.com\share`).

A gyökérkonszolidáció csak önálló névterekkel használható. Ha már rendelkezik meglévő tartományalapú névtér a fájlmegosztások számára, nem kell létrehoznia egy összesített gyökérnévteret.

### <a name="enabling-root-consolidation"></a>Gyökérkonszolidáció engedélyezése
A gyökérkonszolidáció a következő beállításkulcsok emelt szintű PowerShell-munkamenetből való beállításával (vagy PowerShell-alapú eltolással) engedélyezhető.

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>DNS-bejegyzések létrehozása meglévő fájlkiszolgáló-nevekhez
Ahhoz, hogy az elosztott fájlrendszerbeli névterek válaszoljanak a meglévő fájlkiszolgálók nevére, hozzon létre aliasrekordokat (CNAME) a meglévő fájlkiszolgálókhoz, amelyek az Elosztott fájlrendszerbeli névterek kiszolgálójának nevére mutatnak. A DNS-rekordok frissítésének pontos eljárása attól függhet, hogy a szervezet milyen kiszolgálókat használ, és hogy a szervezet egyéni eszközök használatával automatizálja-e a DNS kezelését. Az alábbi lépések a Windows Serverhez mellékelt ÉS a Windows AD által automatikusan használt DNS-kiszolgálóhoz tartoznak.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Egy Windows DNS-kiszolgálón nyissa meg a DNS felügyeleti konzolt. Ehhez válassza a Start **gombot,** és írja be a **DNS szöveget.** Navigáljon a tartományhoz a továbbítási keresési zónához. Ha például a tartománya , a keresési zóna a felügyeleti konzolOn a Keresési zónák továbbítása területen `contoso.com`   >  **`contoso.com`** található. A párbeszédpanelen látható pontos hierarchia a hálózat DNS-konfigurációától függ.

Kattintson a jobb gombbal a címkeresési zónára, és válassza az **Új alias (CNAME) lehetőséget.** Az eredményül kapott párbeszédpanelen adja meg a lecserélni kívánt fájlkiszolgáló rövid nevét (a teljes tartománynév automatikusan ki lesz töltve a Teljes tartománynév címkével rendelkező **szövegmezőben).** A cél gazdagép teljes tartományneve **(FQDN)** címkével rendelkező szövegmezőbe írja be a beállított DFS-N kiszolgáló nevét. A Tallózás **gombbal** szükség esetén kiválaszthatja a kiszolgálót. Kattintson **az OK** gombra a kiszolgáló CNAME rekordjának létrehozásához.

![Képernyőkép egy CNAME DNS-bejegyzés **Új erőforrásrekord** bejegyzésről.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Windows DNS-kiszolgálón nyisson meg egy PowerShell-munkamenetet (vagy használja a PowerShell-alapú eltolást) a következő parancsok végrehajtásához a és a feltöltéséhez a környezet megfelelő értékeivel (a automatikusan kitölti a tartománynevet, de ezt manuálisan is be is `$oldServer` `$dfsnServer` `$domain` írhatja).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Névtér létrehozása
Az elosztott fájlrendszerbeli névterek alapvető felügyeleti egysége a névtér. A névtér gyökere vagy neve a névtér kiindulási pontja, így az UNC elérési úton a névtér `\\contoso.com\Public\` gyökere `Public` . 

Ha elosztott fájlrendszerbeli névterekkel veszi át a meglévő kiszolgálónevet gyökérkonszolidációval, a névtér nevének az átveszeni kívánt kiszolgálónévnek kell lennie, a karakterrel előtaggal `#` előzve. Ha például egy nevű meglévő kiszolgálót szeretne át venni, hozzon létre egy `MyServer` nevű DFS-N `#MyServer` névteret. Az alábbi PowerShell-szakasz gondoskodik a előtag előzetes létrehozásáról, de ha az elosztott fájlrendszer kezelőkonzolján keresztül hozza létre a et, akkor a megfelelő `#` előtagra lesz szükség. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Új névtér létrehozásához nyissa meg az **Elosztott fájlrendszer kezelése konzolt.** Ehhez válassza a Start  gombot, és írja be az **Elosztott fájlrendszer kezelése szöveget.** Az eredményül kapott felügyeleti  konzol két szakaszból áll: Névterek és Replikáció **,** amelyek az Elosztott fájlrendszerbeli névterek és Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) szakaszra vonatkoznak. Azure File Sync egy modern replikációs és szinkronizálási mechanizmust biztosít, amely a DFS-R helyére is alkalmazható, ha replikációra van szükség.

Válassza a **Névterek** szakaszt, majd az Új **névtér** gombot (a jobb gombbal a Névterek **szakaszra is kattinthat).** Az eredményül **kapott Új névtér varázsló** végigvezeti a névtér létrehozásának folyamatán. 

A varázsló első szakaszában ki kell választania az elosztott fájlrendszerbeli névtérkiszolgálót a névtér gazdagépeként. Több kiszolgáló is képes egy névtérre, de az elosztott fájlrendszerbeli névtereket egyszerre egy kiszolgálóval kell beállítania. Adja meg a kívánt Elosztott fájlrendszerbeli névtérkiszolgáló nevét, és válassza a **Tovább lehetőséget.** A **Névtér neve és beállításai szakaszban** megadhatja a névtér kívánt nevét, és kiválaszthatja a Tovább **lehetőséget.** 

A **Névtér típusa** szakasz lehetővé teszi, hogy tartományalapú **névtér** és önálló **névtér közül válasszon.** Ha elosztott fájlrendszerbeli névtereket kíván használni egy meglévő fájlkiszolgáló/NAS-eszköz nevének megőrzésére, válassza az önálló névtér lehetőséget. Minden más esetben tartományalapú névteret kell választania. A [névtértípusok közötti](#namespace-types) választásra vonatkozó további információkért tekintse meg a fenti névtértípusokat.

![Képernyőkép a tartományalapú névtér és az önálló névtér közötti választásról az **Új névtér varázslóban**.](./media/files-manage-namespaces/dfs-namespace-type.png)

Válassza ki a környezet kívánt névtértípusát, majd válassza a **Tovább lehetőséget.** A varázsló ezután összegzi a létrehozni fogja a névteret. Válassza **a Létrehozás** lehetőséget a névtér létrehozásához és a **Bezárás** gombra a párbeszédpanel befejeződéskor.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Az elosztott fájlrendszerbeli névtérkiszolgálón található PowerShell-munkamenetből hajtsa végre a következő PowerShell-parancsokat, és a ,, és értékeket a környezetének `$namespace` `$type` megfelelő `$takeOverName` értékekkel.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Mappák és mappacélok konfigurálása
Ahhoz, hogy egy névtér hasznos legyen, mappákkal és mappacélokkal kell lennie. Minden mappa egy vagy több mappacéllal is lehet, amelyek az adott tartalmat tartalmazó SMB-fájlmegosztás(ak)re mutató mutatók. Amikor a felhasználók mappacélokat tartalmazó mappában böngésznek, az ügyfélszámítógép olyan terjesztést kap, amely transzparens módon átirányítja az ügyfélszámítógépet az egyik mappacélra. Mappacélok nélkül is lehet mappákat, hogy struktúrát és hierarchiát adjon a névtérhez.

Az Elosztott fájlrendszerbeli névterek mappái a fájlmegosztások mappáihoz hasonlók. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az Elosztott fájlrendszer kezelőkonzolján válassza ki az előbb létrehozott névteret, majd válassza az **Új mappa lehetőséget.** Az eredményül **kapott Új mappa** párbeszédpanelen a mappát és annak céljait is létrehozhatja.

![Képernyőkép az **Új mappa** párbeszédpanelről.](./media/files-manage-namespaces/dfs-folder-targets.png)

A Name (Név) **szövegmezőben** adja meg a mappa nevét. Válassza **a Hozzáadás...** lehetőséget mappacélok hozzáadásához ehhez a mappához. Az eredményül kapott **Mappa** céljának hozzáadása  párbeszédpanelen egy Elérési út mappa céljának címkével jelölt szövegmező jelenik meg, amelyben megadhatja a kívánt mappa UNC elérési útját. A **Mappacél** **hozzáadása párbeszédpanelen kattintson az** OK gombra. Ha UNC elérési utat ad hozzá egy Azure-fájlmegosztáshoz, előfordulhat, hogy megjelenik egy üzenet, amely szerint a kiszolgáló nem `storageaccount.file.core.windows.net` lehet kapcsolat. Ez várható; A **folytatáshoz válassza** az Igen lehetőséget. Végül kattintson az **OK** gombra az **Új mappa párbeszédpanelen** a mappa- és mappacélok létrehozásához.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Most, hogy létrehozott egy névteret, egy mappát és egy mappacélt, csatlakoztatnia kell a fájlmegosztást az elosztott fájlrendszerbeli névterekkel. Ha tartományalapú névteret használ, a megosztás teljes elérési útja a következő lesz: `\\<domain-name>\<namespace>\<share>` . Ha önálló névteret használ, a megosztás teljes elérési útja a következő legyen: `\\<DFS-server>\<namespace>\<share>` . Ha egy önálló névteret használ gyökérkonszolidációval, közvetlenül a régi kiszolgálónéven keresztül férhet hozzá, például a következővel: `\\<old-server>\<share>` .

## <a name="see-also"></a>Lásd még
- Azure-fájlmegosztás üzembe helyezése: A Azure Files [tervezése](storage-files-planning.md) és [fájlmegosztás létrehozása.](storage-how-to-create-file-share.md)
- Fájlmegosztás-hozzáférés konfigurálása: [Identitásalapú](storage-files-active-directory-overview.md) hitelesítés és Hálózati szempontok [a közvetlen hozzáféréshez.](storage-files-networking-overview.md)
- [Windows Server elosztott fájlrendszer névterek](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)
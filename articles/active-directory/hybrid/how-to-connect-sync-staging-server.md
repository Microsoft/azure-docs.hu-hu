---
title: 'Azure AD Connect szinkronizálás: üzemeltetési feladatok és szempontok | Microsoft Docs'
description: Ez a témakör a Azure AD Connect szinkronizálásának működési feladatait és az összetevő működésének előkészítését ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48584fa4042cf53fa1084e519dca0e64f530ca59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090125"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: átmeneti kiszolgáló és vész-helyreállítás
Ha egy kiszolgáló átmeneti módban van, módosíthatja a konfigurációt, és előkészítheti a módosításokat a kiszolgáló aktív állapotba helyezése előtt. Azt is lehetővé teszi, hogy teljes importálást és teljes szinkronizálást futtasson annak ellenőrzéséhez, hogy az összes módosítás várható-e, mielőtt a módosításokat elvégezte az éles környezetben.

## <a name="staging-mode"></a>Átmeneti mód
Az előkészítési mód több forgatókönyv esetén is használható, többek között:

* Magas rendelkezésre állás.
* Tesztelje és telepítse az új konfigurációs módosításokat.
* Vezessen be egy új kiszolgálót, és szerelje le a régit.

A telepítés során kiválaszthatja a kiszolgálót **átmeneti módban**. Ezzel a művelettel a kiszolgáló aktív lesz az importáláshoz és a szinkronizáláshoz, de nem futtat semmilyen exportálást. Az átmeneti üzemmódú kiszolgálók nem futtatják a jelszó-szinkronizálást vagy a jelszó-visszaírási, még akkor sem, ha a telepítés során kiválasztotta ezeket a funkciókat. Az átmeneti üzemmód letiltásakor a kiszolgáló elindítja az exportálást, engedélyezi a jelszó-szinkronizálást, és engedélyezi a jelszó-visszaírási.

> [!NOTE]
> Tegyük fel, hogy van egy Azure AD Connect, amelyen engedélyezve van a jelszó-kivonatolási szinkronizálási funkció. Ha engedélyezi az átmeneti üzemmódot, a kiszolgáló leállítja a jelszó módosításait a helyszíni AD-ből. Ha letiltja az átmeneti üzemmódot, a kiszolgáló folytatja a jelszó szinkronizálását, amelyből a legutóbb abbahagyta a módosításokat. Ha a kiszolgáló hosszabb ideig nem átmeneti módban van, eltarthat egy ideig, amíg a kiszolgáló szinkronizálni tudja az adott időszakban történt összes módosítást.
>
>

Az exportálást továbbra is kényszerítheti a szinkronizálási Service Manager használatával.

Az átmeneti üzemmódú kiszolgálók továbbra is megkapják a Active Directory és az Azure AD módosításait, és meghibásodás esetén gyorsan átvehetik a másik kiszolgáló feladatait. Ha módosítja az elsődleges kiszolgáló konfigurációját, az Ön felelőssége, hogy a kiszolgálót átmeneti módban hajtsa végre.

A régebbi szinkronizálási technológiák ismeretével az átmeneti üzemmód eltér, mivel a kiszolgáló saját SQL-adatbázissal rendelkezik. Ez az architektúra lehetővé teszi, hogy az átmeneti üzemmódú kiszolgáló egy másik adatközpontban legyen elhelyezve.

### <a name="verify-the-configuration-of-a-server"></a>Kiszolgáló konfigurációjának ellenőrzése
A metódus alkalmazásához kövesse az alábbi lépéseket:

1. [Előkészítés](#prepare)
2. [Konfigurálás](#configuration)
3. [Importálás és szinkronizálás](#import-and-synchronize)
4. [Ellenőrzés](#verify)
5. [Aktív kiszolgáló váltása](#switch-active-server)

#### <a name="prepare"></a>Előkészítés
1. Telepítse a Azure AD Connectt, válassza ki az **átmeneti üzemmódot**, és törölje a **szinkronizálás elindítása** a telepítővarázsló utolsó lapján. Ez a mód lehetővé teszi a szinkronizálási motor manuális futtatását.
   ![A képernyőképen látható a konfigurálásra kész lap a Azure AD Connect párbeszédpanelen.](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Jelentkezzen ki/jelentkezzen be, és a Start menüben válassza a **szinkronizálási szolgáltatás** elemet.

#### <a name="configuration"></a>Konfiguráció
Ha egyéni módosításokat hajtott végre az elsődleges kiszolgálón, és össze szeretné hasonlítani a konfigurációt az átmeneti kiszolgálóval, akkor a [Azure ad Connect Configuration documenter](https://github.com/Microsoft/AADConnectConfigDocumenter)használatával kell összehasonlítania.

#### <a name="import-and-synchronize"></a>Importálás és szinkronizálás
1. Válassza az **Összekötők** lehetőséget, majd válassza ki az első összekötőt **Active Directory Domain Services** típussal. Kattintson a **Futtatás** elemre, válassza a **teljes importálás** lehetőséget, majd **az OK gombot**. Hajtsa végre ezeket a lépéseket az összes ilyen típusú összekötőhöz.
2. Válassza ki a **Azure Active Directory (Microsoft)** típusú összekötőt. Kattintson a **Futtatás** elemre, válassza a **teljes importálás** lehetőséget, majd **az OK gombot**.
3. Győződjön meg arról, hogy a TAB-összekötők továbbra is ki vannak választva. Minden **Active Directory Domain Services** típusú összekötőhöz kattintson a **Futtatás** elemre, válassza a **különbözeti szinkronizálás** lehetőséget, majd **az OK gombot**.
4. Válassza ki a **Azure Active Directory (Microsoft)** típusú összekötőt. Kattintson a **Futtatás** elemre, válassza a **különbözeti szinkronizálás** lehetőséget, majd **az OK gombot**.

Ezzel elvégezte az Azure AD és a helyszíni Active Directory módosításainak exportálását (ha az Exchange hibrid üzembe helyezést használja). A következő lépésekkel megvizsgálhatja, hogy mi a változás, mielőtt ténylegesen elindítja az exportálást a címtárakba.

#### <a name="verify"></a>Ellenőrzés
1. Indítsa el a parancssort, és nyissa meg a következő parancsot `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Run: az `csexport "Name of Connector" %temp%\export.xml /f:x` összekötő neve megtalálható a szinkronizációs szolgáltatásban. Az Azure AD esetében az "contoso.com – Azure AD" kifejezéshez hasonló név szerepel.
3. Futtatás: a `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` (z)% Temp% nevű fájlban található export.csv, amely megvizsgálható a Microsoft Excelben. Ez a fájl tartalmazza az exportálandó összes módosítást.
4. Végezze el a szükséges módosításokat az adatokat vagy a konfigurációban, majd futtassa újra ezeket a lépéseket (Importálás és szinkronizálás és ellenőrzés), amíg az exportálandó módosítások várhatóak lesznek.

**A export.csv fájl ismertetése** A fájl nagy része magától értetődő. Néhány rövidítés a tartalom megértéséhez:
* OMODT – objektum módosításának típusa. Azt jelzi, hogy egy objektum szintjén a művelet egy Hozzáadás, frissítés vagy törlés.
* AMODT – attribútum módosításának típusa. Azt jelzi, hogy egy attribútum szintjén a művelet egy Hozzáadás, frissítés vagy törlés.

**Közös azonosítók beolvasása** A export.csv fájl tartalmazza az exportálandó összes módosítást. Minden sor az összekötő terület egy objektumának változásának felel meg, és az objektumot a DN attribútum azonosítja. A DN attribútum egyedi azonosító, amely az összekötő terület egy objektumához van rendelve. Ha a export.csv sok sorát vagy változását elemezni kívánja, nehéz lehet megállapítani, hogy mely objektumok esetében a módosítások csak a DN attribútumon alapulnak. A módosítások elemzésének folyamata egyszerűbbé válik a csanalyzer.ps1 PowerShell-parancsfájl használatával. A parancsfájl az objektumok közös azonosítóit (például displayName, userPrincipalName) kérdezi le. A szkript használata:
1. Másolja a PowerShell-szkriptet a szakasz [CSAnalyzer](#appendix-csanalyzer) egy nevű fájlba `csanalyzer.ps1` .
2. Nyisson meg egy PowerShell-ablakot, és keresse meg azt a mappát, ahová a PowerShell-parancsfájlt létrehozta.
3. Adja ki a következő parancsot: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Most már rendelkezik egy **processedusers1.csv** nevű fájllal, amely megvizsgálható a Microsoft Excelben. Vegye figyelembe, hogy a fájl leképezést biztosít a DN attribútumból a közös azonosítók (például a displayName és a userPrincipalName) számára. Jelenleg nem tartalmazza a ténylegesen exportálandó attribútumok változásait.

#### <a name="switch-active-server"></a>Aktív kiszolgáló váltása
1. A jelenleg aktív kiszolgálón kapcsolja ki a kiszolgálót (az rSync/FIM/Azure AD-szinkronizáló), hogy ne exportálja az Azure AD-ba, vagy állítsa be átmeneti módban (Azure AD Connect).
2. Futtassa a telepítő varázslót a kiszolgálón **átmeneti módban** , és tiltsa le az **átmeneti üzemmódot**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Vészhelyreállítás
A megvalósítási terv része annak a megtervezése, hogy mi a teendő abban az esetben, ha a szinkronizálási kiszolgáló elveszíti a katasztrófát. Különböző modellek használhatók, és az egyik használat több tényezőtől függ, többek között a következőktől:

* Mi az a tolerancia, amely nem tudja módosítani az Azure AD-beli objektumokat az állásidőben?
* Ha jelszó-szinkronizálást használ, a felhasználók elfogadják, hogy a régi jelszót kell használniuk az Azure AD-ben abban az esetben, ha a helyszínen változtatják meg?
* Függ a valós idejű műveletektől, például a jelszó visszaírási?

A kérdésekre adott válaszoktól és a szervezet házirendjétől függően az alábbi stratégiák egyike valósítható meg:

* Szükség esetén építse újra.
* Tartalék készenléti kiszolgálóval rendelkezik, amelyet **átmeneti üzemmódnak** nevezünk.
* Virtuális gépek használata.

Ha nem használja a beépített SQL Express-adatbázist, tekintse át az [SQL magas rendelkezésre állási](#sql-high-availability) szakaszt is.

### <a name="rebuild-when-needed"></a>Újraépítés szükség esetén
Egy életképes stratégia a kiszolgáló újraépítésének megtervezése, ha szükséges. Általában a szinkronizálási motor telepítése és a kezdeti Importálás és szinkronizálás is elvégezhető néhány órán belül. Ha nincs elérhető tartalék kiszolgáló, akkor átmenetileg egy tartományvezérlővel is üzemeltetheti a Szinkronizáló motort.

A szinkronizálási motor kiszolgálója nem tárol semmilyen állapotot az objektumokról, így az adatbázis újraépíthető a Active Directory és az Azure AD-ben lévő adatokból. A **sourceAnchor** attribútum a helyszíni és a felhőből származó objektumok csatlakoztatására szolgál. Ha a kiszolgálót a helyszíni és a Felhőbeli meglévő objektumokkal építi újra, akkor a szinkronizálási motor az újratelepítéskor újra összehasonlítja ezeket az objektumokat. A dokumentálni és menteni kívánt dolgok a kiszolgálón végrehajtott konfigurációs változások, például a szűrési és szinkronizálási szabályok. Ezeket az egyéni konfigurációkat újra kell alkalmazni a szinkronizálás megkezdése előtt.

### <a name="have-a-spare-standby-server---staging-mode"></a>Készenléti kiszolgáló – átmeneti üzemmód
Ha összetettebb környezettel rendelkezik, egy vagy több készenléti kiszolgáló használata javasolt. A telepítés során engedélyezheti, hogy a kiszolgáló **átmeneti módban** legyen.

További információ: [átmeneti üzemmód](#staging-mode).

### <a name="use-virtual-machines"></a>Virtuális gépek használata
Egy közös és támogatott módszer a Szinkronizáló motor futtatása egy virtuális gépen. Abban az esetben, ha a gazdagép problémával rendelkezik, a szinkronizálási motor kiszolgálóját tartalmazó rendszerkép áttelepíthető egy másik kiszolgálóra.

### <a name="sql-high-availability"></a>SQL magas rendelkezésre állása
Ha nem a Azure AD Connecthoz tartozó SQL Server Express használja, akkor a SQL Server magas rendelkezésre állását is figyelembe kell venni. A támogatott magas rendelkezésre állású megoldások közé tartozik az SQL-fürtszolgáltatás és az ALAPSZABÁLYT (always on rendelkezésre állási csoportok). A nem támogatott megoldások közé tartoznak a tükrözések.

Az SQL ALAPSZABÁLYT-támogatás a 1.1.524.0 verziójában Azure AD Connect lett hozzáadva. Azure AD Connect telepítése előtt engedélyeznie kell az SQL ALAPSZABÁLYT-t. A telepítés során a Azure AD Connect észleli, hogy a megadott SQL-példány engedélyezve van-e az SQL ALAPSZABÁLYÁBAN vagy sem. Ha az SQL ALAPSZABÁLYT engedélyezve van, Azure AD Connect további adatokat, ha az SQL ALAPSZABÁLYT szinkron replikálás vagy aszinkron replikáció használatára van konfigurálva. A rendelkezésre állási csoport figyelő beállításakor ajánlott a RegisterAllProvidersIP tulajdonságot 0-ra állítani. Ennek az az oka, hogy Azure AD Connect jelenleg SQL Native Clientt használ az SQL-hez való kapcsolódáshoz, és SQL Native Client nem támogatja a MultiSubNetFailover tulajdonság használatát.

## <a name="appendix-csanalyzer"></a>CSAnalyzer függelék
Tekintse meg a következő szakaszt: a parancsfájl használatának [ellenőrzése](#verify) .

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakörök**  

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)  
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)  

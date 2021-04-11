---
title: Azure-beli virtuálisgép-rendszerkép tesztelése az Azure Marketplace-en
description: Megtudhatja, hogyan tesztelheti és küldheti el az Azure-beli virtuális gépek ajánlatait az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 467b7d605b57c479d84fc995b4e0dc53b3ac5275
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558295"
---
# <a name="test-a-virtual-machine-image"></a>Virtuális gép rendszerképének tesztelése

Ez a témakör az Azure Marketplace-en üzembe helyezett virtuálisgép-rendszerképek tesztelésének lépéseit tárgyalja.

## <a name="deploy-an-azure-vm"></a>Azure-beli virtuális gép üzembe helyezése

Virtuális gép üzembe helyezése a megosztott rendszerkép-katalógus rendszerképből:

1. Navigáljon a megosztott képgyűjtemény rendszerképének verziójára
1. Kattintson a virtuális gép létrehozása elemre.
1. Adja meg a virtuális gép nevét, és válasszon egy virtuálisgép-méretet
1. Kattintson a felülvizsgálat + Létrehozás gombra. Az érvényesítés átadása után kattintson a Létrehozás gombra.

> [!NOTE]
> Ha virtuális GÉPET kell létrehoznia egy VHD-fájlból, kövesse az alábbi cikk utasításait, [készítse elő Azure Resource Manager sablont](#connect-the-certification-tool-to-a-vm-image) , vagy [helyezzen üzembe egy Azure-beli virtuális gépet a PowerShell használatával](#how-to-use-powershell-to-consume-the-self-test-api).

Ez a cikk azt ismerteti, hogyan lehet tesztelni és elküldeni a virtuális gép (VM) lemezképét a kereskedelmi piactéren, hogy az megfeleljen az Azure Marketplace legújabb közzétételi követelményeinek.

A virtuális gép ajánlatának elküldése előtt végezze el a következő lépéseket:

- Azure-beli virtuális gép üzembe helyezése az általánosított rendszerkép használatával; Lásd: [virtuális gép létrehozása jóváhagyott alap használatával](azure-vm-create-using-approved-base.md) vagy [virtuális gép létrehozása saját rendszerkép használatával](azure-vm-create-using-own-image.md).
- Érvényesítések futtatása.

## <a name="run-validations"></a>Érvényesítések futtatása

Az üzembe helyezett lemezképen kétféleképpen futtathat érvényességet.

### <a name="use-certification-test-tool-for-azure-certified"></a>Az Azure Certified minősítési teszt eszköz használata

#### <a name="download-and-run-the-certification-test-tool"></a>A minősítési teszt eszköz letöltése és futtatása

Az Azure Certified minősítési teszt eszköze egy helyi Windows-gépen fut, de egy Azure-alapú Windows vagy Linux rendszerű virtuális gépet tesztel. Tanúsítja, hogy a felhasználói virtuálisgép-rendszerképet Microsoft Azure használhatja, és a virtuális merevlemez előkészítésével kapcsolatos útmutatás és követelmények teljesültek.

1. Töltse le és telepítse a legújabb [minősítési teszt eszközt az Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299)minősítéshez.
2. Nyissa meg a minősítési eszközt, majd kattintson az **új teszt indítása** lehetőségre.
3. A teszt adatai képernyőn adjon meg egy **teszt nevet** a teszt futtatásához.
4. Válassza ki a virtuális géphez tartozó platformot, amely **Windows Server** vagy **Linux**. A platform választása hatással van a fennmaradó lehetőségekre.
5. Ha a virtuális gép ezt az adatbázis-szolgáltatást használja, jelölje be a **Azure SQL Database teszt** jelölőnégyzetet.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>A minősítési eszköz összekötése egy virtuálisgép-lemezképpel

1. Válassza ki az SSH hitelesítési módot: jelszó-hitelesítés vagy kulcsfájl-hitelesítés.
2. Jelszó-alapú hitelesítés használata esetén adja meg a **virtuális gép DNS-nevének**, **felhasználónevének** és **jelszavának** értékeit. Módosíthatja az alapértelmezett SSH-portszámot is.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="A virtuális gép tesztelési adatainak kiválasztását mutatja.":::

3. Ha Key file-alapú hitelesítést használ, adja meg a virtuális gép DNS-neve, a Felhasználónév és a titkos kulcs helyének értékeit. Egy hozzáférési kódot is megadhat, vagy megváltoztathatja az alapértelmezett SSH-portszámot.
4. Adja meg a teljesen minősített virtuális gép DNS-nevét (például MyVMName.Cloudapp.net).
5. Adja meg a **felhasználónevet** és a **jelszót**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Megjeleníti a virtuális gép felhasználónevének és jelszavának kiválasztását.":::

6. Kattintson a **Tovább** gombra.

#### <a name="run-a-certification-test"></a>Minősítési teszt futtatása

Miután megadta a virtuálisgép-rendszerkép paramétereinek értékét a minősítési eszközben, válassza a kapcsolódás tesztelése lehetőséget a virtuális géphez való érvényes kapcsolatok létrehozásához. A kapcsolatok ellenőrzése után kattintson a **tovább** gombra a teszt elindításához. A teszt befejezésekor az eredmények egy táblázatban jelennek meg. Az Állapot oszlopban az egyes tesztek (Pass/Fail/Warning) láthatók. Ha a tesztek bármelyike meghiúsul, a rendszerkép nem rendelkezik tanúsítvánnyal. Ebben az esetben tekintse át a követelményeket és a hibaüzeneteket, végezze el a javasolt módosításokat, majd futtassa újra a tesztet.

Az automatikus teszt befejezése után további információkat adhat meg a virtuálisgép-rendszerképről a kérdőív képernyő két lapján, az általános értékelés és a rendszermag testreszabásában, majd kattintson a Tovább gombra.

Az utolsó képernyőn több információt is megadhat, például az SSH-hozzáférési információkat egy linuxos virtuálisgép-rendszerképhez, valamint a sikertelen értékelések magyarázatát, ha kivételeket keres.

Végül válassza a jelentés készítése lehetőséget, hogy letöltse a teszt eredményeit és a naplófájlokat a végrehajtott tesztelési esetekhez, valamint a kérdőívre adott válaszokat.
> [!Note]
> Néhány közzétevőnek van olyan forgatókönyve, amelyben a virtuális gépeket zárolni kell, mert olyan szoftverekkel rendelkeznek, mint például a virtuális gépre telepített tűzfalak. Ebben az esetben töltse le a [Certified test Tool eszközt](https://aka.ms/AzureCertificationTestTool) , és küldje el a jelentést a partner Center [támogatási szolgálatának](https://aka.ms/marketplacepublishersupport).

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>A Self-Test API használata a PowerShell használatával

### <a name="on-linux-os"></a>Linux operációs rendszeren

Az API meghívása a PowerShellben:

1. A Invoke-WebRequest parancs használatával hívja meg az API-t.
2. A metódus a post és a Content típus JSON, ahogy az alábbi kódrészletben látható.
3. A szövegtörzs paramétereit JSON formátumban kell megadni.

A következő példa egy PowerShell-hívást mutat be az API-ra:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Íme egy példa az API meghívására a PowerShellben:

[![Példa az API PowerShellben való meghívására.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Az előző példa használatával lekérheti a JSON-t, és elemezheti a következő részletek beszerzéséhez:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Ez a minta képernyő, amely a `$res.Content` teszt eredményének részleteit jeleníti meg JSON formátumban:

[![Példa az API PowerShellben való meghívására a tesztelési eredmények részleteivel.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Íme egy példa a JSON-teszt eredményeire, amely egy online JSON-megjelenítőben (például a [Code szépít](https://codebeautify.org/jsonviewer) vagy a [JSON Viewer](https://jsonformatter.org/json-viewer)) tekinthető meg.

![További tesztelési eredmények egy online JSON-megjelenítőben.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows operációs rendszeren

Az API meghívása a PowerShellben:

1. A Invoke-WebRequest parancs használatával hívja meg az API-t.
2. A metódus a post és a Content típus JSON, ahogy az a következő példában látható, például a minta képernyőn.
3. Hozza létre a törzs paramétereit JSON formátumban.

Ez a mintakód egy PowerShell-hívást mutat be az API-nak:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Ezek a példák az API PowerShellben való meghívására mutatnak példát:

**SSH-kulccsal**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Az API meghívása a PowerShellben SSH-kulccsal.":::

**Jelszóval**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Az API meghívása a PowerShellben jelszóval.":::

<br>Az előző példa használatával lekérheti a JSON-t, és elemezheti a következő részletek beszerzéséhez:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Ez a képernyő azt mutatja `$res.Content` , hogy a teszt eredményei JSON formátumban jelennek meg:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="A teszt eredményének részletei JSON formátumban.":::

<br>Az alábbi példa egy online JSON-megjelenítőben megtekintett teszt eredményeire mutat (például a [Code szépít](https://codebeautify.org/jsonviewer) vagy a [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Teszt eredménye egy online JSON-megjelenítőben.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>A Self-Test API használata Linux rendszeren a CURL használatával

Ebben a példában a curl használatával a Azure Active Directory és a Self-Host virtuális gép utáni API-hívást fog végezni.

1. Azure AD-jogkivonat igénylése az önkiszolgáló virtuális géphez való hitelesítéshez

   Győződjön meg arról, hogy a megfelelő értékek a curl-kérelemben vannak helyettesítve.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Íme egy példa a kérelemre adott válaszra:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Kérelem küldése az önteszt virtuális géphez

   Győződjön meg arról, hogy a tulajdonosi jogkivonat és a paraméterek a megfelelő értékekkel vannak helyettesítve.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Példa válasz az önteszt VM API-hívásra

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Következő lépések

- Jelentkezzen be a [partner központba](https://partner.microsoft.com/).

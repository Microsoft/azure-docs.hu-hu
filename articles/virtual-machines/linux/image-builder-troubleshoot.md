---
title: Az Azure rendszerkép-készítő szolgáltatás hibáinak megoldása
description: Az Azure VM rendszerkép-készítő szolgáltatás használata során felmerülő gyakori problémák és hibák elhárítása
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f76c3e6c739ae4dd13355d350a01b878e4d4f360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666205"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Az Azure rendszerkép-készítő szolgáltatás hibáinak megoldása

Ez a cikk segítséget nyújt az Azure rendszerkép-készítő szolgáltatás használata során felmerülő gyakori problémák elhárításában és megoldásában.

A AIB hibák két területen is megtörténhetnek:
- Rendszerkép-sablon beküldése
- Rendszerkép létrehozása

## <a name="troubleshoot-image-template-submission-errors"></a>A rendszerkép-sablon küldésével kapcsolatos hibák elhárítása

A rendszerkép-sablon küldésével kapcsolatos hibák csak a küldéskor lesznek visszaadva. A rendszerkép-sablon beküldési hibáinak hiba miatt nem jelenik meg a naplóban. Ha hiba történt az elküldés során, a sablon állapotának ellenőrzésével visszaállíthatja a hibát `ProvisioningState` `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> A PowerShell esetében telepítenie kell az [Azure rendszerkép-készítő PowerShell-modulokat](../windows/image-builder-powershell.md#prerequisites).

A következő témakörök a gyakori Képsablon-küldési hibákkal kapcsolatos problémamegoldási útmutatót tartalmazzák.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>A rendszerkép-sablonok frissítése/frissítése jelenleg nem támogatott

#### <a name="error"></a>Hiba

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Ok

A sablon már létezik.

#### <a name="solution"></a>Megoldás

Ha elküld egy rendszerkép-konfigurációs sablont, és a Küldés meghiúsul, akkor a sablon hibás összetevője továbbra is létezik. Törölje a hibás sablont.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>Az erőforrás-művelet a (z) "Failed" terminál-kiépítési állapottal fejeződött be

#### <a name="error"></a>Hiba

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Ok

A legtöbb esetben az erőforrás-telepítési hiba a hiányzó engedélyek miatt fordul elő.

#### <a name="solution"></a>Megoldás

A forgatókönyvtől függően az Azure rendszerkép-szerkesztőnek szüksége lehet a következőkre:
- Forrás rendszerkép vagy megosztott képgyűjteményi erőforráscsoport
- Terjesztési rendszerkép vagy megosztott képgyűjteményi erőforrás
- A fájl-testreszabó által elérni kívánt Storage-fiók, tároló vagy blob. 

További információ az engedélyek konfigurálásáról: [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása az Azure CLI használatával](image-builder-permissions-cli.md) vagy az [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása a PowerShell használatával](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>Hiba történt a felügyelt rendszerkép beolvasása közben

#### <a name="error"></a>Hiba

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Ok

Hiányzó engedélyek.

#### <a name="solution"></a>Megoldás

A forgatókönyvtől függően az Azure rendszerkép-szerkesztőnek szüksége lehet a következőkre:
* Forrás rendszerkép vagy megosztott képgyűjteményi erőforráscsoport
* Terjesztési rendszerkép vagy megosztott képgyűjteményi erőforrás
* A fájl-testreszabó által elérni kívánt Storage-fiók, tároló vagy blob. 

További információ az engedélyek konfigurálásáról: [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása az Azure CLI használatával](image-builder-permissions-cli.md) vagy az [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása a PowerShell használatával](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>A létrehozási lépés sikertelen volt a rendszerkép verziója esetében

#### <a name="error"></a>Hiba
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Ok

Az Azure-rendszerkép-szerkesztő nem találja a forrás rendszerképét.

#### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a forrás képe helyes, és létezik az Azure rendszerkép-szerkesztő szolgáltatásának helyén.

### <a name="downloading-external-file-to-local-file"></a>Külső fájl letöltése helyi fájlba

#### <a name="error"></a>Hiba

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Ok

A fájl neve vagy helye helytelen, vagy a hely nem érhető el.

#### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a fájl elérhető. Ellenőrizze, hogy helyesek-e a név és a hely.

## <a name="troubleshoot-build-failures"></a>Build-hibák elhárítása

A rendszerkép-létrehozási hibák esetén a következő hibaüzenetet kaphatja meg `lastrunstatus` , majd áttekintheti a Testreszabás. log fájl részleteit.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Testreszabási napló

A rendszerkép létrehozásakor a rendszer létrehozza és tárolja a naplókat egy Storage-fiókban. Az Azure rendszerkép-készítő létrehozza a Storage-fiókot az ideiglenes erőforráscsoporthoz, amikor létrehoz egy Képsablon-összetevőt.

A Storage-fiók neve a következő mintát használja **: \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\> IT_**

Például *IT_aibmdi_helloImageTemplateLinux01*.

A Storage-fiókban található Storage-fiókban a **Storage-fiók**  >  **Blobok** lehetőség kiválasztásával megtekintheti a  >  Testreszabás. `packerlogs`  Ezután válassza a **könyvtár > customization. log** elemet.


### <a name="understanding-the-customization-log"></a>A testreszabási napló ismertetése

A napló részletes. Magában foglalja a lemezképek összeállítását, beleértve a lemezképek terjesztésével kapcsolatos problémákat, például a megosztott képkatalógus replikálását. Ezek a hibák a Képsablon állapotának hibaüzenetében jelennek meg.

A customization. log a következő szakaszokat tartalmazza:

1. Üzembe helyezheti a virtuális gépet és a függőségeket az ARM-sablonokkal a IT_ átmeneti erőforráscsoport szakaszába. Ebben a szakaszban több bejegyzés is szerepel az Azure rendszerkép-készítő erőforrás-szolgáltatónál:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. A központi telepítések fázisának állapota. Ez a szakasz az egyes erőforrás-telepítések állapotát tartalmazza:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Kapcsolódjon a Build VM-fázishoz.

    Ha a Windows, az Azure rendszerkép-készítő szolgáltatás a WinRM használatával csatlakozik:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Linux esetén az Azure rendszerkép-készítő szolgáltatás az SSH használatával fog kapcsolódni:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Testreszabási fázis futtatása. A testreszabások futtatásakor a testreszabási napló áttekintésével azonosíthatja őket. Keresse meg a *(telemetria)* kifejezést.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Kiépítési szakasz. Az Azure rendszerkép-szerkesztő egy rejtett testreszabó hoz létre. Ennek a kiépítési lépésnek a feladata a virtuális gép kiépítésének előkészítése. Futtatja a Windows Sysprep eszközt (c:\DeprovisioningScript.ps1 használatával) vagy a Linux waagent (a/tmp/DeprovisioningScript.sh használatával). 

    Például:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Tisztítási fázis. A Build befejezése után a rendszer törli az Azure rendszerkép-szerkesztő erőforrásait.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Tippek a parancsfájl/beágyazott Testreszabás hibaelhárításához
- A kód tesztelése a rendszerkép-szerkesztőbe való beadás előtt
- Győződjön meg arról, hogy a Azure Policy és a tűzfalak lehetővé teszik a távoli erőforrásokhoz való kapcsolódást.
- Kimeneti megjegyzések a konzolhoz, például a vagy a használatával, `Write-Host` `echo` Ez lehetővé teszi a testreszabási. log keresését.

## <a name="troubleshoot-common-build-errors"></a>Gyakori összeállítási hibák elhárítása

### <a name="packer-build-command-failure"></a>A csomagoló-Build parancs hibája

#### <a name="error"></a>Hiba

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Ok

Testreszabási hiba.

#### <a name="solution"></a>Megoldás

A testreszabók hibáinak megkereséséhez tekintse át a naplót. Keresse meg a *(telemetria)* kifejezést. 

Például:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Időtúllépés túllépve

#### <a name="error"></a>Hiba

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Ok

A Build túllépte a létrehozási időtúllépést. Ez a hiba a "lastrunstatus" látható.

#### <a name="solution"></a>Megoldás

1. Tekintse át a customization. log naplófájlt. A futtatandó legutóbb használt testreszabó azonosítása. Keressen rá a `(telemetry)` napló aljáról. 

2. A parancsfájlok testreszabásának ellenőrzését. Előfordulhat, hogy a testreszabások nem fogják letiltani a felhasználói interakciót a parancsokhoz, például a `quiet` beállításokhoz. Például `apt-get install -y` a parancsfájl végrehajtásának eredménye a felhasználói interakcióra vár.

3. Ha a `File` testreszabást a 20 megabájtnál nagyobb összetevők letöltésére használja, tekintse meg a megkerülő megoldások szakaszt.

4. Tekintse át a parancsfájlban szereplő hibákat és függőségeket, amelyek miatt a szkript várni lehet.

5. Ha várható, hogy a testreszabások több időre van szükségük, növelje a [buildTimeoutInMinutes](image-builder-json.md). Az alapértelmezett érték négy óra.

6. Ha erőforrás-igényes műveletekkel rendelkezik, például a GB-os fájlok letöltésével, vegye figyelembe az alapul szolgáló Build VM-méretet. A szolgáltatás Standard_D1_v2 virtuális gépet használ. A virtuális gép 1 vCPU és 3,5 GB memóriával rendelkezik. Ha 50 GB-ot tölt le, ez valószínűleg kimeríti a virtuális gépek erőforrásait, és kommunikációs hibákat okoz az Azure rendszerkép-készítő szolgáltatás és a virtuális gép létrehozása között. Próbálja megismételni a buildet egy nagyobb memóriával rendelkező virtuális géppel a [VM_Size](image-builder-json.md#vmprofile)beállításával.

### <a name="long-file-download-time"></a>Hosszú fájl letöltési ideje

#### <a name="error"></a>Hiba
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Ok 

A fájl-testreszabó egy nagyméretű fájl letöltése.

#### <a name="solution"></a>Megoldás

A fájl-testreszabó csak a 20 MB-nál kisebb méretű fájlok letöltésére alkalmas. Nagyobb fájlok letöltéséhez használjon parancsfájlt vagy beágyazott parancsot. A Linux rendszeren például a vagy a használható `wget` `curl` . Windows rendszeren a következőt használhatja: `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Hiba történt a megosztott képgyűjteményre való várakozás közben

#### <a name="error"></a>Hiba

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Ok

A rendszerkép-szerkesztő időtúllépést várt a rendszerkép felvételének és replikálásának a megosztott képkatalógusba (SIG) való várakozásakor. Ha a képet befecskendezi a SIG-ba, feltételezhető, hogy a rendszerkép létrehozása sikeres volt. Az általános folyamat azonban nem sikerült, mert a rendszerkép-készítő a megosztott rendszerkép-katalógusra várakozik a replikálás befejezéséhez. Annak ellenére, hogy a Build nem sikerült, a replikálás folytatódik. A rendszerkép verziójának tulajdonságait a terjesztési *runOutput* ellenőrizheti.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Megoldás

Növelje a **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Alacsony Windows-erőforrás-információs események

#### <a name="error"></a>Hiba

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Ok

Erőforrás-kimerültség. Ezt a problémát általában a Windows Update futtatja az alapértelmezett virtuálisgép-méret D1_V2 használatával.

#### <a name="solution"></a>Megoldás

Növelje a virtuális gép Build méretét.

### <a name="builds-finished-but-no-artifacts-were-created"></a>A buildek készek, de nem jöttek létre összetevők

#### <a name="error"></a>Hiba

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Ok

Időtúllépést okozott a szükséges Azure-erőforrások létrehozására való várakozás.

#### <a name="solution"></a>Megoldás

Futtassa újra a buildet, és próbálkozzon újra.

### <a name="resource-not-found"></a>Az erőforrás nem található

#### <a name="error"></a>Hiba

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Ok

Hiányzó engedélyek.

#### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy az Azure-rendszerkép-készítő minden szükséges engedélyekkel rendelkezik. 

További információ az engedélyek konfigurálásáról: [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása az Azure CLI használatával](image-builder-permissions-cli.md) vagy az [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása a PowerShell használatával](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>A Sysprep időzítése

#### <a name="error"></a>Hiba

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Ok

Az ok a D1_V2 VM-méret miatti időzítési probléma lehet. Ha a testreszabások korlátozottak, és három másodpercnél kevesebbet hajtanak végre, a Sysprep-parancsokat az Azure rendszerkép-készítő futtatja, hogy kiépítse. Ha az Azure rendszerkép-készítője de-kikötéseket használ, a Sysprep parancs ellenőrzi a *WindowsAzureGuestAgent*, amely esetleg nem teljesen települ, ami az időzítési problémát okozhatja. 

#### <a name="solution"></a>Megoldás

Növelje a virtuális gép méretét. Emellett az időzítési probléma elkerülése érdekében hozzáadhat egy 60-másodperces PowerShell-alvási testreszabást is.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Build megszakítása a környezet lemondási környezetének megszakítása után

#### <a name="error"></a>Hiba
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Ok
A rendszerkép-készítő szolgáltatás a 22-es (Linux) vagy a 5986 (Windows) portot használja a Build virtuális géphez való csatlakozáshoz. Ez akkor fordul elő, ha a szolgáltatás leválasztása a virtuális gép létrehozásához a rendszerkép létrehozásakor történik. A leválasztás okai eltérőek lehetnek, de a parancsfájlok engedélyezése vagy konfigurálása a fenti portok blokkolását is lehetővé teszi.

#### <a name="solution"></a>Megoldás
Tekintse át a tűzfalak változásait/engedélyezését, illetve az SSH vagy a WinRM módosítását, és győződjön meg arról, hogy a módosítások lehetővé teszik a szolgáltatás közötti állandó kapcsolódást és a virtuális gép kiépítését a fenti portokon. A rendszerkép-készítő hálózatkezeléssel kapcsolatos további információkért tekintse át a [követelményeket](./image-builder-networking.md).

## <a name="devops-task"></a>DevOps-feladat 

### <a name="troubleshooting-the-task"></a>A feladat hibaelhárítása
A feladat csak akkor lép működésbe, ha a Testreszabás során hiba történik, amikor ez megtörténik, a feladat hibát jelez, és az előkészítési erőforráscsoportot a naplók használatával hagyja, így azonosíthatja a problémát. 

A napló megkereséséhez ismernie kell a sablon nevét, a folyamat > nem sikerült felépíteni > részletezést a AIB DevOps feladatba, majd látni fogja a naplót és a sablon nevét:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Nyissa meg a portált, keresse meg a sablon nevét az erőforráscsoport területen, majd keresse meg az erőforráscsoportot a IT_ * használatával.
Nyissa meg a Storage-fiók > Blobok > tárolók > naplókat.

### <a name="troubleshooting-successful-builds"></a>Sikeres buildek hibaelhárítása
Bizonyos esetekben előfordulhat, hogy a sikeres buildek vizsgálatára van szükség, és át szeretné tekinteni a naplót. Ahogy azt említettük, ha a rendszerkép létrehozása sikeres, a naplókat tartalmazó átmeneti erőforráscsoport törlődik a karbantartás részeként. Azonban az is előfordulhat, hogy a beágyazott parancs után alvó állapotba kerül, majd lekéri a naplókat, mivel a Build szüneteltetve van. Ehhez kövesse az alábbi lépéseket:
 
1. Frissítse a beágyazott parancsot, és adja hozzá a következőt: Write-Host/echo "Sleep" – Ez lehetővé teszi a keresést a naplóban
2. Vegyen fel egy alvó állapotot legalább 10mins, a [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep)vagy a `Sleep` Linux parancsot használhatja.
3. A fenti módszer használatával azonosíthatja a napló helyét, majd megtarthatja a napló letöltését/ellenőrzését, amíg az alvó állapotba nem kerül.


### <a name="operation-was-canceled"></a>A művelet meg lett szakítva

#### <a name="error"></a>Hiba

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Ok

Ha a buildet nem egy felhasználó törölte, azt az Azure DevOps felhasználói ügynöke törölte. Az Azure DevOps képességei miatt a legnagyobb valószínűséggel az 1 órás időtúllépés történt. Ha privát projektet és ügynököt használ, 60 perces felépítési időt vehet igénybe. Ha a Build túllépi az időkorlátot, a DevOps megszakítja a futó feladatot.

További információ az Azure DevOps képességeiről és korlátairól: [Microsoft által üzemeltetett ügynökök](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)
 
#### <a name="solution"></a>Megoldás

Saját DevOps-ügynököket is üzemeltetheti, vagy megtekintheti a Build időpontjának csökkentését. Ha például a megosztott rendszerkép-katalógusra terjeszt, replikálja egy régióba. Ha aszinkron módon kíván replikálni. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Lassú Windows-bejelentkezés: "várjon, amíg a Windows-modulok telepítője"

#### <a name="error"></a>Hiba
Miután létrehozott egy Windows 10-es rendszerképet a rendszerkép-készítővel, hozzon létre egy virtuális gépet a rendszerképből, az RDP-t, és várjon néhány percet az első bejelentkezéskor egy kék képernyővel az üzenettel:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Megoldás
Első lépésként a rendszerkép-összeállításban győződjön meg arról, hogy a Windows újraindítási testreszabása nem szükséges a legutóbbi testreszabáshoz, és hogy az összes szoftver telepítése befejeződött. Végül adja hozzá a [/Mode: VM](/windows-hardware/manufacture/desktop/sysprep-command-line-options) lehetőséget a AIB által használt alapértelmezett Sysprep-hez, lásd alább, "a AIB-lemezképek által létrehozott virtuális gépek nem hozhatnak létre sikeresen" > "felülbírálja a parancsokat"  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>A AIB-lemezképből létrehozott virtuális gépek nem jönnek létre sikeresen

Alapértelmezés szerint az Azure-rendszerkép-szerkesztő az egyes rendszerkép-testreszabási fázis végén található, *de-kiépítés* kódot futtatja a rendszerkép *általánosítása* érdekében. Az általánosítás olyan folyamat, amelyben a rendszerkép úgy van beállítva, hogy több virtuális gép létrehozásához újra felhasználható legyen, és átadható a virtuálisgép-beállítások, például az állomásnév, a Felhasználónév stb. Windows rendszeren az Azure rendszerkép-szerkesztő végrehajtja a *Sysprep* alkalmazást, valamint a Linux Azure rendszerkép-szerkesztő futtatását `waagent -deprovision` . 

A Windows esetében az Azure rendszerkép-készítő egy általános Sysprep parancsot használ. Előfordulhat azonban, hogy ez nem alkalmas minden sikeres Windows-általánosításhoz. Az Azure rendszerkép-szerkesztővel testreszabhatja a Sysprep parancsot. Megjegyzés: az Azure rendszerkép-készítő egy rendszerkép-automatizálási eszköz. A Sysprep parancs sikeres futtatásának felelőse. Előfordulhat azonban, hogy más Sysprep-parancsokra van szüksége, hogy a rendszerkép újrafelhasználható legyen. Linux rendszeren az Azure rendszerkép-készítő általános `waagent -deprovision+user` parancsot használ. További információ: [Microsoft Azure Linux-ügynök dokumentációja](https://github.com/Azure/WALinuxAgent#command-line-options).

Ha meglévő testreszabást telepít át, és különböző Sysprep/waagent parancsokat használ, kipróbálhatja a rendszerkép-készítő általános parancsait. Ha a virtuális gép létrehozása sikertelen, használja az előző Sysprep/waagent-parancsokat.

> [!NOTE]
> Ha a AIB létrehoz egy egyéni Windows-rendszerképet, és létrehoz egy virtuális gépet, akkor a virtuális gép nem jön létre sikeresen (például a virtuális gép létrehozási parancsa nem fejeződik be/időtúllépés), a Windows Server Sysprep dokumentációját is át kell tekintenie. Vagy egy támogatási kérést is felvehet a Windows Server Sysprep Customer Services támogatási csapatával, aki a megfelelő Sysprep-parancson keresztül képes a hibakeresésre és a tanácsadásra.

### <a name="command-locations-and-filenames"></a>Parancsok helye és fájlneve

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep-parancs: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Kiépítés parancs: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>A parancsok felülbírálása

A parancsok felülbírálásához a PowerShell vagy a rendszerhéj parancsfájl-kiépítő használatával hozza létre a megfelelő fájlnevet tartalmazó parancsfájlokat, és helyezze azokat a korábban felsorolt címtárakba. Az Azure-rendszerkép-szerkesztő beolvassa ezeket a parancsokat, és a kimenet a *customization. log* fájlba íródik.

## <a name="getting-support"></a>Támogatás kérése
Ha erre az útmutatóra hivatkozik, és továbbra sem tudja elhárítani a problémát, nyisson meg egy támogatási esetet. Ha így tesz, válassza ki a megfelelő termék-és támogatási témakört, ezzel bekerül az Azure VM rendszerkép-készítő támogatási csapatával.

Az eset termék kiválasztása:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Következő lépések

További információ: az [Azure rendszerkép-készítő áttekintése](../image-builder-overview.md).

---
title: Azure FPGFA-igazolási szolgáltatás
description: Az NP sorozatú virtuális gépek igazolási szolgáltatása.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: a3408d30a9caa24355cf3976235c3a9b8061b95f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531232"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA-igazolás Azure-beli NP-Series virtuális gépekhez (előzetes verzió)

Az FPGA igazolási szolgáltatás ellenőrzést végez az Xilinx eszközkészlet által létrehozott tervezési ellenőrzőpont-fájlon (más néven "netlist"), és létrehoz egy fájlt, amely tartalmazza az ellenőrzött rendszerképet (úgynevezett "bitstreamet"), amely betölthető az Np-sorozat virtuális gépének Xilinx U250 FPGA-kártyájára.  

## <a name="prerequisites"></a>Előfeltételek  

Szüksége lesz egy Azure-előfizetésre és egy Azure Storage-fiókra. Az előfizetés hozzáférést biztosít az Azure-hoz, a tárfiók pedig az igazolási szolgáltatás netlist- és kimeneti fájljainak tárolására szolgál.  

Az igazolási kérések elküldését PowerShell- és Bash-szkriptekkel biztosítjuk.   A szkriptek az Azure CLI-t használják, amely Windows és Linux rendszeren is futtatható. A PowerShell Windows, Linux és macOS rendszeren is futtatható.  

Azure CLI-letöltés (kötelező):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Windows, Linux és macOS rendszeren a PowerShell letöltése (csak PowerShell-parancsfájlok esetén):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Az igazolási szolgáltatásba való küldéshez jogosult bérlő- és előfizetés-azonosítóval kell rendelkezik. Hozzáférés https://aka.ms/AzureFPGAAttestationPreview kérése a webhelyről. 

## <a name="building-your-design-for-attestation"></a>Az igazolási terv kialakítása  

A tervezéshez az előnyben részesített Xilinx eszközkészlet a Vxin 2020.2. Az eszközkészlet korábbi verziójával létrehozott és a 2020.2-es verzióval továbbra is kompatibilis netlist fájlok használhatók. Győződjön meg arról, hogy a megfelelő rendszerhéjat telepítette a buildhez. A jelenleg támogatott verzió a xilinx_u250_gen3x16_xdma_2_1_202010_1. A támogatási fájlok az Xilinx Alveo-ból tölthetők le. 

Ahhoz, hogy egy bitstream helyett netlistát tartalmazó xclbin-fájlt hoz létre, bele kell foglalnia a Vemet (v++ cmd line) argumentumát a következő argumentumba.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Bejelentkezés az Azure-ba  

Mielőtt bármilyen műveletet hajt végre az Azure-ral, be kell jelentkeznie az Azure-ba, és be kell állítania azt az előfizetést, amely jogosult a szolgáltatás hívására. Erre a célra ```az login``` használja a és a ```az account set –s <Sub ID or Name>``` parancsot. A folyamattal kapcsolatos további információkat itt dokumentáljuk:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. A parancssorban használja a "bejelentkezés interaktívan" vagy a "bejelentkezés hitelesítő adatokkal" lehetőséget.  

## <a name="creating-a-storage-account-and-blob-container"></a>Tárfiók és blobtároló létrehozása  

A netlist-fájlt fel kell tölteni egy Azure Storage-blobtárolóba, hogy az igazolási szolgáltatás hozzáférjen.  

A fiók és a tároló létrehozásával, valamint a netlist blobként való feltöltésével kapcsolatos további információkért tekintse meg ezt az oldalt: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](/azure/storage/blobs/storage-quickstart-blobs-cli) .  

Ehhez a Azure Portal is használhatja.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>A netlist fájl feltöltése az Azure Blob Storage-ba  

A fájl másolásának több módja is van; Alább látható egy példa az az storage upload parancsmag használatával. Az az parancsok Linux és Windows rendszeren is futnak. A "blob" névhez bármilyen nevet kiválaszthat, de ügyeljen arra, hogy megtartsa az xclbin bővítményt. 

```az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Az igazolási szkriptek letöltése  

Az érvényesítési szkriptek a következő Azure Storage-blobtárolóból tölthetők le:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

A zip-fájl két PowerShell-szkriptet tartalmaz: az egyiket el kell küldenünk, a másikat monitorba kell küldenünk, míg a harmadik egy Bash-szkript, amely mindkét funkciót végrehajtja.  

## <a name="running-the-attestation-scripts"></a>Az igazolási szkriptek futtatása  

A szkriptek futtatásához meg kell adnia a tárfiók nevét, a netlist fájlt tároló blobtároló nevét és a netlist fájl nevét. Létre kell hoznia egy szolgáltatás közös hozzáférésű jogosultságát (SAS), amely olvasási/írási hozzáférést biztosít a tárolóhoz (nem a netlistához). Ezt az SAS-t az igazolási szolgáltatás arra használja, hogy helyi másolatot készítsen a netlist fájlról, és visszaírja az érvényesítési folyamat eredményül kapott kimeneti fájljait a tárolóba.  

A közös hozzáférésű jogosultságok áttekintését itt, a szolgáltatási SAS-sel kapcsolatos konkrét információkkal együtt itt olvashatja el. A Szolgáltatás SAS-oldala fontos figyelmeztetést tartalmaz a létrehozott SAS védelméről.  Olvassa el a figyelmeztetést, hogy tisztában legyen vele, hogy az SAS-t meg kell védeni a kártékony vagy nem szándékos használattól.  

A tárolóhoz az az storage container generate-sas parancsmag használatával hozhat létre SAS-t. Adjon meg egy UTC formátumú lejárati időt, amely legalább néhány órával a beküldés előtt van; körülbelül 6 óra elegendőnek kell lennie.  

Ha virtuális könyvtárakat szeretne használni, a könyvtárhierarchiát is bele kell foglalnia a tároló argumentumába. Ha például van egy "netlists" nevű tárolója, és van egy "image1" nevű virtuális könyvtára, amely a netlist blobot tartalmazza, akkor a tároló neve "netlists/image1". A további címtárnevek hozzáfűzése mélyebb hierarchia megadásához. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>A beküldés állapotának ellenőrzése  

Az igazolási szolgáltatás visszaadja a beküldés vezénylési azonosítóját. A beküldési szkriptek automatikusan elkezdik a beküldés monitorozását a lekérdezett lekérdezésekkel. A vezénylési azonosító az elsődleges módja annak, hogy áttekintsük, mi történt a beküldése során, ezért kérjük, tartsa meg, ha problémája van. Referenciapontokként az igazolás körülbelül 30 percet vesz igénybe egy kis netlistás fájlhoz (300 MB méretű); egy 1,6 GB-os fájl egy óráig tartott. 

A vezénylési Monitor-Validation.ps1 bármikor hívhatja az állapot és az igazolási eredmények lehívására, argumentumként megtéve a vezénylési azonosítót:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Másik lehetőségként http post kérést is küldhet az igazolási szolgáltatásvégpontnak:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

A kérelem törzsének tartalmaznia kell az előfizetés-azonosítót, a bérlőazonosítót és az igazolási kérelem vezénylési azonosítóját:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Az ellenőrzés utáni lépések

A szolgáltatás visszaírja a kimenetét a tárolóba. Ha az érvényesítés sikeres, a tárolóban lesz az eredeti netlist fájl (abc.xclbin), egy bitstreamet tartalmazó fájl (abc.bit.xclbin), egy fájl, amely azonosítja a tárolt bitstream privát helyét (abc.azure.xclbin), valamint négy naplófájlt: egyet az indítási folyamathoz (abc-log.txt), egyet pedig az ellenőrzést végző három párhuzamos fázishoz. Ezek neve *logPhaseX.txt ahol az X a fázis sorszáma. A virtuális gépen az azure.xclbin jelzi az ellenőrzött rendszerkép feltöltését az U250-re. 

Ha az érvényesítés sikertelen, a rendszer egy error-*.txt fájlt ír, amely jelzi, hogy melyik lépés volt sikertelen. Ellenőrizze a naplófájlokat is, ha a hibanapló azt jelzi, hogy az igazolás sikertelen volt. Ha támogatási kérést kér tőlünk, mindenképpen adja meg ezeket a fájlokat a támogatási kérés részeként, valamint a vezénylési azonosítót.  

A tárolót a Azure Portal használhatja, valamint feltöltheti a netlistát, valamint letöltheti a bitstream- és a naplófájlokat. Az igazolási kérések elküldése és a folyamat előrehaladásának a portálon keresztüli monitorozása jelenleg nem támogatott, és a fent leírt szkriptekkel kell őket elérni. 


---
title: Azure FPGFA igazolási szolgáltatás
description: Igazolási szolgáltatás az NP-sorozatú virtuális gépekhez.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 563155bb6559f8443f1453a65fa0b1574af106f7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556172"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA-igazolás Azure NP-Series virtuális gépekhez (előzetes verzió)

A FPGA igazolási szolgáltatás a xilinx eszközkészlet által generált tervezési ellenőrzőpont-fájl ("netlist") sorozatát hajtja végre, és egy olyan fájlt hoz létre, amely tartalmazza az érvényesített képet (úgynevezett "Bitstream"), amely egy NP sorozatú virtuális gép xilinx U250 FPGA kártyára tölthető be.  

## <a name="prerequisites"></a>Előfeltételek  

Szüksége lesz egy Azure-előfizetésre és egy Azure Storage-fiókra. Az előfizetés hozzáférést biztosít az Azure-hoz, és a Storage-fiók az igazolási szolgáltatás netlist és kimeneti fájljainak tárolására szolgál.  

Az igazolási kérelmek elküldéséhez PowerShell-és bash-szkripteket biztosítunk.   A szkriptek az Azure CLI-t használják, amely Windows és Linux rendszeren is futtatható. A PowerShell Windows, Linux és macOS rendszeren is futtatható.  

Azure CLI letöltése (kötelező):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Windows, Linux és macOS rendszerhez készült PowerShell Letöltés (csak PowerShell-parancsfájlok esetén):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Rendelkeznie kell a Bérlővel és az előfizetés-AZONOSÍTÓval, amely jogosult az igazolási szolgáltatásba való küldésre. Látogasson el https://aka.ms/AzureFPGAAttestationPreview a hozzáférés kéréséhez. 

## <a name="building-your-design-for-attestation"></a>Az igazolás kialakításának kialakítása  

A Designs xilinx eszközkészlet a Vitis 2020,2. Az eszközkészlet korábbi verziójával létrehozott Netlist-fájlok, amelyek továbbra is kompatibilisek az 2020,2-mel, használhatók. Győződjön meg arról, hogy betöltötte a megfelelő rendszerhéjt a létrehozáshoz. A jelenleg támogatott verzió xilinx_u250_gen3x16_xdma_2_1_202010_1. A támogatási fájlok a xilinx Alveo Lounge-ból tölthetők le. 

A következő argumentumot kell tartalmaznia a Vitis (v + + cmd vonal) számára egy olyan xclbin-fájl létrehozásához, amely netlist tartalmaz egy Bitstream helyett.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Bejelentkezés az Azure-ba  

Mielőtt bármilyen műveletet végezne az Azure-ban, be kell jelentkeznie az Azure-ba, és be kell állítania azt az előfizetést, amely a szolgáltatás meghívására van engedélyezve. Használja a ```az login``` és a ```az account set –s <Sub ID or Name>``` parancsokat erre a célra. A folyamatról további információt itt talál:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. A parancssorban használja a "Bejelentkezés interaktív módon" vagy a "bejelentkezés hitelesítő adatokkal" lehetőséget.  

## <a name="creating-a-storage-account-and-blob-container"></a>Storage-fiók és blob-tároló létrehozása  

A netlist-fájlt fel kell tölteni egy Azure Storage blob-tárolóba az igazolási szolgáltatáshoz való hozzáféréshez.  

A fiók létrehozásával, a tárolóval és a netlist a tárolóba való feltöltésével kapcsolatos további információkért tekintse meg a következő oldalt: https://docs.microsoft.com/azure/storage/blobs/storage-quickstartblobs-cli .  

Ehhez is használhatja a Azure Portal.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Töltse fel a netlist-fájlt az Azure Blob Storage-ba  

A fájl másolásának számos módja van; alább látható egy példa az az Storage upload parancsmag használatával. Az az parancsok Linux és Windows rendszeren is futnak. Kiválaszthatja a "blob" nevét, de ügyeljen rá, hogy megőrizze a xclbin-bővítményt. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Az igazolási parancsfájlok letöltése  

Az érvényesítési parancsfájlok a következő Azure Storage blob-tárolóból tölthetők le:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

A zip-fájl két PowerShell-szkripttel rendelkezik, az egyiket be kell küldeni, a másikat pedig figyelni, míg a harmadik fájl egy bash-szkript, amely mindkét funkciót végrehajtja.  

## <a name="running-the-attestation-scripts"></a>Az igazolási parancsfájlok futtatása  

A parancsfájlok futtatásához meg kell adnia a Storage-fiók nevét, annak a blob-tárolónak a nevét, amelyben a netlist-fájlt tárolja, valamint a netlist fájl nevét. Emellett létre kell hoznia egy szolgáltatás-közös hozzáférési aláírást (SAS), amely írási/olvasási hozzáférést biztosít a tárolóhoz (nem a netlist). Az igazolási szolgáltatás ezt az SAS-t használja a netlist-fájl helyi másolatának elvégzéséhez, és az érvényesítési folyamat eredményül kapott kimeneti fájljainak a tárolóba való visszaírásához.  

A közös hozzáférésű aláírások áttekintését itt találja az itt elérhető Service SAS-vel kapcsolatos konkrét információkkal. A szolgáltatás SAS-lapja fontos figyelmeztetést tartalmaz a generált SAS védelméről.  Olvassa el figyelmesen, hogy tisztában kell lennie azzal, hogy meg kell őriznie az SAS által védett kártékony vagy nem rendeltetésszerű használatot.  

Létrehozhat egy SAS-t a tárolóhoz az az Storage Container generált-sas parancsmag használatával. Olyan lejárati időt UTC formátumban kell megadni, amely legalább néhány órával korábbi a beküldési időpontnál. a körülbelül 6 óra értékének a megfelelőnél nagyobbnak kell lennie.  

Ha virtuális könyvtárakat szeretne használni, a tároló argumentumának részeként fel kell vennie a címtár-hierarchiát. Ha például rendelkezik egy "netlists" nevű tárolóval, és rendelkezik egy "image1" nevű virtuális könyvtárral, amely a netlist blobot tartalmazza, akkor a "netlists/image1" nevet kell megadnia a tároló neveként. Fűzze hozzá a további könyvtárak nevét a mélyebb hierarchia megadásához. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>A beküldési állapot ellenőrzése  

Az igazolási szolgáltatás visszaadja a beküldésének előkészítési AZONOSÍTÓját. A beküldési parancsfájlok automatikusan megkezdik a Küldés figyelését a befejezéshez való lekérdezéssel. A bejelentési azonosító az elsődleges módszer, amellyel áttekintheti, hogy mi történt a beküldéssel, ezért kérjük, hogy ha probléma merül fel. A hivatkozási pontként az igazolás körülbelül 30 percet vesz igénybe egy kis netlist-fájl (300MB méretekben) teljesítése érdekében. egy 1,6 GB-os fájl egy órát vett igénybe. 

A Monitor-Validation.ps1-szkriptet bármikor meghívhatja az igazolás állapotának és eredményének lekéréséhez, amely argumentumként megadja a koordinálási azonosítót:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Azt is megteheti, hogy HTTP POST-kérést küld az igazolási szolgáltatás végpontjának:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

A kérelem törzsének tartalmaznia kell az igazolási kérelemhez tartozó előfizetés-azonosítót, a bérlő AZONOSÍTÓját és az előkészítési AZONOSÍTÓját:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Ellenőrzés utáni lépések

A szolgáltatás a kimenetét vissza fogja írni a tárolóba. Ha az érvényesítés sikeres, a tárolóban az eredeti netlist-fájl (ABC. xclbin), a Bitstream (ABC. bit. xclbin) fájl található, amely a tárolt Bitstream (ABC. Azure. xclbin) saját helyét azonosítja, valamint négy naplófájlt: egyet az indítási folyamathoz (abc-log.txt), egyet pedig az ellenőrzést végrehajtó három párhuzamos fázishoz. Ezek neve * logPhaseX.txt, ahol az X a fázishoz tartozó szám. Az Azure. xclbin a virtuális gépen van használatban, hogy jelezze az érvényesített rendszerkép feltöltését a U250. 

Ha az érvényesítés nem sikerült, a rendszer egy error-*. txt fájlt ír, amely azt jelzi, hogy melyik lépés nem sikerült. A naplófájlokat is ellenőrizze, ha a hibanapló azt jelzi, hogy az igazolás sikertelen volt. Ha segítségre van szüksége a kapcsolatfelvételhez, ügyeljen arra, hogy a támogatási kérelem részeként tartalmazza az összes fájlt, valamint a koordinálási azonosítót.  

A Azure Portal használatával létrehozhatja a tárolót, valamint feltöltheti a netlist, és letöltheti a Bitstream és a naplófájlokat. Az igazolási kérelem elküldése és a portálon keresztüli előrehaladás monitorozása jelenleg nem támogatott, és a fent leírtak szerint szkripteken keresztül kell végrehajtani. 


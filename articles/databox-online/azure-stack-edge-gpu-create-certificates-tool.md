---
title: Tanúsítványok létrehozása Microsoft Azure Stack Hub készenlét-ellenőrző eszközének használatával | Microsoft Docs
description: Ismerteti, hogyan hozhat létre tanúsítványkérelmeket, és hogyan kérhet le és telepíthet tanúsítványokat Azure Stack Edge Pro GPU-eszközén az Azure Stack Hub készenlét-ellenőrző eszközzel.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 8316dd0abfa437d4bf88e8268dfe034344c6614c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389332"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Tanúsítványok létrehozása a Azure Stack Edge Pro a Azure Stack Hub ellenőrzőeszköz használatával 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre tanúsítványokat a Azure Stack Edge Pro a Azure Stack Hub-ellenőrző eszközzel. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Az Azure Stack Hub-ellenőrző eszköz használata

A Azure Stack Hub ellenőrzőeszköz használatával tanúsítvány-aláírási kérelmeket (CSR-eket) hozhat létre egy Azure Stack Edge Pro telepítéséhez. Ezeket a kéréseket azután hozhatja létre, hogy megrendelést Azure Stack Edge Pro eszközre, és megvárja az eszköz megérkezését.

> [!NOTE]
> Ezt az eszközt csak tesztelési vagy fejlesztési célokra használja, éles eszközökhöz nem. 

A következő tanúsítványok Azure Stack Hub az AzsReadinessChecker (AzsReadinessChecker) készenlét-ellenőrző eszközzel:

- Azure Resource Manager tanúsítvány
- Helyi felhasználói felület tanúsítványa
- Csomópont-tanúsítvány
- Blob-tanúsítvány
- VPN-tanúsítvány


## <a name="prerequisites"></a>Előfeltételek

Ha csrs-eket Azure Stack Edge Pro eszköz üzembe helyezéséhez, győződjön meg a következőről: 

- Az ügyfélen a windowsos Windows 10 Windows Server 2016 vagy újabb rendszer fut. 
- Letöltötte az Microsoft Azure Stack Hub készenlét-PowerShell-galéria eszközét. [](https://aka.ms/AzsReadinessChecker)
- A tanúsítványokról a következő információk adatokat kell tartalmazni:
  - Eszköz neve
  - Csomópont sorozatszáma
  - Külső teljes tartománynév (FQDN)

## <a name="generate-certificate-signing-requests"></a>Tanúsítvány-aláírási kérelmek létrehozása

Az eszköztanúsítványok előkészítéséhez Azure Stack Edge Pro lépéseket:

1. Futtassa a PowerShellt rendszergazdaként (5.1-es vagy újabb).
2. Telepítse a Azure Stack Hub készenlét-ellenőrző eszközt. A PowerShell parancssorba írja be a következőt: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    A telepített verzió lekért verziójához írja be a következőt:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Hozzon létre egy könyvtárat az összes tanúsítványhoz, ha még nem rendelkezik ilyen tanúsítványokkal. Típus: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Tanúsítványkérelem létrehozásához adja meg az alábbi adatokat. HA VPN-tanúsítványt generál, a bemenetek némelyike nem alkalmazható.
    
    |Bevitel |Leírás  |
    |---------|---------|
    |`OutputRequestPath`|A fájl elérési útja a helyi ügyfélen, ahol létre szeretné hozatni a tanúsítványkérelmeket.        |
    |`DeviceName`|Az eszköz neve az  eszköz helyi webes felhasználói felületének Eszközök lapján. <br> Ez a mező nem szükséges a VPN-tanúsítványhoz.         |
    |`NodeSerialNumber`|Az eszköz helyi webes felhasználói felületének **Hálózat** lapján található eszközcsomópont sorozatszáma. <br> Ez a mező nem szükséges a VPN-tanúsítványhoz.       |
    |`ExternalFQDN`|A DNSDomain értéke  az eszköz helyi webes felhasználói felületén, az Eszközök lapon.         |
    |`RequestType`|A kérelem típusa lehet a különböző végpontok különböző tanúsítványai, vagy egyetlen tanúsítvány az összes `MultipleCSR` `SingleCSR` végponthoz. <br> Ez a mező nem szükséges a VPN-tanúsítványhoz.     |

    A VPN-tanúsítvány kivételével minden tanúsítványhoz írja be a következőt: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    VPN-tanúsítvány létrehozásakor írja be a következőt: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. A tanúsítványkérelem fájljait a fenti OutputRequestPath paraméterben megadott könyvtárban találja. A paraméter használata esetén a következő négy fájl látható `MultipleCSR` a `.req` bővítvével:

    
    |Fájlnevek  |Tanúsítványkérelem típusa  |
    |---------|---------|
    |Kezdve a `DeviceName`     |Helyi webes felhasználói felület tanúsítványigénylése      |
    |Kezdve a `NodeSerialNumber`     |Csomóponti tanúsítványkérelem         |
    |Kezdés: `login`     |Azure Resource Manager végponti tanúsítványkérelem       |
    |Kezdés: `wildcard`     |Blob Storage-tanúsítványkérelem. Helyettesítő karaktereket tartalmaz, mivel ez tartalmazza az eszközön létrehozható összes tárfiókot.          |
    |Kezdés: `AzureStackEdgeVPNCertificate`     |VPN-ügyfél tanúsítványigénylése.         |

    Egy INF-mappát is látni fog. Ez tartalmaz egy management.<edge-devicename> a tanúsítvány részleteit tartalmazó tiszta szöveges információs fájlban.  


6. Küldje el ezeket a fájlokat a hitelesítésszolgáltatónak (belső vagy nyilvános). Győződjön meg arról, hogy a hitelesítésszolgáltató a létrehozott kérés használatával olyan tanúsítványokat [](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)hoz létre, amelyek megfelelnek Azure Stack Edge Pro csomóponttanúsítványokra, [](azure-stack-edge-gpu-manage-certificates.md#node-certificates)végponttanúsítványokra és helyi felhasználói felületi tanúsítványokra vonatkozó [követelményeinek.](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)

## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok előkészítése üzembe helyezéshez

A hitelesítésszolgáltatótól (CA) származó tanúsítványfájlokat olyan tulajdonságokkal kell importálni és exportálni, amelyek megfelelnek az Azure Stack Edge Pro tanúsítványkövetelményeinek. Kövesse az alábbi lépéseket ugyanazon a rendszeren, ahol a tanúsítvány-aláírási kérelmeket generálta.

- A tanúsítványok importáláshoz kövesse a Tanúsítvány importálása az eszközhöz hozzáférő [Azure Stack Edge Pro lépéseit.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)

- A tanúsítványok exportáláshoz kövesse a Tanúsítványokat exportálása az eszközhöz hozzáférő [ügyféltől Azure Stack Edge Pro lépéseit.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)


## <a name="validate-certificates"></a>Tanúsítványok érvényesítése

Először létre kell hoznia egy megfelelő mappastruktúrát, és a tanúsítványokat a megfelelő mappákba kell tennie. A tanúsítványokat csak ezután fogja érvényesíteni az eszközzel.

1. Futtassa rendszergazdaként a PowerShellt.

2. A megfelelő mappastruktúra létrehozásához írja be a következőt a parancssorba:

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Alakítsa át a PFX-jelszót biztonságos sztringgé. Típus:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Ezután ellenőrizze a tanúsítványokat. Típus:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Következő lépések

[A Azure Stack Edge Pro üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)

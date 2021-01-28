---
title: A tanúsítvány tartalmának konvertálása Base-64-Azure HDInsight
description: Egyszerű tanúsítvány tartalmának konvertálása Base-64 kódolású karakterlánc-formátumra az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944408"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Egyszerű tanúsítvány tartalmának konvertálása Base-64 kódolású karakterlánc-formátumra a HDInsight-ben

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Hibaüzenet jelenik meg, amely szerint a bemenet nem érvényes Base-64 karakterlánc, mert nem Base 64 karaktert, több mint két kitöltési karaktert vagy egy nem fehér szóköz karaktert tartalmaz a kitöltési karakterek között.

## <a name="cause"></a>Ok

Ha a PowerShell vagy az Azure template üzembe helyezésével fürtöket hoz létre Data Lake elsődlegesként vagy további tárolóként, akkor az Data Lake Storage-fiók eléréséhez megadott egyszerű szolgáltatás tanúsítványa a Base-64 formátumban van. A pfx-tanúsítvány tartalmának a Base-64 kódolású sztringre való helytelen átalakítása ehhez a hibához vezethet.

## <a name="resolution"></a>Feloldás

Ha az egyszerű szolgáltatás tanúsítványa pfx formátumban van (lásd [itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) az egyszerű minta-létrehozási lépéseknél), használja a következő PowerShell-parancsot vagy C#-kódrészletet a tanúsítvány tartalmának base-64 formátumra való átalakításához.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
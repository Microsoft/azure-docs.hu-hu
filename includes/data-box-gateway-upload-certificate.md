---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582172"
---
A megfelelő SSL-tanúsítvány biztosítja, hogy a titkosított adatokat a megfelelő kiszolgálóra küldi. A titkosítás mellett a tanúsítvány a hitelesítésre is lehetőséget biztosít. A saját megbízható SSL-tanúsítványát az eszköz PowerShell-felületén keresztül töltheti fel.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. A `Set-HcsCertificate` tanúsítvány feltöltéséhez használja a parancsmagot. Ha a rendszer kéri, adja meg a következő paramétereket:

   - `CertificateFilePath` – A tanúsítványfájl *. pfx* formátumú fájlját tartalmazó megosztás elérési útja.
   - `CertificatePassword` – A tanúsítvány védeleméhez használt jelszó.
   - `Credentials` -Username a tanúsítványt tartalmazó megosztás eléréséhez. Kérés esetén adja meg a hálózati megosztás jelszavát.

     A következő példa a parancsmag használatát mutatja be:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

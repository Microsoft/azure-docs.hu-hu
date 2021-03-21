---
title: Hitelesítés az Azure-erőforrásokkal az ív használatára képes kiszolgálók használatával
description: Ez a cikk az Azure Instance Metadata Service támogatását ismerteti az ív használatára képes kiszolgálókon, valamint az Azure-erőforrásokon és a helyi titokon keresztüli hitelesítéssel.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96939158"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Hitelesítés az Azure-erőforrásokkal az ív használatára képes kiszolgálók használatával

Az Azure arc-kompatibilis kiszolgálókon közvetlenül futó alkalmazások vagy folyamatok a felügyelt identitásokat használhatják a Azure Active Directory-alapú hitelesítést támogató egyéb Azure-erőforrások eléréséhez. Egy alkalmazás olyan [hozzáférési jogkivonatot](../../active-directory/develop/developer-glossary.md#access-token) szerezhet be, amely az arc-kompatibilis kiszolgálók rendszeréhez hozzárendelt identitását jelképezi, és "tulajdonos" tokenként használja egy másik szolgáltatásnak való hitelesítéshez.

Tekintse át a felügyelt [identitások áttekintő](../../active-directory/managed-identities-azure-resources/overview.md) dokumentációját a felügyelt identitások részletes leírását, valamint a rendszer által hozzárendelt és a felhasználó által hozzárendelt identitások megkülönböztetését.

Ebben a cikkben bemutatjuk, hogy a kiszolgálók hogyan használhatják a rendszerhez rendelt felügyelt identitást az Azure [Key Vault](../../key-vault/general/overview.md)eléréséhez. A rendszerindítási szolgáltatásként szolgáló Key Vault lehetővé teszi, hogy az ügyfélalkalmazás egy titkos kulcsot használjon a Azure Active Directory (AD) által nem védett erőforrások eléréséhez. Az IIS-webkiszolgálók által használt TLS/SSL-tanúsítványokat például Azure Key Vault tárolhatja, és biztonságosan telepítheti a tanúsítványokat az Azure-on kívüli Windows-vagy Linux-kiszolgálókra.

## <a name="security-overview"></a>Biztonsági áttekintés

A kiszolgáló Azure arc-kompatibilis kiszolgálókra való beléptetése során számos művelet végrehajtására kerül sor az Azure-beli virtuális gépekhez hasonló felügyelt identitás használatával történő konfiguráláshoz:

- Azure Resource Manager kap egy kérelmet, amely engedélyezi a rendszerhez rendelt felügyelt identitást az ív-kompatibilis kiszolgálón.

- Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a kiszolgáló identitásához. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.

- Azure Resource Manager úgy konfigurálja az identitást a kiszolgálón, hogy frissíti a Windows vagy [Linux](../../virtual-machines/linux/instance-metadata-service.md) [rendszerhez](../../virtual-machines/windows/instance-metadata-service.md) készült Azure instance metadata Service (IMDS) identitás-végpontot az egyszerű szolgáltatás ügyfél-azonosítójával és tanúsítványával. A végpont egy jól ismert, nem irányítható IP-címmel rendelkező REST-végpont, amely csak a kiszolgálóról érhető el. Ez a szolgáltatás az ív használatára képes kiszolgáló metaadat-információinak egy részhalmazát nyújtja a kezeléséhez és konfigurálásához.

A felügyelt identitást támogató kiszolgáló környezete a következő változókkal lesz konfigurálva egy Windows arc-kompatibilis kiszolgálón:

- **IMDS_ENDPOINT**: a IMDS-végpont IP-címe az `http://localhost:40342` ív használatára képes kiszolgálók esetében.

- **IDENTITY_ENDPOINT**: a szolgáltatás felügyelt identitásának megfelelő localhost végpont `http://localhost:40342/metadata/identity/oauth2/token` .

A kiszolgálón futó kód az Azure példány metaadatainak szolgáltatási végpontján igényelhet tokent, amely csak a kiszolgálóról érhető el.

A rendszerkörnyezeti változó **IDENTITY_ENDPOINT** az identitás-végpont alkalmazások általi felderítésére szolgál. Az alkalmazásoknak meg kell próbálnia **IDENTITY_ENDPOINT** és **IMDS_ENDPOINT** értékeket beolvasni és használni őket. A hozzáférési szinttel rendelkező alkalmazások jogosultak a végpontokra irányuló kérések elvégzésére. A metaadatokra adott válaszokat a rendszer a szokásos módon kezeli, és a gép bármely folyamata számára. Ha azonban olyan kérést kap, amely egy jogkivonatot tesz elérhetővé, az ügyfélnek meg kell adnia egy titkot, amely tanúsítja, hogy csak a magasabb jogosultságú felhasználók számára férhet hozzá.

## <a name="prerequisites"></a>Előfeltételek

- A felügyelt identitások ismerete.
- Az ív használatára képes kiszolgálókon létesített és regisztrált kiszolgáló.
- Az előfizetés vagy az erőforráscsoport [tulajdonos csoportjának](../../role-based-access-control/built-in-roles.md#owner)tagja (* *) a szükséges erőforrás-létrehozási és szerepkör-felügyeleti lépések végrehajtásához.
- Egy Azure Key Vault a hitelesítő adatok tárolásához és lekéréséhez. és rendelje hozzá az Azure arc-identitás hozzáférést a kulcstartóhoz.

    - Ha nincs Key Vault létrehozva, tekintse meg a [Key Vault létrehozása](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-)című témakört.
    - A kiszolgáló által használt felügyelt identitás hozzáférésének konfigurálásához lásd: [hozzáférés engedélyezése Linuxra](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) vagy [Windows-hozzáférés biztosítása](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). Az 5. lépésnél adja meg az ív-kompatibilis kiszolgáló nevét. A PowerShell használatával történő elvégzéséhez tekintse meg a [hozzáférési házirend társítása a PowerShell használatával](../../key-vault/general/assign-access-policy-powershell.md)című témakört.

## <a name="acquiring-an-access-token-using-rest-api"></a>Hozzáférési jogkivonat beszerzése REST API használatával

A rendszerszintű felügyelt identitások Azure-erőforrásokkal való hitelesítéshez való beszerzésének és használatának módszere hasonló ahhoz, ahogyan az Azure-beli virtuális géppel történik.

Ahhoz, hogy egy ív engedélyezve legyen a Windows Server rendszerhez, a PowerShell használatával meghívja a webes kérést, hogy megkapja a tokent a helyi gazdagépről az adott porton. Az IP-cím vagy a **IDENTITY_ENDPOINT** környezeti változó használatával határozza meg a kérelmet.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

A következő válasz a visszaadott példa:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="A hozzáférési jogkivonat sikeres beolvasása a PowerShell használatával.":::

Az arc-kompatibilis Linux-kiszolgálók esetében a bash használatával meghívja a webes kérést, hogy megkapja a tokent a helyi gazdagépről az adott porton. Az IP-cím vagy az **IDENTITY_ENDPOINT** környezeti változó használatával határozza meg a következő kérelmet. A lépés elvégzéséhez szüksége lesz egy SSH-ügyfélre.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

A következő válasz a visszaadott példa:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="A hozzáférési jogkivonat sikeres lekérése a bash használatával.":::

A válasz tartalmazza az Azure-beli erőforrásokhoz való hozzáféréshez szükséges hozzáférési jogkivonatot. Ha a konfigurációt a Azure Key Vault hitelesítéséhez szeretné elvégezni, tekintse meg a következőt: [hozzáférés Key Vault a Windowshoz](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) vagy [a Key Vault a Linuxhoz való hozzáféréshez](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Következő lépések

- További információ a Azure Key Vaultről: [Key Vault Overview (áttekintés](../../key-vault/general/overview.md)).

- Ismerje meg, hogyan rendelhet hozzá felügyelt identitás-hozzáférést egy erőforráshoz a [PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) vagy [Az Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)használatával.
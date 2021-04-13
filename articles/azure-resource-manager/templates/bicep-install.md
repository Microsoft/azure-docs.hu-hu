---
title: A bicep fejlesztési és üzembe helyezési környezetek beállítása
description: A bicep fejlesztési és üzembe helyezési környezetek konfigurálása
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2c905a3885fcfc5c9eb9d9db4004126882798611
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313555"
---
# <a name="install-bicep-preview"></a>A bicep (előzetes verzió) telepítése

Megtudhatja, hogyan állíthatja be a bicep fejlesztési és üzembe helyezési környezeteket.

## <a name="development-environment"></a>Fejlesztési környezet

A legjobb bicep-szerzői élmény eléréséhez két összetevőre van szükség:

- **A bicep bővítmény a Visual Studio Code**-hoz. A bicep-fájlok létrehozásához jó bicep szerkesztőre van szükség. Javasoljuk, hogy a [Visual Studio Code](https://code.visualstudio.com/) -ot a [bicep kiterjesztéssel](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Ezek az eszközök nyelvi támogatást és erőforrás-újratöltést biztosítanak. Segítenek létrehozni és érvényesíteni a bicep-fájlokat. További információ a Visual Studio Code és a bicep bővítménnyel kapcsolatban: gyors útmutató [: a bicep-fájlok létrehozása a Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md)használatával.
- **BICEP parancssori** felület. A bicep parancssori felület használatával lefordíthatja a bicep-fájlokat az ARM JSON-sablonokba, és visszafordíthatja az ARM JSON-sablonokat a bicep-fájlokba. A telepítési utasításokért lásd: a [BICEP CLI telepítése](#install-manually).

## <a name="deployment-environment"></a>Üzembehelyezési környezet

Helyi bicep-fájlok telepítéséhez két összetevő szükséges:

- **Az Azure CLI verziója 2.20.0 vagy újabb, vagy Azure PowerShell 5.6.0 vagy újabb verziójú**. A telepítési utasításokért lásd:

  - [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
  - [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
  - [Az Azure CLI telepítése Linux rendszeren](/cli/azure/install-azure-cli-linux)
  - [Az Azure CLI telepítése macOS rendszeren](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > Jelenleg az Azure CLI és a Azure PowerShell is csak helyi bicep-fájlokat telepíthet. A bicep-fájlok Azure CLI használatával történő telepítésével kapcsolatos további információkért lásd: [Deploy-CLI](./deploy-cli.md#deploy-remote-template). A bicep-fájlok Azure PowerShell használatával történő telepítésével kapcsolatos további információkért lásd: [Deploy-PowerShell]( ./deploy-powershell.md#deploy-remote-template).

- **BICEP parancssori** felület. A bicep parancssori felület szükséges a bicep-fájlok JSON-sablonokhoz való fordításához az üzembe helyezés előtt. A telepítési utasításokért lásd: a [BICEP CLI telepítése](#install-bicep-cli).

Az összetevők telepítése után a következővel telepíthet egy bicep-fájlt:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>A bicep CLI telepítése

- Ha a bicep CLI-t a bicep-fájlok fordítására és defordítására szeretné használni, olvassa el a [telepítés manuálisan](#install-manually)című témakört.
- Ha az Azure CLI-t használja a bicep-fájlok üzembe helyezéséhez, tekintse meg [a használat az Azure CLI-vel](#use-with-azure-cli)
- Ha a bicep-fájlok telepítéséhez Azure PowerShellt szeretne használni, tekintse meg [a használat a](#use-with-azure-powershell)következővel: Azure PowerShell.

### <a name="use-with-azure-cli"></a>Használat az Azure CLI-vel

Az Azure CLI 2.20.0 vagy újabb verziójának telepítése esetén a rendszer automatikusan telepíti a bicep CLI-t, amikor egy olyan parancs fut, amelytől függ. Például:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

vagy

```azurecli
az bicep ...
```

Manuálisan is telepítheti a CLI-t a beépített parancsok használatával:

```bash
az bicep install
```

A legújabb verzióra való frissítéshez:

```bash
az bicep upgrade
```

Adott verzió telepítése:

```bash
az bicep install --version v0.3.255
```

> [!IMPORTANT]
> Az Azure CLI telepíti a bicep CLI egy külön verzióját, amely nem ütközik a többi bicep-telepítéssel, és az Azure CLI nem adja hozzá a bicep CLI-t az elérési úthoz. Ha a bicep CLI-t a bicep-fájlok fordítására/kifordítására, illetve a bicep-fájlok telepítéséhez Azure PowerShell használatára szeretné használni, lásd: [manuális telepítés](#install-manually) vagy az [Azure PowerShell használata](#use-with-azure-powershell).

A bicep CLI összes elérhető verziójának listázásához:

```bash
az bicep list-versions
```

A telepített verziók megjelenítése:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Használat Azure PowerShell

A Azure PowerShell nem tudja telepíteni a bicep CLI-t. Azure PowerShell (v 5.6.0 vagy újabb) azt várja, hogy a bicep CLI már telepítve van, és elérhető az elérési úton. Kövesse az egyik [kézi telepítési módszert](#install-manually).

A bicep-fájlok telepítéséhez a bicep CLI-verzió 0.3.1 vagy újabb verziója szükséges. A bicep CLI-verziójának ellenõrzése:

```cmd
bicep --version
```

> [!IMPORTANT]
> Az Azure CLI a bicep CLI saját, önálló verzióját telepíti. Azure PowerShell telepítés meghiúsul, még akkor is, ha az Azure CLI-hez telepítve van a szükséges verzió.

Miután telepítette a bicep CLI-t, a rendszer akkor hívja a bicep CLI-t, amikor szükséges egy telepítési parancsmaghoz. Például:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>Manuális telepítés

A következő módszerekkel telepítheti a bicep CLI-t, és hozzáadhatja az elérési úthoz.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>Homebrew-on keresztül

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

##### <a name="macos-manual-install"></a>macOS manuális telepítés

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

Töltse le és futtassa a [legújabb Windows Installert](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). A telepítőhöz nincs szükség rendszergazdai jogosultságokra. A telepítés után a bicep CLI bekerül a felhasználói elérési útra. Az ELÉRÉSi út módosításának érvénybe léptetéséhez zárjunk be és nyissa meg újra az összes megnyitott parancssori rendszerhéj-ablakot.

##### <a name="chocolatey"></a>Csokoládés

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Manuális PowerShell-lel

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Az éjszakai buildek telepítése

Ha szeretné kipróbálni a legújabb előzetes kiadású bicep-biteket a megjelenésük előtt, tekintse meg az [éjszakai buildek telepítése](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)című témakört.

> [!WARNING]
> Ezek az előzetes verziók sokkal nagyobb valószínűséggel ismert vagy ismeretlen hibákat okozhatnak.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [bicep](./quickstart-create-bicep-use-visual-studio-code.md)rövid útmutatóval.

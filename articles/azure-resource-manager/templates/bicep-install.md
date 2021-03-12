---
title: A bicep fejlesztési és üzembe helyezési környezetek beállítása
description: A bicep fejlesztési és üzembe helyezési környezetek konfigurálása
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0434c08a4427d2549a9adf4e4ab1e7e73a465ad8
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620052"
---
# <a name="setup-bicep-development-and-deployment-environment"></a>A bicep fejlesztési és üzembe helyezési környezet beállítása

Megtudhatja, hogyan lehet beállítani a bicep fejlesztési és üzembe helyezési környezeteket.

## <a name="development-environment"></a>Fejlesztési környezet

A legjobb bicep-szerzői élmény eléréséhez két összetevőre van szükség:

- **A bicep bővítmény a Visual Studio Code**-hoz. A bicep-fájlok létrehozásához jó bicep szerkesztőre van szükség. Javasoljuk, hogy a [Visual Studio Code](https://code.visualstudio.com/) -ot a [bicep kiterjesztéssel](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Ezek az eszközök nyelvi támogatást és erőforrás-újratöltést biztosítanak. Segítenek létrehozni és érvényesíteni a bicep-fájlokat. További információ: rövid útmutató [: a bicep-fájlok létrehozása a Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md)-ban.
- **BICEP parancssori** felület. A bicep parancssori felület használatával lefordíthatja a bicep-fájlokat az ARM JSON-sablonokba, és visszafordíthatja az ARM JSON-sablonokat a bicep-fájlokba. További információ: a [BICEP CLI telepítése](#install-bicep-cli).

## <a name="deployment-environment"></a>Üzembehelyezési környezet

A bicep-fájlokat az Azure CLI vagy a Azure PowerShell használatával helyezheti üzembe. Az Azure CLI esetén a 2.20.0 vagy újabb verzióra van szükség; Azure PowerShell esetén a 5.6.0 vagy újabb verzióra van szükség. A telepítési utasításokért lásd:

- [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
- [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
- [Az Azure CLI telepítése Linux rendszeren](/cli/azure/install-azure-cli-linux)
- [Az Azure CLI telepítése macOS rendszeren](/cli/azure/install-azure-cli-macos)

> [!NOTE]
> Jelenleg az Azure CLI és a Azure PowerShell is csak helyi bicep-fájlokat telepíthet. A bicep-fájlok Azure CLI használatával történő telepítésével kapcsolatos további információkért lásd: [Deploy-CLI](/deploy-cli.md#deploy-remote-template). A bicep-fájlok Azure PowerShell használatával történő telepítésével kapcsolatos további információkért lásd: [Deploy-PowerShell](/deploy-powershell.md#deploy-remote-template).

A Azure PowerShell vagy az Azure CLI támogatott verziójának telepítése után a következővel telepíthet egy bicep-fájlt:

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

A bicep CLI-t az Azure CLI használatával Azure PowerShell vagy manuálisan is telepítheti.

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Az az CLI Version 2.20.0 vagy újabb verziója esetén a rendszer automatikusan telepíti a bicep CLI-t, amikor egy olyan parancs fut, amely attól függ, hogy végrehajtja-e az alkalmazást. Például `az deployment ... -f *.bicep` vagy `az bicep ...`.

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
az bicep install --version v0.2.212
```

> [!NOTE]
> Az az CLI telepíti a bicep CLI egy külön verzióját, amely nem ütközik az esetlegesen használt többi bicep-telepítéssel, és az az CLI nem adja hozzá a Bicept az elérési úthoz.

A telepített verziók megjelenítése:

```bash
az bicep version
```

A bicep CLI összes elérhető verziójának listázásához:

```bash
az bicep list-versions
```

### <a name="use-azure-powershell"></a>Azure PowerShell használatával

A Azure PowerShell nem tudja telepíteni a bicep CLI-t. Azure PowerShell (v 5.6.0 vagy újabb) azt várja, hogy a bicep CLI már telepítve van, és elérhető az elérési úton. Kövesse az egyik [kézi telepítési módszert](#install-manually). Miután telepítette a bicep CLI-t, a rendszer akkor hívja a bicep CLI-t, amikor szükséges egy telepítési parancsmaghoz. Például: `New-AzResourceGroupDeployment ... -TemplateFile main.bicep`.

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
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
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

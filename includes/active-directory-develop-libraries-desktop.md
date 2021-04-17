---
ms.openlocfilehash: 9ddc240257bda29d5db5cae3eb6a830273d339cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590120"
---
| Nyelv/keretrendszer | Projekt a<br/>GitHub                                                                                     | Csomag                                                                               | Szerzés<br/>Kezdett                        | Felhasználók bejelentkezése                                         | Webes API-k elérése                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes<sup>verzió 1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| macOS (Swift/Obj-C)  | [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [A nyilvános előzetes verzióban][preview-tos] elérhető Microsoft Azure kiegészítő használati feltételek a *kódtárakra vonatkoznak.*

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
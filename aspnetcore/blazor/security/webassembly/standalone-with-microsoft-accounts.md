---
title: 使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: 了解如何使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 268debd51b6828aad0bcfe917bdf95b691ac7365
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710511"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用

作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

本文介绍如何创建使用[带有 Azure Active Directory (AAD) 的 Microsoft 帐户](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)进行身份验证的[独立 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)。

在 Azure 门户的“Azure Active Directory” > “应用注册”区域中注册 AAD 应用：

::: moniker range=">= aspnetcore-5.0"

1. 提供应用的名称（例如 Blazor 独立 AAD Microsoft 帐户） 。
1. 在“支持的帐户类型”中，选择“任何组织目录中的帐户”。
1. 将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback`。 在 Kestrel 上运行的应用的默认端口为 5001。 如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。 对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。 由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。 本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。
1. 取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。
1. 选择“注册”。

记录应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）。

在“身份验证”>“平台配置”>“单页应用程序(SPA)”中：

1. 确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。
1. 对于“隐式授权”，请确保没有选中“访问令牌”和“ID 令牌”的复选框。
1. 此体验可接受应用的其余默认值。
1. 选择“保存”按钮。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 提供应用的名称（例如 Blazor 独立 AAD Microsoft 帐户） 。
1. 在“支持的帐户类型”中，选择“任何组织目录中的帐户”。
1. 将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。 在 Kestrel 上运行的应用的默认端口为 5001。 如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。 对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。 由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。 本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。
1. 取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。
1. 选择“注册”。

记录应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）。

在“身份验证”>“平台配置”>“Web”中：

1. 确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。
1. 对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。
1. 此体验可接受应用的其余默认值。
1. 选择“保存”按钮。

::: moniker-end

创建应用。 将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| 占位符   | Azure 门户中的名称       | 示例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | 应用程序（客户端）ID | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。

> [!NOTE]
> 在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置应用的平台配置“重定向 URI”。
>
> 如果应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的应用属性中找到该应用的端口。
>
> 如果端口之前未使用应用的已知端口进行配置，请返回到 Azure 门户中应用的注册，并使用正确的端口更新重定向 URI。

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

创建应用后，应该能够：

* 使用 Microsoft 帐户登录到应用。
* 请求 Microsoft API 的访问令牌。 有关详情，请参阅：
  * [访问令牌作用域](#access-token-scopes)
  * [快速入门：配置应用程序来公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。

## <a name="authentication-package"></a>身份验证包

创建应用以使用工作或学校帐户 (`SingleOrg`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。 此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。

如果向应用添加身份验证，请手动将包添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。

## <a name="authentication-service-support"></a>身份验证服务支持

使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。 此方法设置应用与 Identity 提供者 (IP) 交互所需的所有服务。

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。 注册应用时，可以从 AAD 配置中获取配置应用所需的值。

配置由 `wwwroot/appsettings.json` 文件提供：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

示例：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>访问令牌作用域

Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。 要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的默认访问令牌作用域中：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

使用 `AdditionalScopesToConsent` 指定其他作用域：

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

有关详细信息，请参阅“其他方案”一文的以下部分：

* [请求其他访问令牌](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [将令牌附加到传出请求](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>登录模式

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>导入文件

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>索引页

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 组件

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:blazor/security/webassembly/additional-scenarios>
* [生成 Authentication.MSAL JavaScript 库的自定义版本](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [快速入门：将应用程序注册到 Microsoft 标识平台](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [快速入门：配置应用程序以公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

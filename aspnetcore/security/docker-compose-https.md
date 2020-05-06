---
title: HTTPS를 사용 하 여 docker 작성을 사용 하 여 컨테이너에 ASP.NET Core 이미지 호스팅
author: ravipal
description: HTTPS를 통해 Docker Compose를 사용 하 여 ASP.NET Core 이미지를 호스트 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-compose-https
ms.openlocfilehash: 533d86fb17e3c89fdca59685b090645a11ba5473
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775143"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="21441-103">HTTPS를 통해 Docker Compose를 사용 하 여 ASP.NET Core 이미지 호스팅</span><span class="sxs-lookup"><span data-stu-id="21441-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="21441-104">ASP.NET Core는 [기본적으로 HTTPS를](/aspnet/core/security/enforcing-ssl)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="21441-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) 는 [인증서](https://en.wikipedia.org/wiki/Public_key_certificate) 를 신뢰, id 및 암호화에 의존 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="21441-106">이 문서에서는 HTTPS를 사용 하 여 미리 작성 된 컨테이너 이미지를 실행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="21441-107">개발 시나리오는 [HTTPS를 통해 Docker를 사용 하 여 ASP.NET Core 응용 프로그램 개발](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="21441-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="21441-108">이 샘플에는 docker [클라이언트](https://www.docker.com/products/docker)의 [docker 17.06](https://docs.docker.com/release-notes/docker-ce) 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21441-109">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="21441-109">Prerequisites</span></span>

<span data-ttu-id="21441-110">이 문서의 지침 중 일부에는 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="21441-111">인증서</span><span class="sxs-lookup"><span data-stu-id="21441-111">Certificates</span></span>

<span data-ttu-id="21441-112">도메인에 대 한 [프로덕션 호스팅을](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) 위해서는 [인증 기관의](https://wikipedia.org/wiki/Certificate_authority) 인증서가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="21441-113">[Let's Encrypt](https://letsencrypt.org/)는 무료 인증서를 제공 하는 인증 기관입니다.</span><span class="sxs-lookup"><span data-stu-id="21441-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="21441-114">이 문서에서는 미리 빌드된 이미지를 호스트 하기 위해 [자체 서명 된 개발 인증서](https://wikipedia.org/wiki/Self-signed_certificate) 를 사용 `localhost`합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="21441-115">지침은 프로덕션 인증서를 사용 하는 것과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="21441-116">프로덕션 인증서의 경우:</span><span class="sxs-lookup"><span data-stu-id="21441-116">For production certificates:</span></span>

* <span data-ttu-id="21441-117">도구 `dotnet dev-certs` 는 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="21441-118">지침에 사용 되는 위치에 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="21441-119">사이트 디렉터리 외부의 모든 위치에 인증서를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="21441-120">다음 섹션에 포함 된 지침은 `volumes` *docker-compose.ci.build.yml* 의 속성을 사용 하 여 컨테이너에 인증서를 탑재 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="21441-121">`COPY` *Dockerfile*의 명령을 사용 하 여 컨테이너 이미지에 인증서를 추가할 수 있지만 권장 되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="21441-122">다음과 같은 이유로 인증서를 이미지로 복사 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="21441-123">개발자 인증서를 사용 하 여 테스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="21441-124">프로덕션 인증서를 사용 하 여 호스트 하는 데 동일한 이미지를 사용 하는 것은 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="21441-125">인증서 공개에는 상당한 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="21441-126">Docker 작성을 사용 하 여 https 지원으로 컨테이너 시작</span><span class="sxs-lookup"><span data-stu-id="21441-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="21441-127">운영 체제 구성에 대해 다음 지침을 따르십시오.</span><span class="sxs-lookup"><span data-stu-id="21441-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="21441-128">Linux 컨테이너를 사용 하는 Windows</span><span class="sxs-lookup"><span data-stu-id="21441-128">Windows using Linux containers</span></span>

<span data-ttu-id="21441-129">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="21441-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="21441-130">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="21441-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="21441-131">다음 콘텐츠를 사용 하 여 _docker-compose.ci.build.yml_ 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="21441-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="21441-132">Docker 작성 파일에 지정 된 암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="21441-133">HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="21441-134">macOS 또는 Linux</span><span class="sxs-lookup"><span data-stu-id="21441-134">macOS or Linux</span></span>

<span data-ttu-id="21441-135">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="21441-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="21441-136">`dotnet dev-certs https --trust`는 macOS 및 Windows 에서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21441-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="21441-137">배포판에서 지 원하는 방식으로 Linux에서 인증서를 신뢰 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="21441-138">브라우저에서 인증서를 신뢰 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21441-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="21441-139">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="21441-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="21441-140">다음 콘텐츠를 사용 하 여 _docker-compose.ci.build.yml_ 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="21441-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="21441-141">Docker 작성 파일에 지정 된 암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="21441-142">HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="21441-143">Windows 컨테이너를 사용 하는 windows</span><span class="sxs-lookup"><span data-stu-id="21441-143">Windows using Windows containers</span></span>

<span data-ttu-id="21441-144">인증서 생성 및 로컬 컴퓨터 구성:</span><span class="sxs-lookup"><span data-stu-id="21441-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="21441-145">위의 명령에서을 암호로 바꿉니다 `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="21441-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="21441-146">다음 콘텐츠를 사용 하 여 _docker-compose.ci.build.yml_ 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="21441-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="21441-147">Docker 작성 파일에 지정 된 암호는 인증서에 사용 된 암호와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="21441-148">HTTPS에 대해 구성 된 ASP.NET Core를 사용 하 여 컨테이너를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="21441-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

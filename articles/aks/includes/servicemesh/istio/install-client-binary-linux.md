---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e2dd45f778bd5e596b5bcc91c63984742237ad4c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170819"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Baixar e instalar o binário do cliente İSTİO istioctl

Em um shell baseado em bash no [subsistema Linux ou Windows para Linux][install-wsl], use `curl` para baixar a versão İSTİO e, em seguida, extrair com `tar` da seguinte maneira:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

O binário de cliente do `istioctl` é executado no computador cliente e permite que você interaja com a malha do serviço İSTİO. Use os comandos a seguir para instalar o binário do İSTİO `istioctl` Client em um shell baseado em bash no [subsistema Linux ou Windows para Linux][install-wsl]. Esses comandos copiam o `istioctl` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser concluir a linha de comando do binário de cliente do Istio `istioctl`, configure-o conforme a seguir:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
---
title: Bereitstellen eines Kubernetes-Clusters in einem benutzerdefinierten virtuellen Netzwerk in Azure Stack Hub
description: Hier erfahren Sie, wie Sie einen Kubernetes-Cluster in einem benutzerdefinierten virtuellen Netzwerk in Azure Stack Hub bereitstellen.
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: b8bc4c1a9e56f363fa604e8df7a1fa0dbe37fcb0
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81624981"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>Erstellen eines SSH-Schlüssels für Linux in Azure Stack Hub

Sie können auf einem Windows-Computer einen SSH-Schlüssel (Secure Shell) für Ihren Linux-Computer erstellen. Verwenden Sie den öffentlichen Schlüssel, der mit den Schritten in diesem Artikel generiert wurde, für die SSH-Authentifizierung mit VMs. Installieren Sie bei Verwendung eines Windows-Computers Ubuntu unter Windows, um ein Terminal mit Hilfsprogrammen wie Bash, SSH, Git, apt usw. zu erhalten. Führen Sie **ssh-keygen** aus, um Ihren Schlüssel zu erstellen.

## <a name="open-bash-on-windows"></a>Öffnen von Bash unter Windows

1. Installieren Sie [Ubuntu unter Windows](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), falls das Windows-Subsystem für Linux auf Ihrem Computer nicht installiert ist.  
    Weitere Informationen zur Verwendung des Windows-Subsystems für Linux finden Sie unter [Windows-Subsystem für Linux: Dokumentation](https://docs.microsoft.com/windows/wsl/about).

2. Geben Sie in der Symbolleiste **Ubuntu** ein, und wählen Sie **Öffnen** aus.

## <a name="create-a-key-with-ssh-keygen"></a>Erstellen eines Schlüssels mit ssh-keygen

1. Geben Sie an Ihrer Bash-Eingabeaufforderung den folgenden Befehl ein:

    ```bash  
    ssh-keygen -t rsa
    ```

    In Bash wird die folgende Eingabeaufforderung angezeigt:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. Geben Sie den Dateinamen und die Passphrase ein. Geben Sie die Passphrase erneut ein.

    In Bash wird Folgendes angezeigt:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): key.txt
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in key.txt.
    Your public key has been saved in key.txt.pub.
    The key fingerprint is:
    SHA256:xanotrealoN6z1/KChqeah0CYVeyhL50/0rq37qgy6Ik username@machine
    The key's randomart image is:
    +---[RSA 2048]----+
    |   o.     .      |
    |  . o.   +       |
    | + o .+ o o      |
    |o o .  O +       |
    | . o .o S .      |
    |  o +. .         |
    |.  o +..o. .     |
    |= . ooB +o+ .    |
    |E=..*X=*.. +.    |
    +----[SHA256]-----+
    ```

3. Zeigen Sie den öffentlichen SSH-Schlüssel wie folgt an:

    ```bash
    cat /home/<username>/<filename>
    ```

    In Bash wird in etwa Folgendes angezeigt:

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. Kopieren Sie den Text von `ssh-rsa [...]` bis `username@machinename`. Stellen Sie sicher, dass der Text keine Wagenrückläufe enthält. Sie können diesen Text beim Erstellen Ihrer VM oder Ihres Kubernetes-Clusters mit der AKS-Engine verwenden.

5. Auf einem Windows-Computer können Sie auf Ihre Linux-Dateien mit **\\\\wsl$** zugreifen.

    1. Geben Sie in der Symbolleiste die Zeichenfolge `\\wsl$` ein. Das Standardfenster Ihrer Distribution wird geöffnet.

    2. Navigieren Sie zu `\\wsl$\Ubuntu\home\<username>`, suchen Sie nach dem öffentlichen und privaten Schlüssel, und speichern Sie ihn an einem sicheren Ort.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines Kubernetes-Cluster mit der AKS-Engine in Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [Schnellstart: Erstellen eines virtuellen Linux-Servers mit dem Azure Stack Hub-Portal](azure-stack-quick-linux-portal.md)

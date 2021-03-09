---
title: Verwenden von Active Directory-SSO in AKS für Azure Stack HCI
description: Verwenden von Active Directory-Authentifizierung, um sichere Verbindungen mit dem API-Server mit SSO-Anmeldeinformationen herzustellen
author: v-susbo
ms.topic: how-to
ms.date: 02/12/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: fd4b8982ecaa9de1b3b63dd97cb460772e25971d
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874445"
---
# <a name="use-active-directory-single-sign-in-credentials-for-aks-on-azure-stack-hci"></a>Verwenden von Active Directory-SSO-Anmeldeinformationen für AKS in Azure Stack HCI

> Gilt für: AKS auf Azure Stack HCI, AKS-Runtime unter Windows Server 2019 Datacenter

Ohne Active Directory-Authentifizierung müssen sich Benutzer auf eine zertifikatbasierte _KUBECONFIG_-Datei verlassen, wenn sie über den Befehl `kubectl` eine Verbindung mit dem API-Server herstellen. Die _KUBECONFIG_-Datei enthält Geheimnisse, z. B. private Schlüssel und Zertifikate, die sorgfältig verteilt werden müssen. Dies kann ein erhebliches Sicherheitsrisiko darstellen.

Als Alternative zur Verwendung der zertifikatbasierten _KUBECONFIG_-Datei können Sie AD-SSO-Anmeldeinformationen (Active Directory, Single Sign-On) als sichere Methode zum Herstellen einer Verbindung mit dem API-Server verwenden. Mithilfe von AD-Integration in Azure Kubernetes Service auf Azure Stack HCI kann ein Benutzer auf einem in die Domäne eingebundenen Windows-Computer mithilfe von SSO-Anmeldeinformationen eine Verbindung mit dem API-Server (über `kubectl`) herstellen. Dadurch entfällt die Notwendigkeit, zertifikatbasierte _KUBECONFIG_-Dateien zu verwalten und zu verteilen, die private Schlüssel enthalten.

Bei der AD-Integration wird _KUBECONFIG_ von AD verwendet, die sich von den zertifikatbasierten _KUBECONFIG_-Dateien unterscheidet und keine Geheimnisse enthält. Die zertifikatbasierte _KUBECONFIG_-Datei kann jedoch für Sicherungszwecke verwendet werden, z. B. für Problembehandlung, wenn beim Herstellen einer Verbindung mit Active Directory-Anmeldeinformationen Probleme auftreten.

Ein weiterer Sicherheitsvorteil bei der AD-Integration besteht darin, dass Benutzer und Gruppen als [Sicherheits-IDs (SIDs)](https://docs.microsoft.com/troubleshoot/windows-server/identity/security-identifiers-in-windows) gespeichert werden. Im Gegensatz zu Gruppennamen sind SIDs unveränderlich und eindeutig und stellen daher keine Namenskonflikte dar.

> [!Note] 
> Für dieses Vorschaurelease wird die AD-SSO-Konnektivität nur für Workloadcluster bereitgestellt. 

Dieses Dokument führt Sie durch die folgenden Schritte zum Einrichten von Active Directory als Identitätsanbieter und zum Aktivieren von SSO über `kubectl`:

- Erstellen Sie das AD-Konto für den API-Server, und erstellen Sie dann die [KEYTAB](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html)-Datei, die dem Konto zugeordnet ist. Weitere Informationen finden Sie unter [Erstellen von AD-Authentifizierung mithilfe der KEYTAB-Datei](#create-ad-auth-using-the-keytab-file), um das AD-Konto zu erstellen und die KEYTAB-Datei zu generieren.
- Verwenden Sie die [KEYTAB](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html)-Datei, um AD-Authentifizierung auf dem Kubernetes-Cluster zu installieren. Im Rahmen dieses Schritts wird automatisch eine Standardkonfiguration von rollenbasierter Zugriffssteuerung (Role-Based Access Control, RBAC) erstellt.
- Konvertieren Sie Gruppennamen in SIDs beim Erstellen oder Bearbeiten von RBAC-Konfigurationen und umgekehrt. Anweisungen dazu finden Sie unter [Erstellen und Aktualisieren der Rollenbindung der AD-Gruppe](#create-and-update-the-ad-group-role-binding).
 
## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit dem Konfigurieren Active Directory-SSO-Anmeldeinformationen beginnen, sollten Sie sicherstellen, dass die folgenden Elemente verfügbar sind:

 - Das neueste **Aks-HciI-PowerShell**-Modul ist installiert. Wenn dies nicht der Fall ist, lesen Sie [Herunterladen und Installieren des AksHci-PowerShell-Moduls](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module). 
 - Der AKS-Host ist installiert und konfiguriert. Wenn dies nicht der Fall ist, befolgen Sie die Anweisungen unter [Konfigurieren der Bereitstellung](./setup-powershell.md#step-4-configure-your-deployment).  
 - Stellen Sie sicher, dass die KEYTAB-Datei für die Verwendung verfügbar ist. Wenn dies nicht der Fall ist, lesen Sie [Erstellen des AD-Kontos des API-Servers und der KEYTAB-Datei](#create-the-api-server-ad-account-and-the-keytab-file). 
 - Die KEYTAB-Datei wird für einen bestimmten Dienstprinzipalnamen (Service Principal Name, SPN) generiert, und dieser SPN muss dem AD-Konto des API-Servers des Workloadclusters entsprechen. Außerdem sollten Sie sicherstellen, dass derselbe SPN im gesamten AD-Authentifizierungsprozess verwendet wird. Die KEYTAB-Datei muss den Namen _current.keytab_ aufweisen.
 - Stellen Sie für jeden AKS in Azure Stack HCI-Workloadcluster sicher, dass ein AD-Konto des API-Servers verfügbar ist.
 - Beim Clientcomputer muss es sich um einen in die Domäne eingebundenen Windows-Computer handeln.

## <a name="create-ad-auth-using-the-keytab-file"></a>Erstellen von AD-Authentifizierung mithilfe der KEYTAB-Datei

### <a name="step-1-create-the-workload-cluster-and-enable-ad-authentication"></a>Schritt 1: Erstellen des Workloadclusters und Aktivieren von AD-Authentifizierung

Bevor Sie AD-Authentifizierung installieren, müssen Sie zunächst einen AKS in Azure Stack HCI-Workloadcluster erstellen und das AD-Authentifizierungs-Add-On für den Cluster aktivieren. Wenn Sie AD-Authentifizierung beim Erstellen des neuen Clusters nicht aktivieren, können Sie sie später nicht mehr aktivieren.

Öffnen Sie PowerShell als Administrator und führen Sie Folgendes mit dem Parameter **-enableADAuth** des Befehls `New-AksHciCluster` aus:

```powershell
New-AksHciCluster -name mynewcluster1 -enableADAuth
```

Stellen Sie für jeden Workloadcluster sicher, dass ein AD-Konto des API-Servers verfügbar ist.

Ausführliche Informationen zum Erstellen des Workloadclusters finden Sie unter [Erstellen von Kubernetes-Clustern mithilfe von Windows PowerShell](./create-kubernetes-cluster-powershell.md).

### <a name="step-2-install-ad-authentication"></a>Schritt 2: Installieren von AD-Authentifizierung

Bevor Sie die AD-Authentifizierung installieren können, müssen Sie sicherstellen, dass der Workloadcluster installiert ist und AD-Authentifizierung auf dem Cluster aktiviert ist. Verwenden Sie eine der folgenden Optionen, um AD-Authentifizierung zu installieren.

#### <a name="option-1"></a>Option 1:

Öffnen Sie für einen in die Domäne eingebundenen Azure Stack HCI-Cluster PowerShell als Administrator, und führen Sie den folgenden Befehl aus:

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8s/apiserver@CONTOSO.COM -adminUser contoso\bob
```  

#### <a name="option-2"></a>Option 2:

Wenn der Clusterhost nicht in die Domäne eingebunden ist, verwenden Sie den Administratorbenutzernamen oder den Gruppennamen im SID-Format, wie im folgenden Beispiel gezeigt:
 
```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8
```  

Informationen zum Ermitteln der SID für das Benutzerkonto finden Sie unter [Bestimmen der Benutzer- oder Gruppen-SID](#determine-the-user-or-group-security-identifier). 

Bevor Sie mit den nächsten Schritten fortfahren, sollten Sie die folgenden Punkte beachten:

- Stellen Sie sicher, dass die KEYTAB-Datei den Namen _current.keytab_ aufweist.
- Ersetzen Sie den SPN, der Ihrer Umgebung entspricht.
- **-adminUser** erstellt eine entsprechende Rollenbindung für die angegebene AD-Gruppe mit Clusteradministratorrechten. Ersetzen Sie _contoso\bob_ durch die AD-Gruppe bzw. den Benutzer, die oder der Ihrer Umgebung entspricht.

### <a name="step-3-test-the-ad-webhook-and-keytab-file"></a>Schritt 3: Testen des AD-Webhooks und der KEYTAB-Datei

Sie müssen sicherstellen, dass der AD-Webhook auf dem API-Server ausgeführt wird und die KEYTAB-Datei als Kubernetes-Geheimnis gespeichert wird. Führen Sie die folgenden Schritte aus, um die zertifikatbasierte _KUBECONFIG_-Datei für den Workloadcluster abzurufen:

1. Rufen Sie eine zertifikatbasierte _KUBECONFIG_-Datei ab. Sie verwenden die _KUBECONFIG_-Datei, um mithilfe des folgenden Befehls eine Verbindung mit dem Cluster als lokaler Host herzustellen:

   ```powershell
   Get-AksHciCredential -name mynewcluster1
   ```  

2. Führen Sie `kubectl` auf dem Server aus, mit dem Sie eine Verbindung hergestellt haben (unter Verwendung der zertifikatsbasierten _KUBECONFIG_-Datei, die Sie zuvor erstellt haben), und überprüfen Sie dann die AD-Webhookbereitstellung, um sicherzustellen, dass sie das Format _ad-auth-webhook-xxxx_ aufweist.  

    ```bash
    kubectl get pods -n=kube-system
    ```

3. Führen `kubectl` Sie aus, um zu bestätigen, dass die KEYTAB-Datei als Geheimnis bereitgestellt wurde und als Kubernetes-Geheimnis aufgeführt wird: 

   ```bash
   kubectl get secrets -n=kube-system
   ```

### <a name="step-4-create-the-ad-kubeconfig-file"></a>Schritt 4: Erstellen der AD-KUBECONFIG-Datei

Nachdem der AD-Webhook und die KEYTAB-Datei erfolgreich bereitgestellt wurden, erstellen Sie die AD-_KUBECONFIG_-Datei.
Nachdem die Datei erstellt wurde, kopieren Sie die AD-_KUBECONFIG_-Datei auf den Clientcomputer und verwenden sie für die Authentifizierung beim API-Server. Im Gegensatz zur zertifikatsbasierten _KUBECONFIG_-Datei ist AD-_KUBECONFIG_ kein Geheimnis und kann sicher als Nur-Text kopiert werden.

Öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus:  

   ```powershell
   Get-AksHciCredential -name mynewcluster1 -outputLocation .\AdKubeconfig -adAuth
   ```

### <a name="step-5-copy-kubeconfig-and-other-files-to-the-client-machine"></a>Schritt 5: Kopieren von KUBECONFIG- und anderen Dateien auf den Clientcomputer

Kopieren Sie die unten aufgeführten drei Dateien aus dem Azure Stack HCI-Cluster auf Ihren Clientcomputer:

- Kopieren Sie die im vorherigen Schritt erstellte AD-_KUBECONFIG_-Datei in „$env:USERPROFILE\.kube\config“.

- Erstellen Sie den Ordnerpfad `c:\adsso`, und kopieren Sie die folgenden Dateien aus dem Azure Stack HCI-Cluster auf den Clientcomputer.
  - „Kubectl.exe“ unter `$env:ProgramFiles\AksHci` in „c:\adsso“.
  - „Kubectl-adsso.exe“ unter `$env:ProgramFiles\AksHci` in „c:\adsso“.

### <a name="step-6-connect-to-the-api-server-from-the-client-machine"></a>Schritt 6: Herstellen einer Verbindung mit dem API-Server über den Clientcomputer

Nachdem Sie die vorherigen Schritte abgeschlossen haben, melden Sie sich mit ihren SSO-Anmeldeinformationen bei Ihrem in die Domäne eingebundenen Windows-Clientcomputer an. Öffnen Sie PowerShell, und versuchen Sie dann, mithilfe von `kubectl` auf den API-Server zuzugreifen. Wenn Sie zur Authentifizierung aufgefordert werden, haben Sie AD-SSO erfolgreich eingerichtet.

## <a name="create-and-update-the-ad-group-role-binding"></a>Erstellen und Aktualisieren der Rollenbindung der AD-Gruppe

Wie in Schritt 2 erwähnt, wird eine Standardrollenbindung mit Clusteradministratorrechten für den Benutzer und/oder die Gruppe erstellt, der bzw. die während der Installation bereitgestellt wurde. Rollenbindung in Kubernetes definiert die Zugriffsrichtlinien für AD-Gruppen. In diesem Schritt wird beschrieben, wie Sie mithilfe von RBAC neue AD-Gruppenrollenbindungen in Kubernetes erstellen und vorhandene Rollenbindungen bearbeiten. Der Clusteradministrator möchte z. B. Benutzern mithilfe von AD-Gruppen zusätzliche Berechtigungen erteilen (wodurch der Prozess effizienter wird). Weitere Informationen zu RBAC finden Sie unter [Verwenden von RBAC-Autorisierung](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

Wenn Sie weitere RBAC-Einträge für die AD-Gruppe erstellen oder bearbeiten, sollte dem Antragstellernamen „`microsoft:activedirectory:CONTOSO\\group name`“ als Präfix vorangestellt werden. Beachten Sie, dass die Namen einen Domänennamen und ein Präfix enthalten müssen, die in doppelte Anführungszeichen eingeschlossen sind.

Zwei Beispiele:

**Beispiel 1**

```bash
apiVersion: rbac.authorization.k8s.io/v1 
 kind: ClusterRoleBinding 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: ClusterRole 
   name: cluster-admin 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\Bob" 
```

**Beispiel 2**

Das folgende Beispiel zeigt, wie Sie eine benutzerdefinierte Rolle und Rollenbindung für einen [Namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) mit einer AD-Gruppe erstellen. Im Beispiel ist „SREGroup“ eine bereits vorhandene Gruppe im Contoso-Active Directory. Wenn Benutzer der AD-Gruppe hinzugefügt werden, werden ihnen sofort Berechtigungen erteilt.

```bash
kind: Role 
 apiVersion: rbac.authorization.k8s.io/v1 
 metadata: 
   name: sre-user-full-access 
   namespace: sre 
 rules: 
 - apiGroups: ["", "extensions", "apps"] 
   resources: ["*"] 
   verbs: ["*"] 
 - apiGroups: ["batch"] 
   resources: 
   - jobs 
   - cronjobs 
   verbs: ["*"] 
 apiVersion: rbac.authorization.k8s.io/v1 
 kind: RoleBinding 
 namespace: sre 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: Role 
   name: sre-user-full-access 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\SREGroup" 
```

Vor dem Anwenden der YAML-Datei sollten die **Gruppen- und Benutzernamen** mit dem folgenden Befehl immer in SIDs konvertiert werden:

```bash
kubectl-adsso nametosid <rbac.yml>
```  

Um eine vorhandene RBAC zu aktualisieren, können Sie die SID auch in einen benutzerfreundlichen Gruppennamen konvertieren, bevor Sie Änderungen vornehmen. Verwenden Sie zum Konvertieren der SID den folgenden Befehl: 

```bash
kubectl-adsso sidtoname <rbac.yml>
```

## <a name="change-the-ad-account-password-associated-with-the-api-server-account"></a>Ändern des AD-Kontokennworts, das dem Konto des API-Servers zugeordnet ist

Wenn das Kennwort für das Konto des API-Servers geändert wird, müssen Sie das Add-On für AD-Authentifizierung deinstallieren und mithilfe der aktualisierten aktuellen und vorherigen KEYTAB-Dateien neu installieren. 

Jedes Mal, wenn Sie das Kennwort ändern, müssen Sie die aktuelle KEYTAB-Datei (_current.keytab_) in _previous.keytab_ umbenennen und dann sicherstellen, dass Sie das neue Kennwort _current.keytab_ nennen.

> [!Important] 
> Die Dateien müssen den Namen _current.keytab_ bzw. _previous.keytab_ aufweisen. Die vorhandenen Rollenbindungen sind hiervon nicht betroffen.

### <a name="uninstall-and-reinstall-ad-authentication"></a>Deinstallieren und erneutes Installieren von AD-Authentifizierung

Möglicherweise möchten Sie AD-SSO neu installieren, wenn das Konto für den API-Server geändert bzw. das Kennwort aktualisiert wird, oder um einen Fehler zu beheben.

Um AD-Authentifizierung zu deinstallieren, öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus:  

```powershell
Uninstall-AksHciAdAuth -name mynewcluster1
```

Um AD-Authentifizierung erneut zu deinstallieren, öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus:

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

> [!Note] 
> Der `-previousKeytab`-Parameter ist nur erforderlich, wenn Sie das Kennwort ändern. Dadurch vermeiden Sie Ausfallzeiten, wenn die Clients Tickets zwischengespeichert haben.

## <a name="create-the-api-server-ad-account-and-the-keytab-file"></a>Erstellen des AD-Kontos des API-Servers und der KEYTAB-Datei

Für diesen Prozess sind zwei Schritte erforderlich. Erstellen Sie zunächst ein neues AD-Konto bzw. einen neuen Benutzer für den API-Server mit dem Dienstprinzipalnamen (SPN), und erstellen Sie dann die KEYTAB-Datei für das AD-Konto.

### <a name="step-1-create-a-new-ad-account-or-user-for-the-api-server"></a>Schritt 1: Erstellen eines neuen AD-Kontos oder -Benutzers für den API-Server

Verwenden Sie den PowerShell-Befehl [New-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/new-aduser?view=win10-ps&preserve-view=true), um ein neues AD-Konto bzw. einen neuen -Benutzer mit dem SPN zu erstellen. Hier sehen Sie ein Beispiel: 

```powershell 
New-ADUser -Name apiserver -ServicePrincipalNames k8s/apiserver -AccountPassword (ConvertTo-SecureString "password" -AsPlainText -Force) -KerberosEncryptionType AES128 -Enabled 1
```

### <a name="step-2-create-the-keytab-file-for-the-ad-account"></a>Schritt 2: Erstellen der KEYTAB-Datei für das AD-Konto

Um die KEYTAB-Datei zu erstellen, verwenden Sie den Windows-Befehl [ktpass](https://docs.microsoft.com/windows-server/administration/windows-commands/ktpass). 

Das folgende Beispiel zeigt die Verwendung von ktpass:

```bash
ktpass /out current.keytab /princ k8s/apiserver@BCONTOSO.COM /mapuser contoso\apiserver_acct /crypto all /pass p@$$w0rd /ptype KRB5_NT_PRINCIPAL
```

> [!NOTE]
> Wenn dieser Fehler angezeigt wird, hat **DsCrackNames 0x2 im Namenseintrag zurückgegeben**. Stellen Sie sicher, dass der Parameter für `/mapuser` das Format `mapuser DOMAIN\user` aufweist, wobei DOMAIN die Ausgabe von echo `%userdomain%` ist.

  
## <a name="determine-the-user-or-group-security-identifier"></a>Bestimmen der Benutzer- oder Gruppen-SID

Verwenden Sie eine der beiden folgenden Optionen, um die SID für Ihr Konto oder andere Konten zu ermitteln.

Geben Sie an einer Eingabeaufforderung Ihres Stammverzeichnisses Folgendes ein, um die SID zu ermitteln, die Ihrem Konto zugeordnet ist:

```bash
whoami /user
``` 

Öffnen Sie PowerShell als Administrator, und führen Sie Folgendes aus, um die SID zu ermitteln, die einem anderen Konto zugeordnet ist:

```powershell
(New-Object System.Security.Principal.NTAccount(<CONTOSO\Bob>)).Translate([System.Security.Principal.SecurityIdentifier]).value
```

## <a name="next-steps"></a>Nächste Schritte 

In diesem Leitfaden mit Vorgehensweisen haben Sie erfahren, wie Sie AD-Authentifizierung konfigurieren, um eine sichere Verbindung mit dem API-Server mit SSO-Anmeldeinformationen herzustellen. Als Nächstes haben Sie folgende Möglichkeiten:

- [Bereitstellen von Linux-Anwendungen in einem Kubernetes-Cluster](./deploy-linux-application.md).
- [Bereitstellen einer Windows Server-Anwendung in einem Kubernetes-Cluster](./deploy-windows-application.md).

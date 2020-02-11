---
title: Funzionalità iCloud in Xamarin.iOS
description: L'aggiunta di funzionalità a un'applicazione spesso richiede una configurazione aggiuntiva del provisioning. Questa guida illustra la configurazione necessaria per le funzionalità iCloud.
ms.prod: xamarin
ms.assetid: 3CBAC982-D8DE-48DD-97CD-32B551D9DB85
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: c8c1a5d284e5faaffae33a724c461b6fd74cf39b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028580"
---
# <a name="icloud-capabilities-in-xamarinios"></a>Funzionalità iCloud in Xamarin.iOS

_L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per le funzionalità iCloud._

iCloud offre agli utenti iOS un modo semplice e pratico per archiviare contenuti e condividerli tra i dispositivi. Gli sviluppatori possono usare iCloud in quattro modi per offrire ai propri utenti un mezzo di archiviazione: archiviazione chiave-valore, archiviazione UIDocument, CoreData e uso diretto di CloudKit per l'archiviazione di singoli file e directory. Per altre informazioni su questi argomenti, vedere la guida [Introduction to iCloud](~/ios/data-cloud/introduction-to-icloud.md) (Introduzione a iCloud).

L'aggiunta della funzionalità iCloud a un'applicazione è leggermente più complessa rispetto ad altri servizi app a causa dei _contenitori_. In iCloud i contenitori vengono usati per archiviare le informazioni per un'app e consentire la separazione di tutte le informazioni contenute in un singolo account iCloud, come il sandboxing nel dispositivo iOS di un utente. Per altre informazioni sui contenitori, vedere la guida [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introduzione a CloudKit).

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

<a name="icloud-developer-center" />

## <a name="developer-center"></a>Centro sviluppatori

Durante il provisioning di una nuova app tramite il centro sviluppatori è necessario eseguire due passaggi:

1. Creare un contenitore.
2. Creare un ID app con la funzionalità iCloud e aggiungervi il contenitore.
3. Creare un profilo di provisioning che includa questo ID app

La procedura seguente illustra questi passaggi:

1. Visitare il [centro sviluppatori Apple](https://developer.apple.com/account/) e passare alla sezione Certificates, Identifier, and Profiles (Certificati, identificatori e profili): 
    
     ![Pagina principale del centro sviluppatori Apple](icloud-capabilities-images/image22.png)

2. In **Identifiers** (Identificatori) selezionare **iCloud Containers** (Contenitori iCloud) e quindi selezionare il pulsante **+** per creare un nuovo contenitore:  
    
    ![Schermata del contenitore iCloud](icloud-capabilities-images/image23.png)

3. Immettere un valore nel campo **Description** (Descrizione) e un valore univoco nel campo **Identifier** (Identificatore) per il contenitore iCloud: 
    
    ![Schermata di registrazione del contenitore iCloud](icloud-capabilities-images/image24.png)

4. Premere **Continue** (Continua), verificare che le informazioni siano corrette e premere **Register** (Registra) per creare il contenitore iCloud:  
    
    ![Schermata di registrazione del contenitore iCloud](icloud-capabilities-images/image25.png)

Per creare un nuovo ID app e aggiungervi un contenitore, seguire questa procedura:

1. Nel [centro sviluppatori](https://developer.apple.com/account/) fare clic su **App IDs** (ID app) in **Identifiers** (Identificatori): 
    
    ![Sezione dell'identificatore nel centro sviluppatori](icloud-capabilities-images/image26.png)

2. Selezionare il pulsante **+** per aggiungere un nuovo ID app: 
    
    ![Pulsante per aggiungere un nuovo ID app](icloud-capabilities-images/image27.png)

3. Immettere un valore nel campo **Name** (Nome) per l'ID app e assegnargli un **Explicit App ID** (ID app esplicito):
    
    ![Immettere i dettagli del nuovo ID app](icloud-capabilities-images/image28.png)

4. In **App Services** (Servizi app) selezionare **iCloud** e scegliere **Include CloudKit support** (Includi supporto CloudKit):
    
    ![Selezionare i servizi app iCloud](icloud-capabilities-images/image29.png)

5. Selezionare **Continue** (Continua) e quindi **Register** (Registra). Si noti che nella schermata di conferma iCloud viene visualizzato con la voce Configurable (Configurabile) selezionata, con un simbolo giallo:   
    
    ![Schermata di conferma](icloud-capabilities-images/image30.png)

6. Tornare all'elenco di ID app e selezionare quello appena creato: 
    
    ![Schermata di selezione ID app](icloud-capabilities-images/image31.png)

7. Scorrere fino alla fine di questa sezione espansa e fare clic su **Edit** (Modifica):
    
    ![Modifica ID app](icloud-capabilities-images/image32.png)

8. Scorrere l'elenco verso il basso fino a iCloud e fare clic sul pulsante **Edit** (Modifica):  
    
    ![Modifica ID app iCloud](icloud-capabilities-images/image33.png)

9. Selezionare il contenitore da usare con questo ID app:  
    
    ![Schermata di selezione del contenitore](icloud-capabilities-images/image34.png)

10. Verificare le assegnazioni del contenitore e premere **Assign** (Assegna).

Questo ID app può ora essere usato per generare o rigenerare un nuovo profilo di provisioning, come descritto nella guida [Working with Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Uso delle funzionalità). 

Per altre informazioni sull'uso di iCloud, vedere le guide seguenti:

* [Introduction to iCloud](~/ios/data-cloud/introduction-to-icloud.md) (Introduzione a iCloud)
* [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introduzione a CloudKit)
* [Introduction to Document Picker](~/ios/platform/document-picker.md) (Introduzione a Selezione documento)

## <a name="next-steps"></a>Passaggi successivi

Nell'elenco seguente vengono descritti i passaggi aggiuntivi che potrebbero essere necessari:

* Usare lo spazio dei nomi del framework nell'app.
* Aggiungere all'app gli entitlement necessari. Per informazioni dettagliate sugli entitlement necessari e su come aggiungerli, vedere la guida [Uso degli entitlement](~/ios/deploy-test/provisioning/entitlements.md).
* In  **Firma del bundle iOS** dell'app assicurarsi che **Entitlement personalizzati** sia impostato su **Entitlements.plist**. Questo _non_ è l'impostazione predefinita per le compilazioni di debug e del simulatore iOS.

In caso di problemi con i servizi app, vedere la sezione [Troubleshooting](~/ios/deploy-test/provisioning/capabilities/index.md) (Risoluzione dei problemi) della guida principale.

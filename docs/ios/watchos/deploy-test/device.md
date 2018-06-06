---
title: Test nei dispositivi di Apple Watch
description: Questo documento viene descritto come distribuire un'app watchOS compilata con Xamarin per i test su un Apple Watch effettivo. Vengono illustrati i dispositivi, i profili, test, il provisioning e vengono forniti alcuni suggerimenti sulla risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: a960d81d41ff127fa3316e6190dfbf4881305c02
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790861"
---
# <a name="testing-on-apple-watch-devices"></a>Test nei dispositivi di Apple Watch

Dopo aver eseguito la [passaggi di distribuzione](~/ios/watchos/deploy-test/index.md) per creare gli ID di App e i gruppi di App (se richiesto), utilizzare le istruzioni riportate in questa pagina per:

- [Configurazione dei dispositivi](#devices) nel centro per sviluppatori di Apple, e
- [Creare profili di Provisioning sviluppo](#profiles), quindi
- [Distribuire e testare](#testing) su un Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivi

Test di App iOS in un reale iPhone o iPad ha sempre richiesto il dispositivo sia registrato in Dev Center. L'elenco dei dispositivi è simile al seguente (fare clic sul segno più **+** per aggiungere un nuovo dispositivo):

![](device-images/devices-sml.png "L'elenco dei dispositivi è simile al seguente")

Le espressioni di controllo non sono diversi, è ora necessario aggiungere il dispositivo di Apple Watch prima di distribuire le app. Ricerca di espressioni di controllo UDID utilizzando **Xcode** (**Windows > dispositivi** elenco). Quando è connesso il telefono associato verranno visualizzate anche le informazioni dell'orologio:

[![](device-images/xcode-devices-sml.png "Informazioni di controllo associati")](device-images/xcode-devices.png#lightbox)

Quando si conosce l'orologio UDID, aggiungerlo all'elenco di dispositivo nel centro per sviluppatori:

![](device-images/devices-watch-sml.png "L'orologio UDID nell'elenco dei dispositivi")

Dopo aver aggiunto il dispositivo di espressioni di controllo, verificare che sia selezionato in qualsiasi nuovo o esistente di sviluppo o profili di provisioning ad hoc creati:

![](device-images/devices-provisioning.png "Elenco dei dispositivi disponibili")

Non dimenticare se si modifica un profilo di provisioning esistente per scaricarlo e installarlo nuovamente.

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Sviluppo di profili di Provisioning

Per compilare per il test sul tuo dispositivo è necessario creare un **profilo di Provisioning di sviluppo** per ogni ID di App nella soluzione.

Se si dispone di un carattere jolly ID App, *sarà necessario un solo profilo di Provisioning*; ma se si dispone di un ID App separate per ogni progetto, è necessario un profilo di provisioning per ogni ID di App:

![](device-images/provisioningprofile-development.png "Profilo di Provisioning di sviluppo")

Dopo aver creato tutti e tre i profili, questi verranno visualizzati nell'elenco. Ricordarsi di scaricare e installare ciascuno di essi:

![](device-images/provisioningprofiles.png "I profili di Provisioning sviluppo disponibili")

È possibile verificare il profilo di provisioning nel **opzioni progetto** selezionando il **compilazione > firma Bundle iOS** dello schermo e selezionando il **versione** o **Debug iPhone** configurazione.

Il **profilo di Provisioning** elenco verranno visualizzati tutti i profili corrispondenti, si noterà che i profili corrispondenti che sono stati creati in questo elenco a discesa:

![](device-images/options-selectprofile.png "L'elenco dei profili di Provisioning")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Test su un dispositivo di espressioni di controllo

Dopo aver configurato il dispositivo, ID di App e i profili di Provisioning, si è pronti per eseguire il test.

1. Verificare che sia collegato un iPhone, e le espressioni di controllo è già associato a iPhone.

2. Verificare la configurazione è impostata su **versione** o **Debug**.

3. Verificare che il dispositivo iPhone connesso sia selezionato nell'elenco di destinazione.

4. Fare clic sul progetto di App iOS (non le espressioni di controllo o estensione) e scegliere **imposta come progetto di avvio**.

5. Fare clic su di **eseguire** pulsante (o scegliere un **avviare** opzione il **eseguire** menu).

6. La soluzione verrà compilata e verrà distribuito l'app iOS per iPhone.
  Se l'app iOS o il provisioning di estensione di espressioni di controllo non è impostato correttamente la distribuzione in iPhone avrà esito negativo.

7. Se la distribuzione viene completata correttamente, iPhone tenterà automaticamente di inviare l'app di espressioni di controllo per le espressioni di controllo associato. Icona dell'app verrà visualizzata nella finestra Espressioni di controllo con circolare *installazione* indicatore di stato.

8. Se è stata installata l'app watch, l'icona rimarrà nella finestra Espressioni di controllo - touch per avviare il test dell'app.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante l'utilizzo di distribuzione di **Vista > Pad > Log dispositivo** per visualizzare ulteriori informazioni sull'errore. Di seguito sono elencate alcuni errori e le relative cause:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Errore MT3001: Impossibile non AOT assembly

Ciò può verificarsi durante la compilazione in modalità di DEBUG per distribuire in un dispositivo Apple Watch.

Per *temporaneamente* risolvere questo problema, disabilitare **compilazioni incrementali** nell'estensione Watch **opzioni progetto > compilare > watchOS compilazione** finestra:

[![](device-images/disable-incremental-sml.png "La casella di controllo compilazioni incrementali")](device-images/disable-incremental.png#lightbox)

Questo problema verrà risolto in una versione futura, dopo il quale le compilazioni incrementali possono essere riabilitate per sfruttare i tempi di compilazione.


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Applicazione delle espressioni di controllo non viene avviato durante il debug nel dispositivo

Quando vengono visualizzati il tentativo di eseguire il debug di un'applicazione di espressioni di controllo in un dispositivo fisico, solo l'icona & caricamento spinner (e infine timeout). Questo verrà risolti nelle versioni future. una soluzione alternativa consiste nell'eseguire una build di rilascio (che non consente il debug).


### <a name="invalid-application-executable-or-application-verification-failed"></a>File eseguibile dell'applicazione non valido o verifica dell'applicazione non è riuscita

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Avviso di applicazione eseguibile non valido")

Se vengono visualizzati questi messaggi *nella finestra Espressioni di controllo* dopo l'applicazione ha tentato di installare, potrebbe esserci un paio di problemi:

- Non è stato aggiunto il dispositivo di espressioni di controllo stesso come un dispositivo nel centro per sviluppatori di Apple. Seguire le istruzioni per [configurare correttamente i dispositivi](#devices).

- I profili di provisioning di sviluppo utilizzati per il test non conteneva il dispositivo di espressioni di controllo incluso; oppure, dopo le espressioni di controllo è stato aggiunto ai profili di provisioning non sono stati scaricati nuovamente e reinstallati. Seguire le istruzioni per [configurare correttamente i profili di provisioning](#profiles).

- Se il **iOS Log dispositivo** contiene `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` quindi espressioni di controllo dell'App **Info. plist** il è errato **MinimumOSVersion** valore.
  Deve essere **8.2** : se è stato installato 6.3 Xcode potrebbe essere necessario modificare manualmente l'origine di insert impostata su 8.2.

- L'applicazione di espressioni di controllo **Entitlements.plist** in modo non corretto ha un diritto abilitato (ad esempio i gruppi di App) non deve essere.

- L'applicazione di espressioni di controllo **ID App** in modo non corretto ha un diritto abilitato (ad esempio i gruppi di App) nel centro per sviluppatori che non devono avere.



### <a name="install-never-finished"></a>Non installare mai completata

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Questo errore potrebbe indicare chiavi non necessari (e non valide) in espressioni di controllo dell'App **Info. plist** file. Non è necessario includere le chiavi per l'estensione per iOS app o espressioni di controllo nell'applicazione di espressioni di controllo.

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"in attesa del debugger per la connessione"

Se il **Output dell'applicazione** si blocca finestra che mostra

```csharp
waiting for debugger to connect
```

controllare se uno qualsiasi dei NuGets che sono stati inclusi nel progetto presenta una dipendenza da **Microsoft.Bcl.Build**. Viene aggiunto automaticamente con alcune librerie di pubblicazione Microsoft tra cui i diffusi [Microsoft Http Client Libraries](http://www.nuget.org/packages/Microsoft.Net.Http/).

Il **Microsoft.Bcl.Build.targets** file che viene aggiunto al **csproj** può interferire con il pacchetto di estensioni iOS durante la distribuzione. È possibile tenere traccia di [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Una possibile soluzione consiste nel modificare il file con estensione csproj e spostare manualmente il **Microsoft.Bcl.Build.targets** per essere l'ultimo elemento.


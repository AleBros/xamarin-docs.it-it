---
title: Test sui dispositivi Apple Watch
description: Questo documento descrive come distribuire un'app watchos compilata con Novell per il testing in un Apple Watch effettivo. Vengono illustrati i dispositivi, i profili di provisioning, i test e vengono forniti alcuni suggerimenti per la risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 4b1e232259d7b1816e64298b5c0b8853d8385c20
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283862"
---
# <a name="testing-on-apple-watch-devices"></a>Test sui dispositivi Apple Watch

Dopo aver seguito la [procedura di distribuzione](~/ios/watchos/deploy-test/index.md) per creare gli ID app e i gruppi di app (se necessario), seguire le istruzioni in questa pagina per:

- [Configurare i dispositivi](#devices) in Apple Dev Center e
- [Creare i profili di provisioning di sviluppo](#profiles), quindi
- [Distribuire e testare](#testing) in un Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivi

Il test delle app iOS in un iPhone o iPad reale ha sempre richiesto la registrazione del dispositivo in Dev Center. L'elenco dei dispositivi ha un aspetto simile al seguente ( **+** fare clic sul segno più per aggiungere un nuovo dispositivo):

![](device-images/devices-sml.png "L'elenco dei dispositivi ha un aspetto simile al seguente")

Gli orologi non sono diversi. è ora necessario aggiungere il dispositivo Apple Watch prima di distribuire le app. Trovare il UDID dell'orologio usando **Xcode** (**Windows > Devices** List). Quando il telefono associato è connesso, verranno visualizzate anche le informazioni dell'espressione di controllo:

[![](device-images/xcode-devices-sml.png "Informazioni di controllo associate")](device-images/xcode-devices.png#lightbox)

Quando si conosce il UDID dell'orologio, aggiungerlo all'elenco dei dispositivi in Dev Center:

![](device-images/devices-watch-sml.png "UDID dell'espressione di controllo nell'elenco dei dispositivi")

Quando il dispositivo di controllo è stato aggiunto, assicurarsi che sia selezionato in uno sviluppo nuovo o esistente o in profili di provisioning ad hoc creati:

![](device-images/devices-provisioning.png "Elenco dei dispositivi disponibili")

Non dimenticare se si modifica un profilo di provisioning esistente per scaricare e reinstallare.

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Profili di provisioning di sviluppo

Per compilare per i test nel dispositivo è necessario creare un **profilo di provisioning di sviluppo** per ogni ID app nella soluzione.

Se si dispone di un ID app con caratteri jolly, *sarà necessario solo un profilo di provisioning*; Tuttavia, se si dispone di un ID app separato per ogni progetto, è necessario un profilo di provisioning per ogni ID app:

![](device-images/provisioningprofile-development.png "Profilo di provisioning di sviluppo")

Una volta creati tutti e tre i profili, verranno visualizzati nell'elenco. Ricordarsi di scaricare e installare ognuno di essi:

![](device-images/provisioningprofiles.png "Profili di provisioning di sviluppo disponibili")

È possibile verificare il profilo di provisioning nelle **Opzioni del progetto** selezionando la schermata **Compila > firma del bundle iOS** e selezionando la configurazione di **rilascio** o **debug di iPhone** .

L'elenco **profilo di provisioning** mostrerà tutti i profili corrispondenti. verranno visualizzati i profili corrispondenti creati in questo elenco a discesa:

![](device-images/options-selectprofile.png "Elenco del profilo di provisioning")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Test su un dispositivo di controllo

Dopo aver configurato il dispositivo, gli ID app e i profili di provisioning, si è pronti per eseguire il test.

1. Assicurarsi che l'iPhone sia collegato e che l'orologio sia già associato all'iPhone.

2. Verificare che la configurazione sia impostata su **rilascio** o **debug**.

3. Verificare che il dispositivo iPhone connesso sia selezionato nell'elenco destinazione.

4. Fare clic con il pulsante destro del mouse sul progetto di app iOS (non l'espressione di controllo o l'estensione) e scegliere **Imposta come progetto di avvio**.

5. Fare clic sul pulsante **Run (Esegui** ) oppure scegliere un'opzione di **avvio** dal menu **Run (Esegui** ).

6. La soluzione verrà compilata e l'app per iOS verrà distribuita nell'iPhone.
  Se l'app iOS o il provisioning dell'estensione dell'espressione di controllo non è impostato correttamente, la distribuzione in iPhone avrà esito negativo.

7. Se la distribuzione viene completata correttamente, l'iPhone tenterà automaticamente di inviare l'app Watch all'orologio abbinato. L'icona dell'app verrà visualizzata nella schermata di controllo con un indicatore di stato di *installazione* circolare.

8. Se l'app Watch viene installata correttamente, l'icona rimarrà visualizzata nella schermata di controllo per iniziare a testare l'app.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante la distribuzione, usare la **vista > i rilievi > log del dispositivo** per visualizzare altre informazioni sull'errore. Di seguito sono elencati alcuni errori e le relative cause:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Errore MT3001: Non è stato possibile AOT l'assembly

Questo problema può verificarsi durante la compilazione in modalità di DEBUG per la distribuzione in un dispositivo Apple Watch.

Per risolvere *temporaneamente* questo problema, disabilitare le **compilazioni incrementali** nelle opzioni del progetto dell'estensione Watch **> Compila > finestra di compilazione watchos** :

[![](device-images/disable-incremental-sml.png "Casella di controllo compilazioni incrementali")](device-images/disable-incremental.png#lightbox)

Questo problema verrà risolto in una versione futura, dopo le quali è possibile abilitare nuovamente le compilazioni incrementali per sfruttare i tempi di compilazione più rapidi.


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Non è possibile avviare l'app Watch durante il debug nel dispositivo

Quando si tenta di eseguire il debug di un'app Watch in un dispositivo fisico, viene visualizzata solo l'icona & il caricamento della casella di selezione e infine il timeout. Questa operazione verrà risolta in una versione futura. una soluzione alternativa consiste nell'eseguire una build di rilascio, che non consente il debug.


### <a name="invalid-application-executable-or-application-verification-failed"></a>Verifica dell'applicazione o dell'eseguibile dell'applicazione non valido

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Avviso eseguibile dell'applicazione non valido")

Se questi messaggi vengono visualizzati *nella schermata di controllo* dopo che l'app ha tentato di eseguire l'installazione, potrebbero essersi verificati alcuni problemi:

- Il dispositivo di controllo non è stato aggiunto come dispositivo in Apple Dev Center. Seguire le istruzioni per [configurare correttamente i dispositivi](#devices).

- I profili di provisioning di sviluppo usati per i test non hanno incluso il dispositivo Watch; in alternativa, dopo che l'espressione di controllo è stata aggiunta ai profili di provisioning, non è stato nuovamente scaricato e reinstallato. Seguire le istruzioni per [configurare correttamente i profili di provisioning](#profiles).

- Se il **log del dispositivo iOS** contiene `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` , il valore **MinimumOSVersion** dell'app Watch **. plist** non è corretto.
  Dovrebbe essere **8,2** : se è stato installato Xcode 6,3, potrebbe essere necessario modificare manualmente il codice sorgente per inserirlo in 8,2.

- Il titolo dell'app Watch **. plist** non dispone correttamente di un diritto abilitato, ad esempio gruppi di app, che non deve avere.

- L' **ID app** dell'app Watch non dispone correttamente di un diritto abilitato, ad esempio gruppi di app, in Dev Center.



### <a name="install-never-finished"></a>Installazione non completata

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Questo errore potrebbe indicare chiavi non necessarie (e non valide) nel file **info. plist** dell'app Watch. Non includere chiavi significative per l'app iOS o l'estensione Watch nell'app Watch.

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"in attesa della connessione del debugger"

Se la finestra di **output dell'applicazione** viene bloccata mostrando

```csharp
waiting for debugger to connect
```

controllare se i NuGet inclusi nel progetto hanno una dipendenza da **Microsoft. BCL. Build**. Questa operazione viene aggiunta automaticamente con alcune librerie pubblicate da Microsoft, incluse le [librerie client HTTP Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http/)più diffuse.

Il file **Microsoft. BCL. Build. targets** aggiunto al file con **estensione csproj** può interferire con la creazione di pacchetti di estensioni iOS durante la distribuzione. È possibile tenere traccia del [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Una possibile soluzione alternativa consiste nel modificare il file con estensione csproj e spostare manualmente **Microsoft. BCL. Build. targets** in modo che sia l'ultimo elemento.


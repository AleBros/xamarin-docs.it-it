---
title: Test su dispositivi di Apple Watch
description: Questo documento descrive come distribuire un'app watchOS compilata con Xamarin per i test su un Apple Watch effettivo. Illustra i dispositivi, i profili, test, il provisioning e vengono forniti alcuni suggerimenti sulla risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 9c15e9205b96a02caa182e47b71c6d36c8bff1aa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282950"
---
# <a name="testing-on-apple-watch-devices"></a>Test su dispositivi di Apple Watch

Dopo aver seguito le [passaggi di distribuzione](~/ios/watchos/deploy-test/index.md) per creare ID App e i gruppi di App (se richiesto), usare le istruzioni riportate in questa pagina per:

- [Configurare i dispositivi](#devices) nel centro per sviluppatori Apple, e
- [Creare profili di Provisioning di sviluppo](#profiles), quindi
- [Distribuire e testare](#testing) su un Apple Watch.

<a name="devices" />

## <a name="devices"></a>Dispositivi

Test delle App iOS in un reale iPhone o iPad ha sempre richiesto il dispositivo sia registrato in Dev Center. L'elenco dei dispositivi si presenta come segue (fare clic sul segno più **+** per aggiungere un nuovo dispositivo):

![](device-images/devices-sml.png "L'elenco dei dispositivi si presenta come segue")

Le espressioni di controllo non sono diversi: è ora necessario aggiungere il dispositivo di Apple Watch prima di distribuire le app a esso. Trovare dell'orologio UDID **Xcode** (**Windows > dispositivi** elenco). Quando è connesso il telefono associato verranno visualizzate anche le informazioni dell'orologio:

[![](device-images/xcode-devices-sml.png "Informazioni Watch associato")](device-images/xcode-devices.png#lightbox)

Quando si conosce l'orologio UDID, aggiungerlo all'elenco dei dispositivi in Dev Center:

![](device-images/devices-watch-sml.png "Le espressioni di controllo UDID nell'elenco dei dispositivi")

Dopo aver aggiunto il dispositivo di espressioni di controllo, assicurarsi che sia stata in qualsiasi sviluppo nuovo o esistente o ad hoc i profili di provisioning creato:

![](device-images/devices-provisioning.png "Elenco dei dispositivi disponibili")

Non dimenticare se si modifica un profilo di provisioning esistente per scaricarlo e installarlo nuovamente.

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>Sviluppo per i profili di Provisioning

Per compilare per il test sul tuo dispositivo è necessario creare un **profilo di Provisioning di sviluppo** per ogni ID App nella soluzione.

Se si dispone di un ID App, con caratteri jolly *sarà necessario un solo profilo di Provisioning*; ma se si ha un ID App separate per ogni progetto, è necessario un profilo di provisioning per ogni ID App:

![](device-images/provisioningprofile-development.png "Il profilo di Provisioning di sviluppo")

Dopo aver creato tutte e tre i profili, questi verranno visualizzati nell'elenco. Ricordarsi di scaricare e installare ognuno di essi:

![](device-images/provisioningprofiles.png "I profili di Provisioning sviluppo disponibili")

È possibile verificare il profilo di provisioning nel **opzioni progetto** selezionando il **compilazione > firma del Bundle iOS** dello schermo e selezionare il **versione** o **Eseguire il debug iPhone** configurazione.

Il **profilo di Provisioning** elenco verranno visualizzati tutti i profili corrispondenti, si dovrebbero vedere i profili corrispondenti che sono stati creati in questo elenco a discesa:

![](device-images/options-selectprofile.png "L'elenco dei profili di Provisioning")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>Test su un dispositivo di espressioni di controllo

Dopo aver configurato il dispositivo, gli ID App e i profili di Provisioning, si è pronti per eseguire il test.

1. Assicurarsi che sia connesso un iPhone, e le espressioni di controllo è già associato con l'iPhone.

2. Verificare la configurazione sia impostata su **Release** oppure **Debug**.

3. Verificare che il dispositivo iPhone connessi è selezionato nell'elenco di destinazione.

4. Fare clic sul progetto dell'App iOS (non le espressioni di controllo o estensione) e scegliere **imposta come progetto di avvio**.

5. Fare clic sul **eseguire** pulsante (o scegliere un **avviare** opzione il **eseguire** menu).

6. La soluzione verrà compilata e verrà distribuito l'app per iOS per iPhone.
  Se l'app per iOS o guardare il provisioning dell'estensione non è impostato correttamente, la distribuzione in iPhone avrà esito negativo.

7. Se la distribuzione viene completata correttamente, iPhone tenterà automaticamente di inviare l'app watch a Watch associato. Icona dell'app verrà visualizzati nella schermata di espressioni di controllo con un cerchio *installazione* indicatore di stato.

8. Se è installato correttamente l'app watch, l'icona rimarrà nella schermata di espressioni di controllo - toccheranno per iniziare a testare l'app.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante la distribuzione Usa la **Vista > riquadri > Log del dispositivo** per visualizzare altre informazioni sull'errore. Di seguito sono elencate alcuni errori e le relative cause:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Errore MT3001: Potrebbe non AOT di assembly

Ciò può verificarsi durante la compilazione in modalità di DEBUG per distribuire un dispositivo Apple Watch.

Per *temporaneamente* risolvere questo problema, disabilitare **compilazioni incrementali** nell'estensione delle espressioni di controllo **opzioni progetto > compilazione > compilazione watchOS** finestra:

[![](device-images/disable-incremental-sml.png "La casella di controllo compilazioni incrementali")](device-images/disable-incremental.png#lightbox)

Questo problema verrà risolto in una versione futura, dopo il quale le compilazioni incrementali possono essere riabilitate possa sfruttare i vantaggi dei tempi di compilazione.


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>App Watch non viene avviato durante il debug nel dispositivo

Quando il tentativo di eseguire il debug di un'app in un dispositivo fisico, solo l'icona & casella di selezione di caricamento vengono visualizzati (e alla fine timeout). Questo problema verrà risolto nelle versioni future. soluzione alternativa consiste nell'eseguire una build di rilascio (che non consente il debug).


### <a name="invalid-application-executable-or-application-verification-failed"></a>File eseguibile dell'applicazione non è valido o non riuscito di verifica dell'applicazione

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Avviso di file eseguibile dell'applicazione non valido")

Se questi messaggi vengono visualizzati *sullo schermo watch* dopo che l'app ha tentato di installare, potrebbe esserci un paio di problemi:

- Il dispositivo di espressioni di controllo stesso non è stato aggiunto come un dispositivo nel centro per sviluppatori Apple. Per seguire le istruzioni [configurare i dispositivi in modo corretto](#devices).

- I profili di provisioning di sviluppo in uso per il test non è incluso; il dispositivo di espressioni di controllo oppure, dopo l'espressione di controllo è stato aggiunto per i profili di provisioning non sono stati scaricati nuovamente e reinstallati. Per seguire le istruzioni [configurare i profili di provisioning corretto](#profiles).

- Se il **Log del dispositivo iOS** contiene `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` quindi l'espressione di controllo dell'App **Info. plist** non corretta **MinimumOSVersion** valore.
  Deve trattarsi **8.2** : se è stato installato Xcode 6.3 potrebbe essere necessario modificare manualmente l'origine da insert impostarla su 8.2.

- Le App Watch **entitlements. plist** erroneamente ha diritto abilitato (ad esempio i gruppi di App) non deve avere.

- Le App Watch **App ID** erroneamente ha diritto abilitato (ad esempio i gruppi di App) nel centro per sviluppatori che non deve avere.



### <a name="install-never-finished"></a>Non installare mai completata

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Questo errore potrebbe indicare le chiavi non necessari (e non è valide) dell'App Watch **Info. plist** file. Non è necessario includere le chiavi progettate per l'estensione di app o espressioni di controllo di iOS in App Watch.

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"in attesa di connessione del debugger"

Se il **Output applicazione** si blocca finestra che mostra

```csharp
waiting for debugger to connect
```

Verificare se uno qualsiasi dei pacchetti NuGet che sono stati inclusi nel progetto hanno una dipendenza **Microsoft.Bcl.Build**. Questo viene aggiunto automaticamente con alcune librerie pubblicate da Microsoft, tra cui i diffusi [Microsoft Http Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http/).

Il **Microsoft.Bcl.Build.targets** file che viene aggiunto per il **csproj** può interferire con la creazione di pacchetti di estensioni iOS durante la distribuzione. È possibile tenere traccia di [bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Una possibile soluzione alternativa consiste nel modificare il file con estensione csproj e spostare manualmente il **Microsoft.Bcl.Build.targets** per essere l'ultimo elemento.


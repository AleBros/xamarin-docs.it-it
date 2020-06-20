---
title: Ricaricamento attivo XAML perXamarin.Forms
description: Ricarica immediatamente le modifiche apportate al file XAML nell'applicazione in esecuzione, in modo da non dover compilare il Xamarin.Forms progetto dopo ogni modifica del codice XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/14/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0655739c95ba58b8d93aae6d3987d54bd0582c7b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84127452"
---
# <a name="xaml-hot-reload-for-xamarinforms"></a>Ricaricamento attivo XAML perXamarin.Forms

Il ricaricamento a caldo di XAML si collega al flusso di lavoro esistente per aumentare la produttività e risparmiare tempo. Senza il ricaricamento a caldo di XAML, è necessario compilare e distribuire l'app ogni volta che si desidera visualizzare una modifica XAML. Con il ricaricamento a caldo, quando si salva il file XAML le modifiche vengono riflesse in tempo reale nell'app in esecuzione. Inoltre, lo stato e i dati di navigazione verranno conservati, consentendo di eseguire rapidamente l'iterazione sull'interfaccia utente senza perdere il posto nell'app. Pertanto, con il ricaricamento a caldo di XAML, dedicare meno tempo alla ricompilazione e alla distribuzione delle app per convalidare le modifiche dell'interfaccia utente.

> [!NOTE]
> Se si sta scrivendo un'app WPF o UWP, vedere [ricaricamento a caldo di XAML per UWP e WPF](/visualstudio/debugger/xaml-hot-reload).
>
> Il ricaricamento a caldo di XAML per Xamarin.Forms non funziona attualmente per i _not_ Xamarin.Forms progetti UWP.

## <a name="system-requirements"></a>Requisiti di sistema

| IDE/Framework | Versione obbligatoria |
|------|------------------|
|Visual Studio 2019 | 16,4 o versione successiva
Visual Studio 2019 per Mac | 8,4 o versione successiva
Xamarin.Forms | 4,1 o versione successiva

## <a name="enable-xaml-hot-reload-for-xamarinforms"></a>Abilita ricaricamento attivo XAML perXamarin.Forms

Se si inizia da un modello, il ricaricamento frequente di XAML è attivo per impostazione predefinita e il progetto è configurato per funzionare senza alcuna installazione aggiuntiva. Eseguire il debug dell'app in un emulatore Android o iOS, un simulatore o un dispositivo fisico, modificare il codice XAML e salvare il file per attivare un ricaricamento a caldo di XAML.

Se si lavora da una soluzione esistente Xamarin.Forms , non è necessaria alcuna installazione aggiuntiva per usare il ricaricamento a caldo di XAML, ma potrebbe essere necessario controllare la configurazione per garantire la migliore esperienza. Per prima cosa, abilitarla nelle impostazioni dell'IDE:

* In Windows selezionare la casella di controllo **Abilita ricaricamento a caldo Novell** in **strumenti**  >  **Opzioni**  >  **Novell**  >  **ricarica a caldo**.
* In Mac, selezionare la casella di controllo **Abilita ricaricamento a caldo Novell** in **Visual Studio**  >  **Preferences**  >  **strumenti di preferenze di**Visual Studio per il  >  **ricaricamento a caldo di XAML**Novell.
  * Nelle versioni precedenti di Visual Studio per Mac, il menu si trova in progetti di preferenze di **Visual Studio**  >  **Preferences**  >  **Projects**  >  **Novell Hot reload**.

Quindi, nelle impostazioni di compilazione di Android e iOS, verificare che il linker sia impostato su "non collegare" o "link None". Per usare il ricaricamento a caldo di XAML con un dispositivo iOS fisico, è necessario selezionare anche **Enable the mono interprete** (visual Studio 16,4 e versioni successive) o Add **--Interpreter** per gli **argomenti mtouch aggiuntivi** (Visual Studio 16,3 e versioni precedenti).

È possibile utilizzare il diagramma di flusso seguente per controllare l'installazione del progetto esistente per l'utilizzo con il ricaricamento a caldo di XAML:

![Installazione di ricaricamento a caldo di XAML](hot-reload-images/hotreloadflowchart.png "Diagramma di flusso di installazione a caldo di XAML")

## <a name="resilient-reloading"></a>Ricaricamento resiliente

Se si effettua una modifica che non è possibile ricaricare il ricaricamento frequente di XAML, verrà visualizzato un messaggio di errore con IntelliSense. Queste modifiche, note come modifiche scortesi, includono la digitazione errata del codice XAML o il collegamento di un controllo a un gestore eventi che non esiste. Anche con una modifica non elaborata, è possibile continuare a ricaricare senza riavviare l'app. apportare un'altra modifica altrove nel file XAML e fare clic su Salva. La modifica rude non verrà ricaricata, ma le altre modifiche continueranno a essere applicate.

## <a name="reload-on-multiple-platforms-at-once"></a>Ricarica su più piattaforme contemporaneamente

Il ricaricamento a caldo di XAML supporta il debug simultaneo in Visual Studio e Visual Studio per Mac. È possibile distribuire contemporaneamente un dispositivo Android e una destinazione iOS per visualizzare le modifiche riflesse in entrambe le piattaforme. Per eseguire il debug su più piattaforme, vedere:
* **Windows** [procedura: impostare più progetti di avvio](https://docs.microsoft.com/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [impostare più progetti di avvio](https://docs.microsoft.com/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>Limitazioni note

* Altre Xamarin.Forms destinazioni, ad esempio UWP e MacOS, *non* sono ancora supportate. È possibile tenere traccia dello stato di avanzamento del supporto di UWP [qui](https://developercommunity.visualstudio.com/idea/661682/xaml-hot-reload-for-xamarinforms-on-uwp.html).
* Non è possibile aggiungere, rimuovere o rinominare i file o i pacchetti NuGet durante una sessione di ricaricamento a caldo di XAML. Se si aggiunge o rimuove un file o un pacchetto NuGet, ricompilare e ridistribuire l'app per continuare a usare il ricaricamento a caldo di XAML.
* Per un'esperienza ottimale, impostare il linker in modo che **non venga collegato** o **collegato** . L'impostazione di **link SDK only** funziona la maggior parte del tempo, ma in alcuni casi potrebbe non riuscire. Le impostazioni del linker sono disponibili nelle opzioni di compilazione per Android e iOS.
* Per eseguire il debug su un iPhone fisico è necessario che l'interprete usi il ricaricamento a caldo di XAML. A tale scopo, aprire le impostazioni del progetto, selezionare la scheda compilazione iOS e verificare che **l'opzione Abilita l'interprete mono** sia abilitata. Potrebbe essere necessario modificare l'opzione **Platform** nella parte superiore della pagina delle proprietà su **iPhone**.
* Tutti i riferimenti creati assegnando un controllo a un altro campo o a una proprietà usando il relativo `x:Name` valore non verranno ricaricati.
* L'aggiornamento della gerarchia visiva dell'applicazione shell in AppShell. XAML può causare problemi di gestione dello stato dell'applicazione. Se si verificano problemi, ricompilare l'app per continuare il ricaricamento.
* Il ricaricamento a caldo di XAML non è in grado di ricaricare il codice C#, inclusi i gestori eventi, i controlli personalizzati, il code-behind e le classi aggiuntive.

## <a name="more-resources"></a>Altre risorse

* [Suggerimenti e trucchi per il ricaricamento a caldo di XAML](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
* [Ricaricamento a caldo di XAML per Xamarin.Forms approfondimento: Novell Show](https://www.youtube.com/watch?v=crhjjPjzknk)

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Se non è possibile inizializzare il ricaricamento a caldo di XAML:
  * Aggiornare la Xamarin.Forms versione.
  * Assicurarsi di avere la versione più recente dell'IDE.
  * Impostare le impostazioni del linker per Android o iOS in modo che **non vengano collegate** nelle impostazioni di compilazione del progetto.
* Se non si verifica nulla quando si salva il file XAML, verificare che il ricaricamento a caldo di XAML sia abilitato nell'IDE.
* Se si sta eseguendo il debug in un iPhone fisico e l'app smette di rispondere, verificare che l'interprete sia abilitato. Per attivarlo, selezionare **Abilita l'interprete mono** (Visual Studio 16.4/8,4 e versioni precedenti) o Aggiungi **--interprete** al campo **argomenti mTouch aggiuntivi** (Visual Studio 16,3/8.3 e versioni precedenti) nelle impostazioni di compilazione iOS.

Per segnalare un bug, usare la **Guida**  >  **Invia commenti e suggerimenti**  >  **segnala un problema** in **Help**Windows e  >  **segnala un problema** in Mac.

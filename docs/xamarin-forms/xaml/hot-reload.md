---
title: Ricaricamento a caldo di XAML per Novell. Forms
description: Ricarica immediatamente le modifiche apportate al file XAML nell'applicazione in esecuzione, quindi non è necessario compilare il progetto Novell. Forms dopo ogni modifica del codice XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: 2a47876b7b53cf557014c772333e651146afe53f
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106008"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>Ricaricamento a caldo di XAML per Novell. Forms (anteprima)

![Funzionalità di anteprima](~/media/shared/preview.png)

Il ricaricamento a caldo di XAML si collega al flusso di lavoro esistente per aumentare la produttività e risparmiare tempo. Senza il ricaricamento a caldo di XAML, è necessario compilare e distribuire l'app ogni volta che si desidera visualizzare una modifica XAML. Con il ricaricamento a caldo, quando si salva il file XAML le modifiche vengono riflesse in tempo reale nell'app in esecuzione. Inoltre, lo stato e i dati di navigazione verranno conservati, consentendo di eseguire rapidamente l'iterazione sull'interfaccia utente senza perdere il posto nell'app. Pertanto, con il ricaricamento a caldo di XAML, dedicare meno tempo alla ricompilazione e alla distribuzione delle app per convalidare le modifiche dell'interfaccia utente.

> [!NOTE]
> Se si sta scrivendo un'app WPF o UWP, vedere [ricaricamento a caldo di XAML per UWP e WPF](/visualstudio/debugger/xaml-hot-reload).

## <a name="system-requirements"></a>Requisiti di sistema

| IDE/Framework | Versione obbligatoria |
|------|------------------|
|Visual Studio 2019 | 16,3 o versione successiva
Visual Studio 2019 per Mac | 8,3 o versione successiva
Xamarin.Forms | 4,1 o versione successiva

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>Usare il ricaricamento a caldo di XAML per Novell. Forms

Non è necessaria alcuna installazione o installazione aggiuntiva per usare il ricaricamento a caldo di XAML. È incorporata in Visual Studio e può essere abilitata nelle impostazioni dell'IDE. Una volta abilitata, è possibile iniziare a usare il ricaricamento a caldo di XAML eseguendo il debug dell'app in un emulatore, un simulatore o un dispositivo fisico. Attualmente, il ricaricamento a caldo di XAML funziona solo quando si esegue il debug in iOS o Android.

In Windows è possibile abilitare il ricaricamento a caldo di XAML selezionando la casella di controllo **Abilita ricaricamento a caldo Novell** in **strumenti** > **Opzioni** > **Novell** > **ricarica a caldo**.

In un Mac è possibile abilitare il ricaricamento a caldo di XAML selezionando la casella di controllo **Abilita ricaricamento a caldo Novell** in**progetti** > di**Preferenze** > di **Visual Studio** > **Novell ricarica a caldo**.

## <a name="resilient-reloading"></a>Ricaricamento resiliente

Se si effettua una modifica che non è possibile ricaricare il ricaricamento frequente di XAML, verrà visualizzato un messaggio di errore con IntelliSense. Queste modifiche, note come modifiche scortesi, includono la digitazione errata del codice XAML o il collegamento di un controllo a un gestore eventi che non esiste. Anche con una modifica non elaborata, è possibile continuare a ricaricare senza riavviare l'app. apportare un'altra modifica altrove nel file XAML e fare clic su Salva. La modifica rude non verrà ricaricata, ma le altre modifiche continueranno a essere applicate.

## <a name="known-limitations"></a>Limitazioni note

- Non è possibile aggiungere, rimuovere o rinominare i file o i pacchetti NuGet durante una sessione di ricaricamento a caldo di XAML. Se si aggiunge o rimuove un file o un pacchetto NuGet, ricompilare e ridistribuire l'app per continuare a usare il ricaricamento a caldo di XAML.
- Impostare il linker per **collegare None** per la migliore esperienza. L'impostazione di **link SDK only** funziona la maggior parte del tempo, ma in alcuni casi potrebbe non riuscire.
- Per eseguire il debug su un iPhone fisico è necessario che l'interprete usi il ricaricamento a caldo di XAML. Aggiungere l' **interprete** al campo **argomenti mTouch aggiuntivi** nelle impostazioni di compilazione iOS per usare il ricaricamento a caldo di XAML.
- Tutti i riferimenti creati assegnando un controllo a un altro campo o a una `x:Name` proprietà usando il relativo valore non verranno ricaricati.
- L'aggiornamento della gerarchia visiva dell'applicazione shell in **AppShell. XAML** può causare problemi di gestione dello stato dell'applicazione. Ricompilare l'app per continuare il ricaricamento.
- Il ricaricamento a caldo di C# XAML non è in grado di ricaricare il codice, inclusi gestori eventi, controlli personalizzati, code-behind della pagina e classi aggiuntive.

## <a name="migrate-from-the-private-preview"></a>Eseguire la migrazione dall'anteprima privata

Se si fa parte dell'anteprima privata, l'estensione per il ricaricamento a caldo di XAML verrà aggiornata automaticamente al momento dell'aggiornamento di Visual Studio. Se si sceglie di non aggiornare Visual Studio, è possibile continuare a usare la versione corrente del ricaricamento a caldo di XAML, ma non si riceveranno altri aggiornamenti tramite il feed dell'estensione di anteprima privata.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Se non è possibile inizializzare il ricaricamento a caldo di XAML:
  - Aggiornare la versione di Novell. Forms.
  - Assicurarsi di avere la versione più recente dell'IDE.
  - Impostare le impostazioni del linker per Android o iOS in modo che **non vengano collegate** nelle impostazioni di compilazione del progetto.
- Se non si verifica nulla quando si salva il file XAML, verificare che il ricaricamento a caldo sia abilitato nell'IDE.
- Se si sta eseguendo il debug in un iPhone fisico e l'app smette di rispondere, verificare che l'interprete sia abilitato. Per attivarlo, aggiungere l' **interprete** al campo **argomenti mTouch aggiuntivi** nelle impostazioni di compilazione iOS.

Per segnalare un bug, usare lo strumento commenti e suggerimenti **nel** > menu**Invia commenti** > e > suggerimenti**segnala un problema** in Windows e **il menu** **segnala un problema** in un computer Mac.

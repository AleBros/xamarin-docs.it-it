---
title: Riepilogo del capitolo 6. Clic sui pulsanti
description: 'Creazione di App per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 6. Clic sui pulsanti'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334715"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Riepilogo del capitolo 6. Clic sui pulsanti

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

Il [ `Button` ](xref:Xamarin.Forms.Button) è la vista che consente all'utente di avviare un comando. Oggetto `Button` è identificata dal testo (e facoltativamente un'immagine come illustrato nella [capitolo 13, bitmap](chapter13.md)). Di conseguenza `Button` definisce molti delle stesse proprietà `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` definisce anche tre proprietà che controllano l'aspetto del bordo, ma il supporto di queste proprietà e la loro indipendenza reciproca è specifica della piattaforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) di tipo `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) di tipo `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) di tipo `Double`

`Button` eredita anche tutte le proprietà del `VisualElement` e `View`, tra cui `BackgroundColor`, `HorizontalOptions`, e `VerticalOptions`.

## <a name="processing-the-click"></a>L'elaborazione di fare clic su

Il `Button` classe definisce un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento generato quando l'utente tocca il `Button`. Il `Click` gestore di è di tipo `EventHandler`. Il primo argomento è il `Button` dell'oggetto che genera l'evento; il secondo argomento è un `EventArgs` oggetto che non fornisce nessuna informazione aggiuntiva.

Il [ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) illustra semplici `Clicked` la gestione.

## <a name="sharing-button-clicks"></a>Pulsante di condivisione fa clic su

Più `Button` viste possono condividere lo stesso `Clicked` gestore, ma il gestore deve in genere determinare quale `Button` è responsabile di un determinato evento. Un approccio consiste nell'archiviare i vari `Button` oggetti come campi e verificare quale sta generando l'evento nel gestore.

Il [ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) illustra questa tecnica. Il programma viene anche illustrato come impostare il [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà di un `Button` al `false` quando si preme il `Button` non è più valido. Oggetto disabilitato `Button` non genera un `Clicked` evento.

## <a name="anonymous-event-handlers"></a>Gestori eventi anonimo

È possibile definire `Clicked` gestori eventi come funzioni lambda anonima, come i [ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) esempio viene illustrato. Tuttavia, non condivisibile gestori anonimi senza un codice di reflection complessa.

## <a name="distinguishing-views-with-ids"></a>Viste distintive con ID

Più `Button` oggetti sono inoltre possibile distinguere impostando il [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) proprietà oppure [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) proprietà a un `string`. Questa proprietà è definita da `Element` ma non viene usato all'interno di Xamarin.Forms. Si dovrà essere utilizzato esclusivamente da parte delle applicazioni.

Il [ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) esempio Usa lo stesso gestore eventi per tutti i tasti numerici 10 un tastierino numerico e consente di distinguere tra di essi con il `StyleId` proprietà:

[![Screenshot triplo della più semplice tastierino](images/ch06fg04-small.png "Calculator")](images/ch06fg04-large.png#lightbox "calcolatore")

## <a name="saving-transient-data"></a>Salvataggio dei dati temporanei

Molte applicazioni devono salvare i dati quando un programma viene terminato e ricaricare i dati quando il programma viene avviato nuovamente. Il [ `Application` ](xref:Xamarin.Forms.Application) classe definisce diversi membri che consentono di salvare e ripristinare i dati temporanei del programma:

- Il [ `Properties` ](xref:Xamarin.Forms.Application.Properties) proprietà è un dizionario con `string` chiavi e `object` elementi. Il contenuto del dizionario venga automaticamente salvato nell'archivio locale dell'applicazione prima della chiusura del programma e ricaricato quando il programma di avvio.
- Il `Application` classe definisce tre metodi virtuali protetti che il programma del standard `App` classe sostituzioni: [ `OnStart` ](xref:Xamarin.Forms.Application.OnStart), [ `OnSleep` ](xref:Xamarin.Forms.Application.OnSleep), e [ `OnResume` ](xref:Xamarin.Forms.Application.OnResume). Questi valori si riferiscono *application Lifecycle Management* gli eventi.
- Il [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) metodo salva il contenuto del dizionario.

Non è necessario chiamare `SavePropertiesAsync`. Il contenuto del dizionario viene automaticamente salvato prima della chiusura del programma e recuperato prima dell'avvio del programma. È utile durante il programma di test per salvare i dati se il programma si blocca.

Anche utile è:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), una proprietà statica che restituisce l'attuale `Application` oggetto che è quindi possibile usare per ottenere il `Properties` dizionario.

Il primo passaggio consiste nell'identificare tutte le variabili nella pagina in cui si vuole rendere persistente quando il programma termina. Se si conoscono tutte le posizioni in cui modificare tali variabili, è possibile aggiungere semplicemente per il `Properties` dizionario a quel punto. Nel costruttore della pagina, è possibile impostare le variabili dal `Properties` dizionario se la chiave esiste.

Un programma più vasto probabilmente sarà necessario gestire gli eventi del ciclo di vita dell'applicazione. La più importante è la `OnSleep` (metodo). Una chiamata a questo metodo indica che il programma ha lasciato il primo piano. Ad esempio l'utente ha premuto il **Home** pulsante sul dispositivo, visualizzare tutte le applicazioni o è in corso l'arresto del telefono. Una chiamata a `OnSleep` è il solo avviso che un programma riceve prima di essere terminata. Il programma deve cogliere questa opportunità per assicurarsi che il `Properties` dizionario è aggiornato.

Una chiamata a `OnResume` indica che il programma non è stato terminato dopo l'ultima chiamata a `OnSleep` ma viene ora eseguita nuovamente in primo piano. Il programma potrebbe usare questa opportunità per aggiornare le connessioni internet (ad esempio).

Una chiamata a `OnStart` si verifica durante l'avvio del programma. Non è necessario attendere fino a quando non chiamare questo metodo per l'accesso di `Properties` dizionario perché il contenuto è già stati ripristinati quando il `App` costruttore viene chiamato.

Il [ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) è molto simile all'esempio **SimplestKeypad** ad eccezione del fatto che il programma utilizza il `OnSleep` sottoposto a override per salvare la voce tastierino corrente, e il costruttore della pagina per ripristinare i dati.

> [!NOTE]
> Un altro approccio al salvataggio delle impostazioni del programma avviene tramite il Xamarin.Essentials [preferenze](~/essentials/preferences.md) classe.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 6 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Esempi di capitolo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Capitolo 6 F# esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Pulsante di Xamarin.Forms](~/xamarin-forms/user-interface/button.md)

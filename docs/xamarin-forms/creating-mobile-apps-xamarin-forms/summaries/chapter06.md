---
title: Riepilogo del capitolo 6. Clic sui pulsanti
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 6. Clic sui pulsanti'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334715"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Riepilogo del capitolo 6. Clic sui pulsanti

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

Il [`Button`](xref:Xamarin.Forms.Button) è la visualizzazione che consente all'utente di avviare un comando. Un `Button` viene identificato dal testo (e, facoltativamente, da un'immagine, come illustrato nel [capitolo 13, bitmap](chapter13.md)). Di conseguenza, `Button` definisce molte delle stesse proprietà di `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` definisce anche tre proprietà che regolano l'aspetto del bordo, ma il supporto di queste proprietà e la loro indipendenza reciproca è specifico della piattaforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) di tipo `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) di tipo `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) di tipo `Double`

`Button` eredita anche tutte le proprietà di `VisualElement` e `View`, inclusi `BackgroundColor`, `HorizontalOptions`e `VerticalOptions`.

## <a name="processing-the-click"></a>L'elaborazione di fare clic su

La classe `Button` definisce un evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) che viene generato quando l'utente tocca il `Button`. Il gestore `Click` è di tipo `EventHandler`. Il primo argomento è la `Button` oggetto che genera l'evento; il secondo argomento è un oggetto `EventArgs` che non fornisce informazioni aggiuntive.

L'esempio [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) illustra la gestione di `Clicked` semplici.

## <a name="sharing-button-clicks"></a>Pulsante di condivisione fa clic su

Più visualizzazioni `Button` possono condividere lo stesso gestore `Clicked`, ma in genere è necessario che il gestore determini quale `Button` è responsabile di un determinato evento. Un approccio consiste nell'archiviare i vari oggetti `Button` come campi e verificare quale sia la generazione dell'evento nel gestore.

Questa tecnica è illustrata nell'esempio [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) . Il programma illustra anche come impostare la proprietà [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) di un `Button` su `false` quando si preme il `Button` non è più valido. Un `Button` disabilitato non genera un evento di `Clicked`.

## <a name="anonymous-event-handlers"></a>Gestori eventi anonimo

È possibile definire `Clicked` gestori come funzioni lambda anonime, come illustrato nell'esempio [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) . Tuttavia, non condivisibile gestori anonimi senza un codice di reflection complessa.

## <a name="distinguishing-views-with-ids"></a>Viste distintive con ID

È anche possibile distinguere più oggetti `Button` impostando la proprietà [`StyleId`](xref:Xamarin.Forms.Element.StyleId) o [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) proprietà su un `string`. Questa proprietà è definita da `Element` ma non viene usata all'interno di Novell. Forms. Si dovrà essere utilizzato esclusivamente da parte delle applicazioni.

L'esempio [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) usa lo stesso gestore eventi per tutte le 10 chiavi numeriche su un tastierino numerico e le distingue con la proprietà `StyleId`:

[![Schermata tripla del tastierino più semplice](images/ch06fg04-small.png "Calcolatrice")](images/ch06fg04-large.png#lightbox "Calcolatrice")

## <a name="saving-transient-data"></a>Salvataggio dei dati temporanei

Molte applicazioni devono salvare i dati quando un programma viene terminato e ricaricare i dati quando il programma viene avviato nuovamente. La classe [`Application`](xref:Xamarin.Forms.Application) definisce diversi membri che consentono al programma di salvare e ripristinare i dati temporanei:

- La proprietà [`Properties`](xref:Xamarin.Forms.Application.Properties) è un dizionario con `string` chiavi e elementi di `object`. Il contenuto del dizionario venga automaticamente salvato nell'archivio locale dell'applicazione prima della chiusura del programma e ricaricato quando il programma di avvio.
- La classe `Application` definisce tre metodi virtuali protetti che vengono sostituiti dalla classe `App` standard del programma: [`OnStart`](xref:Xamarin.Forms.Application.OnStart), [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep)e [`OnResume`](xref:Xamarin.Forms.Application.OnResume). Che fanno riferimento agli eventi del ciclo di vita *dell'applicazione* .
- Il metodo [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) Salva il contenuto del dizionario.

Non è necessario chiamare `SavePropertiesAsync`. Il contenuto del dizionario viene automaticamente salvato prima della chiusura del programma e recuperato prima dell'avvio del programma. È utile durante il programma di test per salvare i dati se il programma si blocca.

Anche utile è:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), una proprietà statica che restituisce l'oggetto `Application` corrente che è possibile utilizzare per ottenere il dizionario `Properties`.

Il primo passaggio consiste nell'identificare tutte le variabili nella pagina in cui si vuole rendere persistente quando il programma termina. Se si conoscono tutte le posizioni in cui tali variabili cambiano, è possibile aggiungerle semplicemente al dizionario `Properties` in quel momento. Nel costruttore della pagina è possibile impostare le variabili dal dizionario `Properties` se la chiave esiste.

Un programma più vasto probabilmente sarà necessario gestire gli eventi del ciclo di vita dell'applicazione. Il più importante è il metodo `OnSleep`. Una chiamata a questo metodo indica che il programma ha lasciato il primo piano. È probabile che l'utente abbia premuto il pulsante **Home** sul dispositivo o abbia visualizzato tutte le applicazioni o stia arrestando il telefono. Una chiamata a `OnSleep` è l'unica notifica ricevuta da un programma prima che venga terminata. Il programma deve avere la possibilità di assicurarsi che il dizionario `Properties` sia aggiornato.

Una chiamata a `OnResume` indica che il programma non è stato terminato dopo l'ultima chiamata a `OnSleep` ma è ora in esecuzione di nuovo in primo piano. Il programma potrebbe usare questa opportunità per aggiornare le connessioni internet (ad esempio).

Una chiamata a `OnStart` si verifica durante l'avvio del programma. Non è necessario attendere il completamento della chiamata al metodo per accedere al dizionario `Properties` perché il contenuto è già stato ripristinato quando viene chiamato il costruttore di `App`.

L'esempio [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) è molto simile a **SimplestKeypad** , ad eccezione del fatto che il programma usa il `OnSleep` override per salvare la voce del tastierino corrente e il costruttore della pagina per ripristinare i dati.

> [!NOTE]
> Un altro approccio al salvataggio delle impostazioni del programma è fornito dalla classe di [Preferenze](~/essentials/preferences.md) Novell. Essentials.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 6 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Esempi del capitolo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Esempi del F# capitolo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Pulsante Novell. Forms](~/xamarin-forms/user-interface/button.md)

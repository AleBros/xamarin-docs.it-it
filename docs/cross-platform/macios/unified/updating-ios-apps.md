---
title: Aggiornamento delle App iOS esistente
description: Seguire questi passaggi per aggiornare un'app xamarin esistente per utilizzare l'API unificata.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 74534333bb0c4ae54dc6816312a5531f29a80ce5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="updating-existing-ios-apps"></a>Aggiornamento delle App iOS esistente

_Seguire questi passaggi per aggiornare un'app xamarin esistente per utilizzare l'API unificata._

L'aggiornamento di un'app esistente per utilizzare l'API unificata richiede modifiche al file di progetto e i relativi spazi dei nomi e le API usate nel codice dell'applicazione.

## <a name="the-road-to-64-bits"></a>La strada a 64 bit

Le nuove API unificata sono necessari per supportare le architetture di dispositivo a 64 bit da un'applicazione per dispositivi mobili con xamarin. IOS. A partire dal 1 ° febbraio 2015 Apple richiede che tutti i nuovi invii di applicazione a iTunes App Store supportano architetture a 64 bit.

Xamarin offre strumenti per Visual Studio per Mac e Visual Studio automatizzare il processo di migrazione dall'API classica all'API unificata o è possibile convertire manualmente i file di progetto. Mentre l'usando gli strumenti automatica è elevata consigliato, in questo articolo verrà illustrate in entrambi i metodi.

### <a name="before-you-start"></a>Prima di iniziare...

Prima di aggiornare il codice esistente per l'API unificata, si consiglia di consente di eliminare tutti *avvisi di compilazione*. Molti *avvisi* nell'API classica diventerà errori quando si esegue la migrazione a unificato. È più semplice correggerli prima di iniziare, poiché i messaggi del compilatore dall'API classica spesso forniscono suggerimenti sulle operazioni da aggiornare.

## <a name="automated-updating"></a>Automatico l'aggiornamento

Dopo che sono stati corretti gli avvisi, selezionare un progetto iOS in Visual Studio per Mac o Visual Studio e scegliere **migrazione all'API unificata xamarin** dal **progetto** menu. Ad esempio:

![](updating-ios-apps-images/beta-tool1.png "Scegliere di eseguire la migrazione di xamarin Unified API dal menu progetto")

È necessario accettare l'avviso prima che venga eseguita la migrazione automatica (ovviamente, è necessario disporre del controllo origine/i backup prima di adottare questo adventure):

![](updating-ios-apps-images/beta-tool2.png "Accettare l'avviso prima che venga eseguita la migrazione automatica")

Lo strumento consente di automatizzare fondamentalmente tutti i passaggi descritti nel **aggiornamento manualmente** sezione riportati di seguito ed è il metodo consigliato di conversione di un progetto xamarin esistente per l'API unificata.

## <a name="steps-to-update-manually"></a>Procedura per aggiornare manualmente

Nuovamente, dopo che sono stati corretti gli avvisi, seguire questi passaggi per aggiornare manualmente le app xamarin per utilizzare la nuova API unificata:

### <a name="1-update-project-type--build-target"></a>1. Tipo di progetto di aggiornamento & destinazione Build

Modificare la versione del progetto nel **csproj** file `6BC8ED88-2882-458C-8E55-DFD12B67127B` a `FEACFBD2-3405-455C-9665-78FE426C6842`. Modificare il **csproj** file in un editor di testo, sostituendo il primo elemento di `<ProjectTypeGuids>` come illustrato:

![](updating-ios-apps-images/csproj.png "Modificare il file csproj in un editor di testo, sostituendo il primo elemento nell'elemento ProjectTypeGuids, come illustrato")

Modifica il **importazione** elemento contenente `Xamarin.MonoTouch.CSharp.targets` per `Xamarin.iOS.CSharp.targets` come illustrato:

![](updating-ios-apps-images/csproj2.png "Modificare l'elemento Import contenente Xamarin.MonoTouch.CSharp.targets per Xamarin.iOS.CSharp.targets, come illustrato")

### <a name="2-update-project-references"></a>2. Aggiornare i riferimenti di progetto

Espandere il progetto applicazione iOS **riferimenti** nodo. Inizialmente verrà visualizzato un * interrotti - **monotouch** riferimento simile a questo screenshot (perché è stato appena modificato il tipo di progetto):

![](updating-ios-apps-images/references.png "Inizialmente risulterà un riferimento interrotto-monotouch simile a questo screenshot poiché il tipo di progetto modificato")

Pulsante destro del mouse sul progetto di applicazione iOS per **Modifica riferimenti**, quindi fare clic su di **monotouch** riferimento ed eliminarlo utilizzando il pulsante "X" rossa.

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Pulsante destro del mouse sul progetto di applicazione iOS per modificare i riferimenti, quindi fare clic sul riferimento monotouch eliminarlo rossa pulsante X")

Scorrere fino a questo punto alla fine dell'elenco di riferimenti e segni di graduazione di **xamarin** assembly.

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Ora scorrere fino alla fine dell'elenco di riferimenti e segni di graduazione l'assembly xamarin")

Premere **OK** per salvare le modifiche di riferimenti di progetto.

### <a name="3-remove-monotouch-from-namespaces"></a>3. Rimuovere gli spazi dei nomi di MonoTouch

Rimuovere il **MonoTouch** prefisso degli spazi dei nomi in `using` istruzioni o ovunque un classname è stato completamente qualificato (ad es. `MonoTouch.UIKit` diventa appena `UIKit`).

### <a name="4-remap-types"></a>4. Modificare il mapping di tipi

[I tipi nativi](~/cross-platform/macios/nativetypes.md) sono state introdotte cui sostituire alcuni tipi che sono stati utilizzati in precedenza, ad esempio le istanze di `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (ad esempio). L'elenco completo dei tipi è reperibile nel [tipi nativi](~/cross-platform/macios/nativetypes.md) pagina.

### <a name="5-fix-method-overrides"></a>5. Risolvere gli override di metodo

Alcuni `UIKit` metodi sono stata modificata per utilizzare la nuova firma [tipi nativi](~/cross-platform/macios/nativetypes.md) (ad esempio `nint`). Se le sottoclassi personalizzate esegue l'override di questi metodi, le firme non corrispondono più e si verificheranno errori. Correggere questi override del metodo modificando la sottoclasse per corrispondere alla firma di nuovo utilizzando i tipi nativi.

Gli esempi includono la modifica `public override int NumberOfSections (UITableView tableView)` per restituire `nint` e la modifica sia il tipo restituito e i tipi di parametro in `public override int RowsInSection (UITableView tableView, int section)` a `nint`.

## <a name="considerations"></a>Considerazioni

Le considerazioni seguenti devono tener conto durante la conversione di un progetto xamarin esistente dall'API classica alla nuova API unificata che app si basa su uno o più componenti o pacchetto NuGet.

### <a name="components"></a>Componenti

Qualsiasi componente che sono stati inclusi nell'applicazione sarà necessario aggiornare a Unified API o verrà generato un conflitto quando si tenta di compilare. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione dall'archivio di componenti di Xamarin che supporta l'API unificata ed eseguire una compilazione pulita. Qualsiasi componente che non è ancora stato convertito dall'autore, verrà visualizzato un a 32 bit solo avviso nell'archivio del componente.

### <a name="nuget-support"></a>Supporto NuGet

Mentre si hanno contribuito modifiche a NuGet per funzionare con il supporto di Unified API, non è stata una nuova versione di NuGet, pertanto ci stiamo la valutazione come ottenere NuGet di riconoscere le nuove API.

Fino a quel momento, come i componenti, è necessario passare tutti i pacchetti NuGet sono stati inclusi nel progetto per una versione che supporta le API unificata ed eseguire una compilazione pulita in un secondo momento.

> [!IMPORTANT]
> Se si dispone di un errore nel modulo _"errore 3 non può includere sia 'monotouch.dll' 'Xamarin.iOS.dll' nello stesso progetto xamarin. IOS - 'Xamarin.iOS.dll' viene fatto riferimento in modo esplicito, mentre si fa riferimento 'monotouch.dll' ' xxx, versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo la conversione dell'applicazione per le API unificata, è in genere a causa del componente o un pacchetto NuGet nel progetto che non è stato aggiornato per l'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione a 64 Bit build di App xamarin

Per un'applicazione per dispositivi mobili di xamarin che è stata convertita in Unified API, lo sviluppatore deve comunque consentono di creare l'applicazione per computer a 64 bit delle opzioni dell'applicazione. Vedere il **abilitazione Bit 64 compilazioni di App xamarin** del [considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md#enable-64) documenti per istruzioni dettagliate sull'abilitazione di a 64 bit.

## <a name="finishing-up"></a>Completamento della

Se si sceglie di utilizzare il metodo automatico o manuale per convertire l'applicazione di xamarin dalla classica per le API unificata, sono presenti più istanze che verranno ulteriormente, richiedono un intervento manuale. Consultare il nostro [suggerimenti per l'aggiornamento del codice Unified API](~/cross-platform/macios/unified/updating-tips.md) dei documenti per i problemi noti e soluzioni alternative.

## <a name="related-links"></a>Collegamenti correlati

- [Suggerimenti per l'aggiornamento del codice per l'API unificata](~/cross-platform/macios/unified/updating-tips.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [La migrazione all'API unificata (video)](http://university.xamarin.com/lightninglectures/migrating-to-the-unified-api)

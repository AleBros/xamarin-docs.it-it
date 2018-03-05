---
title: L'aggiornamento di App Mac esistente
description: Seguire questi passaggi per aggiornare un'app Xamarin.Mac esistente per utilizzare l'API unificata.
ms.topic: article
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1967dea3b7f3a870950cdc7732292e9d4e24a1a8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="updating-existing-mac-apps"></a>L'aggiornamento di App Mac esistente

_Seguire questi passaggi per aggiornare un'app Xamarin.Mac esistente per utilizzare l'API unificata._

L'aggiornamento di un'app esistente per utilizzare l'API unificata richiede modifiche al file di progetto e i relativi spazi dei nomi e le API usate nel codice dell'applicazione.

# <a name="the-road-to-64-bits"></a>La strada a 64 bit

Le nuove API unificata sono necessari per supportare le architetture di dispositivo a 64 bit da un'applicazione Xamarin.Mac. A partire dal 1 ° febbraio 2015 Apple richiede che tutti i nuovi invii di applicazione per Mac App Store supportano architetture a 64 bit.

Xamarin offre strumenti per Visual Studio per Mac e Visual Studio automatizzare il processo di migrazione dall'API classica all'API unificata o è possibile convertire manualmente i file di progetto. Mentre l'usando gli strumenti automatica è elevata consigliato, in questo articolo verrà illustrate in entrambi i metodi.

## <a name="before-you-start"></a>Prima di iniziare...

Prima di aggiornare il codice esistente per l'API unificata, si consiglia di consente di eliminare tutti *avvisi di compilazione*. Molti *avvisi* nell'API classica diventerà errori quando si esegue la migrazione a unificato. È più semplice correggerli prima di iniziare, poiché i messaggi del compilatore dall'API classica spesso forniscono suggerimenti sulle operazioni da aggiornare.


# <a name="automated-updating"></a>Automatico l'aggiornamento

Una volta gli avvisi sono stati corretti, selezionare un progetto Mac esistente in Visual Studio per Mac o Visual Studio e scegliere **migrazione all'API unificata Xamarin.Mac** dal **progetto** menu. Ad esempio:

![](updating-mac-apps-images/beta-tool1.png "Scegliere di eseguire la migrazione di Xamarin.Mac Unified API dal menu progetto")

È necessario accettare l'avviso prima che venga eseguita la migrazione automatica (ovviamente, è necessario disporre del controllo origine/i backup prima di adottare questo adventure):

![](updating-mac-apps-images/migrate01.png "Accettare l'avviso prima che venga eseguita la migrazione automatica")

Esistono due tipi di .NET Framework di destinazione supportati che possono essere selezionati quando si utilizza l'API unificata in un'applicazione Xamarin.Mac:

- **Framework Mobile Xamarin.Mac** -si tratta di stesso ottimizzato .NET framework usato da xamarin. IOS e xamarin che supporta un sottoinsieme della versione completa **Desktop** framework. Questo è il framework consigliato perché fornisce più piccoli file binari medi a causa del comportamento di collegamento superiore.
- **Xamarin.Mac .NET Framework 4.5** -questo framework è nuovamente disponibile un sottoinsieme di **Desktop** framework. Tuttavia, riduce decisamente minore della versione completa **Desktop** framework rispetto al **Mobile** framework e deve _"semplice funzionamento"_ con la maggior parte dei pacchetti NuGet o librerie di terze parti 3rd. In questo modo lo sviluppatore di utilizzare standard **Desktop** assembly mentre continua a usare un framework supportati, ma questa opzione produce più grandi bundle dell'applicazione. Questo è il framework consigliato in cui vengono utilizzati assembly .NET di terze parti 3rd che non sono compatibili con la **Xamarin.Mac Mobile Framework**. Per un elenco degli assembly supportati, consultare il nostro [assembly](~/cross-platform/internals/available-assemblies.md) documentazione.

Per informazioni dettagliate sul framework di destinazione e le implicazioni della selezione di una destinazione specifica per l'applicazione Xamarin.Mac, consultare il nostro [Framework di destinazione](~/mac/platform/target-framework.md) documentazione. 

Lo strumento consente di automatizzare fondamentalmente tutti i passaggi descritti nel **aggiornamento manualmente** sezione riportati di seguito ed è il metodo consigliato di conversione di un progetto Xamarin.Mac esistente per l'API unificata.

# <a name="steps-to-update-manually"></a>Procedura per aggiornare manualmente

Nuovamente, dopo che sono stati corretti gli avvisi, seguire questi passaggi per aggiornare manualmente le app Xamarin.Mac per utilizzare la nuova API unificata:

## <a name="1-update-project-type--build-target"></a>1. Tipo di progetto di aggiornamento & destinazione Build

Modificare la versione del progetto nel **csproj** file `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` a `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Modificare il **csproj** file in un editor di testo, sostituendo il primo elemento di `<ProjectTypeGuids>` come illustrato:

![](updating-mac-apps-images/csproj.png "Modificare il file csproj in un editor di testo, sostituendo il primo elemento nell'elemento ProjectTypeGuids, come illustrato")

Modifica il **importazione** elemento contenente `Xamarin.Mac.targets` per `Xamarin.Mac.CSharp.targets` come illustrato:

![](updating-mac-apps-images/csproj2.png "Modificare l'elemento Import contenente Xamarin.Mac.targets per Xamarin.Mac.CSharp.targets, come illustrato")

Aggiungere le seguenti righe di codice dopo il `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Esempio:

![](updating-mac-apps-images/csproj3.png "Aggiungere queste righe di codice dopo l'elemento < NomeAssembly >")


## <a name="2-update-project-references"></a>2. Aggiornare i riferimenti di progetto

Espandere il progetto di applicazione Mac **riferimenti** nodo. Inizialmente verrà visualizzato un * interrotti - **XamMac** riferimento simile a questo screenshot (perché è stato appena modificato il tipo di progetto):

![](updating-mac-apps-images/references.png "Inizialmente risulterà un riferimento interrotto-XamMac simile a questo screenshot")

Fare clic su di **icona dell'ingranaggio** accanto il **XamMac** voce e selezionare **eliminare** per rimuovere il riferimento interrotto.

Successivamente, fare clic su di **riferimenti** cartella il **Esplora** e selezionare **Modifica riferimenti**. Scorrere fino alla fine dell'elenco di riferimenti e inserire un segno di spunta oltre **Xamarin.Mac**.

![](updating-mac-apps-images/references2.png "Scorrere fino alla fine dell'elenco di riferimenti e inserire un segno di spunta oltre Xamarin.Mac")

Premere **OK** per salvare le modifiche di riferimenti di progetto.

## <a name="3-remove-monomac-from-namespaces"></a>3. Rimuovere gli spazi dei nomi di MonoMac

Rimuovere il **MonoMac** prefisso degli spazi dei nomi in `using` istruzioni o ovunque un classname è stato completamente qualificato (ad es. `MonoMac.AppKit` diventa appena `AppKit`).

## <a name="4-remap-types"></a>4. Modificare il mapping di tipi

[I tipi nativi](~/cross-platform/macios/nativetypes.md) sono state introdotte cui sostituire alcuni tipi che sono stati utilizzati in precedenza, ad esempio le istanze di `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (ad esempio). L'elenco completo dei tipi è reperibile nel [tipi nativi](~/cross-platform/macios/nativetypes.md) pagina.

## <a name="5-fix-method-overrides"></a>5. Risolvere gli override di metodo

Alcuni `AppKit` metodi sono stata modificata per utilizzare la nuova firma [tipi nativi](~/cross-platform/macios/nativetypes.md) (ad esempio `nint`). Se le sottoclassi personalizzate esegue l'override di questi metodi, le firme non corrispondono più e si verificheranno errori. Correggere questi override del metodo modificando la sottoclasse per corrispondere alla firma di nuovo utilizzando i tipi nativi. 

# <a name="considerations"></a>Considerazioni

Le considerazioni seguenti devono tener conto durante la conversione di un progetto Xamarin.Mac esistente dall'API classica alla nuova API unificata che app si basa su uno o più componenti o pacchetto NuGet. 

## <a name="components"></a>Componenti

Qualsiasi componente che sono stati inclusi nell'applicazione sarà necessario aggiornare a Unified API o verrà generato un conflitto quando si tenta di compilare. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione dall'archivio di componenti di Xamarin che supporta l'API unificata ed eseguire una compilazione pulita. Qualsiasi componente che non è ancora stato convertito dall'autore, verrà visualizzato un a 32 bit solo avviso nell'archivio del componente.


## <a name="nuget-support"></a>Supporto NuGet

Mentre si hanno contribuito modifiche a NuGet per funzionare con il supporto di Unified API, non è stata una nuova versione di NuGet, pertanto ci stiamo la valutazione come ottenere NuGet di riconoscere le nuove API. 

Fino a quel momento, come i componenti, è necessario passare tutti i pacchetti NuGet sono stati inclusi nel progetto per una versione che supporta le API unificata ed eseguire una compilazione pulita in un secondo momento.

> [!IMPORTANT]
> **Nota:** se si dispone di un errore nel modulo _"errore 3 non può includere nello stesso progetto Xamarin.Mac 'monomac.dll' sia 'Xamarin.Mac.dll' - 'Xamarin.Mac.dll' viene fatto riferimento in modo esplicito, mentre 'monomac.dll' fa riferimento ' xxx, Versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo la conversione dell'applicazione per le API unificata, è in genere a causa del componente o un pacchetto NuGet nel progetto che non è stato aggiornato per l'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.




# <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Abilitazione a 64 Bit build di App Xamarin.Mac

Per un'applicazione per dispositivi mobili Xamarin.Mac che è stata convertita in Unified API, lo sviluppatore deve comunque consentono di creare l'applicazione per computer a 64 bit delle opzioni dell'applicazione. Vedere il **abilitazione Bit 64 compilazioni di App Xamarin.Mac** del [considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64.md) documenti per istruzioni dettagliate sull'abilitazione di a 64 bit.
    
# <a name="finishing-up"></a>Completamento della

Se si sceglie di utilizzare il metodo automatico o manuale per convertire l'applicazione Xamarin.Mac dalla classica per le API unificata, sono presenti più istanze che verranno ulteriormente, richiedono un intervento manuale. Consultare il nostro [suggerimenti per l'aggiornamento del codice Unified API](~/cross-platform/macios/unified/updating-tips.md) dei documenti per i problemi noti e soluzioni alternative.


## <a name="related-links"></a>Collegamenti correlati

- [Suggerimenti per l'aggiornamento del codice per l'API unificata](~/cross-platform/macios/unified/updating-tips.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)

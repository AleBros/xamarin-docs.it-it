---
title: L'aggiornamento di App Mac esistenti
description: Questo documento vengono descritti i passaggi da seguire per aggiornare un'app xamarin. Mac dall'API classica all'API unificata.
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 5e6034b079bba5e884872e4f2096d677fd3641d0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213524"
---
# <a name="updating-existing-mac-apps"></a>L'aggiornamento di App Mac esistenti

L'aggiornamento di un'app esistente per usare l'API unificata richiede modifiche al progetto file stesso anche i relativi spazi dei nomi e le API usate nel codice dell'applicazione.

## <a name="the-road-to-64-bits"></a>Il passaggio a 64 bit

Le nuove API unificata sono necessari per supportare le architetture di dispositivo a 64 bit da un'applicazione xamarin. Mac. A partire dal 1 ° febbraio 2015 Apple richiede che tutti i nuovi invii di applicazione a Store di App Mac supportano le architetture a 64 bit.

Xamarin offre strumenti per Visual Studio per Mac e Visual Studio automatizzare il processo di migrazione dall'API classica all'API unificata di oppure è possibile convertire manualmente i file di progetto. Mentre l'usando gli strumenti automatici è altamente consigliato, questo articolo verranno illustrati entrambi i metodi.

### <a name="before-you-start"></a>Prima di iniziare...

Prima di aggiornare il codice esistente per l'API unificata, si consiglia vivamente consente di eliminare tutti i *avvisi di compilazione*. Molte *avvisi* nell'API classica diventerà errori quando si esegue la migrazione a unificato. Risolvere i problemi prima di iniziare è più semplici perché i messaggi del compilatore dall'API classica spesso forniscono suggerimenti sulle operazioni da aggiornare.

## <a name="automated-updating"></a>Aggiornamenti automatici

Dopo che sono stati corretti gli avvisi, selezionare un progetto esistente Mac in Visual Studio per Mac o Visual Studio e scegli **eseguire la migrazione all'API unificata di xamarin. Mac** dalle **progetto** menu. Ad esempio:

![](updating-mac-apps-images/beta-tool1.png "Scegliere di eseguire la migrazione all'API unificata di xamarin. Mac dal menu progetto")

È necessario accettare di questo avviso prima che venga eseguita la migrazione automatica (ovviamente è necessario assicurarsi di avere controllo del codice sorgente/backup prima di avventurarsi in questa avventura):

![](updating-mac-apps-images/migrate01.png "Accettare l'avviso prima che venga eseguita la migrazione automatica")

Esistono due tipi di Framework di destinazione supportati che possono essere selezionati quando si usa l'API unificata in un'applicazione xamarin. Mac:

- **Framework per dispositivi mobili xamarin. Mac** -questo è lo stesso ottimizzato .NET framework usato da xamarin. IOS e xamarin. Android che supportano un subset della versione completa **Desktop** framework. Questo è il framework consigliato perché offre più piccoli file binari a causa di un comportamento del collegamento superiore.
- **Xamarin. Mac .NET Framework 4.5** -questo framework anche in questo caso è un subset del **Desktop** framework. Tuttavia, riduce molto meno della versione completa **Desktop** framework rispetto al **per dispositivi mobili** framework ed è consigliabile _"funzionano"_ con la maggior parte dei pacchetti NuGet o librerie di terze parti 3rd. Questo consente allo sviluppatore di utilizzare standard **Desktop** assembly mentre Usa ancora un framework supportato, ma questa opzione produce bundle dell'applicazione più grande. Questo è il framework consigliato in cui vengono utilizzati assembly .NET di terze parti 3rd che non sono compatibili con la **Framework di dispositivi mobili xamarin. Mac**. Per un elenco di assembly supportati, vedere la [assembly](~/cross-platform/internals/available-assemblies.md) documentazione.

Per informazioni dettagliate sul framework di destinazione e le implicazioni della selezione di una destinazione specifica per l'applicazione xamarin. Mac, vedere la [Framework di destinazione](~/mac/platform/target-framework.md) documentazione. 

Lo strumento automatizza fondamentalmente tutti i passaggi descritti nel **Update manualmente** sezione riportate di seguito e rappresenta il metodo consigliato di conversione di un progetto xamarin. Mac esistente per l'API unificata.

## <a name="steps-to-update-manually"></a>Procedura per aggiornare manualmente

Anche in questo caso, dopo che sono stati corretti gli avvisi, seguire questi passaggi per aggiornare manualmente le app xamarin. Mac per usare la nuova API unificata:

### <a name="1-update-project-type--build-target"></a>1. Tipo di progetto di aggiornamento & destinazione della compilazione

Modificare la versione del progetto nel **csproj** dei file dalla `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` a `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Modificare il **csproj** file in un editor di testo, sostituendo il primo elemento nel `<ProjectTypeGuids>` elemento, come illustrato:

![](updating-mac-apps-images/csproj.png "Modificare il file csproj in un editor di testo, sostituendo il primo elemento nell'elemento ProjectTypeGuids come mostrato")

Modifica il **importazione** elemento contenente `Xamarin.Mac.targets` a `Xamarin.Mac.CSharp.targets` come illustrato:

![](updating-mac-apps-images/csproj2.png "Modificare l'elemento Import contenente Xamarin.Mac.targets a Xamarin.Mac.CSharp.targets come mostrato")

Aggiungere le seguenti righe di codice dopo la `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Esempio:

![](updating-mac-apps-images/csproj3.png "Aggiungere queste righe di codice dopo l'elemento < NomeAssembly >")

### <a name="2-update-project-references"></a>2. Aggiornare i riferimenti al progetto

Espandere il progetto dell'applicazione Mac **riferimenti** nodo. Inizialmente mostrerà un * interrotti - **XamMac** riferimento simile al seguente (perché è stato appena modificato il tipo di progetto):

![](updating-mac-apps-images/references.png "Inizialmente mostrerà un riferimento interrotto-XamMac simile a questo screenshot")

Fare clic sui **icona dell'ingranaggio** accanto al **XamMac** voce e scegliere **Elimina** per rimuovere il riferimento interrotto.

Successivamente, fare clic sui **riferimenti** cartella la **Esplora soluzioni** e selezionare **Modifica riferimenti**. Scorrere fino alla fine dell'elenco di riferimenti e inserire un segno di spunta oltre **xamarin. Mac**.

![](updating-mac-apps-images/references2.png "Scorrere fino alla fine dell'elenco di riferimenti e inserire un segno di spunta oltre a xamarin. Mac")

Premere **OK** per salvare il progetto le modifiche di riferimenti.

### <a name="3-remove-monomac-from-namespaces"></a>3. Rimuovere MonoMac dagli spazi dei nomi

Rimuovere il **MonoMac** prefisso dagli spazi dei nomi in `using` istruzioni o ovunque un classname è stato completamente qualificato (ad es. `MonoMac.AppKit` diventa semplicemente `AppKit`).

### <a name="4-remap-types"></a>4. Modificare il mapping di tipi

[I tipi nativi](~/cross-platform/macios/nativetypes.md) sono state introdotte che sostituiscono alcuni tipi che sono stati usati in precedenza, ad esempio le istanze del `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (ad esempio). L'elenco completo dei tipi sono reperibili nel [i tipi nativi](~/cross-platform/macios/nativetypes.md) pagina.

### <a name="5-fix-method-overrides"></a>5. Risolvere gli override dei metodi

Alcuni `AppKit` metodi sono stata modificata per usare la nuova firma [tipi nativi](~/cross-platform/macios/nativetypes.md) (ad esempio `nint`). Se le sottoclassi personalizzate esegue l'override di questi metodi, le firme non corrisponderanno più e si verificheranno errori. Correggere questi override dei metodi modificando la sottoclasse per corrispondere alla firma di nuovo utilizzando i tipi nativi. 

## <a name="considerations"></a>Considerazioni

Le considerazioni seguenti devono tener conto durante la conversione di un progetto xamarin. Mac esistente dall'API classica alla nuova API unificata che l'app si basa su uno o più componenti o un pacchetto NuGet. 

### <a name="components"></a>Componenti

Qualsiasi componente che sono stati inclusi nell'applicazione dovrà anche essere aggiornata all'API unificata di caso, verrà generato un conflitto quando si prova a compilare. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione da Store il componente Xamarin che supporta l'API unificata ed eseguire una compilazione pulita. Qualsiasi componente che non è ancora stato convertito dall'autore, verrà visualizzato un a 32 bit solo avviso nell'archivio del componente.

### <a name="nuget-support"></a>Supporto NuGet

Anche se si ha contribuito con le modifiche apportate a NuGet per lavorare con il supporto per l'API unificata, non è stata una nuova versione di NuGet, in modo che si sta la valutazione di come ottenere NuGet per riconoscere le nuove API. 

Fino a quel momento, proprio come i componenti, è necessario passare i pacchetti NuGet sono stati inclusi nel progetto per una versione che supporta le API unificata ed eseguire una compilazione pulita in un secondo momento.

> [!IMPORTANT]
> Se si dispone di un errore nel modulo _"errore 3 non può includere sia 'monomac.dll' e 'Xamarin' nello stesso progetto xamarin. Mac: 'Xamarin' viene fatto riferimento in modo esplicito, mentre 'monomac.dll' fa riferimento ' xxx, versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo avere convertito l'applicazione per le API unificata, si tratta in genere con un componente o pacchetto NuGet nel progetto che non è stato aggiornato all'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Abilitazione a 64 Bit le compilazioni di App xamarin. Mac

Per un'applicazione per dispositivi mobili xamarin. Mac che è stata convertita per l'API unificata, lo sviluppatore deve comunque abilitare la compilazione dell'applicazione per i computer a 64 bit delle opzioni dell'app. Vedere la **abilitazione Bit 64 compilazioni di App xamarin. Mac** delle [considerazioni sulle piattaforme a 32 o 64 bit](~/cross-platform/macios/32-and-64/index.md) compilazioni di documento per istruzioni dettagliate sull'abilitazione di a 64 bit.
    
## <a name="finishing-up"></a>Completamento dell'operazione

Se si sceglie di usare il metodo manuale o automatico per convertire l'applicazione xamarin. Mac dalla distribuzione classica per le API unificata, sono presenti più istanze che richiederanno inoltre, un intervento manuale. Vedere la [suggerimenti per l'aggiornamento di codice all'API unificata di](~/cross-platform/macios/unified/updating-tips.md) documenti per i problemi noti e alternative.

## <a name="related-links"></a>Collegamenti correlati

- [Suggerimenti per l'aggiornamento del codice per l'API unificata](~/cross-platform/macios/unified/updating-tips.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)

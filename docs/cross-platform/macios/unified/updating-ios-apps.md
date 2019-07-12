---
title: Aggiornamento delle App iOS esistenti
description: Questo documento vengono descritti i passaggi da seguire per aggiornare un'app xamarin. IOS dall'API classica all'API unificata.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4e471aaca2a7a5f247b21dd1c1863a01b062a716
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855745"
---
# <a name="updating-existing-ios-apps"></a>Aggiornamento delle App iOS esistenti

_Seguire questi passaggi per aggiornare un'app xamarin. IOS esistente per usare l'API unificata._

L'aggiornamento di un'app esistente per usare l'API unificata richiede modifiche al progetto file stesso anche i relativi spazi dei nomi e le API usate nel codice dell'applicazione.

## <a name="the-road-to-64-bits"></a>Il passaggio a 64 bit

Le nuove API unificata sono necessari per supportare le architetture di dispositivo a 64 bit da un'applicazione per dispositivi mobili xamarin. IOS. A partire dal 1 ° febbraio 2015 Apple richiede che tutti i nuovi invii di applicazione a Store l'App iTunes supportano architetture a 64 bit.

Xamarin offre strumenti per Visual Studio per Mac e Visual Studio automatizzare il processo di migrazione dall'API classica all'API unificata di oppure è possibile convertire manualmente i file di progetto. Mentre l'usando gli strumenti automatici è altamente consigliato, questo articolo verranno illustrati entrambi i metodi.

### <a name="before-you-start"></a>Prima di iniziare...

Prima di aggiornare il codice esistente per l'API unificata, si consiglia vivamente consente di eliminare tutti i *avvisi di compilazione*. Molte *avvisi* nell'API classica diventerà errori quando si esegue la migrazione a unificato. Risolvere i problemi prima di iniziare è più semplici perché i messaggi del compilatore dall'API classica spesso forniscono suggerimenti sulle operazioni da aggiornare.

## <a name="automated-updating"></a>Aggiornamenti automatici

Dopo che sono stati corretti gli avvisi, selezionare un progetto iOS esistente in Visual Studio per Mac o Visual Studio e scegli **eseguire la migrazione all'API unificata di xamarin. IOS** dalle **progetto** menu. Ad esempio:

![](updating-ios-apps-images/beta-tool1.png "Scegliere di eseguire la migrazione all'API unificata di xamarin. IOS dal menu progetto")

È necessario accettare di questo avviso prima che venga eseguita la migrazione automatica (ovviamente è necessario assicurarsi di avere controllo del codice sorgente/backup prima di avventurarsi in questa avventura):

![](updating-ios-apps-images/beta-tool2.png "Accettare l'avviso prima che venga eseguita la migrazione automatica")

Lo strumento automatizza fondamentalmente tutti i passaggi descritti nel **Update manualmente** sezione riportate di seguito e rappresenta il metodo consigliato di conversione di un progetto xamarin. IOS esistente per l'API unificata.

## <a name="steps-to-update-manually"></a>Procedura per aggiornare manualmente

Anche in questo caso, dopo che sono stati corretti gli avvisi, seguire questi passaggi per aggiornare manualmente le app xamarin. IOS per usare la nuova API unificata:

### <a name="1-update-project-type--build-target"></a>1. Tipo di progetto di aggiornamento & destinazione della compilazione

Modificare la versione del progetto nel **csproj** dei file dalla `6BC8ED88-2882-458C-8E55-DFD12B67127B` a `FEACFBD2-3405-455C-9665-78FE426C6842`. Modificare il **csproj** file in un editor di testo, sostituendo il primo elemento nel `<ProjectTypeGuids>` elemento, come illustrato:

![](updating-ios-apps-images/csproj.png "Modificare il file csproj in un editor di testo, sostituendo il primo elemento nell'elemento ProjectTypeGuids come mostrato")

Modifica il **importazione** elemento contenente `Xamarin.MonoTouch.CSharp.targets` a `Xamarin.iOS.CSharp.targets` come illustrato:

![](updating-ios-apps-images/csproj2.png "Modificare l'elemento Import contenente Xamarin.MonoTouch.CSharp.targets a Xamarin.iOS.CSharp.targets come mostrato")

### <a name="2-update-project-references"></a>2. Aggiornare i riferimenti al progetto

Espandere il progetto di applicazione iOS **riferimenti** nodo. Inizialmente mostrerà un * interrotti - **monotouch** riferimento simile al seguente (perché è stato appena modificato il tipo di progetto):

![](updating-ios-apps-images/references.png "Inizialmente mostrerà un riferimento interrotto-monotouch simile al seguente perché il tipo di progetto modificato")

Pulsante destro del mouse sul progetto dell'applicazione iOS per **Modifica riferimenti**, quindi fare clic sui **monotouch** fanno riferimento a ed eliminarla usando il pulsante "X" rossa.

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Pulsante destro del mouse sul progetto dell'applicazione iOS per modificare i riferimenti, quindi fare clic sul riferimento monotouch ed eliminarlo di colore rosso pulsante X")

Ora scorrere fino alla fine dell'elenco di riferimenti e segni di graduazione il **xamarin. IOS** assembly.

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Ora scorrere fino alla fine dell'elenco di riferimenti e segni di graduazione all'assembly xamarin. IOS")

Premere **OK** per salvare il progetto le modifiche di riferimenti.

### <a name="3-remove-monotouch-from-namespaces"></a>3. Rimuovere MonoTouch dagli spazi dei nomi

Rimuovere il **MonoTouch** prefisso dagli spazi dei nomi in `using` istruzioni o ovunque un classname è stato completamente qualificato (ad es. `MonoTouch.UIKit` diventa semplicemente `UIKit`).

### <a name="4-remap-types"></a>4. Modificare il mapping di tipi

[I tipi nativi](~/cross-platform/macios/nativetypes.md) sono state introdotte che sostituiscono alcuni tipi che sono stati usati in precedenza, ad esempio le istanze del `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (ad esempio). L'elenco completo dei tipi sono reperibili nel [i tipi nativi](~/cross-platform/macios/nativetypes.md) pagina.

### <a name="5-fix-method-overrides"></a>5. Risolvere gli override dei metodi

Alcuni `UIKit` metodi sono stata modificata per usare la nuova firma [tipi nativi](~/cross-platform/macios/nativetypes.md) (ad esempio `nint`). Se le sottoclassi personalizzate esegue l'override di questi metodi, le firme non corrisponderanno più e si verificheranno errori. Correggere questi override dei metodi modificando la sottoclasse per corrispondere alla firma di nuovo utilizzando i tipi nativi.

Esempi includono la modifica `public override int NumberOfSections (UITableView tableView)` per restituire `nint` e la modifica sia il tipo restituito e i tipi di parametro nelle `public override int RowsInSection (UITableView tableView, int section)` a `nint`.

## <a name="considerations"></a>Considerazioni

Le considerazioni seguenti devono tener conto durante la conversione di un progetto xamarin. IOS esistente dall'API classica alla nuova API unificata che l'app si basa su uno o più componenti o un pacchetto NuGet.

### <a name="components"></a>Componenti

Qualsiasi componente che sono stati inclusi nell'applicazione dovrà anche essere aggiornata all'API unificata di caso, verrà generato un conflitto quando si prova a compilare. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione da Store il componente Xamarin che supporta l'API unificata ed eseguire una compilazione pulita. Qualsiasi componente che non è ancora stato convertito dall'autore, verrà visualizzato un a 32 bit solo avviso nell'archivio del componente.

### <a name="nuget-support"></a>Supporto NuGet

Anche se si ha contribuito con le modifiche apportate a NuGet per lavorare con il supporto per l'API unificata, non è stata una nuova versione di NuGet, in modo che si sta la valutazione di come ottenere NuGet per riconoscere le nuove API.

Fino a quel momento, proprio come i componenti, è necessario passare i pacchetti NuGet sono stati inclusi nel progetto per una versione che supporta le API unificata ed eseguire una compilazione pulita in un secondo momento.

> [!IMPORTANT]
> Se si dispone di un errore nel modulo _"errore 3 non può includere sia 'monotouch' e 'Dll' nello stesso progetto xamarin. ios: 'Dll' viene fatto riferimento in modo esplicito, mentre 'monotouch' fa riferimento ' xxx ', versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo avere convertito l'applicazione per le API unificata, si tratta in genere con un componente o pacchetto NuGet nel progetto che non è stato aggiornato all'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione a 64 Bit le compilazioni di App xamarin. IOS

Per un'applicazione per dispositivi mobili xamarin. IOS che è stata convertita per l'API unificata, lo sviluppatore deve comunque abilitare la compilazione dell'applicazione per i computer a 64 bit delle opzioni dell'app. Vedere la **abilitazione Bit 64 build dell'App xamarin. IOS** delle [considerazioni sulle piattaforme a 32 o 64 bit](~/cross-platform/macios/32-and-64/index.md#enable-64) compilazioni di documento per istruzioni dettagliate sull'abilitazione di a 64 bit.

## <a name="finishing-up"></a>Completamento dell'operazione

Se si sceglie di usare il metodo manuale o automatico per convertire l'applicazione xamarin. IOS dalla distribuzione classica per le API unificata, sono presenti più istanze che richiederanno inoltre, un intervento manuale. Vedere la [suggerimenti per l'aggiornamento di codice all'API unificata di](~/cross-platform/macios/unified/updating-tips.md) documenti per i problemi noti e alternative.

## <a name="related-links"></a>Collegamenti correlati

- [Suggerimenti per l'aggiornamento del codice per l'API unificata](~/cross-platform/macios/unified/updating-tips.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)

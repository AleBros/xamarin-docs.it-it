---
title: Aggiornamento di app iOS esistenti
description: Questo documento descrive i passaggi da seguire per aggiornare un'app Novell. iOS dal API classica al API unificata.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 9b531bd095781c80c5f3418725d57f8f6bbb06fd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015034"
---
# <a name="updating-existing-ios-apps"></a>Aggiornamento di app iOS esistenti

_Seguire questa procedura per aggiornare un'app Novell. iOS esistente per l'uso del API unificata._

L'aggiornamento di un'app esistente per l'uso del API unificata richiede modifiche al file di progetto stesso nonché agli spazi dei nomi e alle API usati nel codice dell'applicazione.

## <a name="the-road-to-64-bits"></a>La strada a 64 bit

Le nuove API unificate sono necessarie per supportare le architetture di dispositivi a 64 bit da un'applicazione per dispositivi mobili Novell. iOS. A partire dal 1 ° febbraio 2015 Apple richiede che tutti i nuovi invii di app ad iTunes App Store supportino le architetture a 64 bit.

Novell fornisce strumenti per Visual Studio per Mac e Visual Studio per automatizzare il processo di migrazione dalla API classica al API unificata oppure è possibile convertire i file di progetto manualmente. Mentre l'utilizzo degli strumenti automatici è molto suggerito, in questo articolo vengono illustrati entrambi i metodi.

### <a name="before-you-start"></a>Prima di iniziare...

Prima di aggiornare il codice esistente alla API unificata, è consigliabile eliminare tutti gli *avvisi di compilazione*. Molti *avvisi* nell'API classica diventeranno errori dopo la migrazione a Unified. La correzione prima di iniziare è più semplice perché i messaggi del compilatore provenienti dal API classica spesso forniscono suggerimenti sugli elementi da aggiornare.

## <a name="automated-updating"></a>Aggiornamento automatico

Una volta corretti gli avvisi, selezionare un progetto iOS esistente in Visual Studio per Mac o Visual Studio e scegliere **Esegui migrazione a Novell. iOS API unificata** dal menu **progetto** . Esempio:

![](updating-ios-apps-images/beta-tool1.png "Choose Migrate to Xamarin.iOS Unified API from the Project menu")

È necessario accettare questo avviso prima dell'esecuzione della migrazione automatica (ovviamente è necessario assicurarsi di disporre di backup/controllo del codice sorgente prima di intraprendere questa avventura):

![](updating-ios-apps-images/beta-tool2.png "Agree to this warning before the automated migration will run")

Lo strumento consente di automatizzare tutti i passaggi descritti nella sezione **aggiornamento manuale** riportata di seguito ed è il metodo consigliato per convertire un progetto Novell. iOS esistente nel API unificata.

## <a name="steps-to-update-manually"></a>Passaggi per l'aggiornamento manuale

Anche in questo caso, dopo aver corretto gli avvisi, seguire questa procedura per aggiornare manualmente le app Novell. iOS per usare la nuova API unificata:

### <a name="1-update-project-type--build-target"></a>1. aggiornare il tipo di progetto & destinazione di compilazione

Modificare la versione del progetto nei file **csproj** da `6BC8ED88-2882-458C-8E55-DFD12B67127B` a `FEACFBD2-3405-455C-9665-78FE426C6842`. Modificare il file **csproj** in un editor di testo, sostituendo il primo elemento nell'elemento `<ProjectTypeGuids>` come illustrato:

![](updating-ios-apps-images/csproj.png "Edit the csproj file in a text editor, replacing the first item in the ProjectTypeGuids element as shown")

Modificare l'elemento **Import** che contiene `Xamarin.MonoTouch.CSharp.targets` in `Xamarin.iOS.CSharp.targets`, come illustrato:

![](updating-ios-apps-images/csproj2.png "Change the Import element that contains Xamarin.MonoTouch.CSharp.targets to Xamarin.iOS.CSharp.targets as shown")

### <a name="2-update-project-references"></a>2. aggiornare i riferimenti al progetto

Espandere il nodo **riferimenti** del progetto di applicazione iOS. Inizialmente verrà visualizzato un riferimento di tipo * Broken- **MonoTouch** simile a questo screenshot (perché è stato appena modificato il tipo di progetto):

![](updating-ios-apps-images/references.png "It will initially show a broken- monotouch reference similar to this screenshot because the project type changed")

Fare clic con il pulsante destro del mouse sul progetto di applicazione iOS per **modificare i riferimenti**, quindi fare clic sul riferimento **MonoTouch** ed eliminarlo usando il pulsante rosso "X".

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Right-click on the iOS application project to Edit References, then click on the monotouch reference and delete it using the red X button")

Scorrere ora fino alla fine dell'elenco di riferimenti e selezionare l'assembly **Novell. iOS** .

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Now scroll to the end of the references list and tick the Xamarin.iOS assembly")

Fare clic su **OK** per salvare le modifiche dei riferimenti al progetto.

### <a name="3-remove-monotouch-from-namespaces"></a>3. rimuovere MonoTouch da spazi dei nomi

Rimuovere il prefisso **MonoTouch** dagli spazi dei nomi nelle istruzioni `using` o in qualsiasi punto in cui un nomeclasse è stato completo (ad esempio, `MonoTouch.UIKit` diventa solo `UIKit`).

### <a name="4-remap-types"></a>4. modifica del mapping di tipi

Sono stati introdotti [tipi nativi](~/cross-platform/macios/nativetypes.md) che sostituiscono alcuni tipi utilizzati in precedenza, ad esempio istanze di `System.Drawing.RectangleF` con `CoreGraphics.CGRect` (ad esempio). L'elenco completo dei tipi è reperibile nella pagina [tipi nativi](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. correggere le sostituzioni del metodo

Per alcuni `UIKit` metodi la firma è stata modificata in modo da usare i nuovi [tipi nativi](~/cross-platform/macios/nativetypes.md) , ad esempio `nint`. Se sottoclassi personalizzate eseguono l'override di questi metodi, le firme non saranno più corrispondenti e comporteranno errori. Correggere questi override del metodo modificando la sottoclasse in modo che corrisponda alla nuova firma utilizzando i tipi nativi.

Gli esempi includono la modifica `public override int NumberOfSections (UITableView tableView)` per restituire `nint` e la modifica del tipo restituito e i tipi di parametro in `public override int RowsInSection (UITableView tableView, int section)` `nint`.

## <a name="considerations"></a>Considerazioni

Quando si converte un progetto Novell. iOS esistente dal API classica al nuovo API unificata se tale app si basa su uno o più pacchetti di componenti o NuGet, tenere presente le considerazioni seguenti.

### <a name="components"></a>Componenti

Tutti i componenti inclusi nell'applicazione dovranno anche essere aggiornati al API unificata o si verifica un conflitto quando si tenta di eseguire la compilazione. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione dall'archivio componenti di Novell che supporta il API unificata ed eseguire una compilazione pulita. Per tutti i componenti che non sono stati ancora convertiti dall'autore, verrà visualizzato un avviso solo 32 bit nell'archivio componenti.

### <a name="nuget-support"></a>Supporto NuGet

Anche se sono state apportate modifiche a NuGet per lavorare con il supporto API unificata, non è stata rilasciata una nuova versione di NuGet, quindi si sta valutando come ottenere NuGet per riconoscere le nuove API.

Fino a quel momento, come per i componenti, è necessario cambiare il pacchetto NuGet incluso nel progetto in una versione che supporta le API unificate ed eseguire una compilazione pulita in seguito.

> [!IMPORTANT]
> Se è presente un errore nel formato _"errore 3: non è possibile includere sia ' MonoTouch. dll ' che ' Novell. iOS. dll ' nello stesso progetto Novell. iOS. viene fatto riferimento in modo esplicito a' Novell. iOS. dll ', mentre a' MonoTouch. dll ' fa riferimento ' xxx, Version = 0.0.000, culture = neutral, PublicKeyToken = null ' "_ dopo la conversione dell'applicazione nelle API unificate, è in genere dovuto alla presenza di un componente o di un pacchetto NuGet nel progetto che non è stato aggiornato al API unificata. È necessario rimuovere il componente/NuGet esistente, eseguire l'aggiornamento a una versione che supporta le API unificate ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione delle build a 64 bit delle app Novell. iOS

Per un'applicazione per dispositivi mobili Novell. iOS che è stata convertita nel API unificata, lo sviluppatore deve comunque abilitare la compilazione dell'applicazione per i computer a 64 bit dalle opzioni dell'app. Per istruzioni dettagliate sull'abilitazione di compilazioni a 64 bit, vedere la pagina relativa all' **Abilitazione delle build a 64 bit delle app Novell. iOS** del documento sulle [considerazioni sulla piattaforma di bit 32/64](~/cross-platform/macios/32-and-64/index.md#enable-64)

## <a name="finishing-up"></a>Completamento

Indipendentemente dal fatto che si scelga di usare il metodo automatico o manuale per convertire l'applicazione Novell. iOS dalla distribuzione classica alle API unificate, esistono diverse istanze che richiedono un intervento manuale. Per informazioni sui problemi noti e su come risolvere il problema, vedere [i suggerimenti per l'aggiornamento del codice al documento API unificata](~/cross-platform/macios/unified/updating-tips.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Suggerimenti per l'aggiornamento del codice per l'API unificata](~/cross-platform/macios/unified/updating-tips.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)

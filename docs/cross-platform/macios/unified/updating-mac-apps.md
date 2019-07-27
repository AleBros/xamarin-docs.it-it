---
title: Aggiornamento delle app Mac esistenti
description: Questo documento descrive i passaggi da seguire per aggiornare un'app Novell. Mac dalla API classica al API unificata.
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: c1a374feaadf28898b7fde8e364cf0adab83acd5
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509598"
---
# <a name="updating-existing-mac-apps"></a>Aggiornamento delle app Mac esistenti

L'aggiornamento di un'app esistente per l'uso del API unificata richiede modifiche al file di progetto stesso nonché agli spazi dei nomi e alle API usati nel codice dell'applicazione.

## <a name="the-road-to-64-bits"></a>La strada a 64 bit

Le nuove API unificate sono necessarie per supportare le architetture di dispositivi a 64 bit da un'applicazione Novell. Mac. A partire dal 1 ° febbraio 2015 Apple richiede che tutti i nuovi invii di app per Mac App Store supportino architetture a 64 bit.

Novell fornisce strumenti per Visual Studio per Mac e Visual Studio per automatizzare il processo di migrazione dalla API classica al API unificata oppure è possibile convertire i file di progetto manualmente. Mentre l'utilizzo degli strumenti automatici è molto suggerito, in questo articolo vengono illustrati entrambi i metodi.

### <a name="before-you-start"></a>Prima di iniziare...

Prima di aggiornare il codice esistente alla API unificata, è consigliabile eliminare tutti gli *avvisi di compilazione*. Molti *avvisi* nell'API classica diventeranno errori dopo la migrazione a Unified. La correzione prima di iniziare è più semplice perché i messaggi del compilatore provenienti dal API classica spesso forniscono suggerimenti sugli elementi da aggiornare.

## <a name="automated-updating"></a>Aggiornamento automatico

Una volta corretti gli avvisi, selezionare un progetto Mac esistente in Visual Studio per Mac o Visual Studio e scegliere **Esegui migrazione a Novell. Mac API unificata** dal menu **progetto** . Ad esempio:

![](updating-mac-apps-images/beta-tool1.png "Scegliere Esegui migrazione a Novell. Mac API unificata dal menu progetto")

È necessario accettare questo avviso prima dell'esecuzione della migrazione automatica (ovviamente è necessario assicurarsi di disporre di backup/controllo del codice sorgente prima di intraprendere questa avventura):

![](updating-mac-apps-images/migrate01.png "Accetta questo avviso prima dell'esecuzione della migrazione automatica")

Sono disponibili due tipi di Framework di destinazione supportati che è possibile selezionare quando si usa il API unificata in un'applicazione Novell. Mac:

- **Novell. Mac Mobile Framework** : si tratta della stessa versione di .NET Framework ottimizzata usata da Novell. iOS e Novell. Android che supporta un subset del Framework **Desktop** completo. Si tratta del Framework consigliato perché fornisce binari di dimensioni inferiori a causa del comportamento di collegamento superiore.
- **Novell. Mac .net 4,5 Framework** -questo Framework è di nuovo un subset del Framework per **Desktop** . Tuttavia, viene ritagliato molto meno del Framework **Desktop** completo rispetto al Framework per **dispositivi mobili** e dovrebbe essere _semplicemente funzionante_ con la maggior parte dei pacchetti NuGet o librerie di terze parti. Questo consente allo sviluppatore di utilizzare gli assembly **Desktop** standard, pur continuando a utilizzare un Framework supportato, ma questa opzione genera bundle di applicazioni di dimensioni maggiori. Si tratta del Framework consigliato in cui vengono usati gli assembly .NET di terze parti che non sono compatibili con il **Framework per dispositivi mobili Novell. Mac**. Per un elenco degli assembly supportati, vedere la documentazione degli [assembly](~/cross-platform/internals/available-assemblies.md) .

Per informazioni dettagliate sui Framework di destinazione e le implicazioni della selezione di una destinazione specifica per l'applicazione Novell. Mac, vedere la documentazione relativa ai [Framework di destinazione](~/mac/platform/target-framework.md) . 

Lo strumento consente di automatizzare tutti i passaggi descritti nella sezione **aggiornamento manuale** riportata di seguito ed è il metodo consigliato per convertire un progetto Novell. Mac esistente nel API unificata.

## <a name="steps-to-update-manually"></a>Passaggi per l'aggiornamento manuale

Anche in questo caso, dopo aver corretto gli avvisi, seguire questa procedura per aggiornare manualmente le app Novell. Mac per usare la nuova API unificata:

### <a name="1-update-project-type--build-target"></a>1. Aggiornare il tipo di progetto & destinazione di compilazione

Modificare la versione del progetto nei file **csproj** da `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` a `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Modificare il file **csproj** in un editor di testo, sostituendo il primo elemento `<ProjectTypeGuids>` nell'elemento come illustrato di seguito:

![](updating-mac-apps-images/csproj.png "Modificare il file csproj in un editor di testo, sostituendo il primo elemento nell'elemento ProjectTypeGuids come illustrato")

Modificare l'elemento **Import** che contiene `Xamarin.Mac.targets` in `Xamarin.Mac.CSharp.targets` come illustrato:

![](updating-mac-apps-images/csproj2.png "Modificare l'elemento Import che contiene Novell. Mac. targets in Novell. Mac. CSharp. targets come illustrato")

Aggiungere le seguenti righe di codice dopo l' `<AssemblyName>` elemento:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Esempio:

![](updating-mac-apps-images/csproj3.png "Aggiungere queste righe di codice dopo l'elemento < AssemblyName >")

### <a name="2-update-project-references"></a>2. Aggiornare i riferimenti al progetto

Espandere il nodo **riferimenti** del progetto di applicazione Mac. Inizialmente verrà visualizzato un riferimento di tipo * Broken- **XamMac** simile a questo screenshot (perché è stato appena modificato il tipo di progetto):

![](updating-mac-apps-images/references.png "Inizialmente verrà visualizzato un riferimento XamMac rotto simile a questo screenshot")

Fare clic sull' **icona** a forma di ingranaggio accanto alla voce **XamMac** e selezionare **Elimina** per rimuovere il riferimento rotto.

Fare quindi clic con il pulsante destro del mouse sulla cartella **riferimenti** nella **Esplora soluzioni** e scegliere **modifica riferimenti**. Scorrere fino alla fine dell'elenco di riferimenti e inserire un segno di spunta oltre a **Novell. Mac**.

![](updating-mac-apps-images/references2.png "Scorrere fino alla fine dell'elenco di riferimenti e inserire un segno di spunta oltre a Novell. Mac.")

Fare clic su **OK** per salvare le modifiche dei riferimenti al progetto.

### <a name="3-remove-monomac-from-namespaces"></a>3. Rimuovere MonoMac da spazi dei nomi

Rimuovere il prefisso **MonoMac** dagli spazi dei nomi `using` nelle istruzioni o ogni volta che un nomeclasse è stato completo (ad esempio, `MonoMac.AppKit`diventa semplicemente `AppKit`).

### <a name="4-remap-types"></a>4. Mapping di tipi

Sono stati introdotti [tipi nativi](~/cross-platform/macios/nativetypes.md) che sostituiscono alcuni tipi utilizzati in precedenza, ad esempio istanze `System.Drawing.RectangleF` di `CoreGraphics.CGRect` con (ad esempio). L'elenco completo dei tipi è reperibile nella pagina [tipi nativi](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. Correggi override del metodo

Per `AppKit` alcuni metodi la firma è stata modificata in modo da usare i nuovi [tipi nativi](~/cross-platform/macios/nativetypes.md) , ad esempio `nint`. Se sottoclassi personalizzate eseguono l'override di questi metodi, le firme non saranno più corrispondenti e comporteranno errori. Correggere questi override del metodo modificando la sottoclasse in modo che corrisponda alla nuova firma utilizzando i tipi nativi. 

## <a name="considerations"></a>Considerazioni

Quando si converte un progetto Novell. Mac esistente dal API classica al nuovo API unificata se tale app si basa su uno o più pacchetti di componenti o NuGet, tenere presente le considerazioni seguenti. 

### <a name="components"></a>Componenti

Tutti i componenti inclusi nell'applicazione dovranno anche essere aggiornati al API unificata o si verifica un conflitto quando si tenta di eseguire la compilazione. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione dall'archivio componenti di Novell che supporta il API unificata ed eseguire una compilazione pulita. Per tutti i componenti che non sono stati ancora convertiti dall'autore, verrà visualizzato un avviso solo 32 bit nell'archivio componenti.

### <a name="nuget-support"></a>Supporto NuGet

Anche se sono state apportate modifiche a NuGet per lavorare con il supporto API unificata, non è stata rilasciata una nuova versione di NuGet, quindi si sta valutando come ottenere NuGet per riconoscere le nuove API. 

Fino a quel momento, come per i componenti, è necessario cambiare il pacchetto NuGet incluso nel progetto in una versione che supporta le API unificate ed eseguire una compilazione pulita in seguito.

> [!IMPORTANT]
> Se è presente un errore nel formato _"errore 3 non può includere sia ' monomac. dll ' che ' Novell. Mac. dll ' nello stesso progetto Novell. Mac-' Novell. Mac. dll ' viene fatto riferimento in modo esplicito, mentre ' monomac. dll ' fa riferimento a' xxx, Version = 0.0.000, Culture = neutral, PublicKeyToken = null ' "_ dopo la conversione dell'applicazione nelle API unificate, è in genere dovuto alla presenza di un componente o di un pacchetto NuGet nel progetto che non è stato aggiornato al API unificata. È necessario rimuovere il componente/NuGet esistente, eseguire l'aggiornamento a una versione che supporta le API unificate ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Abilitazione di Build a 64 bit di app Novell. Mac

Per un'applicazione Novell. Mac per dispositivi mobili che è stata convertita nel API unificata, lo sviluppatore deve comunque abilitare la compilazione dell'applicazione per i computer a 64 bit dalle opzioni dell'app. Per istruzioni dettagliate sull'abilitazione di compilazioni a 64 bit, vedere la pagina relativa all' **Abilitazione delle build a 64 bit delle app Novell. Mac** del documento sulle [considerazioni sulla piattaforma di bit 32/64](~/cross-platform/macios/32-and-64/index.md)
    
## <a name="finishing-up"></a>Completamento

Indipendentemente dal fatto che si scelga di usare il metodo automatico o manuale per convertire l'applicazione Novell. Mac dalla versione classica alle API unificate, esistono diverse istanze che richiedono un intervento manuale. Per informazioni sui problemi noti e su come risolvere il problema, vedere [i suggerimenti per l'aggiornamento del codice al documento API unificata](~/cross-platform/macios/unified/updating-tips.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Suggerimenti per l'aggiornamento del codice per l'API unificata](~/cross-platform/macios/unified/updating-tips.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)

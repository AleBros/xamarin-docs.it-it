---
title: Uso di associazioni di xamarin. Mac per le app Console
description: Creare un'app console headless usando xamarin. Mac per macOS native, le API di accesso.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: 135ef06cd044235023c3acc970c8e7a33f144b47
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378276"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Binding xamarin. Mac in applicazioni console

Esistono alcuni scenari in cui si desidera utilizzare alcune delle API native Apple nel linguaggio c# per compilare un'applicazione headless &ndash; uno che non ha un'interfaccia utente &ndash; usando c#.

I modelli di progetto per le applicazioni Mac includono una chiamata a `NSApplication.Init()` seguita da una chiamata a `NSApplication.Main(args)`, si presenta in genere simile al seguente:

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

La chiamata a `Init` Prepara il runtime di xamarin. Mac, la chiamata a `Main(args)` avvia Cocoa applicazione ciclo principale, che consente di preparare l'applicazione per ricevere gli eventi di tastiera e mouse e visualizzare la finestra principale dell'applicazione.   La chiamata a `Main` si tenterà di individuare le risorse di Cocoa, preparare una finestra toplevel e prevede che il programma a far parte di un bundle dell'applicazione (applicazioni distribuite in una directory con il `.app` estensione e un layout molto specifico).

Applicazioni headless non è necessario un utente, l'interfaccia e non è necessario eseguire come parte di un bundle dell'applicazione.

## <a name="creating-the-console-app"></a>Creazione di app console

Pertanto, è preferibile iniziare con un normale tipo di progetto Console .NET.

È necessario eseguire alcune operazioni:

- Creare un progetto vuoto.
- Fare riferimento alla libreria di xamarin.
- Portare la dipendenza non gestita al progetto.

Questi passaggi sono illustrati in dettaglio più avanti:

### <a name="create-an-empty-console-project"></a>Creare un progetto Console vuoto

Creare un nuovo progetto Console .NET, assicurarsi che sia non .NET Core, come xamarin e .NET non viene eseguito con il runtime di .NET Core, viene eseguita solo con il runtime di Mono.

### <a name="reference-the-xamarinmac-library"></a>Fare riferimento alla libreria xamarin. Mac

Per compilare il codice, è consigliabile fare riferimento il `Xamarin.Mac.dll` assembly da questa directory: `/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

A tale scopo, passare ai riferimenti del progetto, selezionare la **Assembly .NET** e scegliere il **Sfoglia** pulsante per individuare il file nel file system.  Passare al percorso precedente e quindi selezionare il **xamarin** da tale directory.

Ciò ti darà accesso alle API Cocoa in fase di compilazione.   A questo punto, è possibile aggiungere `using AppKit` nella parte superiore del file e chiamata di `NSApplication.Init()` (metodo).   È presente un solo altro passaggio prima di poter eseguire l'applicazione.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Portare la libreria di supporto non gestita all'interno del progetto

Prima che venga eseguita l'applicazione, è necessario portare il `Xamarin.Mac` libreria di supporto nel progetto.   A tale scopo, aggiungere un nuovo file al progetto (in Opzioni progetto selezionare **Add**e quindi **Aggiungi File esistente**) e passare a questa directory:

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

In questo caso, selezionare il file **libxammac.dylib**.   All'utente verrà offerta una scelta di copia, il collegamento o lo spostamento.   Mi piace personalmente il collegamento, ma la copia è possibile usare anche.    È necessario selezionare il file e nel riquadro Proprietà (selezionare **Vista > riquadri > proprietà** se il riquadro proprietà non è visibile), passare al **compilare** sezione e impostare il **Copy to Output Directory** se si imposta su **copia se più recente**.

È ora possibile eseguire l'applicazione xamarin. Mac.

Il risultato nella directory bin avrà un aspetto simile al seguente:

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Per eseguire questa app, è necessario tutti questi file nella stessa directory.

## <a name="building-a-standalone-application-for-distribution"></a>Creazione di un'applicazione autonoma per la distribuzione

È possibile distribuire un singolo file eseguibile per gli utenti.  A tale scopo, è possibile usare il `mkbundle` dello strumento per trasformare i vari file in un file eseguibile autonomo.

In primo luogo, assicurarsi che l'applicazione viene compilata e viene eseguito.   Quando si è soddisfatti dei risultati, è possibile eseguire il comando seguente dalla riga di comando:

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

Nella chiamata della riga di comando precedente, l'opzione `-o` viene usato per specificare l'output generato, in questo caso, abbiamo girato `/tmp/consoleapp`.   Ora è un'applicazione autonoma che è possibile distribuire e non presenta dipendenze esterne su Mono o xamarin. Mac, è un file eseguibile autonomo completamente.

La riga di comando specificata manualmente il **Machine. config** file da usare e un file di configurazione di mapping di libreria a livello di sistema.   Non sono necessari per tutte le applicazioni, ma è consigliabile creare un bundle, perché sono usati quando si usa più funzionalità di .NET

## <a name="project-less-builds"></a>Compilazioni senza progetto

Non è necessario un progetto completo per creare un'applicazione xamarin. Mac indipendente, è anche possibile usare semplice Unix makefile per svolgere il lavoro.   Nell'esempio seguente viene illustrato come si può configurare un makefile per un'applicazione semplice riga di comando:

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)


bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

Il codice precedente `Makefile` fornisce tre destinazioni:

- `make` verrà compilato il programma
- `make run` verrà compilata ed eseguita l'applicazione nella directory corrente
- `make bundle` verrà creato un file eseguibile autonomo

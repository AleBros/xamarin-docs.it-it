---
title: Uso dei binding Xamarin.Mac per le app console
description: Creare un'app console per l'uso di Xamarin.Mac per accedere alle API macOS native.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: a38543d575f655a3b1b70ff94eece7fef1bf2d40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769901"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Binding Xamarin.Mac nelle app console

Esistono scenari in cui si vogliono usare alcune API native di Apple C# in per compilare un'applicazione &ndash; senza intestazione che non dispone di un'interfaccia &ndash; utente che usa. C#

I modelli di progetto per le applicazioni Mac includono una `NSApplication.Init()` chiamata a seguita da una `NSApplication.Main(args)`chiamata a, in genere è simile a quanto segue:

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

La chiamata a `Init` prepara il runtime Xamarin.Mac, la chiamata a `Main(args)` avvia il ciclo principale dell'applicazione Cocoa, che prepara l'applicazione a ricevere gli eventi della tastiera e del mouse e Mostra la finestra principale dell'applicazione.   La chiamata a `Main` tenterà anche di individuare le risorse Cocoa, preparare una finestra di livello superiore e prevedere che il programma faccia parte di un bundle di applicazioni (programmi distribuiti in una directory con `.app` l'estensione e un layout molto specifico).

Le applicazioni senza intestazione non necessitano di un'interfaccia utente e non devono essere eseguite come parte del bundle di un'applicazione.

## <a name="creating-the-console-app"></a>Creazione dell'app console

Quindi, è preferibile iniziare con un normale tipo di progetto console .NET.

È necessario eseguire alcune operazioni:

- Creare un progetto vuoto.
- Fare riferimento alla libreria Xamarin.Mac. dll.
- Portare la dipendenza non gestita al progetto.

Questi passaggi sono illustrati in modo più dettagliato di seguito:

### <a name="create-an-empty-console-project"></a>Creare un progetto console vuoto

Creare un nuovo progetto console .NET, assicurarsi che sia .NET e non .NET Core, poiché Xamarin.Mac. dll non viene eseguito nel runtime di .NET Core, ma viene eseguito solo con il runtime di mono.

### <a name="reference-the-xamarinmac-library"></a>Fare riferimento alla libreria Xamarin.Mac

Per compilare il codice, è necessario fare riferimento all' `Xamarin.Mac.dll` assembly da questa directory:`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

A tale scopo, passare ai riferimenti del progetto, selezionare la scheda **assembly .NET** e fare clic sul pulsante **Sfoglia** per individuare il file nella file System.  Passare al percorso precedente, quindi selezionare **Xamarin.Mac. dll** da tale directory.

In questo modo sarà possibile accedere alle API Cocoa in fase di compilazione.   A questo punto, è possibile aggiungere `using AppKit` all'inizio del file e chiamare il `NSApplication.Init()` metodo.   Prima di poter eseguire l'applicazione, è necessario un solo passaggio.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Portare la libreria di supporto non gestita nel progetto

Prima che l'applicazione venga eseguita, è necessario portare la `Xamarin.Mac` libreria di supporto nel progetto.   A tale scopo, aggiungere un nuovo file al progetto (in opzioni progetto, selezionare **Aggiungi**e quindi **Aggiungi file esistente**) e passare a questa directory:

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

Qui, selezionare il file **libxammac. dylib**.   Verrà offerta una scelta di copia, collegamento o trasferimento.   Personalmente come il collegamento, ma anche la copia funziona.    È quindi necessario selezionare il file e, nel riquadro delle proprietà, selezionare **visualizza > rilievi > proprietà** se il riquadro proprietà non è visibile), passare alla sezione **compilazione** e impostare l'impostazione **copia in directory di output** su **copia se più recente**.

È ora possibile eseguire l'applicazione Xamarin.Mac.

Il risultato nella directory bin sarà simile al seguente:

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Per eseguire questa app, sono necessari tutti i file nella stessa directory.

## <a name="building-a-standalone-application-for-distribution"></a>Compilazione di un'applicazione autonoma per la distribuzione

Potrebbe essere necessario distribuire un singolo file eseguibile agli utenti.  A tale scopo, è possibile utilizzare lo `mkbundle` strumento per trasformare i vari file in un eseguibile autonomo.

Assicurarsi prima di tutto che l'applicazione venga compilata ed eseguita.   Quando si è soddisfatti dei risultati, è possibile eseguire il comando seguente dalla riga di comando:

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

Nella chiamata della riga di comando precedente, l'opzione `-o` viene usata per specificare l'output generato, in questo caso, è stato `/tmp/consoleapp`superato.   Si tratta di un'applicazione autonoma che è possibile distribuire e non ha dipendenze esterne in mono o Xamarin.Mac, ma è un file eseguibile completamente indipendente.

La riga di comando ha specificato manualmente il file **Machine. config** da usare e un file di configurazione del mapping della libreria a livello di sistema.   Non sono necessarie per tutte le applicazioni, ma è utile aggregarle, perché vengono usate quando si usano più funzionalità di .NET

## <a name="project-less-builds"></a>Compilazione senza progetto

Non è necessario un progetto completo per creare un'applicazione Xamarin.Mac autonoma, ma è anche possibile usare semplici makefile UNIX per eseguire il processo.   Nell'esempio seguente viene illustrato come è possibile configurare un makefile per una semplice applicazione della riga di comando:

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

Il precedente `Makefile` fornisce tre destinazioni:

- `make`compilerà il programma
- `make run`compilerà ed eseguirà il programma nella directory corrente
- `make bundle`creerà un eseguibile indipendente

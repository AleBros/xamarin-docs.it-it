---
title: Introduzione a macOS
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: c129079aad14ac9e8aad6f73670ce9a43a36f222
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-macos"></a>Introduzione a macOS


## <a name="what-you-will-need"></a>È necessario

* Seguire le istruzioni il nostro [introduzione Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida.

* Un assembly .NET da utilizzare con **Embeddinator 4000**.

* Un'applicazione Cocoa macOS

Continua dopo aver seguito le istruzioni riportate in questo [introduzione Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida. Se si dispone già di un assembly .NET è possibile ignorare direttamente in **utilizzando Embeddinator-4000** sezione.

## <a name="creating-a-net-assembly"></a>Creazione di un assembly .NET

Per compilare un assembly .NET è necessario aprire [Visual Studio per Mac](https://www.visualstudio.com/vs/visual-studio-mac/) e creare un nuovo **progetto di libreria .NET** attraverso la pratica *File > nuova soluzione > altri > .NET > libreria*. Fare clic su Avanti e assegnare *Weather* come il *nome progetto*e fare clic su *crea*.

Seguire i passaggi successivi:

1. Eliminare il **MyClass.cs** file e **proprietà** cartella.

2. Fare clic con il pulsante destro su *progetto Weather > Aggiungi > Nuovo File.*

3. Selezionare *classe vuota* e utilizzare **XAMWeatherFetcher** come nome, quindi fare clic su nuovo.

4. Sostituire il contenuto di *XAMWeatherFetcher.cs* con il codice seguente:

```csharp
using System;
using System.Json;
using System.Net;

public class XAMWeatherFetcher {

    static string urlTemplate = @"https://query.yahooapis.com/v1/public/yql?q=select%20item.condition%20from%20weather.forecast%20where%20woeid%20in%20(select%20woeid%20from%20geo.places(1)%20where%20text%3D%22{0}%2C%20{1}%22)&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys";
    public string City { get; private set; }
    public string State { get; private set; }

    public XAMWeatherFetcher (string city, string state)
    {
        City = city;
        State = state;
    }

    public XAMWeatherResult GetWeather ()
    {
        try {
            using (var wc = new WebClient ()) {
                var url = string.Format (urlTemplate, City, State);
                var str = wc.DownloadString (url);
                var json = JsonValue.Parse (str)["query"]["results"]["channel"]["item"]["condition"];
                var result = new XAMWeatherResult (json["temp"], json["text"]);
                return result;
            }
        }
        catch (Exception ex) {
            // Log some of the exception messages
            Console.WriteLine (ex.Message);
            Console.WriteLine (ex.InnerException?.Message);
            Console.WriteLine (ex.InnerException?.InnerException?.Message);

            return null;
        }

    }
}

public class XAMWeatherResult {
    public string Temp { get; private set; }
    public string Text { get; private set; }

    public XAMWeatherResult (string temp, string text)
    {
        Temp = temp;
        Text = text;
    }
}
```

Si noterà che `Using System.Json;` restituisce un errore; per risolvere il problema è necessario eseguire le operazioni seguenti:

1. Fare doppio clic su di **riferimenti** cartella.

2. Fare clic su di **pacchetti** scheda.

3. Controllare **System.Json**.

4. Click **Ok**.

Al termine sopra, è sufficiente è compilazione l'assembly .NET facendo clic su *menu Compila > Build All* o ⌘ + b. Compilare messaggio deve essere visualizzato nella barra di stato superiore di caso di esito positivo.

A questo punto, fare clic sul *Weather* nodo del progetto e selezionare *rivela in Finder*. In Finder passare al *bin/Debug* all'interno della cartella; trovi **Weather.dll.**

## <a name="using-embeddinator-4000"></a>Utilizzando Embeddinator 4000

Se è stato installato utilizzando l'installazione guidata pkg Embeddinator-4000 e avviata una nuova sessione terminal dopo l'installazione, dovrebbe essere in grado di utilizzare il **objcgen** comando (in caso contrario è possibile utilizzare il percorso assoluto: `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`); **objcgen** è lo strumento che è necessario generare una libreria nativa da un assembly .NET.

Aprire terminal, `cd` alla cartella contenente Weather.dll ed eseguire **objcgen** con gli argomenti indicati di seguito:

```shell
cd /Users/Alex/Projects/Weather/Weather/bin/Debug

objcgen --debug --outdir=output -c Weather.dll
```

Tutto ciò che è necessario verrà inserito il **output** accanto alla directory *Weather.dll*. Se si dispone di propri assembly .NET, sostituire *Weather.dll* e seguire la stessa procedura sopra.

## <a name="using-the-generated-output-in-an-xcode-project"></a>Utilizzo dell'output generato in un progetto Xcode

Xcode aprire e creare un nuovo **macOS applicazione Cocoa** e denominarlo **MyWeather**. Fare clic con il pulsante destro sul *nodo del progetto MyWeather*, selezionare *aggiungere file alla "MyWeather"*, passare al **output** directory creata dal *Embeddinator 4000* e aggiungere i file seguenti:

* bindings.h
* embeddinator.h
* glib.h
* mono-support.h
* mono_embeddinator.h
* objc-support.h
* libWeather.dylib
* Weather.dll

Assicurarsi che **copiare gli elementi, se necessario** sia selezionata nel pannello Opzioni della finestra di dialogo file.

Ora è necessario verificare che **libWeather.dylib** e **Weather.dll** inserire il bundle dell'app:

* Fare clic su *nodo del progetto MyWeather*.
* Selezionare *Build Phases* scheda.
* Aggiungere un nuovo *fase di copia dei file*.
* In *destinazione* selezionare **Framework** e aggiungere **libWeather.dylib**.
* Aggiungere un nuovo *fase di copia dei file*.
* In *destinazione* selezionare **eseguibili**, aggiungere **Weather.dll** e assicurarsi che *accesso di codice copia* è selezionata.

Aprire quindi **ViewController.m** e sostituirne il contenuto con:

```objective-c
#import "ViewController.h"
#import "bindings.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    XAMWeatherFetcher * fetcher = [[XAMWeatherFetcher alloc] initWithCity:@"Boston" state:@"MA"];
    XAMWeatherResult * weather = [fetcher getWeather];

    NSString * result;
    if (weather)
        result = [NSString stringWithFormat:@"%@ °F - %@", weather.temp, weather.text];
    else
        result = @"An error occured";

    NSTextField * textField = [NSTextField labelWithString:result];
    [self.view addSubview:textField];
}

@end
```

Infine eseguire il progetto Xcode e simile al seguente verrà visualizzati:

![Esecuzione di esempio MyWeather](macos-images/weather-from-csharp-macos.png)

È disponibile un esempio più completo e di qualità superiore [qui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).

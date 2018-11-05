---
title: Unit test di app Xamarin.iOS
description: Questo documento offre una panoramica su come eseguire unit test di un'applicazione Xamarin.iOS. Descrive come creare un progetto di unit test, scrivendo ed eseguendo test.
ms.prod: xamarin
ms.assetid: BD959779-3239-79B6-5289-3A9ECDFBD973
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: b995ed5cf8d8735e87fb18c3a69d43b5a079b82f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674983"
---
# <a name="unit-testing-xamarinios-apps"></a>Unit test di app Xamarin.iOS

Questo documento descrive come creare unit test per i progetti Xamarin.iOS.
Gli unit test con Xamarin.iOS vengono eseguiti mediante il framework Touch.Unit, che include un test runner iOS e una versione modificata di NUnit denominata [Touch.Unit](https://github.com/xamarin/Touch.Unit), dotata di un set di API note che semplificano la creazione di unit test.

## <a name="setting-up-a-test-project-in-visual-studio-for-mac"></a>Configurazione di un progetto di test in Visual Studio per Mac

Per configurare un framework di testing unità per il progetto, è sufficiente aggiungere alla propria soluzione un progetto di tipo **Progetto di unit test iOS**. A questo scopo, fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi > Aggiungi nuovo progetto**. Nell'elenco selezionare **iOS > Test > API unificata > Progetto di unit test iOS** (è possibile scegliere C# o F#).

![](touch.unit-images/00.png "Scegliere C# o F#")

L'esempio riportato sopra crea un progetto di base che contiene un programma runner di base e che fa riferimento al nuovo assembly MonoTouch.NUnitLite. Il progetto avrà un aspetto simile al seguente:

![](touch.unit-images/01.png "Progetto in Esplora soluzioni")

La classe `AppDelegate.cs` contiene il test runner e ha un aspetto simile al seguente:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TouchRunner runner;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow (UIScreen.MainScreen.Bounds);
        runner = new TouchRunner (window);

        // register every tests included in the main application/assembly
        runner.Add (System.Reflection.Assembly.GetExecutingAssembly ());

        window.RootViewController = new UINavigationController (runner.GetViewController ());

        // make the window visible
        window.MakeKeyAndVisible ();

        return true;
    }
}
```

> [!NOTE]
> Il tipo di progetto di unit test iOS non è disponibile in Visual Studio 2017 in Windows.

## <a name="writing-some-tests"></a>Scrittura di alcuni test

Ora che la shell di base è configurata, è possibile scrivere i primi set di test.

I test vengono scritti creando classi a cui è applicato l'attributo `[TestFixture]`. All'interno di ogni classe TestFixture occorre applicare l'attributo `[Test]` a ogni metodo che deve essere richiamato dal test runner. Le fixture di test effettive possono essere incluse in qualsiasi file del progetto di test.

Per iniziare rapidamente, selezionare **Aggiungi/Aggiungi nuovo file**, quindi nel gruppo Xamarin.iOS selezionare **UnitTests**. Verrà aggiunto un file scheletro che contiene un test superato, un test non superato e un test ignorato, come nell'esempio seguente:

```csharp
using System;
using NUnit.Framework;

namespace Fixtures {

    [TestFixture]
    public class Tests {

        [Test]
        public void Pass ()
        {
                Assert.True (true);
        }

        [Test]
        public void Fail ()
        {
                Assert.False (true);
        }

        [Test]
        [Ignore ("another time")]
        public void Ignore ()
        {
                Assert.True (false);
        }
    }
}
```

## <a name="running-your-tests"></a>Esecuzione dei test

Per eseguire questo progetto all'interno della soluzione, fare clic su di esso con il pulsante destro del mouse e scegliere **Debug Item** (Esegui debug elemento) o **Run Item** (Esegui elemento).

Il test runner consente di vedere quali test sono registrati e di selezionare singolarmente i test che possono essere eseguiti.

[![](touch.unit-images/02-sml.png "Elenco dei test registrati")](touch.unit-images/02.png#lightbox) 
[![](touch.unit-images/03-sml.png "Singolo test")](touch.unit-images/03.png#lightbox) 

[![](touch.unit-images/04-sml.png "Risultati dell'esecuzione")](touch.unit-images/04.png#lightbox)

È possibile eseguire singole fixture di test selezionandone una dalle visualizzazioni annidate oppure eseguire tutti i test scegliendo "Run Everything" (Esegui tutto). Se si esegue il test predefinito, che dovrebbe includere un test superato, un test non superato e un test ignorato, il report ha un aspetto simile al seguente ed è possibile eseguire il drill-down direttamente al test non superato e trovare maggiori informazioni sull'errore:

[![](touch.unit-images/05-sml.png "Report di esempio")](touch.unit-images/05.png#lightbox) [![](touch.unit-images/06-sml.png "Report di esempio")](touch.unit-images/06.png#lightbox) [![](touch.unit-images/07-sml.png "Report di esempio")](touch.unit-images/07.png#lightbox)

Si può anche consultare la finestra Output applicazione nell'IDE per verificare quali test sono in esecuzione e qual è il loro stato corrente.

## <a name="writing-new-tests"></a>Scrittura di nuovi test

NUnitLite è una versione modificata del progetto NUnit denominato [Touch.Unit](https://github.com/xamarin/Touch.Unit). È un framework di testing leggero per .NET, basato sulle idee contenute in [NUnit](http://nunit.com/), di cui fornisce un sottoinsieme di funzionalità.
Usa una quantità minima di risorse e può essere eseguito su piattaforme con limitazioni di risorse come quelle usate per lo sviluppo per sistemi operativi Mobile ed Embedded. È possibile [esplorare l'API NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/) disponibile in Xamarin.iOS. Con lo scheletro di base fornito dal modello di unit test, il punto di ingresso principale è costituito dai metodi della [classe Assert](https://developer.xamarin.com/api/type/NUnit.Framework.Assert/).

Oltre a questi metodi, la funzionalità di testing unità è suddivisa negli spazi dei nomi seguenti che fanno parte di NUnitLite:

- [NUnit.Framework](https://developer.xamarin.com/api/namespace/NUnit.Framework/)
- [NUnit.Constraints](https://developer.xamarin.com/api/namespace/NUnit.Framework.Constraints/)
- [NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/)
- [NUniteLite.Runner](https://developer.xamarin.com/api/namespace/NUnitLite.Runner/)

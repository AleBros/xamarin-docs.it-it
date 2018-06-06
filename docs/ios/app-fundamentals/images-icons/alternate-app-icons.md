---
title: Icone di App alternativi in xamarin. IOS
description: Questo documento descrive come usare le icone app alternativi in xamarin. IOS. Viene descritto come gestire a livello di codice sull'icona dell'app, come aggiungere queste icone per un progetto xamarin. IOS e come modificare il file Info. plist.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 1d37a29982454367c35bfdfad205abce0eb025af
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784110"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Icone di App alternativi in xamarin. IOS

_In questo articolo viene illustrato l'utilizzo di icone app alternativi in xamarin. IOS._

Apple ha aggiunto numerosi miglioramenti a iOS 10.3 che consentono di gestire la relativa icona di un'app:

 - `ApplicationIconBadgeNumber` -Ottiene o imposta la chiave per il badge dell'icona di app.
 - `SupportsAlternateIcons` -Se `true` l'applicazione dispone di un set alternativo di icone.
 - `AlternateIconName` : Restituisce il nome dell'icona alternativo attualmente selezionata o `null` se si utilizza l'icona primario.
 - `SetAlternameIconName` : Utilizzare questo metodo per passare l'icona dell'app dell'icona alternativo specificato.

![](alternate-app-icons-images/icons04.png "Un esempio di avviso quando un'applicazione modifica la relativa icona")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Aggiunta di icone alternative a un progetto xamarin

Per consentire un'app passare a un'icona di alternativa, una raccolta di immagini icona dovrà essere incluso nel progetto dell'app xamarin. IOS. Queste immagini non possono essere aggiunto al progetto utilizzando la tipica `Assets.xcassets` (metodo), devono essere aggiunti al **risorse** cartella direttamente.

Seguire questa procedura:

1. Selezionare le immagini icona obbligatorio in una cartella, selezionare tutte e trascinarli il **risorse** cartella la **Esplora**:

    ![](alternate-app-icons-images/icons00.png "Selezionare le immagini di icone da una cartella")

2. Quando richiesto, selezionare **copia**, **utilizzare la stessa operazione per tutti i file selezionati** e fare clic su di **OK** pulsante:

    ![](alternate-app-icons-images/icons02.png "Aggiungi File per la finestra di dialogo cartella")

3. Il **risorse** cartella dovrebbe essere simile al seguente quando completata:

    ![](alternate-app-icons-images/icons01.png "La cartella delle risorse dovrebbe essere simile al seguente")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modificare il File Info. plist

Con le immagini necessarie aggiunti per il **risorse** cartella, il [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) chiave dovrà essere aggiunta al progetto **Info. plist** file. Questa chiave definirà il nome della nuova icona e le immagini che lo compongono.

Seguire questa procedura:

1. In **Esplora soluzioni** fare doppio clic sul file **Info.plist** per aprirlo e modificarlo.
2. Passare il **origine** visualizzazione.
3. Aggiungere un **aggregare icone** chiave e lasciare il **tipo** impostato su **dizionario**.
4. Aggiungere un `CFBundleAlternateIcons` chiave e impostare il **tipo** a **dizionario**.
5. Aggiungere un `AppIcons2` chiave e impostare il **tipo** a **dizionario**. Questo sarà il nome del nuovo set di icona app alternativi.
6. Aggiungere un `CFBundleIconFiles` chiave e impostare il **tipo** a **matrice**
7. Aggiungere una nuova stringa per il `CFBundleIconFiles` matrice per ogni file di icona omettendo l'estensione e `@2x`, `@3x`, suffissi e così via (esempio `100_icon`). Ripetere questo passaggio per ogni file che costituiscono il set di icone alternativo.
8. Aggiungere un `UIPrerenderedIcon` chiave per la `AppIcons2` dizionario, impostare il **tipo** a **booleano** e il valore da **n**.
9. Salvare le modifiche apportate al file.

Il valore risultante **Info. plist** file dovrebbe essere simile al seguente quando completata:

![](alternate-app-icons-images/icons03.png "Il file Info. plist completato")

O come questa se aperti in un editor di testo:

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Icona dell'applicazione di gestione 

Con le immagini icona incluse nel progetto xamarin e **Info. plist** file configurato correttamente, lo sviluppatore può utilizzare una delle molte nuove funzionalità aggiunte a iOS 10.3 per controllare l'icona dell'applicazione.

Il `SupportsAlternateIcons` proprietà la `UIApplication` classe consente allo sviluppatore di verificare se un'applicazione supporta icone alternative. Ad esempio:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

Il `ApplicationIconBadgeNumber` proprietà la `UIApplication` classe consente allo sviluppatore di ottenere o impostare il numero corrente di badge dell'icona di app nella chiave. Il valore predefinito è zero (0). Ad esempio:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

Il `AlternateIconName` proprietà del `UIApplication` classe consente allo sviluppatore di ottenere il nome dell'icona app alternativo attualmente selezionata o restituisce `null` se l'app sta usando l'icona primario. Ad esempio:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

Il `SetAlternameIconName` proprietà la `UIApplication` classe consente agli sviluppatori di modificare l'icona dell'app. Passare il nome dell'icona per selezionare o `null` da restituire per l'icona primario. Ad esempio:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

Quando si esegue l'app e l'utente seleziona un'icona alternativa, verrà visualizzato un avviso simile al seguente:

![](alternate-app-icons-images/icons04.png "Un esempio di avviso quando un'applicazione modifica la relativa icona")

Se l'utente passa all'icona primario, verrà visualizzato un avviso simile al seguente:

![](alternate-app-icons-images/icons05.png "Un esempio di avviso quando un'app diventa l'icona primario")

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati l'aggiunta di icone app alternativo a un progetto xamarin e il relativo utilizzo all'interno dell'app.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)

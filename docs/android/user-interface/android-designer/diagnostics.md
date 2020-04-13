---
title: Diagnostica layout Android
description: Illustra la diagnostica del layout Android e come iniziare
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 746f74e68fa4816f1f7979980af9506dc0173542
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987582"
---
# <a name="android-layout-diagnostics"></a>Diagnostica del layout Android

La diagnostica del layout Android è progettata per contribuire a migliorare la qualità dei file di layout Android evidenziando problemi di qualità comuni e ottimizzazioni utili. Questa funzionalità è disponibile sia per Visual Studio 16.5 e Visual Studio per Mac 8.5.

Viene fornito un set predefinito di analizzatori per un'ampia gamma di problemi e ognuno può essere personalizzato per soddisfare le esigenze specifiche di un progetto. Gli analizzatori sono liberamente basati sul sistema di linting Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Abilitare la diagnostica del layout Android in Visual Studio 2019Enable Android layout diagnostics on Visual Studio 2019

Assicurarsi che l'impostazione di diagnostica del layout, **Abilita diagnostica layout**, sia abilitata. Per accedere a questa pagina delle opzioni, scegliere **Opzioni** > **strumenti**e quindi Editor di >  **testo****Android XML** > **Advanced**:

![Finestra di dialogo Opzioni che mostra l'opzione di diagnostica](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Una volta abilitato, l'editor di layout Android visualizzerà i problemi:

![Diagnostica Android abilitata in Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Abilitare la diagnostica del layout Android in Visual Studio per MacEnable Android layout diagnostics on Visual Studio for Mac

Assicurarsi che l'impostazione di diagnostica del layout, **Abilita diagnostica layout**, sia abilitata. Per accedere a questa pagina delle opzioni, **Text Editor** > scegliere Preferenze di **Visual Studio...** > **Preferences...**, quindi Scegliere Editor di testo**Android XML**:

![Finestra di dialogo Preferenze che mostra l'opzione di diagnostica](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Una volta abilitato, l'editor di layout Android visualizzerà i problemi:

![Diagnostica Android abilitata in Visual Studio per Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Funzionalità

Le sezioni seguenti illustrano le funzionalità disponibili nella diagnostica del layout Android.The following sections outline the available features in Android layout diagnostics.

### <a name="analyzers"></a>Analizzatori

Gli analizzatori vengono utilizzati per rilevare i problemi nei file di layout, ridurre i valori hardcoded, migliorare le prestazioni e contrassegnare gli errori. Per un elenco di analizzatori, vedere [Analizzatori diagnostici di progettazione AndroidFor](diagnostic-analyzers.md) a list of analyzers, see Android designer diagnostic analyzers

### <a name="diagnostic-configuration"></a>Configurazione diagnostica

Gli analizzatori possono essere configurati utilizzando un file XML, consentendo di modificare il livello di gravità predefinito, ignorare determinati file e passare variabili.

Puoi usare un file di base se hai un set di configurazioni che vuoi condividere tra più app Android. Per utilizzare questa funzionalità, creare un `-baseline` nuovo file di configurazione e aggiungere al nome del file. Le configurazioni di base vengono applicate prima e quindi i file di configurazione rimanenti.

> [!TIP]
> Ciò può essere utile se si desidera ignorare una serie di problemi in un'app Android nuova o esistente.

Il formato è:

```xml
<?xml version="1.0" encoding="utf-8" ?> 
<configuration>
    <issue id="DuplicateIDs" severity="warning">
        <ignore path="Resources/layout/layout1.xml" />
    </issue>
    <issue id="HardcodedText" severity="informational">
        <ignore path="Resources/layout/layout1.xml" />
        <ignore path="Resource/layout/layout2.xml" />
    </issue>
    <issue id="TooManyViews">
        <variable name="MAX_VIEW_COUNT" value="12" />
    </issue>
    <issue id="TooDeepLayout">
        <variable name="MAX_DEPTH" value="12" />
    </issue>
</configuration>
```

> [!NOTE]
> Attualmente le uniche `MAX_VIEW_COUNT` variabili sono (default: 80) `MAX_DEPTH` e `TooManyViews` `TooDeepLayout` (default: 10) per e rispettivamente.

I livelli di gravità sono:

- Suggerimento
- Info
- Avviso
- Errore
- Ignora

### <a name="add-a-configuration-file"></a>Aggiungere un file di configurazione

Creare un nuovo file XML nella radice di un progetto di app Android.Create a new XML file in the root of an Android app project. Il nome del file non è importante, `AndroidLayoutDiagnostics.xml`ma in questo esempio viene utilizzato:

![Aggiungi nuovo elemento](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Una volta aggiunto, il nuovo file XML dovrebbe essere visualizzato nell'albero del progetto dell'app Android:

![Albero del progetto dell'app Android](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Assicurarsi che l'azione di compilazione sia impostata su **AndroidResourceAnalysisConfig** nel pannello delle proprietà.
Il modo più semplice per estrarre il pannello delle proprietà per il nuovo file consiste nel fare clic con il pulsante destro del mouse sul file e selezionare le proprietà. Una volta visualizzato il pannello delle proprietà, è necessario modificare **l'azione** di compilazione in **AndroidResourceAnalysisConfig:**

![Impostare l'azione di compilazione nelle proprietà dell'elementoSet Build Action in Item Properties](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Ora che si dispone di un file `<configuration>` XML vuoto è necessario aggiungere l'elemento radice. A questo punto, è possibile regolare il comportamento predefinito di eventuali problemi supportati.
Se si desidera assicurarsi che le stringhe hardcoded vengano considerate come errori, aggiungere:

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![File di configurazione della diagnostica](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Ora che il testo hardcoded è considerato un errore, ora è contrassegnato con una linea ondulata rossa nell'editor di layout:

![Layout tramite la configurazione di diagnostica](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Per rendere effettive le nuove modifiche apportate ai nuovi file di configurazione, è necessario riaprire tutti i file di layout attualmente aperti.
>

## <a name="troubleshooting"></a>Risoluzione dei problemi

Ecco alcuni possibili problemi comuni.

- Assicurarsi che non vi siano errori di formato XML.
- L'azione di compilazione è impostata correttamente su **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problemi noti

- Il riquadro degli errori non viene popolato fino a quando il file non viene modificato la prima volta.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli Lint Android](http://tools.android.com/tips/lint-checks)
- [Migliorare il codice con controlli lint](https://developer.android.com/studio/write/lint)

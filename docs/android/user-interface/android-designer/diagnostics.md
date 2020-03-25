---
title: Diagnostica layout Android
description: Illustra la diagnostica del layout Android e come iniziare
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 03/24/2020
ms.openlocfilehash: 5c29a1a80d8c1f599f0bbc750d22d8334ddb3494
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247741"
---
# <a name="android-layout-diagnostics"></a>Diagnostica layout Android

La diagnostica del layout Android è progettata per contribuire a migliorare la qualità dei file di layout Android evidenziando i problemi di qualità comuni e ottimizzazioni utili. Questa funzionalità è disponibile sia per Visual Studio 16.5 che per Visual Studio per Mac 8.5 +.

Viene fornito un set predefinito di analizzatori per un'ampia gamma di problemi, ciascuno dei quali può essere personalizzato per soddisfare le esigenze specifiche di un progetto. Gli analizzatori sono vagamente basati sul sistema di lanugine Android.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="enable-android-layout-diagnostics-on-visual-studio-2019"></a>Abilitare la diagnostica del layout Android in Visual Studio 2019

Verificare che l'impostazione di diagnostica del layout, **Abilita diagnostica layout**, sia abilitata. Per accedere a questa pagina di opzioni, scegliere **strumenti** > **Opzioni**e quindi scegliere **editor di testo** > **Android XML** > **Advanced**:

![Finestra di dialogo Opzioni che Mostra come abilitare l'opzione di diagnostica](diagnostics-images/AndroidDiagnosticsEnableOption.png)

Una volta abilitata, l'editor di layout Android visualizzerà problemi:

![Diagnostica Android abilitata in Visual Studio 2019](diagnostics-images/AndroidDiagnosticsEnabled.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

## <a name="enable-android-layout-diagnostics-on-visual-studio-for-mac"></a>Abilitare la diagnostica del layout Android in Visual Studio per Mac

Verificare che l'impostazione di diagnostica del layout, **Abilita diagnostica layout**, sia abilitata. Per accedere a questa pagina di opzioni, scegliere **Visual Studio** > **Preferenze...** e quindi scegliere **editor di testo** > **Android XML**:

![Finestra di dialogo delle preferenze che Mostra come abilitare l'opzione di diagnostica](diagnostics-images/AndroidDiagnosticsEnableOptionVSmac.png)

Una volta abilitata, l'editor di layout Android visualizzerà problemi:

![Diagnostica Android abilitata in Visual Studio per Mac](diagnostics-images/AndroidDiagnosticsEnabledVSmac.png)

-----

## <a name="features"></a>Funzionalità

Le sezioni seguenti descrivono le funzionalità disponibili nella diagnostica del layout Android.

### <a name="analyzers"></a>Analizzatori

Gli analizzatori vengono utilizzati per consentire il rilevamento dei problemi nei file di layout. Alcuni sono i modi per ridurre i valori hardcoded, migliorare le prestazioni e contrassegnare gli errori.

### <a name="diagnostic-configuration"></a>Configurazione diagnostica

Gli analizzatori possono essere configurati utilizzando un file XML, consentendo di modificare il livello di gravità predefinito, ignorare determinati file e passare le variabili.

Se si dispone di un set di configurazioni da condividere tra più app Android, è possibile usare un file di base. Per usare questa funzionalità, creare un nuovo file di configurazione e aggiungere `-baseline` al nome del file. Verranno applicate prima le configurazioni di base e quindi i file di configurazione rimanenti.

> [!TIP]
> Questo può essere utile se si vuole ignorare un set di problemi in un'app Android nuova o esistente.

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
> Attualmente le uniche variabili sono `MAX_VIEW_COUNT` (valore predefinito: 80) e `MAX_DEPTH` (valore predefinito: 10) rispettivamente per `TooManyViews` e `TooDeepLayout`.

I livelli di gravità sono:

- Suggerimento
- Info
- Avviso
- Errore
- Ignora

### <a name="add-a-configuration-file"></a>Aggiungere un file di configurazione

Creare un nuovo file XML nella radice di un progetto di app Android. Il nome del file non è importante, ma in questo esempio viene usato `AndroidLayoutDiagnostics.xml`:

![Aggiungi nuovo elemento](diagnostics-images/AndroidDiagnosticsNewFileDialog.png)

Una volta aggiunto il nuovo file XML, questo verrà visualizzato nell'albero del progetto di app Android:

![Albero del progetto di app Android](diagnostics-images/AndroidDiagnosticsFileAddToTree.png)

Verificare che l'azione di compilazione sia impostata su **AndroidResourceAnalysisConfig** nel pannello Proprietà.
Il modo più semplice per estrarre il pannello delle proprietà per il nuovo file è fare clic con il pulsante destro del mouse sul file e scegliere Proprietà. Una volta visualizzato il pannello Proprietà, è necessario modificare l' **azione di compilazione** in **AndroidResourceAnalysisConfig**:

![Imposta l'azione di compilazione nelle proprietà degli elementi](diagnostics-images/AndroidDiagnosticsSetBuildAction.png)

Ora che si dispone di un file XML vuoto, è necessario aggiungere il `<configuration>` elemento radice. A questo punto, è possibile modificare il comportamento predefinito di eventuali problemi supportati.
Se si desidera assicurarsi che le stringhe hardcoded vengano considerate come errori, aggiungere:

```xml
<issue="HardcodedText" severity="error">
</issue>
```

![File di configurazione della diagnostica](diagnostics-images/AndroidDiagnosticsConfigurationFileExample.png)

Ora che il testo hardcoded viene considerato un errore, ora è contrassegnato con una zigzag rossa nell'editor di layout:

![Layout con la configurazione di diagnostica](diagnostics-images/AndroidDiagnosticsUsingConfiguration.png)

> [!NOTE]
> Per rendere effettive le modifiche apportate a un nuovo file di configurazione, è necessario riaprire tutti i file di layout attualmente aperti.
>

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito sono riportati alcuni dei possibili problemi comuni.

- Verificare che non siano presenti errori di formato XML.
- L'azione di compilazione è impostata correttamente su **AndroidResourceAnalysisConfig**.

## <a name="known-issues"></a>Problemi noti

- Il riquadro errori non viene popolato finché il file non viene modificato la prima volta.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli pelucchi Android](http://tools.android.com/tips/lint-checks)
- [Migliorare il codice con i controlli di pelucchi](https://developer.android.com/studio/write/lint)

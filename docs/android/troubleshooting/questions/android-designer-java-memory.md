---
title: Modifica dei parametri di memoria Java per Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 9c9b9f5a205a2eef7db9f27e8d09b10ce65a4318
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027052"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Modifica dei parametri di memoria Java per Android Designer

I parametri di memoria predefiniti `java` utilizzati all'avvio del processo per la finestra di progettazione Android potrebbero essere incompatibili con alcune configurazioni di sistema.

A partire da Xamarin Studio 5.7.2.7 (e versioni successive, Visual Studio per Mac) e Visual Studio Tools per Xamarin 3.9.344, queste impostazioni possono essere personalizzate in base al progetto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nuove proprietà della finestra di progettazione Android e le opzioni Java corrispondenti

I seguenti nomi di proprietà corrispondono all'opzione della [riga di comando](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) java indicata

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Aprire la soluzione in Visual Studio.

2. Selezionare ogni progetto Android uno per uno in Esplora soluzioni e fare clic su [Mostra tutti i file](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) due volte in ogni progetto. È possibile ignorare i progetti `.axml` che non contengono file di layout. Questo passaggio garantisce che `.csproj.user` ogni directory di progetto contenga un file.

3. Uscire da Visual Studio.

4. Individuare `.csproj.user` il file per ognuno dei progetti dal passaggio 2.

5. Modificare `.csproj.user` ogni file in un editor di testo.

6. Aggiungere una o tutte le nuove proprietà `<PropertyGroup>` di memoria della finestra di progettazione Android all'interno di un elemento. È possibile utilizzare `<PropertyGroup>` uno esistente o crearne uno nuovo. Ecco un file `.csproj.user` di esempio completo che include tutti e 3 gli attributi impostati sui valori predefiniti:Here's a complete example file that includes all 3 attributes set to their default values:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7. Salvare e chiudere tutti `.csproj.user` i file aggiornati.

8. Riavviare Visual Studio e riaprire la soluzione.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire la soluzione in Visual Studio per Mac `.userprefs` per assicurarsi che la directory della soluzione contenga un file.

2. Uscire da Visual Studio per Mac.

3. Individuare `.userprefs` il file nella directory della soluzione.

4. Modificare `.userprefs` il file in un editor di testo.

5. Individuare l'elemento XML esistente con il formato seguente. L'ultima parte di questo nome di elemento corrisponderà al nome del progetto: "AndroidApplication1" in questo esempio:The last part of this element name will match the name of your project: "AndroidApplication1" in this example:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. Se `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` l'elemento non esiste, crearlo `<Properties>` in un punto qualsiasi all'interno dell'elemento che lo contiene. Assicurarsi di sostituire "AndroidApplication1" con il nome del progetto.

7. Aggiungere una o tutte le nuove proprietà di memoria della finestra di progettazione Android come attributi nell'elemento. Ecco un file `.userprefs` di esempio completo che include tutti e 3 gli attributi impostati sui valori predefiniti:Here's a complete example file that includes all 3 attributes set to their default values:

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8. Ripetere i passaggi da 5 a 7 per `.axml` ogni progetto Android nella soluzione che contiene i file di layout. (Ovvero, aggiungere `<MonoDevelop.Ide.ItemProperties.ProjectName>` un elemento per ogni progetto.)

9. Salvare e chiudere il file `.userprefs`.

10. Riavviare Visual Studio per Mac e riaprire la soluzione.

-----

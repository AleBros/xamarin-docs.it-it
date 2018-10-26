---
title: Come è possibile copiare i file di output IPA nella cartella di rilascio TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 74e2f2219dcb0908edce7f109844932639038b25
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113040"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Come è possibile copiare i file di output IPA nella cartella di rilascio TFS?

Aprire il `.csproj` file per il progetto di app iOS in un editor di testo e quindi aggiungere le righe seguenti alla fine (immediatamente prima della chiusura `</Project>` tag):

```xml
<PropertyGroup>
    <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
    </CreateIpaDependsOn>
</PropertyGroup>

<Target Name="CopyIpa"
    Condition="'$(OutputType)' == 'Exe'
        And '$(ComputedPlatform)' == 'iPhone'
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Note

- Si tratta della stessa tecnica generale descritte sul [è possibile modificare il percorso di output del file IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). I due punti importanti devono impostate `$(TF_BUILD_BINARIESDIRECTORY)` come cartella di destinazione e aggiungere quindi una condizione aggiuntiva `CopyIpa` verrà eseguito solo per le compilazioni di TFS.

- Per una descrizione delle `TF_BUILD_BINARIESDIRECTORY` visualizzare [variabili di compilazione predefinita](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Riferimenti aggiuntivi

- [Documentazione sull'installazione di TFS per l'uso con Xamarin](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Attività di generazione di DevOps di Azure: xamarin. Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Attività di generazione di DevOps di Azure: xamarin. IOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Passaggi successivi

Questo documento illustra il comportamento corrente a partire da Xamarin 3.11.666 per Visual Studio e xamarin. IOS 8.10.3 nel Mac build host. Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario.

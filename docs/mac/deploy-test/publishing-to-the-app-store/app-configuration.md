---
title: Configurazione di app Mac
description: La guida descrive in dettaglio come configurare un'app Xamarin.Mac per la pubblicazione.
ms.topic: article
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: 0bc64d0b03aa4f80b19ea098904dc1e2155313f6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="mac-app-configuration"></a>Configurazione di app Mac

_La guida descrive in dettaglio come configurare un'app Xamarin.Mac per la pubblicazione._


## <a name="mac-app-configuration"></a>Configurazione di app Mac

Fare clic con il pulsante destro del mouse sul progetto dell'applicazione Mac in Visual Studio per Mac e scegliere **Opzioni**.


### <a name="application-settings"></a>Impostazioni dell'applicazione

Per modificare le impostazioni di un'applicazione Xamarin.Mac, fare doppio clic sul file **Info.plist** nel **riquadro della soluzione**:

![Selezione del file Info.plist](app-configuration-images/config04.png "Selezione del file Info.plist")

In questo modo verranno visualizzate le opzioni disponibili per l'app:

 [![Modifica del file Info.plist](app-configuration-images/config01.png "Modifica del file Info.plist")](app-configuration-images/config01-large.png#lightbox)

Per eseguire le applicazioni Mac create con Xamarin.Mac sono necessari i requisiti di sistema seguenti:

- Computer Mac che esegue Mac OS X 10.7 o versione successiva.


### <a name="signing-settings"></a>Impostazioni della firma

La sezione **Firma Mac** della finestra di dialogo **Opzioni progetto** consente allo sviluppatore di firmare un'app Xamarin.Mac per il test, il rilascio o il rilascio automatico tramite l'App Store Apple:

[![Editor Firma Mac](app-configuration-images/config02.png "Finestra Firma Mac")](app-configuration-images/config02-large.png#lightbox)

Da qui selezionare Identità, Profilo di provisioning e tutti i diritti personalizzati usati per firmare l'app quando viene compilata. Lo sviluppatore può facoltativamente firmare il programma di installazione usato per installare l'app in un altro Mac.


### <a name="build-settings"></a>Impostazioni di compilazione

La sezione **Compilazione Mac** della finestra di dialogo **Opzioni progetto** consente allo sviluppatore di selezionare l'architettura per un'app Xamarin.Mac, di controllare quale versione di macOS verrà supportata dall'app e, facoltativamente, di creare un pacchetto di installazione quando l'app è stata compilata correttamente:

 [![Modifica delle impostazioni di compilazione](app-configuration-images/config03.png "Modifica delle impostazioni di compilazione")](app-configuration-images/config03-large.png#lightbox)


## <a name="related-links"></a>Collegamenti correlati

- [Installazione](/visualstudio/mac/installation/)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/resources/developer-id/)

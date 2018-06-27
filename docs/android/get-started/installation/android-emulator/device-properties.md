---
title: Modifica delle proprietà del dispositivo virtuale Android
description: Questo articolo illustra come usare Android Device Manager per modificare le proprietà del profilo di un dispositivo virtuale Android.
ms.prod: xamarin
ms.assetid: 3E33C136-8042-4184-A40C-3200D8CD99CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: 75ac85c67825e5db1b663d00f10eee6d093bfc1f
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733629"
---
# <a name="editing-android-virtual-device-properties"></a>Modifica delle proprietà del dispositivo virtuale Android

_Questo articolo illustra come usare Android Device Manager per modificare le proprietà del profilo di un dispositivo virtuale Android._


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**Android Device Manager** supporta la modifica delle singole proprietà del profilo di un dispositivo virtuale Android. Le schermate **New Device** (Nuovo dispositivo) e **Device Editor** (Editor dispositivi) elencano le proprietà del dispositivo virtuale nella prima colonna e i valori corrispondenti di ogni proprietà nella seconda colonna (come in questo esempio): 

[![Schermata New Device (Nuovo dispositivo) di esempio](device-properties-images/win/01-new-device-editor-sml.png)](device-properties-images/win/01-new-device-editor.png#lightbox)

Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà. È possibile modificare le *proprietà del profilo hardware* e le *proprietà del dispositivo virtuale Android*. Le proprietà del profilo hardware (ad esempio `hw.ramSize` e `hw.accelerometer`) descrivono le caratteristiche fisiche del dispositivo di emulazione. Tali caratteristiche includono le dimensioni dello schermo, la quantità di RAM disponibile e la presenza o meno di un accelerometro. Le proprietà AVD specificano il funzionamento del dispositivo AVD quando è in esecuzione. Ad esempio è possibile configurare le proprietà AVD per specificare come il dispositivo AVD usa la scheda grafica del computer di sviluppo per il rendering.

È possibile modificare le proprietà in base alle linee guida seguenti:

-   Per modificare una proprietà booleana, fare clic sul segno di spunta a destra della proprietà:

    ![Modifica di una proprietà booleana](device-properties-images/win/02-boolean-value.png)

-   Per modificare una proprietà *enum* (di enumerazione), fare clic sulla freccia giù a destra della proprietà e scegliere un nuovo valore.

    ![Modifica di una proprietà enum](device-properties-images/win/04-enum-value.png)

-   Per modificare una proprietà di tipo string o integer, fare doppio clic sull'impostazione corrente della proprietà nella colonna del valore e immettere un nuovo valore.

    ![Proprietà integer](device-properties-images/win/03-integer-value.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

**Android Device Manager** supporta la modifica delle singole proprietà del profilo di un dispositivo virtuale Android. Le schermate **New Device** (Nuovo dispositivo) e **Device Editor** (Editor dispositivi) elencano le proprietà del dispositivo virtuale nella prima colonna e i valori corrispondenti di ogni proprietà nella seconda colonna (come in questo esempio): 

[![Schermata New Device (Nuovo dispositivo) di esempio](device-properties-images/mac/01-new-device-editor-sml.png)](device-properties-images/mac/01-new-device-editor.png#lightbox)

Quando si seleziona una proprietà, sul lato destro viene visualizzata una descrizione dettagliata di tale proprietà. È possibile modificare le *proprietà del profilo hardware* e le *proprietà del dispositivo virtuale Android*. Le proprietà del profilo hardware (ad esempio `hw.ramSize` e `hw.accelerometer`) descrivono le caratteristiche fisiche del dispositivo di emulazione. Tali caratteristiche includono le dimensioni dello schermo, la quantità di RAM disponibile e la presenza o meno di un accelerometro. Le proprietà AVD specificano il funzionamento del dispositivo AVD quando è in esecuzione. Ad esempio è possibile configurare le proprietà AVD per specificare come il dispositivo AVD usa la scheda grafica del computer di sviluppo per il rendering.

È possibile modificare le proprietà in base alle linee guida seguenti:

-   Per modificare una proprietà booleana, fare clic sul segno di spunta a destra della proprietà:

    ![Modifica di una proprietà booleana](device-properties-images/mac/02-boolean-value.png)

-   Per modificare una proprietà *enum* (di enumerazione), fare clic sul menu a discesa a destra della proprietà e scegliere un nuovo valore.

    ![Modifica di una proprietà enum](device-properties-images/mac/04-enum-value.png)

-   Per modificare una proprietà di tipo string o integer, fare doppio clic sull'impostazione corrente della proprietà nella colonna del valore e immettere un nuovo valore.

    ![Proprietà integer](device-properties-images/mac/03-integer-value.png)

-----

La tabella seguente spiega in dettaglio le proprietà elencate nelle schermate **New Device** (Nuovo dispositivo) e **Device Editor** (Editor dispositivi):

[!include[](~/android/includes/emulator-properties.md)]

Per altre informazioni su queste proprietà, vedere [Hardware Profile Properties](https://developer.android.com/studio/run/managing-avds.html#hpproperties) (Proprietà del profilo hardware).


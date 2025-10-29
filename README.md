# Laboratorio-4-procesamiento-de-señales
Entrega 4 laboratorio de procesamiento 

# Parte B  
En esta parte del laboratorio se realizó la adquisición de una señal electromiográfica (EMG) real en la zona del antebrazo, con el fin de analizar la actividad eléctrica producida por la contracción muscular y su relación con la fatiga. Para ello, se ubicaron dos electrodos de superficie en el antebrazo y un electrodo de referencia en el codo, se aseguro que la piel estuviera limpia. La señal fue registrada mediante un sistema de adquisición en phyton que permitia graficar y a su vez guardar la señal, se uso una frecuencia de muestreo de 5 kHz, valor adecuado para este tipo de registros según lo reportado en la literatura, ya que permite captar de forma precisa el rango de frecuencias característico de las señales EMG de superficie (generalmente entre 20 Hz y 450 Hz) (Wegrzyk et al., 2020).  

Esta configuración permitió observar las variaciones en la frecuencia media y mediana a lo largo de contracciones sucesivas, las cuales reflejan los cambios fisiológicos asociados a la fatiga muscular, como la disminución la variación en la conducción eléctrica. El análisis de estos parámetros, junto con el uso de filtros adecuados, facilita la interpretación de la señal en el dominio de la frecuencia y su vinculación con la actividad muscular real.  


# Parte C

En esta parte del laboratorio se realizó el análisis espectral de la señal EMG real obtenida del grupo muscular del antebrazo, con el fin de estudiar la evolución del contenido de frecuencia durante una serie de 53 contracciones musculares.
Para esto se aplicó la Transformada Rápida de Fourier (FFT), una herramienta que permite transformar la señal del dominio del tiempo al dominio de la frecuencia, asi facilitando la observación de los cambios energéticos asociados a la fatiga muscular.

### a. Aplicación de la FFT

Se aplicó la Transformada Rápida de Fourier (FFT) a cada una de las contracciones previamente segmentadas.

    from scipy.fft import rfft, rfftfreq
    import numpy as np

    fft_list = []
    freq_list = []

    for i in range(len(contracciones)):
    y = contracciones[i] - np.mean(contracciones[i])   
    N = len(y)
    Y = np.abs(rfft(y)) / N
    f = rfftfreq(N, 1/fs)
    fft_list.append(Y)
    freq_list.append(f)

    print("FFT calculada para", len(fft_list), "contracciones.")

En esta etapa se aplicó la Transformada Rápida de Fourier (FFT) a cada una de las contracciones de la señal EMG del antebrazo.
Este procedimiento permitió obtener la representación de la señal en el dominio de la frecuencia, donde se identifican las componentes armónicas de la actividad muscular.
Los resultados obtenidos a partir de esta transformación se presentan en la siguiente sección (parte b), mediante los espectros de amplitud correspondientes a diferentes contracciones.

### b. Espectro de amplitud

    import matplotlib.pyplot as plt

    i = 9  
    plt.figure(figsize=(10,5))
    plt.plot(freq_list[i], fft_list[i], color='purple', linewidth=1.8)
    plt.xlim(0, 120)   
    plt.ylim(0, max(fft_list[i])*1.1)
    plt.xlabel("Frecuencia [Hz]")
    plt.ylabel("Magnitud")
    plt.title(f"Espectro de amplitud (sin DC) - Contracción {i+1}")
    plt.grid(True)
    plt.show()

En Google Colab se ejecutó una celda que permite graficar el espectro de amplitud obtenido mediante la Transformada Rápida de Fourier (FFT).
En este caso se seleccionó la contracción número 10 para observar cómo se distribuye la energía de la señal EMG en el rango de 0 a 120 Hz.
Esta visualización facilita identificar las componentes de frecuencia predominantes de la contracción y sirve como base para el análisis comparativo posterior.

<img width="876" height="471" alt="image" src="https://github.com/user-attachments/assets/9ba2796b-1d54-495b-a217-e4e557f14dd4" />


En la Figura se muestra el espectro de amplitud correspondiente a la contracción número 10.
Se observa que la mayor concentración de energía se encuentra entre 30 Hz y 60 Hz, con un pico principal cercano a los 45 Hz.
Este comportamiento es característico de una señal EMG de contracción muscular normal, donde las fibras se activan de forma estable y sin indicios de fatiga.

La disminución de la magnitud a medida que aumenta la frecuencia indica que las componentes de alta frecuencia tienen menor presencia, lo cual es esperable en una señal biológica.
En general, la gráfica confirma que la señal procesada mediante la FFT presenta un contenido espectral coherente con la actividad muscular real.

### c. Comparación entre las primeras y las últimas contracciones

En esta parte se ejecutó en Google Colab un bloque de código que compara los espectros de amplitud obtenidos mediante la Transformada Rápida de Fourier (FFT) para la primera y la última contracción de la señal EMG.


    plt.figure(figsize=(14,6))

    # Primera contracción
    plt.subplot(1,2,1)
    plt.plot(freq_list[0], fft_list[0], color='blue', linewidth=1.8)
    plt.xlim(0, 120)
    plt.ylim(0, max(fft_list[0])*1.1)
    plt.title("Primera contracción (sin DC)")
    plt.xlabel("Frecuencia [Hz]")
    plt.ylabel("Magnitud")
    plt.grid(True)

    # Última contracción
    plt.subplot(1,2,2)
    plt.plot(freq_list[-1], fft_list[-1], color='red', linewidth=1.8)
    plt.xlim(0, 120)
    plt.ylim(0, max(fft_list[-1])*1.1)
    plt.title("Última contracción (sin DC)")
    plt.xlabel("Frecuencia [Hz]")
    plt.ylabel("Magnitud")
    plt.grid(True)

    plt.tight_layout()
    plt.show()


El procedimiento organiza dos gráficas lado a lado, donde se representa la magnitud de las componentes frecuenciales en el rango de 0 a 120 Hz.
Este código permite visualizar los cambios en el contenido de frecuencia entre el inicio y el final del esfuerzo muscular, facilitando la identificación de posibles indicios de fatiga al observar la disminución de la energía en las frecuencias más altas.

<img width="1390" height="590" alt="image" src="https://github.com/user-attachments/assets/b1751101-6ea8-4ec8-8241-bbfa693019d1" />

Se observa que la primera contracción posee una mayor energía y un pico de frecuencia dominante alrededor de los 46 Hz, mientras que en la última contracción la energía total disminuye y el pico se desplaza hacia frecuencias más bajas (38 Hz).

Esta variación indica una reducción del contenido de alta frecuencia, fenómeno directamente asociado con la aparición de fatiga muscular.
A medida que el músculo se fatiga, la velocidad de conducción de las fibras se reduce y las unidades motoras se activan de manera más lenta, lo cual se refleja en un desplazamiento del espectro hacia frecuencias menores.

### d. Reducción del contenido de alta frecuencia

A medida que avanzaron las contracciones, se observó una disminución progresiva del contenido de alta frecuencia en los espectros obtenidos mediante la FFT.
Este efecto está directamente asociado con la fatiga muscular, ya que durante el esfuerzo sostenido disminuye la velocidad de conducción de las fibras musculares y la activación de las unidades motoras rápidas.
Como resultado, la energía del espectro se concentra en frecuencias más bajas y la magnitud general de la señal disminuye.
Este comportamiento confirma que la pérdida de componentes de alta frecuencia es un indicador confiable del inicio de la fatiga en el músculo analizado.

### e. Desplazamiento del pico espectral

En Google Colab se ejecutó un bloque de código encargado de calcular la frecuencia pico de cada contracción individual y analizar su variación a lo largo del tiempo.

    peak_freqs = []
    for i in range(len(contracciones)):
    idx = np.argmax(fft_list[i])
    peak_freqs.append(freq_list[i][idx])

    plt.figure(figsize=(8,4))
    plt.plot(range(1, len(contracciones)+1), peak_freqs, 'o-', color='purple')
    plt.xlabel('Número de contracción')
    plt.ylabel('Frecuencia pico [Hz]')
    plt.title('Desplazamiento del pico espectral (fatiga muscular)')
    plt.grid(True)
    plt.show()

    print("Primera contracción:", round(peak_freqs[0],2), "Hz")
    print("Última contracción:", round(peak_freqs[-1],2), "Hz")
    print("Desplazamiento:", round(peak_freqs[0] - peak_freqs[-1],2), "Hz")


Para ello, se identificó el índice de máxima magnitud dentro del espectro de amplitud (FFT) de cada contracción, almacenando el valor correspondiente de frecuencia en una lista.
Posteriormente, se generó una gráfica donde se representa la frecuencia pico en función del número de contracción, permitiendo visualizar el desplazamiento progresivo hacia frecuencias más bajas.
Este comportamiento es característico del fenómeno de fatiga muscular, ya que a medida que el músculo se fatiga, disminuye la velocidad de conducción de las fibras y el contenido de alta frecuencia se reduce.
Finalmente, el código imprime la frecuencia pico de la primera y última contracción, junto con el desplazamiento total observado entre ambas.

<img width="686" height="393" alt="image" src="https://github.com/user-attachments/assets/49cc3ffa-66a6-4488-8c3a-1be02cc4bfc0" />

En la gráfica se observa la variación de la frecuencia pico de la señal EMG a lo largo de las 53 contracciones musculares registradas.
Cada punto representa la frecuencia dominante obtenida mediante la Transformada Rápida de Fourier (FFT) para una contracción específica.

Al inicio del registro, la frecuencia pico fue de aproximadamente 45.88 Hz, mientras que en la última contracción disminuyó hasta 37.70 Hz, generando un desplazamiento total de 8.18 Hz hacia frecuencias más bajas.

Este comportamiento confirma la presencia de fatiga muscular progresiva en el grupo muscular analizado.
En conjunto, esta gráfica evidencia de forma cuantitativa cómo la energía de la señal EMG se desplaza hacia frecuencias más bajas conforme aumenta el número de contracciones, validando la interpretación de la fatiga a partir del análisis espectral.

### f. Conclusiones sobre el uso del análisis espectral como herramienta diagnóstica en electromiografía

El análisis espectral resultó ser una herramienta muy útil para estudiar el comportamiento de la señal electromiográfica.
A través de la Transformada Rápida de Fourier (FFT) fue posible observar cómo cambia el contenido de frecuencia del músculo durante las contracciones y cómo estos cambios reflejan la presencia de fatiga muscular.
Este tipo de análisis permite identificar con claridad la disminución de las componentes de alta frecuencia y el desplazamiento del pico espectral, lo que aporta información importante sobre el estado fisiológico del músculo.
En general, el uso del análisis espectral facilita la interpretación de las señales EMG y puede servir como apoyo en el diagnóstico y seguimiento de la actividad muscular tanto en estudios clínicos como experimentales.


# Referencias
- Wegrzyk, J., Fouré, A., Vilmen, C., Maffiuletti, N. A., Mattei, J. P., Place, N., & Bendahan, D. (2020). Fatigue-related adaptations in muscle activity and metabolism during high-intensity intermittent exercise: A combined electromyographic and ^31P-MRS study. Neurophysiologie Clinique/Clinical Neurophysiology, 50(4), 301–312. https://doi.org/10.1016/j.neucli.2019.11.002
- 


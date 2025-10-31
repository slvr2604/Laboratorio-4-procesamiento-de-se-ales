# Laboratorio-4-procesamiento-de-señales

En el presente laboratorio se realizó la captura de señales electromiográficas con el propósito de observar cómo cambia la actitud muscular al realizar contracciones hasta finalizar con el fallo muscular. El EMG permitirá observar como se activa el musculo y como es el funcionamiento fisiológico de este. La práctica se desarrollo tanto con señales simuladas como con señales medidas con un sensor AD8232 que fue colocado en el antebrazo, a estas señales se les realizó el debido procesamiento y análisis para entender las características relacionadas con su frecuencia y evolución con el aumento de la fatiga muscular. Adicionalmente, se le realizó un análisis espectral a través de la Transformada Rápida de Fourier (FFT) que permitió observar los cambios de frecuencia en las contracciones, lo que permite analizar las diferencias entre las primeras y las últimas contracciones. Esto nos permitió comprender como las señales EMG nos muestran el comportamiento fisiológico del músculo cuando se realiza un esfuerzo y el análisis en el dominio de la frecuencia nos permite estudiar la fatiga y el rendimiento muscular.

# Diagrama de flujo.
Como primera etapa, se elaboró un diagrama de flujo que permitió estructurar tanto la programación como el procedimiento general del análisis. Esta herramienta facilitó la organización lógica de las tareas, definiendo el orden de ejecución, las funciones necesarias y los criterios de procesamiento que se aplicarían a las señales vocales.

*Insertar diagrama de flujo*
# Parte A.
La parte A se centró inicialmente en la adquisición y procesamiento de una señal de contracciones musculares simuladas, se adquirieron 5 contracciones a las cuales se les realizó el siguiente procedimiento:


Tras la adquisición y carga de la señal en Google Colab, se realizó un centrado a la señal, y adicionalmente una conversión a milivoltios, lo que nos permitió graficarla completa, con la siguiente programación.

```
t = data[:, 0]
senal = data[:, 1]

# Eliminamos el offset (centrar la señal)
senal_centrada = senal - np.mean(senal)

# Convertimos a milivoltios
senal_mV = senal_centrada * 1000

# Graficamos la señal completa.
plt.figure(figsize=(12, 5))
plt.plot(t, senal_mV, color='navy', linewidth=1)
plt.xlabel('Tiempo [s]', fontsize=12)
plt.ylabel('Amplitud [mV]', fontsize=12)
plt.title('Señal EMG adquirida con la DAQ (centrada)', fontsize=14)
plt.grid(True, linestyle='--', alpha=0.6)
plt.ticklabel_format(axis='y', style='plain')
plt.tight_layout()
plt.show()
```
Finalmente la gráfica de la señal se observará de la siguiente forma:
<img width="1189" height="490" alt="image" src="https://github.com/user-attachments/assets/31a397dc-db44-4332-b1e9-9adb0bab4fc0" />
Donde se observa una contracción por segundo. Y procedemos con el procesamiento de la señal, donde usaremos librerías como `numpy`, `matplotlib.pyplot` y `pandas`. 
Se comienza segmentando la señal en cinco partes, la cual se puede hacer de la siguiente forma en python:

```
segmentos_emg = np.array_split(amplitud_emg_mV, 5)
segmentos_tiempo = np.array_split(tiempo_emg, 5)
```

Como observamos, la función `np.array_split()` segmenta el arreglo en 5 partes tanto a la amplitud como al tiempo, lo que nos permitirá analizar por separado las 5 contracciones simuladas.

Y estas gráficamente se verán de la siguiente forma:
<img width="1189" height="790" alt="image" src="https://github.com/user-attachments/assets/1e7c0735-d1e1-4e31-828c-7a9cca3e1f17" />
Se observan las cinco contracciones musculares simuladas. Cada segmento representa una contracción independiente, lo cual nos permitió analizar el comportamiento de la señal de manera más profunda.
Las contracciones tienen picos de amplitud bien definidos, que corresponden al momento en que se activa el músculo, tras esos picos encontramos periodos de menor actividad, que representa las fases de reposo. Esto nos indica que la segmentación que se realizó fue hecha correctamente y cada segmento contiene una contracción completa.
Adicionalmente, se puede observar que la amplitud varía entre contracciones, teniendo unas más intensas que otras, esto puede deberse por fluctuaciones del generador biológico, también se observa cierto nivel de ruido y oscilaciones pequeñas, algo que puede resultar normal en registros EMG.
A pesar de esto, la forma de las contracciones se mantiene similar, lo que confirma que la señal emulada tiene un comportamiento estable y representativo.
Tras la segmentación, podremos iniciar con el cálculo de la frecuencia media y la frecuecia mediana de la señal. Que se realiza de la siguiente forma: 

```
frecuencia_media_Hz = []
frecuencia_mediana_Hz = []

for indice, contraccion in enumerate(segmentos_emg):
    frecuencias, densidad_potencia = welch(contraccion, fs=frecuencia_muestreo_Hz, nperseg=1024)

    # Calculamos frecuencia media (promedio ponderado)
    media = np.sum(frecuencias * densidad_potencia) / np.sum(densidad_potencia)
    frecuencia_media_Hz.append(media)

    # Calculamos frecuencia mediana (mitad del área espectral)
    mediana = frecuencias[np.where(np.cumsum(densidad_potencia) >= np.sum(densidad_potencia)/2)[0][0]]
    frecuencia_mediana_Hz.append(mediana)
```
Comenzamos inicializando dos listas vacías `frecuencia_media_Hz` y `frecuencia_mediana_Hz` que se encargarán de almacenar los resultados de cada contracción. Luego, mediante un ciclo `for`, se recorrieron los cinco segmentos de la señal `segmentos_emg`, correspondientes a las contracciones simuladas.

Dentro de este ciclo, se aplicó la función `welch()` de la librería `scipy.signal`, que calcula la densidad espectral de potencia (PSD) de la señal utilizando el método de Welch. Esta función nos devuelve dos arreglos: `frecuencias`, que contiene los valores de frecuencia (en Hz) y `densidad_potencia`, que muestra la potencia o energía asociada a cada frecuencia.

Con estos datos, se calculó la frecuencia media como un promedio ponderado entre las frecuencias y su potencia espectral.
Luego, se calculó la frecuencia mediana tomando en cuenta la frecuencia en la que la suma acumulativa de la potencia alcanza el 50% del total del espectro. Este punto hace que se divida el área espectral en dos partes iguales, mostrando cómo se distribuye la energía de la señal.
Una vez analizado el código, nos arroja la tabla de frecuencia media y frecuencia mediana y además la gráfica de estas.

<img width="623" height="232" alt="image" src="https://github.com/user-attachments/assets/0ee8a128-20f9-4518-aea7-a231782ddfe7" />


<img width="686" height="394" alt="image" src="https://github.com/user-attachments/assets/78d0f093-1b61-4944-8e7f-37b57395b13b" />

En la tabla se observan los valores obtenidos de frecuencia media y frecuencia mediana para las cinco contracciones musculares simuladas. Se puede observar que ambas frecuencias muestran una tendencia ascendente a medida que avanzan las contracciones. La frecuencia media inicia con un valor de aproximadamente 36.62 Hz en la primera contracción y aumenta hasta 67.41 Hz en la quinta. Esto indica que, conforme se repiten las contracciones, el contenido energético de la señal tiende a desplazarse hacia frecuencias más altas, lo que nos sugiere una mayor activación o intensidad muscular en las contracciones finales. A su vez, la frecuencia mediana pasa de 5.86 Hz en las dos primeras contracciones a 14.65 Hz en la última. Aunque los valores son menores que los de la frecuencia media, el incremento también refleja un desplazamiento del espectro hacia frecuencias más altas. Este comportamiento nos indica que es coherente con el hecho de que fue medida con un generador biológico, el cual simula mayor actividad muscular en las contracciones finales. Algo que en una medición real se asocia con la fatiga muscular.


# Parte B  
En esta parte del laboratorio se realizó la adquisición de una señal electromiográfica (EMG) real en la zona del antebrazo, con el fin de analizar la actividad eléctrica producida por la contracción muscular y su relación con la fatiga. Para ello, se ubicaron dos electrodos de superficie en el antebrazo y un electrodo de referencia en el codo, se aseguro que la piel estuviera limpia. La señal fue registrada mediante un sistema de adquisición en phyton que permitia graficar y a su vez guardar la señal, se uso una frecuencia de muestreo de 5 kHz, valor adecuado para este tipo de registros según lo reportado en la literatura, ya que permite captar de forma precisa el rango de frecuencias característico de las señales EMG de superficie (generalmente entre 20 Hz y 450 Hz) (Wegrzyk et al., 2020).  

Esta configuración permitió observar las variaciones en la frecuencia media y mediana a lo largo de contracciones sucesivas, las cuales reflejan los cambios fisiológicos asociados a la fatiga muscular, como la disminución la variación en la conducción eléctrica. El análisis de estos parámetros, junto con el uso de filtros adecuados, facilita la interpretación de la señal en el dominio de la frecuencia y su vinculación con la actividad muscular real.  

### a. Colocar los electrodos sobre el grupo muscular definido por el grupo. 
Se trabajó con el grupo muscular del antebrazo. Para ubicar bien los electrodos, se le pidió a la persona que hiciera una contracción del músculo, así se podía sentir dónde se movía más y decidir el mejor lugar para colocarlos. También se puso un electrodo de referencia en el codo.  
<img width="659" height="661" alt="image" src="https://github.com/user-attachments/assets/39ec9d0d-6254-45b9-9f8c-9b4c58775c80" />

### b. Registrar la señal EMG de un paciente o voluntario sano realizando contracciones repetidas hasta la fatiga (o la falla).  
Se armó un circuito pequeño usando la ST-Link para alimentar el módulo AD8232. Los electrodos del módulo se colocaron en el antebrazo y se conectaron al DAQ para poder capturar la señal. A partir de ahí, se usó un programa en Python que permitía capturar, graficar y guardar la señal en tiempo real.  

Usamos un programa en Python para manejar todo el proceso. Al principio se importaron las bibliotecas necesarias para poder programar y obtener la información desde el DAQ. Estas librerías permitieron establecer la conexión, capturar los datos, graficarlos y guardarlos en tiempo real    

         import nidaqmx
         from nidaqmx.constants import AcquisitionType
         import numpy as np
         import matplotlib.pyplot as plt
         import time  

Luego se ajustaron los parámetros de la señal que se iba a almacenar. Se configuró la frecuencia de muestreo, el canal al que estaba conectado al DAQ, y cuántas muestras se iban a obtener, y se indicó que el buffer iba a acumular datos durante 5 segundos. También se declaró una variable tipo vector para guardar esos datos, y se lleva un registro del tiempo para saber cuándo se inicia la captura.  

         frecuencia_muestreo = 5000       
         canal_daq = "Dev5/ai0"           
         muestras_por_bloque = 500        
         tiempo_buffer = 5                 

         datos_capturados = []        
         inicio = time.time()    

Después se añade un canal analógico de voltaje usando el nombre definido en canal_daq, que corresponde al canal físico del DAQ donde está conectado el módulo. Luego se configura el reloj de muestreo, estableciendo la frecuencia muestreo y el modo de adquisición como continuo, lo que permite capturar datos sin interrupciones. Se ajusta el tamaño del buffer de entrada para que pueda almacenar la cantidad de muestras correspondientes a 5 segundos, calculado como frecuencia_muestreo * tiempo_buffer.

Después, se imprime un mensaje indicando que la adquisición ha comenzado. Dentro del bloque try, se inicia un ciclo infinito que lee bloques de datos del DAQ, con el tamaño definido en muestras_por_bloque, y los agrega a la lista datos_capturados. Finalmente, se calcula el tiempo total de adquisición restando el tiempo inicial al tiempo actual, y se imprime en pantalla.  

    with nidaqmx.Task() as tarea:
       tarea.ai_channels.add_ai_voltage_chan(canal_daq)
       tarea.timing.cfg_samp_clk_timing(
        freq=frecuencia_muestreo,
        sample_mode=AcquisitionType.CONTINUOUS
    )
    tarea.in_stream.input_buf_size = int(frecuencia_muestreo * tiempo_buffer)

    print("Iniciando adquisición continua... (Ctrl + C para finalizar)")
    try:
        while True:
            bloque = tarea.read(number_of_samples_per_channel=muestras_por_bloque)
            datos_capturados.extend(bloque)
    except KeyboardInterrupt:
        print("\nAdquisición interrumpida por el usuario.")
    finally:
        tiempo_total = time.time() - inicio
        print(f"Tiempo total de adquisición: {tiempo_total:.2f} segundos")  

Primero, los datos capturados se convierten en un arreglo de tipo NumPy con np.array(datos_capturados), lo que permite trabajar con ellos de forma más eficiente. Luego se genera un vector de tiempo llamado tiempo, que va desde 0 hasta la duración total de la señal, calculado como el número de muestras dividido por la frecuencia de muestreo. Este vector sirve para asociar cada muestra con su instante correspondiente en segundos; después se grafica la señal de la adquisición en tiempo real.


         datos_capturados = np.array(datos_capturados)
         tiempo = np.arange(0, len(datos_capturados)) / frecuencia_muestreo

         plt.figure(figsize=(10, 5))
         plt.plot(tiempo, datos_capturados, color='steelblue')
         plt.xlabel("Tiempo [s]")
         plt.ylabel("Amplitud [V]")
         plt.title("Registro EMG")
         plt.grid(True)
         plt.show()

Finalmente se define la ruta donde se va a guardar el archivo de texto con los datos capturados, se guarda el arreglo datos_capturados en esa ruta como un archivo .txt  

         ruta_guardado = f"C:/Users/carlo/.spyder-py3/RegistroEMG_{frecuencia_muestreo}.txt"
         np.savetxt(ruta_guardado, datos_capturados)
         print(f"Archivo guardado en: {ruta_guardado}")

La señal obtenida fue esta, se hizo zoom entre el segundo 20-22 para tener una mejor visualización:
<img width="1478" height="353" alt="image" src="https://github.com/user-attachments/assets/2fc59bd6-8728-4286-ada1-cb215cf08474" />

### c. Aplicar un filtro pasa banda (20–450 Hz) para eliminar ruido y artefactos  
Primero se definió la frecuencia de muestreo de la señal EMG de 5kHz. A partir de esto, se generó un vector de tiempo que permite asociar cada muestra con su instante correspondiente, facilitando el análisis de la señal.

Luego se diseñó el filtro pasabanda con el objetivo de conservar únicamente las frecuencias entre 20 Hz y 450 Hz, que son las más representativas de la actividad muscular. Para ello, se normalizaron las frecuencias de corte con respecto a la frecuencia de Nyquist y se utilizó un filtro Butterworth de cuarto orden, conocido por su respuesta suave y estable.  

Posteriormente, se aplicó el filtro a la señal EMG utilizando un método de filtrado bidireccional, lo que evita distorsiones en la forma de onda.  


    frecuencia_muestreo = 5000 
    tiempo = np.arange(len(senal_emg)) / frecuencia_muestreo



    def crear_filtro_pasabanda(frecuencia_baja, frecuencia_alta, frecuencia_muestreo, orden=4):

        frecuencia_nyquist = 0.5 * frecuencia_muestreo
        frecuencia_baja_norm = frecuencia_baja / frecuencia_nyquist
        frecuencia_alta_norm = frecuencia_alta / frecuencia_nyquist
        b, a = butter(orden, [frecuencia_baja_norm, frecuencia_alta_norm], btype='band')
        return b, a

    def aplicar_filtro_pasabanda(senal, frecuencia_baja=20, frecuencia_alta=450, frecuencia_muestreo=5000, orden=4):

        b, a = crear_filtro_pasabanda(frecuencia_baja, frecuencia_alta, frecuencia_muestreo, orden)
        senal_filtrada = filtfilt(b, a, senal)
        return senal_filtrada


    senal_emg_filtrada = aplicar_filtro_pasabanda(senal_emg, 20, 450, frecuencia_muestreo, 4)

Finalmente se graficó con un zoom entre 20-22 segundos para mejorar la visualización:  
    
    plt.subplot(2,1,2)
    plt.plot(tiempo, senal_emg_filtrada, color='purple', linewidth=0.8)
    plt.xlim(20, 22)
    plt.title('Señal EMG filtrada (20–450 Hz)')
    plt.xlabel('Tiempo [s]')
    plt.ylabel('Amplitud [V]')
    plt.grid(True)
    plt.show()

<img width="1482" height="381" alt="image" src="https://github.com/user-attachments/assets/2619775b-672f-46cb-ab9e-c825aec4fe2b" />
Finalmente se graficó la respuesta del filtro pasabanda Butterworth en distintas frecuencias. Primero, se crean los coeficientes del filtro utilizando la función crear_filtro_pasabanda, con un rango de paso entre 20 Hz y 450 Hz y una frecuencia de muestreo de 5000 Hz. Luego, se calcula la respuesta en frecuencia del filtro con la función freqz, que devuelve las frecuencias y la ganancia correspondiente en cada una, finalmente se grafica:  
<img width="883" height="492" alt="image" src="https://github.com/user-attachments/assets/314f52c0-eeb3-499f-858b-126f513d6dc1" />


### d. Dividir la señal en el número de contracciones realizadas.  
El total de contracciones fueron 53 en 75.1 segundos, la persona a la que se le tomó el exámen se le pidió apretar una botella hasta que tuviera fatiga, se graficaron todas las contracciones, pero se pondran las primeras 3 contracciones, una del medio y las tres últimas:  

    fs = 5000    
    duracion_total = 75.1
    n_contracciones = 53


    duracion_c = duracion_total / n_contracciones
    muestras_c = int(duracion_c * fs)

    t = np.arange(len(senal_emg_filtrada)) / fs

    print(f"Duración por contracción: {duracion_c:.2f} s")
    print(f"Muestras por contracción: {muestras_c}")


    for i in range(n_contracciones):
    inicio = i * muestras_c
    fin = inicio + muestras_c
    if fin > len(senal_emg_filtrada):
        fin = len(senal_emg_filtrada)

    t_seg = t[inicio:fin] - t[inicio]  # tiempo local desde 0
    s_seg = senal_emg_filtrada[inicio:fin]

    plt.figure(figsize=(8,3))
    plt.plot(t_seg, s_seg, color='purple', linewidth=1)
    plt.title(f'Contracción {i+1}')
    plt.xlabel('Tiempo [s]')
    plt.ylabel('Amplitud [V]')
    plt.grid(True)
    plt.show()

<img width="702" height="316" alt="image" src="https://github.com/user-attachments/assets/875f8891-945a-4d30-8867-1c911734363e" />
<img width="702" height="316" alt="image" src="https://github.com/user-attachments/assets/6c0fbb0b-35ad-4bd8-96b3-382c1b956801" />
<img width="702" height="316" alt="image" src="https://github.com/user-attachments/assets/4f95fd4b-c698-48f7-b679-674296ce41b8" />
<img width="702" height="316" alt="image" src="https://github.com/user-attachments/assets/4eed88f4-d717-4ffa-83f5-4a95a3975e66" />
<img width="702" height="316" alt="image" src="https://github.com/user-attachments/assets/89a7ddc9-e8b3-43ca-8dbe-caa9a280b972" />
<img width="702" height="316" alt="image" src="https://github.com/user-attachments/assets/ad34ebc9-ce82-4034-9bd1-d20ced6c533b" />
<img width="702" height="316" alt="image" src="https://github.com/user-attachments/assets/94ae1130-2559-4a9d-9efd-1c0a7b30807c" />

### e. Calcular para cada contracción:  
- Frecuencia media: Representa el promedio ponderado de las frecuencias presentes en el espectro, según su potencia.  
- Frecuencia mediana: Es la frecuencia que divide el espectro de potencia en dos partes iguales; es decir, la mitad de la energía total del EMG está por debajo de esa frecuencia, y la otra mitad está por encima.  

        fs = 5000 
        n_contracciones = 53 
        duracion_total = 75.1 
        duracion_c = duracion_total / n_contracciones
        muestras_c = int(duracion_c * fs)

        frecuencias_medias = []
        frecuencias_medianas = []
        for i in range(n_contracciones):
            inicio = i * muestras_c
            fin = inicio + muestras_c
            if fin > len(senal_emg_filtrada):
        fin = len(senal_emg_filtrada)
            segmento = senal_emg_filtrada[inicio:fin]

  
            N = len(segmento)
            fft_segmento = np.fft.rfft(segmento * np.hanning(N))
            potencia = np.abs(fft_segmento)**2
            frecuencias = np.fft.rfftfreq(N, d=1/fs)

   
            frecuencia_media = np.sum(frecuencias * potencia) / np.sum(potencia)


            energia_acumulada = np.cumsum(potencia)
            energia_total = energia_acumulada[-1]
            frecuencia_mediana = frecuencias[np.where(energia_acumulada >= energia_total/2)[0][0]]


            frecuencias_medias.append(frecuencia_media)
            frecuencias_medianas.append(frecuencia_mediana)

        frecuencias_medias = np.array(frecuencias_medias)
        frecuencias_medianas = np.array(frecuencias_medianas)

        plt.figure(figsize=(10,5))
        plt.plot(frecuencias_medias, 'o-', color='blue', label='Frecuencia media')
        plt.plot(frecuencias_medianas, 'o-', color='red', label='Frecuencia mediana')
        plt.title('Evolución de la frecuencia media y mediana por contracción')
        plt.xlabel('Número de contracción')
        plt.ylabel('Frecuencia [Hz]')
        plt.grid(True)
        plt.legend()
        plt.show()


        print(f"Promedio frecuencia media: {np.mean(frecuencias_medias):.2f} Hz")
        print(f"Promedio frecuencia mediana: {np.mean(frecuencias_medianas):.2f} Hz")
<img width="841" height="471" alt="image" src="https://github.com/user-attachments/assets/d92cf826-ff16-4187-85e6-7e43c35e6dcf" />  
**Promedio frecuencia media:** 43.42 Hz  
**Promedio frecuencia mediana:** 42.84 Hz  

### Graficar los resultados obtenidos y analizar la tendencia de la frecuencia media y mediana a medida que progresa la fatiga muscular.  


### Discutir la relación entre los cambios de frecuencia y la fisiología de la fatiga muscular.  

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
- Naeije, M., & Zorn, H. (1982). Relation between EMG power spectrum shifts and muscle fibre action potential conduction velocity changes during local muscular fatigue in man. European Journal of Applied Physiology and Occupational Physiology, 50, 23-33. https://doi.org/10.1007/BF00952241  


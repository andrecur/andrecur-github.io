# andrecur-github.io
Blog de prácticas de Visión Robótica (MUVA 25/26)

# Preparación: Docker y Unibotics
Domingo 15/02/2026

## Objetivo
Instalar Docker y conseguir correr el Robotic Backend local.

## Workflow
1. Descarga de la imagen.
La imagen es enorme.. la estaba descargando por WiFi y el proceso iba lentísimo 🐌 (ya vi que era mejor conectarse directamente al router pero me dio pereza ir hasta allí).
Mientras tanto, pregunté a los compañeros si era normal que tardara tanto.
La respuesta la verdad fue un poco ambigüa, pero seguí confiando.
Pasan 5 horas y, como no podía ser otra, se había quedado pillado.😒
Pensé que tenía que volver a empezar de 0... reinicié el portatil, limpié algunas cosas y volví a lanzar el 'docker pull'. POR SUERTE, Docker retomó la descarga de donde se había quedado.
Tardó otras 3 horas pero llegó al final, que bien.
2. GPU
Mi portatil tiene AMD Radeon así que nada de CUDA ni soporte NVIDIA. --> Correr el backend sin GPU.
3. Empezar el BVC
Intenté comenzar el ejercicio, pero me parecía que había algo que no estaba funcionando, que no veía lo que decían los compañeros. Era ya bastante tarde, domingo, con muchas cosas aún pendientes por preparar para el día siguiente... así que decidí no empezar con unibotics ese mismo día sino dejarlo para otro momento con mente más fresca. Además aún tenía que crear el blog y enviar el usuario.

# Comprobación
Miercolés 18/02/2026
El profesor recordó que había que enviar usuario y blog (verdad, aún no lo he hecho!).
En ese momento aún estaba convencida de que mi imagen no funcionaba correctamente. Pero los compañeros me han ayudado a comprobar que sí, todo funcionaba (mil gracias compis!).

# Blog
Jueves 19/02/2026
Aún no he tenido tiempo de ponerme con BCV, pero he creado el blog.
Aquí estamos.

# BCV
Sábado 21/02/2026
Hoy por fin toca ponerse en serio con los ejercicios de Basic Computer Vision. 
Lanzé el BCV, conecté correctamente y pude comprobar que estaba recibiendo la imagen de la cámara sin problema. Primer paso superado✔️.
Luego pasé a intentar modificar la imagen y visualizar el resultado en la GUI.

## Conversión a escala de grises
Seguí lo que indicaba la página de info de BCV y empecé por algo sencillo (al menos en teoría), que era convertir la imagen de la cámara a escala de grises usando `cv2.cvtColor`.
La idea era confirmar que el pipeline funcionaba, modificar la imagen Y Mostrar el resultado en la GUI.

Inicialmente intenté utilizar 'Frequency.tick()' para controlar el bucle, ya que aparecía en la pestaña de código por defecto. Sin embargo, al ejecutar el programa, la aplicación fallaba y monstraba el mensaje 'failed to run the application'.
No podía ver el traceback porque la ventana  parecía estar intentando reconectarse, como si el contenedor no estuviera conectado. Aunque a mi me parecía que sí lo estaba. 
Después de probar distintas opciones, eliminé el control de frecuencia ('Frequency.tick()').
Al ejecutar el código sin esto,  el programa funcionó correctamente y pude visualizar la imagen en escala de gris en la ventana.

## Operaciones morfológicas
Como paso siguiente la página sugiere de probar con operaciones morfológicas. Realicé varias operaciones sobre la imagen en escala de gris y el primer resultado fue tan divertido que estoy empezando a pasarmelo bien. 
![erosion1](https://github.com/user-attachments/assets/46944a94-486f-4667-b897-9fb3269edae5)

Con la erosion parecía un poco película de terror. 
También después de aplicar cierre, que ya me quedé sin ojos, sin boca y sin agujeros de la nariz. Que horror. 🤣 

![closing7x7](https://github.com/user-attachments/assets/bdeba984-69c6-4e93-95a9-c7ce3c1feba9)

Apliqué erosion, dilatación, abertura, cierre y gradientes morfológico. También repetí las pruebas añadiendo una umbralización previa. Probé primero con un threshold clásico y luego con Otsu. El resultado de la erosión sobre la imagen umbralizada es esteticamente más agradable, sin duda.

Con estas operaciones estoy empezando a entender que no son solo efectos raros (como los que se usan para el filtro de la cámara del móvil), sino herramientas muy potentes para limpiar ruido y preparar la imagen para pasos posteriores.

## Filtro de color
En este paso las cosas ya no fueron tan sencillas y divertidas como antes; aquí sí me tocó pelearme un poco más. 
La idea es implementar un filtro de color para detectar objetos utilizando su color como característica principal. Para esto, en lugar que trabajar en RGB (o mejor dicho, BGR en OpenCV), se convierte la imagen al espacio HSV. Este espacio facilita mucho la segmentación ya que H (el color - Hue) está separado de brillo e intensidad.

1. Definí entonces un rango de valores correspondiente al color rojo (estoy bebiendo un té en una taza roja). Pero en realidad para el rojo fue necesario definir dos rango, ya que el rojo se encuentra en los extremos del círculo de tonos, por lo que aparece tanto al inicio como al final. Que buena elección de color...😒
Después generé una máscara binaria combinando ambos rangos. Esta máscara convierta en blanco las zonas que cumplen con el rango de color seleccionado (verdadero) y en negro el resto (falso). Así, solo el objeto rojo queda aislado del resto de la imagen.
Dado que la segmentación inicial suele contener ruido, apliqué operaciones morfológicas para limpiar imperfecciones y mejorar la detección.

Por supuesto no funcionó bien a la primera: además del objeto, detectaba zonas de mi cara. Como dije: el rojo, elección acertadísima. 
Tuve que ajustar los valores del rango aumentando un poco la saturación minima para hacer la detección más estricta. Tras varios intentos, finalmente conseguí aislar el  objeto.  

<img width="810" height="722" alt="red_filter1" src="https://github.com/user-attachments/assets/1e2bf0c7-9a86-4d6b-9719-b83effb332f9" />


2. Realicé una segunda prueba cambiando el color (quizás el azul sea más agradecido) y también el objeto a detectar, para observar cómo cambia la detección.



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

Con erosion parecía un poco película de terror. 
También después de aplicar cierre, que ya no tenía ojos ni boca ni boquetitos de la nariz. Que horror. 🤣 
![closing7x7](https://github.com/user-attachments/assets/bdeba984-69c6-4e93-95a9-c7ce3c1feba9)

Apliqué erosion, dilatación, abertura, cierre y gradientes también. Y repetí pero añadiendo una umbralización. Probé con un threshold clásico y luego con Otsu. El resultado de erosion sobre la imagen umbralizada es mucho más bonito, sin duda.


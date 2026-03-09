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

Resulta que soy muy lista, porque mi sudadera aparece azul oscuro en la cámara (aunque en realidad es morada; la luz y la resolución de la cámara del pc me la enseñan con otro tono). Así que, además de la botella, también detecta la manga de mi sudadera 😆.
<img width="846" height="712" alt="image" src="https://github.com/user-attachments/assets/da50b97c-d25b-4622-a51d-5eea85f2ceee" />

Bueno... yo diría que funciona.
Para comprobarlo, me quito la sudadera:
<img width="777" height="543" alt="image" src="https://github.com/user-attachments/assets/baaaafd0-97f0-47b2-8a63-d35e2160901a" />

Lo damos por bueno, ¿sí?

## Detección de bordes
Domingo 22/02/2026

Hoy he retomado el proyecto, tarde por la tarde. Me gustaría dedicarle mucho más tiempo pero tuve que avanzar en otras cosas además de preparar todo para la semana que va a empezar.. Pero sí que me he dado cuenta de que necesito ir un poco más deprisa si quiero empezar la práctica “de verdad” a tiempo, así que vamos adelante.

Esta vez apliqué filtros de detección de bordes. Probé con Canny y con Laplace, y la verdad... están guapísimos! Me encanta esa visualización basada solo en los bordes, es como ver solo la estructura esencial.
Canny devuelve un resultado limpio y definido, mientras que Laplace genera un efecto más "artístico", está especialmente bonito: resalta muchísimo los contrastes y le da un aspecto muy potente a la imagen.

## Convolución
Después probé aplicar convolución utilizando filtros de distintos tamaños: 3x3, 5x5 y 7x7. Pude ver cómo cambia el resultado dependiendo del tamaño del kernel: con 3x3 el efecto es más sutil y conserva más detalle, a medida que aumento a 5x5 y 7x7, la imagen se suaviza más y pierde más información fina. Pero, aun así, sigo pensando que me gustaba más la detección de bordes.. 
Lo que me llevo de aquí es que el tamaño del filtro no es un detalle sino una decisión que afecta directamente al equilibrio entre detalle y suavizado.

Y ahora toca algo que parece bastante más “serio”: optical flow.

Pero wow... viendo el tutorial es una pasada! Así que me pongo enseguida a ver como se hace.

## Optical flow
Confirmo: optical flow es una pasada. Cuando muevo la mano, aparecen colores donde hay movimiento. Por lo que he entendido, cada color es una dirección distinta, y cuanto más brillante es la zona, mayor es la velocidad. Si me quedo quieta, la imagen se vuelve prácticamente negra.

Eso sí, no se ve tan perfecto como en el tutorial. No aparecen líneas claras, veo más bien manchas o blobs de color que se mueven y cambian. Al principio pensé que algo estaba mal, pero en realidad he visto que es normal ya que estoy usando el método de flujo óptico denso, que calcula el movimiento en todos los píxeles (por eso la visualización es más "continua"). Es que se me pasó por alto la primera opción que aparece en la documentación de OpenCV, el método de Lucas-Kanade, que es un flujo óptico disperso. Por lo que leo ese trabaja solo con puntos característicos y dibuja vectores más definidos. Lo voy a probar ahora mismo, seguro que esto es el de las líneas.

Con el método de Lucas-Kanade, la visualización cambia completamente: ahora me veo y aparecen líneas que siguen puntos de la imagen. Es muy curioso ver cómo se dibujan trayectorias en tiempo real. Estaría guay conseguir que se dibujaran solo las que deberían de dibujarse de verdad, y no muchas que parecen moverse al azar, eheh. Estuve cambiando configuraciones para mejorarlo, en una de ellas incluso conseguí que siguiera el movimiento de un solo dedo, dibujando con bastante precisión. Pero duró poco 😆.
Todavía no lo domino del todo. He estado modificando parámetros (detección de esquinas y tamaño de la ventana de búsqueda) y el comportamiento cambia según los ajustes; a veces detecta demasiados puntos y es un caos total, otras veces aparecen líneas en objetos que están quietos y se quedan dibujadas aunque no haya movimiento real.

Todavía tengo que afinarlo, pero lo dejo aquí por hoy. Mañana (o cuando me dé tiempo) lo revisaré con la cabeza más fresca.

# Práctica 1
Miercoles 25/02/2025
 
Hoy es el día, empezamos la práctica. 
Sí, lo sé... aún no he terminado el flujo óptico y un par de cosas del BCV.
Pero en dos semanas hay entrega, así que: a darle caña.

Objetivo de la práctica: programar un coche de Fórmula1 (me encanta la F1), equipado con una cámara frontal, para que recorra un circuito de carreras que tiene una línea roja en el medio de la pista.

## Arranca la práctica
Primer objetivo específico: activar la cámara. Sé hacerlo, de hecho tengo éxito: veo la línea roja, bien. Vamos!
Le pongo valores a la velocidad lineas y a la angular... y el coche entpnces decide de girarse a la derecha y contemplar el césped eternamente, probablemente le gusta más que la línea roja (a mi también me gusta más el verde).
Eheh, algo no funciona. Bueno, claro. Si no configuro nada más, esto no puede funcioar por arte de magia.

Segundo objteivo: que el coche avance. 
Claro, arranqué la práctica pero no el coche.
Tendría que ir recto, pero en la práctica, estando aún mal orientado no ve la línea roja y no hace nada. Pues nada, reinicio y sigo.

## Detectar la línea roja
Vale, sé que tiene que detectar esa línea roja. En teoría lo sé hacer, confiamos.

Entonces, convertimos la imagen a HSV, definimos los dos rangos de rojo (sí, ya vimos que el rojo en HSV es especialito). Creamos la máscara, limpiamos ruido con operaciones morfológicas, y en teoría listo. Le doy al play... 🎉Iuppiii! Detecta la línea roja! 
O eso parecía.. 

Primera curva: tragedia. El coche sigue recto como si no supiera que no se tiene que chocar contra la pared (efectivamente, el coche aún no sabe que no tiene que chocar contra la pared 😅).
Ya me lo imaginaba. 🤣 La curva la hace, sí. Pero la hace gracias a la pared. Y cuando se acaba la pared: termina recto en el césped.
Maravilloso.

Menos mal que no estoy conduciendo de verdad (y que conste que conduzco muy bien y me encanta). Pero aquí la misión es que el coche automático no se estrelle. Y claramente aún no estamos ahí.

Resumiendo: el coche ve el rojo, pero no sabe que hacer con ello. Creo que aquí empieza oficialmente mi relación con el famoso PID.

## Valores PID
Sabado 28/02/2026
Vale, ya tengo la línea roja detectada. Ahora necesito que el coche haga algo inteligente con eso. Y es aquí donde tengo que introducir el famoso error.

Para hacer el seguimiento de la línea necesito saber dónde está la línea respecto al coche, y eso significa calcular su centroide. Voy a calcularlo directamente como la media de los píxeles blancos: tengo una región blanca (la máscara del rojo), y lo único que necesito es su centro horizontal. Así que calculo la media horizontal de los píxeles blancos. 
Con eso ya puedo calcular el error: centro de la línea menos el centro ideal (w/2); y ese error es el que meto en el controlador proporcional. Le pongo un circulo verde para visualizar en la línea dónde cree el coche que está la línea (veo lo que sigue el coche).

Le añado la constante Kp, que multiplica el error para corregir proporcionalmente la trayectoria del coche. Y entonces... Empieza a reaccionar (por fin)! 🤩

Pero claro, no me esperaba de acertar a la primera. Y efectivamente. Ahora curva un poco, pero pierde la linea, corrige tarde, vuelve a ver la linea y ahora se pasa; lo guay es que ha pasado la primera curva, y ahora se estampa a la siguente!

Pero mira, hemos pasado de recorrer un 11% del circuito a llegar a un 20%.  A trompicones eheh, pero oye, vamos avanzando.
Ahora habrá que hacerlo estable, a ver cuanto tardo 😆.

## Ajustando valores
Primero bajo la velocidad, porque creo que lo estoy haciendo ir demasiado rápido para que pueda reaccionar bien. Y oye, que guay! Solo con bajar la velocidad veo que el controlador P empieza a funcionar de verdad.

Conseguí dar una vuelta entera sin estrellarmeee!👏🏻👏🏻 Que feliz estoy!!! Esi sí, tengo que avisar que si alguien sufre de mal de mar, mejor que no mire mi resultado actual, porque en las curvas oscila bastante, ahahah. Pero en los tramos rectos va perfecto. Y sinceramente, me sorprende bastante haber llegado hasta aquí en pocos pasos.

Entonces vamos a afinar! 

## Añadir término derivativo (D)
Resumiendo, en recta el error es pequeño, así que la corrección también es pequeña; el sistema es estable y el coche avanza bien. En cambio, en curva el error aumenta, usando solo el P la corrección se vuelve grande, así que se pasa hacía el otro lado, vuelve a corregir y se vuelve a pasar; uff, oscilación constante en curva..  

Como estabamos con controlador proporcional puro, llegó el momento de añadir el término derivativo D. Hemos visto que lo que hace D es no solo mirar cuanto error tenemos, sino también mirar que tan rápido está cambiando el error: de esta forma, si el error está creciendo mucho, frena la correccion y 'amortigua' ese cambio brusco que percibimos ahora con solo P.

Mmm.. En teoría parecía la solución perfecta. En la práctica... bueno 😅 

Con los primeros valores que probé, el coche seguía oscilando. Quizás algo menos, pero claramente no era suficiente. Así que empezó la fase intensa de tuning: tocar velocidad, Kp, Kd,... probar, observar, ajustar, repetir.

Además, recordé que el profesor comentó que la cámara en el coche no es exactamente frontal, sino un poco desplazada hacia la izquierda. Eso significa que el cálculo del centro ideal de la imagen no es exactamente w/2. Añadí un pequeño offset en el cálculo del error, y efectivamente, el comportamiento cambiaba. Lo que faltaba... otro parámetro más para ajustar 😅

La verdad es que esta fase de afinado está siendo mucho más larga de lo que imaginaba.
La primera curva, que es en U hacia la derecha, sigue sin salir perfecta. La siguiente, hacia la izquierda, la hace casi impecable. Y luego llega una curva grande hacia la derecha donde vuelve a dar volantazos.

Me da a mi que el sistema no es completamente simétrico, así que sigo hilando todavía más fino.

## Ya lo tengo?
Después de unos cuantos ajustes de configuración (y unas cuantas pruebas), por fin el coche empieza a comportarse bastante bien. Estable en rectas, razonable en curvas, y con menos oscilaciones que antes. Tuve que añadir una pequeña compensación para el giro hacia la derecha, porque resultaba más inestable que el izquierdo. Al final, los sistemas reales no son perfectamente simétricos, y parte del trabajo es adaptarse a eso.

Y cuando por fin estaba contenta... Redoble de tambores 🥁
Acabo de enterarme de que el coche debería tardar 2.5 minutos en dar la vuelta completa.

Mis actuales 299.75 segundos (yo tan orgullosa de haber bajado de 300 😭) no se parecen demasiado a 2.5 minutos.

Así que ahora empieza otra fase, la de aumentar poco a poco la velocidad sin romper el equilibrio que tanto me ha costado encontrar.
Más horas de ajuste.
Más pruebas.
Más aprendizaje.

Veremos si consigo acercarme a esos 2.5 minutos... pero al menos ahora siento que tenga un poco más de controo sobre el sistema.

## Ajustes finales (o no..)
Vamos!!! He conseguido terminar la vuelta en 167.30 segundos! Esto si que se acerca mucho más a 2.5 minutos, (que son 150 segundo, se comparan mejor así, ¿no? 😆).
Pero hay un pero (y quizás más de uno..): que en la curva grande y larga hacia la derecha el término D (creo que sea él) provoca bandazos fuertes. Uff, seguimos ajustando.
Mejor tiempo: 156.96. Pero hay que ajustar un poco más.
Hoy no puedo más.

## Seguimos
Miercoles 04/03/2026
En clase comentamos nuestro respectivos estado de la práctica (un poco desastre en mi caso 🥲). Y además del PD para la velocidad, oigo que están comentando que podría configurar incluso dos escenarios, el de curva y el de recta. Pero piano, piano... vamos un paso a la vez.
Empiezo a añadir Kp y Kd también a la velocidad, porque hasta ahora solo estaba controlando "bien" el giro, pero el robot seguía yendo a toda pastilla aunque estuviera entrando en una curva. Así que claro, luego le pedía que corrigiera y aquello era una fiesta de bandazos.

Así que venga, a probar combinaciones: sube Kp, baja Kp, vamos con Kd, baja de nuevo Kd. Ahora gira demasiado. Ahora no gira bien. Pero me fijo que sí controlo la velocidad. Ahora parece que va mejor... ah no, espera, se acaba de ir de nuevo con sus oscilaciones.

Después de un rato de este maravilloso proceso científico (también conocido como prueba-error-desesperación) consigo algo bastante decente. No es perfecto, pero wow! El tiempo de vuelta baja a unos 2 minutos (126.17 que ilusión!!!), que ya es una mejora bastante grande respecto al principio.

En ese momento me vuelvo valiente y me lanzo a intentar tener dos escenarios distintos, uno para recta y otro para curva, con parámetros diferentes. Y pienso: bueno, qué puede salir mal... La idea parecía sencilla: detectar cuándo estoy en curva y aplicar un control más agresivo, y cuando estoy en recta suavizarlo para evitar oscilaciones. Ok, vamos, implementamos... y adivina? Lo he rotooo!! El robot ahora decide que la línea está en el otro lado y se gira justo al lado contrario de donde debería. Maravilloso.

Después de varios intentos de arreglarlo y de ver que cada vez hacía cosas más raras, tomo una decisión muy sensata desde el punto de vista de la salud mental y nerviosa: quitarlo por ahora. 
Mi conclusión fue: primero voy a dejar bien ajustado el control con un único escenario, y cuando eso esté más sólido volveré a intentar lo de distinguir entre rectas y curvas. Porque claramente el robot todavía tiene suficientes maneras de sabotearme sin necesidad de añadirle más lógica. Empiezo a pensar que no le gusto mucho :-(.

## La entrega se acerca
Sabado 07/03/2026
Ayer estuve ajustando y tuneando los parámetros. Le dediqué el poco tiemnpo que he podido, pero la verdad es que fue un poco frustrante porque parecía no llegar nunca a algo que funcionara realmente bien. Probaba ajustes, cambiaba valores... y siempre había alguna parte del circuito donde algo fallaba.

Pero después de unos cuantos ajustes más, bueno… creo haber alcanzado un cierto equilibrio en mis parámetros. O por lo menos, creo haber llegado al máximo equilibrio posible en mi código con los parámetros que tengo ahora mismo. Así que, por el momento, poco más voy a tocar. 

Hoy quiero probarlo en otros circuitos. Qué chulo el de Montreal! Vamos con ese.

Y sorpresa! Parece que está funcionando sorprendentemente bien en el primer tramo. Otra sorpresa es ver que realmente va lentísimo (estoy viendo que el tiempo en el simulador corre despacísimo)... llevo 10 minutos de tiempo real y solo 1 minuto de simulación 😅. 
Pero aun así estoy bastante contenta, porque sinceramente esperaba que se saliera en la primera curva. Y no. Está aguantando!

Llevo un 38% del circuito, y ni tan mal.
Bueno... a veces oscila, sí. Pero de verdad me esperaba una reacción mucho peor.

Por cierto, el circuito de Montreal mide 4.361 km, y la vuelta rápida la tiene Valtteri Bottas con 1'13". AHAHAH... igualito que mi coche, que ha tardado 2'48" según el simulador.

<img width="544" height="597" alt="Captura de pantalla 2026-03-07 211158" src="https://github.com/user-attachments/assets/970f38c7-f99b-4fbc-9284-8e18857dae33" />

Bueno, no sé si es un tiempo aceptable para este circuito con este simulador, pero si estuviera compitiendo contra un carrito del supermercado puede que gane, eh... Y oye, he terminado la vuelta, que ya es todo un logro. Iupiiii!

Seguimos con Montemló.

Wow, parecía ir bien... hasta que se estrella al quedar menos del 20% para llegar al final del circuito. ¡Ohhh, qué pena!

Pero ¿sabes por qué se estrelló? Porque perdió la línea y no fue capaz de volver a verla. Así que realmente este choque me sirvió, eheh. 

Después de llevar un rato allí pegado a la pared, me fijé en que daba pequeños trompicones intentando girarse. Y ahí me di cuenta de que tenía que cambiar el ángulo y la velocidad en el caso de que pierda la línea. Tenía configurada una velocidad demasiado elevada para el ángulo de giro que estaba usando cuando el robot perdía la línea. Así que ajusté esos parámetros, porque cuando se pierde la línea conviene girar fuerte y avanzar despacio, justo como hacen los pilotos de F1. Lo importante para mi robot es reencontrar la línea primero.

Volví a correr Montmeló y... ¡wow! otro circuito completado, sorpendenteeeee 🎉!

Lo que me da un poco de rabia es que no podré comprobar todos los circuitos que hay, porque las simulaciones tardan muchísimo y de aquí a la entrega no tendré tiempo suficiente para probarlos todos. Así que, confío en estos tres circuitos superados, y espero que el comportamiento sea lo suficientemente robusto para los demás, y que cuando (y si) pierde la línea, sea capaz de recuperarla y volver a la trayectoria 🙏🏻.



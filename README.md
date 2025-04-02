# Actividad de seguimiento - Simulación 1

|Integrante|correo|usuario github|
|---|---|---|
|Nombre completo integrante 1|correo integrante 1|gihub user integrante 1|
|Nombre completo integrante 2|correo integrante 2|gihub user integrante 2|

## Instrucciones

Antes de empezar a realizar esta actividad haga un **fork** de este repositorio y sobre este trabaje en la solución de las preguntas planteadas en la actividad de simulación. Las respuestas deben ser respondidas en español o si lo prefiere en ingles en el lugar señalado para ello (La palabra **answer** muestra donde).

**Importante**:
* Como la actividad es en las parejas del laboratorio, solo uno de los integrantes tiene que hacer el fork; y sobre repositorio bifurcado que se genera, la modificación se realiza en equipo.
* Como la entrega se debe hacer modificando el archivo READNE, se recomienda que consulte mas sobre el lenguaje **Markdown**. En el repo adjuntan dos cheatsheet ([cheat sheet 1](Markdown_Cheat_Sheet.pdf), [cheatsheet 2](markdown-cheatsheet.pdf)) para consulta rapida.
* Entre mas creativo mejor.

## Homework (Simulation)

This program, [`process-run.py`](process-run.py), allows you to see how process states change as programs run and either use the CPU (e.g., perform an add instruction) or do I/O (e.g., send a request to a disk and wait for it to complete). See the [README](https://github.com/remzi-arpacidusseau/ostep-homework/blob/master/cpu-intro/README.md) for details.

### Questions

1. Run `process-run.py` with the following flags: `-l 5:100,5:100`. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the `-c` and `-p` flags to see if you were right.
   
   <details>
   <summary>Answer</summary>
   
      En la simulación, se ejecutan dos procesos con las banderas `-l 5:100,5:100`, lo que significa que cada proceso se ejecuta en la CPU durante 5 unidades de tiempo sin realizar operaciones de entrada/salida (I/O). Cada uno usando la CPU al 100%. 

   Para corroborar lo anterior, se puede observar la traza de ejecución con `-p`
   - En los primeros 5 ciclos, el Proceso 0 usa la CPU.
   - En los siguientes 5 ciclos, el Proceso 1 usa la CPU.
   - No hay tiempos de inactividad, ya que siempre hay un proceso en ejecución.

   <br>

   Al verificar con la opción -c, obtenemos la estadística:
   - Tiempo total: 10 unidades
   - Tiempo CPU ocupada: 10 unidades
   - Tiempo I/O ocupada: 0 unidades
   - CPU Busy: 100.00%
   <br>
   Esto confirma que la CPU estuvo en uso el 100% del tiempo, sin momentos de inactividad. Se adjunta imagen que corrobora lo dicho anteriormente:

   <div align="center">
      <img src="https://github.com/DuvanR0598/Simulacion1_SO20251/blob/main/Imagenes/Pregunta%201.png?raw=true" alt="Pregunta 1" width="400"/>
   </div>
   </details>
   <br>

1. Now run with these flags: `./process-run.py -l 4:100,1:0`. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use `-c` and `-p` to find out if you were right. 
   
   <details>
   <summary>Answer</summary>
   La CPU estará ocupada solo durante los primeros 4 ciclos, luego quedará inactiva.
      
   - `4:100` → Un proceso que usa la CPU durante 4 unidades de tiempo al 100%.
   - `1:0` → Un proceso que no usa CPU y espera en la cola.

   <br>

   **¿Cuánto tiempo tarda en completarse ambos procesos?**
   - El primer proceso (PID 0) ejecuta 4 instrucciones en la CPU, lo que toma 4 unidades de tiempo.
   - El segundo proceso (PID 1) ejecuta una operación de E/S en el tiempo 5, lo que lo pone en estado bloqueado durante 5 unidades de tiempo.
   - Cuando finaliza la espera de E/S (tiempo 10), el proceso 1 ejecuta su última instrucción en el tiempo 11 y termina.

   <br>

   _El tiempo total que tardan en completarse ambos procesos es 11 unidades de tiempo._

   Lo anterior, se puede verificar en las estadísticas del programa:
   - Tiempo total de ejecución: 11 unidades de tiempo.
   - Tiempo en que la CPU estuvo ocupada: 6 unidades (54.55%).
   - Tiempo en que la CPU estuvo inactiva esperando la E/S: 5 unidades (45.45%).

   <br>
   
   <div align="center">
      <img src="https://github.com/DuvanR0598/Simulacion1_SO20251/blob/main/Imagenes/Pregunta%202.png?raw=true" alt="Pregunta 1" width="400"/>
   </div>
   </details>
   <br>

3. Switch the order of the processes: `-l 1:0,4:100`. What happens now? Does switching the order matter? Why? (As always, use `-c` and `-p` to see if you were right)
   
   <details>
   <summary>Answer</summary>

   - `PID 0:` Un proceso que solo realiza una operación de E/S.
   - `PID 1:` Un proceso que ejecuta 4 instrucciones en la CPU.
  
   <br>

   Al cambiar el orden de los procesos con `-l 1:0,4:100`, el proceso que realiza E/S (PID 0) se ejecuta primero. Como resultado, el sistema debe esperar a que finalice la operación de E/S antes de permitir que el proceso de CPU (PID 1) comience su ejecución.
   En este caso, el tiempo total de ejecución se reduce a 7 unidades de tiempo en comparación con los 11 tiempos de la ejecución anterior `(-l 4:100,1:0)`. Esto ocurre porque el proceso de CPU ahora puede ejecutarse en paralelo con la espera de E/S, optimizando el uso de los recursos del sistema.
   Por lo tanto, sí importa el orden de ejecución, ya que afecta el tiempo total de finalización de los procesos y la eficiencia del sistema. Ejecutar primero un proceso de E/S permite que la CPU trabaje mientras la E/S está en progreso, reduciendo el tiempo total de espera.

   <br>

   <div align="center">
      <img src="https://github.com/DuvanR0598/Simulacion1_SO20251/blob/main/Imagenes/Pregunta%202.png?raw=true" alt="Pregunta 1" width="400"/>
   </div>
   </details>
   <br>

5. We'll now explore some of the other flags. One important flag is `-S`, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (`-l 1:0,4:100 -c -S SWITCH ON END`), one doing I/O and the other doing CPU work?
   
   <details>
   <summary>Answer</summary>
   
   Este comando ejecuta dos procesos:
   - Un proceso `(PID 0)` que realiza una operación de entrada/salida (E/S).
   - Un proceso `(PID 1)` que ejecuta 4 instrucciones de CPU.
   - La opción `-S SWITCH_ON_END` hace que el sistema no cambie de proceso hasta que el proceso en ejecución termine completamente.

   <br>

   Con la configuración anterior, es decir, `-S SWITCH_ON_END` el sistema no cambia a otro proceso hasta que el proceso actual termine completamente, incluso si está esperando una E/S. Esto significa que:
   - El proceso de E/S (PID 0) se ejecuta primero y la CPU queda ociosa mientras espera que termine la E/S.
   - El proceso de CPU (PID 1) no puede ejecutarse hasta que PID 0 termine por completo, lo que desperdicia ciclos de CPU.
   - El tiempo total de ejecución aumenta, ya que la CPU no aprovecha los ciclos en los que el proceso de E/S está bloqueado.
  
   <br>

   Este comportamiento es menos eficiente en comparación con la ejecución sin `SWITCH_ON_END`, donde el proceso de CPU podría haber avanzado mientras la E/S se completaba en paralelo.

   <br>

   <div align="center">
      <img src="https://github.com/DuvanR0598/Simulacion1_SO20251/blob/main/Imagenes/Pregunta%204.png?raw=true" alt="Pregunta 1" width="700"/>
   </div>
   </details>
   <br>

7. Now, run the same processes, but with the switching behavior set to switch to another process whenever one is WAITING for I/O (`-l 1:0,4:100 -c -S SWITCH ON IO`). What happens now? Use `-c` and `-p` to confirm that you are right.
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

8. One other important behavior is what to do when an I/O completes. With `-I IO RUN LATER`, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (`./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I IO RUN LATER`) Are system resources being effectively utilized?
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

9. Now run the same processes, but with `-I IO RUN IMMEDIATE` set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>


### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.

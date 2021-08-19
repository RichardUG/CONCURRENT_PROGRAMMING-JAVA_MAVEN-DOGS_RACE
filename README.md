### Escuela Colombiana de Ingeniería
### Arquitecturas de Software – ARSW
## Laboratorio Programación concurrente, condiciones de carrera, esquemas de sincronización, colecciones sincronizadas y concurrentes - Caso Dogs Race

### Descripción:
Este ejercicio tiene como fin que el estudiante conozca y aplique conceptos propios de la programación concurrente.

### Parte I 
Antes de terminar la clase.

Creación, puesta en marcha y coordinación de hilos.

1. Revise el programa “primos concurrentes” (en la carpeta parte1), dispuesto en el paquete edu.eci.arsw.primefinder. Este es un programa que calcula los números primos entre dos intervalos, distribuyendo la búsqueda de los mismos entre hilos independientes. Por ahora, tiene un único hilo de ejecución que busca los primos entre 0 y 30.000.000. Ejecútelo, abra el administrador de procesos del sistema operativo, y verifique cuantos núcleos son usados por el mismo.

> Al ejecutar el ```main``` de la clase ```main``` de la ```parte 1``` observamos que el consumo de CPU es de casi el 20%
>
> ![](/img/media/proceso1.PNG)
>
> Tras observar el monitor de rescursos podemos ver que hace uso de los 8 nucleos de procesamiento
>
> ![](/img/media/CPU1.PNG)

2. Modifique el programa para que, en lugar de resolver el problema con un solo hilo, lo haga con tres, donde cada uno de éstos hará la tarcera parte del problema original. Verifique nuevamente el funcionamiento, y nuevamente revise el uso de los núcleos del equipo.

> ```java
> public class Main {
>
>	public static void main(String[] args) {
>		
>		PrimeFinderThread pft1=new PrimeFinderThread(0, 10000000);
>		PrimeFinderThread pft2=new PrimeFinderThread(10000000, 20000000);
>		PrimeFinderThread pft3=new PrimeFinderThread(20000000, 30000000);
>		pft1.start();
>		pft2.start();
>		pft3.start();
>    }
> }
> ```
>
> Podemos observar que el consumo de CPU es mayor, debido a que hace la ejecución de los datos mas veloz, asi mismo se refleja que el consumo de CPU es por menor tiempo
> 
> ![](/img/media/proceso2.PNG)
>
> ![](/img/media/CPU2.PNG)

3. Lo que se le ha pedido es: debe modificar la aplicación de manera que cuando hayan transcurrido 5 segundos desde que se inició la ejecución, se detengan todos los hilos y se muestre el número de primos encontrados hasta el momento. Luego, se debe esperar a que el usuario presione ENTER para reanudar la ejecución de los mismo.

> ```java
> public class Main {
>
>	public static void main(String[] args) {
>		
>		PrimeFinderThread pft1=new PrimeFinderThread(0, 10000000);
>		PrimeFinderThread pft2=new PrimeFinderThread(10000000, 20000000);
>		PrimeFinderThread pft3=new PrimeFinderThread(20000000, 30000000);
>		pft1.start();
>		pft2.start();
>		pft3.start();
>		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
>		new Timer().schedule(
>				new TimerTask() {
>					@Override
>					public void run() {
>						try{
>							pft1.suspend();
>							pft2.suspend();
>							pft3.suspend();
>							while (br.read() != '\n'){
>								br.read();
>							}
>							pft1.resume();
>							pft2.resume();
>							pft3.resume();
>						} catch (IOException e) {
>							e.printStackTrace();
>						}
>
>					}
>				}
>		,5000);
>	}
> }
> ```


### Parte II 


Para este ejercicio se va a trabajar con un simulador de carreras de galgos (carpeta parte2), cuya representación gráfica corresponde a la siguiente figura:

![](./img/media/image1.png)

En la simulación, todos los galgos tienen la misma velocidad (a nivel de programación), por lo que el galgo ganador será aquel que (por cuestiones del azar) haya sido más beneficiado por el *scheduling* del
procesador (es decir, al que más ciclos de CPU se le haya otorgado durante la carrera). El modelo de la aplicación es el siguiente:

![](./img/media/image2.png)

Como se observa, los galgos son objetos ‘hilo’ (Thread), y el avance de los mismos es visualizado en la clase Canodromo, que es básicamente un formulario Swing. Todos los galgos (por defecto son 17 galgos corriendo en una pista de 100 metros) comparten el acceso a un objeto de tipo
RegistroLLegada. Cuando un galgo llega a la meta, accede al contador ubicado en dicho objeto (cuyo valor inicial es 1), y toma dicho valor como su posición de llegada, y luego lo incrementa en 1. El galgo que
logre tomar el ‘1’ será el ganador.

Al iniciar la aplicación, hay un primer error evidente: los resultados (total recorrido y número del galgo ganador) son mostrados antes de que finalice la carrera como tal. Sin embargo, es posible que una vez corregido esto, haya más inconsistencias causadas por la presencia de condiciones de carrera.

Parte III

1.  Corrija la aplicación para que el aviso de resultados se muestre
    sólo cuando la ejecución de todos los hilos ‘galgo’ haya finalizado.
    Para esto tenga en cuenta:

    a.  La acción de iniciar la carrera y mostrar los resultados se realiza a partir de la línea 38 de MainCanodromo.

    b.  Puede utilizarse el método join() de la clase Thread para sincronizar el hilo que inicia la carrera, con la finalización de los hilos de los galgos.
    
    > Para solucionar este problema hacemos uso del metodo ```join()``` de la clase ```Thread``` dentro de un ciclo que inicializa este metodo para cada objeto de tipo ```Galgo``` guardado en el arreglo ```galgos``` asi espera a que finalice y muera el Thread de un galgo para mostrar el gandor, sin embargo al hacer esto cada vez que terminara un galgo mostraria el mensaje del ganador, asi que para evitar esto se instaura una condición con una variable booleana ```carreraGanada``` que indica que cuando esta sea ```false``` es porque nadie ha ganado y puedo mostrar el mensaje, esta variable inicia en ```false```pero en cuanto el primer galgo finalice es decir cuando el primer thread muera, cambiara el estado de la variable a ```true``` para que no vuelva a mostrar el mensaje.
    >
    > ```java
    > can.setStartAction(
    >            new ActionListener() {
    >
    >                @Override
    >                public void actionPerformed(final ActionEvent e) {
    >			//como acción, se crea un nuevo hilo que cree los hilos
    >                    //'galgos', los pone a correr, y luego muestra los resultados.
    >                    //La acción del botón se realiza en un hilo aparte para evitar
    >                    //bloquear la interfaz gráfica.
    >                    ((JButton) e.getSource()).setEnabled(false);
    >                    new Thread() {
    >                        public void run() {
    >                            for (int i = 0; i < can.getNumCarriles(); i++) {
    >                                //crea los hilos 'galgos'
    >                                galgos[i] = new Galgo(can.getCarril(i), "" + i, reg);
    >                                //inicia los hilos
    >                                galgos[i].start();
    >                            }
    >                           boolean carreraGanada=false;
    >                            for (Galgo g: galgos){
    >                                try {
    >                                    g.join();
    >                                    if(!carreraGanada) {
    >                                        can.winnerDialog(reg.getGanador(), reg.getUltimaPosicionAlcanzada() - 1);
    >                                        System.out.println("El ganador fue:" + reg.getGanador());
    >                                        carreraGanada = true;
    >                                    }
    >                                }catch (InterruptedException ex){
    >                                    ex.printStackTrace();
    >                                }
    >                            }
    >                        }
    >                    }.start();
    >                }
    >            }
    >    );
    > ```
    >
    > Podemos observar que ahora al iniciar la aplicación no nos muestra el mensaje de ganador
    > 
    > ![](/img/media/Carrera1.PNG)
    > 
    > Aca observamos que una vez llegado el primer galgo muestra el mensaje del ganador
    > 
    > ![](/img/media/resultadocarrera1.PNG)
    >
    > Ahora vemos el outpt de la aplicación por consola
    > 
    > ![](/img/media/consolacarrera1.PNG)
    

2.  Una vez corregido el problema inicial, corra la aplicación varias
    veces, e identifique las inconsistencias en los resultados de las
    mismas viendo el ‘ranking’ mostrado en consola (algunas veces
    podrían salir resultados válidos, pero en otros se pueden presentar
    dichas inconsistencias). A partir de esto, identifique las regiones
    críticas () del programa.
    
    > El error que encontramos es que cuando finalizan 2 galgos relaticamente al mismo tiempo, usan el mismo recurso en la clase de ```RegistroLlegada``` y marcan la misma posición de llegada, como podemos observar en la imagen anterior.

3.  Utilice un mecanismo de sincronización para garantizar que a dichas
    regiones críticas sólo acceda un hilo a la vez. Verifique los
    resultados.
    
    > Para solucionar este problema nos dirigimos al metodo ```corra()``` de la clase ```Galgo``` y antes de marcar la variable ```ubicación``` de este objeto con la información que trae del metodo ```getUltimaPosicionAlcanzada()``` del objeto ```regl``` de tipo ```RegistroLlegada``` hacemos uso de la función ```synchronized``` sobre el objeto ```regl``` para que identifique si el recurso esta en uso y que en caso de estarlo espere a que lo liberen para continuar, asi solo de a un galgo ingresan a marcar su posición.
    > ```java
    > public void corra() throws InterruptedException {
	>	while (paso < carril.size()) {
	>		Thread.sleep(100);
	>		carril.setPasoOn(paso++);
	>		carril.displayPasos(paso);
	>		if (paso == carril.size()) {
	>			carril.finish();
	>			synchronized (regl) {
	>				int ubicacion = regl.getUltimaPosicionAlcanzada();
	>				regl.setUltimaPosicionAlcanzada(ubicacion + 1);
	>				System.out.println("El galgo " + this.getName() + " llego en la posicion " + ubicacion);
	>				if (ubicacion == 1) {
	>					regl.setGanador(this.getName());
	>				}
	>			}
	>		}
    >    }
	> }
	> ```
	> 
	> En este resultado que nos muestra la consela observamos que el problema ha sido solucionado y que cada posición esta asignada a un unico galgo
	> 
	> ![](/img/media/consolacarrera2.PNG)

4.  Implemente las funcionalidades de pausa y continuar. Con estas,
    cuando se haga clic en ‘Stop’, todos los hilos de los galgos
    deberían dormirse, y cuando se haga clic en ‘Continue’ los mismos
    deberían despertarse y continuar con la carrera. Diseñe una solución que permita hacer esto utilizando los mecanismos de sincronización con las primitivas de los Locks provistos por el lenguaje (wait y notifyAll).
    
    > Para realizar estas dos funcionalidades se creo una variable booleana ```pause``` que indica si la ejecución se encuentra o no en pausa  y un metodo  ```setPause()``` para realizar el cambio de estado de la variable
    > 
    > ```java
    > public void setPause(boolean pause) {
	>	this.pause = pause;
	> }
	> ```
	> 
	> También tuvimos que completar las funcionalidades de los botones ```setStopAction``` y ```setContinueAction```.
	> En el caso de ```setStopAction``` se hizo que para cada galgo creado se cmabiara el estado de la variable ```pause``` a true
	> 
	> ```java
	> can.setStopAction(
    >            new ActionListener() {
    >                @Override
    >                public void actionPerformed(ActionEvent e) {
    >                    for (Galgo g: galgos){
    >                        g.setPause(true);
    >                    }
    >                    System.out.println("Carrera pausada!");
    >                }
    >            }
    >    );
    > ```
    >    
    > Y en el caso de ```setStopAction``` se hizo que para cada galgo creado se cambiara el estado de la variable ```pause``` a false y hacemos uso del metodo ```notifyAll()``` de la clase ```Thread``` para que active todo los hilos que se encuentran bloqueados en ```reg```
    > 
    > ```java
    > can.setContinueAction(
    >            new ActionListener() {
    >                @Override
    >                public void actionPerformed(ActionEvent e) {
    >                    synchronized (reg) {
    >                        reg.notifyAll();
    >                        for (Galgo g : galgos) {
    >                            g.setPause(false);
    >                        }
    >                        System.out.println("Carrera reanudada!");
    >                    }
    >                }
    >            }
    >    );
    > ```
    >    
    > Ademas tambien debimos implementar el metodo getReg() en la clase ```MainCanodromo``` el cual sirve para obtener el objeto ```reg```
    > 
    > ```java   
    > public static RegistroLlegada getReg() {
    >    return reg;
    > }
    > ```
    > 
    > También se creo un metodo llamado ```Pause```en la clase ```Galgo``` que coloca en espera ese registro en el objeto ```reg```
    > 
    > ```java
    > public void pause() {
	>	synchronized (MainCanodromo.getReg()) {
	>		try {
	>			MainCanodromo.getReg().wait();
	>		} catch (InterruptedException e) {
	>			e.printStackTrace();
	>		}
	>	}
	> }
	> ```
	> 
	> Finalmente en el metodo ```corre()``` de la clase ```Galgo``` colocamos una condición en la cual si la variable ```pause``` indica que no esta pausado continue su ejecución normal, pero en caso de que indique que si esta pausada invoque el metodo ```pause()```
	> 
	> ```java
	> public void corra() throws InterruptedException {
	>	while (paso < carril.size()) {
	>		if (!pause) {
	>			Thread.sleep(100);
	>			carril.setPasoOn(paso++);
	>			carril.displayPasos(paso);
	>			if (paso == carril.size()) {
	>				carril.finish();
	>				synchronized (regl) {
	>					int ubicacion = regl.getUltimaPosicionAlcanzada();
	>					regl.setUltimaPosicionAlcanzada(ubicacion + 1);
	>					System.out.println("El galgo " + this.getName() + " llego en la posicion " + ubicacion);
	>					if (ubicacion == 1) {
	>						regl.setGanador(this.getName());
	>					}
	>				}
	>			}
	>		}
	>		else{
	>			pause();
	>		}
	>	}
	> }
	> ```


    



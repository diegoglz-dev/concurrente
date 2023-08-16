# Práctica 5

1. Se requiere modelar un puente de un solo sentido, el puente solo soporta el peso de 5 unidades de peso. Cada auto pesa 1 unidad, cada camioneta pesa 2 unidades y cada camión 3 unidades. Suponga que hay una cantidad innumerable de vehículos (A autos, B camionetas y C camiones).
   a. Realice la solución suponiendo que todos los vehículos tienen la misma prioridad.
   b. Modifique la solución para que tengan mayor prioridad los camiones que el resto de los vehículos.

1.a)

```c
Procedure Puente1 is

TASK Puente is
	ENTRY pasarAuto();
	ENTRY pasarCamioneta();
	ENTRY pasarCamion();
	ENTRY salirAuto();
	ENTRY salirCamioneta();
	ENTRY salirCamion();
end Puente;

TASK TYPE Auto;
arrAuto: array (1 .. N) of Auto;

TASK TYPE Camioneta;
arraCamioneta: array (1 .. N) of Camioneta;

TASK TYPE Camion;
arrCamion: array (1 .. N) of Camion;

TASK BODY Auto is
BEGIN
	Puente.pasarAuto();
	cruzandoPuente();
	Puente.salirAuto();
END Auto;

TASK BODY Camioneta is
BEGIN
	Puente.pasarCamioneta();
	cruzandoPuente();
	Puente.salirCamioneta();
END Camioneta;

TASK BODY Camion is
BEGIN
	Puente.pasarCamion();
	cruzandoPuente();
	Puente.salirCamion();
END Camion;

TASK BODY Puente is
peso:= 0 : int
BEGIN
	LOOP
		SELECT
			WHEN (peso + 1 <= 5) =>
				ACCEPT pasarAuto() DO
				END pasarAuto;
				peso := peso + 1;
			OR
				WHEN (peso + 2 <= 5) =>
					ACCEPT pasarCamioneta() DO
					END pasarCamioneta;
					peso:= peso + 2;
			OR
				WHEN (peso + 3 <= 5) =>
					ACCEPT pasarCamion() DO
					END pasarCamion;
					peso:= peso + 3;
			OR
				ACCEPT salirAuto() DO
				END salirAuto;
				peso:= peso - 1;
			OR
				ACCEPT salirCamioneta DO
				END salirCamioneta;
				peso:= peso - 2;
			OR
				ACCEPT salirCamion DO
				END salirCamion;
				peso:= peso - 3;
		END SELECT
	END LOOP
END Puente

BEGIN
	null;
END Puente1
```

1.b)

```c
Procedure Puente1 is

TASK Puente is
	ENTRY pasarAuto();
	ENTRY pasarCamioneta();
	ENTRY pasarCamion();
	ENTRY salirAuto();
	ENTRY salirCamioneta();
	ENTRY salirCamion();
end Puente;

TASK TYPE Auto;
arrAuto: array (1 .. N) of Auto;

TASK TYPE Camioneta;
arraCamioneta: array (1 .. N) of Camioneta;

TASK TYPE Camion;
arrCamion: array (1 .. N) of Camion;

TASK BODY Auto is
BEGIN
	Puente.pasarAuto();
	cruzandoPuente();
	Puente.salirAuto();
END Auto;

TASK BODY Camioneta is
BEGIN
	Puente.pasarCamioneta();
	cruzandoPuente();
	Puente.salirCamioneta();
END Camioneta;

TASK BODY Camion is
BEGIN
	Puente.pasarCamion();
	cruzandoPuente();
	Puente.salirCamion();
END Camion;

TASK BODY Puente is
peso:= 0 : int
BEGIN
	LOOP
		SELECT
			  WHEN (peso + 1 <= 5) and (pasarCamion'count == 0) =>
				  ACCEPT pasarAuto() DO
				  END pasarAuto;
				  peso := peso + 1;
			OR
				WHEN (peso + 2 <= 5) and (pasarCamion'count == 0) =>
					ACCEPT pasarCamioneta() DO
					END pasarCamioneta;
					peso:= peso + 2;
			OR
				WHEN (peso + 3 <= 5) =>
					ACCEPT pasarCamion() DO
					END pasarCamion;
					peso:= peso + 3;
			OR
				ACCEPT salirAuto() DO
				END salirAuto;
				peso:= peso - 1;
			OR
				ACCEPT salirCamioneta DO
				END salirCamioneta;
				peso:= peso - 2;
			OR
				ACCEPT salirCamion DO
				END salirCamion;
				peso:= peso - 3;
		END SELECT
	END LOOP
END Puente

BEGIN
	null;
END Puente1
```

2. Se quiere modelar la cola de un banco que atiende un solo empleado, los clientes llegan y si esperan más de 10 minutos se retiran.

```c
Procedure Banco is
Task Empleado is
	ENTRY pedido(datos: in texto, res: out texto)
end Empleado;

TASK TYPE Cliente;
arrCliente: array (1 .. N) of Cliente;

TASK BODY Cliente is
	resultado: texto;
BEGIN
	SELECT
		Empleado.pedido("datos", resultado);
	OR DELAY(600)
		// Se va
		null;
	END SELECT
END Cliente

TASK BODY Empleado is
BEGIN
	LOOP
		ACCEPT pedido(datos: in texto, res: out texto) DO
			res:= resolverPedido(dato);
		END pedido;
	END LOOP
END Empleado

BEGIN
	null;
END Banco

```

3. Se dispone de un sistema compuesto por **_1 central_** y **_2 procesos_**. Los procesos envían señales a la central. La central comienza su ejecución tomando una señal del proceso 1, luego toma aleatoriamente señales de cualquiera de los dos indefinidamente. Al recibir una señal de proceso 2, recibe señales del mismo proceso durante 3 minutos.

    El proceso 1 envía una señal que es considerada vieja (se deshecha) si en 2 minutos no fue recibida.

    El proceso 2 envía una señal, si no es recibida en ese instante espera 1 minuto y vuelve a mandarla (no se deshecha).

```c
Procedure Sistema is

TASK Central is
	ENTRY Señal1(s: in texto);
	ENTRY Señal2(s: in texto);
	ENTRY FIN();
END Central

TASK Proceso1;
TASK Proceso2;

TASK Reloj is
	ENTRY Inicio();
END Reloj

TASK BODY Proceso1 is
s: texto;
BEGIN
	LOOP
		s = generarSeñal();
		SELECT
			Central.Señal1(s);
		OR DELAY(120)
		END SELECT
	END LOOP
END Proceso1

TASK BODY Proceso2 is
s: texto;
BEGIN
	s = generarSeñal()
	LOOP
		SELECT
			Central.Señal2(s);
			s = generarSeñal();
		ELSE
			delay(60) // Se duerme 1 min.
		END SELECT
	END LOOP
END Proceso2

TASK BODY Central is
boolean fin;
BEGIN
	ACCEPT Señal1(s: in texto);
	while (true)
		SELECT
			ACCEPT Señal1(s: in texto);
		OR
			ACCEPT Señal2(s: in texto);
			Reloj.Inicio();
			fin = false;
			while (not fin)
				SELECT
					WHEN (FIN'Count == 0) DO
						ACCEPT Señal2(s: in texto);
				OR
					ACCEPT FIN()
					fin = true;
				END SELECT
			END SELECT
END Central

TASK BODY Reloj is
BEGIN
	LOOP
		ACCEPT Inicio() DO
		END Inicio;
		delay(180);
		CENTRAL.FIN()
	END LOOP
END Reloj

BEGIN
	null;
END Sistema

```

4. En una clínica existe un **_médico_** de guardia que recibe continuamente peticiones de atención de las **_E enfermeras_** que trabajan en su piso y de las **_P personas_** que llegan a la clínica ser atendidos.

    Cuando una _persona_ necesita que la atiendan espera a lo sumo 5 minutos a que el médico lo haga, si pasado ese tiempo no lo hace, espera 10 minutos y vuelve a requerir la atención del médico. Si no es atendida tres veces, se enoja y se retira de la clínica.

    Cuando una _enfermera_ requiere la atención del médico, si este no lo atiende inmediatamente le hace una nota y se la deja en el consultorio para que esta resuelva su pedido en el momento que pueda (el pedido puede ser que el médico le firme algún papel). Cuando la petición ha sido recibida por el médico o la nota ha sido dejada en el escritorio, continúa trabajando y haciendo más peticiones.

    El _médico_ atiende los pedidos dándole prioridad a los enfermos que llegan para ser atendidos.

    Cuando atiende un pedido, recibe la solicitud y la procesa durante un cierto tiempo. Cuando está libre aprovecha a procesar las notas dejadas por las enfermeras.

    ```c
    Procedure Clinica is
    TASK Medico is
    	ENTRY atencionPersona(sintomas: in texto, diagnostico: out texto);
    	ENTRY atencionEnfermera();
    	ENTRY atenderNota();
    END Medico

    TASK TYPE Persona;
    arrPersonas: array (1 .. N) of Persona;

    TASK BODY Persona is
    intentos: int, atendido: boolean, diag: texto;
    BEGIN
    	intentos = 0;
    	atendido = false;
    	while (not atendido and intentos < 3)
    		SELECT
    			Medico.atencionPersona("SINTOMAS", diag)
    			atendido = true;
    		OR DELAY(300)
    			// Espera 10 min. sin hacer nada
    			delay(600);
    			intentos = intentos + 1;
    		END SELECT
    END Persona

    TASK Enfermera is
    nota: texto;
    BEGIN
    	LOOP
    		SELECT
    			Medico.atencionEnfermera();
    		ELSE
    			Escritorio.dejarNota(nota);
    		END SELECT
    	END LOOP
    END Enfermera

    TASK BODY Medico is
    Begin
    	LOOP
    		SELECT
    			ACCEPT atencionPersona(sint: in texto, res: out texto) DO
    				res = resolver(sint);
    			END atencionPersona
    		OR WHEN (atencionPersona'count == 0) DO
    			ACCEPT atencionEnfermera() DO
    			END atencionEnfermera;
    		OR WHEN (atencionPersona'count == 0) and (atencionEnfermera'count == 0) DO
    			ACCEPT atenderNota(nota: in texto) DO
    			END atenderNota;
    		END SELECT
    	END LOOP
    END Medico

    TASK BODY Escritorio is
    cola notas;
    BEGIN
    	LOOP
    		SELECT
    			ACCEPT dejarNota(n: in texto) DO
    				notas.push(n);
    			END dejarNota
    		OR WHEN (not notas.empty()) DO
    			ACCEPT tomarNota(n: out texto) DO
    				n := pop(notas);
    			END tomarNota
    		END SELECT
    	END LOOP
    END Escritorio

    TASK BODY Secretaria is
    n: texto
    BEGIN
    	LOOP
    		Escritorio.tomarNota(n);
    		Medico.atenderNota(n);
    	END LOOP
    END Secretaria

    BEGIN
    	null;
    END Clinica
    ```

    5. En un sistema para acreditar carreras universitarias, hay UN Servidor que atiende pedidos de U Usuarios de a uno a la vez y de acuerdo con el orden en que se hacen los pedidos.
       Cada usuario trabaja en el documento a presentar, y luego lo envía al servidor; espera la respuesta de este que le indica si está todo bien o hay algún error. Mientras haya algún error, vuelve a trabajar con el documento y a enviarlo al servidor. Cuando el servidor le responde que está todo bien, el usuario se retira. Cuando un usuario envía un pedido espera a lo sumo 2 minutos a que sea recibido por el servidor, pasado ese tiempo espera un minuto y vuelve a intentarlo (usando el mismo documento).

    ```c
    Procedure Sistema is

    TASK Servidor is
    	ENTRY pedido(doc: in texto, res: out texto)
    END Servidor

    TASK TYPE Usuario;
    arrUsuarios: array (1 .. N) of Usuario;

    TASK BODY Usuario is
    doc: texto; res: texto; boolean fin = false;
    BEGIN
    	doc = generarDoc();
    	while (not fin) {
    		SELECT
    			Servidor.pedido(doc, res);
    			if (res == "Sin errores")
    				fin = true;
    			else
    				doc = corregir();
    		OR DELAY(180)
    			delay(60)
    		END SELECT
    	}
    	// SE VA
    END Usuario

    TASK BODY Servidor is
    doc: texto; res: texto;
    BEGIN
    	loop
    		ACCEPT pedido(doc: in texto, res: out texto) DO
    			res := resolver(doc);
    		END Pedido
    	END loop
    END Servidor

    BEGIN
    	NULL;
    END Sistema
    ```

    8. Una empresa de limpieza se encarga de recolectar residuos en una ciudad por medio de 3 camiones. Hay P personas que hacen continuos reclamos hasta que uno de los camiones pase por su casa. Cada persona hace un reclamo, espera a lo sumo 15 minutos a que llegue un camión y si no vuelve a hacer el reclamo y a esperar a lo sumo 15 minutos a que llegue un camión y así sucesivamente hasta que el camión llegue y recolecte los residuos; en ese momento deja de hacer reclamos y se va. Cuando un camión está libre la empresa lo envía a la casa de la persona que más reclamos ha hecho sin ser atendido. Nota: maximizar la concurrencia.

    ```c
    Procedure Empresa is
    TASK TYPE Administrador is
    	ENTRY Pedido(idp: in int);
    	ENTRY siguiente(idpers: out int);
    END Administrador

    TASK TYPE Persona is
    	ENTRY camionEnCasa();
    END Persona

    arrPersonas: array (1 .. N) of Persona;

    TASK TYPE Camion;
    camion1, camion2, camion3: Camion;

    TASK BODY Camion is
    idp: int;
    BEGIN
    	loop
    		Administrador.siguiente(idp);
    		arrPersonas[idp].camionEnCasa();
    	end loop
    END Camion;

    TASK BODY Persona is
    id: int; boolean atendido = false;
    BEGIN
    	ACCEPT Ident(pos: in int) do
    		id:=pos;
    	END Ident;
    	while (not atendido) {
    		Administrador.reclamo(id);
    		SELECT
    			ACCEPT CamionEnCasa();
    			atendido = true;
    		OR
    			DELAY(900);
    	}
    END Persona;

    TASK BODY Administrador is
    idpers: int; boolean hayReclamo;
    array atendidos([N] false); array reclamos([N] 0);
    BEGIN
    	loop
    		SELECT
    			ACCEPT reclamo(idpers: in int) do
    				if(not atendido[idPers]) {
    					reclamo[idPers]++;
    					hayReclamo = true;
    				}
    			END reclamo;
    		OR
    			WHEN (hayReclamo)
    				ACCEPT siguiente(idpers: out int) do
    					idpers = max(reclamos);
    					atendidos[idpers] = true;
    					reclamos[idpers] = -1;
    					hayReclamo = buscarMayorReclamo();
    					/* buscarMayorReclamo() que recorre arreglo de reclamos y si hay reclamos mayor a -1, significa que hay reclamos y devuelve true o false en caso contrario.*/
    				END siguiente;
    		END Select
    	end loop
    End Administrador

    BEGIN
    	for i : 1 to N {
    		arrPersonas(i).Ident(i);
    	}
    END Empresa


    ```

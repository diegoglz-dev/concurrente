# Práctica 2

1)

a)

```c
sem detector = 1;

Process Persona[id: 0 .. N-1] {
	P(detector);
	//avanzo
	V(detector);
}
```

b)

```c
sem detector = 3;

Process Persona[id: 0 .. N-1] {
	P(detector)
	// avanzo
	V(detector)
}
```

2)

```c
sem mutex=1; int cant=5;
cola q=[5, newRecurso()]

Process Proceso[id: 1 .. N] {
	while (true) {
		P(cant)
		
		P(mutex)
		elemento = q.pop(id)
		V(mutex)

		utilizoElemento(elemento);

		P(mutex)
		q.push(elemento, id)
		V(mutex)

		V(cant)
	}
}
```

3) El problema que tiene el código es que en ambos casos se bloquea el sem. general bloqueando el acceso de llegada del otro usuario de distinta prioridad.

**Ejemplo:** Llegan 6 usuarios de alta utilizan máximo de recursos, pero solo 5 pueden acceder bloqueando 1 lugar a los de prioridad baja y viceversa pasa lo mismo con el Process Usuario-Baja

**Solución:** invertir las lineas con las sentencias P

4)

a) 

```c
sem mutex=1;

Process Persona[id: 1 .. N] {
	while(true) {
		P(mutex)
		imprimir(Documento)
		V(mutex)
	}
}
```

b)

```c
sem buff[N] = ([N] 0), mutex=1;
cola q, bool libre = true;

Process Persona[id: 0 .. N-1] {
	P(mutex)
	if (!libre) {
		push(q, id)
		V(mutex)
		P(buff[id])
	} else {
		libre = false;
		V(mutex)
	}

	imprimir(Documento)

	P(mutex)
	if (!q.empty()) {
		pop(q, id);
		V(buff[id])     <---- Hacer un V aca no garantiza que la impresora va estar libre.
	} else {          <---- Por el else sabemos que la empresa está libre.
		libre = true;
	}
	V(mutex)
}
```

c)

```c
int turno = 0;
sem buff[N] = ([N] 1);

Process Persona[id: 0 .. N-1] {
	if (turno == id) {
		imprimir(Documento);
		turno++;
		V(buff[turno])
	} else {
		P(buff[id])
	}
}
```

Otra solución propuesta por el profesor

```c
sem buff[N] = ([N] 1)

Process Persona[id: 0 .. N-1] {
	if (id > 0)
		P(buff[id])
	imprimir(Documento)
	V(buff[id + 1])
}
```

d)

```c
cola q; 
sem buff[N] = ([N] 0), mutex = 1, avisar = 0; termino = 0;

Process Persona[id: 0 .. N] {
	P(mutex)
	push(q, id)
	V(mutex)
	V(avisar)

	P(buff[id])

	imprimir(Documento)
	V(terminar)
}

Process Cordinador {
	int id_persona;
	
	while (true) {
		P(avisar)
		P(mutex)

		pop(q, id_persona)
		V(buff[id_persona])

		V(mutex)
		P(termino)
	}
}
```

6)

```c
int llegue = 0, termine = 0, idGanador = -1;
cola tareas;
array maxTareas[N] = ([N] 0);
sem mutex = 1, semTermine = 1, barrera[N] = ([N] 0)

Process Empleado[id: 0 .. N-1]

P(mutex)
llegue++;
if (llegue == N) {
 for i: 0 to N
   V(barrera[i];
}
V(mutex)
P(barrera[id])

P(mutex)
while(! tareas.empty()) {
 pop(tareas, t)
 V(mutex)
 realizarTarea(t);
 maxTareas[id] = maxTareas[id]++;
}

P(semTermine)
termine++;
if (termine == N) {
 idGanador = max(maxTareas);
 for i: 0 to N-1 {
  V(barrera[i])
 }
}
V(semTermine)

if (id == idGanador) {
 reclamarPremio();
}
```

7)

```c
sem hayMarco = 0;
sem hayVidrio = 0;
cola marcos;
cola vidrios;
sem sMutexMarcos = 1;
sem sMutexVidrios = 1;
sem sMarcos = 30;
sem sVidrios = 50;

Process Carpintero[id: 1 .. 4] {
	while (true) {
		m = fabricar_marco();
		
		P(sMarcos)
		P(sMutexMarcos)
		push(marcos, m);
		V(sMutexMarcos)
		
		V(hayMarco)
	}
}

Process Vidriero {
	while(true)
		v = fabricar_vidrio();
		P(sVidrios)
		P(sMutexVidrios)
		push(vidrios, v);
		V(sMutexVidrios)

		V(hayVidrio)
}

Process Armador[id: 1 .. 2] {
	while (true) {
		P(hayMarco)
		
		P(sMutexMarcos)
		m = pop(marcos);
		V(sMutexMarcos)

		V(sMarcos) <---- Le avisa al carpintero que tiene un lugar más disponible para depositar

		P(hayVidrio)
		P(sMutexVidrios)
		v = pop(vidrios);
		V(sMutexVidrios)

		V(sVidrios)

		// fabricar ventana
		ventana = fabricar(m, v);
	}
}
```

8)

```c
sem general = 7;
sem max_trigo = 5;
sem max_maiz = 5;

Process CamionTrigo[id: 1 .. T] {
	P(max_trigo)
	P(general)
	
	descargaCereal();

	V(max_trigo)
	V(general)
}

Process CamionMaiz[id: 1 .. M] {
	P(max_maiz)
	P(general)
	
	descargaCereal();
	
	V(max_trigo)
	V(general)
}
```

9)

Puede que en el instante que checkea la condición evaluando si un alumno está esperando un profesor pise al otro y estar tomando examen a un alumno que ya está interactuando con el otro profesor.

```c
Solución

Sem sem([N] 1)

Profesor A::
P(llegaA)
P(mutexA)
idAlumno = pop(colaA)
V(mutexA)
P(sem[idAlumno]) <---- Bloquea para checkear
if (estado[idAlumno] == "Esperando") {
	estado[idAlumno] == "A";
	V(sem[idAlumno]) <---- Libera
	V(esperando[idAlumno])
	// se toma el examen
	V(esperando[idAlumno])
} else {
	V(sem[idAlumno]) <---- Libera
}
```

10)

```c
cola fila; int cant = 0;
sem mutex = 1, llegaron5 = 0, esperaVacuna[N] = ([N] 0)

Process Persona[id 1 .. N] {
	P(cant)
	cant++;
	P(mutex)
	push(fila, id);
	V(mutex)
	
	if(cant == 5) {
		V(llegaron5)
		cant = 0;
	}
	
	P(esperaVacuna[id])
	// se va
}

Process Empleado {
	cola aux;
	for i: 1 to 10
		P(llegaron5)
		
		P(mutex)
		id = pop(fila);
		V(mutex)
		
		vacunar();
		push(aux, id);

		for j: 1 to 5
			id = pop(aux);
			V(esperaVacuna[id])
}
```
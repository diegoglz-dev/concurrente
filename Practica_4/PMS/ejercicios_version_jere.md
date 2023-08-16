1.

```c++
Process Robot[id:0..R-1]{
    string webInfectada;
    while(true)
        webInfectada = buscarWebInfectada();
    Admin!enviarWeb(webInfectada);
}

Process Analizador{
    string webInfectada;
    boolean infectado;
    while(true)
        Admin!estoy()
        Admin?webTestear(webInfectada);
        infectado = determinarWebInfectada(webInfectada);
}

Process Admin{
    cola tareas;
    string webInfectada;

    do Robot[*]?enviarWeb(webInfectada); -> push(tareas,webInfectada);
    if(not empty(tareas)); Analizador?estoy(); webInfectada = pop(tareas); Analizador!webTestear(webInfectada);
}
```

2.

```c++
Process Empleado1{
    string muestra;
    while(true)
        muestra = prepararMuestra();
        Administrador!enviarMuestra(muestra);
}

Process Administrador{
    cola c;
    string muestra;

        do Empleado1?enviarMuestra(muestra); -> push(c, muestra);
        if (not empty(c)); Empleado2?estoy(); Empleado2!darMuestra(pop(c));
}


Process Empleado2{
    string muestra;
    string resultado;

    while(true)
        Administrador!estoy();
        Administrador?darMuestra(muestra);
        set = armarSetAnalisis(muestra);
        Empleado3!enviarSet(set);
        Empleado3?resultado(resultado);
}


Process Empleado3{
    string set;
        Empleado2?enviarSet(set);
        Empleado2!resultado(resultado);
}
```

3. a.

```c++
Process Alumno[id:0..N-1]{
    string examen;
    int nota;

    resolución = resolverExamen(examen);
    Administrador!examen(resolución);
    Profesor?nota(nota);
}


Process Administrador{
    int idAlumno;
    string examen;
    cola examenes;

        do Alumno[*]?examen(idAlumno, examen) -> push(examenes,idAlumno, examen);
    if (not empty(examenes)); Profesor?estoy(); Profesor!examen(pop(examenes)); // aclaración: se desapila tanto idAlumno como examen en el mismo pop
}


Process Profesor{
    string examen;
    int nota;
    int cantEntrega = 0;
    while(cantEntrega < N)
        Administrador!estoy();
        Administrador?examen(idAlumno, examen);
        nota = corregirExamen(examen);
        Alumno[idAlumno]!nota(nota);
        cantEntrega++;
}
```

3. b.

```c++
Process Alumno[id:0..N-1]{
    string examen;
    int nota;

    resolución = resolverExamen(examen);
    Administrador!examen(resolución);
    Profesor[*]?nota(nota);
}


Process Profesor[id:0..P-1]{
    int idAlumno;
    string examen;
    int cantEntrega = 0;
    while(cantEntrega < N)
        Administrador!estoy(id);
        Administrador?examenCorregir(idAlumno, examen);
        nota = corregirExamen(examen);
        Alumno[idAlumno]!nota(nota);
        cantEntrega++;
}


Process Administrador{
    int idAlumno;
    int idProfesor;
    string examen;
    cola examenes;

        do Alumno[*]?examen(examen) -> push(examenes, idAlumno, examen);
        if (not empty(examenes)); Profesor[*]?estoy(idProfesor); Profesor[idProfesor]!examenCorregir(pop(examenes, idAlumno, examen));
}
```

3. c.

```c++
Processs Alumno[id:0..N-1]{
    string examen;
    int nota;

    Administrador!llegue(id);
    Administrador?empieza();
    resolución = resolverExamen(examen);
    Administrador!examen(id, resolución);
    Profesor[*]?nota(nota);
}

Process Administrador{
    int idAlumno;
    int idProfesor;
    string examen;
    cola examenes;
    int i;

    for i := 1 to N
        Alumno[*]?llegue(idAlumno);
    for i := 1 to N
        Alumno[*]!empieza();

        do Alumno[*]?examen(idAlumno, examen) -> push(examenes, idAlumno, examen);
    if (not empty(examenes)); Profesor[*]?estoy(idProfesor); Profesor[idProfesor]!examenCorregir(pop(examenes, idAlumno, examen));
}


Process Profesor[id:0..P-1]{
    int idAlumno;
    string examen;
    int cantEntrega = 0;
    while(cantEntrega < N)
        Administrador!estoy(id);
        Administrador?examenCorregir(idAlumno, examen);
        nota = corregirExamen(examen);
        Alumno[idAlumno]!nota(nota);
        cantEntrega++;
}
```

4.

```c++
Process Persona[id:0..P-1]{
    Administrador!llegue(id);
    Empleado?usarSimulador();
    Empleado!termine();
    // se retira
}

Process Administrador{
    int idPersona;
    cola personas;

        do Persona[*]?llegue(idPersona) -> push(personas, idPersona);
        if (not empty(personas)); Empleado?estoy(); Empleado!proximaPersona(pop(personas));
}


Process Empleado{
    int idPersona;
    int cantPersonas = 0;

    while(cantPersonas < P)
        Administrador!estoy();
        Administrador?proximaPersona(idPersona);
        Persona[idPersona]!usarSimulador();
        Persona[idPersona]?termine();
        cantPersonas++;
}
```

5.

```c++
Process Espectador[id:0..E-1]{
	Maquina!llegue(id);
	Maquina!pedido();
	Maquina?usar();
	//usar
	Maquina!termine();
}


Process Maquina{
    int idEspectador;
    cola espectadores;
    boolean libre = True;

        do Espectador[*]?llegue(idEspectador) -> push(espectadores, idEspectador);
        if (not empty(espectadores) && libre); Espectador[*]?pedido(); Espectador[pop(espectadores)]!usar(); libre = False; Espectador[idEspectador]?termine(); libre = True
}


// no hay que usar proceso Administador, debe ser la Máquina quien se ocupe de los llamados a los espectadores

Solución inválida:

Process Espectador[id:0..E-1]{
	Maquina!llegue(id);
	Maquina?usar();
	//usar
	Maquina!termine();
}


Process Administrador{
    int idEspectador;
    cola espectadores;

    do Espectador[*]?llegue(idEspectador) -> push(espectadores, idEspectador);
        if (not empty(espectadores));  Maquina?estoy(); Maquina!proximo(pop(espectadores));
}


Process Maquina{
    int idEspectador;
    while(true)
        Administrador!estoy();
        Administrador?proximo(idEspectador);
        Espectador[idEspectador]!usarMaquina();
        Espectador[idEspectador]?termine();
}
```

3. En un examen final hay N alumnos y P profesores. Cada alumno resuelve su examen, lo entrega y espera a que alguno de los profesores lo corrija y le indique la nota. Los profesores corrigen los exámenes respectando el orden en que los alumnos van entregando.

a) Considerando que P=1.

b) Considerando que P>1.

c) Ídem b) pero considerando que los alumnos no comienzan a realizar su examen hasta que todos hayan llegado al aula.

**Nota:** maximizar la concurrencia y no generar demora innecesaria.

a)

<table>

<tr>
<td>

```c++
Process Alumno[id: 1 .. N] {
    text examen; int nota;

    examen = resolver();
    Admin!termineExamen(examen, id);
    Profesor?esperandoNota(nota);
}
```

</td>

<td>

```c++
Process Admin {
    cola examenes; text examen; int idA;

    do (Alumno[*]?termineExamen(examen, idA)) -> push(examenes(examen,idA));
    (not empty(examenes)); Profesor?pedido() -> pop(examenes(examen, idA))
                                                Profesor!Corregir(examen, idA);
}
```

</td>

<td>

```c++
Process Profesor {
    text examen;
    int nota, idA;

    while (true) {
        Admin!pedido();
        Admin?Corregir(examen, idA);
        nota = evaluar(examen);
        Alumno[idA]!esperandoNota(nota);
    }
}
```

</td>
</tr>
</table>

b)

<table>

<tr>
<td>

```c++
Process Alumno[id: 1 .. N] {
    text examen;
    int nota;

    examen = resolver();
    Admin!termineExamen(examen, id);
    Profesor[*]?respuesta(nota);
}
```

</td>

<td>

```c++
Process Admin {
    cola examenes; text examen; int idA, idP;

    do Alumno[*]?termineExamen(examen, idA) -> push(examenes(examen,idA));
    (not empty(examenes)); Profesor[*]?pedido(idP) -> pop(examenes(examen, idA));
                                                    Profesor[idP]!corregir(examen, idA);
}
```

</td>

<td>

```c++
Process Profesor[id: 1 .. N] {
    text examen;
    int nota, idA;

    while (true) {
        Admin!pedido(id);
        Admin?corregir(examen, idA);
        nota = evaluar(examen);
        Alumno[idA]!respuesta(nota);
    }
}
```

</td>
</tr>
</table>

c)

<table>

<tr>
<td>

```c++
Process Alumno[id: 1 .. N] {
    text examen;
    int nota;

    Admin!llegue();
    Admin?puedeEmpezar();
    examen = resolver();
    Admin!termineExamen(examen,id);
    Profesor[*]?respuesta(nota);
}
```

</td>

<td>

```c++
Process Admin {
    cola examenes; text examen; int idA, idP;

    for i: 1 .. N {
        Alumno[*]?llegue();
    }
    for i: 1 .. N {
        Alumno[*]!puedeEmpezar();
    }

    do Alumno[*]?termineExamen(examen, idA) -> push(examenes(examen, idA));
        (not empty(examen));Profesor[*]?pedido(idP) -> pop(examenes(examen, idA));
                                                Profesor[idP]!corregir(examen, idA);
}
```

</td>

<td>

```c++
Process Profesor[id: 1 .. N] {
    text examen;
    int nota, idA;

    while(true) {
        Admin!pedido(id);
        Admin?corregir(examen, idA);
        nota = evaluar(examen);
        Alumnos[idA]!respuesta(nota);
    }
}
```

</td>
</tr>
</table>

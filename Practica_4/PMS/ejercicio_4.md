4. En una exposición aeronáutica hay un simulador de vuelo (que debe ser usado con exclusión mutua) y un empleado encargado de administrar el uso del mismo. Hay P personas que esperan a que el empleado lo deje acceder al simulador, lo usa por un rato y se retira. El empleado deja usar el simulador a las personas respetando el orden de llegada. Nota: cada persona usa sólo una vez el simulador.

<table>

<tr>
<td>

```c++
Process Persona[id: 0 .. N-1] {
    text simulador;

    Admin!pedido(id);
    Empleado?darAcceso(simulador);
    usar(simulador);
    Empleado!termine(simulador);
}
```

</td>

<td>

```c++
Process Admin {
    cola personas;
    int idP;

    do Persona[*]?pedido(idP) -> push(personas, idP);
    (not empty(personas)); Empleado?atender() -> pop(personas, idP);
                                                Empleado!turnoDe(idP);
}
```

</td>

<td>

```c++
Process Empleado {
    text simulador;
    int idP;

    while (true) {
        Admin!atender();
        Admin?turnoDe(idP);
        simulador = preparar();
        Persona[idP]!darAcceso(simulador);
        Persona[idP]?termine(simulador);
    }
}
```

</td>
</tr>
</table>

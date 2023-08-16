5. En un estadio de fútbol hay una máquina expendedora de gaseosas que debe ser usada por E Espectadores de acuerdo al orden de llegada. Cuando el espectador accede a la máquina en su turno usa la máquina y luego se retira para dejar al siguiente. Nota: cada Espectador una sólo una vez la máquina.

<table>

<tr>
<td>

```c++
Process Persona[id: 1 .. N] {
    text gaseosa;

    Admin!pedido(id);
    Maquina?realizarPedido();
    gaseosa = elegir();
    Maquina!miSeleccion(gaseosa, id);
    Maquina?darGaseosa(gaseosa);
}
```

</td>

<td>

```c++
Process Admin {
    cola fila;
    int idp;

    do Persona[*]?pedido(idP) -> push(fila, idP);
        (not empty(fila)); Maquina?atender() -> pop(fila, idP);
                                            Maquina!siguienteTurno(idP);
}
```

</td>

<td>

```c++
Process Maquina {
    int idP; text gaseosa, suPedido;

    while(true) {
        Admin!atender();
        Admin?siguienteTurno(idP);
        Persona[idP]!realizaPedido();
        Persona[idP]?miSeleccion(gaseosa);
        suPedido = darPedido(gaseosa);
        Persona[idP]!darGaseosa(suPedido);
    }
}
```

</td>
</tr>
</table>

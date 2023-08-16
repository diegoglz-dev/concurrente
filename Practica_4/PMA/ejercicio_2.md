2. Se desea modelar el funcionamiento de un banco en el cual existen 5 cajas para realizar pagos. Existen P clientes que desean hacer un pago. Para esto, cada una selecciona la caja donde hay menos personas esperando; una vez seleccionada, espera a ser atendido. En cada caja, los clientes son atendidos por orden de llegada. Luego del pago, se les entrega un comprobante. **Nota:** maximizando la concurrencia.

<table>
<!-- <tr>
<th> Good </th>
<th> Bad </th>
</tr> -->

<tr>
<td>

```c++
chan fila(int);
chan cajaAsignada[N](int);
chan respuestas[N];
chan pedido(); // Utilizado para sincronizar y así evitar Busy Waiting

Process Persona[id: 1 .. N] {
    text tramite, res; int idcaja;

    tramite = generarTramite();
    send pedido();
    send fila(id);
    receive cajaAsignada[id](idcaja);
    send irCaja[idcaja](id, tramite);
    receive respuestas[id](res);
}
```

</td>

<td>

```c++
Process Coordinador {
    int idp; array cajas[5]([5] 0);
    int idcaja;

    while (true) {
    receive pedido();

    if (! empty(fila) && empty(atendido)){
        receive fila(idp);
        min = menosPersona(cajas);
        send cajaAsignada[idp](min);
        cajas[min]++;
    }
    if (!empty(atendido)){
        receive atendido(idcaja);
        caja[idcaja]--;
    }
}
```

</td>

<td>

```c++
Process Empleado {
    int idp; text res, tramite;

    receive irCaja[id](idp, tramite);
    res = resolver(tramite);
    send respuestas[idp](res);
    send atendido(id);
    send pedido();
}
```

</td>
</tr>
</table>

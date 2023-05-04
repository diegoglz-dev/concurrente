2. Se desea modelar el funcionamiento de un banco en el cual existen 5 cajas para realizar pagos. Existen P clientes que desean hacer un pago. Para esto, cada una selecciona la caja donde hay menos personas esperando; una vez seleccionada, espera a ser atendido. En cada caja, los clientes son atendidos por orden de llegada. Luego del pago, se les entrega un comprobante. **Nota:** maximizando la concurrencia.

<table>
<!-- <tr>
<th> Good </th>
<th> Bad </th>
</tr> -->

<tr>
<td>

```c
chan cola(int);
chan cajaAsignada[N](int);
chan respuestas[N](text);

Process Persona[id: 0 .. N-1] {
	text res, tramite;

	tramite = generarTramite();
	send cola(id, tramite);
	send pedido("In");
	receive respuesta[id](res);
}
```

</td>

<td>

```c++
Process Coordinador {
	array caja[5]([5] 0);
	int idp; text tramite, tipo;
    while (true) {
        pedido(tipo)
        if (tipo == "In") {
            receive cola(idp, tramite);
            min = menosPersonas(caja);
            send cajaAsignada[min](id, tramite);
            menosPersonas[min]++;
        } else if (tipo == "Out") {
            receive atendido(idcaja);
            menosPersonas[idcaja]--;
        }
    }
}
```

</td>

<td>

```c++
Process Empleado[id: 0 .. 4] {
    int idp, text tramite, text res;
    receive cajaAsignada[id](idp, tramite);
    res = resolver(tramite);
    send respuesta[id](res);

    send atendido(id);
    send pedido("Out");
}
```

</td>
</tr>
</table>

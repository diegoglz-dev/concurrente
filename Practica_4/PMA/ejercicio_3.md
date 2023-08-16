1. Se debe modelar el funcionamiento de una casa de comida rápida, en la cual trabajan 2 cocineros y 3 vendedores, y que debe atender a C clientes. El modelado debe considerar que:

-   Cada cliente realiza un pedido y luego espera a que se lo entreguen.
-   Los pedidos que hacen los clientes son tomados por cualquiera de los vendedores y se lo pasan a los cocineros para que realicen el plato. Cuando no hay pedidos para atender, los vendedores aprovechan para reponer un pack de bebidas de la heladera (tardan entre
    1 y 3 minutos para hacer esto).
-   Repetidamente cada cocinero toma un pedido pendiente dejado por los vendedores, lo cocina y se lo entrega directamente al cliente correspondiente.

**Nota:** maximizar la concurrencia.

<table>

<tr>
<td>

```c++
chan pedidos(int, text)
chan respuestaPedido[N](text)
chan siguiente[3](text, int)
chan pedidosPendientes(int, text)
chan atender(int)

Process Persona[id: 0 .. N-1] {
    text pedido, miPedido;

    pedido = generarPedido();
    send pedidos(id, pedido);
    receive respuestaPedido[id](miPedido);
}
```

</td>

<td>

```c++
Process Coordinador {
    text pedido; int idP, idV;

    while(true) {
        receive atender(idV);
        if (empty(pedidos(idP, pedido))) {
            pedido = "vacio";
            id = -1;
        } else {
            receive pedidos(idP, pedido);
        }
        send siguiente[idV](pedido, idP);
    }
}
```

</td>

<td>

```c++
Process Vendedor[id: 0 .. 2] {
    text pedido;

    while(true){
        send atender(id);
        receive siguiente[id](pedido, idp);
        if (pedido <> "vacio") {
            send pedidosPendientes(idP, pedido);
        } else {
            reponerBebidas();
            delay(random(60, 180));
        }
    }
}
```

</td>

<td>

```c++
Process Cocinero[id: 0 .. 1] {
    int idP, text ped, plato;

    while(true){
        receive pedidosPendientes(idP, ped);
        plato = cocinar(ped);
        send respuestaPedido[idP](plato);
    }
}
```

</td>
</tr>
</table>

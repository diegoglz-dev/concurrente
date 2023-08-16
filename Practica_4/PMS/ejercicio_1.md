# PMS

1. Suponga que existe un antivirus distribuido en él hay R procesos robots que continuamente están buscando posibles sitios web infectados; cada vez que encuentran uno avisan la dirección y continúan buscando. Hay un proceso analizador que se encargue de hacer todas las pruebas necesarias con cada uno de los sitios encontrados por los robots para determinar si están o no infectados.

<table>

<tr>
<td>

```c++
Process Robot {
    text direccion;

    while(true) {
        direccion = generarReporte();
        Admin!avisar(direccion);
    }
}
```

</td>

<td>

```c++
Process Admin {
    cola buffer;
    text direccion;

    do Robot?avisar(direccion) -> push(buffer, direccion);
    (not empty(buffer));Analizador?pedido() -> Analizador!infectados(pop(buffer))
    od

}
```

</td>

<td>

```c++
Process Analizador {
    text direccion, res;

    while(true) {
        Admin!pedido();
        Admin?infectados(direccion);
        res = probando(direccion);
    }
}
```

</td>
</tr>
</table>

# PMA

1. Suponga que N personas llegan a la cola de un banco. Para atender a las personas existen 2 empleados que van atendiendo de a una y por orden de llegada a las personas.

<table>
<!-- <tr>
<th> Good </th>
<th> Bad </th>
</tr> -->

<tr>
<td>

```c
chan fila(int, text);
chan resolucion[N](resultado);

Process Persona[id: 0 .. N-1] {
	text tramite, res;

	while (true) {
		tramite = generarTramite();
		send fila(id, tramite);
		receive resolucion[id](res);
	}
}
```

</td>

<td>

```c
Process Empleado[id: 0 .. 1] {
	text res, tram, int idp;
	while (true) {
		receive fila(idp, tram);
		res = atender(tram);
		send recolucion[idp](res);
	}
}
```

</td>
</tr>
</table>
5. Resolver la administración de las impresoras de una oficina. Hay 3 impresoras, N usuarios y 1 director. Los usuarios y el director están continuamente trabajando y cada tanto envían documentos a imprimir. Cada impresora, cuando está libre, toma un documento y lo imprime, de acuerdo con el orden de llegada, pero siempre dando prioridad a los pedidos del director.
   **Nota:** los usuarios y el director no deben esperar a que se imprima el documento.

<table>

<tr>
<td>

```c++
chan imprimirUsuario(text)
chan imprimirDirector(text)
chan pedidoDeImpresion();

Process Usuario[id: 0 .. N-1] {
   text doc;

   while(true) {
      doc = generarDoc();
      send imprimirUsuario(doc);
      send pedidoDeImpresion();
   }
}
```

</td>

<td>

```c++
Process Director {
   text doc;

   while(true) {
      doc = generarDoc();
      send imprimirDirector(doc);
      send pedidoDeImpresion();
   }
}
```

</td>

<td>

```c++
Process Impresora[id: 0 .. 2] {
   text doc;

   while(true) {
      receive pedidoDeImpresion();
      if(!empty(imprimirUsuario) && empty(imprimirDirector)) {
         receive imprimirUsuario(doc);
         imprimiendo(doc);
      }
      (!empty(imprimirDirector)) {
         receive imprimirDirector(doc);
         imprimiendo(doc);
      }
   }
}
```

</td>
</tr>
</table>

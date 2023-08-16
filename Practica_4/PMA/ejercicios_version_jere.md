1.

```c++
chan llegue(int);
chan llamar[N]();

process Persona[id:0..N-1]{
	send llegue(id);
	receive llamar[id]();
}

process Empleado[id:0..1]{
    int idP;
    receive llegue(idP);
    send llamar[idP]();
}
```

2.

```c++
chan llegue(int);
chan atencion[5](int);
chan pago[P](int);
chan comprobante[P](string);

Process Cliente[id:0..P-1]{
    int pago, idCaja;
    string comprobante;

    send llegue(id);
    receive caja[id](idCaja);
    send atencion[idCaja](idCliente);
    pago = generarPago();
    receive comprobante[id](comprobante);
    send termine(idCaja);
}


Process Coordinador{
    int idCliente;
    cola c;
    int idCaja;
    array int cajas[5] //inicializadas en 0
    while(true)
        receive llegue(idCliente);
        if(not empty(termine))
            receive termine(idCaja);
            cajas[idCaja]--;
        else
            cajaMinima = calcularCajaMenosGente(cajas);
            cajas[cajaMinima]++;
            send caja[idCliente](cajaMinima);
}


Process Caja[id:0..4]{
    string pago;
    int idCliente;

    receive atencion[id](idCliente);
    atender();
    comprobante = generarComprobante();
    send comprobante[idCliente](comprobante);
}
```

3.

```c++
chan pedidos(int,string);
chan cocineros(int,string);
chan platos[C](int,string);
chan admin(int);
chan siguiente[3](string);

Process Cliente[id:0..C-1]{
    string pedido,comida;
    send pedidos(id,pedido);
    receive platos[id](comida);
    comer();
}

Process Cocinero[id:0..1]{
    int idC;
    string pedido, comida;
    while(true)
        receive cocineros(idC,pedido);
        comida = cocinar();
        send platos[idC](comida);
}

Process Vendedor[id:0..2] {
    string pedido;
    int idC, tiempo;
    while(true)
        send admin(id);
        receive siguiente[id](idC,pedido);
        if(ped <> 'vacio')
            send cocineros(idC,pedido);
        else
            tiempo = generarRandom(1,3); // le mando por parámetro limite min y max
            delay(tiempo); // los vendedores aprovechan para reponer un pack de bebidas de la heladera
}

Process Coordinador { //  lo utilizo para los casos en donde un proceso debe hacer varias cosas, no sólo esperar recibir un mensaje...
    int idE, string ped;
    while(true)
        receive admin(idE);
        if(empty(pedidos))
            ped = 'vacio';
        else
            receive pedidos(ped);
        send siguiente[idE](ped);
}
```

4.

```c++
chan llegue(int);
chan hayMensaje();
chan[N] cabinas(int);
chan facturas(string);

Process Cliente[id:0..N-1]{
    string ticket,
    int cabina, monto;

    send llegue(id);
    send hayMensaje();
    receive cabinas[id](cabina);
    usarCabina();
    pagar();
    send termine(id,cabina);
    send hayMensaje();
    receive cabinas[id](ticket);
}


Process Empleado{
    String ticket;
    int idC, idCab;
    cola cabinaLibre int;

    while(true)
        receive hayMensaje();  // a continuación uso de las guardas
        if(!empty(llegue)) & (!empty(cabinaslibres)) & (empty(termine))
            receive(llegue(idC));
        send cabinas[idC](pop(cabinaslibres))

        if(!empty(termine))
            receive termine(idC, idCab);
            push(cabinasLibres, idCab);
            pago = calcularCosto(idC);
            send pagar[idC](pago);
}
```

5. versión Jere (REVISAR)

```c++
chan enviarDocumentoUsuario(string);
chan enviarDocumentoDirector(string);
chan hayDocumento();
chan impresionUsuario[N](string);
chan impresionDirector(string);
chan documentoImpresoUsuario[N](int, string);
chan documentoImpresoDirector(string);

Process Usuario[id:0..N-1]{
    string documento;
    string documentoImpreso;

    while(true)
        documento = generarDocumento();
        send enviarDocumentoUsuario(id, documento);
        send hayDocumento();
        receive documentoImpresousuario[id](documentoImpreso);
}

Process Director{
    string documento;
    string documentoImpreso;

    while(true)
        documento = generarDocumento();
        send enviarDocumentoDirector(documento);
        send hayDocumento();
        receive documentoImpresoDirector(documentoImpreso);
}


Process Impresora[id:0..2]{    // hay que hacer un admin
    int idImpresora;
    int idUsuario;
    string documento;

    send hayImpresora();
    receive imprimirImpresora();
    if(not empty(impresionDirector))
        receive impresionDirector(documento);
        impresion = imprimir(documento);
        send documentoImpresoDirector(impresión);
    else if (not empty(impresionUsuario))
        receive impresionUsuario(idUsuario, documento);
        impresion = imprimir(documento);
        send documentoImpresoUsuario[idUsuario](documento);
}

Process Administrador{
    string documento;
    int idUsuario;

    while(true)
        receive hayDocumento();
        receive hayImpresora();

        if(not empty(enviarDocumentoDirector))
            receive enviarDocumentoDirector(documento);
            send imprimirImpresora();
            send impresionDirector(impresion);
        else if(not empty(enviarDocumentoUsuario))
            receive enviarDocumentoUsuario(idUsuario, documento);
            send imprimirImpresora();
        send impresionUsuario(idUsuario, impresion);
}
```

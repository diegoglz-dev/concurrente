# Práctica 3

1)

```c
Monitor BaseDeDatos {
	int cant = 0, dormidos = 0; cond cola;
	
	procedure soloLectura() {
		if (cant >= 5) {
			dormidos++;
			wait(cola);
		} else {
			cant++;
		}
	}

	procedure salir() {
		if (dormidos > 0) {
			dormidos--;
			signal(cola);
		} else {
			cant--;
		}
	}
}

Process Usuario[id: 0 .. N] {
	BaseDeDatos.soloLectura();
	leer();
	BaseDeDatos.salir();
}

// Otra solución que no respeta orden

procedure solo_lectura {
	while(cant == 5) {
		wait(cola);
	}
	cant++;
}

procedure salir {
	cant--;
	signal(cola);
}
```

2)

a)

```c
Monitor Fotocopiadora {
 procedure utilizar () {
  fotocopiar();
 }
}

Process Persona[id: 0 .. N-1] {
 Fotocopiadora.utilizar();
}

```

b)

```c
Monitor Fotocopiadora {
 cond cola;
 bool libre = true;
 int esperando = 0;

 procedure utilizar() {
  if (libre) {
    libre = false;
  } else {
    esperando++;
    wait(cola);
  }
 }

 procedure liberar() {
  if (esperando > 0) {
   esperando--;
   signal(cola);
  } else {
    libre = true;
  }
 }
}

Process Persona[id: 0 .. N-1] {
 Fotocopiadora.utilizar();
 fotocopiar();
 Fotocopiadora.liberar();
}
```

c)

```c
Monitor Fotocopiadora {
 cond cola[N]; int esperando = 0;
 bool libre = true; colaOrdenada fila; int idAux;
 
 procedure utilizar (int in id, int in edad) {
  if (not libre) {
    insertarOrdenado(fila, id, edad);
    esperando++;
    wait(cola);
  } else {
    libre = false;
  }
 }
  
 procedure liberar () {
  if (esperando > 0) {
   esperando--;
   sacarOrdenado(fila, idAux);
   signal(cola[idAux]);
  } else {
    libre = false;
  }
 }
}

Process Persona[id: 0 .. N-1] {
 Fotocopiadora.utilizar(id, edad);
 fotocopiar();
 Fotocopiadora.liberar();
}
```

d)

```c
Monitor Fotocopiadora {
 cond cola[N];
 int esperando = 0;
 int siguiente = 0;

 procedure utilizar (int in id) {
  if (siguiente != id)
    wait(cola[id])
 }

 procedure liberar() {
  if (siguiente < N)
   siguiente++;
  else 
   siguiente = 0;
  signal(cola[id])
 }
}

Process Persona[id: 0 .. N] {
 Fotocopiadora.utilizar(id);
 fotocopiar();
 Fotocopiadora.liberar();
}
```

e)

```c
Monitor Fotocopiadora {
	cola esperando;
	cond esperaAcceso[N], despertarEmpleado, liberado;

	procedure solicitarAcceso(int in id) {
		push(esperando, id);
		signal(despertarEmpleado);
		wait(esperaAcceso[id]);
	}

	procedure darAcceso() {
		int idAux;

		if(empty(esperando)) {
			wait(despertarEmpleado);
		}
		pop(esperando, idAux);
		signal(esperaAcceso[idAux]);
		wait(liberado);
	}

	procedure liberar() {
		signal(liberado);
	}
}

Process Persona[id: 0 .. N-1] {
	Fotocopiadora.solitarAcceso(id);
	fotocopiar();
	Fotocopiadora.liberar();
}

Process Empleado {
	Fotocopiadora.darAcceso();
}
```

f)

```c
Monitor Fotocopiadora[id: 0 .. 9] {
	cola f_libre, esperando;
	cond esperaAcceso[N], despertarEmpleado, liberado;

	procedure solicitarAcceso(int in id, int out idf) {
		push(esperando, id);
		signal(despertarEmpleado);
		idf = f_libre;
	}

	procedure darAcceso(int out idf) {
		int idAux;
		
		if(empty(esperando)) {
			wait(despertarEmpleado);
		}
		
		if (empty(f_libre)) {
			wait(liberado)
    }
		pop(f_libre, idf);
		pop(esperando, idAux);
		signal(esperaAcceso[idAux]);
	}

	procedure liberar(int in idf) {
		signal(liberado);
		push(f_libre, idf);
	}
}

Process Persona[id: 0 .. N-1] {
	int idf;
	
	Fotocopiadora.solitarAcceso(id, idf);
	fotocopiar();
	Fotocopiadora.liberar(idf);
}

Process Empleado {
	Fotocopiadora.darAcceso(idf);
}
```

3)

```c
Monitor corralón {
cola c;
int esperando = 0;
cond despertarEmpleado;
cond miturno[N];
cond pedido;
cond comprobanteCompra;
cond meVoy;
string lista;
string comprobante;

Procedure llegada(id: IN int){
	esperando++;
	push(c,id);
	signal(despertarEmpleado);
	wait(miturno[id]);
}

Procedure llamar(){
	int idC;
	if(esperando > 0)
		esperando--;
		pop(c,idC);
		signal(miturno[idC];
	else
		wait(despertarEmpleado);
}

Procedure HacerPedido(miLista: IN string; miComprobante: OUT string){
	lista = miLista;
	signal(pedido);
	wait(comprobanteCompra);
}

procedure atender(lista: IN string){
	comprobante = generarComprobante(lista);
	signal(comprobanteCompra);
	wait(meVoy);
}

Process Cliente[id:0..N-1]{
	corralón.llegada(id);
	corralón.hacerPedido(lista,comprobante);
}

Process Empleado{
	corralón.llamar();
	corralón.atender(lista);
}
```

4)

```c
Monitor comisión{
int llegue = 0;
cond despertarJTP;
cond despertar;
con termine;
array numerogrupo[50] = 0;
array notas[25] = 0;
cola c;
int notas = 25;
boolean estanTodos = False;

Procedure llegada(id: IN int; grupo: OUT int){
	llegue++;
	if(llegue==50)
		estanTodos = True;
		signal(despertarJTP);
	wait(despertar);
	grupo = numerogrupo[id];
}

Procedure entregarNumero(){
	if(estanTodos == False){
		wait(despertarJTP);
	for i := 1 to 50
		numerogrupo[i] = darNumero();
	signal_All(despertar);
}

Procedure avisarTermine(idgrupo: IN int){
	push(c,idgrupo);
	signal(termine);
}

Procedure entregarNota(){
	while(true)
		wait(termine);
		pop(c,tema);
		notas[tema]++;
		if(notas[tema] == 2)
			notas[tema] = nota;
			nota--;
	}
}

Process Alumno [id:0..49]{
	comisión.llegada(id,grupo);
	comisión.avisarTermine(grupo);
}

Process Jefe{
	comisión.entregarNumero();
	comisión.entregarNota();
}
```

<aside>
💡 IMPORTANTE: LA ÚNICA FORMA QUE TENGO DE ACCEDER AL ESTADO DEL MONITOR ES POR MEDIO DEL IN/OUT. SI YO INVOCO A UN PROCEDIMIENTO DEL MONITOR (DENTRO DEL PROCESS), PUEDO ENVIAR UN DATO CONOCIDO POR MI (POR IN), Y ESPERAR RECIBIR OTRO (POR OUT).

</aside>
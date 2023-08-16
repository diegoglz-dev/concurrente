# Práctica 1

1) 

```c
P1::
(1) if (x = 0) then
(2)	  y:=4 * 2;
(3)	  x:=y + 2;

P2::
(4) if (x > 0) then
(5)	  x:=x + 1;

P3::
(6) x:= (x * 3) + (x * 2) + 1;
```

a) 

- Se ejecuta P1 (1) (2) (3) finaliza con x = 10
- Se ejecuta P2 (4) (5) finaliza con x = 11
- Se ejecuta P3 (6) finaliza con x = 56

b)

- Arranca P1 (1)
- Arranca P3 (6.1) y ejecuta solo (x * 3), hasta el momento x=0
- Vuelve a P1 (2) (3) finaliza con x=10
- Vuelve a P3 y ejecuta (x * 2) + 1 y finaliza con x=21
- Se ejecuta P2 (4) (5) y finaliza con x=22

c)

- Arranca P3 (6.1) y ejecuta (x * 3), hasta el momento x=0
- Se ejecuta P1 (1) (2) (3) finaliza con x=10
- Se ejecuta P2 (4) (5) finaliza con x=11
- Vuelve a P3 (6.2) y ejecuta (x * 2) + 1 y finaliza con x=23

2) 

```c
int cant = 0;
int n = 10;
array[1 .. M]
Process recorrer
for (i = 1 .. M)
	< if (array[i] == N)
			cant++; >
```

Si este ejercicio se piensa Process recorrer [id: 0 .. M] obtendríamos peor procesamiento(en cuanto a tiempos) para un programa con solo un proceso que en realidad se resuelve de forma secuencial.

3)

a) No funciona el código de productor/consumidor.

En el caso del productor lo único que esta excluyendo es el acumulador cant y no esta restringiendo el acceso al buffer.

En el caso del consumidor pasa algo similar, aunque podría estar consumiendo algo que aun no se ha generado (es decir basura).

Solución:

```c
int cant = 0;
int pri_ocupada = 0;
int pri_vacia = 0;
int buffer[N];

Process Productor
{
	while (true)
  { // Produce elemento
		< await (cant < N);
			buffer[pri_vacia] = elemento;
			cant++; >
		pri_vacia = (pri_vacia + 1) mod N;
  }
}

Process Consumidor
{
	while (true)
  {
		< await (cant > 0);
		elemento = buffer[pri_ocupada];
		cant--; >
		pri_ocupada = (pri_ocupada + 1) mod N;
		// Consume elemento
  }
}
```

b) 

```c
int cant = 0;
int pri_ocupada = 0;
int pri_vacia = 0;
int buffer[N];

Process Productor[id: 0 .. P-1]
{
	while (true)
  { // Produce elemento
		< await (cant < N);
			buffer[pri_vacia] = elemento;
			cant++;
			pri_vacia = (pri_vacia + 1) mod N; >
  }
}

Process Consumidor[id: 0 .. C-1]
{
	while (true)
  {
		< await (cant > 0);
		elemento = buffer[pri_ocupada];
		cant--;
		pri_ocupada = (pri_ocupada + 1) mod N; >
		// Consume elemento
  }
}
```

4)

```c
buffer = cola (5)

Process Process[id: 0 .. P-1]
int elemento;
	< await (! buffer.empty())
    elemento = buffer.pop();>
	utilizarElemento(elemento);
	< buffer.push(elemento);>
	// Libera elemento
```

5) 

a)

```c
bool libre = true;

Process Persona[id: 0 .. N-1]
	< await (libre); libre = false; >
	// Usa impresora
	imprimir(documento);
	// Libera impresora
	libre = true;
```

b)

```c
int siguiente = -1;
colaImpresora c;

Process Persona[id: 0 .. N-1]
	< if (siguiente == -1) siguiente = id;
		else agregar(c, id); >

	< await (siguiente == id); >
	imprimir(documento);

	< if (empty(c)) siguiente = -1;
		else siguiente = sacar(c); >
```

c)

```c
int siguiente = -1;
colaImpresa c;

Process Persona[id: 0 .. N-1]
	< if (siguiente == -1) siguiente = id;
		else agregarOrdenado(c, id); >
	
	< await (siguiente == id); >
	imprimir(documento);

	< if (empty(c)) siguiente = -1;
		else siguiente = sacar(c); >
```

d) 

```c
boolean impresora = True;
int turno = 0;

Process Persona[id=0..N-1]{
	string documento;

	<await (turno == id); impresora = False>
	ImprimirDocumento(documento);
	impresora = True;
	turno++;
}
```

e)

```c
cola c;
int siguiente = -1;
boolean termine = False;

Process Persona [id=0..N-1]{
	string documento;

	<encolarOrdenado(c,id)>
	<await (siguiente == id)>
	Imprimir(Documento);
	termine = true;
}

Process Coordinador {
int i;

for i := 1 to N
	<await (not empty (c)); siguiente = desencolarOrdenado(c)>
	await(termine); ← chequear si puede ir sin exclusión
}
```

6)

```c
bool ok = false; colaEspecial q; int total = 0;
int cant_examen = 0; buffer[0 .. P] = -1;

Process Alumno[id: 0 .. P-1]
	< total = total + 1; >
	< await (ok); >
	// Rinde final
	< q.push(examen, id);>
	< await (buffer[id] <> -1); >

Process Profesor[id: 0 .. 2]
	int id_examen; int nota; bool seguir = true;
	
	< await (total == P); ok = true; >
	// Total el final
	while (seguir)
		< if (cant_examen < P) cant_examen++;
			else seguir = false; >

		if (seguir)
		{
			< await (!q.empty()); id_examen = q.pop(); >
			nota = corrigeExamen(examen);

			buffer[id_examen] = nota;

			< cant_examen++ >
		}

```

7) Siempre los procesos se van a ejecutar intercalado, es decir, cuando termina uno la variable turno cambia y el otro proceso puede empezar.

Las propiedades que se cumplen son:

- Exclusión mutua: a los sumo un proceso está en su SC.
- Ausencia de Deadlock (livelock): si 2 o más procesos tratan de entrar a sus SC al menos uno tendrá éxito.
- Eventual entrada: un proceso que intenta entrar a su SC tiene posibilidad de hacerlo (eventualmente lo hará).

Puede producirse Ausencia de demora innecesaria, ya que si el Process2 termina tiene que esperar al Process1 a que cambie la variable turno y si este proceso se demora por x causa, estaría produciendo la demora innecesaria del Process2.
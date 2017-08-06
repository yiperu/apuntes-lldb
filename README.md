# LLDB

### Impresiones

Impresion de Objetos (print object):

	po <Object>
	po self.view
	po [helloWordString lowercaseString]

Impresion de Escalares

	(lldb) p (int)[[self myMassiveArray] count]
	3

Algunos Comandos

Comando  | Descripcion
|------------- | ------------- |
| (lldb) br l  | Listar los breakpoint |
| (lldb) br delete <indiceBreakPoint> | delete a breakpoint |
| (lldb) br e <indiceBreakPoint> | enable a breakpoint |
| (lldb) br di <indiceBreakPoint> | disable a breakpoint |
| (lldb) b MyViewController.m:30 | set a breakpoint (ojo solo b) |

💡 Los indices dependen de lo que se muestra en la lista de breakpoint `br list`

### Symbolic Breakpoints
Add a symbolic breakpoint:

Ejemplo a los metodos - (void)viewDidLoad

```(lldb) br set -n viewDidLoad```


##### Tener en consideración que en los breakPoint existen 2 importantes características y estas a la vez tienen subcaracterísticas:

1. ***Condition***  `(BOOL)(launchOptions == NULL)`

	1.1. Esto se pone en el 1er combo box	
	
		br modify -c '(BOOL)(launchOptions == NULL)' <#IndiceBreakPoin	
		br mod -c 'self.monthToShow == 1 && !([self.images[1] isEqualToString:@"01.jpg"])' 2


![condition-visual](./img/caracteristic.png)

Example of Set a conditional breakpoint:

```
(lldb) b <NombreClase>.m:32
Breakpoint 3: where = [<NombreClase> bla bla bla]
(lldb) br mod -c "totalValue > 1000" 3
```

2. Action  :  `br command add <#> / br com add <#>`

	Subcaracteristicas de Action:
	1. AppleScript	
	2. Capture GPU Frame	
	3. `Debugger Command` // for Default
	4. Log Message	
	5. Shell Command	
	6. Sound
	
![condition-subcategory-visual](./img/action-subcaracteristic.png)		

💡 Por quieres editar un breakpoint (Visualmente) sale por defecto `Debugger Command`

**Run a `debugger command` from a breakpoint**

Ejemplo de como agregar como Action un `Debugger Command` al un breakpoint de indice 2, en este caso un
Run and debugger command from a breakpoint: backTrace:

	(lldb) br com add 2   // Luego de esto aparecera un mensaje: Enter your debugger command(s). Type ‘DONE’ to end;
	Enter your debuger command(s). Type 'DONE' to end.
	> bt // back trace
	> continue
	> DONE

##### Comandos

| Comando  | Accion |
|------------- | -------------|
| (lldb) continue | Resume Execution (play) |
| (lldb) n | Step Over |
| (lldb) s | Step In |
| (lldb) finish | Step Out |

*br  =  breakpoint

### Symbolic BreakPoint: (simbolic): br set -n <method>

	br set -n viewDidLoad
	br set -n prepareForSegue:sender:

#### Ver lista de breakPoint

	br list
💡 For go to exactly place `Shift + Cmd + o` and write `<ClassName.m:<LineNumber>>`

💡 For clear console `Cmd + K`



### Set a Conditional Break Point
	
	1ro establecer el breakpoint:
	(lldb) b BlablaViewController.m:32
	Breakpoint 3: where = bla bla bla
	(lldb) br mod -c “totalValue > 1000” 3. // Para modificar, en este caso comando *3=ID breakpoint
	Otro Ejemplo para modificar el breakpoint de ID 2
	(lldb) br mod -c ‘self.monthToshow == 1 && !([(NSString *)self.images[1] isEquaToString:@”01.jpg”])’ 2
	
**Comprobar esto con: br list  // y ver en la descripcion en la consola los IDs de los breakpoint


-

## Using Expression and variables:

💡 If you use any of  the command option, then you must place `--` at the end of option

```
(lldb) help po
     Evaluate an expression (ObjC++ or Swift) in the current program context, using user defined variables and
     variables currently in scope.
...
'po' is an abbreviation for 'expression -O  -- '
Example: Muestra el mismo resultado
💡 po self.monthToShow = expr -o -- self.monthToShow

```

Example:  

	(lldb) expr -L -- 5+5  // Esto genera una variable ($0 o $1 o $<#>) con el valor de 10

⚒ Tambien podemos crear una variable cualquiera:

	(lldb) expr int $monthToShow
	
⚒ Can type “raw” Objetive-C code into LLDB parser which be evaluated in real time.

	(lldb) expr self.view.hidden = YES
	
⚒ Me ha pasado que cuando lo tengo en modo `Debug View Hierarchy` solo tengo la direccion de memoria:
	
	expr ((UILabel *)0x7ff171d05580).hidden = TRUE  // con la direccion
	
⚒ May change a running state of your program

	(lldb) expr self.player.lives = 100
	
⚒ Can be used to log messages to the console 

	(lldb) expr (void) NSLog(@“hello word!”)  // Mensajes en consola
	
⚒ Can call methods on Objets

	(lldb) expr (BOOL) [self.myArray containsObject:@“CarKeys”]
	
⚒ Print out structures in your code

	(lldb) expr -- (CGRect) [self.view frame]
	
⚒ Take “shortcuts” in your app

	(lldb) expr [self prepareForSegue:@“mySegue” sender:nil]
	
⚒ Para asignar cualquier variable interna puedo utilizar de frente expresion

	(lldb) expr <variable> = <Any Value compatible> // Funciona en runtime

#### LLDB Variables: 

💡 Variable names must have a `type` and begin with a `$`

	(lldb) expr int $meaningOfLife = 42
	
⚒ Can be used with other expressions

	(lldb) expr  100 + $meaningOfLife
	
⚒ Some expressions provide results in LLDB variables

	(lldb) $0 = 142
	(lldb) p $0 + 200
	(lldb) $1 = 242

Create and run code on the fly

	(lldb) expr NSString * $json = [self fetchRemoteData];
	
	(lldb) expr NSData * $data = [$json dataUsingEncoding:4]
	
	(lldb) expr NSDictionary * $parsedJson = [[NSJSONSerialization JSONObjectWithData:$data options:0 error:NULL]
	
	(lldb) po parsedData

Otro Ejemplo:

	po [[NSString alloc] initWithData:response.responseData encoding:4]

💡 Un ejemplo de como aplicar esto:

```
1.- Se creo 1ro un br
2.- se le modifico su action
	br com add 1
	> expression [self performSegueWithIdentifier:@"GoToPaper"] sender:nil]
	> continue
	> DONE
3.- Se puso c de continuar
4.- Se simuló la accion para ver los resultados
```

✅ Hubo un ejemplo muy interesante donde en runtime se hizo debug, y se pudo asignar un valor a una variable del sistema, y luego continuar el proceso, lo que llaman `run code on the fly`.


## Backtrace, Thread and Frame

### backtrace
	(lldb) bt      // Muestra thread actual con sus frames
	(lldb) bt all  // Muestra todos los threads con sus frames

### thread

	(lldb) thread backtrace			// veo que muestra lo mismo que: bt
	(lldb) thread backtrace all	// veo que muestra lo mismo que: bt all
	
	(lldb) thread list				// Muestra la lista de threads
	
	(lldb) thread select 1
	
	⚒ Continue a certain line (much like a breakpoint)
	(lldb) thread until 100

	⚒ Return from function with custom value
	(lldb) thread return @“Custom Return String”

### frame

	⚒ Inspect local variable in realtime
	(lldb) frame variable
	
	⚒ Para ver una variable particular : frame variable <variable> // Example:
	(lldb) frame variable self
	💡 "frame variable" is not a full expression parser but does support operations like &, *, [], -> 
	
	⚒ Select another frame
	(lldb) frame select 2
	
	⚒ Select a new frame relative to the curretn frame
	(lldb) frame select -relative -1

Demo 5'

💡 Para ver visualmente en Xcode los thread con sus frames `Cmd + 6` = `Show the debug navigator`

![Show-the-debug Navigator](./img/show-debug-navigator.png)


**expr  =  expression
Ejemplos:
	expression -- (CGRect)[self.view frame]
	expr -o -- self.view  =  po self.view	
**frame
	frame variable
	frame variable self
	frame select -relative 1	
**thread
	thread list
	thread select 1
	thread select -1
	
	
**Hay una demostracion donde sigue estos comandos:
bt
thread list
thread select 1
bt
frame select 7
frame variable
frame variable sender
po sender
frame select -relative 1
frame select -relative -1
.
.
.
thread until 63
frame variable
thread return YES
n
frame variablea




def function   // definir función en python

type summary add -P CGRect
type format add
type filter add
type category list
type filter list
type summary list


Chisel:
https://github.com/facebook/chisel

border self.view

expr (void) [self.view setHidden:YES]  = hide self.view
show self.view  // para mostrar la vis
caflush   // para actualizar la vista en caso (en el eje que no se oculta en real time)
border self.view
border - - color orange  self.view
border [self.navigationItem titleView]
border --color orange [self.navigationItem titleView]
mask [self.navigationItem titleView]
unmask [self.navigationItem titleView]

**pca (print core animation)
pviews

vs self.view  // Para salir la opción q

presponder self.view

visualize self.view

help -a

help <Any>



**Cuando tengo en modo debug view Hierarchy: puedo acceder a travez de su dirección
po ((UILabel *)0x7ff171d05580).text




QuickLook
https://github.com/ryanolsonk/LLDB-QuickLook

Ima25
RJu17

[Pagina Oficial: The LLDB Debugger](https://lldb.llvm.org/)



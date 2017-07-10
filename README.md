# LLDB

### Impresiones

Impresion de Objetos:

	po <Object>

Impresion de Escalares

	(lldb) p (int)[[self myMassiveArray] count]
	3

Algunos Comandos

Comando  | Descripcion
|------------- | ------------- |
| (lldb) br l  | Listar los breakpoint |
| (lldb) br delete 1 | delete a breakpoint |
| (lldb) br e 1 | enable a breakpoint |
| (lldb) br di 1 | disable a breakpoint |
| (lldb) b MyViewController.m:30 | set a breakpoint (ojo solo b) |

Add a symbolic breakpoint:

Ejemplo a los metodos - (void)viewDidLoad

```(lldb) br set -n viewDidLoad```


#####Tener en consideración que en los breakPoint existen 2 importantes características y estas a la vez tienen subcaracterísticas:

1. ***Condition***  `(BOOL)(launchOptions == NULL)`

	1.1. Esto se pone en el 1er combo box	
	`br modify -c '(BOOL)(launchOptions == NULL)'`

2. Action  :  `br command add <#> / br com add <#>`

	1. AppleScript	
	2. Capture GPU Frame	
	3. Debugger Command	
	4. Log Message	
	5. Shell Command	
	6. Sound
		
**Run a debugger command from a breakpoint**

Ejemplo de como agregar como Action un Debugger command al un breakpoint de indice 2, en este caso un
Run and debugger command from a breakpoint: backTrace:

	(lldb) br com add 2   // Luego de esto aparecera un mensaje: Enter your debugger command(s). Type ‘DONE’ to end;
	Enter your debuger command(s). Type 'DONE' to end.
	> bt // back trace
	> continue
	> DONE

#####Comandos

| Comando  | Accion |
|------------- | -------------|
| (lldb) continue | Resume Execution (play) |
| (lldb) n | Step Over |
| (lldb) s | Step In |
| (lldb) finish | Step Out |

*br  =  breakpoint

###Symbolic BreakPoint: (simbolic): br set -n <method>

	br set -n viewDidLoad
	br set -n prepareForSegue:sender:

####Ver lista de breakPoint

	br list

-

###Set a Conditional Break Point
	
	1ro establecer el breakpoint:
	(lldb) b BlablaViewController.m:32
	Breakpoint 3: where = bla bla bla
	(lldb) br mod -c “totalValue > 1000” 3. // Para modificar, en este caso comando *3=ID breakpoint
	Otro Ejemplo para modificar el breakpoint de ID 2
	(lldb) br mod -c ‘self.monthToshow == 1 && !([(NSString *)self.images[1] isEquaToString:@”01.jpg”])’ 2
	
**Comprobar esto con: br list  // y ver en la descripcion en la consola los IDs de los breakpoint


-

##Using Expression and variables:

// ** If you use any of  the command option, then you must place  - -  at the end of option
Example:  

	(lldb) expr -L -- 5+5  // Esto genera una variable con el valor de 10
**Esto tambien: expr 5+6  = $0 = 11
**Tambien podemos crear una variable cualquiera:

	(lldb) expr int $monthToShow
// Can type “raw” Objetive-C code into LLDB parser which be evaluated in real time.

	(lldb) expr self.view.hidden = YES
// expr ((UILabel *)0x7ff171d05580).hidden = TRUE  // con la direccion
// May change a running state of your program

	(lldb) expr self.player.lives = 100
// Can be used to log messages to the console 

	(lldb) expr (void) NSLog(@“hello word!”)  // Mensajes en consola
// Can call methods on Objets

	(lldb) expr (BOOL) [self.myArray containsObject:@“CarKeys”]
// Print out structures in your code

	(lldb) expr -- (CGRect) [self.view frame]
// Take “shortcuts” in your app

	(lldb) expr [self prepareForSegue:@“mySegue” sender:nil]
// Para asignar cualquier variable interna puedo utilizar defrente expresion

	(lldb) expr <variable> = <Any Value compatible> // Funciona en runtime

LLDB Variables: Variable names must have a type and begin with a $

	(lldb) expr int $meaningOfLife = 42
//Can be used with other expressions

	(lldb) expr  100 + $meaningOfLife
// Some expressions provide results in LLDB variables

	(lldb) $0 = 142


Create and run code on the fly

	(lldb) expr NSString * $json = [self fetchRemoteData];
	
	(lldb) expr NSData * $data = [$json dataUsingEncoding:4]
	
	(lldb) expr NSDictionary * $parsedJson = [[NSJSONSerialization JSONObjectWithData:$data options:0 error:NULL]
	
	(lldb) po parsedData

Otro Ejemplo:

	po [[NSString alloc] initWithData:response.responseData encoding:4]

// Hubo un ejemplo muy interesante donde en tiempo de debug se pudo asignar un valor a una variable del sistema, lo que llaman run code on the fly

**Despues de llamar a un metodo, puedes continuar con continue:
expr [self performSegueWithIdentifier:@”GoToPaper” sender:nil]
c
**Para terminanar: DONE




##Backtrace, Thread and Frame

	(lldb) bt
	(lldb) bt all

	(lldb) thread backtrace
	(lldb) thread backtrace all
	(lldb) thread list
	(lldb) thread select 1
	(lldb) thread until 100
	(lldb) thread return @“Custom Return String”

**Frame  // Inspect local variable in realtime

	(lldb) frame variable
	(lldb) frame variable self
	(lldb) frame select 2  // Select another frame
	(lldb) frame select -relative -1  // Select a new frame relative to the current frame

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



Falta desde el video 4 WatchPoint Script Command





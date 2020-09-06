# Streams

Streams (corriente en español), es como un tubo, que se introduce (emite) información por un lado del tubo, y existe alguien (oyente) que capta esa información que saldrá por el otro extremo del tubo.

## Crear un Stream
Para crear un Stream, necesitamos crear un streamController, este controlador permite enviar datos, errores y eventos del stream.
```Dart

// Se indica que por el "tubo" pasarán números de tipo double
StreamController<double> controller = StreamController<double>();

//acceder al stream que el controlador esta controlando
Stream stream = controller.stream;
```
## Escuchar a un Stream
Como se menciono al inicio, existe un oyente que capta la información que sale por el tubo, esto se conoce como una subscripción o escuchar a un stream. Para suscribirse a un stream se hace uso de la función listen.
```dart
StreamSubscription streamSubscription = stream.listen(
    // función que se ejecuta cuando se recibe una data
    (value){
        print('printing the value $value');
    },
    // función que se ejecuta cuando sucede un error
    onError : (error){
        print('Error');
    },
    // cancelOnError es true por defecto, quiere decir que si se recibe un error, es oyente es cancelado, pero si es falso, la suscripción continuará
    cancelOnError : false,
    //función que se ejecuta cuando se cierra el stream
    onDone : (){
        print('It\'s done');
    }
);

// el objeto streamSubscription tiene los siguientes métodos
/* .pause()
   * .resume()
   * .cancel()*/
```
## Enviar/emitir data a un stream
Esto se hace gracias al método ```.add()``` del streamController.
```dart
// emitir/enviar un valor al stream
controller.add(13);

// esto hará que se ejecute la primera función del método listen
//printing the value 12

//también se puede agregar un valor de la siguiente forma
controller.sink.add(13);

```
```.add()``` y ```.sink.add()``` hacen internamente lo mismo, pero es conveniente usar el segundo en caso se valla a pasar como parámetro de otro objeto para prevenir a una clase de acceder a propiedades que no debería.

## Suscripciones
Por defecto un stream puede tener solo un listener o suscriptor durante toda su vida.
```dart
// al agregar un nuevo listener al stream, producirá un error
stream.listen((value){
    print('2 printing the value $value');
  });

//Uncaught Error: Bad state: Stream has already been listened to.
```
Para tener varios listeners a la vez, podemos usar broadcast, podemos hacer uso de la siguiente forma:
```dart
//forma 1
StreamController<double> controller = StreamController<double>.broadcast();

//forma 2
Stream stream = controller.stream.asBroadcastStream();

// ya no habría problemas al colocar otro listener
stream.listen((value){
    print('2 printing the value $value');
});
```
## Manipulación de datos
Lo interesante de los streams es como podemos manipular la llegada de la información
```dart
//podemos filtrar los números que son pares y que nos retorne un string en vez de un double
Stream stream = controller.stream.where((i)=>i%2 == 0).map((i)=>'map double $i');

// si agregamos los siguientes datos
controller.add(12);
controller.add(13);
controller.add(14);
controller.add(16);

// se imprimirá lo siguiente
//printing the value map double 12
//printing the value map double 14
//printing the value map double 16
```

## Ejemplo
Tomando el ejemplo del video tutorial de Streams del canal Flutter en Youtube [link](https://www.youtube.com/watch?v=nQBpOIHE4eE), podemos ver como podemos crear nuestro stream y ejecutar una función cada vez que se reciba nueva data.
```dart
import 'dart:async';

void main() async{
  NumberCreator creator = NumberCreator();
  
  Stream stream = creator.stream;
  
  stream.listen((value){
    print('Valor del stream $value');
  });
}

class NumberCreator{
  int _number = 1;
  StreamController<int> _streamController = StreamController<int>();
  
  NumberCreator(){
    Timer.periodic(Duration(seconds : 1),(t){
      _streamController.sink.add(_number);
      ++_number;
    });
  }
  
  Stream get stream => _streamController.stream;
}
```
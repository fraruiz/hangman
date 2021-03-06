# Application Design Document

## Problema a Resolver
Implementar una aplicación para jugar al juego clásico del "ahorcado". La aplicación selecciona una palabra de su lista de palabras, y el usuario debe adivinar la palabra arriesgando letras una por una.

## Requerimientos
- Lista de palabras
- Cada vez que el usuario propone una letra que forma parte de la palabra, se deben mostrar todas las apariciones de la letra en la palabra.
- El usuario tiene una cierta cantidad de intentos fallidos, luego de los cuales pierde el juego
- Interfaz gráfica
- Proponer varios niveles de dificultad, que consistan por ejemplo en palabras más difíciles o bien una menor cantidad de intentos.
- Permitir que el idioma de la aplicación sea configurable, para adivinar por ejemplo
palabras en inglés.
- Hacer una aplicación para que el usuario sea quien propone la palabra, y la aplicación trata de adivinarla!

## Solución Técnica

### Arquitectura
La **arquitectura hexagonal**, o arquitectura de puertos y adaptadores, es un patrón arquitectónico utilizado en el diseño de software. Su objetivo es crear componentes de aplicaciones débilmente acoplados que puedan conectarse fácilmente a su entorno de software por medio de puertos y adaptadores. La misma está conformada por cuatro capas importantes: Domain; Application; Infrastructure; App

![Clean Architecture](https://miro.medium.com/max/700/1*2nqUx2LoWvC2sK91HVZcFQ.png "Clean Architecture")

### Ventajas
##### Independencia de Frameworks
La arquitectura no depende de la existencia de alguna libreria o frameworks de software cargado de funciones. Esto le permite utilizar dichos frameworks como herramientas.

##### Testing
Gracias al desacople y cohesión de nuestra aplicación, las reglas de negocio se pueden probar sin la interfaz de usuario, la base de datos, el servidor web o cualquier otro elemento externo.

##### UI - Interfaz de usuario
La interfaz de usuario puede cambiar fácilmente, sin cambiar el resto del sistema. Una interfaz de usuario de escritorio podría reemplazarse con una interfaz de usuario de consola, por ejemplo, sin cambiar las reglas comerciales.

##### Idenpendencia de la persistencia de datos
Podemos cambiar nuestra implementación de persistencia de datos sin que nuestras reglas de negocios sean afectadas, ya que no están vinculadas a la base de datos.

Actualmente, en la aplicación, persistimos todos los datos en memoria. En el día de mañana podemos implementar cualquier otra base de datos sin tocar la lógica de negocios.

#### Capas
##### Domain
Conceptos y entidades que pertenecen a nuestro contexto de la problemática a solucionar (Word, Language, Difficult, Session), y reglas de negocio que vienen determinadas en exclusiva por los requerimientos (servicios de dominio).

##### Application
La capa de aplicación es donde están los casos de uso de nuestra aplicación.

##### Infrastructure
Es la capa más externa de nuestra aplicación, teniendo interacción permanente con input y output de nuestras implementaciones de infraestructura. Por ej: Persistencia de datos, interfaces gráficas y/o servicios externos.

En esta capa vivirán las implementaciones de las interfaces que definiremos a nivel de dominio. Es decir, nos apoyaremos en la D de SOLID (Principio de Inversión de dependencias), para poder desacoplarnos de las dependencias externas.

#### Regla de dependencia
La regla de dependencia es una norma que hay que seguir para que nuestra aplicación tenga un bajo acoplamiento y una alta cohesión. 

Esta regla nos dice que el código que está en cada una de nuestras capas solo deberá conocer las clases que se ubican en la capa inmediatamente siguiente. Es decir, entendemos el orden de las capas desde fuera hacia dentro del círculo: Infraestructura -> Aplicación -> Dominio.

La norma nos proporciona es la posibilidad de cambiar elementos de nuestras capas más externas sin que las internas se vean afectadas. Esto adquiere más sentido ya que logramos que la aplicación se escalable y mantenible, logrando que cambios en las capas mas externas no afecten a nuestro dominio.

#### Estructura
El proyecto está estructurado mediante las carpetas:
- **main**:
	- **app**: Contiene todos nuestros puntos de entradas de la aplicación con el exterior. Gracias a la clase Starter y Application, mediante argumentos podemos iniciar la aplicación swing o de consola.
	- 	**core**: Contiene el Core de nuestra aplicación. A su vez, todas los paquetes que contiene se los consideran **módulos**, donde estos pueden interactuar entre sí mediante servicios de dominio.
		- **sessions**: Módulo que contiene la capa de infrastructure, application & domain de nuestro modelado de dominio Sessions
		- **word**: Módulo que contiene la capa de infrastructure, application & domain de nuestro modelado de dominio Word
- **test**: En esta carpeta alojamos todos los tests unitarios de la aplicación.


```bash
src
├── main
│   ├── ar
│   │   └── edu
│   │       └── ungs
│   │           └── hangman
│   │               ├── apps
│   │               │   ├── Starter.java
│   │               │   ├── cli
│   │               │   │   └── CliApplication.java
│   │               │   ├── shared
│   │               │   │   └── Application.java
│   │               │   └── swing
│   │               │       ├── SwingApplication.java
│   │               │       └── views
│   │               │           ├── GuesserView.java
│   │               │           ├── HangmanView.java
│   │               │           ├── MainView.java
│   │               │           └── View.java
│   │               └── core
│   │                   ├── sessions
│   │                   │   ├── application
│   │                   │   │   ├── SessionResponse.java
│   │                   │   │   ├── create
│   │                   │   │   │   ├── SessionDefaultCreator.java
│   │                   │   │   │   └── SessionGuessCreator.java
│   │                   │   │   ├── find
│   │                   │   │   │   └── SessionFinder.java
│   │                   │   │   ├── guess
│   │                   │   │   │   └── SessionGuesser.java
│   │                   │   │   └── tries
│   │                   │   │       └── SessionTryer.java
│   │                   │   ├── domain
│   │                   │   │   ├── DomainSessionFinder.java
│   │                   │   │   ├── DomainSessionTryer.java
│   │                   │   │   ├── Session.java
│   │                   │   │   ├── SessionFinished.java
│   │                   │   │   ├── SessionNotExists.java
│   │                   │   │   └── SessionRepository.java
│   │                   │   └── infrastructure
│   │                   │       └── persistence
│   │                   │           └── inmemory
│   │                   │               └── InMemorySessionRepository.java
│   │                   └── words
│   │                       ├── application
│   │                       ├── domain
│   │                       │   ├── Difficult.java
│   │                       │   ├── DomainWordRandomPicker.java
│   │                       │   ├── Language.java
│   │                       │   ├── Word.java
│   │                       │   ├── WordNotExists.java
│   │                       │   └── WordRepository.java
│   │                       └── infrastructure
│   │                           └── persistence
│   │                               └── inmemory
│   │                                   └── InMemoryWordRepository.java
│   └── resources
│       ├── SansitaSwashed.ttf
│       ├── hanged.png
│       └── ungs.jpg
└── test
    ├── ar
    │   └── edu
    │       └── ungs
    │           └── hangman
    │               ├── apps
    │               └── core
    │                   ├── sessions
    │                   │   ├── application
    │                   │   │   ├── create
    │                   │   │   │   ├── SessionDefaultCreatorTest.java
    │                   │   │   │   └── SessionGuessCreatorTest.java
    │                   │   │   ├── find
    │                   │   │   │   └── SessionFinderTest.java
    │                   │   │   ├── guess
    │                   │   │   │   └── SessionGuesserTest.java
    │                   │   │   └── tries
    │                   │   │       └── SessionTryerTest.java
    │                   │   ├── domain
    │                   │   │   ├── DomainSessionFinderTest.java
    │                   │   │   ├── DomainSessionTryerTest.java
    │                   │   │   └── SessionMother.java
    │                   │   └── infrastructure
    │                   │       └── InMemorySessionRepositoryTest.java
    │                   └── words
    │                       ├── application
    │                       ├── domain
    │                       │   ├── DifficultMother.java
    │                       │   ├── DomainWordRandomPickerTest.java
    │                       │   ├── LanguageMother.java
    │                       │   └── WordMother.java
    │                       └── infrastructure
    │                           └── InMemoryWordRepositoryTest.java
    └── resources
        └── application.properties

```
### Repositories
Un repositorio, como dice la palabra, es una abstracción a la persistencia colecciones de datos de un objeto de dominio. En el proyecto, tenemos dos implementaciones:
1. [InMemorySessionRepository](https://github.com/fraruiz/hangman/blob/master/src/main/ar/edu/ungs/hangman/core/sessions/infrastructure/persistence/inmemory/InMemorySessionRepository.java "InMemorySessionRepository")
2. [InMemoryWordRepository](https://github.com/fraruiz/hangman/blob/master/src/main/ar/edu/ungs/hangman/core/words/infrastructure/persistence/inmemory/InMemoryWordRepository.java "InMemoryWordRepository")

### Servicios de Aplicación
Representan de forma atómica un caso de uso de nuestro sistema.
1. [SessionDefaultCreator](https://github.com/fraruiz/hangman/blob/master/docs/services/application/session-default-creator.md "SessionDefaultCreator")
2. [SessionFinder](https://github.com/fraruiz/hangman/blob/master/docs/services/application/session-finder.md "SessionFinder")
3. [SessionGuessCreator](https://github.com/fraruiz/hangman/blob/master/docs/services/application/session-guess-creator.md "SessionGuessCreator")
4. [SessionGuesser](https://github.com/fraruiz/hangman/blob/master/docs/services/application/session-guesser.md "SessionGuesser")
5. [SessionTryer](https://github.com/fraruiz/hangman/blob/master/docs/services/application/session-tryer.md "SessionTryer")

### Servicios de Dominio
Los servicios de dominio representan una agrupación de lógica de negocio que podremos reutilizar desde múltiples Servicios de Aplicación.
1. [DomainSessionFinder](https://github.com/fraruiz/hangman/blob/master/docs/services/domain/domain-session-finder.md "DomainSessionFinder")
2. [DomainSessionTryer](https://github.com/fraruiz/hangman/blob/master/docs/services/domain/domain-session-tryer.md "DomainSessionTryer")
3. [DomainWordRandomPicker](https://github.com/fraruiz/hangman/blob/master/docs/services/domain/domain-word-random-picker.md "DomainWordRandomPicker")

## UML
### Apps
![Apps UML](https://github.com/fraruiz/hangman/blob/master/docs/assets/apps.png?raw=true)

### Core
![Core UML](https://github.com/fraruiz/hangman/blob/master/docs/assets/core.png?raw=true)
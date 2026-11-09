# Grupo 1: TDD y pruebas unitarias con el laboratorio de calculadora

**Integrantes:** Shirley, Miguel y Sebastián

## Propósito de la exposición

Explicar con palabras sencillas qué es el desarrollo guiado por pruebas (TDD), qué son las pruebas unitarias, para qué sirven y cómo se escriben. La demostración se hace sobre el laboratorio 08, que contiene `Calculadora.java`, `CalculadoraTest.java` y JUnit 5 configurado en Maven.

## 1. ¿Qué es TDD?

TDD viene de *Test Driven Development* y significa desarrollo guiado por pruebas. La idea es escribir primero una prueba que describa lo que esperamos que haga una función. Luego implementamos la función y usamos la prueba para comprobar el resultado.

El ciclo se suele llamar **Rojo, Verde, Refactorizar**:

1. **Rojo:** escribimos la prueba antes de tener la funcionalidad y la ejecutamos. Debe fallar porque la función todavía no existe o no cumple la regla.
2. **Verde:** implementamos lo mínimo necesario para que la prueba pase.
3. **Refactorizar:** ordenamos o mejoramos el código sin cambiar su comportamiento y ejecutamos otra vez todas las pruebas.

La falla inicial es útil: muestra que la prueba detecta que todavía falta implementar el comportamiento pedido.

## 2. ¿Qué es una prueba unitaria?

Es una comprobación automática de una parte pequeña del programa, normalmente un método. La prueba prepara una entrada, llama al método y compara lo que devolvió con lo que se esperaba. También puede comprobar que una entrada incorrecta produzca un error definido.

Las pruebas sirven para comprobar reglas, encontrar errores pronto, dejar ejemplos de cómo se debe comportar el código y asegurarnos de que un cambio no dañe funciones que ya servían. Una prueba que pasa solo confirma los casos que esa prueba revisa; no demuestra que el programa completo no tenga errores.

## 3. Cómo se escribe una prueba en este laboratorio

El proyecto usa Java 17 y JUnit 5. Las pruebas están en `src/test/java/co/umariana/CalculadoraTest.java`, en el mismo paquete que la calculadora. El método anotado con `@BeforeEach` crea una calculadora nueva antes de cada prueba. `@Test` marca cada caso como una prueba.

El patrón sencillo es **Preparar, Actuar, Comprobar**:

```java
@Test
void sumarPositivos() {
    // Preparar: calc ya fue creada por @BeforeEach
    int resultado = calc.sumar(2, 3); // Actuar
    assertEquals(5, resultado);       // Comprobar
}
```

`assertEquals(esperado, real)` compara el resultado esperado con el real. También encontramos `assertTrue` y `assertFalse` para valores booleanos, y `assertThrows` para verificar excepciones.

## 4. Demostración en vivo: agregar factorial

La calculadora ya tiene suma, resta, multiplicación, división y verificación de paridad. Para mostrar cómo se agrega una funcionalidad con TDD, añadimos factorial.

El factorial de un entero positivo es el producto de ese número y todos los enteros positivos menores que él. Por ejemplo: `5! = 5 × 4 × 3 × 2 × 1 = 120`. Por definición, `0! = 1` y `1! = 1`. En nuestro ejemplo aceptamos de 0 a 20, porque el resultado se guarda en un `long`; fuera de ese rango se lanza `IllegalArgumentException`.

### Rojo: escribir primero la prueba

En `CalculadoraTest.java`, dentro de la clase `CalculadoraTest`, agregamos:

```java
@Test
@DisplayName("factorial: calcula el producto de los enteros positivos")
void factorialPositivo() {
    assertEquals(120L, calc.factorial(5));
}
```

Al ejecutarla antes de agregar el método, no compila porque `factorial` aún no existe. Esa es la primera etapa: la prueba expresa el comportamiento que falta.

### Verde: implementar lo mínimo

En `Calculadora.java` agregamos el método:

```java
public long factorial(int n) {
    if (n < 0 || n > 20) {
        throw new IllegalArgumentException("El factorial debe estar entre 0 y 20");
    }
    long resultado = 1;
    for (int i = 2; i <= n; i++) {
        resultado *= i;
    }
    return resultado;
}
```

Al ejecutar de nuevo, `factorial(5)` debe producir 120 y pasar la prueba.

### Ampliar los casos y comprobar errores

También agregamos pruebas para los casos base y para valores que rechazamos:

```java
@Test
void factorialCasosBase() {
    assertEquals(1L, calc.factorial(0));
    assertEquals(1L, calc.factorial(1));
}

@Test
void factorialFueraDeRango() {
    assertThrows(IllegalArgumentException.class, () -> calc.factorial(-1));
    assertThrows(IllegalArgumentException.class, () -> calc.factorial(21));
}
```

`assertThrows` ejecuta la llamada y comprueba que se lance el tipo de excepción esperado. Así cubrimos entradas válidas, casos límite y entradas inválidas.

### Refactorizar y volver a ejecutar

Si organizamos o simplificamos el método, volvemos a ejecutar la suite completa para comprobar que suma, resta, multiplicación, división, paridad y factorial siguen comportándose como esperamos.

## 5. Relación con el laboratorio 08

El `CalculadoraTest.java` original ya contiene casos para las operaciones implementadas. Por ejemplo:

- suma con positivos, cero y negativos;
- división con resultado decimal;
- división entre cero y el mensaje de error esperado;
- números pares, impares, cero y negativos.

Cada `@Test` es un ejemplo de una regla observable. La prueba de división entre cero muestra cómo verificar no solo que hay un error, sino también su mensaje. En la demostración añadimos una regla nueva de la misma manera para `factorial`.

El README original del laboratorio indica que sus pruebas iniciales no deben modificarse mientras se resuelven los ejercicios de calentamiento. En esta exposición añadimos los casos nuevos al final de `CalculadoraTest.java` solo para enseñar cómo se prueba una nueva función; conservamos todas las pruebas existentes. Si el docente exige mantener el archivo original intacto para la entrega evaluada, se puede presentar este bloque de pruebas como ejemplo aparte y no incluirlo en la entrega.

## 6. Cómo ejecutar la demostración

Desde una terminal abierta en la carpeta del laboratorio 08:

```bash
mvn test
```

Para ejecutar solo las pruebas de la calculadora:

```bash
mvn -Dtest=CalculadoraTest test
```

Después de cada cambio se puede repetir el comando. En la presentación, muestren primero la prueba nueva, expliquen por qué falla antes del método, agreguen la implementación y finalmente ejecuten todas las pruebas.

## 7. Reparto sugerido de la presentación

**Shirley — conceptos:** explica TDD, el ciclo Rojo, Verde, Refactorizar y define prueba unitaria usando la suma como ejemplo.

**Miguel — demostración:** agrega la prueba de `factorial(5)`, muestra el fallo inicial, implementa el método y ejecuta las pruebas.

**Sebastián — casos y conexión:** explica `0!`, `1!`, entradas negativas y `assertThrows`; conecta los ejemplos con las pruebas de división y paridad que ya existen en `CalculadoraTest.java`.

**Cierre conjunto:** “Una prueba automática convierte una regla en una comprobación repetible. TDD propone escribir primero esa comprobación, implementar después y volver a verificar todo al cambiar el código.”

## 8. Preguntas posibles

**¿Una prueba que pasa significa que el programa es perfecto?**  
No. Significa que pasó para los casos específicos que verificamos.

**¿Por qué se prueba el 0?**  
Porque es un caso límite y factorial tiene una definición especial: `0! = 1`.

**¿Para qué sirve `assertThrows`?**  
Para comprobar que una operación inválida lance la excepción esperada.

**¿Qué diferencia hay entre probar una función manualmente y con una prueba unitaria?**  
La prueba unitaria queda guardada en el proyecto, puede ejecutarse muchas veces y avisa automáticamente si un cambio rompe el comportamiento.

## Referencias

- Laboratorio 08: `README.md`, `pom.xml`, `src/main/java/co/umariana/Calculadora.java` y `src/test/java/co/umariana/CalculadoraTest.java`.
- JUnit 5 User Guide: https://junit.org/junit5/docs/current/user-guide/
- Kent Beck, *Test-Driven Development: By Example*, Addison-Wesley, 2002.

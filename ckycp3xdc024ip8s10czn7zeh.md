## Loops Asíncronos: Mapping Promise.all

En JavaScript hay dos formas básicas de hacer loops con operaciones asíncronas.

1. Promise.all
2. Reduce

En este post voy a explicar para que sirve `Promise.all()` y como usarlo de una mejor forma.



### Promise.all

El método `Promise.all([])` recibe un array de promesas y si todas se completan correctamente, nos va a devolver una promesa terminada correctamente. Si *solo una* de las promesas del array falla, `Promise.all()` nos va a devolver un error.


Por ejemplo:
```
const p1 =  axios.get("http://httpstat.us/200");
const p2 =  axios.get("http://httpstat.us/200");
const p3 =  axios.get("http://httpstat.us/400");

try {
  const result = await Promise.all([p1, p3, p2]);
  console.log("SUCCESS");
} catch (e) {
  console.log("ERROR");
}
// ERROR
```

En el caso de arriba la salida sera `ERROR` puesto que la segunda promesa a la que llamamos `p3` devuelve error 400

Si en lugar de eso solo llamamos a las dos primeras promesas
```
const p1 =  axios.get("http://httpstat.us/200");
const p2 =  axios.get("http://httpstat.us/200");
//const p3 =  axios.get("http://httpstat.us/400");

try {
  const result = await Promise.all([p1, p2]);
  console.log("SUCCESS");
} catch (e) {
  console.log("ERROR");
}
// SUCCESS
```
La salida en la consola sería `SUCCESS` y `result` es un array con las respuestas de cada una de las promesas.

Hay una mejor forma de trabajar con `Promise.all()`
Normalmente no vamos a querer construir un array declarando cada uno de los valores por separado. Demasiado trabajo, demasiado espacio para el error. Lo que queremos hacer es un loop que nos construya un array usando `.map`.

En ese caso podríamos construir la siguiente función

```
const mapAsync = (arr,fn) => Promise.all(arr.map(fn))
```

Con esta función ya no necesitamos tener un array de promesas. Podemos tener un array de objetos con la información necesaria y trabajar con `Promise.all`de una forma más sencilla.

Veamos el ejemplo:
```
const mapAsync = (arr, fn) => Promise.all(arr.map(fn));

const array = [{ status: 200 }, { status: 204 }, { status: 400 }];

try {
  const result = await mapAsync(array, async (item) => {
    const response = await axios.get(`http://httpstat.us/${item.status}`);
    return response.status;
  });
  console.log(result)
} catch (e) {
  console.log("ERROR");
}
//ERROR
```
Este ejemplo siempre devolverá `ERROR` por que una de las promesas está pidiendo un status 400. 

Pero lo que nos interesa es la forma en la que hemos construido la petición. Ahora en nuestra variable `arrray` hemos metido solo los status que queremos pedir y después construimos las solicitudes con un `arr.map`  pasándole a cada status su url correspondiente.

Podríamos directamente construir un array con urls y status o urls y datos y hacer peticiones masivas sin preocuparnos por construir un array de promesas. Este array se va a construir solo cuando llamemos a mapAsync.

Ejemplo:

```
const mapAsync = (arr, fn) => Promise.all(arr.map(fn));

const array = [
{ status: 200, url: "http://httpstat.us/"}, 
{ status: 204, url: "http://httpstat.us/" }, 
{ status: 400, url: "http://httpstat.us/" }
];

try {
  const result = await mapAsync(array, async (item) => {
    const response = await axios.get(`${item.url}${item.status}`);
    return response.status;
  });
  console.log(result)
} catch (e) {
  console.log("ERROR");
}
//ERROR
```





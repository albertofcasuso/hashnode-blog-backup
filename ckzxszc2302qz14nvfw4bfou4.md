## Desarrollando un login con Ethereum - Parte I

Estas últimas semanas he estado trabajando en desarrollar un login con Ethereum. Es la primera vez que entro en contacto con el mundo Blockchain y el reto no es pequeño. Algo de esta clase, en este momento, es todavía territorio poco explorado.

Las características de un login con Ethereum son  bastante diferentes de un login social basado en un servidor de identidades.

## Login social

Esto es sencillo. Seguramente has visto más de una web con un link de "Haz login con Facebook" "Haz login con Google" Bien. Cuando haces click en uno de esos botones, eres redirigido a la página de Facebook o Google, donde te logueas con tu ID y de nuevo redirigido al servicio original.

Facebook o Google, lo que hacen es decirle al servicio que efectivamente tienes una cuenta válida con ellos. El servicio te registra como un nuevo usuario y te autentica.

![auth_code_grant-1024x486.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1642417964687/9LMTmw2FI.jpeg)

Este login está basado en un servidor de identidades que se comunica con otros servicios y gestiona identidades. Toda la autoridad está delegada en este servidor y todas las autorizaciones son llevadas a cabo por él.
 
Cuando nosotros enviamos usuario y contraseña (o hacemos click en login con FB y Google), le estamos pidiendo permiso al servidor para que nos autentique. Es el que se encarga de verificar si nuestra contraseña es válida y de enviarnos un token que después usaremos en cada petición al servicio.


## Login con Ethereum

Para empezar, no existe un servidor de identidades puesto que la autoridad somos nosotros. 

Me explico.

Al crear una cuenta en Ethereum, usando metamask (por ejemplo) lo que estoy haciendo es guardar una clave privada y una pública. La clave privada se guarda en mi ordenador y la clave pública es la dirección Ethereum que usamos para que nos envíen deliciosos Ethers. La mía  [es esta](https://etherscan.io/address/0xbc659c8e5162e14a90bd660a52C1105d58ed44B5)  si te sientes generoso.

Este par de claves es único, irrepetible e inseparable. Si pierdes la clave privada, lo pierdes todo. Así que guardala bien.

Para crear un login basado en Ethereum, debo verificar que el usuario efectivamente tiene la clave privada y que esa clave, coincide con la pública.

### ¿Cómo se hace eso?

Aquí empieza la tierra incógnita. 
Con los servidores de identidades hay varios estándares ya bien definidos y ampliamente usados para hacer esto ( [detalle aquí](https://oauth.net/2/) ). De hecho, son los propios servidores los que hacen la verificación. Sin embargo en el mundo blockchain estos flujos todavía están en pañales.

En este momento tenemos lo que se llaman *Ethereum Improvement Proposals* o EIP's. Son lo más cercano a una descripción de estándares que existe en el mundo de Ethereum.  Son el mapa que vamos a seguir para desarrollar nuestro login.

La EIP para el login con Ethereum es la *EIP-4361* y está documentada  [aquí](https://eips.ethereum.org/EIPS/eip-4361). Si te fijas en la fecha de creación, dice 11 de Octubre de 2021 y su status es *Draft*, lo que significa que aún está en desarrollo y que fue creada hace apenas 3 meses desde que estoy escribiendo este artículo. Ahora ya te puedes sentir como el Magallanes de la Blockchain.

En la *EIP-4361* se describe como un presunto login con Ethereum debería funcionar.

### Vamos por pasos:

**1. El wallet presenta al usuario un mensaje para ser firmado.**

Entramos a una web app que tiene un botón "Login con Ethereum". Hacemos click sobre él e inmediatamente metamask nos pide firmar un mensaje. 

El mensaje a firmar debe contener ciertos datos especificados en el  [EIP-191](https://eips.ethereum.org/EIPS/eip-191) incluido un ***nonce*** una serie de caractéres aleatorios que se guarda en el server.

La clave pública ya se ha enviado al servidor puesto que es pública (sorpresa) y metamask la hace disponible por medio de su API.

**2. La firma es enviada al servidor.**

El mensaje que acabamos de firmar se envía al servidor.

Éste verifica que la firma del mensaje coincide con la clave pública y que el contenido del mensaje es el que efectivamente hemos enviado. ¿Recuerdas el ***nonce***? Pues bien, el servidor compara si el nonce enviado y el recibido son el mismo.

**3. Si todo coincide, estás autenticado**

Si el mensaje firmado coincide con la clave pública y con el contenido del mensaje, el usuario queda autenticado. Aquí ya podemos guardar información del usuario para futuras conexiones o enviarle un token para que interactúe con la app.


Como ves, ni siquiera enviamos la clave privada al servidor. Simplemente enviamos un mensaje firmado y el servidor se encarga de verificar que la clave publica corresponde con la firma.

A grandes rasgos, este es el flujo para crear un login con Ethereum.





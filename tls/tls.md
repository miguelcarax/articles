# TLS
## Conceptos previos

Desde hace un tiempo vengo viendo que hay muchas dudas y lagunas sobre el funcionamiento del TLS o anteriormente conocido SSL, por ello me gustaría explicar un poco a mi manera el funcionamiento de este protocolo. Este artículo no va orientado a una parte téncnica si no a un público amplio, ésta explicación es genérica pero se puede aplicar a cualquier sistema que haga uso de éste protocolo. Vamos a comenzar con unos conceptos previos sobre criptografía, seguridad en sistemas informáticos y algo más para ponernos en contexto y que podamos entender en profundidad el funcionamiento de este protocolo

### Criptografía básica
La criptografía es el estudio de los métodos empleados para lograr una comunicación segura sobre entornos no seguros, osea, evitar que terceros ajenos lean mensajes privados que no les corresponden. En la informática, que es lo que nos concierne, la criptografía se basa en varios conceptos importantes que debes conocer como son:

+ Confidencialidad - Es la capacidad de enmascarar o transformar un mensaje en plano de forma que solo emisor y receptor sean capaces de entender su contenido.
+ Integridad - Es la capacidad de poder demostrar que un mensaje no ha sido alterado en su envío desde el emisor hasta el receptor.
+ Autenticación -Es la capacidad que tiene el emisor de demostrar la autoría de un mensaje enviado por el mismo.
+ No Repudio - Es la capacidad que tiene el receptor de demostrar la autoría de un mensaje enviado por un origen determinado.

La criptografía se basa en el uso de lo que se determinan ***claves criptográficas*** o únicamente ***claves*** (_keys_). Una clave es un trozo de información que junto a un algoritmo transforma un mensaje en claro, cifrandolo y volviendolo ilegible para cualquier persona que no conozca esa clave. Para que lo entendamos, una clave es normalmente un número de gran tamaño obtenido a través de algún tipo de operación matemática o simplementen un número aleatorio.

TODO: AÑADIR DIBUJO SOBRE CIFRADO

#### Criptografía de clave simétrica
La criptografía simétrica es un método de cifrado donde el emisor y el receptor utilizan una misma clave para cifrar y descifrar el mensaje respectivamente, por ejemplo, AES o DES son algunos algoritmos conocidos. Tanto receptor como emisor deben haber acordado previamente el uso de la clave común para el correcto funcionamiento de la comunicación, lo cúal es uno de las contras de este tipo de cifrado, por ello se suele hacer uso de una comunicación previa o de protocolos de intercambio de claves seguras sobre redes no seguras, como Diffie-Hellman.

TODO: AÑADIR DIBUJO


#### Criptografía de clave asimétrica
La criptografía asimétrica o de clave pública es un método de cifrado que se basa en el uso de un par de claves determinadas ***clave pública*** que es conocida por todo el mundo y ***clave privada*** que es únicamente conocida por el propietario. Éstas claves estan generadas a la vez y guardan una relación matemática que en resumen determina que no se puede obtener la clave privada a partir de la clave pública y viceversa.

En este tipo de cifrado cualquier emisor es capaz de cifrar su mensaje con la clave pública del receptor, pero ese mensaje cifrado es únicamente descifrable por el receptor, con la clave privada asociada a dicha clave pública. Uno de los algoritmos más utilizados es RSA que genera un par de claves de hasta 4096 bits de tamaño.

Muchos estándares actuales de Internet se basan en el uso de este tipo de cifrado, como pueden ser PGP, S/MIME y TLS.

TODO: AÑADIR DIBUJO

Estos dos algoritmos de cifrado, tanto simétrico como asimétrico proporcionan confidencialidad a nuestros mensajes. No confundir este par de claves con por ejemplo las claves SSH.

#### Funciones hash
Las funciones hash son algoritmos matemáticos que dada una entrada de tamaño variable da como resultado un ***hash*** o _digest_, que es una cadena de caracteres de tamaño fijo, por ejemplo:

```
821f636b21a176a2be226d1f3753dde01f092d05a442f966d8ca54d228fc7f5d
```

Este tipo de algoritmos se basan en la capacidad de:

+ Una misma entrada siempre genera una misma salida
+ Es rápido calcular el hash de un mensaje
+ No es viable calcular el mensaje original a partir de su hash
+ Cualquier cambio en el mensaje original produce un hash diferente
+ No es viable encontrar dos mensajes que produzcan el mismo hash

Los algoritmos de _hashing_ proporcionan integridad a los mensajes. Algunos de los protocolos más utilizados son MD5 o SHA2, que seguro os suenan.


### Firma digital
Una firma digital es una forma matemática de verificar la autenticidad y vericidad de un mensaje. Una firma digital se base en demostrar tres conceptos mencionados previamente:

+ Autenticación -El receptor puede confiar en que el mensaje fué enviado enviado por un emisor conocido
+ No Repudio - El emisor no puede negar que el mensaje fué enviado por él.
+ Integridad - El mensaje no fué alterado en el trayecto desde el emisor hasta el receptor.

Las firmas digitales o ***firmado*** de documentos se basan en el uso de criptografía pública, el funcionamiento es el siguiente:

1. El emisor genera un par de claves pública/privada (si no existen previamente).
2. El emisor pasa el mensaje a firmar junto con su clave privada por un algoritmo de firmado (Ej: RSA), obteniendo una firma.
3. El receptor verifica mediante el mensaje recibido, la firma y la clave pública del emisor la autenticidad del mensaje.

También añadir que es normal combinar el uso de algoritmos de firmado con algortimos de hashing. Los algoritmos de firmado requieren tiempo computacional por lo que es mucho más rápido firmar un hash (que recordemos es único) que firmar el mensaje entero, por ejemplo, SHA256 con cifrado RSA.




### Public Key Infrastructure (PKI)
El Public Key Infrastructure es una serie de roles y procedimientos necesario para la gestión, creación, distribución, almacenamiento y revocación de certificados digitales. El propósito del PKI es facilitar la posibilidad de tener comunicaciones seguras a través de una red no segura. Permite verificar la identidad tanto de origen como destino y validar la información transferida.


#### Certificados digitales
En criptografía conocemos como certificado digital o certificado de clave pública a un documento electrónico que demuestra la propiedad o autoría de una entidad sobre una clave pública determinada. Estos certificados continen diferente información como:

+ La clave pública de la entidad
+ Número de serie
+ Información sobre la entidad o _subject_
+ Información sobre la entidad externa que certifica la validez del certificado o _issuer_
+ Caducidad y validez del certificado
+ Usos del certificado (Ej: TLS)
+ Firma digital del certificado firmado por el _issuer_
+ Algoritmo de firmado
+ Extensiones - _Subject Alternative Names_
+ ...

El tipo de certificado digital más común es el definido por el estándar X.509. En el caso del TLS este tipo de certificados determinan la propiedad o correspondencia de una clave pública con un servidor. La idea detrás de los certificados digitales es la posibilidad de demostrar a un tercero desconocido tu identidad de cara a establecer una comunicación.

#### Entidades Certificadoras o CA
Las _Certified Authorities (CA)_ son las encargadas de determinar que una clave pública específica pertenece a una persona u organización determinada, son la vieja confiable. Esto se realiza mediante la firma de dicha clave pública a través de la clave privada de la CA. Para que este proceso sea veráz y válido la clave pública de dicha CA debe estar en posesión de la persona a validar la validez de dicha firma. Por ello es común que las claves pública de estás CA vengan preinstaladas por defecto en los sistemas operativos. Algunas CA conocidas son VeriSign, Digicert o Symantec y echarle un ojo a Let's Ecrypt. Éstas CA son lo que antes hemos denominado issuers.

Estás empresas permiten demostrar de forma unequívoca que una clave pública pertenece a una entidad o individuo.

Cabe destacar que es normal hacer uso de cadenas de confianza o _trusted chains_, donde el proceso de verificación de una clave pública involucra a varías CA en cadena. La clave pública original es verificada por una primera CA, denominada _CA intermediaria_ que a su vez es verificada por una segunda CA, denominada _CA Root_. Siendo necesario en última instancia únicamente la clave pública de la CA Root para verificar la validez de la clave pública del individuo.

Ya hemos explicado todos los componentes por separado, ahora vamos a ver la foto completa para entender como encaja todo esto para que funcione como un todo.

## TLS
TLS o _Transport Layer Security_ es una serie de protocolos criptográficos que permiten realizar comunicaciones seguras a través de una red no segura.

El protocolo TLS busca conseguir algunos de los puntos ya mencionados previamente:

+ Confidencialidad - La comunicación entre origen y destino es cifrada mediante el uso de criptografía de clave simétrica.
+ Autenticidad y No Repudio - La identidad del destino (y opcionalmente la del origen) es verificada mediante el uso de certificados digitales y CAs.
+ Integridad - La conexión es fiable ya que cada mensaje incluye una comprobación de integridad mediante un MAC.

Para volver a un lenguaje algo más coloquial y comprender la necesidad de este protocolo vamos a explicarlo con un ejemplo: 

Queremos conectarnos a la página web de nuestro banco con nuestras credenciales de acceso y como e es una información muy sensible la que queremos transmitir exigimos:

+ Que la página web a la que nos conectemos sea únicamente la de mi banco y no la de un impostor que quiera robarme mis credenciales.
+ Que la información que recibamos (Ej: transferencias bancarias realizadas) venga completamente cifrada desde el servidor hasta nosotros.
+ Queda podemos demostrar que esa información ha sido obtenida de la página web de mi banco y no de otra entidad.

¿Cómo vamos a conseguir todo esto? Juntando todos los elementos que hemos estado explicando previamente. Veamos el proceso en detalle desde el inicio de la comunicación hasta el final en una comunicación con un servidor HTTPS que es la versión de HTTP que tiene TLS por debajo

TODO: Añadir diagrama simple

1. Nos conectamos desde nuestro navegador a la pagina web deseada, digamos `www.google.es`. Esto realizará una conexión TCP entre nuestro ordenador y el servidor HTTPS de Google. Ver el candadito verde en la barra de URL del navegador.
2. El cliente envía un mensaje _ClientHello_ en el que especifica la versión de TLS que soporta, un número aleatorio junto con una sugerencia de algortimos de cifrado y de compresión.
3. El servidor envía un mensaje de _ServerHello_ indicando la versión del protocolo elegida, otro número aleatorio y el algoritmo de cifrado y compresión seleccionado.
4. El servidor envía un mensaje _Certificate_ que incluye el certificado digital del servidor de Google con toda la información asociada al servidor, por ejemplo, su FQDN. El certificado digital del servidor está almacenado en su _keystore_.
5. El servidor manda un mensaje _ServerHelloDone_.
6. El cliente comprueba la validez del certificado digital enviado por el servidor contra su _truststore_, que es su almacen de claves públicas de CAs. Comprueba entre otras cosas que el hostname del servidor sea el mismo que el obtenido en el certificado.
7. El cliente, tras verificar la validez del certificado contesta con un mensaje _ClientKeyExchange_ que contiene una _PreMasterSecret_ que es cifrada con la clave pública del servidor, que está incluido en el certificado digital de éste.
8. El servidor recibe la _PreMasterSecret_ cifrada con su clave pública y la descifra con su clave privada, que está incluida en su keystore.
9. Tanto cliente como servidor generar la misma clave simétrica a partir de los 2 números aleatorios generados prevaimente y la _PreMasterSecret_.
10. El cliente manda un mensaje _ChangeChiperSpec_ diciendo que a partir de ahora todo estará cifrado y autenticado en la comunicación.
11. El cliente manda un mensaje _Finished_ cifrado y autenticado, con un hash y MAC del mensaje anterior.
12. El servidor descrifra y valida la autenticidad del mensaje _Finished_ del cliente.
13. El servidor manda un mensaje _ChangeChiperSpec_ y _Finished_ igual que el que envió el cliente.
14. El cliente descifra y valida la autenticidad del mensaje _Finished_ del servidor.
15. A partir de este punto el protocolo está completamente operativo y el _handshake_ ha finalizado.

Una vez entendido (más o menos) el proceso me gustaría puntualizar una serie de matices de problemas que suelen darse y que provocan que esta comunicación no funcione correctamente o no llegue a realizarse.

+ El servidor debe tener un _keystore_ que no es otra cosa que es una almacen de certificados digitales con sus claves privadas asociadas. Es necesario tener tanto la clave privada como la pública (certificado). Si no hay certificado no hay nada que enviar al cliente para demostrar nuestra identidad y si no hay clave privada no podremos descifrar el _PreSharedSecert_ cifrado por el cliente.
+ El cliente debe tener un _truststore_ que no es otra cosa que un almacen de certificados _Root_ de CAs conocidas. Si no hay _truststore_ el cliente no puede verificar la autenticidad del certificado del servidor, de misma manera tampoco podra verificarlo si está firmado por una CA cuyo certificado Root no está almacenado en el truststore, que es lo que ocurriría si utilizamos certificados _self-signed_.
+ Tanto servidor como cliente deben poder utilizar protocolos comunes, tanto de versión de TLS como de algoritmos de cifrado y compresión.
+ El certificado del servidor tiene una serie de información asociada (_subject_) como puede ser O, OU, CN, C, ST, L, etc. El _Common Name (CN)_ es el hostname de la propia máquina. Si este CN no concuerda con el de la máquina a la que nos conectamos el proceso de verificación fallará.
+ Algunos servicios no soportan algoritmos de cifrados obsoletos o poco seguros como SHA1 o RSA 1024, utilizar protocolos más seguros como SHA2 o RSA 4096.
+ Repito, el _truststore_ almacena claves públicas de CAs y el _keystore_ almacena certificados públicos y sus claves privadas asociadas.
+ En el _truststore_ se pueden almacenar a fuego los certificados del servidor, pero no es buena práctica. Deben almacenarse únicamente los certificados Root de las CA.
+ Los certificados pueden venir configurados para servir para diferentes elementos que pertenezcan a una misma compañia. Esto se define como _Extensiones_ dentro del certifinado con el apartado que se denomina _Subject Alternative Names_. Este parámetro nos permite definir varios nombres para el certificado, Google, por ejemplo, utiliza el mismo certificado para las urls, `google.es`, `goo.gl` o incluye `youtube.com`.
+ En caso de hacer uso de CA intermedias, el certificado ofrecido por el servidor deben contener tanto el certificado del servidor como el de la CA intermedia, en caso contrario no funcionará.





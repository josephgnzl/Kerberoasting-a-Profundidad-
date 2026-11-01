# Kerberoasting

Kerberoasting es una técnica de ataque contra entornos Windows, específicamente Active Directory, que permite obtener tickets de servicio (TGS) asociados a cuentas que tienen un Service Principal Name (SPN) registrado, con el objetivo de intentar recuperar sus contraseñas mediante cracking offline.

La premisa del ataque es sencilla, pero su impacto puede ser significativo: cualquier usuario autenticado del dominio puede solicitar un ticket TGS para una cuenta que tenga un SPN registrado, sin necesidad de contar con privilegios administrativos especiales.

## ¿Cómo funciona?

El proceso comienza con la **enumeración de SPN**, donde se identifican las cuentas de servicio registradas en Active Directory que pueden ser objetivos potenciales.

Una vez identificadas, se realiza la **solicitud de tickets TGS** al Domain Controller. Estos tickets contienen material criptográfico protegido mediante una clave derivada de la contraseña de la cuenta de servicio.

El siguiente paso consiste en la **extracción del ticket TGS** para llevarlo a un entorno controlado y realizar un análisis offline.

Mediante técnicas de **cracking offline**, se prueban distintas combinaciones de contraseñas hasta encontrar una que permita validar el material criptográfico obtenido.

Si se consigue recuperar la contraseña, se procede a evaluar los **privilegios y permisos de la cuenta**, determinando el impacto que su compromiso podría tener dentro del dominio.

## ¿A quién va dirigido?

| Tipo de cuenta | Descripción |
|---|---|
| **Cuentas de servicio** | Usualmente poseen un SPN asociado y pueden ser objetivo de solicitudes de tickets TGS. |
| **Cuentas de servicio con privilegios elevados** | Representan un riesgo mayor si sus credenciales llegan a verse comprometidas. |


## Reproduccion 

### 01. Extracción del ticket TGS

Una vez identificadas las cuentas de servicio, podemos solicitar sus tickets TGS al Domain Controller y almacenar el material criptográfico obtenido para su posterior análisis.

```
GetUserSPNs.py 'raynex.lab/USUARIO' \
-dc-ip 10.0.0.46 \
-request \
-outputfile TGS.txt
```
### Verificación del archivo obtenido:
```
cat TGS.txt
```
El resultado contiene una cadena que comienza con $krb5tgs$, cuyo identificador permite reconocer el tipo de cifrado utilizado.

| Identificador | Cifrado | Modo Hashcat |
|:--------|:------:|------:|
| $krb5tgs$23$| RC4-HMAC | 13100 |
| $krb5tgs$18$ | AES-256 | 19700 |

### 02. Cracking offline

Con el ticket TGS extraído, podemos realizar un proceso de cracking offline para intentar recuperar la contraseña de la cuenta de servicio.

```
hashcat -m 19700 hash.txt ~/wordlists/RockYou/rockyou.txt
```

### Resultado esperado

si se consigue recuperar la contraseña, Hashcat mostrará el material asociado al ticket y la contraseña encontrada. El éxito dependerá de la complejidad de la contraseña y del espacio de búsqueda utilizado.

### 03. Validación de credenciales

Una vez recuperada la contraseña, el siguiente paso consiste en comprobar si las credenciales permiten autenticarse en los servicios del entorno.

```
nxc smb raynex.lab -u raynex -p Raynex2026! --shares
```
La autenticación permite confirmar si las credenciales son válidas para SMB. Sin embargo, una autenticación exitosa no implica necesariamente que la cuenta tenga privilegios administrativos, lo cual es importante siempre tener en cuenta.

### 04. Movimiento lateral

El acceso dependerá de los permisos asignados a la cuenta y de las políticas de seguridad del equipo objetivo.

### 05. Análisis de privilegios y exposición del dominio

Una vez comprometida una cuenta, es importante determinar qué permisos posee dentro de Active Directory.
En particular, debemos prestar atención a los permisos de replicación del directorio, como ```DS-Replication-Get-Changes``` y ```DS-Replication-Get-Changes-All.```

Cuando una cuenta dispone de los permisos necesarios, puede existir una condición que permita realizar un ataque ```DCSync.```


### Conclusión personal

Kerberoasting permite evaluar la seguridad de un entorno corporativo, extrayendo el material criptográfico asociado a cuentas de servicio y someterlo a análisis offline para intentar descifrar sus contraseñas.

Es importante mencionar, que si la cuenta posee una contrasena robusta y fuerte, no importa los permisos que tenga la misma, puesto a que no podrian ser aprovechados. 

La principal limitación de este ataque está relacionada con la fortaleza de las contraseñas de las cuentas de servicio. Más adelante abordaré cómo la psicología humana influye en la elección de nuestras contraseñas y por qué algunas organizaciones mantienen contraseñas genéricas o predeterminadas en sus cuentas de servicio.

El impacto real depende de las credenciales recuperadas, los privilegios de las cuentas comprometidas y las relaciones de confianza existentes dentro del dominio.

### h4theplanet, Joseph. 

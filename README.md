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

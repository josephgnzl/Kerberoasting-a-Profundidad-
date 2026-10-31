# ¿Qué es Kerberoasting?

Kerberoasting es una técnica de ataque contra entornos Windows, específicamente Active Directory, que permite obtener tickets de servicio (TGS) asociados a cuentas que tienen un Service Principal Name (SPN) registrado, con el objetivo de intentar recuperar sus contraseñas mediante cracking offline.

La premisa del ataque es sencilla, pero su impacto puede ser significativo: cualquier usuario autenticado del dominio puede solicitar un ticket TGS para una cuenta que tenga un SPN registrado, sin necesidad de contar con privilegios administrativos especiales.

## ¿A quién está dirigido el ataque?

- **Cuentas de servicio:** tienen un SPN asociado y pueden ser objetivo de solicitudes de tickets TGS.
- **Cuentas de servicio con privilegios elevados:** representan un riesgo mayor si sus credenciales llegan a verse comprometidas.

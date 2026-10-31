## 01. Fundamentos

### ¿Qué es Kerberoasting?

Kerberoasting es una técnica de ataque contra entornos Windows, específicamente Active Directory, que permite obtener tickets de servicio (TGS) asociados a cuentas que tienen un Service Principal Name (SPN) registrado, con el objetivo de intentar recuperar sus contraseñas mediante cracking offline.

La premisa del ataque es sencilla, pero su impacto puede ser significativo: cualquier usuario autenticado del dominio puede solicitar un ticket TGS para una cuenta que tenga un SPN registrado, sin necesidad de contar con privilegios administrativos especiales.

### ¿A quién está dirigido el ataque?

- **Cuentas de servicio:** tienen un SPN asociado y pueden ser objetivo de solicitudes de tickets TGS.
- **Cuentas de servicio con privilegios elevados:** representan un riesgo mayor si sus credenciales llegan a verse comprometidas.

### ¿Cómo funciona el ataque?

| Etapa | Descripción |
|---|---|
| **01. Enumeración** | Identificación de cuentas de servicio con SPN registrado. |
| **02. Solicitud TGS** | Se solicita un ticket de servicio al Domain Controller. |
| **03. Extracción** | Se obtiene el material criptográfico del ticket TGS. |
| **04. Cracking offline** | Se intenta recuperar la contraseña mediante ataques de diccionario o fuerza bruta. |
| **05. Compromiso** | Si se recupera la contraseña, se evalúan los privilegios de la cuenta. |

---

## 02. Reproducción del ataque

### 2.1 Entorno de laboratorio

| Parámetro | Valor |
|---|---|
| **Dominio** | `raynex.lab` |
| **Domain Controller** | `RAYNEX-DC-01` |
| **Dirección IP** | `10.0.0.46` |
| **Sistema operativo** | Windows Server 2025 |
| **Atacante** | Arch Linux |

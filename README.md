# Manual de Preconstitución de Prueba y Sellado de Tiempo Criptográfico (Chile)

Guía técnica y marco normativo para la protección preventiva de propiedad industrial y derechos de autor mediante sellado de tiempo criptográfico (RFC 3161) y firma electrónica en Chile.

---

## 1. Marco Legal y Alcance Probatorio

### 1.1. Regla de Prioridad Registral vs. Preconstitución de Prueba
Bajo la Ley N° 19.039 de Propiedad Industrial en Chile, la **prioridad registral** frente a terceros nace única y exclusivamente con el **ingreso formal de la solicitud a trámite ante el Instituto Nacional de Propiedad Industrial (INAPI)** (o mediante prioridad extranjera válidamente invocada bajo el Convenio de París).

> **Aviso fundamental:** Ningún sello de tiempo, protocolización notarial o registro digital sustituye la concesión formal de una patente, modelo de utilidad o diseño industrial ante INAPI.

El sellado de tiempo cumple un rol de **preconstitución de prueba** orientado a:
* Proteger el **secreto empresarial** frente a filtraciones o incumplimientos de acuerdos de confidencialidad (NDA).
* Acreditar autoría y titularidad previa en disputas judiciales, juicios de nulidad o reivindicación de invención (reclamación de usurpación de diseño por terceros).
* Resguardar la expresión formal de planos y bocetos técnicos amparados por la Ley N° 17.336 sobre Propiedad Intelectual (Derechos de Autor).

### 1.2. Marco Normativo en Chile (Ley N° 19.799)
La Ley N° 19.799 regula los documentos electrónicos, la firma electrónica y los servicios de certificación en Chile:
* **Entidades Certificadoras Acreditadas (ECA):** Para alcanzar el máximo estándar de eficacia probatoria (equivalente al mérito ejecutivo o presunción legal de plena prueba respecto de la integridad y fecha cierta), el estampado debe ser emitido por un prestador acreditado ante la Subsecretaría de Economía, Fomento y Turismo.
* **Sistemas Internacionales o Gratuitos (RFC 3161 / Blockchain):** Constituyen un principio de prueba por escrito admisible en juicio bajo las reglas de la sana crítica (Código de Procedimiento Civil), requiriendo un peritaje informático que corrobore el estándar criptográfico aplicado.

---

## 2. Estructura de Archivos del Procedimiento

Para garantizar la inalterabilidad de la prueba, se debe mantener un directorio ordenado con la siguiente nomenclatura:

```text
proyecto-custodia/
├── original/
│   └── diseno_tecnico_v1.pdf       # Archivo original inalterable (Solo lectura)
├── hashes/
│   └── diseno_tecnico_v1.sha256    # Huella criptográfica del documento
├── sellos/
│   ├── peticion.tsq                # Petición de sellado (Timestamp Request)
│   └── diseno_tecnico_v1.tsr       # Respuesta de la entidad certificadora (Timestamp Response)
└── certs/
    └── tsa_root_cacert.pem         # Certificado público raíz de la TSA

```

---

## 3. Procedimiento Práctico y Didáctico (Paso a Paso con OpenSSL)

Este flujo utiliza el estándar internacional **RFC 3161** y herramientas nativas de código abierto (`openssl` y `curl`).

### Paso 1: Generación del Resumen Hash (SHA-256)

El hash es el identificador único del documento. Si un solo byte cambia, el hash cambiará de forma irreversible.

```bash
# Calcular el hash SHA-256 en formato hexadecimal
openssl dgst -sha256 original/diseno_tecnico_v1.pdf > hashes/diseno_tecnico_v1.sha256

# Visualizar el hash generado
cat hashes/diseno_tecnico_v1.sha256

```

### Paso 2: Creación de la Petición de Sellado (`.tsq`)

Se crea una solicitud que empaqueta el hash del archivo sin revelar su contenido técnico:

```bash
openssl ts -query -data original/diseno_tecnico_v1.pdf -sha256 -cert -out sellos/peticion.tsq

```

### Paso 3: Emisión de la Marca de Tiempo

Se envía la solicitud a una Autoridad de Sellado de Tiempo (TSA). Para efectos de práctica o uso inicial, se puede emplear un servidor TSA público:

```bash
# Envío de la consulta RFC 3161 vía cURL a FreeTSA (o servidor institucional correspondiente)
curl -H "Content-Type: application/timestamp-query" \
     --data-binary @sellos/peticion.tsq \
     [https://freetsa.org/tsr](https://freetsa.org/tsr) -o sellos/diseno_tecnico_v1.tsr

```

### Paso 4: Descarga del Certificado de la Autoridad

Para verificar la firma de forma autónoma (offline), se descarga la cadena de certificados públicos de la TSA:

```bash
curl [https://freetsa.org/files/tsa.crt](https://freetsa.org/files/tsa.crt) -o certs/tsa.crt
curl [https://freetsa.org/files/cacert.pem](https://freetsa.org/files/cacert.pem) -o certs/cacert.pem

```

---

## 4. Reglas Críticas de Custodia e Integridad

### Cómo mostrar el contenido sin destruir la validez del Hash

1. **Regla de Solo Lectura:** El archivo ubicado en `original/` jamás debe ser editado, renombrado internamente ni sobreescrito. Un cambio mínimo destruye la coincidencia matemática con el sello `.tsr`.
2. **Copias de Trabajo:** Para visualizar el plano, imprimirlo o compartirlo en reuniones:
* Genere un duplicado: `cp original/diseno_tecnico_v1.pdf copia_lectura.pdf`.
* Abra únicamente la copia de trabajo en el lector PDF o software CAD.


3. **Bloqueo contra escritura:** Establezca permisos de solo lectura en el sistema de archivos:
```bash
chmod 444 original/diseno_tecnico_v1.pdf

```



---

## 5. Verificación y Demostración ante Perito

Cualquier perito informático o ministro de fe puede verificar la autenticidad e integridad del documento siguiendo estos pasos:

### 5.1. Inspección del Contenido del Sello

Permite extraer la fecha y hora oficial registrada (UTC), el algoritmo utilizado y el nombre de la TSA emisora:

```bash
openssl ts -reply -in sellos/diseno_tecnico_v1.tsr -text

```

### 5.2. Validación Criptográfica Cruzada

El comando contrasta el archivo original con la firma digital y el certificado de la autoridad:

```bash
openssl ts -verify \
  -data original/diseno_tecnico_v1.pdf \
  -in sellos/diseno_tecnico_v1.tsr \
  -CAfile certs/cacert.pem \
  -untrusted certs/tsa.crt

```

* **Salida esperada (Validación exitosa):**
```text
Verification: OK

```


* **Salida en caso de alteración del archivo:**
```text
Verification: FAILED
140...:error:...message digest failure

```



---

## 6. Protocolo de Entrega al Perito / Ministro de Fe

Al remitir los antecedentes para una pericia judicial o protocolización notarial, empaquete los archivos manteniendo las sumas de verificación intactas:

1. **Creación del paquete inmutable:**
```bash
tar -cvzf paquete_evidencia_peritaje.tar.gz original/ hashes/ sellos/ certs/

```


2. **Cálculo del hash maestro del paquete:**
```bash
sha256sum paquete_evidencia_peritaje.tar.gz > paquete_evidencia_peritaje.tar.gz.sha256

```


3. **Entrega de antecedentes:**
* Entregar el paquete comprimido junto con el archivo de hash maestro.
* Indicar explícitamente en el oficio o minuta de entrega:
* Nombre original del archivo técnico.
* Hash SHA-256 precalculado.
* Fecha y hora aproximada de estampado.
* Nombre y URL de la Autoridad de Sellado de Tiempo utilizada.



# Manual de Sellado de Tiempo Criptográfico y Custodia Digital con Valor Probatorio en Chile

## 1. Marco Jurídico en Chile y Prelación Registral

### 1.1 El Principio Registral y la Prioridad Formal

En el ordenamiento jurídico chileno, regido por la **Ley N° 19.039 de Propiedad Industrial** y los tratados internacionales suscritos por Chile (en particular, el Convenio de París y el Tratado de Cooperación en materia de Patentes - PCT):

> **ADVERTENCIA LEGAL FUNDAMENTAL:**
>
> La **fecha de prioridad registral** nace **única y exclusivamente con el ingreso formal de la solicitud** ante el Instituto Nacional de Propiedad Industrial (INAPI) o ante una oficina extranjera receptora válida.
>
> Ningún sellado de tiempo, timestamping notarial ni registro privado en blockchain otorga prioridad registral ni reserva formal de una patente, modelo de utilidad o diseño industrial frente a un tercero que presente primero una solicitud ante el INAPI (*principio prior in tempore, potior in jure*).

### 1.2 ¿Para qué sirve entonces el Sellado de Tiempo (*Timestamping*)?

Aunque no sustituye el trámite ante INAPI, el sellado de tiempo de alto estándar otorga un respaldo decisivo en dos escenarios procesales:

1. **Acciones de Nulidad o Reclamación de Autoría:** Demostrar que el legítimo creador concibió el invento con anterioridad a una solicitud fraudulenta interpuesta por un tercero, probando la falta de novedad del registro impugnado o la usurpación de derechos.

2. **Defensa de Secreto Empresarial e Infracción Contractual:** Acreditar fehacientemente que la información técnica ya existía y estaba en posesión de la parte antes de ser entregada a un proveedor bajo un Acuerdo de Confidencialidad (NDA).

### 1.3 Régimen Legal de la Prueba Digital: Ley N° 19.799

La **Ley N° 19.799 sobre Documentos Electrónicos, Firma Electrónica y Servicios de Certificación** establece los estándares de admisibilidad y fuerza probatoria:

* **Entidades Prestadoras de Servicios de Certificación Acreditadas en Chile:**

  Para que un certificado de sellado de tiempo cuente con presunción de **plena prueba** (asimilable a la fe pública de un instrumento público o notarial según el Código de Procedimiento Civil), debe haber sido emitido por una entidad de certificación **oficialmente acreditada** ante la Subsecretaría de Economía, Fomento y Turismo (por ejemplo: E-Sign, Acepta, Firma.cl, Thomas Signe). Bajo este esquema, la ley presume de pleno derecho la integridad del documento y la exactitud de su fecha.

* **Sellos Internacionales o Descentralizados (RFC 3161 / Blockchain):**

  Son plenamente admisibles en tribunales e INAPI, pero se aprecian bajo las reglas de la **sana crítica**. Su eficacia descansa en un peritaje informático que demuestre que el estándar técnico aplicado (algoritmo SHA-256 y firma criptográfica de clave pública) hace matemáticamente imposible la adulteración de la fecha o del contenido.

## 2. Fundamentos Criptográficos Básicos

Para no incurrir en errores técnicos que anulen la prueba:

* **Función Resumen (*Hash*):** Algoritmo unidireccional (como SHA-256) que convierte cualquier archivo en una huella digital alfanumérica única de 64 caracteres hexadecimales. Si un solo bit o píxel cambia, el hash resultante es completamente distinto.

* **Sello de Tiempo (*Timestamp Token* - RFC 3161):** Fichero complementario (`.tsr`) que enlaza de manera inalterable el hash del archivo con la hora oficial mundial (UTC) mediante la firma digital de una Autoridad de Sellado de Tiempo (TSA).

## 3. Procedimiento Práctico y Gratuito de Carácter Didáctico

Este procedimiento permite entrenar y aplicar el sellado sin exponer la confidencialidad de los planos, utilizando herramientas estándar de código abierto.

### 3.1 Cómo generar el Hash del archivo original

Para generar la huella digital localmente sin subir el plano a ningún sitio web:

#### En Windows (PowerShell):

1. Presionar `Inicio`, escribir `PowerShell` y abrirlo.

2. Escribir el siguiente comando sustituyendo la ruta de su archivo:

   ```
   Get-FileHash -Path "C:\Ruta\A\Su\plano_tecnico.pdf" -Algorithm SHA256
   
   ```

3. Se obtendrá una salida con la cadena hash:

   ```
   Algorithm       Hash                                                                Path
   ---------       ----                                                                ----
   SHA256          A3F5B829C1E074D16B3C...[64 caracteres alfanuméricos]                C:\Ruta\...
   
   ```

#### En macOS y Linux (Terminal):

1. Abrir la consola Terminal.

2. Ejecutar:

   ```
   shasum -a 256 "plano_tecnico.pdf"
   
   ```

### 3.2 Cómo obtener la firma de tiempo gratuita (Protocolo RFC 3161)

Utilizando el paquete estándar **OpenSSL** (preinstalado en Linux y macOS, o instalable en Windows):

1. **Generar el requerimiento de sellado (`.tsq`):**

   ```
   openssl ts -query -data plano_tecnico.pdf -sha256 -cert -out plano_tecnico.tsq
   
   ```

   *Este comando calcula el hash de su plano localmente e inserta la solicitud en el archivo `plano_tecnico.tsq`.*

2. **Enviar la huella a una Autoridad de Sellado pública (FreeTSA o DigiCert):**

   ```
   curl -H "Content-Type: application/timestamp-query" --data-binary @plano_tecnico.tsq https://freetsa.org/tsr -o plano_tecnico.tsr
   
   ```

   *Se descarga el archivo `plano_tecnico.tsr`, que contiene la fecha oficial y la firma digital.*

## 4. Custodia y Manejo del Documento: "Cómo Mostrar sin Destruir el Hash"

El error más común es alterar accidentalmente el archivo tras haber obtenido el sello.

### 4.1 Reglas Críticas de Inmutabilidad

1. **Nunca editar ni "guardar como" sobre el original:**

   Abrir un archivo en Adobe Acrobat, hacer clic en "Guardar", o modificar un solo atributo en AutoCAD o Illustrator altera los metadatos internos del archivo. **Cualquier cambio de metadato destruye la coincidencia del hash.**

2. **Definir el archivo como "Solo Lectura":**

   Inmediatamente después de generar el sello, hacer clic derecho sobre `plano_tecnico.pdf` > `Propiedades` > marcar la casilla **"Solo lectura"** (*Read-only*).

### 4.2 Procedimiento para mostrar el contenido

Para revisar el plano o enseñarlo a terceros sin arriesgar el original:

* **Hacer una copia de trabajo explícita:**

  1. Copiar el archivo original: `plano_tecnico.pdf` $\rightarrow$ `plano_tecnico_COPIA_CONSULTA.pdf`.

  2. Abrir, imprimir o exportar exclusivamente la **copia de consulta**.

  3. El original sellado debe mantenerse intocado en su almacenamiento seguro.

## 5. Almacenamiento y Estructura de Resguardo

Ambos archivos deben convivir bajo una regla de asociación estricta:

```
Carpeta_Proyecto_Confidencial/
│
├── 01_Custodia_Sellada/           <-- CARPETA DE RESERVA INALTERABLE
│   ├── plano_tecnico_v1.pdf      <-- Archivo original exacto (Solo Lectura)
│   ├── plano_tecnico_v1.tsr      <-- Sello criptográfico RFC 3161
│   └── hash_sha256.txt           <-- Registro en texto plano del hash
│
└── 02_Copias_de_Trabajo/          <-- CARPETA DE USO COMERCIAL Y PERITAJES
    ├── plano_tecnico_CONSULTA.pdf
    └── plano_impresion.pdf

```

* **Copias de seguridad en frío:** Se recomienda almacenar la carpeta `01_Custodia_Sellada` en un soporte físico externo (disco USB no regrabable o almacenamiento WORM / Read-Only) y una copia en un servicio de almacenamiento en la nube cifrado.

## 6. Verificación y Demostración ante Perito o Tribunal

Para que un perito informático judicial certifique la fecha exacta:

### 6.1 Obtención del certificado de la entidad

El perito descarga el certificado público del servidor emisor (en este caso, de FreeTSA):

```
curl https://freetsa.org/files/cacert.pem -o cacert.pem
curl https://freetsa.org/files/tsa.crt -o tsa.crt

```

### 6.2 Comando pericial de verificación de integridad y tiempo

El perito corre el comando de validación cruzada:

```
openssl ts -verify -data plano_tecnico_v1.pdf -in plano_tecnico_v1.tsr -CAfile cacert.pem -untrusted tsa.crt

```

* **Resultado exitoso:**

  ```
  Verification: OK
  
  ```

  Esto acredita matemáticamente que el contenido del archivo `plano_tecnico_v1.pdf` no ha cambiado en un solo bit desde el instante en que la TSA emitió el certificado.

### 6.3 Extracción de la fecha y hora oficial legible

Para incorporar al informe pericial:

```
openssl ts -reply -in plano_tecnico_v1.tsr -text

```

El reporte detallará:

* **Time:** La fecha y segundo exacto en formato UTC.

* **Hash / Message digest:** El resumen exacto del plano.

* **Serial Number & Signer:** Datos del certificado emisor.

## 7. Protocolo para Compartir el Original y la Firma con el Experto

Cuando un tribunal o perito solicite acceso para auditar la evidencia:

1. **Empaquetado sin alteración:**

   Comprimir los dos archivos en un archivo ZIP sin compresión destructiva o generar un archivo de imagen ISO:

   * `plano_tecnico_v1.pdf`

   * `plano_tecnico_v1.tsr`

2. **Generar un comprobante de entrega:**

   En el correo electrónico formal o documento de entrega, indicar textualmente el hash SHA-256 precalculado:

   > *"Se adjuntan los archivos `plano_tecnico_v1.pdf` (SHA-256: `A3F5B8...`) y su comprobante `plano_tecnico_v1.tsr`. Sírvase constatar la integridad del hash y verificar la marca temporal mediante el protocolo RFC 3161."*

3. **Comprobación de recepción:**

   Al recibir el archivo, el perito corre `Get-FileHash` o `shasum -a 256` en su propio equipo para confirmar que el hash del archivo recibido coincide de forma exacta con la cadena informada en el comprobante de entrega.

## 8. Resumen Ejecutivo de Ruta Crítica para el Inventor

| **Fase** | **Acción Técnica / Legal** | **Costo Oficial** | **Efecto Jurídico** | 
| **1. Inmediata** | Generar Hash SHA-256 y obtener sello RFC 3161 o certificación local | \$0 (FreeTSA/OpenSSL) o tarifa entidad local | Preconstituye prueba de autoría y fecha para juicios de nulidad o NDA. | 
| **2. Custodia** | Bloquear el original en solo lectura y operar solo con copias | \$0 | Evita la corrupción accidental del hash. | 
| **3. Presentación INAPI** | Ingreso de la solicitud formal en INAPI | 1 UTM (\~\$72.000 CLP) | **Adquiere la verdadera Prioridad Registral legal.** | 

#  S3 Content Fetcher & Enricher

Este notebook permite conectarse a un bucket de DigitalOcean Spaces (S3 compatible) para:

- Listar archivos disponibles.
- Filtrar los últimos archivos según criterios específicos.
- Cargar archivos .jsonl como DataFrames.
- Extraer y transformar datos clave, como identificadores externos, títulos y enlaces.

## Requisitos
- Python 3.11+
- Credenciales AWS (AWS_KEY, AWS_SECRET)
- Librerías
```bash
pip install boto3 pandas numpy
```

## Estructura esperada del Bucket
```
/
├── Content/
│   └── latest/
│       └── es_hbomax.jsonl
├── Stats/
├── Episodes/
├── ComingSoon/
```

### Uso
#### Configuración de credenciales
Configura tus credenciales en las variables del script:
```python
REGION = 'nyc3'
ENDPOINT = 'https://nyc3.digitaloceanspaces.com'
AWS_KEY = 'TU_CLAVE'
AWS_SECRET = 'TU_SECRETO'
BUCKET = 'NOMBRE_DEL_BUCKET'
```

####  Conexión a S3
```python
client = client_s3()
```

####  Listar y filtrar archivos
```python
# Obtener listado completo
df_files = pd.DataFrame(list_paginas)

# Filtrar por 'es_hbomax'
df_select = df_files[
    (df_files['Key'].str.contains('es_hbomax')) &
    (df_files['Type'] == 'Content')
].sort_values('LastModified', ascending=False)
```
#### Cargar archivo .jsonl
```python
obj = client.get_object(Bucket=BUCKET, Key=_key)
data = obj['Body'].read()
df = pd.read_json(BytesIO(data), lines=True)
```

### Búsquedas
#### Por ExternalIds
```python
df_external_ids[
    (df_external_ids['ExternalId'] == '250307') &
    (df_external_ids['ExternalProvider'] == 'tmdb') &
    (df_external_ids['ExternalType'] == 'Tv Show')
]
```
#### Por título
```python
df_external_ids[df_external_ids['Title'].str.contains('The Pitt')]\
    .drop(columns=['ExternalProvider', 'ExternalType', 'ExternalId'])\
    .drop_duplicates(subset=['PlatformName', 'PlatformCountry', 'Id'])
```

###  Notas
- Este código está orientado a tareas de análisis de catálogo Streaming Availability.
- Este libro es exclusivo para el uso de los datos de BB Media, a Fabric Data Company

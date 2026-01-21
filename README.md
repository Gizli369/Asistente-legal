# LegalAI Assistant - Sistema RAG con IA

Sistema completo de asistente legal inteligente que utiliza RAG (Retrieval Augmented Generation), bases de datos vectoriales, LLM local y técnicas avanzadas de procesamiento de documentos.

## Características Principales

### Tecnologías de IA Implementadas

- **RAG (Retrieval Augmented Generation)**: Sistema completo de recuperación y generación aumentada
- **Base de Datos Vectorial**: ChromaDB para almacenamiento y búsqueda semántica
- **LLM Local**: Ollama con Llama 3.2 o Mistral 
- **Embeddings**: Sentence Transformers para generación de vectores
- **Chunking Inteligente**: Fragmentación optimizada de documentos con overlap
- **Procesamiento de Documentos**: Soporte para PDF, DOCX y TXT

### Seguridad Implementada

-  Autenticación JWT con access y refresh tokens
-  Hashing de contraseñas con bcrypt
-  Encriptación de datos sensibles con Fernet
-  Rate limiting para prevenir ataques DDoS
-  Validación de fortaleza de contraseñas
-  Sanitización de inputs
-  CORS configurado correctamente
-  Logs de auditoría de seguridad
-  Protección contra inyecciones SQL

### Características de Usuario

-  Carga y gestión de documentos legales
-  Consultas en lenguaje natural con RAG
-  Análisis automático de documentos
-  Estadísticas y dashboard
-  Historial de consultas
-  Interfaz responsive (móvil y desktop)

## Arquitectura del Sistema

```
┌─────────────────────────────────────────┐
│         Frontend (React)                │
│  - Material-UI                          │
│  - Responsive Design                    │
└─────────────┬───────────────────────────┘
              │ HTTP/REST
┌─────────────▼───────────────────────────┐
│         Backend (FastAPI)               │
│  - Autenticación JWT                    │
│  - Rate Limiting                        │
│  - Validación                           │
└─────────────┬───────────────────────────┘
              │
    ┌─────────┼─────────┬─────────────┐
    │         │         │             │
┌───▼────┐ ┌─▼──────┐ ┌▼─────────┐ ┌─▼──────┐
│SQLite  │ │ChromaDB│ │  Ollama  │ │Security│
│User/Doc│ │Vectores│ │   LLM    │ │ Layer  │
└────────┘ └────────┘ └──────────┘ └────────┘
```

## Requisitos Previos

### Software Necesario

- Python 3.10 o superior
- Node.js 18 o superior
- Ollama (para el LLM local)

### Instalación de Ollama

```bash
# Linux/Mac
curl -fsSL https://ollama.com/install.sh | sh

# Windows: Descargar desde https://ollama.com/download

# Iniciar Ollama
ollama serve

# Descargar modelo (en otra terminal)
ollama pull llama3.2:3b
# O alternativamente:
ollama pull mistral
```

## Instalación y Configuración

### 1. Clonar el Repositorio

```bash
git clone <tu-repositorio>
cd legalai-assistant
```

### 2. Configurar Backend

```bash
# Crear entorno virtual
python -m venv venv

# Activar entorno virtual
# Windows:
venv\Scripts\activate
# Linux/Mac:
source venv/bin/activate

# Instalar dependencias
pip install -r requirements.txt

# Copiar archivo de configuración
cp .env.example .env

# Editar .env y configurar las variables necesarias
# Especialmente SECRET_KEY y ENCRYPTION_KEY
```

### Generar Claves de Seguridad

```python
# En terminal Python:
python -c "import secrets; print(secrets.token_urlsafe(32))"  # Para SECRET_KEY

python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"  # Para ENCRYPTION_KEY
```

### 3. Iniciar Backend

```bash
# Asegúrate de que Ollama esté corriendo
# En una terminal: ollama serve

# Iniciar FastAPI
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# El backend estará disponible en: http://localhost:8000
# Documentación API: http://localhost:8000/api/docs
```

### 4. Configurar Frontend

```bash
# En una nueva terminal
cd frontend  # o el directorio del frontend

# Instalar dependencias
npm install

# Copiar archivo de configuración
cp .env.example .env

# Iniciar servidor de desarrollo
npm run dev

# El frontend estará disponible en: http://localhost:3000
```

##  Uso del Sistema

### 1. Registro e Inicio de Sesión

- Accede a `http://localhost:3000`
- Regístrate con un email y contraseña segura (mínimo 8 caracteres, mayúsculas, minúsculas, números y símbolos)
- O usa el usuario de prueba:
  - Email: `admin@legalai.com`
  - Contraseña: `Admin123!`

### 2. Cargar Documentos

1. Ve a la sección "Documentos"
2. Haz clic en "Subir Documento"
3. Selecciona archivos PDF, DOCX o TXT (máximo 10MB)
4. El sistema procesará el documento automáticamente:
   - Extracción de texto
   - Chunking inteligente
   - Generación de embeddings
   - Almacenamiento en base de datos vectorial

### 3. Realizar Consultas

1. Ve a la sección "Consultar"
2. Escribe tu pregunta en lenguaje natural
3. Ajusta el número de documentos a consultar (slider)
4. El sistema:
   - Busca fragmentos relevantes en la base vectorial
   - Genera respuesta usando el LLM con contexto
   - Muestra fuentes y nivel de confianza

### 4. Ver Historial

- Accede al historial de consultas anteriores
- Revisa respuestas pasadas
- Analiza patrones de uso

## Seguridad

### Características de Seguridad Implementadas

#### Autenticación
- JWT tokens con expiración
- Refresh tokens para sesiones largas
- Hashing bcrypt para contraseñas
- Validación de fortaleza de contraseña

#### Protección de Datos
- Encriptación Fernet para datos sensibles
- Sanitización de todos los inputs
- Validación de tipos de archivo
- Límite de tamaño de archivos

#### Prevención de Ataques
- Rate limiting (20 req/min por defecto)
- CORS configurado
- SQL Injection prevention con ORM
- XSS prevention con sanitización
- Logs de auditoría de todas las acciones

#### Mejores Prácticas
- Variables de entorno para secretos
- Principio de menor privilegio
- Separación de usuarios y permisos
- Logs de seguridad detallados

## Funcionamiento del RAG

### Pipeline de Procesamiento

```
1. Documento → 2. Extracción → 3. Chunking → 4. Embeddings → 5. ChromaDB
                                                                    ↓
6. Query ← 7. LLM ← 8. Contexto ← 9. Búsqueda ← 10. Embedding Query
```

### Detalles Técnicos

1. **Chunking**: 
   - Tamaño: 1000 caracteres
   - Overlap: 200 caracteres
   - Separadores: "\n\n", "\n", ". "

2. **Embeddings**:
   - Modelo: all-MiniLM-L6-v2
   - Dimensión: 384
   - Normalización: Cosine similarity

3. **Retrieval**:
   - Top-K documentos (configurable 1-10)
   - Filtrado por usuario
   - Scores de relevancia

4. **Generation**:
   - LLM: Llama 3.2 o Mistral
   - Temperature: 0.3 (más determinístico)
   - Context window: 4096 tokens

## Estructura del Proyecto

```
legalai-assistant/
├── backend/
│   ├── main.py                 # Aplicación FastAPI principal
│   ├── config.py               # Configuración
│   ├── database.py             # Modelos SQLAlchemy
│   ├── security.py             # Seguridad y JWT
│   ├── schemas.py              # Esquemas Pydantic
│   ├── chunking.py             # Procesamiento de documentos
│   ├── vector_store.py         # ChromaDB
│   ├── llm_manager.py          # Gestión de LLM
│   ├── requirements.txt        # Dependencias Python
│   └── .env.example            # Configuración de ejemplo
│
├── frontend/
│   ├── src/
│   │   ├── api/
│   │   │   └── client.js       # Cliente API
│   │   ├── components/
│   │   │   ├── Auth/
│   │   │   │   ├── Login.jsx
│   │   │   │   └── Register.jsx
│   │   │   ├── Dashboard/
│   │   │   ├── Documents/
│   │   │   ├── Query/
│   │   │   ├── History/
│   │   │   └── Layout/
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── package.json
│   ├── vite.config.js
│   └── .env.example
│
├── uploads/                    # Documentos subidos
├── chroma_db/                  # Base de datos vectorial
├── logs/                       # Logs de aplicación
└── README.md
```

##  Testing

### Ejecutar Tests del Backend

```bash
pytest -v
```

### Tests Implementados

-  Autenticación (registro, login, tokens)
-  Upload de documentos
-  Procesamiento de chunks
-  Búsqueda vectorial
-  Consultas RAG
-  Seguridad (rate limiting, validación)

##  Mejoras Futuras

### Técnicas
- [ ] Fine-tuning del modelo LLM en dominio legal
- [ ] Hybrid search (dense + sparse)
- [ ] Re-ranking de resultados
- [ ] Generación de respuestas multimodales
- [ ] Cache de embeddings

### Funcionalidades
- [ ] Comparación de documentos
- [ ] Extracción de entidades legales
- [ ] Generación de resúmenes ejecutivos
- [ ] Alertas de cambios legales
- [ ] Colaboración multi-usuario

### Infraestructura
- [ ] Docker y Docker Compose
- [ ] CI/CD con GitHub Actions
- [ ] Deployment en cloud
- [ ] Monitoreo con Prometheus
- [ ] Backups automáticos

##  Contribución

Este es un proyecto de código abierto. Las contribuciones son bienvenidas:

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## Licencia

Este proyecto es 100% open source y gratuito. Licencia MIT.

## Soporte

Para preguntas o problemas:
- Abre un issue en GitHub
- Consulta la documentación de la API en `/api/docs`
- Revisa los logs en `./logs/app.log`

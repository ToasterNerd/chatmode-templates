---
applyTo: "**/mechquote-api-extra/**/*.py"
description: "Instructions for FastAPI development in MechQuote API Extra"
---

# FastAPI Instructions - MechQuote API Extra

## 🌐 Language: ENGLISH ONLY

All code must be in English:
- Variable/function names: `analyze_geometry()`, `calculate_costs()`
- Comments and docstrings: `# Process DXF file`, `"""Run nesting optimization"""`
- New file names: `laser_service.py`, `nesting_utils.py`

## 🔧 Code Formatting Rules (CRITICAL)

**All Python code MUST comply with pre-commit hooks:**

### Line Length: MAX 119 CHARACTERS
```
⚠️ EVERY line must be < 120 characters (use max 119 to be safe)
   Lines with 120+ characters will FAIL validation
```

### Import Order (isort)
```python
# 1. Standard library
import json
import os
from pathlib import Path
from typing import List, Optional

# 2. Third-party
from fastapi import APIRouter, File, Form, HTTPException, UploadFile
from fastapi.responses import JSONResponse
from pydantic import BaseModel, Field

# 3. Local imports
from laserapp.services.laser_service import LaserCuttingService

from .models import LaserAnalysisResponse
```

### String Quotes
```python
# ✅ Use single quotes
name = 'value'
description = 'Laser cutting analysis'

# ❌ Avoid double quotes (pre-commit will fix them)
```

### Breaking Long Lines
```python
# Long function signatures - break parameters
@router.post('/analyze')
async def analyze_laser_cutting(
    upload_dxf_file: UploadFile = File(..., description='DXF file'),
    cantidad_piezas: int = Form(..., description='Pieces'),
    plancha_largo: float = Form(..., description='Sheet length'),
):
    pass

# Long f-strings - break into multiple lines
message = (
    f'Analysis completed: {result.pieces} pieces, '
    f'utilization: {result.utilization:.1f}%'
)

# Long print/log statements
print(
    f'📥 Receiving file: {filename}, '
    f'size: {file_size}, params: {params}'
)
```

## Project Structure

```
api/
├── main.py                 # Entry point, CORS config, routers
├── laserapp/
│   ├── routers/           # API endpoints
│   │   ├── laser.py       # Laser cutting analysis
│   │   └── config.py      # User configuration
│   ├── services/          # Business logic
│   │   └── laser_service.py
│   ├── models/            # Pydantic schemas
│   │   └── laser.py
│   └── laser_cutting_system/  # Analysis system
│       ├── analysis/      # Nesting algorithms
│       ├── config/        # System configuration
│       ├── core/          # Core logic
│       ├── geometry/      # Geometric processing
│       └── reporting/     # Report generation
```

## Router Definition

```python
from fastapi import APIRouter, File, Form, HTTPException, UploadFile
from fastapi.responses import JSONResponse

router = APIRouter(
    prefix='/api/laser',
    tags=['laser'],
    responses={404: {'description': 'Not found'}},
)

@router.post('/analyze')
async def analyze_laser_cutting(
    upload_dxf_file: UploadFile = File(..., description='DXF file'),
    cantidad_piezas: int = Form(..., description='Number of pieces'),
    tipo_material: str = Form(..., description='Material type'),
):
    """
    Analyze a DXF file for laser cutting.
    
    **Process:**
    1. DXF file validation
    2. Geometry analysis
    3. Nesting optimization
    4. Quote generation
    """
    try:
        # Validate file
        if not upload_dxf_file.filename.lower().endswith('.dxf'):
            raise HTTPException(
                status_code=400,
                detail='File must be a valid DXF'
            )
        
        # Process...
        result = await laser_service.analyze(upload_dxf_file, params)
        return JSONResponse(content=result)
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

## Pydantic Models

```python
from pydantic import BaseModel, Field
from typing import Optional, List

class HealthResponse(BaseModel):
    status: str
    environment: str
    version: str

class LaserAnalysisRequest(BaseModel):
    cantidad_piezas: int = Field(..., gt=0, description='Number of pieces')
    plancha_largo: float = Field(..., gt=0)
    plancha_ancho: float = Field(..., gt=0)
    plancha_espesor: float = Field(..., gt=0)
    tipo_material: str
    calidad_u_rango: int = Field(2, ge=1, le=5)
    
class LaserAnalysisResponse(BaseModel):
    success: bool
    analysis_id: str
    resultado: dict
    archivos_generados: List[str]
```

## Services

```python
class LaserCuttingService:
    """Service for laser cutting analysis."""
    
    def __init__(self):
        self.config = SystemConfig()
    
    async def analyze(
        self, 
        dxf_file: UploadFile, 
        params: dict
    ) -> dict:
        """
        Execute complete laser cutting analysis.
        
        Args:
            dxf_file: Uploaded DXF file
            params: Analysis parameters
            
        Returns:
            Dictionary with analysis results
        """
        # Save temporary file
        temp_path = await self._save_temp_file(dxf_file)
        
        try:
            # Analyze geometry
            geometry = self._analyze_geometry(temp_path)
            
            # Run nesting
            nesting = self._run_nesting(geometry, params)
            
            # Calculate costs
            costs = self._calculate_costs(nesting, params)
            
            return {
                'geometry': geometry,
                'nesting': nesting,
                'costs': costs
            }
        finally:
            # Clean up temporary file
            os.remove(temp_path)
```

## CORS Configuration

```python
from fastapi.middleware.cors import CORSMiddleware

origins = [
    'http://localhost:8000',
    'http://localhost:8001',
    'http://127.0.0.1:8000',
    # Add production URLs
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=['*'],
    allow_headers=['*'],
    max_age=3600,
)
```

## Health Checks

```python
@app.get('/', response_model=HealthResponse)
async def root():
    """Root endpoint - health check"""
    return HealthResponse(
        status='healthy',
        environment=ENVIRONMENT,
        version=API_VERSION
    )

@app.get('/health', response_model=HealthResponse)
async def health_check():
    """Health check for monitoring"""
    return HealthResponse(
        status='healthy',
        environment=ENVIRONMENT,
        version=API_VERSION
    )
```

## DXF File Handling

```python
import ezdxf

def process_dxf(file_path: str) -> dict:
    """Process DXF file with ezdxf."""
    doc = ezdxf.readfile(file_path)
    msp = doc.modelspace()
    
    entities = {
        'lines': [],
        'circles': [],
        'arcs': [],
        'polylines': []
    }
    
    for entity in msp:
        if entity.dxftype() == 'LINE':
            entities['lines'].append({
                'start': entity.dxf.start,
                'end': entity.dxf.end
            })
        # ... more types
    
    return entities
```

## Nesting Algorithms

```python
from shapely.geometry import Polygon
import rectpack

def optimize_nesting(
    pieces: List[dict], 
    sheet_width: float, 
    sheet_height: float,
    margin: float = 5.0
) -> dict:
    """
    Optimize piece distribution on sheet.
    
    Args:
        pieces: List of pieces with geometry
        sheet_width: Sheet width in mm
        sheet_height: Sheet height in mm
        margin: Separation between pieces in mm
    """
    packer = rectpack.newPacker()
    
    # Add bins (sheets)
    packer.add_bin(sheet_width, sheet_height)
    
    # Add rectangles (pieces)
    for piece in pieces:
        packer.add_rect(
            piece['width'] + margin,
            piece['height'] + margin
        )
    
    packer.pack()
    
    return {
        'positions': packer.rect_list(),
        'utilization': calculate_utilization(packer)
    }
```

## Logging

```python
import logging

logger = logging.getLogger(__name__)

# In functions
logger.info(f'📥 Receiving file: {filename}')
logger.warning(f'⚠️ Invalid parameter: {param}')
logger.error(f'❌ Processing error: {error}')

# Debug with emojis for clarity
print(f'🔍 DEBUG - Parameters: {params}')
print(f'✅ Analysis completed: {result}')
```

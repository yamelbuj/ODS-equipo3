# Proyecto GUI en Tkinter — Documentación técnica

Este repo arma una app de escritorio con **Tkinter** (UI nativa de Python) dividida en ventanas modulares. Aquí te explico qué hace cada módulo, cómo corre el proyecto y cómo extenderlo.

---

## Estructura prevista

```
<repo-root>/
├─ .vscode/
│  └─ launch.json                 # Config de depuración para VS Code (PYTHONPATH=src)
├─ src/
│  ├─ app/
│  │  └─ main.py                  # Punto de entrada (esperado por launch.json)
│  └─ ui/
│     ├─ win_home.py              # Ventana “Home / Bienvenida”
│     ├─ win_list.py              # Ventana CRUD simple con Listbox
│     └─ win_table.py             # Ventana de tabla (Treeview) cargada desde CSV
├─ data/
│  └─ sample.csv                  # Datos de ejemplo (nombre, valor1, valor2)
└─ README.md (este archivo)
```

> **Nota:** Los archivos cargados muestran las 3 ventanas (`win_home.py`, `win_list.py`, `win_table.py`) y la configuración de **VS Code** (`launch.json`). El `main.py` no está incluido en los adjuntos, pero la configuración lo espera en `src/app/main.py`. Abajo te dejo una plantilla mínima de `main.py` compatible.

---

## Cómo ejecutar

### Opción A: VS Code (recomendado)
1. Abre la carpeta del repo en VS Code.
2. Asegúrate de tener la extensión **Python** instalada.
3. Revisa `.vscode/launch.json` y que exista `src/app/main.py`.
4. Presiona **F5** y selecciona la configuración “Programa con PYTHONPATH=src”.

### Opción B: Consola
Configura `PYTHONPATH` para que `src` esté en el path y ejecuta `python src/app/main.py`.
En Windows PowerShell:
```powershell
$env:PYTHONPATH="$PWD/src"; python .\srcpp\main.py
```
En macOS/Linux (bash/zsh):
```bash
PYTHONPATH="$PWD/src" python ./src/app/main.py
```

---

## Dependencias

- Python 3.8+ (recomendado 3.10+)
- Tkinter (viene con la instalación estándar de Python en la mayoría de sistemas)
- No se usan librerías externas.

---

## Módulos de UI

### `win_home.py` — Ventana de bienvenida
- Crea un `Toplevel` con título “Home / Bienvenida” y tamaño 360×220.
- Contiene un `Frame` con padding, un label en **negritas**, un texto guía y dos botones:
  - **Mostrar mensaje**: lanza `messagebox.showinfo("Info", "¡Equipo listo!")`.
  - **Cerrar**: destruye la ventana.
- Pensada como ventana simple para saludar y probar UI.

### `win_list.py` — Lista con CRUD básico
- Crea un `Toplevel` (420×300) con `Frame` principal y un `Listbox` de 10 filas.
- Entrada (`Entry`) para escribir ítems y tres acciones:
  - **Agregar**: inserta el texto (si no está vacío) y limpia el `Entry`. Si está vacío, muestra `messagebox.showwarning`.
  - **Eliminar seleccionado**: borra el elemento seleccionado en el `Listbox`.
  - **Limpiar**: vacía toda la lista.
- Botón **Cerrar** para destruir la ventana.
- Esqueleto ideal para practicar/encapsular un CRUD simple.

### `win_table.py` — Tabla con `Treeview` (lee CSV)
- Crea un `Toplevel` (480×300) y arma un `ttk.Treeview` con columnas: **nombre**, **valor1**, **valor2**.
- Intenta leer `data/sample.csv` asumiendo estructura tipo:
  ```csv
  nombre,valor1,valor2
  Alfa,10,20
  Beta,15,30
  ```
- La ruta se resuelve como: `Path(__file__).resolve().parents[2] / "data" / "sample.csv"`,
  es decir: **dos niveles arriba** de este archivo, en `data/sample.csv`.
- Si el CSV no existe, muestra un aviso y **no** carga la tabla.
- Botón **Cerrar** para destruir la ventana.

> **Recomendación:** Valida encabezados y agrega manejo de errores (por ejemplo, envolver la lectura en `try/except` para errores de CSV incompleto).

---

## `launch.json` — Depuración con PYTHONPATH

El perfil **“Programa con PYTHONPATH=src”** ejecuta:
- `program`: `${workspaceFolder}/src/app/main.py`
- `cwd`: `${workspaceFolder}`
- `env`: `PYTHONPATH=${workspaceFolder}/src`

Eso permite importar paquetes relativos a `src` sin hackear `sys.path`.

---

## Plantilla mínima de `main.py`

Crea `src/app/main.py` con algo así:

```python
import tkinter as tk
from tkinter import ttk

# Importa las ventanas. Ajusta el paquete según tu estructura real.
from ui.win_home import open_win_home
from ui.win_list import open_win_list
from ui.win_table import open_win_table

def main():
    root = tk.Tk()
    root.title("Demo Tkinter – Ventanas")
    root.geometry("420x280")

    frm = ttk.Frame(root, padding=16)
    frm.pack(fill="both", expand=True)

    ttk.Label(frm, text="Pantalla principal", font=("Segoe UI", 12, "bold")).pack(pady=(0, 8))
    ttk.Button(frm, text="Home / Bienvenida", command=lambda: open_win_home(root)).pack(fill="x", pady=4)
    ttk.Button(frm, text="Lista (CRUD)", command=lambda: open_win_list(root)).pack(fill="x", pady=4)
    ttk.Button(frm, text="Tabla (CSV → Treeview)", command=lambda: open_win_table(root)).pack(fill="x", pady=4)

    ttk.Button(frm, text="Salir", command=root.destroy).pack(pady=8)

    root.mainloop()

if __name__ == "__main__":
    main()
```

---

## CSV de ejemplo (`data/sample.csv`)

Crea el archivo si no existe:
```csv
nombre,valor1,valor2
Alfa,10,20
Beta,15,30
Gamma,5,40
```

---

## Buenas prácticas y siguientes pasos

1. **Encapsula estado**: convierte funciones sueltas en clases si la UI crece (p. ej., `class ListWindow(tk.Toplevel)`).
2. **Validación**: en `win_table.py`, valida que las columnas existan y maneja errores de conversión.
3. **Theming**: usa `ttk.Style()` y temas (`"clam"`, `"vista"`) para look & feel consistente.
4. **DTO/Modelos**: para tablas, define dataclasses que representen filas; así separas UI de datos.
5. **Persistencia**: en `win_list.py`, guarda/lee la lista a JSON/CSV para conservar ítems entre sesiones.
6. **i18n**: centraliza textos (“Cerrar”, “Agregar”, etc.) en un módulo `i18n.py` si vas a soportar varios idiomas.
7. **Pruebas**: para lógica no-UI, extrae funciones puras y cubre con `pytest`.
8. **Empaquetado**: si quieres distribuir, mira `pyinstaller` (`--onefile`) para generar ejecutables.

---

## Troubleshooting rápido

- **No abre la tabla**: verifica que exista `data/sample.csv` y que tenga encabezados correctos.
- **ImportError** al ejecutar: confirma que `PYTHONPATH` apunte a `src` y que los imports usen paquetes válidos (`from ui.win_home import ...`). 
- **Ventanas fuera de foco**: si pulsas botones y “no ves nada”, revisa si la ventana quedó detrás; `Toplevel` no es modal por defecto.

---

## Licencia
Define una licencia (MIT/Apache-2.0) en el repo si piensas compartir el código.

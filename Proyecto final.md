import tkinter as tk
from tkinter import ttk, messagebox
import datetime

usuarios = {}

class Usuario:
    def __init__(self, nombre, curp, num_control, nss, turno, genero, area):
        self.nombre = nombre
        self.curp = curp
        self.num_control = num_control
        self.nss = nss
        self.turno = turno
        self.genero = genero
        self.area = area
        self.faltas = 0
        self.suspendido_hasta = None
        self.vacaciones = []
        self.retardos = 0
        self.entradas = []
        self.salidas = []

def registrar_usuario():
    nombre = entry_nombre.get()
    curp = entry_curp.get()
    num_control = entry_num_control.get()
    nss = entry_nss.get()
    turno = combo_turno.get()
    genero = combo_genero.get()
    area = entry_area.get()
    if not all([nombre, curp, num_control, nss, turno, genero, area]):
        messagebox.showerror("Error", "Todos los campos son obligatorios.")
        return
    key = (curp, num_control)
    if key in usuarios:
        messagebox.showerror("Error", "Usuario ya registrado.")
        return
    usuarios[key] = Usuario(nombre, curp, num_control, nss, turno, genero, area)
    messagebox.showinfo("Éxito", "Registro exitoso. Ahora puede iniciar sesión.")
    frame_registro.pack_forget()
    frame_login.pack()

def login_usuario():
    curp = entry_login_curp.get()
    num_control = entry_login_num_control.get()
    key = (curp, num_control)
    if key not in usuarios:
        messagebox.showerror("Error", "Usuario no encontrado.")
        return
    global usuario_actual
    usuario_actual = usuarios[key]
    frame_login.pack_forget()
    actualizar_panel_usuario()
    frame_usuario.pack()

def actualizar_panel_usuario():
    lbl_nombre_val.config(text=usuario_actual.nombre)
    lbl_faltas_val.config(text=f"{usuario_actual.faltas}/5")
    lbl_retardos_val.config(text=str(usuario_actual.retardos))
    lbl_vacaciones_val.config(text=", ".join(usuario_actual.vacaciones) if usuario_actual.vacaciones else "Ninguna")
    lbl_entradas_val.config(text="\n".join(usuario_actual.entradas[-5:]))
    lbl_salidas_val.config(text="\n".join(usuario_actual.salidas[-5:]))
    if usuario_actual.suspendido_hasta:
        hoy = datetime.date.today()
        if hoy <= usuario_actual.suspendido_hasta:
            lbl_suspension_val.config(text=f"Hasta {usuario_actual.suspendido_hasta}")
            btn_marcar_entrada.config(state="disabled")
            btn_marcar_salida.config(state="disabled")
            btn_agregar_falta.config(state="disabled")
        else:
            usuario_actual.suspendido_hasta = None
            lbl_suspension_val.config(text="No")
            btn_marcar_entrada.config(state="normal")
            btn_marcar_salida.config(state="normal")
            btn_agregar_falta.config(state="normal")
    else:
        lbl_suspension_val.config(text="No")
        btn_marcar_entrada.config(state="normal")
        btn_marcar_salida.config(state="normal")
        btn_agregar_falta.config(state="normal")

def agregar_falta():
    if usuario_actual.faltas < 5:
        usuario_actual.faltas += 1
        if usuario_actual.faltas > 5:
            usuario_actual.faltas = 5
        if usuario_actual.faltas == 5:
            usuario_actual.suspendido_hasta = datetime.date.today() + datetime.timedelta(days=7)
            messagebox.showwarning("Suspensión", "Has sido suspendido por una semana.")
    actualizar_panel_usuario()

def agregar_vacacion():
    fecha = entry_vacacion.get()
    try:
        datetime.datetime.strptime(fecha, "%Y-%m-%d")
    except ValueError:
        messagebox.showerror("Error", "Formato de fecha inválido. Use AAAA-MM-DD.")
        return
    usuario_actual.vacaciones.append(fecha)
    entry_vacacion.delete(0, tk.END)
    actualizar_panel_usuario()

def marcar_entrada():
    hora = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    usuario_actual.entradas.append(hora)
   
    if datetime.datetime.now().time() > datetime.time(8, 10):
        usuario_actual.retardos += 1
    actualizar_panel_usuario()

def marcar_salida():
    hora = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    usuario_actual.salidas.append(hora)
    actualizar_panel_usuario()

def cerrar_sesion():
    frame_usuario.pack_forget()
    frame_login.pack()

ventana = tk.Tk()
ventana.title("Control de Asistencias Hospital")

frame_registro = tk.Frame(ventana)
tk.Label(frame_registro, text="Registro de Usuario", font=("Arial", 14)).pack(pady=10)
tk.Label(frame_registro, text="Nombre completo:").pack()
entry_nombre = tk.Entry(frame_registro)
entry_nombre.pack()
tk.Label(frame_registro, text="CURP:").pack()
entry_curp = tk.Entry(frame_registro)
entry_curp.pack()
tk.Label(frame_registro, text="Número de control:").pack()
entry_num_control = tk.Entry(frame_registro)
entry_num_control.pack()
tk.Label(frame_registro, text="Número de seguro social:").pack()
entry_nss = tk.Entry(frame_registro)
entry_nss.pack()
tk.Label(frame_registro, text="Turno:").pack()
combo_turno = ttk.Combobox(frame_registro, values=["Matutino", "Vespertino", "Nocturno"])
combo_turno.pack()
tk.Label(frame_registro, text="Género:").pack()
combo_genero = ttk.Combobox(frame_registro, values=["Masculino", "Femenino", "Otro"])
combo_genero.pack()
tk.Label(frame_registro, text="Área de trabajo:").pack()
entry_area = tk.Entry(frame_registro)
entry_area.pack()
tk.Button(frame_registro, text="Registrar", command=registrar_usuario).pack(pady=10)

frame_login = tk.Frame(ventana)
tk.Label(frame_login, text="Inicio de Sesión", font=("Arial", 14)).pack(pady=10)
tk.Label(frame_login, text="CURP:").pack()
entry_login_curp = tk.Entry(frame_login)
entry_login_curp.pack()
tk.Label(frame_login, text="Número de control:").pack()
entry_login_num_control = tk.Entry(frame_login)
entry_login_num_control.pack()
tk.Button(frame_login, text="Iniciar Sesión", command=login_usuario).pack(pady=10)

frame_usuario = tk.Frame(ventana)
tk.Label(frame_usuario, text="Panel de Usuario", font=("Arial", 14)).grid(row=0, column=0, columnspan=2, pady=10)
tk.Label(frame_usuario, text="Nombre:").grid(row=1, column=0, sticky="e")
lbl_nombre_val = tk.Label(frame_usuario, text="")
lbl_nombre_val.grid(row=1, column=1, sticky="w")
tk.Label(frame_usuario, text="Faltas:").grid(row=2, column=0, sticky="e")
lbl_faltas_val = tk.Label(frame_usuario, text="")
lbl_faltas_val.grid(row=2, column=1, sticky="w")
tk.Label(frame_usuario, text="Suspensión:").grid(row=3, column=0, sticky="e")
lbl_suspension_val = tk.Label(frame_usuario, text="")
lbl_suspension_val.grid(row=3, column=1, sticky="w")
tk.Label(frame_usuario, text="Retardos:").grid(row=4, column=0, sticky="e")
lbl_retardos_val = tk.Label(frame_usuario, text="")
lbl_retardos_val.grid(row=4, column=1, sticky="w")
tk.Label(frame_usuario, text="Vacaciones:").grid(row=5, column=0, sticky="e")
lbl_vacaciones_val = tk.Label(frame_usuario, text="")
lbl_vacaciones_val.grid(row=5, column=1, sticky="w")
tk.Label(frame_usuario, text="Entradas recientes:").grid(row=6, column=0, sticky="e")
lbl_entradas_val = tk.Label(frame_usuario, text="", justify="left")
lbl_entradas_val.grid(row=6, column=1, sticky="w")
tk.Label(frame_usuario, text="Salidas recientes:").grid(row=7, column=0, sticky="e")
lbl_salidas_val = tk.Label(frame_usuario, text="", justify="left")
lbl_salidas_val.grid(row=7, column=1, sticky="w")

btn_marcar_entrada = tk.Button(frame_usuario, text="Marcar Entrada", command=marcar_entrada)
btn_marcar_entrada.grid(row=8, column=0, pady=5)
btn_marcar_salida = tk.Button(frame_usuario, text="Marcar Salida", command=marcar_salida)
btn_marcar_salida.grid(row=8, column=1, pady=5)
btn_agregar_falta = tk.Button(frame_usuario, text="Agregar Falta", command=agregar_falta)
btn_agregar_falta.grid(row=9, column=0, pady=5)
tk.Label(frame_usuario, text="Agregar día vacacional (AAAA-MM-DD):").grid(row=10, column=0, sticky="e")
entry_vacacion = tk.Entry(frame_usuario)
entry_vacacion.grid(row=10, column=1, sticky="w")
tk.Button(frame_usuario, text="Agregar Vacación", command=agregar_vacacion).grid(row=11, column=0, columnspan=2, pady=5)
tk.Button(frame_usuario, text="Cerrar Sesión", command=cerrar_sesion).grid(row=12, column=0, columnspan=2, pady=10)

frame_registro.pack()

ventana.mainloop()

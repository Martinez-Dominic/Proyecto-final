import tkinter as tk
from tkinter import messagebox
import datetime

usuarios = {}

class Usuario:
    def __init__(self, nombre, curp, num_control, turno):
        self.nombre = nombre
        self.curp = curp
        self.num_control = num_control
        self.turno = turno
        self.entradas = []
        self.salidas = []

def registrar_usuario():
    nombre = entry_nombre.get()
    curp = entry_curp.get()
    num_control = entry_num_control.get()
    turno = combo_turno.get()

    if not all([nombre, curp, num_control, turno]):
        messagebox.showerror("Error", "Todos los campos son obligatorios.")
        return

    key = (curp, num_control)
    if key in usuarios:
        messagebox.showerror("Error", "Usuario ya registrado.")
        return

    usuarios[key] = Usuario(nombre, curp, num_control, turno)
    messagebox.showinfo("Éxito", "Registro exitoso. Inicia sesión.")
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
    lbl_turno_val.config(text=usuario_actual.turno)
    lbl_entradas_val.config(text="\n".join(usuario_actual.entradas[-5:]))
    lbl_salidas_val.config(text="\n".join(usuario_actual.salidas[-5:]))

def marcar_entrada():
    hora = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    usuario_actual.entradas.append(hora)
    actualizar_panel_usuario()

def marcar_salida():
    hora = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    usuario_actual.salidas.append(hora)
    actualizar_panel_usuario()

def cerrar_sesion():
    frame_usuario.pack_forget()
    frame_login.pack()

ventana = tk.Tk()
ventana.title("Control de Asistencia")

# Frame de registro
frame_registro = tk.Frame(ventana)
tk.Label(frame_registro, text="Registro", font=("Arial", 14)).pack(pady=10)
tk.Label(frame_registro, text="Nombre completo:").pack()
entry_nombre = tk.Entry(frame_registro)
entry_nombre.pack()
tk.Label(frame_registro, text="CURP:").pack()
entry_curp = tk.Entry(frame_registro)
entry_curp.pack()
tk.Label(frame_registro, text="Número de control:").pack()
entry_num_control = tk.Entry(frame_registro)
entry_num_control.pack()
tk.Label(frame_registro, text="Turno:").pack()
combo_turno = tk.Entry(frame_registro)
combo_turno.pack()
tk.Button(frame_registro, text="Registrar", command=registrar_usuario).pack(pady=10)

# Frame de login
frame_login = tk.Frame(ventana)
tk.Label(frame_login, text="Inicio de Sesión", font=("Arial", 14)).pack(pady=10)
tk.Label(frame_login, text="CURP:").pack()
entry_login_curp = tk.Entry(frame_login)
entry_login_curp.pack()
tk.Label(frame_login, text="Número de control:").pack()
entry_login_num_control = tk.Entry(frame_login)
entry_login_num_control.pack()
tk.Button(frame_login, text="Iniciar Sesión", command=login_usuario).pack(pady=10)

# Frame de usuario
frame_usuario = tk.Frame(ventana)
tk.Label(frame_usuario, text="Panel de Usuario", font=("Arial", 14)).grid(row=0, column=0, columnspan=2, pady=10)
tk.Label(frame_usuario, text="Nombre:").grid(row=1, column=0, sticky="e")
lbl_nombre_val = tk.Label(frame_usuario, text="")
lbl_nombre_val.grid(row=1, column=1, sticky="w")
tk.Label(frame_usuario, text="Turno:").grid(row=2, column=0, sticky="e")
lbl_turno_val = tk.Label(frame_usuario, text="")
lbl_turno_val.grid(row=2, column=1, sticky="w")
tk.Label(frame_usuario, text="Entradas:").grid(row=3, column=0, sticky="e")
lbl_entradas_val = tk.Label(frame_usuario, text="", justify="left")
lbl_entradas_val.grid(row=3, column=1, sticky="w")
tk.Label(frame_usuario, text="Salidas:").grid(row=4, column=0, sticky="e")
lbl_salidas_val = tk.Label(frame_usuario, text="", justify="left")
lbl_salidas_val.grid(row=4, column=1, sticky="w")

tk.Button(frame_usuario, text="Marcar Entrada", command=marcar_entrada).grid(row=5, column=0, pady=5)
tk.Button(frame_usuario, text="Marcar Salida", command=marcar_salida).grid(row=5, column=1, pady=5)
tk.Button(frame_usuario, text="Cerrar Sesión", command=cerrar_sesion).grid(row=6, column=0, columnspan=2, pady=10)

frame_registro.pack()

ventana.mainloop()

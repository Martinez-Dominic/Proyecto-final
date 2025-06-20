import tkinter as tk
from tkinter import ttk
import time

def registrar_llegada():
    nombre = entry_nombre.get()
    turno = combo_turno.get()

    if not nombre or not turno:
        label_resultado.config(text="Por favor ingresa tu nombre y selecciona un turno.", fg="red")
        return

    hora = time.strftime("%H:%M:%S")
    mensaje = f"{nombre} ({turno}) llegó a las {hora}"
    label_resultado.config(text=mensaje, fg="green")


ventana = tk.Tk()
ventana.title("Registro de Llegada")
ventana.geometry("350x250")

tk.Label(ventana, text="Nombre:").pack(pady=5)
entry_nombre = tk.Entry(ventana)
entry_nombre.pack()

tk.Label(ventana, text="Turno:").pack(pady=5)
combo_turno = ttk.Combobox(ventana, values=["Matutino", "Vespertino"])
combo_turno.pack()
combo_turno.set("Matutino")  

tk.Button(ventana, text="Registrar Llegada", command=registrar_llegada).pack(pady=20)

label_resultado = tk.Label(ventana, text="", font=("Arial", 12))
label_resultado.pack()

ventana.mainloop()

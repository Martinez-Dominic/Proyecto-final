import tkinter as tk
from tkinter import ttk
import time


entradas = []
salidas = []

def marcar_entrada():
    nombre = entry_nombre.get()
    turno = combo_turno.get()
    if not nombre or not turno:
        label_mensaje.config(text="Por favor, ingresa nombre y turno.", fg="red")
        return

    hora = time.strftime("%Y-%m-%d %H:%M:%S")
    entradas.append(f"{nombre} - {turno} - Entrada: {hora}")
    actualizar_historial()
    label_mensaje.config(text="Entrada registrada", fg="green")

def marcar_salida():
    nombre = entry_nombre.get()
    turno = combo_turno.get()
    if not nombre or not turno:
        label_mensaje.config(text="Por favor, ingresa nombre y turno.", fg="red")
        return

    hora = time.strftime("%Y-%m-%d %H:%M:%S")
    salidas.append(f"{nombre} - {turno} - Salida: {hora}")
    actualizar_historial()
    label_mensaje.config(text="Salida registrada", fg="green")

def actualizar_historial():
    texto_entradas = "\n".join(entradas[-5:])
    texto_salidas = "\n".join(salidas[-5:])
    label_entradas.config(text=texto_entradas)
    label_salidas.config(text=texto_salidas)


ventana = tk.Tk()
ventana.title("Registro de Asistencia")
ventana.geometry("500x400")

tk.Label(ventana, text="Nombre:").pack()
entry_nombre = tk.Entry(ventana)
entry_nombre.pack()

tk.Label(ventana, text="Turno:").pack()
combo_turno = ttk.Combobox(ventana, values=["Matutino", "Vespertino"])
combo_turno.pack()
combo_turno.set("Matutino")


tk.Button(ventana, text="Marcar Entrada", command=marcar_entrada).pack(pady=5)
tk.Button(ventana, text="Marcar Salida", command=marcar_salida).pack(pady=5)


label_mensaje = tk.Label(ventana, text="", fg="green")
label_mensaje.pack()


tk.Label(ventana, text="Últimas Entradas:").pack()
label_entradas = tk.Label(ventana, text="", justify="left", bg="#f0f0f0", anchor="w")
label_entradas.pack(fill="x", padx=10)

tk.Label(ventana, text="Últimas Salidas:").pack()
label_salidas = tk.Label(ventana, text="", justify="left", bg="#f0f0f0", anchor="w")
label_salidas.pack(fill="x", padx=10)

ventana.mainloop()

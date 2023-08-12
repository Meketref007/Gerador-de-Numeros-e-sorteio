import tkinter as tk
from tkinter import scrolledtext, messagebox
from itertools import permutations
import random

geracao_permutacoes = True
numero_sorteado = None

def alternar_geracao():
    global geracao_permutacoes
    geracao_permutacoes = not geracao_permutacoes
    if geracao_permutacoes:
        alternar_botao.config(text='Alternar para Números Aleatórios')
        sortear_botao.config(state=tk.DISABLED)
    else:
        alternar_botao.config(text='Alternar para Permutações')
        sortear_botao.config(state=tk.NORMAL)

def gerar_numeros():
    global geracao_permutacoes, numero_sorteado
    resultado_text.config(state=tk.NORMAL)
    resultado_text.delete(1.0, tk.END)
    
    sortear_botao.config(state=tk.DISABLED)
    
    if geracao_permutacoes:
        numeros_gerados = []
        for p in permutations(range(1, 10), 9):
            numero = ''.join(map(str, p))
            numeros_gerados.append(numero)
    else:
        numeros_gerados = random.sample(range(1, 10), 9)
    
    random.shuffle(numeros_gerados)
    for numero in numeros_gerados:
        resultado_text.insert(tk.END, str(numero) + '\n')
    
    numeros_gerados_global.extend(numeros_gerados)
    sortear_botao.config(state=tk.NORMAL)
    resultado_text.config(state=tk.DISABLED)
    
    numero_sorteado = None
    sortear_botao.config(text='Sortear Número')

def sortear_numero():
    global numero_sorteado
    if numeros_gerados_global:
        if not numero_sorteado:
            numero_sorteado = random.choice(numeros_gerados_global)
            sortear_botao.config(text='Sortear Novamente')
            resultado_sorteio.config(text=f'Número sorteado: {numero_sorteado}')
            resultado_text.tag_remove("selecionado", "1.0", tk.END)

            pos = resultado_text.search(str(numero_sorteado), "1.0", tk.END)

            while pos:
                start = pos
                end = f"{start} + {len(str(numero_sorteado))}c"
                resultado_text.tag_add("selecionado", start, end)
                resultado_text.tag_config("selecionado", background="yellow")
                resultado_text.see(start)
                pos = resultado_text.search(str(numero_sorteado), f"{end} + 1c", tk.END)
                
            messagebox.showinfo("Número Sorteado", f"O número sorteado é: {numero_sorteado}")
        else:
            messagebox.showinfo("Aviso", "Gere novos números para sortear novamente.")
    else:
        messagebox.showinfo("Aviso", "Gere números antes de sortear.")

# Create the main window
janela = tk.Tk()
janela.title('Gerador e Sorteio de Números')
janela.geometry('600x450')

# Frame for the buttons
botoes_frame = tk.Frame(janela)
botoes_frame.pack(side=tk.LEFT, padx=10, pady=10)

# Create a button to toggle between number generation modes
alternar_botao = tk.Button(botoes_frame, text='Alternar para Números Aleatórios', command=alternar_geracao)
alternar_botao.pack(pady=5, fill=tk.X)

# Description for the toggle button
alternar_descricao = tk.Label(botoes_frame, text='Clique para alternar entre geração de números')
alternar_descricao.pack(fill=tk.X)

# Create a button to generate numbers
gerar_botao = tk.Button(botoes_frame, text='Gerar Números', command=gerar_numeros)
gerar_botao.pack(pady=5, fill=tk.X)

# Description for the generate button
gerar_descricao = tk.Label(botoes_frame, text='Clique para gerar números')
gerar_descricao.pack(fill=tk.X)

# Create a button to draw a number
sortear_botao = tk.Button(botoes_frame, text='Sortear Número', state=tk.DISABLED, command=sortear_numero)
sortear_botao.pack(pady=5, fill=tk.X)

# Frame for the text area
texto_frame = tk.Frame(janela)
texto_frame.pack(side=tk.RIGHT, padx=10, pady=10, fill=tk.BOTH, expand=True)

# Create a text area to display generated numbers
resultado_text = scrolledtext.ScrolledText(texto_frame, wrap=tk.WORD, state=tk.DISABLED)
resultado_text.pack(fill=tk.BOTH, expand=True)

# Global list to store the generated numbers
numeros_gerados_global = []

# Create a label to display the drawn number
resultado_sorteio = tk.Label(janela, text='')
resultado_sorteio.pack()

# Start the GUI event loop
janela.mainloop()

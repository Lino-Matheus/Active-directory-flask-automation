# Automação Active Directory com Flask


## Descrição do Projeto

Este projeto demonstra a automação do **Active Directory (AD)** utilizando **Flask**, permitindo a integração de aplicações web em Python com ambientes corporativos. A aplicação simplifica tarefas administrativas, como gerenciamento de usuários, grupos e permissões, de forma centralizada.

A principal funcionalidade é o **upload de planilhas na interface web**, possibilitando a **criação em massa de usuários no AD**, definição de senhas iniciais e organização em unidades administrativas (OUs). Isso agiliza e padroniza processos administrativos, reduzindo erros manuais.

---

## Funcionalidades

- Upload de planilhas Excel (.xlsx) via web.
- Criação automática de usuários no Active Directory.
- Definição de senhas iniciais.
- Organização de usuários em **Unidades Organizacionais (OUs)**.
- Geração de logs de sucesso e erro para cada operação.
- Interface web simples com formulário de upload.

---

## Tecnologias Utilizadas

- **Python 3**
- **Flask** (Web Framework)
- **Pandas** (Leitura de planilhas Excel)
- **ldap3** (Integração com Active Directory)
- **HTML/CSS** (Interface web)
- **Windows Server com Active Directory**

---

## Estrutura do Projeto
C:\IntranetAD
│── app.py # Script principal da aplicação Flask
│── uploads\ # Pasta para armazenar planilhas enviadas
│── logs\ # Pasta para armazenar logs de operação
│
└── templates
└── upload.html # Formulário de upload de planilhas
└── result.html # Página de resultados (opcional)
└── foto.png # Logo exibido na interface web

[upload.html](https://github.com/user-attachments/files/21994466/upload.html)

[app.py](https://github.com/user-attachments/files/22007341/app.py)
from flask import Flask, request, render_template
import pandas as pd
from ldap3 import Server, Connection, ALL, NTLM
import os

os.makedirs("uploads", exist_ok=True)
os.makedirs("logs", exist_ok=True)

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('upload.html')

@app.route('/upload', methods=['POST'])
def upload():
    file = request.files['file']
    file_path = os.path.join("uploads", file.filename)
    file.save(file_path)
    
    df = pd.read_excel(file_path)
    
    colunas = ['Nome', 'Sobrenome', 'Username', 'Email', 'Unidade Organizacional', 'Senha Inicial']
    if not all(col in df.columns for col in colunas):
        return f"Planilha inválida. Colunas obrigatórias: {colunas}"

    server = Server('Alpha.local', get_info=ALL)
    conn = Connection(server, user='ALPHA\\admin', password='Senai@134', authentication=NTLM)
    if not conn.bind():
        return f"Falha ao conectar no AD: {conn.result}"

    log_sucesso = []
    log_erro = []

    for index, row in df.iterrows():
        try:
            username = row['Username']
            dn = f"CN={row['Nome']} {row['Sobrenome']},OU={row['Unidade Organizacional']},DC=Alpha,DC=local"

            conn.add(dn, ['user'], {
                'sAMAccountName': username,
                'userPrincipalName': f"{username}@Alpha.local",
                'givenName': row['Nome'],
                'sn': row['Sobrenome'],
                'mail': row['Email']
            })
            
            conn.extend.microsoft.modify_password(dn, row['Senha Inicial'])
            conn.modify(dn, {'userAccountControl': [('MODIFY_REPLACE', 512)]})

            log_sucesso.append(username)
        except Exception as e:
            log_erro.append(f"{username}: {str(e)}")

    with open("logs/log.txt", "a") as f:
        f.write(f"Sucesso: {log_sucesso}\nErros: {log_erro}\n")

    return f"Usuários criados: {log_sucesso} <br> Erros: {log_erro}"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)



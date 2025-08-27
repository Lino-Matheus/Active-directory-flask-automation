# Active-directory-flask-automation
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
└── logo.png # Logo exibido na interface web




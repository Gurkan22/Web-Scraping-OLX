# Web-Scraping-OLX

> Automação RPA que coleta anúncios de PlayStation 4 da OLX, filtra apenas consoles com critérios inteligentes, atualiza uma planilha no Google Sheets e envia um relatório por e-mail com CSV anexo.

[![UiPath Studio](https://img.shields.io/badge/UiPath_Studio-FF6D00?style=flat-square&logo=uipath&logoColor=white)](https://www.uipath.com/)
[![Google Sheets API](https://img.shields.io/badge/Google_Sheets_API-34A853?style=flat-square&logo=google-sheets&logoColor=white)](https://developers.google.com/sheets/api)
[![Web Scraping](https://img.shields.io/badge/Web_Scraping-B8860B?style=flat-square)](https://en.wikipedia.org/wiki/Web_scraping)
[![VB.NET](https://img.shields.io/badge/VB.NET-512BD4?style=flat-square&logo=.net&logoColor=white)](https://learn.microsoft.com/dotnet/visual-basic/)

---

## Funcionalidades

- Coleta as N primeiras páginas da busca da OLX (configurável)
- Extrai título, preço e link de cada anúncio via Data Scraping + JavaScript injection
- Filtra automaticamente: mantém apenas consoles PS4 com preço ≥ R$ 800
- Exclui acessórios, jogos, outros modelos (PS2, PS3, PS5) e anúncios com defeito
- Atualiza planilha Google Sheets via API REST com OAuth2
- Envia e-mail com link da planilha e CSV anexo via SMTP
- Suporte a agendamento via UiPath Orchestrator

---

## Arquitetura
```
Main.xaml
├── Workflows/
│   ├── ColetarDados.xaml        → scraping das páginas OLX
│   ├── TratarDados.xaml         → filtro de consoles
│   ├── EnviarDadosPlanilha.xaml → Google Sheets API
│   └── EnviarEmail.xaml         → geração de CSV + envio SMTP
```

---

## Pré-requisitos

- UiPath Studio Community (Windows target)
- Microsoft Edge com extensão UiPath instalada
- Conta Google com Google Sheets API habilitada
- Service Account com credenciais `.json` (ver configuração abaixo)
- Conta de e-mail com SMTP habilitado (Gmail: senha de app)

---

## Pacotes UiPath utilizados

| Pacote | Uso |
|---|---|
| `UiPath.UIAutomation.Activities` | navegação e scraping |
| `UiPath.System.Activities` | DataTable, loops, condicionais |
| `UiPath.GSuite.Activities` | integração Google Sheets |
| `UiPath.Mail.Activities` | envio de e-mail SMTP |
| `UiPath.WebAPI.Activities` | requisições HTTP para Google API |

---

## Configuração

### 1. Google Sheets API

1. Acesse [console.cloud.google.com](https://console.cloud.google.com)
2. Crie um projeto e habilite a **Google Sheets API**
3. Crie uma **Service Account** e baixe o arquivo `credentials.json`
4. Compartilhe a planilha com o e-mail da Service Account
5. Coloque o `credentials.json` em `C:\Users\[usuario]\credentials.json`

### 2. Variáveis sensíveis

Antes de rodar, configure no código:

Main.xaml:
```vb
' Editar os argumentos dos InvokeWorkflow EnviarDadosPlanilha e EnviarEmail

credntialsPath value = "Caminho da Credencial"
sheetId value = "Id da Planilha"
emailDestino value = "E-mail destino para envio da planilha"
```

EnviarDadosPlanilha.xaml:
```vb
' Editar o caminho de escrita do tokenResponse, gerado pelo HTTP Request
Output File Target Folder {} = "caminho do seu token_response"

' Editar o caminho de leitura do tokenResponse
tokenResponse = System.IO.File.ReadAllText("caminho do seu tokenresponse")

' Editar o caminho de escrita do clearResponse, gerado pelo HTTP Request
Output File Target Folder {} = "caminho do seu clearResponse")

' Editar o caminho de escrita do writeResponse, gerado pelo HTTP Request
Output File Target Folder {} = "caminho do seu writeresponse")
```

EnviarEmail.xaml:
```vb
' Editar o caminho a ser salvo o CSV
Dim csvPath As String = "Caminho do CSV"

' Ediar os dados de quem vai enviar o E-mail
smtp.Credentials = New NetworkCredential("seu@email.com", "sua_senha_de_app")
mail.From = New MailAddress("seu@email.com")
```

> ⚠️ **Nunca suba senhas ou tokens para o repositório.**

---

## Como executar

1. Clone o repositório e abra a pasta no UiPath Studio
2. Configure as variáveis sensíveis conforme acima
3. Rode o `Main.xaml` com `Ctrl+F6`

---

## Filtro de consoles — lógica

Um anúncio é mantido se:

- O título contém `"console"`, **OU**
- O título contém `"ps4"` / `"playstation 4"` **E** preço ≥ R$ 800

E é removido se o título contém: `defeito`, `quebrado`, `ruim`.

---

## Agendamento

O processo pode ser publicado no **UiPath Orchestrator** (Community Cloud, gratuito) e agendado com triggers por cron expression para execução automática.

---

## Autor

Desenvolvido como projeto de portfólio para estudo de RPA — Pedro Lucas Almeida dos Santos — Ciência da Computação, UFF.

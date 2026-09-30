# site-um-blazor
homework

# SiteUmBlazor

Projeto em **C# / Blazor Web App / .NET 10**, desenvolvido para a lista
`13.lista_net_blazor.pdf`, da disciplina Desenvolvimento Web / Usabilidade,
Dev. Web, Mobile e Jogos, do professor Daniel Henrique Matos de Paiva.

**Aluno:** Lucas Brandão Viana  
**Curso:** Ciência da Computação  
**Interesse de estudo:** Cybersecurity (segurança cibernética)

## O que o PDF solicitou

Criar um projeto chamado **SiteUmBlazor**, com algoritmos em .NET devidamente
indentados, contendo quatro exercícios:

| Exercício | Arquivo e rota | Comportamento solicitado |
| --- | --- | --- |
| 1. Sobre Mim | `Sobre.razor` em `/sobre` | Usar `@page`; mostrar o nome completo em um `h1` e um parágrafo sobre o curso e os interesses de estudo. |
| 2. Contador de Clicks | `Contador.razor` em `/contador` | Declarar `quantidade = 0`; mostrar `Número de cliques: X`; adicionar um botão `Clique Aqui` ligado ao método `Incrementar` por `@onclick`. |
| 3. Alternador de Mensagem | `Mensagem.razor` em `/mensagem` | Declarar `exibirMensagem = false`; alternar o estado com `AlternarVisibilidade`; usar `@if` para mostrar a mensagem e alterar o texto do botão entre `Exibir Mensagem` e `Ocultar Mensagem`. |
| 4. Placar Interativo | `Placar.razor` em `/placar` | Iniciar `pontos = 0`; adicionar os botões `Somar 1 Ponto`, `Subtrair 1 Ponto` e `Zerar Placar`; impedir pontuação negativa. |

A mensagem do exercício 3 é exatamente:

> Bem-vindo ao desenvolvimento web com .NET 10!

O PDF permite equipes de até cinco alunos e pede entrega no prazo definido
pelo professor, sem informar a data no documento. Também solicita criar e
publicar o repositório **site-um-blazor** no GitHub. Essa etapa ficou para
publicação manual pelo aluno, conforme solicitado; nenhum repositório remoto
foi criado ou atualizado.

## Requisitos

- **SDK do .NET 10** instalado. Verifique com `dotnet --list-sdks`.
- Um navegador atualizado.
- Para executar pelo F5 no VS Code, a extensão **C#** da Microsoft instalada.

O projeto não usa banco de dados, API externa, CDN ou pacotes NuGet de terceiros.
Depois de compilado, pode ser utilizado localmente sem conexão com a internet.

## Como executar

Abra a pasta `homework-listablazor` no VS Code. O arquivo `SiteUmBlazor.csproj`
está diretamente na raiz dessa pasta.

No terminal integrado:

```powershell
dotnet run
```

Acesse **http://localhost:5183** no navegador. A porta está definida no perfil
`http` de `Properties/launchSettings.json`.

Também é possível executar de qualquer terminal PowerShell:

```powershell
Set-Location 'C:\Users\lucas\OneDrive\Documentos\vs code uni\homework-listablazor'
dotnet run --project SiteUmBlazor.csproj
```

Para recompilar automaticamente ao editar os arquivos:

```powershell
dotnet watch
```

Para usar o **F5**, abra a raiz do projeto no VS Code, escolha a configuração
**Executar SiteUmBlazor** em Executar e Depurar e inicie. A tarefa `build`
compila o projeto antes da execução, e o navegador abre quando o servidor
estiver pronto. As configurações estão em `.vscode/launch.json` e
`.vscode/tasks.json`.

Para compilar sem iniciar o servidor:

```powershell
dotnet build
```

Para encerrar o servidor no terminal, use `Ctrl+C`. Se a porta 5183 já estiver
ocupada, encerre a outra execução ou escolha outra porta:

```powershell
dotnet run --urls http://localhost:5184
```

Nesse caso, abra `http://localhost:5184`.

## Execução no Windows com controle de aplicativos

Se aparecer `An Application Control policy has blocked this file` citando
`SiteUmBlazor.exe`, o Windows está bloqueando o executável nativo gerado
pelo SDK (apphost).

O projeto usa `<UseAppHost>false</UseAppHost>` em `SiteUmBlazor.csproj`.
Assim, a aplicação é compilada como DLL e `dotnet run` a executa pelo runtime
`dotnet` instalado, sem depender de `SiteUmBlazor.exe`. Essa configuração
não modifica as políticas de segurança do Windows.

Depois de atualizar os arquivos, execute normalmente:

```powershell
dotnet run
```

Evite `--no-build` na primeira execução após essa alteração, para que o .NET
atualize os arquivos de compilação. O F5 continua usando a DLL, conforme a
configuração em `.vscode/launch.json`.

Se a política também bloquear o runtime ou a DLL, solicite a autorização
ao administrador responsável pelo computador.

Referência: [propriedade UseAppHost na documentação do .NET](https://learn.microsoft.com/dotnet/core/project-sdk/msbuild-props#useapphost).

## Páginas disponíveis

| Endereço padrão | Página |
| --- | --- |
| `http://localhost:5183/` | Início com links para os quatro exercícios. |
| `http://localhost:5183/sobre` | Apresentação de Lucas Brandão Viana. |
| `http://localhost:5183/contador` | Contador de cliques. |
| `http://localhost:5183/mensagem` | Mensagem com visibilidade alternável. |
| `http://localhost:5183/placar` | Placar com soma, subtração e reinicialização. |

O menu superior permite navegar entre as páginas. O layout se adapta a telas
menores e os botões podem ser acionados pelo teclado.

## Como os códigos funcionam

### Inicialização, roteamento e interatividade

`Program.cs` registra os componentes Razor com `AddRazorComponents()` e
habilita o modo de servidor interativo com `AddInteractiveServerComponents()`.
`MapRazorComponents<App>().AddInteractiveServerRenderMode()` configura a
aplicação e seus endpoints.

`Components/App.razor` define o HTML principal, o idioma `pt-BR`, as folhas de
estilo e o script do Blazor. `Routes` e `HeadOutlet` usam
`@rendermode="InteractiveServer"`, permitindo que os cliques executem os
métodos C# no servidor e atualizem a interface sem recarregar a página.

`Components/Routes.razor` procura componentes com `@page` e aplica o layout
`MainLayout`. Por exemplo, `@page "/sobre"` torna `Sobre.razor` acessível pela
rota `/sobre`. `NavLink` realça no menu a página atual.

Os botões permanecem desabilitados durante a renderização inicial, antes de
o Blazor estabelecer a conexão interativa. Isso evita cliques sem efeito
enquanto a página está conectando. Se a conexão com o servidor cair, o
componente `ReconnectModal` apresenta as opções de reconexão em português.

### Exercício 1: Sobre Mim

`Sobre.razor` contém o `h1` com o nome completo e os parágrafos sobre
Ciência da Computação e Cybersecurity. É uma página de apresentação com
conteúdo HTML e a diretiva `@page "/sobre"`.

### Exercício 2: Contador

O bloco `@code` de `Contador.razor` declara `private int quantidade = 0`.
O botão usa `@onclick="Incrementar"`. O método `Incrementar` executa
`quantidade++`, e o Blazor atualiza automaticamente o `h1` que contém
`Número de cliques: @quantidade`.

### Exercício 3: Mensagem

`Mensagem.razor` inicia `private bool exibirMensagem = false`. O método
`AlternarVisibilidade` executa `exibirMensagem = !exibirMensagem`.
O operador `!` inverte o valor booleano.

Uma expressão condicional escolhe o rótulo do botão. O bloco
`@if (exibirMensagem)` inclui o parágrafo na página somente quando o valor
é `true`; ao voltar para `false`, o parágrafo é removido.

### Exercício 4: Placar

`Placar.razor` inicia `private int pontos = 0` e conecta cada botão ao seu
método por `@onclick`:

- `SomarPonto`: executa `pontos++`.
- `SubtrairPonto`: executa `pontos = Math.Max(0, pontos - 1)`.
- `ZerarPlacar`: executa `pontos = 0`.

`Math.Max` retorna o maior entre zero e o resultado da subtração. Assim, clicar
em subtrair quando o placar já está em zero mantém a pontuação em zero.

### Estado das páginas

Os valores ficam na memória do componente, sem persistência em banco ou
armazenamento do navegador. Recarregar a página ou sair e voltar ao exercício
cria um novo componente com seu estado inicial. O estado não é compartilhado
como uma variável global entre usuários.

## Organização dos arquivos

```text
homework-listablazor/
├── SiteUmBlazor.csproj
├── Program.cs
├── README.md
├── .gitignore
├── .vscode/
│   ├── launch.json
│   └── tasks.json
├── Properties/
│   └── launchSettings.json
├── Components/
│   ├── App.razor
│   ├── Routes.razor
│   ├── _Imports.razor
│   ├── Layout/
│   │   ├── MainLayout.razor
│   │   ├── MainLayout.razor.css
│   │   └── ReconnectModal.razor, .razor.css e .razor.js
│   └── Pages/
│       ├── Home.razor
│       ├── Sobre.razor
│       ├── Contador.razor
│       ├── Mensagem.razor
│       ├── Placar.razor
│       ├── Error.razor
│       └── NotFound.razor
├── wwwroot/
│   └── app.css
├── appsettings.json
└── appsettings.Development.json
```

`app.css` define a aparência geral e a adaptação a dispositivos menores.
`MainLayout` contém o cabeçalho, a navegação, a área de conteúdo e o rodapé.
As páginas `Error` e `NotFound` tratam falhas e endereços inexistentes.
As pastas `bin/` e `obj/` são geradas pelo .NET e ignoradas pelo `.gitignore`.

## Roteiro de verificação

O projeto foi compilado sem erros nem avisos. Foram verificados no navegador
os dados da apresentação, os três cliques do contador, a reinicialização ao
recarregar, a exibição/ocultação da mensagem e as operações do placar,
incluindo subtrair quando a pontuação já é zero.

1. Compile com `dotnet build` e execute com `dotnet run`.
2. Acesse `/sobre` e confira nome completo, curso e interesse de estudo.
3. Em `/contador`, confira o valor inicial zero; clique três vezes e veja três.
4. Em `/mensagem`, confira que a mensagem começa oculta; clique para exibir e
   depois para ocultar, verificando também a mudança no texto do botão.
5. Em `/placar`, clique em subtrair no zero: deve permanecer em zero. Some
   três pontos, subtraia um e confira dois. Clique em zerar e confira zero.
6. Recarregue uma página interativa e verifique que o estado volta ao inicial.

## Referências

- Enunciado: `13.lista_net_blazor.pdf`, fornecido pelo professor.
- [Modos de renderização do Blazor (.NET 10)](https://learn.microsoft.com/aspnet/core/blazor/components/render-modes?view=aspnetcore-10.0).


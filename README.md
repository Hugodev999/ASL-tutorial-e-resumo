Imagine que você tem uma receita de bolo bem detalhada. Nela está escrito: "Misture os ovos. SE o forno já estiver quente, coloque a massa para assar; SENÃO, espere 5 minutos. Quando terminar de assar, avise que o bolo está pronto."

A ASL (Amazon States Language) é exatamente como essa receita de bolo, mas escrita para computadores dentro do serviço de nuvem da Amazon (a AWS).

1. O que é a ASL em palavras simples?
Quando uma empresa cria um sistema (como um aplicativo de entregas), várias "tarefinhas" precisam acontecer em ordem:

-Confirmar o pagamento.

-Avisar o restaurante.

-Chamar o entregador.

-Enviar uma notificação para o seu celular.

A ASL é a linguagem usada para descrever o passo a passo e o caminho que o sistema deve seguir do começo ao fim.

Em vez de escrever linhas e linhas de códigos complexos para ligar um sistema no outro, você escreve um texto simples em formato de "lista de tarefas" (usando um formato chamado JSON) dizendo à AWS: "Primeiro faça a tarefa A. Se der certo, faça a B. Se der errado, tente de novo."

A AWS pega esse seu texto em ASL e cria um mapa visual interativo (um fluxograma), mostrando exatamente em qual passo as coisas estão acontecendo em tempo real.

2. Passo a Passo Prático: Usando a ASL pela primeira vez
Vamos criar um pequeno fluxo de decisões onde o computador vai ler uma nota de um aluno e dizer se ele foi Aprovado ou se precisa de Recuperação.

Passo 1: Acesse a AWS
Entre no Console da AWS (aws.amazon.com).

Na barra de pesquisa no topo, digite Step Functions e clique nele.

Passo 2: Crie um novo fluxo de trabalho
Clique no botão laranja Create state machine (Criar máquina de estado).

Escolha o modo de edição em código (Write in code ou mude para a aba de código).

Deixe o tipo marcado como Standard.

Passo 3: Cole a nossa "receita" em ASL
Apague o que estiver no editor e cole o código abaixo. Leia os comentários para entender o que cada trecho faz:

JSON
{
  "Comment": "Fluxo simples em ASL para verificar se o aluno passou de ano",
  "StartAt": "VerificarNota",
  "States": {

    "VerificarNota": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.nota",
          "NumericGreaterThanEquals": 7,
          "Next": "AlunoAprovado"
        }
      ],
      "Default": "AlunoEmRecuperacao"
    },

    "AlunoAprovado": {
      "Type": "Pass",
      "Result": "Parabéns! Você foi aprovado.",
      "ResultPath": "$.mensagem",
      "Next": "FimDoProcesso"
    },

    "AlunoEmRecuperacao": {
      "Type": "Pass",
      "Result": "Atenção! Você está de recuperação.",
      "ResultPath": "$.mensagem",
      "Next": "FimDoProcesso"
    },

    "FimDoProcesso": {
      "Type": "Succeed"
    }

  }
}
O que esse texto está dizendo?

StartAt: "Comece na etapa VerificarNota."

Choice: "Analise a variável nota. Se for maior ou igual a 7, vá para AlunoAprovado. Se for menor (o padrão/Default), vá para AlunoEmRecuperacao."

Pass: Apenas cria uma mensagem de resposta sem precisar executar um programa complexo.

Succeed: "O processo terminou com sucesso!"

Passo 4: Veja o mapa visual ganhar vida
No lado direito do editor, observe a caixa Graph view (Visualização em gráfico). A AWS vai transformar o texto que você colou em um gráfico bonito com setinhas mostrando os caminhos possíveis.

Passo 5: Salve a sua máquina
Clique no botão Next (Avançar) no topo da tela.

Em State machine name, dê um nome, por exemplo: VerificadorDeNotas.

Role até o final da página e clique em Create state machine.

Passo 6: Faça o teste!
Agora vamos simular dados entrando na nossa receita para ver o computador tomando a decisão sozinho.

Clique no botão Start execution (Iniciar execução).

Na caixa de texto chamada Input (Entrada), cole a nota de um aluno:

JSON
{
  "aluno": "Carlos",
  "nota": 8.5
}
Clique em Start execution.

Veja a mágica: No mapa visual, o caminho percorrido ficará verde. Você verá que o sistema leu a nota 8.5 e seguiu sozinho para o caminho de AlunoAprovado.

Se você clicar em Start execution de novo e mandar uma nota menor que 7 (como "nota": 5.0), verá no gráfico que o sistema mudará de rota automaticamente e seguirá para AlunoEmRecuperacao.

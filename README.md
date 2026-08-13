# ASL-tutorial-e-resumo
Criar e executar um fluxo de trabalho com a Amazon States Language (ASL) no AWS Step Functions é um processo direto. Vamos construir um tutorial prático e hands-on, no qual criaremos um fluxo para validar e processar um pedido de compra.

Neste tutorial, você aprenderá a criar a máquina de estado no console da AWS, aplicar a lógica em ASL, simular entradas e analisar a execução visualmente.

Pré-requisitos
Uma conta ativa na AWS.

Acesso ao serviço AWS Step Functions.

Passo 1: Acessar o AWS Step Functions
Faça login no Console de Gerenciamento da AWS.

Na barra de busca superior, digite Step Functions e selecione o serviço.

No painel principal, clique no botão Create state machine (Criar máquina de estado).

Passo 2: Escolher a Opção de Construção
A AWS oferece um construtor visual (Workflow Studio). Para aprender a sintaxe do ASL, selecione a opção Write in code (Escrever em código) ou mude para a aba de código no editor.

Em Type (Tipo), mantenha selecionado Standard (ideal para aprender e acompanhar o histórico de transições).

Passo 3: Escrever o Código ASL
Substitua qualquer código existente no editor de código do console pelo documento JSON abaixo.

Este fluxo em ASL faz o seguinte:

Recebe um pedido com valor (valorTotal).

Usa um estado Choice para verificar se o valor é maior que 100.

Se for maior que 100, direciona para aprovação manual/especial (AprovarPedidoGrande).

Se for menor ou igual a 100, aprova automaticamente (AprovarPedidoPadrao).

Finaliza com um estado de sucesso (PedidoConcluido).

JSON
{
  "Comment": "Tutorial de introdução ao ASL - Fluxo de Validação de Pedidos",
  "StartAt": "VerificarValorDoPedido",
  "States": {
    "VerificarValorDoPedido": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.valorTotal",
          "NumericGreaterThan": 100,
          "Next": "AprovarPedidoGrande"
        }
      ],
      "Default": "AprovarPedidoPadrao"
    },
    "AprovarPedidoGrande": {
      "Type": "Pass",
      "Result": {
        "status": "APROVADO",
        "tipoProcessamento": "RevisaoEspecial"
      },
      "ResultPath": "$.resultadoAprovacao",
      "Next": "PedidoConcluido"
    },
    "AprovarPedidoPadrao": {
      "Type": "Pass",
      "Result": {
        "status": "APROVADO",
        "tipoProcessamento": "Automatico"
      },
      "ResultPath": "$.resultadoAprovacao",
      "Next": "PedidoConcluido"
    },
    "PedidoConcluido": {
      "Type": "Succeed"
    }
  }
}
Nota: Usamos o tipo "Pass" neste tutorial porque ele simula a execução de uma tarefa sem precisar criar uma função Lambda real ou infraestrutura adicional.

Passo 4: Visualizar o Diagrama
Observe o painel de Graph view (Visualização em gráfico) ao lado do editor. O console da AWS renderiza o código ASL em um diagrama de fluxo em tempo real. Você deverá ver as ramificações de decisão baseadas nas regras que definiu.

Passo 5: Salvar e Configurar a Máquina de Estado
Clique em Next (Avançar) no canto superior direito.

Em State machine name (Nome da máquina de estado), digite: ValidadorDePedidosASL.

Em Permissions (Permissões), selecione Create new role (Criar nova função), e a AWS gerará as permissões necessárias no IAM automaticamente.

Role até o final da página e clique em Create state machine.

Passo 6: Executar e Testar o Fluxo
Agora vamos simular dois cenários diferentes para ver a tomada de decisão da ASL em ação.

Teste 1: Pedido de alto valor (> 100)
Na página da sua State Machine recém-criada, clique em Start execution (Iniciar execução).

Na caixa Input (Entrada), cole o seguinte JSON:

JSON
{
  "pedidoId": "PED-001",
  "valorTotal": 250.00
}
Clique em Start execution.

Resultado: Observe o diagrama verde. O fluxo seguiu o caminho VerificarValorDoPedido → AprovarPedidoGrande → PedidoConcluido.

Clique no estado AprovarPedidoGrande e confira a aba Output no painel direito. Você verá a propriedade resultadoAprovacao anexada ao JSON original.

Teste 2: Pedido de valor padrão (≤ 100)
Clique novamente no botão Start execution (no topo da página).

Na caixa Input, passe um valor menor:

JSON
{
  "pedidoId": "PED-002",
  "valorTotal": 45.50
}
Clique em Start execution.

Resultado: O fluxo agora pegou a rota Default, passando por AprovarPedidoPadrao.

Próximos Passos
Para evoluir suas habilidades em ASL:

Substituir o Pass por Task: Substitua o estado de aprovação por uma invocação real de AWS Lambda ou gravação direta no Amazon DynamoDB.

Adicionar Resiliência: Inclua blocos de Retry e Catch para tratar possíveis falhas ou timeouts das chamadas.

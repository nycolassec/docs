# Gerenciamento do Ciclo de Vida dos Dados

O Gerenciamento do Ciclo de Vida dos Dados do Microsoft Purview fornece ferramentas e recursos para reter o conteúdo que você precisa manter e excluir o conteúdo que não precisa manter.

## Políticas de retenção e rótulos de retenção

A aplicação de rótulos de retenção e a atribuição de políticas de retenção ajudam as organizações a:
- Cumprir de forma proativa as normas do setor e as políticas internas que exigem que o conteúdo seja mantido por um tempo mínimo.
- Reduz o risco quando houver litígio ou uma violação de segurnaça ao excluir permanentemente o conteúdo antigo que a organização não precisa mais manter.
- Garantir que os usuários trabalhem somente com o conteúdo que é atual e relevante para eles. O conteúdo qye não for mais relevante deve ser excluído.

Gerenciar o conteúdo geralmente exige duas ações: reter o conteúdo e excluir o conteúdo.

- Aretenção de conteúdo evita a exclusão permanente e garante que o conteúdo permaneça disponível para Descoberta Eletrônica.
- Excluir o conteúdo excluirá permanentemente o conteúdo da sua organização.

Com essas duas ações de retenção, você deve configurar as configurações de retenção para os seguintes resultados:

- Somente retenção: retem o conteúdo para sempre ou por um período de tempo especificado.
- Excluir somente: exclui permanentemente o conteúdo após um período especificado.
- Reter e depois Excluir: retenho o conteúdo por um período de tempo especificado e, em seguida, exclua-o permanentemente.

Quando o conteúdo tem configurações de retenção atribuídas, esse conteúdo permanece em seu local original. As pessoas podem continuar a trabalhar com seus documentos ou emails como se nada tivesse sido alterado. Mas se elas editarem ou excluírem o conteúdo incluído na política de retenção, uma cópia do conteúdo será mantida automaticamente em um local seguro. 

Na maioria dos casos, as pessoas não precisam nem saber que seu conteúdo está sujeito a configurações de retenção.

As configurações de retenção funcionam com as seguintes cargas de trabalho:

- SharePoint
- OneDrive
- Equipes da Microsoft
- Viva Engage
- Exchange

Para atribuir suas configurações de retenção ao conteúdo, utilize políticas de retenção e rótulos de retenção com políticas de rótulo. Você pode utilizar apenas um desses métodos ou combiná-los.

#### Políticas de retenção

- Aplicam configurações de retenção em nível de contêiner (site, caixa de correio).
- Uma única política pode abranger vários locais, locais específicos ou usuários.
- Os itens herdam a retenção do contêiner.
- Se o item for movido para fora do contêiner, uma cópia é preservada em um local seguro, porém a retenção não acompanha o item no novo local.

#### Rótulos de retenção

- Aplicam retenção em nível de item (pasta, documento ou email).
- Cada item pode ter apenas um rótulo por vez.
- A retenção acompanha o conteúdo quando movido dentro do Microsoft 365, mas não fora do locatário.
- Podem ser aplicados manualmente pelos usuários (se permitido) ou automaticamente por condições.
- Suportam rótulo padrão para documentos do SharePoint.
- Permitem revisão de disposição antes da exclusão permanente.

Em síntese:
Políticas de retenção controlam retenção por local, enquanto rótulos de retenção oferecem controle granular por item, com maior flexibilidade e governança sobre o ciclo de vida do conteúdo.

***

Rótulos de retenção e políticas que os aplicam

Quando você publica rótulos de retenção, eles são incluídos em uma política de rótulos de retenção que os torna disponíveis para que os administradores e usuários os apliquem ao conteúdo.



























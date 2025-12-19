# Gerenciamento de registros

 O Gerenciamento de Registros do Microsoft Purview ajuda uma organização a cuidar de suas obrigações legais. Ele também ajuda a demonstrar a conformidade com regulamentos e aumenta a eficiência com a disposição regular de itens que não precisam mais ser mantidos, que não têm mais valor ou que não são mais necessários para fins comerciais. O Gerenciamento de Registros do Microsoft Purview inclui muitos recursos, incluindo:

- Rotulagem do conteúdo como um registro.
- Estabelecimento de políticas de retenção e exclusão dentro do rótulo de registro.
- Acionamento da retenção baseada em evento.
- Revisão e validação da disposição.
- Prova de exclusão de registros.
- Exportação de informações sobre itens descartados.

Quando o conteúdo é rotulado como um registro usando um rótulo de retenção, o seguinte acontece:

- Restrições são colocadas em vigor para bloquear determinadas atividades.
- Atividades são registradas em log.
- A prova de disposição é mantida no final do período de retenção.

Assim, itens como documentos e emails podem ser marcados como registros com base nesses rótulos de retenção. Os itens podem ser marcados como registros, mas também podem ser mostrados como registros regulatórios. Os registros regulatórios fornecem outros controles e restrições, como:

- Um rótulo normativo não pode ser removido quando um item tiver sido marcado como um registro normativo.
- Os períodos de retenção não podem ser reduzidos depois que o rótulo tiver sido aplicado.

A diferença mais importante é que: se o conteúdo tiver sido marcado como um registro normativo, ninguém, nem mesmo um administrador global, poderá remover o rótulo. A marcação de um item como um registro normativo pode ter consequências irreversíveis e só deve ser usada quando necessário. Por isso, essa opção não está disponível por padrão e precisa ser habilitada pelo administrador usando o PowerShell.

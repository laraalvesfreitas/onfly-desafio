# Declaração de uso de IA

## Ferramenta e modelo
- Ferramenta: Claude (Anthropic), pela interface de chat claude.ai
- Modelo: Claude Sonnet 5.5

## O que a IA fez
- Explicou o enunciado em linguagem mais simples.
- Interpretou as mensagens de falha do `pytest` e apontou em qual arquivo cada defeito provavelmente estava.
- Revisou os trechos de código que enviei e indicou o que ainda faltava: o uso de `float` no dinheiro (defeito 2), a forma correta do cálculo de noites com `.days` e a ordenação decrescente com desempate (`-amount, hotel_id`).
- Revisou o `report_builder.py` para os pontos não cobertos pelos testes.
- Ajudou a redigir o `README-ENTREGA.md`.

## O que eu fiz
- Montei o ambiente, rodei o `pytest` e o CLI e enviei os resultados e os arquivos.
- Localizei e alterei o código em `currency_converter.py`, `stay_calculator.py`, `status_policy.py` e `report_builder.py`.
- Rodei os testes após as correções até todos passarem.
- Rodei a amostra `exemplo-validacao.csv` e comparei o resultado com a tabela do enunciado.
- Revisei e entendi cada correção antes de registrá-la.








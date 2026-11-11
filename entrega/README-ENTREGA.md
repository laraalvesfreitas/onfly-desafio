# Entrega — Consolidador de reservas de hotel (Onfly)

Autora: Lara Alves de Freitas

## Defeitos corrigidos

| # | Arquivo | Defeito | Causa raiz (uma frase) |
|---|---------|---------|------------------------|
| 1 | `services/currency_converter.py` | Conversão para BRL com a operação invertida | O valor era **dividido** pela taxa em vez de multiplicado por `rate_to_brl`. |
| 2 | `services/currency_converter.py` | Uso de `float` no dinheiro | O valor e a taxa eram convertidos para `float` antes do cálculo, contra a regra de usar `Decimal`; agora é `amount * rate` em `Decimal`. |
| 3 | `services/stay_calculator.py` | Noites negativas na virada de mês (ex.: 30/01 → 02/02 dava -28) | O cálculo subtraía o **dia do mês** das datas em vez de subtrair as datas; agora é `(checkout - checkin).days * rooms`. |
| 4 | `services/status_policy.py` | `NO_SHOW` entrava nos totais financeiros | `is_billable` só excluía `CANCELLED` (`!=`), deixando passar `NO_SHOW`; agora aceita apenas `CONFIRMED`. |
| 5 | `services/report_builder.py` | `top_hotels` em ordem crescente de valor | O `sorted` usava `(amount, hotel_id)` crescente; agora usa `(-amount, hotel_id)`: valor decrescente e desempate por `hotel_id` crescente. |

O `test_regression_report` falhava como consequência do defeito 1 e passou sem alteração própria.

## Como investiguei

1. Li o enunciado e anotei as regras de negócio (status, noites, câmbio, `Decimal`, ordenação).
2. Rodei `pytest -q`: 5 testes falhavam. Usei a mensagem de cada falha para localizar o arquivo:
   - total BRL de 118.45 em vez de 642.00 → conversão (100 + 100/5,42 em vez de 100 + 100×5,42);
   - -28 noites → subtração de dias do mês em vez de datas;
   - 120.00 em vez de 100.00 → `NO_SHOW` (20) somado ao total;
   - ranking começando em H04 → ordenação invertida.
3. Corrigi um defeito por vez, rodando o teste correspondente depois de cada correção.
4. Revisei o `report_builder.py` inteiro para os pontos que os testes não cobrem: contagem de status antes do filtro, `by_month` pelo mês do check-in, média com zero noites e arredondamento half-up. Nada precisou mudar.
5. Revisei `csv_rate_repository.py` e `row_parser.py`: ambos já usam Decimal para valores e taxas, sem necessidade de alteração.
6. Rodei o CLI na amostra `data/exemplo-validacao.csv` e comparei com a tabela do enunciado.

## Como validar

```bash
python3.11 -m venv .venv
source .venv/bin/activate        # no Windows (Git Bash): source .venv/Scripts/activate
pip install -r requirements-dev.txt

pytest -q

PYTHONPATH=src python -m onfly_report \
  --input data/exemplo-validacao.csv \
  --rates data/taxas_cambio.csv \
  --output output/exemplo.json

PYTHONPATH=src python -m onfly_report \
  --input data/reservas_hoteis.csv \
  --rates data/taxas_cambio.csv \
  --output output/report.json
```

## Resultado

- `pytest -q`:  `5 passed in 1.52s`
- Amostra de validação: todos os valores bateram com a tabela do enunciado (12 reservas, 28 noites, total 7566.00, média 270.21, meses e top 5 hotéis).
- Os testes fornecidos e os arquivos `data/reservas_hoteis.csv` e `data/taxas_cambio.csv` não foram alterados.
## Uso de IA

Usei IA neste projeto. A sessão exportada e a declaração estão em `entrega/uso_de_ia/`.

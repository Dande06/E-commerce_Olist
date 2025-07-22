## Tecnologias

- Python 3.x  
- Pandas  
- Matplotlib  
- Jupyter Notebook  

Este projeto utiliza o [dataset da Olist no Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) e foi criado para praticar e demonstrar minhas habilidades em análise de dados com Python.

---


# Análise do E-commerce Olist

Este projeto tem como objetivo realizar uma análise exploratória dos dados do e-commerce brasileiro Olist, com foco em entender o comportamento dos clientes, padrões de venda, logística e satisfação.

Diante disso, este projeto foi desenvolvido com o objetivo de entender o funcionamento do marketplace e responder a perguntas de negócio como:

- Quais estados do Brasil concentram o maior número de pedidos?
- Quais categorias de produtos são mais vendidas, nacionalmente e por região?
- Como está a satisfação dos clientes e o que pode estar afetando negativamente suas avaliações?
- O tempo de entrega influencia na nota da avaliação?
- Qual é o comportamento de consumo por estado (ex: ticket médio, volume de compras)?
- Quais formas de pagamento são mais populares?

---

## 🎯 Objetivo

Realizar uma análise exploratória completa que permita a qualquer gestor ou equipe de dados da empresa:

- Tomar decisões estratégicas baseadas em dados reais;
- Otimizar a logística e o atendimento ao cliente com base no tempo médio de entrega e satisfação;
- Compreender o comportamento regional de compra para segmentação de campanhas de marketing;
- Detectar possíveis gargalos operacionais, como atraso na entrega ou categorias com baixa performance.

A proposta é entregar um material visual, técnico e acessível até para quem não tem conhecimento de programação, com explicações simples e gráficos intuitivos.

---
 # Partimos para os códigos
### 📥 Leitura dos arquivos

Fazemos a leitura dos arquivos CSV que representam diferentes partes do negócio: clientes, localização, pedidos, produtos, vendedores, pagamentos e avaliações.

```python
clientes = pd.read_csv('C:\\olist\\olist_customers_dataset.csv')
localizacao = pd.read_csv('C:\\olist\\olist_geolocation_dataset.csv')
ordem_itens = pd.read_csv('C:\\olist\\olist_order_items_dataset.csv')
ordem_pagamentos = pd.read_csv('C:\\olist\\olist_order_payments_dataset.csv')
avaliacoes = pd.read_csv('C:\\olist\\olist_order_reviews_dataset.csv')
status_pedido = pd.read_csv('C:\\olist\\olist_orders_dataset.csv')
tipo_produto = pd.read_csv('C:\\olist\\olist_products_dataset.csv')
vendedores = pd.read_csv('C:\\olist\\olist_sellers_dataset.csv')
categoria_traducao = pd.read_csv('C:\\olist\\product_category_name_translation.csv')
```
##  Análises Realizadas

### 1. Total de Pedidos | Total de Clientes

Começamos com uma análise basica para responder qual o total de pedido e clientes do dados da Olist

```py
print(f"Total de pedidos: {status_pedido['order_id'].nunique()}")
print(f"Total de clientes únicos: {clientes['customer_unique_id'].nunique()}")
Total de pedidos: 99441
Total de clientes únicos: 96096
```
### 2. Faturamento Total
```py
valor_total = ordem_pagamentos['payment_value'].sum()
print(f"Valor Total Movimentado: R$ {valor_total:,.2f}")
Valor Total Movimentado: R$ 16,008,872.12
```
Com essas duas análises, ja temos o ponto de partida para as demais análises e responder o que isso representa de faturamento da Olist

### 3. Pedidos por Estado
Essa é uma análise geral de pedidos por estado com uma visualização em pyplot em ordem descrente. Nos ajuda a entender qual Estado possui mais pedidos.
```py
pedidos_estado = clientes.merge(status_pedido, on="customer_id")
pedidos_por_estado = pedidos_estado["customer_state"].value_counts().sort_values(ascending=False)

plt.figure(figsize=(12,6))
bars = plt.bar(pedidos_por_estado.index, pedidos_por_estado.values, color="skyblue")
plt.title("Total de Pedidos por Estado", fontsize=14)
plt.xlabel("Estado (UF)")
plt.ylabel("Quantidade de Pedidos")
plt.xticks(rotation=45)
plt.grid(axis="y", linestyle= "--", alpha=0.5)
for bar in bars:
    height = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2,
             height + 100,
             f'{int(height)}',
             ha='center', va='bottom', fontsize=9)
plt.tight_layout()
plt.show()
```
![Gráfico de Pedidos por Estado](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_pedidos_estado.png?raw=true)

### 4. Distribuição dos Tipos de Pagamento
Essa análise nos da uma menção dos meios de pagamentos mais usados pelos clientes na Olist
```py
tipos_pagamentos = ordem_pagamentos["payment_type"].value_counts(normalize=True).sort_values()*100
plt.figure(figsize=(10,5))
bars= plt.barh(tipos_pagamentos.index, tipos_pagamentos.values, color="skyblue")
plt.title("Distruibuição dos Tipos de Pagamentos (%)", fontsize=14)
plt.grid(axis="x", linestyle="--", alpha=(0.5))
for i, v in enumerate(tipos_pagamentos.values):
    plt.text(v + 1, i, f'{v:.1f}%', va="center", fontsize=9)

plt.tight_layout()
plt.show()
```
![Gráfico Tipos de Pagamento](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_pagamento.png?raw=true)

### 5. Top 10 Categorias Mais Vendidas
Aqui, temos de forma resumida o TOP 10 de tipos de produtos vendidos pela Olist.

```py
ordem_produtos = ordem_itens.merge(tipo_produto, on="product_id")
ordem_produtos = ordem_produtos.merge(categoria_traducao, on="product_category_name")

categorias = ordem_produtos["product_category_name"].value_counts().head(10).sort_values(ascending=False)

plt.figure(figsize=(12,6))
bars = plt.bar(categorias.index, categorias.values, color="skyblue")

plt.title("Top 10 Categorias Mais Vendidas", fontsize=14)
plt.xlabel("Categoria")
plt.ylabel("Quantidade Vendida")
plt.xticks(rotation=45)
plt.grid(axis="y", linestyle="--", alpha=0.5)

for bar in bars:
    height = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2,
                height + 20,
                f'{int(height)}',
                ha="center",
                va="bottom",
                fontsize=9)

plt.tight_layout()
plt.show()
```
![Gráfico TOP 10 Categorias](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_top_10_categorias.png?raw=true)

### 6. Mais Vendidos por Estados.
Para complementar melhor o entendido a análse anterior, foi criado outra visão de categorias mais vendidas separadadas por Estados(UFs).

```py
ordem_produtos = ordem_itens.merge(tipo_produto, on='product_id')
ordem_produtos = ordem_produtos.merge(status_pedido, on='order_id')
ordem_produtos = ordem_produtos.merge(clientes[['customer_id', 'customer_state']], on='customer_id')

vendas_estado_categoria = (ordem_produtos.groupby(['customer_state', 'product_category_name']).size() .reset_index(name='quantidade_vendida'))
top_categoria_estado = (vendas_estado_categoria.sort_values('quantidade_vendida', ascending=False).drop_duplicates('customer_state').sort_values('quantidade_vendida'))

plt.figure(figsize=(12, 8))
bars = plt.barh(top_categoria_estado['customer_state'], top_categoria_estado['quantidade_vendida'], color='skyblue')

plt.title("Categoria Mais Vendida por Estado", fontsize=14)
plt.xlabel("Quantidade Vendida")
plt.ylabel("Estado (UF)")
plt.grid(axis='x', linestyle='--', alpha=0.5)

for i, bar in enumerate(bars):
    categoria = top_categoria_estado.iloc[i]['product_category_name']
    qtd = top_categoria_estado.iloc[i]['quantidade_vendida']
    plt.text(bar.get_width() + 50, bar.get_y() + bar.get_height()/2,
             f'{categoria} ({qtd})',
             va='center', fontsize=9)

plt.tight_layout()
plt.show()
```
![Gráfico Mais Vendidos por Estados](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_categorias_vendidas_estado.png?raw=true)

### Apos entender os tipos de categorias mais vendidas e Estados que mais vendem, passamos para as proximas análises.

### 7. Ticket Médio por Estados.
Aqui, vemos que alem dos pedidos por Estados, temos a média de Ticket divididos por eles. É notavel perceber que mesmo São Paulo sendo lider em pedidos vendidos, nâo é o Estado com maior ticket.

```py
ticket = ordem_pagamentos.groupby("order_id")["payment_value"].sum().reset_index()
ticket = ticket.merge(status_pedido[["order_id", "customer_id"]], on="order_id")
ticket = ticket.merge(clientes[["customer_id", "customer_state"]], on="customer_id")

ticket_medio_estado = ticket.groupby("customer_state")["payment_value"].mean().sort_values()

plt.figure(figsize=(10, 6))
bars = plt.barh(ticket_medio_estado.index, ticket_medio_estado.values, color="skyblue")
plt.title("Ticket Médio por Estado (R$)")
plt.xlabel("Valor Médio (R$)")
plt.grid(axis="x", linestyle="--", alpha=0.5)

for bar in bars:
    largura = bar.get_width()
    plt.text(largura + 1, bar.get_y() + bar.get_height()/2,
             f'R$ {largura:.2f}', va="center", fontsize=9)

plt.tight_layout()
plt.show()
```
![Gráfico Ticket Médio](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_ticket_medio.png?raw=true)

### 8. Avaliações de Clientes | Notas.
Saindo um pouco das análises Financeiras | Vendas, partimos para análise de avaliações dos clientes. Focando na distribuição de notas, levando em consideração de 0 a 5.

```py
avaliacoes_ordenadas = avaliacoes["review_score"].value_counts().sort_index()

plt.figure(figsize=(10, 5))
bars = plt.bar(avaliacoes_ordenadas.index.astype(str), avaliacoes_ordenadas.values, color='skyblue')

plt.title("Distribuição das Avaliações dos Clientes", fontsize=14)
plt.xlabel("Nota (de 1 a 5)")
plt.ylabel("Quantidade de Avaliações")
plt.grid(axis='y', linestyle='--', alpha=0.5)
for bar in bars:
    height = bar.get_height()
    plt.text(bar.get_x() + bar.get_width() / 2,
             height + 200,
             f'{int(height)}',
             ha='center', va='bottom', fontsize=9)

plt.tight_layout()
plt.show()
```
![Gráfico de Notas](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_avaliacoes_geral.png?raw=true)

### 8. Média de Notas por Estados.
Desmembrando mais as notas dos clientes, partimos para média de avaliações por Estados(UFs).

```py
clientes_avaliacoes = clientes.merge(status_pedido, on="customer_id").merge(avaliacoes, on="order_id")
avaliacoes_estado = clientes_avaliacoes.groupby("customer_state")["review_score"].mean().sort_values()

plt.figure(figsize=(10, 6))
bars = plt.barh(avaliacoes_estado.index, avaliacoes_estado.values, color='skyblue')

plt.title("Nota Média por Estado", fontsize=14)
plt.xlabel("Nota Média (de 1 a 5)")
plt.ylabel("Estado")
plt.grid(axis='x', linestyle='--', alpha=0.5)
for bar in bars:
    width = bar.get_width()
    plt.text(width + 0.02, bar.get_y() + bar.get_height() / 2,
             f'{width:.2f}',
             va='center', fontsize=9)

plt.tight_layout()
plt.show()
```
![Gráfico Média de Notas|Estados](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_avaliacoes_estado.png?raw=true)

### 9. Interferencia de Notas|Tempo de Entrega.
Por fim, podemos medir a instação dos clientes com o atraso e/ou demora na entrega de produtos. Podemos concluir que: Quanto maior o tempo de entrega, menor será a nota atrelada ao produto.

```py
status_pedido["order_purchase_timestamp"] = pd.to_datetime(status_pedido["order_purchase_timestamp"])
status_pedido["order_delivered_customer_date"] = pd.to_datetime(status_pedido["order_delivered_customer_date"])

status_pedido["tempo_entrega"] = (status_pedido["order_delivered_customer_date"] - status_pedido["order_purchase_timestamp"]).dt.days

tempo_por_estado = clientes.merge(status_pedido, on="customer_id")
tempo_medio_estado = tempo_por_estado.groupby("customer_state")["tempo_entrega"].mean().sort_values()

plt.figure(figsize=(10,6))
plt.scatter(avaliacoes_entrega["tempo_entrega"], avaliacoes_entrega["review_score"], s=30, alpha=0.6)
plt.title("Tempo de Entrega x Avaliação do Cliente")
plt.xlabel("Tempo de Entrega (dias)")
plt.ylabel("Avaliação (Review Score)")
plt.grid(True)
plt.show()
```
![Gráfico Interferencia de Notas](https://github.com/Dande06/E-commerce_Olist/blob/main/grafico_analise_avaliacoes.png?raw=true)

### 10. Power BI.
Simples visualização criada no Power BI apenas para fins de demonstração. O objetivo é mostrar a estrutura de um relatório interativo com gráficos, filtros e indicadores. Vale ressaltar a tendência das notas, se trabalharmos na resolução da melhoria das entregas de produtos, fará que a avaliação melhore com o tempo

![Dashboard BI](https://github.com/Dande06/E-commerce_Olist/blob/main/Olist_PowerBI.PNG)

### Conclusão.
Este projeto evidenciou características regionais importantes no comportamento dos clientes e logística da plataforma Olist. Foi possível identificar estados com maior demanda e satisfação, além de verificar que o tempo de entrega influencia diretamente a avaliação dos clientes, reforçando a importância da eficiência logística.


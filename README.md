# ecommerce_estatistica_trabalho_EBAC
Este projeto consiste na criação de um dashboard interativo para análise de dados de e-commerce, utilizando Python, Pandas e Dash.

import pandas as pd
import dash
from dash import dcc, html, Input, Output
import plotly.express as px

# =========================
# 1. Carregar dados
# =========================
df = pd.read_csv(r"C:\Users\Pessoal\OneDrive\Documentos\EBAC\Profissao_analista de dados\7_Manipulacao dados Python\11_python vizualizacao de dados\ecommerce_estatistica.csv")

# =========================
# 2. Criar app
# =========================
app = dash.Dash(__name__)

app.layout = html.Div([
    html.H1('Dashboard E-commerce', style={'textAlign': 'center'}),

    dcc.Dropdown(
        id='grafico-dropdown',
        options=[
            {'label': 'Histograma de Preços', 'value': 'hist'},
            {'label': 'Dispersão Preço vs Vendas', 'value': 'scatter'},
            {'label': 'Mapa de Correlação', 'value': 'heatmap'},
            {'label': 'Top 10 Marcas', 'value': 'bar'},
            {'label': 'Distribuição por Gênero', 'value': 'pie'},
            {'label': 'Distribuição de Notas', 'value': 'nota'},
            {'label': 'Regressão Preço vs Vendas', 'value': 'reg'}
        ],
        value='hist',
        clearable=False
    ),

    dcc.Graph(id='grafico')
])

# =========================
# 3. Callback
# =========================
@app.callback(
    Output('grafico', 'figure'),
    Input('grafico-dropdown', 'value')
)
def atualizar_grafico(tipo):

    if tipo == 'hist':
        return px.histogram(df, x='Preço', nbins=20,
                            title='Distribuição de Preços')

    elif tipo == 'scatter':
        return px.scatter(df, x='Preço', y='Qtd_Vendidos',
                          title='Preço vs Quantidade Vendida')

    elif tipo == 'heatmap':
        corr = df.corr(numeric_only=True)
        return px.imshow(corr, text_auto=True,
                         title='Mapa de Correlação')

    elif tipo == 'bar':
        top = df['Marca'].value_counts().head(10).reset_index()
        top.columns = ['Marca', 'Quantidade']
        return px.bar(top, x='Marca', y='Quantidade',
                      title='Top 10 Marcas')

    elif tipo == 'pie':
        genero = df['Gênero'].value_counts().reset_index()
        genero.columns = ['Gênero', 'Quantidade']
        return px.pie(genero, names='Gênero', values='Quantidade',
                      title='Distribuição por Gênero')

    elif tipo == 'nota':
        return px.histogram(df, x='Nota', nbins=20,
                            title='Distribuição de Notas')

    elif tipo == 'reg':
        return px.scatter(df, x='Preço', y='Qtd_Vendidos',
                          trendline='ols',
                          title='Regressão: Preço vs Vendas')

# =========================
# 4. Rodar app
# =========================
if __name__ == '__main__':
    app.run(debug=True, use_reloader=False)

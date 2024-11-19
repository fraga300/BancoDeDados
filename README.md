# BancoDeDados
## feito por Gabriel e Joao

## Dependencias do Projeto
> Bibliotecas necessarias
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np

> Pode ser utilizado qualquer ambiente virtual JavaScript para execução

## Procedimentos
> Ao executar o codigo sera criado um arquivo chamado <code>filmes.ipynb</code> onde sera colocado o resultado

```
# Aqui carregamos os dados do arquivo CSV
df = pd.read_csv(r"C:\Users\joaos\Documents\dados.csv.csv")
print(df.head()) 
df.columns = df.columns.str.strip()


# Visualizar os dados limpos
print(df.head())

# Remover valores ausentes
df = df.dropna()

# Padronizar nomes das colunas, se necessário
df.columns = df.columns.str.strip()
df['Estúdio'] = df['Estúdio'].str.strip()

# Aqui geramos os tipos de graficos que queremos
class FilmAnalysis:
    def __init__(self, file_path):
        self.df = pd.read_csv(file_path)
        self.clean_data()
    
    def clean_data(self):
        """Limpa e prepara os dados."""
        self.df = self.df.dropna()
        self.df.columns = self.df.columns.str.strip()
        self.df['Estúdio'] = self.df['Estúdio'].str.strip()
        self.df['Bilheteria'] = self.df['Bilheteria'].apply(self.convert_bilheteria)
        self.df = self.df.dropna(subset=['Bilheteria'])
        self.df['Bilheteria'] = pd.to_numeric(self.df['Bilheteria'], errors='coerce')
    
    @staticmethod
    def convert_bilheteria(value):
        """Converte valores de bilheteria para números."""
        value = value.replace("R$", "").replace("$", "").replace(",", ".").strip()
        if 'B' in value:
            return float(value.replace("B", "")) * 1_000_000_000
        elif 'M' in value:
            return float(value.replace("M", "")) * 1_000_000
        else:
            return float(value)
    
    def plot_genre_popularity(self):
        """Plota a popularidade dos gêneros ao longo dos anos."""
        plt.figure(figsize=(10, 6))
        sns.countplot(data=self.df, x='Ano_Lancamento', hue='Genero', palette='muted')
        plt.title("Popularidade dos Gêneros de Filmes ao Longo dos Anos")
        plt.xlabel("Ano de Lançamento")
        plt.ylabel("Quantidade de Filmes")
        plt.legend(title="Gênero")
        plt.xticks(rotation=45)
        plt.show()
    
    def plot_release_trends(self):
        """Plota tendências de lançamento de filmes ao longo dos anos."""
        anos = self.df.groupby('Ano_Lancamento').size()
        plt.figure(figsize=(10, 6))
        sns.lineplot(x=anos.index, y=anos.values, marker='o')
        plt.title("Tendências de Lançamento de Filmes ao Longo dos Anos")
        plt.xlabel("Ano de Lançamento")
        plt.ylabel("Quantidade de Filmes Lançados")
        plt.show()
    
    def plot_worldwide_revenue(self):
        """Plota a bilheteria mundial por filme."""
        plt.figure(figsize=(14, 8))
        sns.barplot(data=self.df, x='Filme', y='Bilheteria', hue='Filme', palette='Blues_d', dodge=False)
        plt.title("Distribuição de Bilheteria Mundial por Filme")
        plt.xlabel("Filme")
        plt.ylabel("Bilheteria (em bilhões)")
        plt.xticks(rotation=90)
        plt.legend().remove()  # Remove a legenda
        plt.show()
    
    def plot_studio_participation(self):
        """Plota a participação percentual dos estúdios."""
        plt.figure(figsize=(13, 13))
        self.df['Estúdio'].value_counts().plot.pie(
            autopct='%1.1f%%', 
            startangle=140, 
            colors=sns.color_palette("pastel"),
            textprops={'fontsize': 10}
        )
        plt.title("Participação de Estúdios na Produção de Filmes", fontsize=14)
        plt.ylabel('')
        plt.show()
    
    def plot_genre_revenue_distribution(self):
        """Plota a distribuição de bilheteria por gênero."""
        plt.figure(figsize=(12, 8))
        sns.boxplot(data=self.df, x='Genero', y='Bilheteria', palette='Set3')
        plt.title("Distribuição de Bilheteria por Gênero")
        plt.xlabel("Gênero")
        plt.ylabel("Bilheteria (em milhões)")
        plt.xticks(rotation=45)
        plt.show()
    
    def plot_studio_revenue_comparison(self):
        """Compara a bilheteria total por estúdio."""
        studio_revenue = self.df.groupby('Estúdio')['Bilheteria'].sum().sort_values(ascending=False)
        plt.figure(figsize=(14, 8))
        studio_revenue.plot(kind='bar', color='skyblue')
        plt.title("Comparação de Bilheteria por Estúdio")
        plt.xlabel("Estúdio")
        plt.ylabel("Bilheteria Total (em milhões)")
        plt.xticks(rotation=45)
        plt.show()

# Exemplo de uso
file_path = r"C:\Users\joaos\Documents\dados.csv.csv"
analysis = FilmAnalysis(file_path)

analysis.plot_genre_popularity()
analysis.plot_release_trends()
analysis.plot_worldwide_revenue()
analysis.plot_studio_participation()
analysis.plot_genre_revenue_distribution()
analysis.plot_studio_revenue_comparison()

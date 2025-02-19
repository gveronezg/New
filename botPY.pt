## Cálculo da variação percentual e inicialização de colunas
#df["percentual"] = df["close"].pct_change()
#df['acumulado'] = 0.0
#df['VarMM'] = 0.0
df['posicionado'] = 0  # Coluna para indicar se estamos posicionados (1) ou não (0)

# Variáveis de controle
acumulado = 0.0   # Lucro/prejuízo acumulado
entrada = 0.0     # Preço de entrada da operação
posicionado = False  # Flag que indica se estamos em operação
comprado = None      # True para Comprado, False para Vendido
grau = 40            # Parâmetro para comparação com VarMM

# Itera sobre o DataFrame a partir do segundo registro
for i in range(1, len(df)):
    current_index = df.index[i]
    prev_index = df.index[i-1]
    
    # Cálculo da variação de MM1 entre o período atual e o anterior
    diff = df.loc[current_index, 'MM1'] - df.loc[prev_index, 'MM1']
    df.loc[current_index, 'VarMM'] = diff

    if posicionado:
        # Atualiza a coluna 'posicionado' para 1, pois já estamos em operação
        df.loc[current_index, 'posicionado'] = 1

        if comprado:  # Posição Long
            # Critério de saída: se a variação cair abaixo do 'grau'
            if df.loc[prev_index, 'VarMM'] < grau:
                saida = df.loc[current_index, 'open']
                profit = saida - entrada  # Lucro da operação Long
                acumulado += profit
                posicionado = False
                comprado = None
                df.loc[current_index, 'acumulado'] = acumulado
                df.loc[current_index, 'posicionado'] = 0  # Sai da posição
            else:
                # Atualiza o PnL não realizado (flutuante)
                df.loc[current_index, 'acumulado'] = acumulado + (df.loc[current_index, 'open'] - entrada)
        else:  # Posição Short
            # Critério de saída: se a variação subir acima de -grau
            if df.loc[prev_index, 'VarMM'] > -grau:
                saida = df.loc[current_index, 'open']
                profit = entrada - saida  # Lucro da operação Short
                acumulado += profit
                posicionado = False
                comprado = None
                df.loc[current_index, 'acumulado'] = acumulado
                df.loc[current_index, 'posicionado'] = 0  # Sai da posição
            else:
                # Atualiza o PnL não realizado (flutuante) para posição Short
                df.loc[current_index, 'acumulado'] = acumulado + (entrada - df.loc[current_index, 'open'])
    else:
        # Sem posição, garante que a coluna 'posicionado' seja 0
        df.loc[current_index, 'posicionado'] = 0
        
        # Verifica o sinal da barra anterior para possível entrada
        sinal_anterior = df.loc[prev_index, 'sinal']
        if sinal_anterior == 'Long' and df.loc[prev_index, 'VarMM'] >= grau:
            entrada = df.loc[current_index, 'open']
            posicionado = True
            comprado = True
            df.loc[current_index, 'acumulado'] = acumulado
            df.loc[current_index, 'posicionado'] = 1
        elif sinal_anterior == 'Short' and df.loc[prev_index, 'VarMM'] <= -grau:
            entrada = df.loc[current_index, 'open']
            posicionado = True
            comprado = False
            df.loc[current_index, 'acumulado'] = acumulado
            df.loc[current_index, 'posicionado'] = 1
        else:
            df.loc[current_index, 'acumulado'] = acumulado
    

# Define os ticks do eixo X apenas nos horários desejados
posicoes_ticks = [dt for dt in df.index if dt.minute == 0]  # Pegando os horários cheios
rotulos_ticks = [dt.strftime('%H:%M') for dt in posicoes_ticks]  # Formatando como 'HH:MM'

plt.xticks(posicoes_ticks, rotulos_ticks, rotation=45)  # Rotação para melhor visualização
plt.plot(df['acumulado'], label="Mini_Indice")
plt.legend()

# Adiciona linhas verticais na grid, baseadas nos ticks do eixo x
##### ax = plt.gca()
##### ax.xaxis.grid(True, linestyle='dotted', color='grey')

plt.tight_layout()
plt.show()

df[['open','close','sinal','VarMM','acumulado','posicionado']]

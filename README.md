# APIs-Market-Data
Python para finanzas quant de Juan Pablo Pisano

Funciones
Vamos a arrancar por algo que venimos haciendo repetidamente hasta este bloque que es leer datos de mercado de archivos de excel, van a ver en este bloque cómo vamos a bajar datos de mercado directo de la web, pero quise arrancar así porque la inmensa mayoría usa a fondo excel para sus análisis y me pareció un buen puente para empezar a meterse en Python. Pero de ahora en más no vamos a tipear las líneas que tipeábamos cuando leíamos en excel sino que vamos a crear una función que se encargue de ello.

Las funciones son un conjunto de instrucciones (líneas de código) que generalmente necesitamos reutilizar más de una vez.

Son el alma de la programación ya que va a ser muy común que necesitemos reutilizar "pedazos de código" de hecho dejé este tema para el cuarto bloque, no sólo porque quería concentrar el foco en el resto de herramientas primero sino para que ahora se valore más el uso de funciones ya que si llegaron hasta acá seguramente ya se habrán topado con esto en algún otro tutorial y además ya habrán percibido la necesidad de usar este tipo de herramientas.

def hora():
    import datetime as dt
    print(dt.datetime.now())
   
hora()



    2021-07-16 20:56:47.714770

También podemos ir agregando cosas:

def hora():
    import datetime as dt
    time = dt.datetime.now()
    time = time.strftime("%H:%M:%S")
    print(time)
   
hora()



    20:58:48

Pero ¿si quisiéramos guardar esa hora en una variable? Si se fijan la función hora() que definimos, imprime la hora en pantalla pero no podemos "capturar" el dato, solo se ejecuta la función, la imprime y chau.

En general las funciones más allá de ejecutar un script, como en el caso que pusimos recién devuelven un "return", es por eso que la palabra "return" es una palabra reservada de python y no la podemos usar como nombre de variable.

Entonces, veamos como sería  si queremos una función que no solo imprima la hora sino que "me la devuelva" para que se la pueda asignar a una variable.

def hora():
    import datetime as dt
    time = dt.datetime.now()
    time = time.strftime("%H:%M:%S")
    print(time)
    return time

horaActual = hora()
print(horaActual)



    22:36:43
    22:36:43

Si se fijan ahora, esto me imprime dos veces la hora, la primera vez es cuando ejecuta la función, y la segunda cuando le pido que me imprima lo que contiene la variable "horaActual".

Por lo general las funciones no imprimen cosas en pantalla sino que devuelven valores que se los asignamos a una varible, si luego queremos imprimirlos los mandamos con un print pero no se suele hacer eso dentro de una función, quedaría más parecida la función a algo así:

def hora():
    import datetime as dt
    time = dt.datetime.now()
    time = time.strftime("%H:%M:%S")
    return time

horaActual = hora()
print(horaActual)



    10:15:55

Si se preguntan ¿Se puede programar sin usar funciones predefinidas?
La respuesta es obvio que se puede, pero van a ver que son super cómodas y útiles por más que programen pequeños scripts.

Muchos habrás notado que usan los entornos como Jupyter, cuando leen datos de un recurso externo, ya sea un excel o una API o lo que fuera, esos datos quedan "Grabados" en la variable a la que se los asignaron y así la pueden reutilizar cuantas veces quieran, de hecho queda grabado y después de hacer muchas pruebas más, ejecutando otros scripts dentro del mismo kernel, incluso después de horas y decenas de pruebas sigue quedando ahí grabado en dato.

Bien, correcto, pero este tipo de entornos si bien está muy bueno para probar cosas o para aprender, en la vida real, no se usan para proyectos grandes, y los entornos que se usan no tienen ese tipo de memoria, por lo cual para poder reutilizar código que tipearon antes, se usan funciones.

Bueno, vamos al grano, veamos una función concreta, vamos a arrancar por lo básico que vimos en los bloques anteriores que es leer un archivo de excel con datos de mercado.

Si recuerdan, teníamos los excels ordenados de reciente a antiguo y por lo general los necesitábamos a antiguos a recientes así que arranquemos armando una función para este proceso repetitivo.

Las instrucciones eran:

import pandas as pd

data = pd.read_excel('AAPL.xlsx')
data = data.sort_values('timestamp', ascending=True)
data.set_index('timestamp', inplace=True)
data.head()
Bien, ahora pensemos , que pasa si ahora quiero leer los datos de otro excel similar como el SPY que tengo en la misma carpeta?

En ese caso, la primera línea "import pandas as pd" ya no lo voy a tipear más porque ya en la memoria tengo esa librería cargada y no necesito andar cargándola a cada rato, de hecho es una mala práctica hacer esto porque consumo recursos innecesarios, pero las otras 3 líneas si las tengo que tipear de nuevo y solo cambiar "AAPL.xlsx" por "SPY.xlsx", de hecho el ".xlsx" tambien queda igual, solo le tengo que cambiar el ticker.

Entonces, como puedo "automatizar" esas 3 líneas de código? Definiendo una función que ejecute siempre esas líneas.

def leerExcel(nombreArchivo):
    data = pd.read_excel(nombreArchivo)
    data = data.sort_values('timestamp', ascending=True)
    data.set_index('timestamp', inplace=True)
    return data

AAPL = leerExcel('AAPL.xlsx')
SPY = leerExcel('ADRs/SPY.xlsx')

AAPL.head()

Ahora imaginen que yo en el directorio tengo miles de excels con esa imagen estructura de precios de acciones diferentes, ¿no se empieza a notar la utilidad de esto?

Funciones con argumentos con un valor por default
Vamos un poco más, vamos a armar la función para que aparte del ticker le pueda pasar el nombre de alguna carpeta y que no haga falta pasarle el ".xlsx" ya que todos los excels tienen esa misma extensión.

def leerExcel(ticker, carpeta=''):
    if carpeta == '':
        ruta = ticker + '.xlsx'
    else:
        ruta = carpeta + '/' + ticker + '.xlsx'
    
    data = pd.read_excel(ruta)
    data = data.sort_values('timestamp', ascending=True)
    data.set_index('timestamp', inplace=True)
    
    return data

data = leerExcel('AAPL')
data.head()

Fíjense que interesante esto que se puede hacer en las funciones, si ven detenidamente el ejemplo sencillo que acabo de poner, en el mismo al definir la función leerExcel, le paso dos argumentos, una el ticker, y el segundo el nombre de la supuesta carpeta donde se encuentra, pero en este segundo argumento, notarán que les puse ='' en la definición de la función, de esta forma estoy definiendo el valor por default que es un string vacío.

Al estar vacío por default, presupongo que el archivo debe estar en la carpeta donde se ejecuta el script, pero si luego quiero leer un archivo de otra carpeta, le tengo que pasar ese argumento a mi función, veámoslo (yo tengo la carpeta ADRs con los Excel de los ADRs argentinos, tal cual como lo puse en la carpeta de recursos de este libro:

Obviamente deben bajar los archivos y carpetas al mismo lugar (directorio) donde estén ejecutando el código, tengan la precaución de verificar que sí les descarga un archivo comprimido (.zip) deben descomprimirlo para poder leerlo.

data = leerExcel(ticker='GGAL', carpeta='ADRs')
data.head()

Como pueden ver, al tener la función ya definida, solo tengo que llamarla y listo, así que si ahora quisiera leer varios tickers miren que fácil y limpio se hace el código:

tickers = ['GGAL', 'YPF', 'TEO']
data = {}
for ticker in tickers:
    data[ticker] = leerExcel(ticker=ticker, carpeta='ADRs')

Lo que voy a hacer iterando dentro de ese FOR, fíjense que recorro todos los tickets de la lista "tickers", es generar una nueva clave por cada iteración del FOR, para el diccionario "data" que acabo de crear antes de entrar al FOR. 

Ese diccionario tendrá una clave por ticker, y el valor de cada clave será un dataFrame entero (recuerden que los valores de un diccionario, podían ser datos, listas o cualquier objeto de estructura de datos, en este caso un dataframe de pandas).

Entonces, "data" es un diccionario que tendrá los dataFrames de todos los tickers leídos de sus respectivos Excels, comprobemos pidiendo que imprima alguno.

data['YPF'].head()

Sigamos avanzando, no sé si recuerdan, cuando graficamos velas o cuando queríamos hacer algún tipo de análisis de velas, gaps o cualquier cosa que tenga que evaluar los precios OHLC teníamos la dificultad de tener los precios OHLC sin ajustar por dividendos o splits.

Y yo les mostró un código básico de ajustar todos los datos de OHLC en función del AdjClose que suele ser una manera estándar de presentar los datos de series financieras, con él OHLC sin ajustar y el precio de cierre ajustado aparte, bueno, vamos a armar una función que no sólo lea el excel sino que además haga lo siguiente: 

Que me ajuste los datos de OHLC.
Que me devuelva el volumen en millones de dolares en lugar de nominales.

Aclaro que este tipo de ajuste básico de las series de precios financieros, es súper común, lo de los precios OHCL es obvio el por qué(*), y lo del volumen en millones de dólares se hace más evidente cuando los precios sufren grandes variaciones o cuando hay importantes splits, en ambos casos si comparo el volumen en diferentes épocas en nominales voy a estar con comparando peras con manzanas, es por ello que se suele normalizar la serie usando el volumen en millones de dólares.

(*) Por si no se entiende, el punto es el siguiente: los precios de cierre ajustados nos traen los ajustes por dividendos por ejemplo, pero si no me ajustan toda la serie OHLC, el precio de apertura de la siguiente rueda luego del pago de un dividendo tendrá un gap que en realidad no es un gap de precio sino el reflejo del pago de un dividendo, por lo tanto estoy alterando la morfología de la vela siguiente a cada dividendo y ni hablar de los splits, es por ello que se debe ajustar por el coeficiente de dividendo o Split cada dato hacia atrás desde el último dato que se cuenta.

La manera de calcularlo es calcular el % de salto del precio ajustado respecto del sin ajustar y por el mismo % ajustar el OHLC, obviamente los días normales donde no haya dividendos ni splits, el factor de ajuste será 1 (ratio) o 0 en caso de tomar un % directo.

Bien, entonces hagamos la función:

def ajustarExcel(ticker, carpeta=''):
    
    if carpeta == '':
        ruta = ticker + '.xlsx'
    else:
        ruta = carpeta + '/' + ticker + '.xlsx'
        
    data = pd.read_excel(ruta)
    data = data.sort_values('timestamp', ascending=True)
    data.set_index('timestamp', inplace=True)
    data['factor'] = data.adjusted_close / data.close
    data['volMlnUSD'] = data.close * data.volume / 1000000
    
    cols = [data.open * data.factor, data.high * data.factor, data.low * data.factor, data.adjusted_close, data.volMlnUSD]
    
    dataAj = pd.concat(cols, axis=1)
    dataAj.columns = ['Open', 'High', 'Low', 'Close', 'volMlnUSD']
    
    return dataAj.round(2)

Como ven la función calcula el factor de ajuste, cuando no pasa nada ese factor es 1, y lo multiplica por todo el OHLC para obtener la serie ajustada.

La probamos:

data = ajustarExcel('YPF', 'ADRs')
data.head()

Recordemos como era la serie original, citada justamente en la página anterior.

BIen, supongamos que un amigo nos pasa un archivo .py (o que la bajamos de un blog de python) con una función supuestamente para graficar un hetmap de correlación, y contiene la siguiente función:

def graficaCorr(dfCorr, title=''):
    # La funcion recibe como argumento un DataFrame de nxn
    # con valores de correlación
    
    # Devuelve un gráfico tipo heatMap de ese DataFrame, bien estilizado
    
    import matplotlib.pyplot as plt
    import numpy as np
    
    fig = plt.figure(figsize=(12, 8))
    plt.matshow(dfCorr, fignum=fig.number, cmap='binary')
    plt.xticks(range(dfCorr.shape[1]), dfCorr.columns, fontsize=12, rotation=90)
    plt.yticks(range(dfCorr.shape[1]), dfCorr.columns, fontsize=12)
    
    cb = plt.colobar(orientation='vertical', label='Factor Correlación "r"')
    cb.ax.tick_params(labelsize=12)
    plt.title(title, fontsize=16, y=1.15)
    
    # gca() captura los objetos para de ejes
    ax = plt.gca()
    
    ax.set_xticks(np.arange(-.5, len(dfCorr), 1), minor=True);
    ax.set_yticks(np.arange(-.5, len(dfCorr), 1), minor=True);
    ax.grid(which='minor', color='w', linestyle='-', linewidth=3)
    
    # recorro la matriz horizontalmente (j) y verticalmente (i)
    
    for i in range(dfCorr.shape[0]):
        for j in range(dfCorr.shape[1]):
            if dfCorr.iloc[i, j] > 0.6:
                color = 'white'
            else:
                color = 'black'
            
            fig.gca().text(i, j, '{:.2f}'.format(dfCorr.iloc[i, j]), ha='center', va='center', c=color, size=14)
            
    return(plt)

Ahora lo que vamos a hacer es probar la función pasándole como argumento una tabla de correlaciones que vamos a generar usando la función nuestra de leer el excel y ajustar el OHLC y el volumen en millones de dólares, y vamos a graficar correlación entre volúmenes.

Si fueron observadores habrán notado que la función venía con un segundo argumento que era el título del gráfico que lo obvié pasar y por ende puso un titulo en blanco.

import pandas as pd

tickers = ['GGAL', 'BMA', 'BBAR', 'PAM', 'TEO', 'CRESY', 'YPF', 'EDN', 'TGS']

tabla = pd.DataFrame()
for ticker in tickers:
    data = ajustarExcel(ticker, carpeta='ADRs')
    tabla = pd.concat([tabla, data['volMlnUSD']], axis=1)
tabla.columns = tickers

plt = graficaCorr(tabla.corr())
plt.show()

￼
¿Se van dando cuenta la importancia de las funciones, no?

Obviamente que no vamos a estar sentados esperando a que un amigo nos pase funciones copadas ni encontrarlas por ahí en un blog de python perdido en la web, cada tanto pasa y genial pero la idea que les quiero ir presentando es que se vayan armando su propio directorio de archivos con funciones, sí, así como digo, "funciones sueltas", ya más adelante vamos a ver que esto de las funciones sueltas así nomás tampoco es lo más copado, sino que se usan "Objetos" pero ya vamos a llegar, por ahora las funciones son una manera bastante piola de empezar a separar y ordenar el código que vayamos generando.

De hecho yo recomiendo de ahora en más que lo saben, empezar a pensar en términos de funciones, de inputs y outputs de cada "sub-proceso", por ejemplo si estamos aprendiendo a generar HeatMaps, cuando generen uno elaborado como el del ejemplo (que de hecho lo saqué del libro anterior) directamente piensen en que reciba un DataFrame, y trabajar con eso, así luego le podemos pasar cualquier otro DataFrame de correlaciones y me lo grafica igual.

SIgamos jugando con estas dos funciones, recordemos que tenemos una función ajustarExcel() que nos lee los datos de nuestro excel, y les ajusta los OHLC y calcula el volumen en USD, y además tenemos otra función que grafica matrices de correlación como HeatMaps.

Bien, usando ambas funciones, ahora grafiquemos la correlación entre las volatilidades de los ADRs.

tickers = ['GGAL', 'BMA', 'BBAR', 'PAM', 'TEO', 'CRESY', 'YPF', 'EDN', 'TGS']

tabla = pd.DataFrame()
for ticker in tickers:
    data = ajustarExcel(ticker, carpeta='ADRs')
    data['variacion'] = data.Close / data.Close.shift(1) - 1
    data['volatilidad'] = data.variacion.rolling(40).std()
    tabla = pd.concat([tabla, data['volatilidad']], axis=1)
tabla.columns = tickers

plt = graficaCorr(tabla.corr(), title='Correlación de Volatilidades')
plt.show()


￼


Pensando funcionalmente

Si repasamos un poco lo que venimos viendo hasta este 4° Libro de  la serie, tenemos que en el primer libro vimos las estructuras lógicas de flujo de un script, en el segundo vimos el trabajo con dataFrames o matrices de datos, y en el tercero vimos gráficas. Bien, con esa simple estructura ya puedo armar un primer proxy de "esquema funcional", vamos a ver un poco que quiero decir.

Supongamos que se me ocurre un determinado análisis de datos, con determinados parámetros y quiero estudiar una determinada métrica. Bien, así dicho parece medio un chino, pero pensemos así, ¿qué necesito?

Conseguir los datos de entrada (input, dataFeed)
Trabajar esos datos matricialmente (DataSet Prepare)
Calcular las métricas (Research)
Reportar los resultados (Report)

Bien, ahora pensemos lo siguiente:

Son los excels, en este bloque vamos a ver cómo conseguir dataFeed de APIs y webs, pero por ahora esta parte viene de los excels que tenga guardados en mi disco rígido, así y todo puedo definir una función que los lea y me devuelva los datos en una matriz.
Esta es la parte del trabajo de matriz, filtro, resampleo, cálculos con rolling, cummulatives, shifts, etc.  Todo esto puede entrar en una función donde haga este amasado de los datos.
Las métricas finales suelen ser cálculos estadísticos, ratios, tablas de resumen, etc que los calculo con las herramientas básicas de python por lo general.
Acá se suele usar una librería gráfica como matplotlib para recortar de manera visual el resultado que genere.

Esta lista no es taxativa ni tiene por que ser así, es un ejemplo de ordenamiento que les sugiero.

Lo bueno ahora que estamos aprendiendo funciones es que vamos a poder ordenar mucho mejor el código que escribimos por ahí, si de entrada ya pensamos en separar las funciones por un criterio de "funcionalidad" valga la redundancia, a la larga se nos va a ir haciendo todo el coding más natural y sencillo.

Mientras que si de entrada ya partimos mal las funciones y dejamos un pedazo de script en una función que no tiene mucho que ver, luego la vamos a sufrir se los aseguro porque me pasó mil veces, por eso ahora si bien parece denso es el momento para empezar a generar buenas prácticas porque a la larga nos van a ayudar un montón.
Aplicaciones Prácticas
Antes de pasar al tema de las APIs y los dataFeed, vamos a meter alguna cosita más de estudi quant, si bien bastante básica, con los elemenos que vimos hasta ahora es algo interesante que se puede ir haciendo para que esto no se torne tan teórico.

Bien, vamos a leer los datos de los ADRs bancarios, a los que llamaremos "ADRs" en una lista. Luego vamos  a hacer los cálculos necesarios para poder decidir cual de los dos tuvo mejor rendimiento en un supuesto método de trading que consista en estar comprado cuando el activo, supera a una media móvil exponencial en "n" ruedas (siendo "n" un número entre fromN y toN, obviamente dos variables a parametrizar en los inputs del script) y estar vendido cuando pasa lo contrario (para ello tomaremos el rendimiento diario del día siguiente al día en que se cumple la condición de comprado o vendido al cierre).

Ahora bien, a fin de comparar los rendimientos para cada ADR por separado, vamos a calcular el rendimiento medio por rueda, de los métodos de comprado y vendido (en la posición vendida vamos a sumar el rendimiento de los cierres ajustados cambiado de signo obviamente, y sin considerar tasa para simplificar).

Por último necesitaremos para visualizar no sólo el número medio del ADR para todos sus "n" sino toda la performance para los distintos "n" un gráfico de líneas que superponga el rendimiento de ambos activos para cada "n".

# FUNCION 1 - dataFeed y preparación
# Esta función solo abre el archivo y le calcula unas columnas

import matplotlib.pyplot as plt, pandas as pd

def abrirExcel(ticker, carpeta=''):
    
    if carpeta == '':
        ruta = ticker + '.xlsx'
    else:
        ruta = carpeta + '/' + ticker + '.xlsx'
        data = pd.read_excel(ruta)
    data = data.sort_values('timestamp', ascending=True)
    data.set_index('timestamp', inplace=True)
    ret = data.adjusted_close.to_frame()
    ret.columns = ['Close']
    ret['Yield'] = (ret['Close'] / ret['Close'].shift() -1)*100
    
    return ret.round(2).dropna()

A continuación les muestro las otras dos funciones...

# FUNCIÓN 2 - Calcula rendimientos de estrategias

def getYields(data, fromEMA, toEMA):
    yields = []
    timeIn = []
    for i in range(fromEMA, toEMA+1):
        key = 'EMA_' + str(i)
        data[key] = data.Close.ewm(span=i).mean()
        data['comprado'] = data.Close.shift() > data[key].shift()
        data['vendido'] = data.Close.shift() < data[key].shift()
        allIn = data.loc[data.comprado == True]['Yield']
        allOut = data.loc[data.vendido == True]['Yield']
        qIn = allIn.count()
        qOut = allOut.count()
        qTot = allIn.count() + allOut.count()
        yields.append((allIn.mean()*qIn-allOut.mean()*qOut)/qTot)
        timeIn.append(100*qIn/qTot)
    
    return yields, timeIn

# FUNCIÓN 3 - Grafico final

def Graficar(ADRs, fromEMA, toEMA):
    ejeX = [i for i in range(fromEMA, toEMA+1)]
    fig, (ax1, ax2) = plt.subplots(figsize=(10,10), nrows=2)
    r , yieldsMedios = [], []
    
    for ADR in ADRs:
        # Llamo a la FUNCION 1
        data = abrirExcel(ADR, 'ADRs')
        
        # Llamo a la FUNCION 2
        yields, timeIn = getYields(data, fromEMA, toEMA)
        
        # Grafico serie de rendimientos y tiempos adentro
        ax1.plot(ejeX, yields, ls='--', lw=1, label=ADR)
        ax2.plot(ejeX, timeIn, ls='--', lw=1, label=ADR)
        
        # Acumulo series para rendimiento promedio total al final
        r.append(yields)
        yieldsMedios.append(sum(yields)/len(yields))
        
    # Grafico Rendimiento Medio
    yieldsTotal = [(x+y+z)/3 for x, y, z in zip(r[0], r[1], r[2])]
    ax1.plot(ejeX, yieldsTotal, color='k', lw=1, label='Total')
    
    # Configuro Ejes
    ax1.legend()
    ax1.set_ylabel('rendimiento % medio diario por rueda')
    ax1.grid(which='major', axis='both', color='black', alpha=0.15)
    ax2.legend()
    ax2.set_xlabel('Ruedas de la Media Movil')
    ax2.set_ylabel('% de tiempo comprado')
    ax2.grid(which='major', axis='both', color='black', alpha=0.15)
    fig.subplots_adjust(hspace=0)
    
    return plt, yieldsMedios

Bueno, probemos esto:

# Defino Tickers
ADRs = ['GGAL', 'BBAR', 'BMA']

# Llamo a la FUNCION 3
plt, yields = Graficar(ADRs, 5, 400)

# Muestro resultados
plt.show()
for i in range(3):
    print(ADRs[i], round(yields[i], 4), end='')


￼

GGAL 0.0893BBAR 0.0576BMA 0.0907

Lo interesante de tener el código ordenado así es que:
Puedo agarrar cualquier función suelta y reutilizarla en otra strategia.
Puedo generar funciones similares con pequeños cambios y tener mi biblioteca de funciones.
Puedo usar todas las funciones ya escritas con solo llamarlas.
El código se hace más facil de entender y más mantenible.

Por ejemplo, probemos el mismo código para otros ADRs por ejemplo TEO, PAM e YPF, y para el rango 50 a 250 ruedas.

# Defino Tickers
ADRs = ['TEO', 'PAM', 'YPF']

# Llamo a la FUNCION 3
plt, yields = Graficar(ADRs, 50, 200)

# Muestro Resultados
plt.show()
for i in range(3):
    print(ADRs[i], round(yields[i], 4), end='')


￼

TEO 0.1064PAM 0.0904YPF 0.0566
Ejercicios

 Definir 4 funciones que se encarguen respectivamente de:
Leer el archivo Excel con los datos históricos para una acción, por ejemplo AAPL 
Calcular el indicador de análisis técnico RSI (en función de la cantidad de ruedas hacia atrás tomando como default 14)
Calcular la recta de regresión entre RSI/RendimientoDiario en la siguiente rueda y su coeficiente de correlación y R^2 
Graficar la correlación RSI/Rendimiento diario y su recta de regresión lineal

import matplotlib.pyplot as plt, pandas as pd, numpy as np

def abrirExcel(ticker, carpeta=''):
    if carpeta == '':
        ruta = ticker + '.xlsx'
    else:
        ruta = carpeta + '/' + ticker + '.xlsx'
    
    data = pd.read_excel(ruta)
    data = data.sort_values('timestamp', ascending=True)
    data.set_index('timestamp', inplace=True)
    ret = data.adjusted_close.to_frame()
    ret.columns = ['Close']
    ret['Yield'] = (ret['Close']/ret['Close'].shift() -1)*100
    
    return ret.round(2).dropna()

def rsi(data, ruedas=14):
    rsi = pd.DataFrame(data)
    rsi['dif'] = rsi['Close'] - rsi['Close'].shift(1)
    rsi['win'] = np.where(rsi['dif'] >= 0, rsi['dif'], 0)
    rsi['loss'] = np.where(rsi['dif'] <= 0, abs(rsi['dif']), 0)
    rsi['ema_win'] = rsi.win.ewm(alpha=1/ruedas).mean()
    rsi['ema_loss'] = rsi.loss.ewm(alpha=1/ruedas).mean()
    rsi['rs'] = rsi.ema_win / rsi.ema_loss
    rsi['rsi'] = 100 - (100 / (1+rsi.rs))
    rsi['nextYield'] = rsi.Yield.shift(-1)
    
    return rsi.reset_index().dropna()

def calcReg(serie1, serie2):
    regresion = {}
    b_1 = serie1.cov(serie2) / serie1.var()
    b_0 = serie2.mean() - b_1 * serie1.mean()
    regresion['recta'] = b_0 + b_1*serie1
    regresion['r'] = round(serie1.corr(serie2), 4)
    regresion['r2'] = round(serie1.corr(serie2)**2, 4)
    
    return regresion

def grafCorr(serie1, serie2, regresion):
    fig, ax = plt.subplots(figsize=(8, 8))
    lab = 'Coef r: ' + str(regresion['r'])
    lab += '\nR^2: ' + str(regresion['r2'])
    ax.plot(serie1, regresion['recta'], color='k', label=lab)
    ax.scatter(serie1, serie2, s=1)
    xmin = serie1.quantile(0.005)
    xmax = serie1.quantile(0.995)
    ymin = serie2.quantile(0.005)
    ymax = serie2.quantile(0.995)
    ax.set_xlim(xmin, xmax)
    ax.set_ylim(ymin, ymax)
    plt.legend(loc='upper right')
    
    return plt
    
# esto es cuando te manda por salida o consola
# Advertencias que no queremos que nos siga mostrando

import warnings
warnings.filterwarnings('ignore')

# Ahora uso las funciones previamente armadas:

data = abrirExcel('YPF', 'ADRs')
rsiDF = rsi(data, 14)
regresion = calcReg(rsiDF.rsi, rsiDF.nextYield)
plt = grafCorr(rsiDF.rsi, rsiDF.nextYield, regresion)
plt.show()

￼
2- Empecemos a trabajar el concepto de la sobreventa y sobrecompra, hacer una función (reutilizando obviamente en las funciones del ejercicio anterior que nos sirvan) para medir el factor de correlación "r"  del RSI contra el rendimiento de la rueda posterior pero filtrando hasta un valor tope del RSI (como por ejemplo <30 que indicarían en principio de sobreventa).

La función debe tomar el factor de correlación para todos los "n" (número de ruedas de la configuración del cálculo del RSI) de 2 hasta "maxRuedas" que es una variable o argumento de la función.
La tope "maxRSI" que indicaría sobreventa, también debe ser un argumento de la función.
El nombre del ticker y su carpeta también deben ser un argumento de la función.
Si la cantidad de puntos filtrados es menor a 20, la función debe devolver "None" en lugar de calcular el factor de correlación
Hacer una función similar pero con topes mínimos para el RSI (SobreCompra)
Hacer una función similar con ambos topes (Zona Media)
Aplicarle las tres funciones a GGAL y graficar las 3 series
Extraer conclusiones

def corrRSIsobreventa(ticker, carpeta='', maxRuedas=60, maxRSI=30):
    ticker = ticker
    data = abrirExcel(ticker, carpeta=carpeta)
    rs = []
    rsEjeX = [i for i in range(2, maxRuedas)]
    
    for ruedas in range(2, maxRuedas):
        
        # Usamos la función "RSI" del ejercicio anterior
        rsiDF = rsi(data, ruedas=ruedas)
        rsiDF = rsiDF.loc[rsiDF.rsi < maxRSI]
        
        # Verificamos que el filtro tengo al menos 20 datos
        if rsiDF.nextYield.count() > 20:
            # Usamos la función "calcReg" del ejercicio anterior
            r = calcReg(rsiDF.rsi, rsiDF.nextYield)['r']
        else:
            r = None
            
        rs.append(r)
        
    return(rsEjeX, rs)

# Lo mismo para sobreCompra cambiando el filtro
def corrRSIsobrecompra(ticker, carpeta='', maxRuedas=60, minRSI=70):
    
    ticker = ticker
    data = abrirExcel(ticker, carpeta=carpeta)
    rs = []
    rsEjeX = [i for i in range(2, maxRuedas)]
    
    for ruedas in range(2, maxRuedas):
        rsiDF = rsi(data, ruedas=ruedas)
        rsiDF = rsiDF.loc[rsiDF.rsi > minRSI]
        
        if rsiDF.nextYield.count() > 20:
            r = calcReg(rsiDF.rsi, rsiDF.nextYield)['r']
        else:
            r = None
        rs.append(r)
    return(rsEjeX, rs)

def corrRSIzonamedia(ticker, carpeta='', maxRuedas=60, minRSI=30, maxRSI=70):
    ticker = ticker
    data = abrirExcel(ticker, carpeta=carpeta)
    rs = []
    rsEjeX = [i for i in range(2, maxRuedas)]
    
    for ruedas in range(2, maxRuedas):
        
        # Usamos la función "RSI" del ejercicio anterior
        rsiDF = rsi(data, ruedas=ruedas)
        rsiDF = rsiDF.loc[rsiDF.rsi > minRSI]
        rsiDF = rsiDF.loc[rsiDF.rsi < maxRSI]
        
        # Verificamos que el filtro tengo al menos 20 datos
        if rsiDF.nextYield.count() > 20:
            # Usamos la función "calcReg" del ejercicio anterior
            r = calcReg(rsiDF.rsi, rsiDF.nextYield)['r']
        else:
            r = None
            
        rs.append(r)
        
    return(rsEjeX, rs)

ticker = 'GGAL'
fig, ax = plt.subplots(figsize=(10, 5))

rsEjeX, rs = corrRSIsobreventa(ticker, carpeta='ADRs', maxRSI = 30)
ax.plot(rsEjeX, rs, 'r--', label='Sobre Venta')

rsEjeX, rs = corrRSIsobrecompra(ticker, carpeta='ADRs', minRSI = 70)
ax.plot(rsEjeX, rs, 'k-', label='Sobre Compra')

rsEjeX, rs = corrRSIzonamedia(ticker, carpeta='ADRs', minRSI = 30, maxRSI= 70)
ax.plot(rsEjeX, rs, 'lightgray', lw=2, label='Zona Media')

ax.set_ylabel('Coeficiente de Correlación ...', color='gray')
ax.set_xlabel('Cantidad de ruedas configuración RSI', color='gray')
plt.legend(loc='upper right')
plt.suptitle('Factores de Correlación RSI/YieldFw(1) según zona ...', y=0.95)
plt.show()


￼
Conclusión: Obviamente no hace falta mucha observación para darse cuenta que claramente hay una gran diferencia entre la correlación que tiene el indicador RSI en las zonas de sobrecompra o sobreventa con la que tienen las zonas medias, claramente hay una correlación mucho más fuerte en las zonas extremas, al parecer en GGAL negativas (a menor RSI comprar en sobreventa y mayor RSI vender en sobrecompra).

Obviamente, los invito a ponerse a jugar con él código en diferentes acciones para ver qué observan en cada una, si bien este es un ejemplo demasiado básico, es un buen proxy al tipo de análisis que se hace al evaluar que métricas van a tomar, en qué rangos y con que logica, para una estrategia de trading del tipo cuantitativa.

Como verán el código se va poniendo "denso" o largo digamos, con lo cual si no empezara a usar funciones que se encarguen cada una de algo concreto, se empieza a descontrolar la cantidad de líneas de código y se hace muy poco entendible y "mantenible".

Una buena métrica a tener en cuenta, en Python, sería que para un script de análisis de datos que haga una sola persona (no hablo de equipos de trabajo), no deberíamos superar  las 100 líneas, si pasa eso es porque mendamos mucho código innecesario que podemos meter dentro de ciclos, o bien porque no estamos usando funciones donde deberíamos usarlas, después, desde ya, podemos importar funciones pero el código en sí sin contar las funciones que usemos, no debería superar las 100 líneas.

3- Reutilizando las funciones que sean útiles de los ejercicios anteriores, hacer un gráfico de líneas que muestran cómo varia el coeficiente de correlación entre el valor del RSI y el rendimiento al día siguiente, en función de la cantidad de ruedas hacia atrás que se use en calcular el RSI.

Hacer este ejercicio ya pensando que conviene meter lo que pueda ser reutilizable en otra función (esto va a servir para el ejercicio siguiente, pero la idea es que no lo lean aún, y solo craneen como pueden separar una parte del código de este ejercicio en una función cosa que les pueda servir para otros análisis.

def rsiSensibilidad(ticker, carpeta='', nMax=30):
    data = abrirExcel(ticker, carpeta)
    rs = []
    for i in range(2, nMax+1):
        rsiDF = rsi(data, i)
        regresion = calcReg(rsiDF.rsi, rsiDF.nextYield)
        rs.append(abs(regresion['r']))
        
    x = [i for i in range(2, nMax+1)]
    
    return ticker, x, rs

ticker, x, rs = rsiSensibilidad(ticker='GGAL', carpeta='ADRs', nMax=100)
fig, ax = plt.subplots(figsize=(4, 3))
ax.plot(x, rs, label=ticker)
plt.legend(loc='upper right')
plt.show()

￼

4- Usando las funciones de los ejercicios anteriores armar un script que recorra 12 ADRs de la carpeta ADRs y que grafique la sensibilidad del coeficiente de correlación RSI/RendimientoSiguiente Rueda, en función de la cantidad de ruedas hacia atrás que se toman para cálculo del RSI.

import math
tickers = ['BBAR', 'BMA', 'CEPU', 'CRESY', 'EDN', 'GGAL', 'LOMA', 'PAM', 'SUPV', 'TEO', 'TGS', 'YPF']

fig, ax = plt.subplots(figsize=(15, 12), nrows=3, ncols=4)

nMax = 60

for i in range(len(tickers)):
    fila = math.floor(i/4)
    col = i%4
    ticker, x, rs = rsiSensibilidad(tickers[i], carpeta='ADRs', nMax=nMax)
    ax[fila][col].plot(x, rs, label=tickers[i])
    ax[fila][col].set_ylim(0, 0.08)
    ax[fila][col].legend(loc='upper righr')
    ax[fila][col].grid(which='major')
    ax[fila][col].set_xlabel('N° ruedas del RSI', color='gray')
    ax[fila][col].set_ylabel('Valor absoluto Factor Corr r', color='gray')
    
plt.suptitle('ABS(r), de Corr entre RSI y el rendimiento de la rueda siguiente, en función del "n" del RSI', y=0.93)

plt.subplots_adjust(wspace=0.45, hspace=0.35)
plt.show()

￼

5- Usando las funcioens de los ejercicios anteriores, armar un script que recorra los 12 ADRs y grafique la sensibilidad del coeficiente de correlación RSI/Rendimiento Siguiente Ruedas, pero segmentado según se encuentre en zona de sobreVenta, sobreCompra o neutral,  y hacer cada gráfico en un recorrido de 2 a 40 ruedas de la configuración del RSI.

import math
tickers = ['BBAR', 'BMA', 'CEPU', 'CRESY', 'EDN', 'GGAL', 'LOMA', 'PAM', 'SUPV', 'TEO', 'TGS', 'YPF']

fig, ax = plt.subplots(figsize=(15, 12), nrows=3, ncols=4)

n = 40

for i in range(len(tickers)):
    fila = math.floor(i/4)
    col = i%4
    ticker = tickers[i]
    
    x, rs = corrRSIsobreventa(ticker, maxRuedas=n, carpeta='ADRs', maxRSI=30)
    ax[fila][col].plot(x, rs, 'r--', label='SobreVenta')
    
    x, rs = corrRSIsobrecompra(ticker, maxRuedas=n, carpeta='ADRs', minRSI=70)
    ax[fila][col].plot(x, rs, 'k-', label='SobreCompra')
    
    x, rs = corrRSIzonamedia(ticker, maxRuedas=n, carpeta='ADRs', minRSI=30, maxRSI=70)
    ax[fila][col].plot(x, rs, 'k-.', alpha=0.3, label='Neutral')

    ax[fila][col].set_ylim(-0.3, 0.3)
    ax[fila][col].legend(loc='upper righr', fontsize=10)
    ax[fila][col].grid(which='major')
    ax[fila][col].set_xlabel('N° ruedas del RSI', color='gray')
    ax[fila][col].set_ylabel('Valor absoluto Factor Corr r', color='gray')
    ax[fila][col].set_title(tickers[i], y=0.03, fontweight='bold', alpha=0.3, fontsize=25, c='gray')

plt.suptitle('Coeficiente de Correlacion entre RSI y el rendimiento de la rueda siguiente\nEn función del "n" del RSI, para estados de SobreCompra y SobreVenta', y=0.96)

plt.subplots_adjust(wspace=0.45, hspace=0.35)
plt.show()

A grandes rasgos se ve claramente que en las zonas de sobrecompra y sobreventa las correlaciones son muchos mayores, incluso se nota que tienen que ser negativas (corroborando la hipótesis de manual que en la zona de sobrecompra hay mayor probabilidad de baja inminente y en la zona de sobreventa mayor probabilidad de rebote inminente).

Bueno, como pueden ver, esto de tener funciones nos va permitiendo escalar la complejidad de los análisis sin necesidad de que el código final sea un loquero inentendible.

Usamos 3 funciones (una para cada una de las zpnas del RSI)
Pero a su vez cada una de las 3 funciones usa otras 3, la del RSI y la de correlaciones y la de abrir el Excel con los datos.

Imaginen si no hubiéramos usado funciones lo que sería el código entero de esas 30 líneas.

6- Definir dos funciones para graficar luego haciendo uso previo de las mismas, la correlación entre un cruce de medias (mediaRapida/mediaLenta) y el rendimiento futuro de la acción para los siguientes parámetros:
Parámetro Input: Media móvil rápida, cantidad de ruedas.
Parámetro Input: Media móvil lenta, cantidad de ruedas.
La cantidad de ruedas fw futuras a observar debe variar entre 2 y 250.
Parámetro Input: Ticker y Carpeta (de donde lee el excel).

Para optimizar el script se pide recorrer de 2 a 250 ruedas forward pero de a 3.

# NO FUNCIONO

import pandas as pd
import matplotlib.pyplot as plt

carpeta = 'ADRs'

def cruce(data, fast=50, slow=200, fw=20):
    cruce = pd.DataFrame(data['Close'])
    cruce['fwYield'] = (cruce['Close'].shift(-fw)/cruce['Close']-1)*100
    cruce['smaFAST'] = cruce['Close'].rolling(fast).mean()
    cruce['smaSLOW'] = cruce['Close'].rolling(slow).mean()
    cruce['cruce'] = (cruce['smaFAST']/cruce['smaSLOW']-1)*100
    
    return cruce.dropna().round(2)

def corrCruce(data, fast, slow):
    lista = []
    for i in range(1, 250, 3):
        c = cruce(data, fast=fast, slow=slow, fw=i)
        regresion = calcReg(c.cruce, c.fwYield)
        lista.append(regresion['r'])
       
    return lista

data = abrirExcel(ticker, carpeta)
lista = corrCruce(data, fast=20, slow=60)

fig, ax = plt.subplots(figsize=(10, 5))
ax.plot([i for i in range(1, 250, 3)], lista)
ax.set_xlabel('Cantidad de ruedas Fw para rendimiento a correlacionar...')
ax.set_ylabel('Coeficiente de correlación R')
ax.grid()

plt.suptitle('Correlación (Eje Y) entre cruce de medias ('+str(fast)+'-'+str(slow)+') y fwField (x ruedas, eje X)', y=0.93)
plt.show()



7- Armar un script bien sencillo que arme pares de cruces de medias con las siguientes restricciones:
Definir de antemano la media más baja de todos los cruces y la más alta.
Que la primera media de cada par sea menor que la segunda.
Que la segunda media sea un %50 más alta que la primera como mínimo.
Que la diferencia sea al menos de 10.
Ej para 5(min), 17(max) debe devolver:
[[5, 15], [5, 16], [5,17], [6, 16], [6, 17], [7, 17]]
Ej para 40(min), 63(max) debe devolver:
[[40, 61], [40, 62], [40, 63], [41, 62], [41, 63]]

def crearCruces(mmMin, mmMax):
    cruces = [[i, j] for i in range (mmMin, mmMax+1)
             for j in range(mmMin+1, mmMax+1)
if (j <= mmMax) & (j > i*1.5) & (j >= i+10)]
    return cruces

crearCruces(40, 63)


    
    [[40, 61], [40, 62], [40, 63], [41, 62], [41, 63]]

8- Siguiendo con la idea del cruce de medias, un script de 3 funciones para encontrar la mejor correlación entre cruces de medias y rendimiento futuro, cuyas 3 funciones sean:

DataFeed: por ahora sería la función que lee el excel sabiendo el ticker y la carpeta donde se ubica.
Generador de cruces: la función del ejercicio anterior que pasándole un mínimo y un máximo, nos devuelve los pares de cantidad de velas para la media rápida lenta del cruce. 
Buscador de mejor cruce (esta función acepta como input, la dataFeed de la función 1, los cruces de la función 2,  y la cantidad de ruedas a futuro con la que quiero correlacionar al cruce.
Me debe devolver cuál de los cruces es el óptimo en factor de correlación y el coeficiente de correlación propiamente dicho.

def mejorCruce(data, cruces, fw):
    maximo = 0
    for mm in cruces:
        data['fwYield'] = (data['Close'].shift(-fw)/data['Close']-1*100)
        data['mmFast'] = data.Close.rolling(mm[0]).mean()
        data['mmSlow'] = data.Close.rolling(mm[1]).mean()
        data['cruce'] = (data['mmFast'] / data['mmSlow'] -1)*100
        r = round(data['cruce'].corr(data['fwYield']), 4)
        
        if r > maximo:
            mejor = [mm[0], mm[1], r]
            maximo = r
    
    return mejor

data = abrirExcel('TEO', 'ADRs')
cruces = crearCruces(5, 50)
mejor = mejorCruce(data, cruces, 100)
mejor



    [5, 50, 0.2212]

APIs
El significado de la sigla es Application Programming Interfaces, o sea son como "puentes" interfaces para unir programas, así como los puentes unen ciudades las APIs unen aplicaciones, por ejemplo la aplicación de un broker manda las órdenes de sus clientes al mercado, para ello el broker se genera su propia aplicación para interactuar con el mercado, ahora bien, si nosotros programamos un bot y queremos que nuestro broker mande al mercado las órdenes que decide nuestro bot, hay que unir ambos programas (aplicaciones), es decir necesitamos algo que "una" a nuestro programa (nuestro bot) con el programa del broker (su plataforma de acceso al mercado), de eso por ejemplo se encargan las APIs.

Lo distintivo de las APIs es que son agnósticas a la tecnología de "los programas que unen" es decir, mi broker puede tener su acceso al mercado escrito en C++ (un lenguaje de programación) y yo puedo tener mi bot en Python, nada que ver, y sin embargo ambos entendemos el estándar de la API, y por lo tanto mi programa escrito en un lenguaje se puede comunicar perfectamente y recibir inputs y generarle outputs al programa del broker.

Obviamente hay APIs de todo tipo, hay APIs que me permiten manejar una cuenta en una red social y hay APIs que me permiten comprar por un marketplace, como Mercadolibre, en nuestro caso nos vamos a ocupar en este capítulo de las APIs que facilitan acceso a market data y las APIs que facilitan la conexión a mercados.

Voy a dejarle una lista de mis APIs favoritas que cumplen esta función específica, solo nombrando aquellas que ofrecen una cuenta gratuita (al menos hasta cierta cantidad de uso, ya veremos este tema en detalle).

Como les digo, por lo general un modelo de negocio en este mundo muy utilizado es el freemium, es decir que ofrecen una versión FREE sin cargo con alguna limitación que para uso profesional te termina conviniendo pagar algo.

De todos modos también hay un mundo en tema precios, yo acá les pongo solo APIs gratuitas y que si tienen alguna funcionalidad premium, esta no supera los 10 dólares a 50 dólares por mes.

En ningún caso hace falta la versión paga, los servicios de conexión son excelentes y los datos de precios y muy precisos, algunas APIs tienen inconsistencias en algunos datos de ratios de fundamentals y ese tipo de datos más difíciles de homogenizar pero por lo general son todas excelentes, por eso no pongo APIs de menor calidad como "yahoo finance" por ejemplo.

Desde ya no pongo acá las APIs caras (de miles de dólares mensuales) ya que para tratar seriamente ese tema requería un libro entero y en este capítulo simplemente quiero hacer un repaso de las mejores APIs para uso personal o institucional de presupuesto agotado.

Sin más presentación les dejo estos listados, me llevó un tiempo prepararlos pero creo que valió la pena, porque hay muchas APIs y cómo vamos a ver las funcionalidades que nos ofrece cada una son muy variadas.
￼

Como verán no hay ninguna que las tenga todas, lo más recomendable es aprender a usar todas, o tener una carpetita con archivo de APIs donde se vayan armando sus funciones y demás y recomiendo ampliamente que este listado que puse estén TODAS, si todas, siempre es bueno tener más buena que brinde la misma data, para chequear cuando vemos resultados raros, o por si se cae una etc.

Es un listado de lo que considero las mejores en su rubro o fuerte digamos, y obviamente hay miles de APIs más, y recomiendo siempre no dormirse y estar atento, esto es muy dinámico, hace tres años, del listado hay varias que no existían aún o eran demasiado más básicas de lo que son hoy, así que atentis siempre a la evolución de esto.

Mi consejo es que para fines de práctica y didácticos estas APIs están más que bien, y no necesitan nada de de pago:
AlphaVantage: Es ideal para indicadores técnicos, buenas series históricas de USA.
FinnHub: Mucha cantidad de mercados en tiempo real, muy completa data de indicadores económicos.
FMP: Es la más fuerte en Análisis Fundamental.
IEX: Es la más profesional, la más trabajada y completa, pero la versión gratuita es bastante limitada y la de 9 USD tampoco permite un uso fuerte, asi que es ideal para un trabajo en una empresa o inversores profesionales.
Alpaca: Es sencilla y práctica y permite realizar trading algorítmico simulado sin plata real pero con precios y entornos reales.
InvertirOnline: Es privada de un bróker pero es lo mejorcito que hay en Argentina para trading algorítmico.
Rofex: Tiene datos de futuros en tiempo real, en pleno desarrollo a mayo 2020 está bastante aceptable para trading de futuros en Argentina.
¿Conviene usar una librería o paquete o usar las APIs con funciones propias?
Prometo que esto es lo último teórico que comento antes de meternos de lleno en cada una, perdón, pero quiero ir aclarando algunas cuestiones, porque sé que en algún momento van a pasar, por esta duda o planteo.

Conviene siempre saber usar las APIs a "modo manual" es decir armando ustedes mismos las funciones y les pongo algunos por que:

Porque a veces actualizan las APIs y no los paquetes, con lo cual nos perdemos "potencia" de API por usarla del paquete. 
Porque muchas veces necesitamos personalizar alguna función con algún parámetro extra que el paquete no lo brinda y nos terminamos limitando o haciendo un código menos eficiente.
Porque si bien uno se puede fijar las "estrellas" que tiene el propósito del paquete que baja, muchas veces tienen bugs que no se notan o no tiran error y puede ser un problema.

Tipos de API y OAuth
Bien, como verán hat varios tipos de APIs que vamos a trabajar, en el caso de las primeras 7 que no son exclusivas del mundo crypto, veremos que hay APIs de solo marketData (las primeras 4) y APIs de trading (las otras 3), obviamente las APIs de trading van a tener que ligarse de algún modo a una cuenta de trading.

Por otro lado verán que las que puse en la lista de cryos, solo una es de marketData y las otras son de exchanges que ofrecen cuentas de trading con conexión via API.

El tema de autenticación es poco intuitivo, ustedes dirán (con bastante lógica) "Ah seguro que requieren autenticación las de trading y las de marketData no".

La respuesta es NO. jaja, es así, las de equity y mercados financieros (sacando crypto) son difíciles de armar porque recolectar y homogenizar la data disponible no es tarea sencilla, por lo cual esas APIs (la primera lista) suelen tener un nivel gratuito y a partir de cierto uso un nivel pago, igual nunca superan los 50 USD para uso incluso de bots.

Les decía en la primera lista al tener un modelo que empezas gratis y si requerís mucho uso te empiezan a cobrar, tiene que si o si medir el uso desde un principio, por lo cual son con autenticación.

Ahora en el caso de las APIs de crypto, la info de criptos es gratuita en todos lados, y acá no hay regulaciones densas, cosas diferentes que homogenizar ni balances, ni filings de la SEC, con lo cual abunda la info gratuita y de excelente calidad.

Con lo cual por más que trate de una API con data en tiempo real y demás para lo que es market data no piden autenticación es decir podes pedir datos a la API sin identificarte, pero para trading tienen una auténticación.

Y acá viene lo importante, el mundo crypto es un mundo hermoso pero en el que hay que tener mucho cuidado del tema seguridad de claves y esas cosas, si yo dejo ahí una clave de acceso a mi broker de acciones, no pasa nada, porque si alguien me quiere robar los fondos de mi comitente en algún momento para extraer los fondos va a tener que salir por una cuenta bancaria y en casi todos los países del mundo tiene que ser de la misma titularidad del de la comitente, así que de la seguridad de nuestros fondos en la comitente se encarga el broker.

Pero en el mundo crypto es diferente, si alguien accede a mi cuenta de un exchange crypto y no tomo las medidas de seguridad necesarias, se transfiere los fondos y a cantarle a gardel.

Entonces como van a ver la autenticación en las APIs de exchanges cryptos es muchisimo más engorrosa y vueltera, incluso exigen si o si el estándar de 2FA, es decir que van a tener que andar siempre con el celu a mano para loguearse cada vez que quieran cambiar algo de esa API, no cada vez que la API se loguee que es otra cosa.

Entonces, resumiendo, se podría decir que:

APIs de acciones, futuros y bonos, son casi siempre con logueo light que es un simple token.
Ojo que hay intermedios que tienen tokens que se refrescan cada tanto tiempo que son un poco más seguras que un token único, pero siguen siendo vulnerables para la seguridad que requiere el trading crypto.
APIs de exchanges cryptos tienen una parte de marketData sin logueo, y una parte de trading con logueo Hard.
Generalidades de una API
Lo primero siempre que vamos a hacer al analizar la conveniencia de usar o no una API, después de ver el precio obvio, es ir a la documentación y chusmear por arriba que onda la API, acá solo a modo de generalidades vamos a ver las cosas que tienen en común.

Al entrar a la documentación de una API vamos a tener por lo general siempre la misma estructura de organización de la información, esta estandarizado (salvando los casos de APIs argentinas que quedaron en el tiempo jaja).

Les pego una a modo de ejemplo (la de IEX).

￼

Como les decía, la estructura general es más o menos siempre la misma:

Barra de navegación a la izquierda con una especie de "índice" de contenidos o funcionalidades de la API, generalmente está organizada por secciones y dentro de cada sección está cada función.
Los primeros contenidos son por lo general informativos (versión, créditos, roadmap, etc).
Las primeras funciones suelen ser un "Getting Started" o primeros pasos, por lo general acá tenemos el tema de la autenticación y a veces una función de ejemplo.
Luego vienen los RateLimits, que son la cantidad de llamadas máximas que se puede hacer a la API por segundo, por minuto, por ahora, por día, mes, etc. Cada API lo maneja a su manera, pero siempre hay un límite a la cantidad de veces que puedo mandarle un request a la API.
Luego arranca la lista de funciones del índice digamos.
Últimamente se está estilando una segunda barra lateral a la derecha con ejemplos prácticos, pero no son muchas las APIs que tienen ese formato de documentación aún.

Esto es lo más usual, pero siguen habiendo APIs con documentación vieja, por ejemplo la de ROFEX en Argentina que es la única API con datos oficiales de futuros, tiene su documentación en un word o pdf.
Explicación básica del funcionamiento de una comunicación con una API
Voy a explicar muy groso como funciona una API REST.

Lo que tienen las APIs es una base de datos que se actualiza a veces en tiempo real a veces una vez por día, etc, y lo que hacen estos servicios de APIs es proveernos de una serie de "funciones" o "endpoints" a los cuales podemos hacer "llamadas" o "requests".

Acostúmbrense a esas palabritas porque las vamos a nombrar un montón a partir de ahora.

Endpoint: es una URL (dirección web) a la que vamos a apuntar para que me devuelva un dato.
Llamados/Requests: es la acción de entrar a esa URL.
Response: es la respuesta de la API a ese llamado.
StatusCode: es un código estandarizado que nos devuelve junto con el Response (abajo lo explico mejor).
Servidor: es la compu de la API.
Cliente: es la compu nuestra o nuestro programa que hace el request.
Credenciales: son las claves o tokens de autenticación.
Body: es el cuerpo de los mensajes entre cliente y servidor (A veces aquí viajan las credenciales).
Headers: son encabezados de los mensajes entre cliente y servidor (A veces aquí viajan las credenciales).
Parametros: son justamente los parámetros (variables) que necesita el endpoint que le mandemos en el mensaje, hay opcionales y obligatorios.
SandBox: son ambientes de prueba, para testear las funciones sin efecto real (como un simulador).
POST, GET: son los principales métodos de comunicación cliente/servidor, a grandes rasgos POST es más seguro y se usa para autenticación o para envío de info sensible ya que cuando viaja por GET podría ser interceptada más fácilmente.

O sea que en definitiva una API rest es como entrar a una web y ver un dato que el servidor de la web lo tiene guardado en una base de datos, pero como se hace todo automatizado el servidor tiene que definir digamos el fino del "cómo" (parámetros) navegar cada endpoint (subpagina) de la web, y para saber quien es quien para evitar abusos y medir el uso, generalmente nos pide una autenticación en los headers del request o en el body.
Rate Limits
Bien, como ya dije, toda API tiene una rate limit, y esto es así porque imagínense que al ser todo automatizado, si me descontrolo y le mando bots a una API que se queden eternamente haciendo requests a lo loco, les saturo el servicio, con lo cual el servidor nos va a bloquear al pasar determinado rateLimit, ya sea por segundo, minuto, hora, día, etc...

Este parámetro es de lo primero que debemos fijarnos, por lo general alcanza y sobra con los que nos deja, es muy común que sean al menos de 1 request por segundo, y alguna cantidad fija por mes también en los modelos freemium, así que siempre chequeen este dato a la hora de evaluar la conveniencia del uso de una API.

Weightening: Hay muchas APIs que miden el "peso" de las respuestas, y para las respuestas largas o pesadas (por ejemplo una serie de precios histórica muy larga) les pone un peso mayor, otras APIs no le den bola a eso y tienen un costo de peso flat digamos para medir la cantidad de requests.
Status Code
Si bien ya vamos a ver más adelante que pasa cuando falla una conexión y esas cosas, voy dejando por acá que es la parte de generalidades una tabla de códigos de respuesta HTTP, que nos devuelven todas las APIs a cada llamada salga bien o mal justamente diciendo eso (si salió bien o mal) y un mínimo estándar que nos orienta de donde está la falla en caso de haberla.

1xx: Mensaje informativo.
2xx: Exito
200 Ok
201 Created
202 Accepted
204 No content
3xx: Redirección
300 Multiple Choice
301 Moved Permanently
302 Found
304 Not Modified
4xx: Error del cliente
400 Bad Request
401 Unauthorized
403 Forbidden
404: Not found
5xx: Error del servidor
500 Internal Server Error
501 Not Implemented
502 Bad Gateway
503 Service Unavailabe
Algunas aclaraciones de los códigos de error
El 404 creo que lo conocen, es el típico error cuando buscamos una wew (endpoint) que no existe, como ven está catalogado como "error de cliente" es decir error nuestro, ya que somos nosotros los que le pedimos una dirección que no existe, cuando es 5xx son siempre errores de servidor, fallas que cuando vemos que empiezan con 5 el status code, olvidate, no podés hacer nada vos... El stratus code que devuelve cuando está todo ok es el 200 y en el caso de APIs los del 3xx no los vamos a ver nunca.

Es importante al menos entender esta diferencia entre la serie 400s y 500s de errores, más allá de las APIs en general en el mundo web, la arquitectura cliente-servidor hace referencia a las computadoras "en tu casa" - "en la casa de la página que visitas".

Es decir "cliente" es la compu de tu casa "Servidor" es la compu de la página web que está visitando.

Una comunicación HTTP, que es lo que tenemos en las APIs o cuando visitamos una web, es una comunicación entre esas dos computadoras, cuando hay un error de comunicación siempre hay una de las dos que lo origina (a menos que sea una error de redireccionamiento las series 300s)

Entonces vamos a lo importante, cuando nos devuelve un error de la serie 400s, ponele 404, 403, 401, etc, es porque algo le pifiamos nosotros, el servidor está respondiendo bien pero nos dice "man le pifiaste con lo que me pedís).

En cambio cuando recibimos un error del tipo 500s, es porque el servidor se está colgando, no es culpa nuestra, no revisen ahí el código porque cuando aparecen ese tipo de errores es culpa de la compu del otro lado.

Ojo que a veces nos devuelve 200 y sale error igual, que nos devuelve status code 200, no significa que haya entendido la API lo que le pedimos o haya aceptado los parámetros sino que significa que hubo comunicación entre la API y nosotros, es decir que hubo una respuesta asignada a nuestro llamdado, pero a veces vamos a ver que esa respuesta no siempre tiene los datos que esperabamos.
API AlphaVantage
Vamos a arrancar con la famosa API de alphaVantage, es una de las pioneras, si no me equivoco con la primera versión de IEX y con las discontinuidades GoogleFinance y YahooFinance fueron las pioneras en este mundo de las APIs financieras.

Como les dije en la intro, todas las  APIs del mundo del equity requieren autenticación, así que tienen que ir a www.alphavantage.co y pedir su "api_key".

En este caso como son los keys gratuitas les voy a empezar a mostrar ejemplos con una "api_key" que a partir de ahora llamaremos "token", que ya saqué yo con un mail de prueba para estos libros.

Obviamente el primer caso va a ser definir la variable token y asignarle el string que me de la API y ya lo dejo en la primera línea del código y me olvido del tema.

token = 'ZWDF2CNNPSO3CAGY'

https://www.alphavantage.co/documentation/#time-series-data

Bueno, como verán esta API es super sencilla, por eso quise empezar por esta, vamos a arrancar directamente por la primera función. 

Como verán es la serie de precios intradiarios, y me da una serie de parámetros obligatorios:

function: el nombre de la función (en esta API es obligatorio siempre este parámetro)
symbol: el ticker por ejemplo "AAPL"
interval: el intervalo entre cada vela (1min, 5min, 15min, 30min, 60min)
apikey: es nuestro clave, o token como definimos antes

Y una serie de parámetros optativos:

outputsize: compacto o full (aclara que por default compacto devuelve solo 100 datos)
datatype: nos da la posibilidad de descargarlo en un CSV (tipo excel) o devolver un JSON (texto que leerá python), por default es JSON y es lo que siempre vamos a usar, a menos que sea la API para descargarse excels, pero no tendría mucho sentido.

Bueno, antes de codear esto, vamos a aclarar una cosa, como dijimos un request de una API REST es ni más ni menos como visitar una página web, así que estos parámetros los podríamos poner en la barra del navegador web y tendría que funcionar, veamos un ejemplo, vamos a usar esta API para saber los precios intradiarios de AAPL en velas de 15 minutos.

La URL base es: https://www.alphavantage.co/query

Luego le debemos poner los parámetros, para ello:

Al primer parámetro lo antecedemos por el signo "?"
Y para concatenar los otros usamos el signo "&"

Entonces la URL final quedaría así:

https://www.alphavantage.co/query?function=TIME_SERIES_INTRADAY&symbol=AAPL&interval=15min&apikey=ZWDF|2CNNPSO3CAGY

{
    "Meta Data": {
        "1. Information": "Intraday (15min) open, high, low, close prices and volume",
        "2. Symbol": "AAPL",
        "3. Last Refreshed": "2021-08-23 20:00:00",
        "4. Interval": "15min",
        "5. Output Size": "Compact",
        "6. Time Zone": "US/Eastern"
    },
    "Time Series (15min)": {
        "2021-08-23 20:00:00": {
            "1. open": "149.7500",
            "2. high": "149.7700",
            "3. low": "149.5000",
            "4. close": "149.7300",
            "5. volume": "33588"
        },
        "2021-08-23 19:45:00": {
            "1. open": "149.7500",
            "2. high": "149.7500",
            "3. low": "149.7200",
            "4. close": "149.7200",
            "5. volume": "3760"
        },
        "2021-08-23 19:30:00": {
            "1. open": "149.7500",
            "2. high": "149.7600",
            "3. low": "149.7100",
            "4. close": "149.7200",
            "5. volume": "15325"
        },
        "2021-08-23 19:15:00": {
            "1. open": "149.7300",
            "2. high": "149.7500",
            "3. low": "149.7300",
            "4. close": "149.7400",
            "5. volume": "9258"
        },

Que obviamente esa respuesta es el JSON que esperamos, pero claramente nosotros no queremos entrar a una web y verlo, sino que queremos que python lo haga por nosotros y guarde en memoria los datos esos para procesarlo eventualmente luego.

Así que veamos como sería eso, vamos a empezar por la manera más burda de hacerlo y después lo emprolijamos.

Vamos a tener importar tres librerías:

requests: para hacer el llamado HTTP a la API
json: para trabajar con la respuesta que nos da
pandas: para guardar los datos en un DataFrame

Obviamente solo necesito la librería requests para comunicarme con la API el resto es para trabajar con los datos que me devuelve. 

# Importamos las librerías

import pandas as pd
import requests
import json

# Definimos las variables (parámetros)

function = 'TIME_SERIES_INTRADAY'
symbol = 'AAPL'
interval = '15min'
token = 'ZWDF2CNNPSO3CAGY'

# Concateno la URL a visitar:

urlBase = 'https://www.alphavantage.co/query'
url = urlBase + '?function=' + function + '&symbol=' + str(symbol)
url += '&interval=' + interval
url += '&outputsize=compact'
url += '&apikey=' + token

# Acá hago el llamado/Request
r = requests.get(url)

print(r)



    <Response [200]>

Bien, pero lo que vemos ahí cuando imprimimos el response, ¿qué es?

Es un objeto response de la librería requests, que obviamente tiene varios elementos y funciones:

text o content: es el texto plano como lo vemos en la web.
headers: los encabezados.
status_code: como vimos el código que ya vimos que es 200, es decir que la comunicación esta ok.
url: la url.
cookies: las cookies.
json(): el método para obtener el objeto json, o diccionario del contenido.

Veamos algún ejemplo:

print(r.url, '\n\n', r.cookies, '\n\n', r.status_code, '\n\n', r.headers)

Pero lo que nos interesa a nosotros es el JSON, entonces:

Pero si miramos bien como está el JSON imprimiendo r.json(), vemos que de todo el diccionario a mi la metadata no me interesa, sino que quiero la serie, así que de ese diccionario solo queremos la clave ['Time Series (15min)']

{'Meta Data': {'1. Information': 'Intraday (15min) open, high, low, close prices and volume',
  '2. Symbol': 'AAPL',
  '3. Last Refreshed': '2021-08-24 20:00:00',
  '4. Interval': '15min',
  '5. Output Size': 'Compact',
  '6. Time Zone': 'US/Eastern'},
 'Time Series (15min)': {'2021-08-24 20:00:00': {'1. open': '149.6400',
   '2. high': '149.6400',
   '3. low': '149.5300',
   '4. close': '149.5800',
   '5. volume': '13493'},
  '2021-08-24 19:45:00': {'1. open': '149.5700',
   '2. high': '149.6600',
   '3. low': '149.5700',
   '4. close': '149.6500',
   '5. volume': '9561'},
  '2021-08-24 19:30:00': {'1. open': '149.5700',
   '2. high': '149.5700',
   '3. low': '149.5500',
   '4. close': '149.5600',
   '5. volume': '8737'},

así que vamos a asignar a la variable "data" r.json() que era la respuesta, pero su clave ['Time Series (15min)']

Y eso lo vamos a estructurar en un DataFrame de pandas al que llamaremos dataDF.

Y como en este caso está orientado con un clave para cada fecha y valor para cada dato, vamos a usar el método from_dict() de los DataFrames para indicarle que la orientación es del índice así no me pone cada fecha en una columna, lo cual sería muy engorroso de leer y trabajar luego.

data = r.json()['Time Series (15min)']
dataDF = pd.DataFrame.from_dict(data, orient='index')
dataDF.head()

Bien, ahora emprolijemos un poco el código, en lugar de concatenar la URL entera con todos los parámetros, lo que podemos hacer, y es una buena práctica hacer, es mandar todos los parámetros como parámetros del request en lugar de mandarlos concatenado la URL.

function = 'TIME_SERIES_INTRADAY'
symbol = 'AAPL'
interval = '15min'
size = 'compact'
token = 'ZWDF2CNNPSO3CAGY'

url = 'https://www.alphavantage.co/query'
parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': token}

r = requests.get(url, params=parametros)
data = r.json()['Time Series (15min)']
dataDF = pd.DataFrame.from_dict(data, orient='index')
dataDF.head()

Y para terminar lo podemos meter en una función.

def getIntra(function, symbol, interval, size, token):
    url = 'https://www.alphavantage.co/query'
    parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': token}

    r = requests.get(url, params=parametros)
    data = r.json()['Time Series (15min)']
    dataDF = pd.DataFrame.from_dict(data, orient='index')
    return dataDF

data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='15min', size='compact', token='ZWDF2CNNPSO3CAGY')
data.head()

Esto funciona porque pasé como argumento a la función un intervalo de 15min pero ¿que pasaba si le pedía un intervalo de 1 min?

data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='1min', size='compact', token='ZWDF2CNNPSO3CAGY')
data.head()

Bien, me está diciendo que no encuentra en el diccionario JSON esa clave "15min" y obvio porque al hacer el request a la API con un intervalo de 1min, el diccionario va a tener en la serie la clave "Time Series (1min)" y no "Time Series (15min)", así que arreglemos la función para que funcione con cualquier intervalo válido.

def getIntra(function, symbol, interval, size, token):
    url = 'https://www.alphavantage.co/query'
    parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': token}

    r = requests.get(url, params=parametros)
    
    # En esta línea arreglo ese tema
    data = r.json()['Time Series ('+interval+')']
    dataDF = pd.DataFrame.from_dict(data, orient='index')
    return dataDF

data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='1min', size='compact', token='ZWDF2CNNPSO3CAGY')
data.head()
HardCodeando una Función
Ahora y ¿qué pasa si mando un intervalo que no es aceptado por la API?

data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='12min', size='compact', token='ZWDF2CNNPSO3CAGY')
data.head()

Ok, me dice que no encuentra esa serie tampoco en el JSON pero es efectivamente el intervalo que le pasamos en la función ¿como sabemos si la API está andando? Bien acá no queda otra que usar el concepto de "HARDCODEAR" la función para encontrar el error.

Esto de hardcodear es muy compún y es meterse en medio de una función ya hecha (en nuestro caso getIntra) y cambiarte algo a mano a propósito para ver que hace el programa, como investigar para rastrear el error (en nuestro caso ya sabemos que el error es porque mandamos un intervalo no válido pero supongamos que no lo sabemos y pensamos que la API tenía ese intervalo, es a modo didáctico el planteo).

Bien, ahí lo que hacemos es esto:

def getIntra(function, symbol, interval, size, token):
    url = 'https://www.alphavantage.co/query'
    parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': token}
    
    r = requests.get(url, params=parametros)
    
    # Comento la linea que falla en adelante
    
    # data = r.json()['Time Series ('+interval+')']
    # dataDF = pd.DataFrame.from_dict(data, orient='index')
    # return dataDF
    
    # Y mando la imporesión en pantalla del response 
    print(r)
    
data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='12min', size='compact', token='ZWDF2CNNPSO3CAGY')

Bien, en primer lugar me dice que la API se comunicó y me dio una respuesta válida, pero sigo sin entender donde está el error, asi que veamos que respuesta es

def getIntra(function, symbol, interval, size, token):
    url = 'https://www.alphavantage.co/query'
    parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': token}
    
    r = requests.get(url, params=parametros)
    
    # Comento la linea que falla en adelante
    
    # data = r.json()['Time Series ('+interval+')']
    # dataDF = pd.DataFrame.from_dict(data, orient='index')
    # return dataDF
    
    # Y mando la imporesión en pantalla del response 
    print(r.text)
    
data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='12min', size='compact', token='ZWDF2CNNPSO3CAGY')

Bien, ahora si, me da una explicación la API de que pasa que no me devolvió la serie cada 12 minutos, es una cagada porque no me sirve de mucho la explicación que me da, me dice solo que la llamada a la API que hice es inválida pero no me aclara que es por el intervalo que le pedí ni me da mayor explicación, pero bueno quería mostrarles como rastrear un error con esta API que es sencilla, hay APIs que cuando haces una llamada con algún parámetro más configurado te dice que es por ese parámetro el error y te devuelve en el mismo mensaje saliente de la API los parámetros válidos.
Sacando los token o keys afuera de la funciones
Bien, ahí va tomando forma, pero podríamos pensar que si vamos a ir definiendo una función para cada consulta, y para todas vamos a tener que pasar el token, y por otro lado el token va a ser siempre el mismo, en este caso, podríamos definir la variable "token" global al principio del código y que cada función la use libremente sin tener que pasarla como argumento siempre, este es un recurso válidom, veamos como queda:

Además por razones obvios de seguridad, por lo general los tokens o keys vienen de importar archivos con estos datos para que no queden expuestos en todos los archivos o vienen de bases de datos, por ahora lo ponemos así como recurso pedagógico pero por lo general no vienen en el mismo código como les digo asi nomás expuestos.

TOKEN = 'ZWDF2CNNPSO3CAGY'

def getIntra(function, symbol, interval, size):
    url = 'https://www.alphavantage.co/query'
    parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': TOKEN}
    
    r = requests.get(url, params=parametros)
    
    data = r.json()['Time Series ('+interval+')']
    dataDF = pd.DataFrame.from_dict(data, orient='index')
    return dataDF
    
data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='15min', size='compact')
data.head()

Ojo siempre con los tipos
Un temita a considerar siempre son los tipos de datos cuando descargamos de una API nueva que no conocemos, porque muchas veces nos confiamos que está todo ok, y después resulta que no tanto, por ejemplo, en el caso que mostré recién todo indicaría que los datos están bien y que ya podemos trabajar con ellos, pero, no es así, veamos bien.

Vamos a probar "redondear" los datos a solo 2 decimales.

def getIntra(function, symbol, interval, size):
    url = 'https://www.alphavantage.co/query'
    parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': TOKEN}
    
    r = requests.get(url, params=parametros)
    
    data = r.json()['Time Series ('+interval+')']
    dataDF = pd.DataFrame.from_dict(data, orient='index')
    return dataDF
    
data = getIntra(function='TIME_SERIES_INTRADAY', symbol='AAPL', interval='15min', size='compact')

data.round(2).head()

¿Que onda? no redondeó nada...

No insistan probando ejecutando de nuevo, va a salir lo mismo, el punto es que si no redondea es porque no interpreta los tipos de datos como números sino como strings, es re común esto en muchas APIs por eso lo muestro acá.

La manera de solucionarlo rápido con Pandas es usar el método "astype()" que transforma los tipos siempre que se pueda de todo el dataFrame.

data = data.astype('float')
data.round(2).head()

Así que lo vamos a meter directamente en la funcíon:

Y otra cosita, no se si habrán observado que esta API devuelve los datos ordenados de nuevo a viejo, cuando para trabajar con este tipo de series lo más cómodo a nuestro sentido común es trabajar de antiguo a nuevo, quizá para visualizar si, es mas util tener al principio de la tabla lo nuevo, pero como son series de tiempo el menor a mayor es de viejo a nuevo, asi que se suele trabajar así, por lo tanto vamos a meter este ordenamiento también dentro de la función.
Y otra cosa más, le vamos a poner los nombres a las columnas más estandarizados.
Y una más, vamos a pasar el índice a un tipo "datetime" porque también viene como un string.
Y ahora si la última cosita, si ven bien, le estamos pasando como parámetro de la función el parámetro del request "function" cuando es innecesario que se lo pasemos como parámetro a la función de python porque getIntra() siempre va a hacer el llamado a la API con function='TIME_SERIES_INTRADAY' así que eso lo vamos a poner como variable dentro de la función (para poder reusar ese código luego) pero no lo vamos a pedir como parámetro, sígname que despues se va a entender joya).

def getIntra(symbol, interval, size):
    function='TIME_SERIES_INTRADAY'
    url = 'https://www.alphavantage.co/query'
    parametros = {'function': function, 'symbol': symbol, 'interval': interval, 'outputsize': size, 'apikey': TOKEN}
    
    r = requests.get(url, params=parametros)
    
    data = r.json()['Time Series ('+interval+')']
    dataDF = pd.DataFrame.from_dict(data, orient='index')
    dataDF = dataDF.astype('float')
    dataDF.index.name = 'Date'
    dataDF.columns = ['Open', 'High', 'Low', 'Close', 'Volume']
    dataDF = dataDF.sort_values('Date', ascending=True)
    dataDF.index = pd.to_datetime(dataDF.index)
    return dataDF
    
data = getIntra(symbol='AAPL', interval='15min', size='compact')
data.round(2).head()

Listo ahí ya tenemos una función que me devuelve el DataFrame tal cual como lo necesito para hacer el análisis cuantitativo que quiera con la mayor comodidad.
Más funciones de la API AlphaVantage

Series Ajustadas

Cotización actual

Cotizaciones de otros mercados

Búsqueda de Activos

FOREX

Crypto

Indicadores Técnicos

Otros indicadores menos conocidos

Medias Móviles



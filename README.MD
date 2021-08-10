# Solucion Ethereum de capa 2

# Polygon

Polygon es una solución de las varias que han florecido desde hace untiempo en el ecosistema Ethereum, tienen como objetivo atacar diversas falencias que Ethereum aún sufre, por ejemplo el costo del gas y los tiempos requeridos para confirmar una transacción, que son inherentes al algoritmo POW (block finality). Polygon es un abanico de proyectos, en este documento solo se revisan algunos parametros importantes y estimaciones relacionadas con la solución POS (proof of stake) de dicho ecosistema.

Polygon POS (https://polygon.technology/technology/)

Si bien generalizando podemos incluir a Polygon en una solución de capa 2 pero para ser estrictos con la idea general de la comunidad de que significa una solución L2, para Polygon la clasificación mas exacta sería la de una "commit chain" o "sidechain".
<br/>
**En breve**: Las soluciones de capa 2 como por ejemplo Optimism, Arbitrum o Metis todas sincronizan todos los estados generados por las transacciones en la capa 2 también en la capa 1 (Ethereum mainnet) utilizando un proceso "batch". Es un punto critico de la seguridad de estas redes que un escenario de catastrofe permite regenerar el state de L2 utilizando los smartcontracts de L1
Para el caso de Polygon la segurización se produce con checkpoints periodicos en la capa 1 registrando solo un arbol de tipo merkle tree de las transacciones generadas en la capa 2. 


![diagrama](https://github.com/gonzalobellino/polygon_metrics/raw/main/polygon_diagram.png)

---
**Otra soluciones L2**


| Nombre | Tipo | Más info |
| -------- | -------- | -------- |
| Optimism     | Optimistic Rollups     | https://optimism.io/   
| Arbitrum     | Optimistic Rollups     | https://offchainlabs.com/   
| Metis        | Optimistic Rollups     | https://www.metis.io/
| ZkZync       | Rollups + SNARK |  https://zksync.io/ |

***(no se describen todas las soluciones disponibles solo algunas)***


---

## Datos tecnicos de Polygon


| Fact | Description |
| ---- | --- |
| L2 chain type | commit chain |
| Consensus|Tendermint|
|Block production time|2s|
|TPS|~100 (15-7-21)|
|Validators|76 generaron bloques en los ultimos 14 dias (15-7-21) |
|TVL| 8 mil millones (15-7)|



## Polygon en numeros
https://www.nansen.ai//research/polygon-and-ethereum-piecing-the-data-together


## Riesgos y mitigaciónes 

| Riesgo | Evaluación del riego | Mitigación |
| -------- | -------- | -------- |
| Aumento costo del gas  | Ver analisis de costo historico más abajo en este doc.  | Ver conclusión final
| Ataque >51% | Dada la cantidad de validadores actualmente en ejecución y la distribución de los mismos este riesgo es bajo | TSA aguarda una cantidad determinada de bloques antes de dar por ok la operatoria, esto permite dar mas seguridad sobre la inmutabilidad de los datos registrados |
| Ataque smart contracts bridge/commit L1 | Actualmente los smart contracts son actualizables con un umbral de firmas de 5 de 8 private keys | TSA mantiene un registro de transacciones de forma local que puede servir de punto de partida para una migración |
| Ataque DDOS    | Es posible dado el bajo costo de operar la red que un actor malisioso provoque demoras generando gran cantidad de transacciones | Incluir monitoreo del costo/gas para ser proactivos en la operatoria del servicio TSA. El riesgo mayor seria un ataque DDOS de larga duración que impida la emisión de transacciones por parte de AFIP

    
## Calculo de provisión de gas

![](https://i.imgur.com/GJyUaq9.png)
tabla de equivalencias

![](https://i.imgur.com/xLsA6cv.jpg)
grafico de promedio diario de costo de gas usado por las aplicaciones para operar la red.


>  1 gwei --> 0.00000001 MATIC

Para la simulación de costos se utilizaron los siguientes párametros 

- Precio del token MATIC de los últimos 365 dias
- Precio promedio del gas de los últimos 365 dias
- Cantidad de unidades GAS requeridas para un año de la TSA 168.600.000 (10m txs) 
- Empaquetado de transacciones con hashes merkle tree de 10.000 nodos

### Consideraciones 
- El costo real reemplazando el AVG. FEE por un SAFE LOW FEE reduciría aún más la estimación.

- el precio del token MATIC a tenido un salto desmesurado durante este año debido principalmente a un mercado de criptomonedas alcista y al arrivo de la maintnet de la solución como así también de multiples servicios de importancia. Sin embargo ahora que el mercado ha entrado en una tendencia bajista existen buenas chances de que el precio de este activo no influya significativamente en el costo de provisión para la TSA. Si seria mas importante monitorear el valor del gas dado que la red muestra costos muy fluctuantes actualmente debido a su aceptación en el mercado

 
## Calculo de provisión interactiva

https://github.com/gonzalobellino/polygon_metrics/blob/main/polygon_metrics.ipynb

### Resumen

Tomando los precios historicos del token MATIC (token nativo de Polygon) de los últimos 365 dias y utilizando como referencia los parámetros anteriores el costo apróximado de operar seria u$s 7.75 (utilizando costo promedio)

---

## Requerimientos para migrar

 - Reemplazar endpoint de nodo propio por el público 
 mainnet: 	https://rpc-mainnet.matic.network 
 testnet: 	https://rpc-mumbai.matic.today
 chainId:  137
 - Contar con una wallet positiva (con MATIC tokens)
 - Crear una estrategia para el cálculo del costo de emitir la transacción utilizando algún API pública para obtener el GAS PRICE, definir un valor de configuración fijo con un precio "SAFE" o crear un estimador que permita determinar el precio del gas requerido para que la transacción pueda ser procesada por los validadores
 - https://www.polygongasstation.com/calculator
https://github.com/QEDK/polygon-gas-station/blob/master/backend/main.py

## Herramientas extras
 - Source code --> https://github.com/maticnetwork/
 - Blockchain explorer --> https://polygonscan.com/
 - Faucet --> https://faucet.matic.network/


## Conclusión

Polygon es una solución mas que interesante para implementar TSA dado el costo operativo de utilizarla y el nulo costo de mantenimiento al interactuar con la red (no se necesita desplegar un nodo). Los puntos mas criticos del proyecto son los smartcontracts modificables por el equipo core del proyecto y las fluctuaciones del costo del gas

Propuestas

La propuesta mas conservadora seria monitorear la evolucion de esta solucion para contrastar las estimaciones de costo de gas realizadas y como se ajustan al incremento de uso de la red, ademas de poder observar potenciales ataques a la red y cuan resiliente es. Por otra parte evaluar que soluciones plantea el equipo core a la centralizacion actual de los smartcontracts. Por ultimo evaluar otra solucion de L2 como ser Arbitrum o Optimism que son consideradas mas seguras que la analizada Polygon o bien Fantom otra solucion de tipo "sidechain"

Una propuesta de riesgo bajo podria ser realizar la integracion para ciertos documentos de Ventanilla electronica para ir evaluando en tiempo real la solucion. Esto permitiria liberar TSA y evaluar el funcionamiento de la solucion al mismo tiempo. Como consideracion conservadora adicional se podria incorporar una leyenda en los documentos firmados indicando que la solucion se encuentra en fase de evaluacion



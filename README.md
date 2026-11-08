# Mover+

App Android (Kotlin, API mínima 29) para organizar treinos de corrida e acompanhar a evolução.
Trabalho prático individual de Programação de Dispositivos Móveis, UBI.

## Abrir, compilar e testar

1. Abrir a pasta Mover no Android Studio e esperar pela sincronização do Gradle.
2. Criar ou escolher um emulador com Android 10 (API 29) ou superior.
3. Carregar em Run. Para gerar o APK: Build → Build App Bundle(s) / APK(s) → Build APK(s).

Testar o GPS no emulador:

1. Abrir Começar corrida e aceitar a permissão de localização.
3. Esperar que o indicador do GPS fique verde e carregar em Começar.
4. No fim, carregar em Terminar → Guardar. O treino aparece no Histórico.

## Funcionalidades

Requisitos (Onde estão)
Definir objetivo de distância e tempo -> PlanoSemanalActivity 
Ver o plano semanal e o treino de hoje -> MainActivity + GeradorPlano 
GPS em tempo real: distância, tempo e ritmo -> CorridaActivity + ServicoGps + EstadoCorrida 
Guardar o treino e ver o histórico -> TreinoRepositorio + TreinoBD + HistoricoActivity 

Funcionalidades adicionais:

GPS ligado ao abrir o ecrã da corrida, para já estar preciso quando se começa
Plano semanal nos dias escolhidos pelo utilizador, com distância e ritmo para cada treino (séries no primeiro dia e longo no último)
Botão "Feito" no plano, que guarda a sessão como registo manual; qualquer treino registado na semana conta como a próxima sessão
Aviso por voz a cada km (TextToSpeech, funciona offline)
Parciais por km e gráfico de evolução semanal (GraficoBarrasView, desenhado com a classe Canvas)
Desnível positivo e negativo (D+ e D−)
Pausa, retoma e recuperação de um treino interrompido
Registo manual, edição e eliminação de treinos
Limpeza de dados nas Definições

## Armazenamento e estrutura dos dados

SQLite(mover.db, classe TreinoBD), acedido só através do TreinoRepositorio.
SharedPreferences para as definições (aviso por voz) e para a cópia da corrida em curso.

Tabela treinos: id, data_millis, distancia_km, duracao_segundos, bpm_medio, elevacao_ganho, elevacao_perda, via_gps, parciais (segundos de cada km, ex.: 340,332,351).

Tabela plano (só um plano ativo): id, tipo_objetivo, distancia_alvo_km, dias_semana,
data_prova_millis, tempo_atual_segundos,tempo_meta_segundos, dias_escolhidos (ex.: 1,3,6, em que 0 é segunda).

As atualizações da base de dados são feitas no onUpgrade
sem apagar os treinos já guardados.

## Decisões de implementação

O GPS usa LocationManager.GPS_PROVIDER, que funciona por satélite e não precisa de rede.
Durante a corrida o GPS corre num serviço de primeiro plano (ServicoGps), para continuar a funcionar mesmo com o ecrã desligado.
Leituras más do GPS são ignoradas: precisão pior que 25 m, movimentos menores que 2 m e saltos acima de 12 m/s.
O tempo usa SystemClock.elapsedRealtime(), que não depende do relógio do telemóvel.
As parciais são guardadas como texto porque são sempre lidas junto com o treino.
Os ecrãs estão fixos na vertical, porque a app é usada com o telemóvel na mão ou no braço.

## Dificuldades

Os valores do GPS oscilam mesmo parado, o que obrigou a filtrar as leituras e a suavizar o ritmo.
Manter o registo com o ecrã desligado, o que levou ao uso de um serviço em primeiro plano.
O GPS demorava a ficar preciso no início da corrida.
Acrescentar colunas à base de dados sem perder os treinos já guardados.

## Utilização de IA

Usei um assistente de IA (Claude) como apoio quando decidi ir além do MVP
e tornar algumas partes da app mais completas e complexas:

ServicoGps e CorridaActivity`: serviço em primeiro plano, notificação e preparação do GPS antes da corrida
EstadoCorrida: filtros das leituras de GPS, desnível (D+ e D−), ritmo suavizado e parciais por km
GraficoBarrasView: gráficos desenhados com Canvas
TreinoBD: migrações da base de dados
Layouts XML dos ecrãs



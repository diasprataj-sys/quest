const { EmbedBuilder } = require('discord.js');
const config = require('../../../config.json');

// --- Configurações Fixas ---
const WINNING_SCORE = 100; // Pontuação para entrar na Pergunta Final
const TIMEOUT_MS = 30000; // 30 segundos para escolher categoria/responder (30 * 1000)
const POINTS = {
    'facil': 2,
    'medio': 5,
    'dificil': 7
};

// --- MAPEMANTO PARA ESCOLHA POR TEXTO ---
const CATEGORIA_ATALHOS = {
    'v': 'variedades',
    's': 'sociedade',
    'm': 'mundo',
    'c': 'ciencia',
    'e': 'esportes'
};

const CATEGORIAS = Object.values(CATEGORIA_ATALHOS);


// --- Perguntas (Atualizadas: 12 FÁCEIS, 18 MÉDIAS, 10 DIFÍCEIS = 40) ---
const perguntas = {
    variedades: [
        // FÁCEIS (12) - 2 Pontos
        { pergunta: 'Qual é o nome da boneca que vive na Barbieland e se tornou um sucesso no cinema em 2023?', resposta: ['barbie'], dificuldade: 'facil' },
        { pergunta: 'Quantos lados tem um triângulo?', resposta: ['3', 'três'], dificuldade: 'facil' },
        { pergunta: 'Qual é o nome do criador de conteúdo mais seguido no Brasil em 2024?', resposta: ['luva de pedreiro'], dificuldade: 'facil' },
        { pergunta: 'Qual a fruta mais consumida no mundo?', resposta: ['banana'], dificuldade: 'facil' },
        { pergunta: 'Qual é a cor do cabelo do personagem Goku na sua forma Super Saiyajin base?', resposta: ['amarelo', 'dourado'], dificuldade: 'facil' },
        { pergunta: 'Qual cidade é conhecida como a "cidade maravilhosa"?', resposta: ['rio de janeiro', 'rio'], dificuldade: 'facil' },
        { pergunta: 'O que o Saci-Pererê usa para se locomover?', resposta: ['redemoinho', 'vento'], dificuldade: 'facil' },
        { pergunta: 'Qual a capital do estado de São Paulo?', resposta: ['sao paulo'], dificuldade: 'facil' },
        { pergunta: 'Quantas cores há no arco-íris?', resposta: ['7', 'sete'], dificuldade: 'facil' },
        { pergunta: 'Qual é o maior animal terrestre?', resposta: ['elefante africano', 'elefante'], dificuldade: 'facil' },
        { pergunta: 'Qual é a bebida típica da Escócia?', resposta: ['whisky'], dificuldade: 'facil' },
        { pergunta: 'Quantos dias tem um ano bissexto?', resposta: ['366', 'trezentos e sessenta e seis'], dificuldade: 'facil' },

        // MÉDIAS (18) - 5 Pontos
        { pergunta: 'Qual instrumento musical o personagem Squidward (Lula Molusco) de Bob Esponja toca?', resposta: ['clarinete'], dificuldade: 'medio' },
        { pergunta: 'Qual foi o primeiro longa-metragem totalmente feito em computação gráfica?', resposta: ['toy story'], dificuldade: 'medio' },
        { pergunta: 'Quem canta a música "Evidências", um clássico sertanejo?', resposta: ['chitaozinho e xororo', 'chitaozinho', 'xororo'], dificuldade: 'medio' },
        { pergunta: 'Qual o nome da moeda usada no Japão?', resposta: ['iene'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do famoso detetive criado por Arthur Conan Doyle?', resposta: ['sherlock holmes', 'holmes'], dificuldade: 'medio' },
        { pergunta: 'Qual é o apelido do cantor e compositor brasileiro Edson Arantes do Nascimento?', resposta: ['pele'], dificuldade: 'medio' },
        { pergunta: 'Em que ano a rede social "Facebook" foi lançada?', resposta: ['2004'], dificuldade: 'medio' },
        { pergunta: 'Qual é a obra de arte de Leonardo da Vinci famosa por seu sorriso enigmático?', resposta: ['mona lisa', 'la gioconda'], dificuldade: 'medio' },
        { pergunta: 'Quem pintou a Capela Sistina?', resposta: ['michelangelo', 'michelangelo buonarroti'], dificuldade: 'medio' },
        { pergunta: 'Qual livro conta a história de um menino que encontra um anel mágico?', resposta: ['o hobbit'], dificuldade: 'medio' },
        { pergunta: 'Qual cidade sediou os Jogos Olímpicos de Verão em 2016?', resposta: ['rio de janeiro', 'rio'], dificuldade: 'medio' },
        { pergunta: 'Qual ator ficou famoso por interpretar Indiana Jones?', resposta: ['harrison ford', 'ford'], dificuldade: 'medio' },
        { pergunta: 'Qual a maior rede social de vídeos curtos do mundo?', resposta: ['tiktok'], dificuldade: 'medio' },
        { pergunta: 'Em que país foi inventado o papel?', resposta: ['china'], dificuldade: 'medio' },
        { pergunta: 'Qual é a capital do México?', resposta: ['cidade do mexico'], dificuldade: 'medio' },
        { pergunta: 'Qual é a diferença entre um violino e uma viola?', resposta: ['tamanho', 'som mais grave'], dificuldade: 'medio' },
        { pergunta: 'Qual o nome do famoso musical que conta a história de Alexander Hamilton?', resposta: ['hamilton'], dificuldade: 'medio' },
        { pergunta: 'Qual super-herói tem o nome secreto de Clark Kent?', resposta: ['superman'], dificuldade: 'medio' },

        // DIFÍCEIS (10) - 7 Pontos
        { pergunta: 'Quem escreveu o livro "Dom Casmurro"?', resposta: ['machado de assis'], dificuldade: 'dificil' },
        { pergunta: 'Qual foi a primeira mulher a ganhar um Prêmio Nobel?', resposta: ['marie curie'], dificuldade: 'dificil' },
        { pergunta: 'Qual é a capital da Austrália?', resposta: ['canberra'], dificuldade: 'dificil' },
        { pergunta: 'Qual artista pop é conhecido por suas obras de serigrafia de Marilyn Monroe?', resposta: ['andy warhol'], dificuldade: 'dificil' },
        { pergunta: 'Quem dirigiu o filme "O Poderoso Chefão"?', resposta: ['francis ford coppola', 'coppola'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome da montanha mais alta do mundo?', resposta: ['monte everest', 'everest'], dificuldade: 'dificil' },
        { pergunta: 'Qual a moeda oficial da Suíça?', resposta: ['franco suico'], dificuldade: 'dificil' },
        { pergunta: 'Em qual cidade está localizada a Torre Eiffel?', resposta: ['paris'], dificuldade: 'dificil' },
        { pergunta: 'Qual o nome do famoso quadro de Edvard Munch que retrata uma figura em agonia?', resposta: ['o grito'], dificuldade: 'dificil' },
        { pergunta: 'Quem é o autor do livro "Cem Anos de Solidão"?', resposta: ['gabriel garcia marquez'], dificuldade: 'dificil' }
    ],
    sociedade: [
        // FÁCEIS (12) - 2 Pontos
        { pergunta: 'Qual é o nome do processo de votação para escolher líderes políticos?', resposta: ['eleicao', 'voto'], dificuldade: 'facil' },
        { pergunta: 'Qual é o nome do documento que garante direitos fundamentais no Brasil?', resposta: ['constituicao federal', 'constituicao'], dificuldade: 'facil' },
        { pergunta: 'Qual é o dia da celebração do Dia do Trabalho?', resposta: ['1 de maio'], dificuldade: 'facil' },
        { pergunta: 'Como se chama o ato de parar de trabalhar para exigir melhores condições?', resposta: ['greve'], dificuldade: 'facil' },
        { pergunta: 'Qual instituição é responsável por criar as leis no Brasil?', resposta: ['congresso nacional', 'congresso', 'poder legislativo'], dificuldade: 'facil' },
        { pergunta: 'O que significa a sigla ONU?', resposta: ['organizacao das nacoes unidas'], dificuldade: 'facil' },
        { pergunta: 'O que a sigla SUS representa na saúde pública brasileira?', resposta: ['sistema unico de saude'], dificuldade: 'facil' },
        { pergunta: 'Qual é o principal símbolo da justiça?', resposta: ['balanca', 'deusa themis'], dificuldade: 'facil' },
        { pergunta: 'Qual é o mês da independência do Brasil?', resposta: ['setembro'], dificuldade: 'facil' },
        { pergunta: 'Qual é o nome dado ao estudo da população?', resposta: ['demografia'], dificuldade: 'facil' },
        { pergunta: 'Qual o nome da moeda oficial do Brasil?', resposta: ['real'], dificuldade: 'facil' },
        { pergunta: 'O que o semáforo indica com a luz verde?', resposta: ['siga', 'avancar'], dificuldade: 'facil' },

        // MÉDIAS (18) - 5 Pontos
        { pergunta: 'Em que ano foi proclamada a República no Brasil?', resposta: ['1889'], dificuldade: 'medio' },
        { pergunta: 'Qual é o conceito que define a liberdade de expressão de cada indivíduo?', resposta: ['democracia', 'liberdade de expressao'], dificuldade: 'medio' },
        { pergunta: 'Qual evento histórico marcou o início da Idade Contemporânea?', resposta: ['revolucao francesa'], dificuldade: 'medio' },
        { pergunta: 'Qual líder indiano defendeu a desobediência civil e a não-violência?', resposta: ['mahatma gandhi', 'gandhi'], dificuldade: 'medio' },
        { pergunta: 'Em que século ocorreu a Segunda Guerra Mundial?', resposta: ['seculo xx', '20'], dificuldade: 'medio' },
        { pergunta: 'Qual documento foi assinado no Brasil em 13 de maio de 1888, abolindo a escravidão?', resposta: ['lei aurea'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do tratado que estabeleceu as regras internacionais para as relações entre países?', resposta: ['carta das nacoes unidas'], dificuldade: 'medio' },
        { pergunta: 'Qual é a capital do estado de Minas Gerais?', resposta: ['belo horizonte'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do movimento de luta por direitos civis liderado por Martin Luther King Jr.?', resposta: ['movimento pelos direitos civis', 'direitos civis'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do líder que uniu a Alemanha no século XIX?', resposta: ['otto von bismarck', 'bismarck'], dificuldade: 'medio' },
        { pergunta: 'Qual foi o ano da queda do Muro de Berlim?', resposta: ['1989'], dificuldade: 'medio' },
        { pergunta: 'Qual país colonizou o Brasil?', resposta: ['portugal'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome da lei que estabelece a idade mínima para o trabalho no Brasil?', resposta: ['eca', 'estatuto da crianca e do adolescente'], dificuldade: 'medio' },
        { pergunta: 'O que é a inflação?', resposta: ['aumento de precos', 'perda de poder de compra'], dificuldade: 'medio' },
        { pergunta: 'Qual é o principal símbolo da Revolução Francesa?', resposta: ['guilhotina', 'liberdade igualdade fraternidade'], dificuldade: 'medio' },
        { pergunta: 'Quem foi o primeiro presidente do Brasil?', resposta: ['marechal deodoro da fonseca', 'deodoro da fonseca'], dificuldade: 'medio' },
        { pergunta: 'Qual foi o ano do início da Primeira Guerra Mundial?', resposta: ['1914'], dificuldade: 'medio' },
        { pergunta: 'O que é o PIB?', resposta: ['produto interno bruto'], dificuldade: 'medio' },

        // DIFÍCEIS (10) - 7 Pontos
        { pergunta: 'Qual líder mundial foi libertado da prisão em 1990 após 27 anos, e se tornou presidente da África do Sul?', resposta: ['nelson mandela', 'mandela'], dificuldade: 'dificil' },
        { pergunta: 'Em que ano foi assinado o Tratado de Tordesilhas?', resposta: ['1494'], dificuldade: 'dificil' },
        { pergunta: 'Qual foi o movimento cultural e intelectual que marcou a transição para a Idade Moderna?', resposta: ['renascimento'], dificuldade: 'dificil' },
        { pergunta: 'Qual foi a principal razão para o Brasil entrar na Segunda Guerra Mundial?', resposta: ['submarinos alemaes', 'ataques nazistas', 'afundamento de navios', 'navios afundados', 'ataque a navios', 'afundamento de navios brasileiros'], dificuldade: 'dificil' },
        { pergunta: 'Qual foi o último país da América a abolir a escravidão?', resposta: ['brasil'], dificuldade: 'dificil' },
        { pergunta: 'Qual foi a primeira mulher a chefiar o governo no Brasil?', resposta: ['dilma rousseff', 'dilma'], dificuldade: 'dificil' },
        { pergunta: 'Em que ano foi promulgada a primeira Constituição do Brasil independente?', resposta: ['1824'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome do sistema econômico baseado na propriedade privada e busca por lucro?', resposta: ['capitalismo'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o principal objetivo da Cruz Vermelha?', resposta: ['ajuda humanitaria', 'socorro em guerras'], dificuldade: 'dificil' },
        { pergunta: 'O que a sigla OMS representa?', resposta: ['organizacao mundial da saude'], dificuldade: 'dificil' }
    ],
    mundo: [
        // FÁCEIS (12) - 2 Pontos
        { pergunta: 'Qual é o maior país do mundo em área territorial?', resposta: ['russia'], dificuldade: 'facil' },
        { pergunta: 'Qual continente é o lar do deserto do Saara?', resposta: ['africa'], dificuldade: 'facil' },
        { pergunta: 'Qual país tem a forma de uma bota?', resposta: ['italia'], dificuldade: 'facil' },
        { pergunta: 'Qual é o rio mais longo do mundo?', resposta: ['rio amazonas', 'amazonas'], dificuldade: 'facil' },
        { pergunta: 'Qual é a moeda oficial dos Estados Unidos?', resposta: ['dolar'], dificuldade: 'facil' },
        { pergunta: 'Qual país é famoso por ter as pirâmides de Gizé?', resposta: ['egito'], dificuldade: 'facil' },
        { pergunta: 'Qual país possui a maior população do mundo?', resposta: ['india'], dificuldade: 'facil' },
        { pergunta: 'Qual é a capital da França?', resposta: ['paris'], dificuldade: 'facil' },
        { pergunta: 'Qual oceano banha a costa oeste da América do Sul?', resposta: ['pacifico', 'oceano pacifico'], dificuldade: 'facil' },
        { pergunta: 'Qual é a capital do Reino Unido?', resposta: ['londres'], dificuldade: 'facil' },
        { pergunta: 'Qual é a língua mais falada no mundo por número de falantes nativos?', resposta: ['mandarim', 'chines'], dificuldade: 'facil' },
        { pergunta: 'Qual é o país vizinho do Brasil ao norte que fala francês?', resposta: ['guiana francesa'], dificuldade: 'facil' },

        // MÉDIAS (18) - 5 Pontos
        { pergunta: 'Qual cidade é cortada ao meio pelo Muro de Berlim, antes de sua queda?', resposta: ['berlim'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do canal artificial que liga o Mar Mediterrâneo ao Mar Vermelho?', resposta: ['canal de suez', 'suez'], dificuldade: 'medio' },
        { pergunta: 'Qual país é conhecido como "Terra do Sol Nascente"?', resposta: ['japao'], dificuldade: 'medio' },
        { pergunta: 'Qual oceano banha a costa leste do Brasil?', resposta: ['atlantico', 'oceano atlantico'], dificuldade: 'medio' },
        { pergunta: 'Qual é o menor país do mundo em área?', resposta: ['vaticano', 'cidade do vaticano'], dificuldade: 'medio' },
        { pergunta: 'Qual é o ponto mais baixo da superfície terrestre, localizado no Oriente Médio?', resposta: ['mar morto'], dificuldade: 'medio' },
        { pergunta: 'Qual é o maior lago de água doce do mundo em volume?', resposta: ['lago baikal', 'baikal'], dificuldade: 'medio' },
        { pergunta: 'Qual país é o maior produtor de café do mundo?', resposta: ['brasil'], dificuldade: 'medio' },
        { pergunta: 'Qual a capital do Canadá?', resposta: ['ottawa'], dificuldade: 'medio' },
        { pergunta: 'Qual cordilheira se estende pela Suíça, Áustria, França e Itália?', resposta: ['alpes'], dificuldade: 'medio' },
        { pergunta: 'Qual país é famoso por seus cangurus e coalas?', resposta: ['australia'], dificuldade: 'medio' },
        { pergunta: 'Qual é a capital da Espanha?', resposta: ['madri', 'madrid'], dificuldade: 'medio' },
        { pergunta: 'Qual o nome do famoso vulcão que destruiu Pompeia?', resposta: ['vesuvio'], dificuldade: 'medio' },
        { pergunta: 'Qual país é banhado pelo Mar Negro e o Mar Mediterrâneo?', resposta: ['turquia'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome da montanha mais alta da África?', resposta: ['kilimanjaro'], dificuldade: 'medio' },
        { pergunta: 'Qual rio passa por Londres?', resposta: ['rio tamisa', 'tamisa'], dificuldade: 'medio' },
        { pergunta: 'Qual o país de origem da pizza?', resposta: ['italia'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do deserto que cobre a maior parte da China e da Mongólia?', resposta: ['deserto de gobi', 'gobi'], dificuldade: 'medio' },

        // DIFÍCEIS (10) - 7 Pontos
        { pergunta: 'Qual é o único país que faz fronteira terrestre com a Dinamarca?', resposta: ['alemanha'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome da cordilheira mais longa da América do Sul?', resposta: ['cordilheira dos andes', 'andes'], dificuldade: 'dificil' },
        { pergunta: 'Em que ano foi inaugurado o Canal do Panamá?', resposta: ['1914'], dificuldade: 'dificil' },
        { pergunta: 'Qual país é conhecido como a "Suíça da América Central" por sua neutralidade política?', resposta: ['costa rica'], dificuldade: 'dificil' },
        { pergunta: 'Qual país era anteriormente conhecido como Rodésia do Sul?', resposta: ['zimbabue'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome do arquipélago pertencente ao Equador, famoso por suas tartarugas gigantes?', resposta: ['galapagos', 'ilhas galapagos'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome do estreito que separa a Europa da Ásia na Turquia?', resposta: ['estreito de bosforo', 'bosforo'], dificuldade: 'dificil' },
        { pergunta: 'Qual país africano foi o último a ser colonizado?', resposta: ['etiopia'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o maior deserto do mundo (não polar)?', resposta: ['saara'], dificuldade: 'dificil' },
        { pergunta: 'Qual país é o maior produtor mundial de petróleo?', resposta: ['estados unidos', 'eua'], dificuldade: 'dificil' }
    ],
    ciencia: [
        // FÁCEIS (12) - 2 Pontos
        { pergunta: 'Qual é o gás que as plantas absorvem para fazer fotossíntese?', resposta: ['dioxido de carbono', 'co2'], dificuldade: 'facil' },
        { pergunta: 'Qual é o planeta mais próximo do Sol?', resposta: ['mercurio'], dificuldade: 'facil' },
        { pergunta: 'O que a sigla H2O representa?', resposta: ['agua'], dificuldade: 'facil' },
        { pergunta: 'Qual é o maior órgão do corpo humano?', resposta: ['pele'], dificuldade: 'facil' },
        { pergunta: 'Qual é o metal mais abundante na crosta terrestre?', resposta: ['aluminio'], dificuldade: 'facil' },
        { pergunta: 'Quantos planetas orbitam o Sol no nosso sistema solar?', resposta: ['8', 'oito'], dificuldade: 'facil' },
        { pergunta: 'Qual é o nome da força que nos mantém presos à Terra?', resposta: ['gravidade'], dificuldade: 'facil' },
        { pergunta: 'Qual é o nome do cientista que formulou a lei da gravidade?', resposta: ['isaac newton', 'newton'], dificuldade: 'facil' },
        { pergunta: 'Qual é a principal fonte de energia da Terra?', resposta: ['sol'], dificuldade: 'facil' },
        { pergunta: 'Qual é o processo pelo qual a água se transforma em vapor?', resposta: ['evaporacao', 'vaporizacao'], dificuldade: 'facil' },
        { pergunta: 'Qual é o nome do sistema de classificação de espécies criado por Lineu?', resposta: ['nomenclatura binomial', 'binomial'], dificuldade: 'facil' },
        { pergunta: 'Qual é o único satélite natural da Terra?', resposta: ['lua'], dificuldade: 'facil' },

        // MÉDIAS (18) - 5 Pontos
        { pergunta: 'Quem propôs a Teoria da Relatividade?', resposta: ['albert einstein', 'einstein'], dificuldade: 'medio' },
        { pergunta: 'Qual é o elemento químico mais leve?', resposta: ['hidrogenio'], dificuldade: 'medio' },
        { pergunta: 'Qual camada da Terra protege os seres vivos da radiação ultravioleta?', resposta: ['camada de ozonio', 'ozonio'], dificuldade: 'medio' },
        { pergunta: 'O que o termo "pH" mede em uma solução?', resposta: ['acidez', 'alcalinidade'], dificuldade: 'medio' },
        { pergunta: 'Qual foi o primeiro ser vivo a orbitar a Terra, a bordo da Sputnik 2?', resposta: ['laika', 'cadela laika'], dificuldade: 'medio' },
        { pergunta: 'Qual é a unidade básica da hereditariedade?', resposta: ['gene'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do processo onde a água passa do estado líquido para o gasoso?', resposta: ['evaporacao', 'vaporizacao'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome da galáxia mais próxima da Via Láctea?', resposta: ['andromeda'], dificuldade: 'medio' },
        { pergunta: 'Qual é o maior planeta do nosso sistema solar?', resposta: ['jupiter'], dificuldade: 'medio' },
        { pergunta: 'O que a luz vermelha e a luz azul fazem com as plantas?', resposta: ['fotossintese'], dificuldade: 'medio' },
        { pergunta: 'Qual é o principal componente do ar que respiramos?', resposta: ['nitrogenio'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do cientista que descobriu a estrutura em dupla hélice do DNA?', resposta: ['james watson e francis crick', 'watson e crick', 'watson', 'crick'], dificuldade: 'medio' },
        { pergunta: 'Em que ano o homem pisou na Lua pela primeira vez?', resposta: ['1969'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome do estudo dos terremotos?', resposta: ['sismologia'], dificuldade: 'medio' },
        { pergunta: 'Qual o metal líquido na temperatura ambiente?', resposta: ['mercurio'], dificuldade: 'medio' },
        { pergunta: 'Qual é o nome dado às células nervosas?', resposta: ['neuronios'], dificuldade: 'medio' },
        { pergunta: 'Qual é a unidade de medida de energia no Sistema Internacional (SI)?', resposta: ['joule'], dificuldade: 'medio' },
        { pergunta: 'Qual o nome do fenômeno óptico que separa a luz branca em cores?', resposta: ['dispersao', 'refracao'], dificuldade: 'medio' },

        // DIFÍCEIS (10) - 7 Pontos
        { pergunta: 'Qual é o nome do buraco negro supermassivo no centro da Via Láctea?', resposta: ['sagitario a estrela', 'sagitarius a'], dificuldade: 'dificil' },
        { pergunta: 'Quem descobriu a penicilina?', resposta: ['alexander fleming', 'fleming'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o número atômico do ouro (Au)?', resposta: ['79'], dificuldade: 'dificil' },
        { pergunta: 'Qual animal é o principal hospedeiro do vírus da raiva?', resposta: ['morcego'], dificuldade: 'dificil' },
        { pergunta: 'Qual teoria sugere que a vida se originou de matéria inanimada?', resposta: ['abiogenese'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o menor tipo de partícula subatômica conhecida?', resposta: ['quark'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome da reação química que libera energia do Sol?', resposta: ['fusao nuclear'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome científico do ser humano?', resposta: ['homo sapiens'], dificuldade: 'dificil' },
        { pergunta: 'Qual a temperatura em Celsius do ponto de congelamento da água?', resposta: ['0', 'zero'], dificuldade: 'dificil' },
        { pergunta: 'Qual a velocidade da luz no vácuo (em km/s)?', resposta: ['300000', '300.000', '300 mil'], dificuldade: 'dificil' }
    ],
    esportes: [
        // FÁCEIS (12) - 2 Pontos
        { pergunta: 'Quantos jogadores de linha um time de futebol tem em campo?', resposta: ['11', 'onze'], dificuldade: 'facil' },
        { pergunta: 'Em qual esporte se usa tacos, buracos e bolas pequenas?', resposta: ['golfe'], dificuldade: 'facil' },
        { pergunta: 'Qual é o esporte de raquete mais popular no Brasil, depois do tênis?', resposta: ['badminton'], dificuldade: 'facil' },
        { pergunta: 'Quantos pontos vale um touchdown no futebol americano, sem o extra point?', resposta: ['6', 'seis'], dificuldade: 'facil' },
        { pergunta: 'Qual é a cor da bandeira que indica que um corredor de F1 venceu a corrida?', resposta: ['quadriculada', 'xadrez'], dificuldade: 'facil' },
        { pergunta: 'Qual país sediou a Copa do Mundo de Futebol em 2014?', resposta: ['brasil'], dificuldade: 'facil' },
        { pergunta: 'Qual é o nome do acessório usado para proteger a cabeça no boxe?', resposta: ['capacete', 'protetor de cabeca'], dificuldade: 'facil' },
        { pergunta: 'Em qual esporte se usa uma rede e um disco de borracha chamado "puck"?', resposta: ['hoquei no gelo', 'hoquei'], dificuldade: 'facil' },
        { pergunta: 'Quantos períodos tem um jogo de basquete?', resposta: ['4', 'quatro'], dificuldade: 'facil' },
        { pergunta: 'Qual o nome do principal torneio de tênis jogado em quadras de saibro?', resposta: ['roland garros'], dificuldade: 'facil' },
        { pergunta: 'Qual é o país de origem da modalidade de luta Judô?', resposta: ['japao'], dificuldade: 'facil' },
        { pergunta: 'Quantos buracos tem um campo de golfe padrão?', resposta: ['18', 'dezoito'], dificuldade: 'facil' },

        // MÉDIAS (18) - 5 Pontos
        { pergunta: 'Quem é o jogador de basquete considerado o maior de todos os tempos, com seis títulos da NBA pelo Chicago Bulls?', resposta: ['michael jordan', 'jordan'], dificuldade: 'medio' },
        { pergunta: 'Em que cidade o primeiro jogo das Olimpíadas Modernas foi realizado em 1896?', resposta: ['atenas'], dificuldade: 'medio' },
        { pergunta: 'Qual é o esporte em que o objetivo é derrubar pinos usando uma bola pesada?', resposta: ['boliche'], dificuldade: 'medio' },
        { pergunta: 'Qual nadador americano possui o maior número de medalhas de ouro olímpicas?', resposta: ['michael phelps', 'phelps'], dificuldade: 'medio' },
        { pergunta: 'Qual o nome do torneio de tênis jogado na grama, considerado o mais tradicional?', resposta: ['wimbledon'], dificuldade: 'medio' },
        { pergunta: 'Quantos sets máximos podem ser jogados em uma partida de vôlei?', resposta: ['5', 'cinco'], dificuldade: 'medio' },
        { pergunta: 'Qual é a distância de uma maratona (em quilômetros)?', resposta: ['42', '42.195', '42.195 km'], dificuldade: 'medio' },
        { pergunta: 'Quem é conhecido como "O Rei do Futebol"?', resposta: ['pele'], dificuldade: 'medio' },
        { pergunta: 'Qual o nome do estádio do time de futebol Barcelona?', resposta: ['camp nou'], dificuldade: 'medio' },
        { pergunta: 'Quantos jogadores compõem um time de handebol em quadra?', resposta: ['7', 'sete'], dificuldade: 'medio' },
        { pergunta: 'Em qual esporte o termo "hole-in-one" é usado?', resposta: ['golfe'], dificuldade: 'medio' },
        { pergunta: 'Qual lutador de boxe é conhecido como "The Greatest"?', resposta: ['muhammad ali', 'ali'], dificuldade: 'medio' },
        { pergunta: 'Qual país detém o maior número de medalhas de ouro na história dos Jogos Olímpicos de Inverno?', resposta: ['noruega'], dificuldade: 'medio' },
        { pergunta: 'O que o jogador de futebol americano faz para marcar um safety?', resposta: ['derrubar o oponente na end zone dele', 'tackle na end zone'], dificuldade: 'medio' },
        { pergunta: 'Qual o nome do jogador de futebol argentino que ficou famoso pela "Mão de Deus"?', resposta: ['diego maradona', 'maradona'], dificuldade: 'medio' },
        { pergunta: 'Qual é o principal torneio de basquete universitário dos EUA?', resposta: ['march madness', 'ncaa'], dificuldade: 'medio' },
        { pergunta: 'Em que esporte o Brasil é mais famoso por suas medalhas olímpicas?', resposta: ['volei', 'futebol'], dificuldade: 'medio' },
        { pergunta: 'Qual a cor da camisa do líder da classificação geral no Tour de France?', resposta: ['amarela'], dificuldade: 'medio' },


        // DIFÍCEIS (10) - 7 Pontos
        { pergunta: 'Qual país venceu a Copa do Mundo de Futebol de 2018?', resposta: ['franca'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o único país da América do Sul que não possui litoral para o oceano?', resposta: ['paraguai', 'bolivia'], dificuldade: 'dificil' },
        { pergunta: 'Quem foi o primeiro brasileiro a vencer um título na Fórmula 1?', resposta: ['emerson fittipaldi', 'fittipaldi'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome da posição no beisebol que arremessa a bola para o batedor?', resposta: ['arremessador', 'pitcher'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o recorde mundial de salto em distância (em metros) masculino que perdura desde 1991?', resposta: ['8.95', '8.95m'], dificuldade: 'dificil' },
        { pergunta: 'Em qual esporte o termo "Birdie" é usado?', resposta: ['golfe'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome do time de futebol americano com o maior número de Super Bowls?', resposta: ['steelers', 'patriots'], dificuldade: 'dificil' },
        { pergunta: 'Qual é a capital dos Jogos Olímpicos?', resposta: ['lausan', 'lausanne'], dificuldade: 'dificil' },
        { pergunta: 'Qual é o nome do torneio de rugby mais importante do mundo?', resposta: ['copa do mundo de rugby'], dificuldade: 'dificil' },
        { pergunta: 'Qual tenista é o maior vencedor de Grand Slams masculinos (em 2024)?', resposta: ['novak djokovic', 'djokovic'], dificuldade: 'dificil' }
    ]
};


// --- Funções Auxiliares ---

/**
 * Cria a string de classificação ordenada.
 * @param {object} placar - O objeto de placar { 'id': pontos }
 * @param {Array<Object>} players - Lista de jogadores
 */
const mostrarClassificacao = (placar, players) => {
    return Object.entries(placar)
        .sort((a, b) => b[1] - a[1])
        .map(([id, pontos], index) => {
            let medalha = '';
            if (index === 0) medalha = '🥇';
            else if (index === 1) medalha = '🥈';
            else if (index === 2) medalha = '🥉';
            const username = players.find(p => p.id === id)?.username || 'Desconhecido';
            return `${medalha} **${username}** - ${pontos} pts`;
        }).join('\n');
};

/**
 * Encontra uma pergunta aleatória.
 */
const getRandomQuestion = (categoria, dificuldade = null) => {
    let lista = perguntas[categoria] || [];
    if (dificuldade) {
        lista = lista.filter(p => p.dificuldade === dificuldade);
    }
    if (lista.length === 0) return null;
    return lista[Math.floor(Math.random() * lista.length)];
};


// --- Funções de Lógica do Jogo ---

/**
 * Avança o turno e chama o seletor de categoria.
 * @param {object} message - O objeto Message
 * @param {object} gameState - O estado do jogo atual
 * @param {object} gameStates - O objeto global de estados
 * @param {string} guildId - ID do servidor para checagem
 */
const proximoTurno = async (message, gameState, gameStates, guildId) => {
    // CORREÇÃO CRÍTICA: Checa se o estado do jogo foi deletado (comando .quest parar)
    if (!gameStates[guildId] || !gameState.jogoAtivo) {
        return; 
    }
    
    // --- Verificação de Pergunta Final ---
    // A pontuação de corte é a pontuação de vitória menos os pontos da difícil
    gameState.candidatosFinal = gameState.players
        .filter(p => gameState.placar[p.id] >= (WINNING_SCORE - POINTS['dificil']));
    
    if (gameState.candidatosFinal.length > 0) {
        // Ordena pelo placar para garantir que o primeiro candidato seja o de maior pontuação
        gameState.candidatosFinal.sort((a, b) => gameState.placar[b.id] - gameState.placar[a.id]);
        return perguntaFinal(message, gameState, gameStates, guildId);
    }
    // -------------------------------------

    // Avança o turno
    gameState.turnoIndex = (gameState.turnoIndex + 1) % gameState.players.length;
    const jogador = gameState.players[gameState.turnoIndex];

    // Envia o placar
    const rankingEmbed = new EmbedBuilder()
        .setTitle('📊 Classificação Atual')
        .setDescription(mostrarClassificacao(gameState.placar, gameState.players))
        .setColor(config.colors.navyBlue);
    await message.channel.send({ embeds: [rankingEmbed] });

    // Envia o seletor de categoria por texto
    const embedEscolha = new EmbedBuilder()
        .setColor(config.colors.royalBlue)
        .setTitle(`🎲 É a vez de ${jogador.username}`)
        .setDescription(`**Escolha a categoria** da pergunta enviando apenas a letra correspondente no chat. Você tem **${TIMEOUT_MS / 1000} segundos!**`) // Mostra 30 segundos
        .addFields(
            { name: 'Categorias', value: 
                '**V** - Variedades\n' +
                '**S** - Sociedade\n' +
                '**M** - Mundo\n' +
                '**C** - Ciência/Tecnologia\n' +
                '**E** - Esportes/Lazer' 
            }
        );

    // Notifica o jogador
    const turnoMessage = await message.channel.send({ content: `<@${jogador.id}>`, embeds: [embedEscolha] });
    
    // Filtro: Aceita apenas mensagens do jogador que é a vez, com a letra correta.
    const validKeys = Object.keys(CATEGORIA_ATALHOS);
    const filter = m => m.author.id === jogador.id && validKeys.includes(m.content.toLowerCase());
    
    const collector = message.channel.createMessageCollector({ 
        filter, 
        max: 1, 
        time: TIMEOUT_MS,
        errors: ['time']
    });

    collector.on('collect', async m => {
        // CORREÇÃO CRÍTICA: Checa se o jogo foi encerrado enquanto o coletor estava ativo
        if (!gameStates[guildId] || !gameStates[guildId].jogoAtivo) return; 
        
        const atalho = m.content.toLowerCase();
        const categoriaEscolhida = CATEGORIA_ATALHOS[atalho];
        
        await m.delete().catch(() => {});
        
        // Passa o gameStates e guildId
        await enviarPergunta(message, gameState, gameStates, guildId, categoriaEscolhida);
    });

    collector.on('end', async (collected, reason) => {
        // CORREÇÃO CRÍTICA: Checa se o jogo foi encerrado
        if (!gameStates[guildId] || !gameStates[guildId].jogoAtivo) return;
        
        if (reason === 'time' && collected.size === 0) {
            // Timeout e nenhuma escolha coletada
            await message.channel.send(`**${jogador.username}** não escolheu categoria a tempo! Pulando turno.`);
            
            // Chama o próximo turno, passando o gameStates e guildId
            proximoTurno(message, gameState, gameStates, guildId);
        }
    });
};

/**
 * Envia a pergunta para o jogador e configura o coletor de resposta.
 * @param {object} message - O objeto Message
 * @param {object} gameState - O estado do jogo atual
 * @param {object} gameStates - O objeto global de estados (NOVO)
 * @param {string} guildId - ID do servidor para checagem (NOVO)
 * @param {string} categoria - Categoria escolhida
 * @param {string} [dificuldade] - Dificuldade da pergunta
 */
const enviarPergunta = async (message, gameState, gameStates, guildId, categoria, dificuldade = null) => {
    // CORREÇÃO CRÍTICA: Checa se o jogo foi parado
    if (!gameStates[guildId] || !gameState.jogoAtivo) return;

    const jogador = gameState.players[gameState.turnoIndex];
    
    const dificuldadeEscolhida = dificuldade || 'medio'; 

    const pergunta = getRandomQuestion(categoria, dificuldadeEscolhida);

    if (!pergunta) {
        await message.channel.send(`❌ Não há perguntas de ${categoria} (${dificuldadeEscolhida}). Pulando turno.`);
        return proximoTurno(message, gameState, gameStates, guildId);
    }
    
    gameState.currentQuestion = pergunta;

    const embedPergunta = new EmbedBuilder()
        .setColor(config.colors.royalBlue)
        .setTitle(`❓ Pergunta para ${jogador.username}`)
        .setDescription(`Categoria: **${categoria.toUpperCase()}**\nDificuldade: **${pergunta.dificuldade.toUpperCase()}**\n\n**Pergunta:** ${pergunta.pergunta}\n\n**Você tem 30 segundos!** Responda usando: **.q (sua resposta)**`);

    await message.channel.send({ embeds: [embedPergunta] });
    
    // O coletor de respostas será configurado no comando .q no arquivo q.js
};


/**
 * Inicia a votação e a Pergunta Final.
 * @param {object} message - O objeto Message
 * @param {object} gameState - O estado do jogo atual
 * @param {object} gameStates - O objeto global de estados
 * @param {string} guildId - ID do servidor para checagem
 */
const perguntaFinal = async (message, gameState, gameStates, guildId) => {
    // CORREÇÃO CRÍTICA: Checa se o jogo foi parado
    if (!gameStates[guildId] || !gameState.jogoAtivo) return;

    gameState.jogoAtivo = false; // Pausa o jogo normal para votação

    const embedFinal = new EmbedBuilder()
        .setColor(config.colors.ultramarine)
        .setTitle('🏆 Pergunta Final - Votação')
        .setDescription(`Um ou mais jogadores atingiram a zona de vitória! A pergunta final será **DIFÍCIL** e apenas o jogador com maior pontuação fará a tentativa.\n\nOs candidatos devem escolher a categoria votando com a letra correspondente:\n\n**V** - Variedades\n**S** - Sociedade\n**M** - Mundo\n**C** - Ciência/Tecnologia\n**E** - Esportes/Lazer`);

    const candidatos = gameState.candidatosFinal;
    await message.channel.send({ content: `Candidatos à Vitória: ${candidatos.map(p => p.username).join(', ')}`, embeds: [embedFinal] });

    const validKeys = Object.keys(CATEGORIA_ATALHOS);
    const filter = m => candidatos.some(p => p.id === m.author.id) && validKeys.includes(m.content.toLowerCase());
    
    const collector = message.channel.createMessageCollector({ 
        filter, 
        time: TIMEOUT_MS 
    });

    const votos = {};
    const votaram = new Set();
    
    collector.on('collect', async m => {
        // CORREÇÃO CRÍTICA: Checa se o jogo foi encerrado enquanto o coletor estava ativo
        if (!gameStates[guildId] || !gameStates[guildId].jogoAtivo) return;
        
        const atalho = m.content.toLowerCase();
        
        if (!votaram.has(m.author.id)) {
            const categoriaVotada = CATEGORIA_ATALHOS[atalho];
            votos[categoriaVotada] = (votos[categoriaVotada] || 0) + 1;
            votaram.add(m.author.id);
            
            if (votaram.size === candidatos.length) {
                collector.stop('limite');
            }
        }
    });

    collector.on('end', async (collected, reason) => {
        // CORREÇÃO CRÍTICA: Checa se o jogo foi encerrado
        if (!gameStates[guildId] || !gameStates[guildId].jogoAtivo) return;
        
        let max = 0;
        let categoriaEscolhida = 'variedades'; 
        
        for (const [categoria, count] of Object.entries(votos)) {
            if (count > max) {
                max = count;
                categoriaEscolhida = categoria;
            }
        }
        
        const jogadorFinal = candidatos[0]; // Jogador de maior pontuação
        
        await message.channel.send(`🎯 **Categoria Escolhida:** ${categoriaEscolhida.toUpperCase()}. A Pergunta Final será enviada para **${jogadorFinal.username}**!`);
        
        // Passa o gameStates e guildId
        enviarPerguntaFinal(message, gameState, gameStates, guildId, jogadorFinal, categoriaEscolhida, 'dificil');
    });
};

/**
 * Envia a pergunta final e processa o resultado.
 * @param {object} message - O objeto Message
 * @param {object} gameState - O estado do jogo atual
 * @param {object} gameStates - O objeto global de estados (NOVO)
 * @param {string} guildId - ID do servidor para checagem (NOVO)
 * @param {object} jogador - O jogador que vai responder
 * @param {string} categoria - Categoria escolhida
 * @param {string} dificuldade - Dificuldade da pergunta
 */
const enviarPerguntaFinal = async (message, gameState, gameStates, guildId, jogador, categoria, dificuldade) => {
    // CORREÇÃO CRÍTICA: Checa se o jogo foi parado
    if (!gameStates[guildId] || !gameState.jogoAtivo) return;

    const pergunta = getRandomQuestion(categoria, dificuldade); 

    if (!pergunta) {
        await message.channel.send("❌ Erro: Não foi possível carregar a pergunta final.");
        // O jogo deve ser encerrado se a pergunta final falhar
        delete gameStates[guildId];
        return;
    }

    // Define a pergunta final como a pergunta ativa
    gameState.currentQuestion = pergunta;
    gameState.jogoAtivo = true; // Reativa o jogo para aceitar a resposta .q
    
    const embedPergunta = new EmbedBuilder()
        .setColor(config.colors.royalBlue)
        .setTitle(`❗ PERGUNTA FINAL para ${jogador.username}`)
        .setDescription(`Categoria: **${categoria.toUpperCase()}**\nDificuldade: **${dificuldade.toUpperCase()}**\n\n**Pergunta:** ${pergunta.pergunta}\n\n**Você tem 30 segundos!** Responda usando: **.q (sua resposta)**. Se acertar, você vence!`);

    await message.channel.send({ embeds: [embedPergunta] });
    
    // O coletor de respostas será configurado no comando .q no arquivo q.js
};


// --- Comando Principal ---

const questCommand = {
    name: "quest",
    description: "Inicia e controla o jogo Quest.",
    async execute(message, args, conversas, client, gameStates) {
        
        const guildId = message.guild.id;

        // Subcomando INICIAR
        if (args[0]?.toLowerCase() === 'iniciar') {
            if (gameStates[guildId]?.jogoAtivo) {
                return message.reply(`❌ Já existe um jogo Quest ativo!`);
            }
            
            let players = message.mentions.users.map(u => ({ id: u.id, username: u.username }));
            
            if (players.length === 0) {
                return message.reply('❌ Você deve marcar pelo menos dois jogadores para iniciar o Quest! Exemplo: `.quest iniciar @user1 @user2`');
            }
            
            if (!players.some(p => p.id === message.author.id)) {
                players.unshift({ id: message.author.id, username: message.author.username });
            }

            const uniquePlayers = Array.from(new Map(players.map(p => [p.id, p])).values());

            if (uniquePlayers.length < 2) {
                return message.reply('❌ O jogo requer no mínimo 2 jogadores.');
            }

            // --- Inicializa o Estado do Jogo ---
            gameStates[guildId] = {
                players: uniquePlayers,
                placar: Object.fromEntries(uniquePlayers.map(p => [p.id, 0])),
                turnoIndex: Math.floor(Math.random() * uniquePlayers.length),
                jogoAtivo: true,
                channelId: message.channel.id,
                currentQuestion: null,
                candidatosFinal: []
            };
            
            // Mensagem de Regras (Com 30s atualizados)
            const embedRegras = new EmbedBuilder()
                .setColor(config.colors.royalBlue)
                .setTitle('🎮 Quest - Regras do Jogo')
                .setDescription(`Jogadores: ${uniquePlayers.map(p => `<@${p.id}>`).join(', ')}\n\n**Perfeito!** Vamos iniciar!`)
                .addFields(
                    { name: '1️⃣ Pontuação para Vencer', value: `Quem atingir **${WINNING_SCORE}** pontos primeiro entra na Pergunta Final e pode vencer. Pontos: Fácil **${POINTS.facil}**, Médio **${POINTS.medio}**, Difícil **${POINTS.dificil}**.` },
                    { name: '2️⃣ Como Jogar', value: 'O jogador da vez escolhe a categoria enviando a **letra** no chat. Para responder a pergunta, use o comando: **.q (sua resposta)**. Você tem **30 segundos** para responder ou escolher a categoria.' }
                )
                .setFooter({ text: 'Boa sorte a todos os jogadores! 🍀' });

            await message.channel.send({ embeds: [embedRegras] });
            
            // Inicia o primeiro turno, passando todos os parâmetros necessários
            proximoTurno(message, gameStates[guildId], gameStates, guildId);

        } else if (args[0]?.toLowerCase() === 'parar') {
            // Subcomando PARAR
            if (!gameStates[guildId]?.jogoAtivo) {
                return message.reply('❌ Não há jogo Quest ativo para parar.');
            }
            
            // 1. Define o jogo como inativo para que todos os loops e coletores parem
            gameStates[guildId].jogoAtivo = false;
            
            // 2. Deleta o estado do jogo
            delete gameStates[guildId]; 
            
            return message.reply('✅ Jogo Quest parado e resetado. O ciclo de turnos foi interrompido.');
            
        } else {
            // Caso não seja 'iniciar' ou 'parar'
            return message.reply('Comando inválido. Use `.quest iniciar @user1 @user2...` para começar ou `.quest parar` para encerrar.');
        }
    }
};

// --- EXPORTAÇÃO PARA USO EXTERNO ---
module.exports = {
    ...questCommand,
    POINTS, // Exporta para o q.js
    proximoTurno, // Exporta para o q.js
    perguntaFinal, // Exporta para o q.js
    enviarPergunta // Exporta para o q.js
};
